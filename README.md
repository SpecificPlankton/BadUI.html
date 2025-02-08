# BadUI.html
<!DOCTYPE html>
<html>
<head>
  <title>Snake Game with Password Input</title>
  <style>
  html, body {
    height: 100%;
    margin: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background: black;
    color: white;
    font-family: Arial, sans-serif;
  }
  canvas {
    border: 1px solid white;
  }
  input, button {
    margin-top: 10px;
    padding: 5px;
    font-size: 16px;
  }
  #timer {
    font-size: 24px;
    margin-bottom: 10px;
  }
  </style>
</head>
<body>
  <h1>Please enjoy our complementary snake game while you create your password</h1>
  <p id="password-requirements"></p>
  <input type="password" id="password" placeholder="Enter password" required>
  <button id="submit">Submit</button>
  <p id="timer">Time Left: 5</p>
  <canvas width="400" height="400" id="game"></canvas>
  <script>
    var canvas = document.getElementById('game');
    var context = canvas.getContext('2d');
    var passwordInput = document.getElementById('password');
    var submitButton = document.getElementById('submit');
    var requirementsText = document.getElementById('password-requirements');
    var timerText = document.getElementById('timer');
    var grid = 16;
    var count = 0;
    var speedDivider = 8 / 6; // Doubling the speed
    var timer = 5;
    var x, y;
    var timerInterval;

    function resetXY() {
      x = Math.floor(Math.random() * 21) + 10;
      y = Math.floor(Math.random() * 21) + 10;
      requirementsText.innerText = `Password must be ${x} characters long and the digits must sum to ${y}.`;
    }
    resetXY();
    
    function resetTimer() {
      clearInterval(timerInterval);
      timer = 5;
      timerText.innerText = `Time Left: ${timer}`;
      timerInterval = setInterval(() => {
        timer--;
        timerText.innerText = `Time Left: ${timer}`;
        if (timer <= 0) {
          resetGame();
        }
      }, 1000);
    }
    resetTimer();
    
    var snake = {
      x: 160,
      y: 160,
      dx: grid,
      dy: 0,
      cells: [],
      maxCells: 4
    };
    var apple = { x: 320, y: 320 };

    function getRandomInt(min, max) {
      return Math.floor(Math.random() * (max - min)) + min;
    }

    function resetGame() {
      snake.x = 160;
      snake.y = 160;
      snake.cells = [];
      snake.maxCells = 4;
      snake.dx = grid;
      snake.dy = 0;
      apple.x = getRandomInt(0, 25) * grid;
      apple.y = getRandomInt(0, 25) * grid;
      passwordInput.value = ""; // Reset password input
      resetXY();
      resetTimer();
    }

    function isValidPassword(password) {
      if (password.length === x) {
        let sum = 0;
        for (let char of password) {
          if (!isNaN(char)) {
            sum += parseInt(char, 10);
          }
        }
        return sum === y;
      }
      return false;
    }

    submitButton.addEventListener('click', function() {
      if (isValidPassword(passwordInput.value)) {
        alert("Password accepted!");
      } else {
        alert("Invalid password. Try again.");
      }
    });

    function loop() {
      requestAnimationFrame(loop);
      if (++count < speedDivider) return;
      count = 0;
      context.clearRect(0, 0, canvas.width, canvas.height);
      snake.x += snake.dx;
      snake.y += snake.dy;

      // Collision detection with walls
      if (snake.x < 0 || snake.x >= canvas.width || snake.y < 0 || snake.y >= canvas.height) {
        resetGame();
        return;
      }

      snake.cells.unshift({ x: snake.x, y: snake.y });
      if (snake.cells.length > snake.maxCells) snake.cells.pop();

      context.fillStyle = 'red';
      context.fillRect(apple.x, apple.y, grid - 1, grid - 1);
      
      context.fillStyle = 'green';
      snake.cells.forEach(function (cell, index) {
        context.fillRect(cell.x, cell.y, grid - 1, grid - 1);
        if (cell.x === apple.x && cell.y === apple.y) {
          snake.maxCells++;
          apple.x = getRandomInt(0, 25) * grid;
          apple.y = getRandomInt(0, 25) * grid;
          resetTimer();
        }
        for (var i = index + 1; i < snake.cells.length; i++) {
          if (cell.x === snake.cells[i].x && cell.y === snake.cells[i].y) {
            resetGame();
            return;
          }
        }
      });
    }

    document.addEventListener('keydown', function (e) {
      if (e.which === 37 && snake.dx === 0) {
        snake.dx = -grid;
        snake.dy = 0;
      } else if (e.which === 38 && snake.dy === 0) {
        snake.dy = -grid;
        snake.dx = 0;
      } else if (e.which === 39 && snake.dx === 0) {
        snake.dx = grid;
        snake.dy = 0;
      } else if (e.which === 40 && snake.dy === 0) {
        snake.dy = grid;
        snake.dx = 0;
      }
    });

    requestAnimationFrame(loop);
  </script>
</body>
</html>
