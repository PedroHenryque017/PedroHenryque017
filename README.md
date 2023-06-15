let player1, player2;
let ball;
let player1Score = 0, player2Score = 0;

function setup() {
  createCanvas(800, 400);
  player1 = new Paddle(20, height / 2);
  player2 = new Paddle(width - 20, height / 2);
  ball = new Ball();
}

function draw() {
  background(0);
  
  // Desenha a linha central
  drawCenterLine();
  
  // Atualiza e exibe os jogadores
  player1.update();
  player2.update();
  player1.show();
  player2.show();
  
  // Atualiza e exibe a bola
  ball.update();
  ball.checkCollision(player1);
  ball.checkCollision(player2);
  ball.show();
  
  // Exibe a pontuação dos jogadores
  drawScores();
}

function drawCenterLine() {
  stroke(255);
  for (let i = 0; i < height; i += 20) {
    line(width / 2, i, width / 2, i + 10);
  }
}

function drawScores() {
  textSize(32);
  fill(255);
  textAlign(CENTER, CENTER);
  text(player1Score, width / 4, 50);
  text(player2Score, 3 * width / 4, 50);
}

function keyPressed() {
  if (keyCode === UP_ARROW) {
    player2.move(-1);
  } else if (keyCode === DOWN_ARROW) {
    player2.move(1);
  } else if (key === 'W' || key === 'w') {
    player1.move(-1);
  } else if (key === 'S' || key === 's') {
    player1.move(1);
  }
}

function keyReleased() {
  if (keyCode === UP_ARROW || keyCode === DOWN_ARROW) {
    player2.move(0);
  } else if (key === 'W' || key === 'w' || key === 'S' || key === 's') {
    player1.move(0);
  }
}

class Paddle {
  constructor(x, y) {
    this.x = x;
    this.y = y;
    this.width = 10;
    this.height = 60;
    this.speed = 5;
    this.direction = 0;
  }

  update() {
    this.y += this.direction * this.speed;
    this.y = constrain(this.y, this.height / 2, height - this.height / 2);
  }

  show() {
    fill(255);
    rectMode(CENTER);
    rect(this.x, this.y, this.width, this.height);
  }

  move(dir) {
    this.direction = dir;
  }
}

class Ball {
  constructor() {
    this.x = width / 2;
    this.y = height / 2;
    this.radius = 10;
    this.xSpeed = random([-1, 1]) * 3;
    this.ySpeed = random([-1, 1]) * 3;
  }

  update() {
    this.x += this.xSpeed;
    this.y += this.ySpeed;

    if (this.y - this.radius < 0 || this.y + this.radius > height) {
      this.ySpeed *= -1;
    }

    if (this.x - this.radius < player1.x + player1.width / 2 &&
        this.y > player1.y - player1.height / 2 &&
        this.y < player1.y + player1.height / 2) {
      this.xSpeed *= -1;
    }

    if (this.x + this.radius > player2.x - player2.width / 2 &&
        this.y > player2.y - player2.height / 2 &&
        this.y < player2.y + player2.height / 2) {
      this.xSpeed *= -1;
    }

    if (this.x - this.radius < 0) {
      player2Score++;
      this.reset();
    }

    if (this.x + this.radius > width) {
      player1Score++;
      this.reset();
    }
  }

  checkCollision(paddle) {
    if (
      this.x + this.radius > paddle.x - paddle.width / 2 &&
      this.x - this.radius < paddle.x + paddle.width / 2 &&
      this.y + this.radius > paddle.y - paddle.height / 2 &&
      this.y - this.radius < paddle.y + paddle.height / 2
    ) {
      this.xSpeed *= -1;
    }
  }

  reset() {
    this.x = width / 2;
    this.y = height / 2;
    this.xSpeed = random([-1, 1]) * 3;
    this.ySpeed = random([-1, 1]) * 3;
  }

  show() {
    fill(255);
    ellipse(this.x, this.y, this.radius * 2);
  }
}

