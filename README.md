<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Space Invaders Game</title>
    <style>
        body {
            margin: 0;
            background-color: black;
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        canvas {
            background-color: #000;
            border: 2px solid white;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="800" height="600"></canvas>
    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        let player = {
            x: canvas.width / 2 - 25,
            y: canvas.height - 60,
            width: 50,
            height: 50,
            speed: 5,
            dx: 0,
            draw: function() {
                ctx.fillStyle = 'lime';
                ctx.fillRect(this.x, this.y, this.width, this.height);
            }
        };

        let bullets = [];
        let enemies = [];
        let score = 0;

        // Bullet class
        class Bullet {
            constructor(x, y) {
                this.x = x;
                this.y = y;
                this.width = 5;
                this.height = 20;
                this.speed = 3;
            }

            draw() {
                ctx.fillStyle = 'red';
                ctx.fillRect(this.x, this.y, this.width, this.height);
            }

            move() {
                this.y -= this.speed;
            }
        }

        // Enemy class
        class Enemy {
            constructor(x, y) {
                this.x = x;
                this.y = y;
                this.width = 50;
                this.height = 50;
                this.speed = 1;
            }

            draw() {
                ctx.fillStyle = 'yellow';
                ctx.fillRect(this.x, this.y, this.width, this.height);
            }

            move() {
                this.y += this.speed;
            }
        }

        // Create multiple enemies
        function createEnemies() {
            for (let i = 0; i < 5; i++) {
                for (let j = 0; j < 6; j++) {
                    enemies.push(new Enemy(80 * j + 50, 60 * i + 30));
                }
            }
        }

        // Draw everything
        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            player.draw();
            bullets.forEach(bullet => bullet.draw());
            enemies.forEach(enemy => enemy.draw());

            // Draw score
            ctx.fillStyle = 'white';
            ctx.font = '20px Arial';
            ctx.fillText('Score: ' + score, 10, 30);

            move();
            collisionDetection();
        }

        // Update player and bullet movement
        function move() {
            player.x += player.dx;

            // Keep player within canvas boundaries
            if (player.x < 0) player.x = 0;
            if (player.x + player.width > canvas.width) player.x = canvas.width - player.width;

            bullets.forEach(bullet => bullet.move());
            enemies.forEach(enemy => enemy.move());
        }

        // Fire bullets
        function fireBullet() {
            bullets.push(new Bullet(player.x + player.width / 2 - 2, player.y));
        }

        // Detect collisions between bullets and enemies
        function collisionDetection() {
            for (let i = 0; i < bullets.length; i++) {
                for (let j = 0; j < enemies.length; j++) {
                    if (bullets[i] && enemies[j]) {
                        if (bullets[i].x < enemies[j].x + enemies[j].width &&
                            bullets[i].x + bullets[i].width > enemies[j].x &&
                            bullets[i].y < enemies[j].y + enemies[j].height &&
                            bullets[i].y + bullets[i].height > enemies[j].y) {
                            
                            // Collision detected, remove bullet and enemy
                            bullets.splice(i, 1);
                            enemies.splice(j, 1);
                            score += 10;
                        }
                    }
                }
            }
        }

        // Keyboard events
        document.addEventListener('keydown', (event) => {
            if (event.key === 'ArrowLeft') {
                player.dx = -player.speed;
            } else if (event.key === 'ArrowRight') {
                player.dx = player.speed;
            } else if (event.key === ' ') {
                fireBullet();
            }
        });

        document.addEventListener('keyup', (event
