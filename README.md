# Tic Tac Toe

**A basic Tic Tac Toe game built using HTML, JavaScript, and CSS. No dependencies required.**

[![View The Demo](https://www.mtb.com/personal/onlineservices/PublishingImages/alt-banking-button-view-demo-cs5452.jpg)](http://codepen.io/vasanthkay/pen/KVzYzG?editors=001)

## Running the Game

Since this is a static HTML/CSS/JS project:

```bash
# Open in browser directly
open index.html
# or on Windows
start index.html
# or serve with Python
python -m http.server 8000
# or with Node.js
npx serve .
```

## Running the Tests

The project includes comprehensive unit tests for all game functionality:

```bash
# Option 1: Open test runner directly in browser
start tests/test-runner.html
# or on macOS/Linux
open tests/test-runner.html

# Option 2: Serve with local server and navigate to tests
python -m http.server 8000
# then open http://localhost:8000/tests/test-runner.html

# Option 3: Using Node.js serve
npx serve .
# then open http://localhost:3000/tests/test-runner.html
```

The test suite includes:
- Game initialization and state management tests
- Move validation and turn switching tests  
- Win detection algorithm tests
- Score tracking and game flow tests
- DOM interaction simulation tests

## How to Play
1. To make a move, the player will use a single mouse click to mark a space. In this version, there is no provision to undo a move. Once a move is made, the game proceeds to the next player's turn.
2. At each move, the game will indicate whose turn (Player A or Player B) it is. When the game ends, it displays one of the following outcomes:
   * Winner: Player A
   * Winner: Player B
   * Draw