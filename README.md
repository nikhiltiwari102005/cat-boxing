<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Cat Boxing Game</title>
  <style>
    body {
      margin: 0;
      background: #222;
      color: white;
      font-family: sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
    }
    canvas {
      background-image: url('fec14451-df37-4fc1-9515-1bc8f9abe833.png');
      background-size: cover;
      border: 2px solid #fff;
      display: block;
    }
    button {
      margin-top: 20px;
      padding: 10px 20px;
      font-size: 16px;
      border-radius: 8px;
      border: none;
      cursor: pointer;
      background: #ff4081;
      color: white;
    }
    .health-bar {
      width: 200px;
      height: 20px;
      background-color: red;
      margin: 10px;
      position: relative;
    }
    .health-fill {
      height: 100%;
      background-color: green;
      position: absolute;
      top: 0;
      left: 0;
    }
  </style>
</head>
<body>
  <div class="health-bar"><div id="cat1HealthBar" class="health-fill"></div></div>
  <div class="health-bar"><div id="cat2HealthBar" class="health-fill"></div></div>
  <canvas id="gameCanvas" width="600" height="400"></canvas>
  <button onclick="startGame()">Start Game</button>
  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');

    const cat1HealthBar = document.getElementById('cat1HealthBar');
    const cat2HealthBar = document.getElementById('cat2HealthBar');

    const cat1Face = new Image();
    cat1Face.src = 'WhatsApp Image 2025-04-30 at 02.18.23_8ae7ddec.jpg';

    const cat2Face = new Image();
    cat2Face.src = 'WhatsApp Image 2025-04-30 at 02.22.55_71827f33.jpg';

    const fatCatBody = new Image();
    fatCatBody.src = '72bed933-59d3-482c-b8c0-fba83c9d9ec8.png';

    const cat1 = { x: 100, y: 150, width: 80, height: 80, dx: 0, dy: 0, health: 100 };
    const cat2 = { x: 450, y: 150, width: 80, height: 80, dx: 0, dy: 0, health: 100 };

    function drawCat(cat, faceImage) {
      ctx.drawImage(fatCatBody, cat.x, cat.y, cat.width, cat.height);
      ctx.drawImage(faceImage, cat.x + 20, cat.y + 10, 40, 40);

      ctx.fillStyle = 'red';
      ctx.beginPath();
      ctx.arc(cat.x + cat.width, cat.y + cat.height / 2, 10, 0, Math.PI * 2);
      ctx.fill();

      ctx.beginPath();
      ctx.arc(cat.x, cat.y + cat.height / 2, 10, 0, Math.PI * 2);
      ctx.fill();
    }

    function updateHealthBars() {
      cat1HealthBar.style.width = cat1.health + '%';
      cat2HealthBar.style.width = cat2.health + '%';
    }

    function showPunchEffect(x, y) {
      ctx.fillStyle = 'yellow';
      ctx.beginPath();
      ctx.arc(x, y, 10, 0, 2 * Math.PI);
      ctx.fill();
      setTimeout(() => {
        ctx.clearRect(x - 10, y - 10, 20, 20);
      }, 100);
    }

    function checkPunching() {
      if (Math.abs(cat1.x - cat2.x) < 85 && Math.abs(cat1.y - cat2.y) < 80) {
        if (cat1Punching) {
          cat2.health = Math.max(0, cat2.health - 1);
          showPunchEffect(cat2.x + cat2.width / 2, cat2.y + cat2.height / 2);
        }
        if (cat2Punching) {
          cat1.health = Math.max(0, cat1.health - 1);
          showPunchEffect(cat1.x + cat1.width / 2, cat1.y + cat1.height / 2);
        }
      }
    }

    function update() {
      cat1.x += cat1.dx;
      cat1.y += cat1.dy;
      cat2.x += cat2.dx;
      cat2.y += cat2.dy;

      cat1.x = Math.max(0, Math.min(canvas.width - cat1.width, cat1.x));
      cat1.y = Math.max(0, Math.min(canvas.height - cat1.height, cat1.y));

      cat2.x = Math.max(0, Math.min(canvas.width - cat2.width, cat2.x));
      cat2.y = Math.max(0, Math.min(canvas.height - cat2.height, cat2.y));

      checkPunching();
      updateHealthBars();
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      drawCat(cat1, cat1Face);
      drawCat(cat2, cat2Face);
    }

    function gameLoop() {
      if (cat1.health <= 0 || cat2.health <= 0) {
        alert(cat1.health <= 0 ? 'Cat 2 Wins!' : 'Cat 1 Wins!');
        return;
      }
      update();
      draw();
      requestAnimationFrame(gameLoop);
    }

    function startGame() {
      cat1.x = 100;
      cat1.y = 150;
      cat1.health = 100;
      cat2.x = 450;
      cat2.y = 150;
      cat2.health = 100;
      updateHealthBars();
      gameLoop();
    }

    let cat1Punching = false;
    let cat2Punching = false;

    document.addEventListener('keydown', (e) => {
      if (e.key === 'a') cat1.dx = -2;
      if (e.key === 'd') cat1.dx = 2;
      if (e.key === 'w') cat1.dy = -2;
      if (e.key === 's') cat1.dy = 2;
      if (e.key === 'f') cat1Punching = true;

      if (e.key === 'ArrowLeft') cat2.dx = -2;
      if (e.key === 'ArrowRight') cat2.dx = 2;
      if (e.key === 'ArrowUp') cat2.dy = -2;
      if (e.key === 'ArrowDown') cat2.dy = 2;
      if (e.key === 'l') cat2Punching = true;
    });

    document.addEventListener('keyup', (e) => {
      if (["a", "d"].includes(e.key)) cat1.dx = 0;
      if (["w", "s"].includes(e.key)) cat1.dy = 0;
      if (e.key === 'f') cat1Punching = false;

      if (["ArrowLeft", "ArrowRight"].includes(e.key)) cat2.dx = 0;
      if (["ArrowUp", "ArrowDown"].includes(e.key)) cat2.dy = 0;
      if (e.key === 'l') cat2Punching = false;
    });
  </script>
</body>
</html>
