let snake;
let food;
let box = 20;
let direction = null; // Tidak bergerak di awal
let score = 0;
let isGameOver = false;
let foodType = "apple"; // Jenis buah

function setup() {
  createCanvas(400, 400);
  frameRate(5); // Kecepatan game
  resetGame();
}

function resetGame() {
  snake = [
    { x: 200, y: 200 },
    { x: 180, y: 200 },
    { x: 160, y: 200 },
  ]; // Panjang awal ular

  direction = null; // Tidak bergerak sampai tombol ditekan
  score = 0;
  isGameOver = false;
  spawnFood();
  loop();
}

function spawnFood() {
  food = {
    x: floor(random(width / box)) * box,
    y: floor(random(height / box)) * box,
  };

  // Pilih jenis buah secara acak
  let foodOptions = ["apple", "grape", "avocado", "strawberry"];
  foodType = random(foodOptions);
}

function draw() {
  if (isGameOver) return; // Berhenti jika game over

  background(0);

  // Gambar ular
  for (let i = 0; i < snake.length; i++) {
   fill(i === 0 ? "pink" : "rgb(255,182,201)");
    rect(snake[i].x, snake[i].y, box, box, 5);
  }

  // Gambar makanan berdasarkan jenisnya
  drawFood(food.x, food.y, foodType);

  if (direction) {
    moveSnake(); // Ular hanya bergerak jika tombol sudah ditekan
  }

  // Gambar skor
  fill(255);
  textSize(20);
  text("Score: " + score, 10, 20);
}

function drawFood(x, y, type) {
  if (type === "apple") {
    // Gambar apel 
    fill("red");
    ellipse(x + box / 2, y + box / 2, box, box);
    fill("green");
    ellipse(x + box / 2, y + 5, 5, 10);
  } else if (type === "grape") {
    // Gambar anggur 
    fill("purple");
    ellipse(x + 10, y + 10, 10, 10);
    ellipse(x + 15, y + 10, 10, 10);
    ellipse(x + 12, y + 5, 10, 10);
    fill("green");
    ellipse(x + 12, y, 5, 10);
  } else if (type === "avocado") {
    // Gambar alpukat 
    fill("green");
    ellipse(x + box / 2, y + box / 2, box, box);
    fill("brown");
    ellipse(x + box / 2, y + box / 2, box / 3, box / 3);
  } else if (type === "strawberry") {
    // Gambar stroberi 🍓
    fill("red");
    triangle(x, y + box, x + box, y + box, x + box / 2, y);
    fill("green");
    ellipse(x + box / 2, y, 10, 5);
  }
}

function moveSnake() {
  let snakeX = snake[0].x;
  let snakeY = snake[0].y;

  if (direction === "LEFT") snakeX -= box;
  if (direction === "UP") snakeY -= box;
  if (direction === "RIGHT") snakeX += box;
  if (direction === "DOWN") snakeY += box;

  // Jika keluar batas, muncul di sisi lain
  if (snakeX < 0) snakeX = width - box;
  if (snakeY < 0) snakeY = height - box;
  if (snakeX >= width) snakeX = 0;
  if (snakeY >= height) snakeY = 0;

  // Jika makan buah
  if (snakeX === food.x && snakeY === food.y) {
    score++;
    spawnFood();
  } else {
    snake.pop(); // Hapus ekor jika tidak makan
  }

  let newHead = { x: snakeX, y: snakeY };

  // Cek tabrakan dengan tubuh sendiri
  if (checkCollision(newHead)) {
    gameOver();
    return;
  }

  snake.unshift(newHead);
}

function checkCollision(head) {
  return snake.some((segment, index) => index !== 0 && head.x === segment.x && head.y === segment.y);
}

function gameOver() {
  isGameOver = true;
  noLoop();
  setTimeout(() => {
    alert("Game Over! Skor: " + score);
    resetGame();
  }, 500);
}

function keyPressed() {
  if (keyCode === LEFT_ARROW && direction !== "RIGHT") direction = "LEFT";
  else if (keyCode === UP_ARROW && direction !== "DOWN") direction = "UP";
  else if (keyCode === RIGHT_ARROW && direction !== "LEFT") direction = "RIGHT";
  else if (keyCode === DOWN_ARROW && direction !== "UP") direction = "DOWN";
}
