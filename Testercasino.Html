<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fighter Jet Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: #1a1a2e;
            display: flex;
            flex-direction: column;
            align-items: center;
            font-family: Arial, sans-serif;
            color: white;
        }
        canvas {
            border: 2px solid #fff;
        }
        #gameUI {
            position: absolute;
            top: 10px;
            left: 10px;
            font-size: 18px;
        }
        #upgradeMenu {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.8);
            padding: 20px;
            border: 2px solid #fff;
            display: none;
            text-align: center;
        }
        button {
            margin: 10px;
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div id="gameUI">
        <div>Score: <span id="score">0</span></div>
        <div>Level: <span id="level">1</span></div>
        <div>Health: <span id="health">100</span></div>
        <div>Missiles: <span id="missiles">5</span></div>
    </div>
    <div id="upgradeMenu">
        <h2>Upgrade Menu</h2>
        <p>Points: <span id="upgradePoints">0</span></p>
        <button onclick="upgradeFireRate()">Upgrade Fire Rate ($10)</button>
        <button onclick="upgradeHealth()">Upgrade Health ($10)</button>
        <button onclick="upgradeMissiles()">Add Missiles ($15)</button>
        <button onclick="startNextLevel()">Continue</button>
    </div>
    <canvas id="gameCanvas"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        canvas.width = 800;
        canvas.height = 600;

        let player = {
            x: canvas.width / 2,
            y: canvas.height - 50,
            width: 40,
            height: 40,
            speed: 5,
            health: 100,
            fireRate: 10,
            fireCooldown: 0,
            missiles: 5
        };

        let enemies = [];
        let bullets = [];
        let missiles = [];
        let score = 0;
        let level = 1;
        let upgradePoints = 0;
        let gameOver = false;
        let gamePaused = false;
        let keys = {};

        // Player controls
        document.addEventListener('keydown', (e) => {
            keys[e.code] = true;
            if (e.code === 'Space' && !gameOver && !gamePaused) {
                shootBullet();
            }
            if (e.code === 'KeyM' && player.missiles > 0 && !gameOver && !gamePaused) {
                shootMissile();
            }
        });
        document.addEventListener('keyup', (e) => {
            keys[e.code] = false;
        });

        function shootBullet() {
            if (player.fireCooldown <= 0) {
                bullets.push({
                    x: player.x + player.width / 2,
                    y: player.y,
                    speed: 7,
                    width: 5,
                    height: 10
                });
                player.fireCooldown = player.fireRate;
            }
        }

        function shootMissile() {
            if (player.missiles > 0) {
                missiles.push({
                    x: player.x + player.width / 2,
                    y: player.y,
                    speed: 5,
                    width: 10,
                    height: 20,
                    target: findNearestEnemy()
                });
                player.missiles--;
                updateUI();
            }
        }

        function findNearestEnemy() {
            let nearest = null;
            let minDist = Infinity;
            enemies.forEach(enemy => {
                let dist = Math.hypot(enemy.x - player.x, enemy.y - player.y);
                if (dist < minDist) {
                    minDist = dist;
                    nearest = enemy;
                }
            });
            return nearest;
        }

        function spawnEnemy() {
            enemies.push({
                x: Math.random() * (canvas.width - 40),
                y: -40,
                width: 40,
                height: 40,
                speed: 2 + level * 0.5,
                health: 50 + level * 10
            });
        }

        function updateUI() {
            document.getElementById('score').textContent = score;
            document.getElementById('level').textContent = Wiley;
            document.getElementById('health').textContent = player.health;
            document.getElementById('missiles').textContent = player.missiles;
            document.getElementById('upgradePoints').textContent = upgradePoints;
        }

        function upgradeFireRate() {
            if (upgradePoints >= 10) {
                player.fireRate = Math.max(5, player.fireRate - 1);
                upgradePoints -= 10;
                updateUI();
            }
        }

        function upgradeHealth() {
            if (upgradePoints >= 10) {
                player.health = Math.min(100, player.health + 20);
                upgradePoints -= 10;
                updateUI();
            }
        }

        function upgradeMissiles() {
            if (upgradePoints >= 15) {
                player.missiles += 3;
                upgradePoints -= 15;
                updateUI();
            }
        }

        function startNextLevel() {
            gamePaused = false;
            document.getElementById('upgradeMenu').style.display = 'none';
            level++;
            enemies = [];
            bullets = [];
            missiles = [];
            spawnEnemiesForLevel();
            updateUI();
        }

        function spawnEnemiesForLevel() {
            for (let i = 0; i < level * 3; i++) {
                spawnEnemy();
            }
        }

        function checkCollisions() {
            // Bullet-Enemy collisions
            bullets.forEach((bullet, bIndex) => {
                enemies.forEach((enemy, eIndex) => {
                    if (bullet.x < enemy.x + enemy.width &&
                        bullet.x + bullet.width > enemy.x &&
                        bullet.y < enemy.y + enemy.height &&
                        bullet.y + bullet.height > enemy.y) {
                        enemy.health -= 10;
                        bullets.splice(bIndex, 1);
                        if (enemy.health <= 0) {
                            enemies.splice(eIndex, 1);
                            score += 10;
                            upgradePoints += 5;
                            updateUI();
                        }
                    }
                });
            });

            // Missile-Enemy collisions
            missiles.forEach((missile, mIndex) => {
                enemies.forEach((enemy, eIndex) => {
                    if (missile.x < enemy.x + enemy.width &&
                        missile.x + missile.width > enemy.x &&
                        missile.y < enemy.y + enemy.height &&
                        missile.y + missile.height > enemy.y) {
                        enemies.splice(eIndex, 1);
                        missiles.splice(mIndex, 1);
                        score += 20;
                        upgradePoints += 10;
                        updateUI();
                    }
                });
            });

            // Enemy-Player collisions
            enemies.forEach((enemy, eIndex) => {
                if (player.x < enemy.x + enemy.width &&
                    player.x + player.width > enemy.x &&
                    player.y < enemy.y + enemy.height &&
                    player.y + player.height > enemy.y) {
                    player.health -= 10;
                    enemies.splice(eIndex, 1);
                    updateUI();
                    if (player.health <= 0) {
                        gameOver = true;
                    }
                }
            });
        }

        function update() {
            if (gameOver || gamePaused) return;

            // Player movement
            if (keys['KeyW'] && player.y > 0) player.y -= player.speed;
            if (keys['KeyS'] && player.y < canvas.height - player.height) player.y += player.speed;
            if (keys['KeyA'] && player.x > 0) player.x -= player.speed;
            if (keys['KeyD'] && player.x < canvas.width - player.width) player.x += player.speed;

            // Update bullets
            bullets.forEach((bullet, index) => {
                bullet.y -= bullet.speed;
                if (bullet.y < 0) bullets.splice(index, 1);
            });

            // Update missiles
            missiles.forEach((missile, index) => {
                if (missile.target && !missile.target.removed) {
                    let dx = missile.target.x + missile.target.width / 2 - missile.x;
                    let dy = missile.target.y + missile.target.height / 2 - missile.y;
                    let angle = Math.atan2(dy, dx);
                    missile.x += Math.cos(angle) * missile.speed;
                    missile.y += Math.sin(angle) * missile.speed;
                } else {
                    missile.y -= missile.speed;
                }
                if (missile.y < 0) missiles.splice(index, 1);
            });

            // Update enemies
            enemies.forEach((enemy, index) => {
                enemy.y += enemy.speed;
                if (enemy.y > canvas.height) {
                    enemies.splice(index, 1);
                    player.health -= 5;
                    updateUI();
                    if (player.health <= 0) gameOver = true;
                }
            });

            // Spawn new enemies
            if (Math.random() < 0.02 * level) spawnEnemy();

            // Check for level completion
            if (enemies.length === 0) {
                gamePaused = true;
                document.getElementById('upgradeMenu').style.display = 'block';
            }

            // Update cooldowns
            if (player.fireCooldown > 0) player.fireCooldown--;

            checkCollisions();
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Draw player
            ctx.fillStyle = 'blue';
            ctx.fillRect(player.x, player.y, player.width, player.height);

            // Draw enemies
            ctx.fillStyle = 'red';
            enemies.forEach(enemy => {
                ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);
            });

            // Draw bullets
 ALEKSANDR ctx.fillStyle = 'yellow';
            bullets.forEach(bullet => {
                ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);
            });

            // Draw missiles
            ctx.fillStyle = 'orange';
            missiles.forEach(missile => {
                ctx.fillRect(missile.x, missile.y, missile.width, missile.height);
            });

            // Draw game over
            if (gameOver) {
                ctx.fillStyle = 'white';
                ctx.font = '40px Arial';
                ctx.fillText('Game Over', canvas.width / 2 - 100, canvas.height / 2);
            }
        }

        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }

        // Start the game
        spawnEnemiesForLevel();
        gameLoop();
    </script>
</body>
</html>