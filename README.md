<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Blue Lock: Goal Rush</title>
  <style>
    body { margin: 0; overflow: hidden; background: #111; color: white; font-family: Arial; }
    canvas { background: #1e1e1e; display: block; margin: auto; border: 2px solid white; }
    #message { position: absolute; top: 20px; left: 50%; transform: translateX(-50%); font-size: 32px; display: none; }
  </style>
</head>
<body>
  <div id="message">GOAL!</div>
  <canvas id="game" width="800" height="500"></canvas>
  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");

    let isagi = { x: 100, y: 220, w: 40, h: 60 };
    let ball = { x: isagi.x + 35, y: isagi.y + 20, r: 8, dx: 0, dy: 0, moving: false };
    let rin = { x: 740, y: 200, w: 20, h: 100, dy: 2 };
    let keys = {};

    document.addEventListener("keydown", e => keys[e.key] = true);
    document.addEventListener("keyup", e => keys[e.key] = false);

    function drawPlayer(p, color) {
      ctx.fillStyle = color;
      ctx.fillRect(p.x, p.y, p.w, p.h);
    }

    function drawBall(b) {
      ctx.beginPath();
      ctx.arc(b.x, b.y, b.r, 0, Math.PI * 2);
      ctx.fillStyle = "white";
      ctx.fill();
      ctx.closePath();
    }

    function update() {
      // Player Movement
      if (keys["ArrowUp"] && isagi.y > 0) isagi.y -= 4;
      if (keys["ArrowDown"] && isagi.y + isagi.h < canvas.height) isagi.y += 4;
      if (keys["ArrowLeft"] && isagi.x > 0) isagi.x -= 4;
      if (keys["ArrowRight"] && isagi.x + isagi.w < canvas.width) isagi.x += 4;

      // Ball shoot
      if (keys[" "] && !ball.moving) {
        ball.dx = 6;
        ball.dy = 0;
        ball.moving = true;
      }

      // Ball position
      if (!ball.moving) {
        ball.x = isagi.x + 35;
        ball.y = isagi.y + 20;
      } else {
        ball.x += ball.dx;
        ball.y += ball.dy;

        // Goal detection
        if (ball.x + ball.r > canvas.width) {
          if (ball.y > rin.y && ball.y < rin.y + rin.h) {
            // Blocked
            resetBall();
          } else {
            // Goal!
            document.getElementById("message").style.display = "block";
            setTimeout(() => {
              document.getElementById("message").style.display = "none";
              resetBall();
            }, 1500);
          }
        }
      }

      // Rin (Goalkeeper) Movement
      rin.y += rin.dy;
      if (rin.y <= 0 || rin.y + rin.h >= canvas.height) rin.dy *= -1;
    }

    function resetBall() {
      ball.moving = false;
      ball.dx = 0;
      ball.dy = 0;
    }

    function drawField() {
      ctx.fillStyle = "#1e1e1e";
      ctx.fillRect(0, 0, canvas.width, canvas.height);
      ctx.strokeStyle = "white";
      ctx.strokeRect(canvas.width - 10, canvas.height / 2 - 100, 10, 200); // Goal
    }

    function gameLoop() {
      drawField();
      update();
      drawPlayer(isagi, "deepskyblue");
      drawPlayer(rin, "crimson");
      drawBall(ball);
      requestAnimationFrame(gameLoop);
    }

    gameLoop();
  </script>
</body>
</html>
