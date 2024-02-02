<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Ping Pong inf</title>
  <style>
    body {
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background-color: #000;
    }
    canvas {
      border: 2px solid #fff;
    }
  </style>
</head>
<body>
  <canvas id="pingPongCanvas" width="800" height="400"></canvas>

  <script>
    const canvas = document.getElementById('pingPongCanvas');
    const ctx = canvas.getContext('2d');

    // Paddle
    const paddleWidth = 8; // Reduce paddle width for better hit detection
    const paddleHeight = 80;
    let leftPaddleY = canvas.height / 2 - paddleHeight / 2;
    let rightPaddleY = canvas.height / 2 - paddleHeight / 2;
    let leftPaddleSpeedY = 0;
    let rightPaddleSpeedY = 0;
    const paddleAcceleration = 5; // Adjust this value for acceleration
    const paddleDamping = 0.5; // Adjust this value for damping

    // Ball
    let ballX = canvas.width / 2;
    let ballY = canvas.height / 2;
    let ballSpeedX = 5;
    let ballSpeedY = 2;
    const maxBallSpeed = 12; // Maximum speed of the ball
    const speedIncreaseFactor = 2;

    // Scores
    let leftPlayerScore = 0;
    let rightPlayerScore = 0;

    function draw() {
      // Clear the canvas
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Draw paddles
      ctx.fillStyle = '#fff';
      ctx.fillRect(0, leftPaddleY, paddleWidth, paddleHeight);
      ctx.fillRect(canvas.width - paddleWidth, rightPaddleY, paddleWidth, paddleHeight);

      // Draw ball
      ctx.beginPath();
      ctx.arc(ballX, ballY, 8, 0, Math.PI * 2);
      ctx.fillStyle = '#fff';
      ctx.fill();
      ctx.closePath();

      // Draw scores
      ctx.font = '24px Arial';
      ctx.fillText('Player: ' + leftPlayerScore, 50, 50);
      ctx.fillText('Computor: ' + rightPlayerScore, canvas.width - 200, 50);

      // Move ball
      ballX += ballSpeedX;
      ballY += ballSpeedY;

      // Bounce off top and bottom walls
      if (ballY - 8 < 0 || ballY + 8 > canvas.height) {
        ballSpeedY = -ballSpeedY;
      }

      // Check collision with paddles
      if (
        (ballX - 8 < paddleWidth && ballY > leftPaddleY && ballY < leftPaddleY + paddleHeight) ||
        (ballX + 8 > canvas.width - paddleWidth && ballY > rightPaddleY && ballY < rightPaddleY + paddleHeight)
      ) {
        if (ballX - 8 < paddleWidth) {
          // Hit by the left paddle
          ballX = paddleWidth + 8; // Set the ball position to the edge of the paddle
        } else {
          // Hit by the right paddle
          ballX = canvas.width - paddleWidth - 8; // Set the ball position to the edge of the paddle
        }

        ballSpeedX = -ballSpeedX * speedIncreaseFactor;

        // Cap the maximum speed of the ball
        ballSpeedX = Math.min(ballSpeedX, maxBallSpeed);
      }

      // Check for scoring
      if (ballX - 8 < 0) {
        // Player 2 scores
        rightPlayerScore++;
        resetBall();
      } else if (ballX + 8 > canvas.width) {
        // Player 1 scores
        leftPlayerScore++;
        resetBall();
      }

      // Move right paddle based on ball position (Unbeatable AI)
      if (ballY > rightPaddleY + paddleHeight / 2) {
        rightPaddleSpeedY += paddleAcceleration;
      } else {
        rightPaddleSpeedY -= paddleAcceleration;
      }

      // Apply damping to the AI paddle
      rightPaddleSpeedY *= paddleDamping;

      // Update paddle positions with acceleration
      leftPaddleY += leftPaddleSpeedY;
      rightPaddleY += rightPaddleSpeedY;

      // Ensure paddles stay within the canvas
      leftPaddleY = Math.max(0, Math.min(canvas.height - paddleHeight, leftPaddleY));
      rightPaddleY = Math.max(0, Math.min(canvas.height - paddleHeight, rightPaddleY));

      requestAnimationFrame(draw);
    }

    function resetBall() {
      ballX = canvas.width / 2;
      ballY = canvas.height / 2;
      ballSpeedX = 5;
      ballSpeedY = 2;
    }

    function handleKeyDown(event) {
      switch (event.key) {
        case 'w':
          leftPaddleSpeedY = -paddleAcceleration;
          break;
        case 's':
          leftPaddleSpeedY = paddleAcceleration;
          break;
      }
    }

    function handleKeyUp(event) {
      switch (event.key) {
        case 'w':
        case 's':
          leftPaddleSpeedY = 0;
          break;
      }
    }

    document.addEventListener('keydown', handleKeyDown);
    document.addEventListener('keyup', handleKeyUp);

    draw(); // Start the game loop
  </script>
</body>
</html>
