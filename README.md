<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Space Shooter Game</title>
    <style>
        body { margin: 0;  hidden; }
        canvas { display: block; }
    </style>
</head>
<body>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script>
        let scene, camera, renderer;
        let spaceship, bullets = [], enemies = [];
        let spaceshipSpeed = 0.2, bulletSpeed = 0.5, enemySpeed = 0.1;
        let score = 0;

        function init() {
            // Scene setup
            scene = new THREE.Scene();
            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            renderer = new THREE.WebGLRenderer();
            renderer.setSize(window.innerWidth, window.innerHeight);
            document.body.appendChild(renderer.domElement);

            // Create spaceship
            let geometry = new THREE.BoxGeometry(1, 1, 3);
            let material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
            spaceship = new THREE.Mesh(geometry, material);
            spaceship.position.z = -5;
            scene.add(spaceship);

            // Create enemy
            function createEnemy() {
                let enemy = new THREE.Mesh(new THREE.BoxGeometry(1, 1, 3), new THREE.MeshBasicMaterial({ color: 0xff0000 }));
                enemy.position.x = Math.random() * 10 - 5;
                enemy.position.y = Math.random() * 10 - 5;
                enemy.position.z = -Math.random() * 20 - 5;
                enemies.push(enemy);
                scene.add(enemy);
            }
            createEnemy();

            // Camera position
            camera.position.z = 5;

            // Event listeners
            window.addEventListener('keydown', (event) => {
                if (event.key === 'ArrowUp') spaceship.position.y += spaceshipSpeed;
                if (event.key === 'ArrowDown') spaceship.position.y -= spaceshipSpeed;
                if (event.key === 'ArrowLeft') spaceship.position.x -= spaceshipSpeed;
                if (event.key === 'ArrowRight') spaceship.position.x += spaceshipSpeed;
                if (event.key === ' ') fireBullet();
            });

            // Start animation loop
            animate();
        }

        function fireBullet() {
            let geometry = new THREE.CylinderGeometry(0.05, 0.05, 1);
            let material = new THREE.MeshBasicMaterial({ color: 0xffff00 });
            let bullet = new THREE.Mesh(geometry, material);
            bullet.position.set(spaceship.position.x, spaceship.position.y, spaceship.position.z);
            bullets.push(bullet);
            scene.add(bullet);
        }

        function animate() {
            requestAnimationFrame(animate);

            // Move bullets
            for (let i = 0; i < bullets.length; i++) {
                bullets[i].position.z -= bulletSpeed;
                if (bullets[i].position.z < -10) scene.remove(bullets[i]);
            }

            // Move enemies
            for (let i = 0; i < enemies.length; i++) {
                enemies[i].position.z += enemySpeed;
                if (enemies[i].position.z > 10) enemies[i].position.z = -10;
            }

            // Check for collisions
            checkCollisions();

            // Render the scene
            renderer.render(scene, camera);
        }

        function checkCollisions() {
            for (let i = 0; i < bullets.length; i++) {
                for (let j = 0; j < enemies.length; j++) {
                    if (bullets[i].position.distanceTo(enemies[j].position) < 1) {
                        // Bullet hits enemy
                        scene.remove(bullets[i]);
                        scene.remove(enemies[j]);
                        bullets.splice(i, 1);
                        enemies.splice(j, 1);
                        createEnemy(); // Spawn a new enemy
                        score += 10; // Increment score
                        console.log("Score: " + score);
                        break;
                    }
                }
            }
        }

        // Initialize the game
        init();
    </script>
</body>
</html>

