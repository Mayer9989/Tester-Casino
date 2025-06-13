<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
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
            touch-action: none;
        }
        canvas {
            border: 2px solid #fff;
            max-width: 100%;
            max-height: 100vh;
        }
        #gameUI {
            position: absolute;
            top: 10px;
            left: 10px;
            font-size: 18px;
            background: rgba(0, 0, 0, 0.5);
            padding: 10px;
            border-radius: 5px;
        }
        #upgradeMenu {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.8);
            padding: 20px;
            border: 2px solid #fff;
            border-radius: 10px;
            display: none;
            text-align: center;
        }
        button {
            margin: 10px;
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            background: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
        }
        button:hover {
            background: #45a049;
        }
        #joystick {
            position: absolute;
            bottom: 20px;
            left: 20px;
            width: 100px;
            height: 100px;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 50%;
            display: none;
        }
        #joystickInner {
            width: 40px;
            height: 40px;
            background: #fff;
            border-radius: 50%;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
        }
        #fireButton, #missileButton {
            position: absolute;
            bottom: 20px;
            right: 20px;
            width: 60px;
            height: 60px;
            background: #ff4444;
            border-radius: 50%;
            display: none;
            text-align: center;
            line-height: 60px;
            font-size: 16px;
            color: white;
        }
        #missileButton {
            right: 100px;
            background: #ffa500;
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
    <div id="joystick">
        <div id="joystickInner"></div>
    </div>
    <div id="fireButton">Fire</div>
    <div id="missileButton">Missile</div>
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
        let clouds = [];
        let score = 0;
        let level = 1;
        let upgradePoints = 0;
        let gameOver = false;
        let gamePaused = false;
        let keys = {};
        let joystick = { active: false, x: 0, y: 0, baseX: 0, baseY: 0 };
        let isMobile = /Mobi|Android/i.test(navigator.userAgent);

        // Initialize joystick and buttons for mobile
        if (isMobile) {
            document.getElementById('joystick').style.display = 'block';
            document.getElementById('fireButton').style.display = 'block';
            document.getElementById('missileButton').style.display = 'block';
        }

        // Player controls
        document.addEventListener('keydown', (e) => {
            keys[e.code] = true;
            if (e.code === 'Space' && !gameOver && !gamePaused) shootBullet();
            if (e.code === 'KeyM' && player.missiles > 0 && !gameOver && !gamePaused) shootMissile();
        });
        document.addEventListener('keyup', (e) => keys[e.code] = false);

        // Joystick controls
        const joystickEl = document.getElementById('joystick');
        const joystickInner = document.getElementById('joystickInner');
        const fireButton = document.getElementById('fireButton');
        const missileButton = document.getElementById('missileButton');

        joystickEl.addEventListener('touchstart', (e) => {
            e.preventDefault();
            joystick.active = true;
            const touch = e.touches[0];
            joystick.baseX = touch.clientX;
            joystick.baseY = touch.clientY;
        });

        joystickEl.addEventListener('touchmove', (e) => {
            e.preventDefault();
            if (joystick.active) {
                const touch = e.touches[0];
                let dx = touch.clientX - joystick.baseX;
                let dy = touch.clientY - joystick.baseY;
                let dist = Math.sqrt(dx * dx + dy * dy);
                let maxDist = 50;
                if (dist > maxDist) {
                    dx = (dx / dist) * maxDist;
                    dy = (dy / dist) * maxDist;
                }
                joystick.x = dx / maxDist;
                joystick.y = dy / maxDist;
                joystickInner.style.transform = `translate(${dx - 20}px, ${dy - 20}px)`;
            }
        });

        joystickEl.addEventListener('touchend', () => {
            joystick.active = false;
            joystick.x = 0;
            joystick.y = 0;
            joystickInner.style.transform = 'translate(-50%, -50%)';
        });

        fireButton.addEventListener('touchstart', (e) => {
            e.preventDefault();
            if (!gameOver && !gamePaused) shootBullet();
        });

        missileButton.addEventListener('touchstart', (e) => {
            e.preventDefault();
            if (player.missiles > 0 && !gameOver && !gamePaused) shootMissile();
        });

        function shootBullet() {
            if (player.fireCooldown <= 0) {
                bullets.push({
                    x: player.x + player.width / 2,
                    y: player.y,
                    speed: 10,
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
                    speed: 7,
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

        function spawnCloud() {
            clouds.push({
                x: Math.random() * canvas.width,
                y: -50,
                width: 100 + Math.random() * 100,
                height: 50,
                speed: 1 + Math.random()
            });
        }

        function updateUI() {
            document.getElementById('score').textContent = score;
            document.getElementById('level').textContent = level;
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

            enemies.forEach((enemy, eIndex) => {
                if (player.x < enemy.x + enemy.width &&
                    player.x + player.width > enemy.x &&
                    player.y < enemy.y + enemy.height &&
                    player.y + player.height > enemy.y) {
                    player.health -= 10;
                    enemies.splice(eIndex, 1);
                    updateUI();
                    if (player.health <= 0) gameOver = true;
                }
            });
        }

        function update() {
            if (gameOver || gamePaused) return;

            // Player movement
            if (isMobile) {
                player.x += joystick.x * player.speed;
                player.y += joystick.y * player.speed;
            } else {
                if (keys['KeyW'] && player.y > 0) player.y -= player.speed;
                if (keys['KeyS'] && player.y < canvas.height - player.height) player.y += player.speed;
                if (keys['KeyA'] && player.x > 0) player.x -= player.speed;
                if (keys['KeyD'] && player.x < canvas.width - player.width) player.x += player.speed;
            }
            player.x = Math.max(0, Math.min(player.x, canvas.width - player.width));
            player.y = Math.max(0, Math.min(player.y, canvas.height - player.height));

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

            // Update clouds
            clouds.forEach((cloud, index) => {
                cloud.y += cloud.speed;
                if (cloud.y > canvas.height) clouds.splice(index, 1);
            });

            // Spawn new enemies and clouds
            if (Math.random() < 0.02 * level) spawnEnemy();
            if (Math.random() < 0.01) spawnCloud();

            // Check for level completion
            if (enemies.length === 0) {
                gamePaused = true;
                document.getElementById('upgradeMenu').style.display = 'block';
            }

            // Update cooldowns
            if (player.fireCooldown > 0) player.fireCooldown--;
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Draw background (sky and mountains)
            ctx.fillStyle = '#87CEEB';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = '#228B22';
            ctx.beginPath();
            ctx.moveTo(0, canvas.height);
            for (let x = 0; x <= canvas.width; x += 50) {
                ctx.lineTo(x, canvas.height - 100 - Math.sin(x / 100) * 50);
            }
            ctx.lineTo(canvas.width, canvas.height);
            ctx.fill();

            // Draw clouds
            clouds.forEach(cloud => {
                ctx.fillStyle = 'rgba(255, 255, 255, 0.7)';
                ctx.beginPath();
                ctx.ellipse(cloud.x, cloud.y, cloud.width / 2, cloud.height / 2, 0, 0, Math.PI * 2);
                ctx.fill();
            });

            // Draw player (jet)
            ctx.fillStyle = '#4682B4';
            ctx.beginPath();
            ctx.moveTo(player.x + player.width / 2, player.y);
            ctx.lineTo(player.x, player.y + player.height);
            ctx.lineTo(player.x + player.width, player.y + player.height);
            ctx.closePath();
            ctx.fill();
            ctx.fillStyle = '#FFD700';
            ctx.fillRect(player.x + player.width / 2 - 5, player.y + player.height - 10, 10, 10); // Cockpit

            // Draw enemies (enemy jets)
            enemies.forEach(enemy => {
                ctx.fillStyle = '#B22222';
                ctx.beginPath();
                ctx.moveTo(enemy.x + enemy.width / 2, enemy.y + enemy.height);
                ctx.lineTo(enemy.x, enemy.y);
                ctx.lineTo(enemy.x + enemy.width, enemy.y);
                ctx.closePath();
                ctx.fill();
                ctx.fillStyle = '#FF4500';
                ctx.fillRect(enemy.x + enemy.width / 2 - 5, enemy.y + 5, 10, 10); // Cockpit
            });

            // Draw bullets
            ctx.fillStyle = '#FFFF00';
            bullets.forEach(bullet => {
                ctx.beginPath();
                ctx.ellipse(bullet.x, bullet.y, bullet.width / 2, bullet.height / 2, 0, 0, Math.PI * 2);
                ctx.fill();
            });

            // Draw missiles
            ctx.fillStyle = '#FFA500';
            missiles.forEach(missile => {
                ctx.save();
                ctx.translate(missile.x, missile.y);
                if (missile.target) {
                    let dx = missile.target.x + missile.target.width / 2 - missile.x;
                    let dy = missile.target.y + missile.target.height / 2 - missile.y;
                    ctx.rotate(Math.atan2(dy, dx));
                }
                ctx.beginPath();
                ctx.moveTo(0, -missile.height / 2);
                ctx.lineTo(-missile.width / 2, missile.height / 2);
                ctx.lineTo(missile.width / 2, missile.height / 2);
                ctx.closePath();
                ctx.fill();
                ctx.restore();
            });

            // Draw game over
            if (gameOver) {
                ctx.fillStyle = 'white';
                ctx.font = '40px Arial';
                ctx.textAlign = 'center';
                ctx.fillText('Game Over', canvas.width / 2, canvas.height / 2);
            }
        }

        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }

        // Start the game
        for (let i = 0; i < 5; i++) spawnCloud();
        spawnEnemiesForLevel();
        gameLoop();
    </script>
</body>
</html>