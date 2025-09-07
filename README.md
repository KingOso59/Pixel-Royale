<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Pixel Royale</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>Pixel Royale</h1>
  <div id="game-container">
    <canvas id="gameCanvas" width="480" height="320"></canvas>
    <div id="elixir-bar">Elixir: <span id="elixir-value">5</span>/10</div>
    <div id="card-buttons"></div>
    <div id="trophies">Trophies: <span id="trophy-count">0</span></div>
  </div>
  <audio id="bgm" src="assets/sounds/bgm.mp3" loop autoplay></audio>
  <script src="game.js"></script>
</body>
</html>
body {
  font-family: monospace;
  background-color: #222;
  color: #fff;
  text-align: center;
}

#game-container {
  display: inline-block;
  margin-top: 20px;
  position: relative;
}

canvas {
  border: 2px solid #fff;
  background-color: #66bb66; /* arena grass */
}

#elixir-bar {
  margin-top: 10px;
  font-size: 18px;
}

#card-buttons {
  margin-top: 10px;
}

#card-buttons button {
  margin: 2px;
  padding: 5px 10px;
  font-size: 14px;
  cursor: pointer;
}

#trophies {
  margin-top: 10px;
  font-size: 16px;
}
// === Game Constants ===
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

const ELIXIR_MAX = 10;
let elixir = 5;
let trophies = 0;
const GAME_TIME = 120000; // 2 minutes
let gameStartTime = Date.now();
let lastElixirTime = Date.now();

const cards = [
  { name: 'Arrows', cost: 3, sprite: 'assets/sprites/arrows.png', type:'spell' },
  { name: 'P.E.K.K.A', cost: 7, sprite: 'assets/sprites/pekka.png', type:'troop' },
  { name: 'Mini P.E.K.K.A', cost: 4, sprite: 'assets/sprites/mini_pekka.png', type:'troop' },
  { name: 'Goblin Barrel', cost: 3, sprite: 'assets/sprites/goblin_barrel.png', type:'spell' },
  { name: 'Skeleton Army', cost: 3, sprite: 'assets/sprites/skeleton_army.png', type:'troop' },
  { name: 'Wizard', cost: 5, sprite: 'assets/sprites/wizard.png', type:'troop' },
  { name: 'Hog Rider', cost: 4, sprite: 'assets/sprites/hog_rider.png', type:'troop' },
  { name: 'Musketeer', cost: 4, sprite: 'assets/sprites/musketeer.png', type:'troop' }
];

let playerUnits = [];
let enemyUnits = [];

// Placeholder towers
const playerTower = { x: 60, y: 130, hp: 1000 };
const enemyTower = { x: 420, y: 130, hp: 1000 };

// === Helper Functions ===
function drawTower(tower, spritePath) {
  const img = new Image();
  img.src = spritePath;
  ctx.drawImage(img, tower.x - 16, tower.y - 16, 32, 32);
}

function drawUnits(units) {
  units.forEach(unit => {
    const img = new Image();
    img.src = unit.sprite;
    ctx.drawImage(img, unit.x, unit.y, 16, 16);
  });
}

function updateElixir() {
  const now = Date.now();
  const elapsed = now - lastElixirTime;
  if (elapsed >= 2800) { // 2.8s per elixir
    elixir = Math.min(elixir + 1, ELIXIR_MAX);
    document.getElementById('elixir-value').textContent = elixir;
    lastElixirTime = now;
  }
}

function render() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  // Draw towers
  drawTower(playerTower, 'assets/sprites/tower_player.png');
  drawTower(enemyTower, 'assets/sprites/tower_enemy.png');
  // Draw units
  drawUnits(playerUnits);
  drawUnits(enemyUnits);
}

// === Game Loop ===
function gameLoop() {
  updateElixir();
  render();
  requestAnimationFrame(gameLoop);
}

// === Card Buttons ===
function setupCardButtons() {
  const container = document.getElementById('card-buttons');
  cards.forEach(card => {
    const btn = document.createElement('button');
    btn.textContent = `${card.name} (${card.cost})`;
    btn.onclick = () => {
      if (elixir >= card.cost) {
        elixir -= card.cost;
        document.getElementById('elixir-value').textContent = elixir;
        spawnCard(card);
      }
    };
    container.appendChild(btn);
  });
}

function spawnCard(card) {
  if (card.type === 'troop') {
    playerUnits.push({ x: playerTower.x + 20, y: playerTower.y, sprite: card.sprite, hp: 100 });
  } else if (card.type === 'spell') {
    // placeholder: instantly damages enemy tower
    enemyTower.hp -= card.cost * 10;
  }
}

// === Initialize Game ===
setupCardButtons();
gameLoop();

