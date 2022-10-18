##Introduction
# Mini Game Server
<!-- Generated with shields.io -->
[![Java Build Status](https://img.shields.io/jenkins/build?jobUrl=https%3A%2F%2Fhopper.une.edu.au%2Fjenkins%2Fjob%2Fcosc220_2022_java%2F&label=Java%20Build%20Status)](https://hopper.une.edu.au/jenkins/job/cosc220_2022_java/)
[![Java Tests](https://img.shields.io/jenkins/tests?compact_message&jobUrl=https%3A%2F%2Fhopper.une.edu.au%2Fjenkins%2Fjob%2Fcosc220_2022_java%2F&label=Java%20Tests)](https://hopper.une.edu.au/jenkins/job/cosc220_2022_java/lastCompletedBuild/testReport/)
[![Scala Build Status](https://img.shields.io/jenkins/build?jobUrl=https%3A%2F%2Fhopper.une.edu.au%2Fjenkins%2Fjob%2Fcosc220_2022_scala%2F&label=Scala%20Build%20Status)](https://hopper.une.edu.au/jenkins/job/cosc220_2022_scala/)
[![Scala Tests](https://img.shields.io/jenkins/tests?compact_message&jobUrl=https%3A%2F%2Fhopper.une.edu.au%2Fjenkins%2Fjob%2Fcosc220_2022_scala%2F&label=Scala%20Tests)](https://hopper.une.edu.au/jenkins/job/cosc220_2022_scala/lastCompletedBuild/testReport/)

The Mini Game Server monorepository created by class COSC220 Software Development Studio 2 during Trimester 2 2022.

There is more than one project in the same git repository. These are divided up largely by language.

To open the `javaprojects`, rather than open the toplevel directory in VS Code, open the `javaprojects` folder in VS Code.

Likewise, each language has its own devcontainer for simplicity.

## Projects
| Folder name | Description |
|---|---|
| `javaprojects` | This contains a set-up that is (more-or-less) traditional for this unit. It is a multi-module gradle project with modules for: <ul>   <li>`common` - Ordinary Java code we want to include in both the server and the client</li>   <li>`server` - The minigame network server. This is based on Vertx, which is a Node.js-like server for Java.</li>   <li>`client` - A Java Swing client that can connect to our server</li> </ul> |
| `scalaclient` | New, experimental, for the brave. This contains a web client written in Scala.js. It is built using sbt. It is totally independent, except that it can talk to the server over HTTP and JSON. |
| `pythonclient` | Under construction. |

## Usage
### Java
#### Server
To run the server, `cd` into `javaprojects`
```sh
./gradlew server:run --args="41234"
```
or some other port number.

This will start a Vertx server, listening on the port you specified.

- You can test this by opening a browser, and visiting http://localhost:41234/ping (replace 41234 with the port number you used)
    - Note: it's just "http" not "https"

#### Client
You'll need to start the server first!

From the `javaprojects` directory:
```sh
./gradlew client:run --args="localhost:41234"
```
Note: Remember to use the same port number you used to start the server.

## Documentation

[Chess](Chess-Game)

The majority of the game logic and UI is built out on the client side. This is due to participating group members having very limited server side experience.

The Chess source code can be found in the chess package directory within the javaprojects project directory in: javaprojects -> client -> src -> main -> java -> minigames.client -> chess

Inside the directory there are 4 Java files/classes:

1. CheckmateManager.java - This class manages the logic for check, refer to the \*public void inCheck()\* method & checkmate for each of the kings, refer to the \*private void checkmate()\* method.
2. Chess.java - Is the main class to call methods to build and initiate the board with pieces. It is also the class where the UI is created using swing components
3. ChessBoard.java - Java class that creates the chessboard object and initiates the board the pieces to render to the correct tiles/squares at the start of a game.
4. Tile.java - Java class that creates a tile object that can referenced using coordinates and determine if the tile is \*occupied\* or \*not occupied\* with a piece. & 2 sub directories

Pieces and Static directories:

1. Pieces sub-directory consists of all the chess pieces as separate classes, \*Bishop, King, Knight, Pawn, Piece, Queen, Rook\* and contains the logic for \*legal & illegal\* chess moves for each individual piece.
2. The Static sub-directory consists of 2 enum classes that denote either \* Black or White \* for the chess piece colours, and a PieceType enum for all the chess piece types, Bishop, pawn, knight, etc. To load the game onto the client, the components are added to the MinigameNetworkClient object. Refer code below in the Chess.java class.

```java
    public void load(MinigameNetworkClient mnClient, GameMetadata game, String player) {

        this.mnClient = mnClient;
        this.gm = game;
        this.player = player;

        mnClient.getMainWindow().addNorth(menu);
        mnClient.getMainWindow().addCenter(chessBoard);
        mnClient.getMainWindow().addEast(counterLabel);
        
        mnClient.getMainWindow().pack();
    }
```

The Chessboard Object is created using a 2-dimensional 8x8 board array object. Each index of the array is assigned a tile that can be referenced.

```java
 private void setupBoard() {

        this.board = new Tile[8][8];
        // Fill black.
        for (int i = 0; i < 8; i++) { // Row
            for (int j = 0; j < 8; j++) { // Column
                if ((i % 2 == 0 && j % 2 == 0) || (i % 2 == 1 && j % 2 == 1)) { // Black
                    this.board[i][j] = new Tile(i, j, false, Colors.BLACK, this);
                    // this.board[j][i] = new Tile(i ,j,false,Colors.BLACK);
                    this.add(board[i][j]);
                } else {
                    this.board[i][j] = new Tile(i, j, false, Colors.WHITE, this); // White
                    // this.board[j][i] = new Tile(i,j,false,Colors.WHITE);     //White
                    this.add(board[i][j]);
                }
            }
        }

        initiatePieces();
        //  this.setSize(new Dimension(size,size));
    }
```

Chess piece objects are then initaited to the relevant board indices,

```java
    private void initiatePieces() {
        // Pawns
        for (int i = 0; i < 8; i++) {
            this.board[1][i].setPiece(new Pawn(Colors.BLACK, board[1][i], this));
            this.board[6][i].setPiece(new Pawn(Colors.WHITE, board[6][i], this));
        }
        // Rooks
        this.board[0][0].setPiece(new Rook(Colors.BLACK, board[0][0], this));
        this.board[0][7].setPiece(new Rook(Colors.BLACK, board[0][7], this));
        this.board[7][0].setPiece(new Rook(Colors.WHITE, board[7][0], this));
        this.board[7][7].setPiece(new Rook(Colors.WHITE, board[7][7], this));
        // Knight
        this.board[0][1].setPiece(new Knight(Colors.BLACK, board[0][1], this));
        this.board[0][6].setPiece(new Knight(Colors.BLACK, board[0][6], this));
        this.board[7][1].setPiece(new Knight(Colors.WHITE, board[7][1], this));
        this.board[7][6].setPiece(new Knight(Colors.WHITE, board[7][6], this));
        // Bishop
        this.board[0][2].setPiece(new Bishop(Colors.BLACK, board[0][2], this));
        this.board[0][5].setPiece(new Bishop(Colors.BLACK, board[0][5], this));
        this.board[7][2].setPiece(new Bishop(Colors.WHITE, board[7][2], this));
        this.board[7][5].setPiece(new Bishop(Colors.WHITE, board[7][5], this));
        // King
        King bk = new King(Colors.BLACK, board[0][3], this);
        King wk = new King(Colors.WHITE, board[7][3], this);
        this.board[0][3].setPiece(bk);
        this.board[7][3].setPiece(wk);
        this.checkmateManager = new CheckmateManager(this, bk, wk);
        // Queen
        this.board[0][4].setPiece(new Queen(Colors.BLACK, board[0][4], this));
        this.board[7][4].setPiece(new Queen(Colors.WHITE, board[7][4], this));
    }
```

## Testing

- Unit test using jUnit5 can be found in javaprojects -> client -> src -> test -> java -> minigames.client -> chess

## How We Work

### Scheduled Meeting Time

- We will meet via Slack once a week and on Sundays to discuss and designate tasks.
- Any additional meeting times will be agreed upon by the whole group

### Communication

- COSC220-2022 Slack group
- Weekly meetings via Teams

### EXAMPLE

![image](https://user-images.githubusercontent.com/51868751/194701654-2e0a751c-a740-40c9-b103-65619d35c395.png)


![image](https://user-images.githubusercontent.com/51868751/194701672-251761d2-c56f-4d0a-ab80-53ec90c7c0fe.png)


![image](https://user-images.githubusercontent.com/51868751/194701730-e434ada9-c798-4e7c-8c5c-4bf21912851e.png)


