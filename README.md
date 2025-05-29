<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Tic-Tac-Toe</title>
  <style>
    /* General Reset and Setup */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Segoe UI', sans-serif;
  background: linear-gradient(-45deg, #ff9a9e, #fad0c4, #fad0c4, #fbc2eb);
  background-size: 400% 400%;
  animation: gradientBG 15s ease infinite;
  min-height: 100vh;
  color: #333;
  overflow-x: hidden;
}

/* Gradient background animation */
@keyframes gradientBG {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

/* Navigation */
header {
  background: rgba(0, 0, 0, 0.7);
  padding: 10px 0;
  position: sticky;
  top: 0;
  z-index: 10;
}

nav ul {
  list-style: none;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 25px;
}

nav a {
  color: #fff;
  text-decoration: none;
  font-weight: bold;
  transition: color 0.3s;
}

nav a:hover {
  color: #ffd700;
}

/* Main Content Sections */
section {
  padding: 40px 20px;
  text-align: center;
}

/* Headings */
h1, h2 {
  margin-bottom: 20px;
  animation: fadeIn 1s ease forwards;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(-20px); }
  to { opacity: 1; transform: translateY(0); }
}

/* Scoreboard */
.scoreboard {
  display: flex;
  justify-content: center;
  gap: 25px;
  margin: 10px 0 20px;
  font-size: 1.2rem;
  background: rgba(255,255,255,0.5);
  padding: 10px 20px;
  border-radius: 10px;
  animation: fadeIn 1s ease forwards;
}

/* Game Board */
.board {
  display: grid;
  grid-template-columns: repeat(3, 100px);
  gap: 12px;
  justify-content: center;
  margin: 20px auto;
  padding: 10px;
}

.cell {
  width: 100px;
  height: 100px;
  background: #fff;
  border-radius: 10px;
  font-size: 2.5rem;
  font-weight: bold;
  color: #333;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
  transition: transform 0.2s ease, background 0.3s ease;
  animation: popIn 0.3s ease-in-out;
}

.cell:hover {
  background: #f0f0f0;
  transform: scale(1.05);
}

@keyframes popIn {
  from { transform: scale(0); }
  to { transform: scale(1); }
}

.cell.win {
  background: #90ee90;
  animation: pulse 0.8s infinite;
}

@keyframes pulse {
  0% { box-shadow: 0 0 10px #90ee90; }
  50% { box-shadow: 0 0 25px #00ff99; }
  100% { box-shadow: 0 0 10px #90ee90; }
}

/* Buttons */
button {
  cursor: pointer;
  border: none;
  background: #333;
  color: white;
  padding: 12px 24px;
  font-size: 1rem;
  border-radius: 8px;
  margin-top: 20px;
  transition: background 0.3s;
}

button:hover {
  background: #555;
}

.mode-select {
  margin: 20px;
}

.mode-select button {
  margin: 0 10px;
}

/* Status Message */
#status {
  margin-top: 15px;
  font-size: 1.3rem;
  font-weight: bold;
}

/* Responsive Design */
@media (max-width: 600px) {
  .board {
    grid-template-columns: repeat(3, 80px);
    gap: 8px;
  }

  .cell {
    width: 80px;
    height: 80px;
    font-size: 2rem;
  }

  .scoreboard {
    flex-direction: column;
    gap: 8px;
  }

  nav ul {
    flex-direction: column;
    gap: 10px;
  }
}
section {
  opacity: 0;
  transform: translateY(20px);
  transition: all 0.6s ease;
}

section.show {
  opacity: 1;
  transform: translateY(0);
}
  </style>
</head>
<body>
  <header>
    <nav>
      <ul>
        <li><a href="#home">Home</a></li>
        <li><a href="#about">About</a></li>
        <li><a href="#projects">Projects</a></li>
        <li><a href="#contact">Contact</a></li>
      </ul>
    </nav>
  </header>
 <section id="home">
    <h1>Tic-Tac-Toe</h1>
    <div class="scoreboard">
      <span>Player X: <span id="x-score">0</span></span>
      <span>Player O: <span id="o-score">0</span></span>
      <span>Draws: <span id="draw-score">0</span></span>
    </div>
    <div class="mode-select">
      <button onclick="setMode('human')">Two Players</button>
      <button onclick="setMode('ai')">Play vs AI</button>
    </div>
    <div id="board" class="board"></div>
    <p id="status"></p>
    <button onclick="resetGame()">Reset Game</button>
  </section>
  <section id="about">
    <h2>About</h2>
    <p>This is a simple interactive tic-tac-toe game with options to play against a friend or AI.</p>
  </section>
  <section id="projects">
    <h2>Projects</h2>
    <p>Explore other web games and interactive apps we’ve created.</p>
  </section>
  <section id="contact">
    <h2>Contact</h2>
    <p>Reach out for collaborations or questions at example@email.com.</p>
  </section>
  <audio id="click-sound" src="click.mp3" preload="auto"></audio>
  <audio id="win-sound" src="win.mp3" preload="auto"></audio>
  <script>
    const boardEl = document.getElementById('board');
const statusEl = document.getElementById('status');
const clickSound = document.getElementById('click-sound');
const winSound = document.getElementById('win-sound');
let mode = 'human';
let board = Array(9).fill(null);
let currentPlayer = 'X';
let gameActive = true;
let scores = { X: 0, O: 0, draws: 0 };

function setMode(newMode) {
  mode = newMode;
  resetGame();
}

function renderBoard() {
  boardEl.innerHTML = '';
  board.forEach((cell, i) => {
    const cellEl = document.createElement('div');
    cellEl.classList.add('cell');
    cellEl.textContent = cell || '';
    cellEl.addEventListener('click', () => handleMove(i));
    boardEl.appendChild(cellEl);
  });
}

function handleMove(index) {
  if (!gameActive || board[index]) return;
  board[index] = currentPlayer;
  clickSound.play();
  renderBoard();
  checkGameStatus();

  if (mode === 'ai' && gameActive && currentPlayer === 'O') {
    setTimeout(aiMove, 500);
  }
}

function aiMove() {
  let available = board.map((val, i) => val === null ? i : null).filter(v => v !== null);
  let move = available[Math.floor(Math.random() * available.length)];
  board[move] = 'O';
  clickSound.play();
  renderBoard();
  checkGameStatus();
}

function checkGameStatus() {
  const winCombos = [
    [0,1,2],[3,4,5],[6,7,8],
    [0,3,6],[1,4,7],[2,5,8],
    [0,4,8],[2,4,6]
  ];

  let winner = null;

  for (let combo of winCombos) {
    const [a, b, c] = combo;
    if (board[a] && board[a] === board[b] && board[a] === board[c]) {
      winner = board[a];
      highlightWinningCells(combo);
      break;
    }
  }

  if (winner) {
    winSound.play();
    gameActive = false;
    statusEl.textContent = `${winner} wins!`;
    scores[winner]++;
    updateScoreboard();
    return;
  }

  if (!board.includes(null)) {
    statusEl.textContent = `It's a draw!`;
    scores.draws++;
    updateScoreboard();
    gameActive = false;
    return;
  }

  currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
  statusEl.textContent = `${currentPlayer}'s turn`;
}

function highlightWinningCells(combo) {
  const cells = document.querySelectorAll('.cell');
  combo.forEach(i => cells[i].classList.add('win'));
}

function resetGame() {
  board = Array(9).fill(null);
  currentPlayer = 'X';
  gameActive = true;
  statusEl.textContent = `${currentPlayer}'s turn`;
  renderBoard();
}

function updateScoreboard() {
  document.getElementById('x-score').textContent = scores.X;
  document.getElementById('o-score').textContent = scores.O;
  document.getElementById('draw-score').textContent = scores.draws;
}

// Initialize game
renderBoard();
statusEl.textContent = `${currentPlayer}'s turn`;
// Animate sections on scroll
const sections = document.querySelectorAll("section");

window.addEventListener("scroll", () => {
  const trigger = window.innerHeight * 0.8;

  sections.forEach((section) => {
    const top = section.getBoundingClientRect().top;
    if (top < trigger) {
      section.classList.add("show");
    }
  });
});


  </script>
</body>
</html>
