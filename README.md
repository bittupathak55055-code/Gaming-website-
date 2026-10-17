<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Neon Car Racing</title>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: #080d1b;
  color: white;
  font-family: Arial, sans-serif;
  text-align: center;
}

h1 {
  color: #00ffff;
  text-shadow: 0 0 15px #00ffff;
}

.game {
  width: 320px;
  max-width: 95%;
  margin: 15px auto;
}

canvas {
  width: 100%;
  background: #263445;
  border: 3px solid #00ffff;
  border-radius: 10px;
  display: block;
}

button {
  background: #00bcd4;
  color: #001018;
  border: none;
  padding: 14px;
  margin: 5px;
  border-radius: 8px;
  font-size: 16px;
  font-weight: bold;
}

.controls {
  display: flex;
  justify-content: center;
  gap: 5px;
}

#score {
  color: #00ff99;
  font-size: 24px;
}
</style>
</head>

<body>

<h1>🏎️ NEON RACER</h1>

<div class="game">

  <p>Score: <span id="score">0</span></p>

  <canvas id="gameCanvas"
          width="320"
          height="450"></canvas>

  <button onclick="startGame()">▶ START GAME</button>

  <div class="controls">
    <button onclick="moveLeft()">⬅ LEFT</button>
    <button onclick="moveRight()">RIGHT ➡</button>
  </div>

  <p id="message">Press START GAME to play!</p>

</div>

<script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

let player;
let enemies = [];
let score = 0;
let running = false;
let animation;
let roadOffset = 0;

function resetGame() {
  player = {
    x: 140,
    y: 365,
    width: 40,
    height: 60,
    speed: 25
  };

  enemies = [];
  score = 0;
  roadOffset = 0;

  document.getElementById("score").textContent = score;
}

function drawCar(x, y, color) {
  ctx.fillStyle = color;
  ctx.fillRect(x, y, 40, 60);

  ctx.fillStyle = "#dffaff";
  ctx.fillRect(x + 7, y + 8, 26, 14);

  ctx.fillStyle = "#111827";
  ctx.fillRect(x + 5, y + 42, 30, 10);

  ctx.fillStyle = "#ffff66";
  ctx.fillRect(x + 3, y + 2, 8, 5);
  ctx.fillRect(x + 29, y + 2, 8, 5);
}

function drawRoad() {
  ctx.fillStyle = "#263445";
  ctx.fillRect(0, 0, 320, 450);

  ctx.fillStyle = "#ffffff";
  ctx.fillRect(15, 0, 4, 450);
  ctx.fillRect(301, 0, 4, 450);

  for (let y = -40 + roadOffset; y < 450; y += 80) {
    ctx.fillRect(105, y, 4, 40);
    ctx.fillRect(211, y, 4, 40);
  }

  roadOffset += 5;

  if (roadOffset >= 80) {
    roadOffset = 0;
  }
}

function drawGame() {
  drawRoad();

  drawCar(player.x, player.y, "#00ffff");

  enemies.forEach(enemy => {
    drawCar(enemy.x, enemy.y, "#ff3344");
  });
}

function moveLeft() {
  if (running && player.x > 25) {
    player.x -= player.speed;
  }
}

function moveRight() {
  if (running && player.x < 255) {
    player.x += player.speed;
  }
}

document.addEventListener("keydown", function(event) {
  if (event.key === "ArrowLeft") moveLeft();
  if (event.key === "ArrowRight") moveRight();
});

function gameLoop() {
  if (!running) return;

  ctx.clearRect(0, 0, canvas.width, canvas.height);

  if (Math.random() < 0.025) {
    enemies.push({
      x: [35, 140, 245][Math.floor(Math.random() * 3)],
      y: -60,
      width: 40,
      height: 60
    });
  }

  enemies.forEach(enemy => {
    enemy.y += 4 + Math.min(score / 200, 5);
  });

  enemies = enemies.filter(enemy => enemy.y < 460);

  for (const enemy of enemies) {
    const collision =
      player.x < enemy.x + 40 &&
      player.x + 40 > enemy.x &&
      player.y < enemy.y + 60 &&
      player.y + 60 > enemy.y;

    if (collision) {
      running = false;

      document.getElementById("message").textContent =
        "💥 GAME OVER! Score: " + score;

      drawGame();
      return;
    }
  }

  score++;
  document.getElementById("score").textContent = score;

  drawGame();

  animation = requestAnimationFrame(gameLoop);
}

function startGame() {
  cancelAnimationFrame(animation);

  resetGame();
  running = true;

  document.getElementById("message").textContent =
    "Dodge the red cars!";

  drawGame();
  gameLoop();
}

resetGame();
drawGame();
</script>

</body>
</html>
