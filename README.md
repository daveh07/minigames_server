##Introduction


## Open Issue

- #37 - Create Game - Chess
- Note: #37 is left open as a working branch for continuous development.
- Currently the working branch is name: 37-chess-server
- In working branch, unit tests still to be added
- #45 - Game manager

## Closed issues

- #46 - Tile class (Server side)
- #47 - Piece class (Server side)
- #48 - Pawn class (Server side)
- #49 - Rook class (Server side)
- #50 - Bishop class (Server side)
- #51 - Knight class (Server side)
- #52 - Queen class (Server side)
- #53 - King class (Server side)
- #54 - Chess client (client side)
- #55 - Tiles class (client side)
- #56 - Piece class (client side)

## Documentation

[Chess](Chess-Game)

The majority of the game logic and UI is built out on the client side. This is due to participating group members having very limited server side experience.

The Chess source code can be found in the chess package directory within the javaprojects project directory in: javaprojects -> client -> src -> main -> java -> minigames.client -> chess

The proposed chess game design is outlined in the flow chart/diagram below:

![image](uploads/85e6f7eb45d475df0f17ceb83f232b40/image.png)

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

### Group Roles & Responsibilities

- David Hill will be our GUI, unit tests & documentation
- Chisomo Banzi will work on piece logic
- Mick McCluskey will be our documentation, pieces/board logic and game design

## Group Contact

If you and your team wish to work with us, please contact us via:

- Refer to our issue on Gitlab
- In the COSC220-2022 Slack group
- Email any one listed in the above table
