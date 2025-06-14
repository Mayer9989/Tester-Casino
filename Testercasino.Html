<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Tank Battle Game</title>
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
        #gameOverMenu {
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
        #moveJoystick, #aimJoystick {
            position: absolute;
            bottom: 20px;
            width: 100px;
            height: 100px;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 50%;
            display: none;
        }
        #moveJoystick {
            left: 20px;
        }
        #aimJoystick {
            right: 20px;
        }
        #moveJoystickInner, #aimJoystickInner {
            width: 40px;
            height: 40px;
            background: #fff;
            border-radius: 50%;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
        }
        #fireButton {
            position: absolute;
            bottom: 100px;
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
    </style>
</head>
<body>
    <div id="gameUI">
        <div>Score: <span id="score">0</span></div>
        <div>Health: <span id="health">100</span></div>
        <div>Team Tanks: <span id="teamCount">3</span></div>
        <div>Enemy Tanks: <span id="enemyCount">3</span></div>
    </div>
    <div id="gameOverMenu">
        <h2>Game Over</h2>
        <p>Final Score: <span id="finalScore">0</span></p>
        <button onclick="restartGame()">Restart</button>
    </div>
    <div id="moveJoystick">
        <div id="moveJoystickInner"></div>
    </div>
    <div id="aimJoystick">
        <div id="aimJoystickInner"></div>
    </div>
    <div id="fireButton">Fire</div>
    <canvas id="gameCanvas"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        canvas.width = 800;
        canvas.height = 600;

        // Audio
        const moveSound = new Audio('https://cdn.pixabay.com/download/audio/2023/03/17/audio_1e2c5a8b5b.mp3'); // Placeholder tank move sound
        const shootSound = new Audio('https://cdn.pixabay.com/download/audio/2022/03/10/audio_4e1b6e5e3f.mp3'); // Placeholder shoot sound
        moveSound.loop = true;
        moveSound.volume = 0.3;
        shootSound.volume = 0.5;

        // Game state
        let player = {
            x: 100,
            y: 500,
            angle: 0,
            turretAngle: 0,
            speed: 2,
            rotationSpeed: 0.05,
            health: 100,
            fireCooldown: 0,
            reloadTime: 30
        };
        let allies = [];
        let enemies = [];
        let shells = [];
        let walls = [];
        let score = 0;
        let gameOver = false;
        let keys = {};
        let moveJoystick = { active: false, x: 0, y: 0, baseX: 0, baseY: 0 };
        let aimJoystick = { active: false, x: 0, y: 0, baseX: 0, baseY: 0 };
        let mouse = { x: canvas.width / 2, y: canvas.height / 2 };
        let isMobile = /Mobi|Android/i.test(navigator.userAgent);

        // Initialize joysticks and fire button for mobile
        if (isMobile) {
            document.getElementById('moveJoystick').style.display = 'block';
            document.getElementById('aimJoystick').style.display = 'block';
            document.getElementById('fireButton').style.display = 'block';
        }

        // Controls
        document.addEventListener('keydown', (e) => {
            keys[e.code] = true;
            if (e.code === 'Space' && !gameOver && player.fireCooldown <= 0) shootShell('player');
        });
        document.addEventListener('keyup', (e) => keys[e.code] = false);
        canvas.addEventListener('mousemove', (e) => {
            const rect = canvas.getBoundingClientRect();
            mouse.x = e.clientX - rect.left;
            mouse.y = e.clientY - rect.top;
        });

        const moveJoystickEl = document.getElementById('moveJoystick');
        const moveJoystickInner = document.getElementById('moveJoystickInner');
        const aimJoystickEl = document.getElementById('aimJoystick');
        const aimJoystickInner = document.getElementById('aimJoystickInner');
        const fireButton = document.getElementById('fireButton');

        moveJoystickEl.addEventListener('touchstart', (e) => {
            e.preventDefault();
            moveJoystick.active = true;
            const touch = e.touches[0];
            moveJoystick.baseX = touch.clientX;
            moveJoystick.baseY = touch.clientY;
        });

        moveJoystickEl.addEventListener('touchmove', (e) => {
            e.preventDefault();
            if (moveJoystick.active) {
                const touch = e.touches[0];
                let dx = touch.clientX - moveJoystick.baseX;
                let dy = touch.clientY - moveJoystick.baseY;
                let dist = Math.sqrt(dx * dx + dy * dy);
                let maxDist = 50;
                if (dist > maxDist) {
                    dx = (dx / dist) * maxDist;
                    dy = (dy / dist) * maxDist;
                }
                moveJoystick.x = dx / maxDist;
                moveJoystick.y = dy / maxDist;
                moveJoystickInner.style.transform = `translate(${dx - 20}px, ${dy - 20}px)`;
            }
        });

        moveJoystickEl.addEventListener('touchend', () => {
            moveJoystick.active = false;
            moveJoystick.x = 0;
            moveJoystick.y = 0;
            moveJoystickInner.style.transform = 'translate(-50%, -50%)';
        });

        aimJoystickEl.addEventListener('touchstart', (e) => {
            e.preventDefault();
            aimJoystick.active = true;
            const touch = e.touches[0];
            aimJoystick.baseX = touch.clientX;
            aimJoystick.baseY = touch.clientY;
        });

        aimJoystickEl.addEventListener('touchmove', (e) => {
            e.preventDefault();
            if (aimJoystick.active) {
                const touch = e.touches[0];
                let dx = touch.clientX - aimJoystick.baseX;
                let dy = touch.clientY - aimJoystick.baseY;
                let dist = Math.sqrt(dx * dx + dy * dy);
                let maxDist = 50;
                if (dist > maxDist) {
                    dx = (dx / dist) * maxDist;
                    dy = (dy / dist) * maxDist;
                }
                aimJoystick.x = dx / maxDist;
                aimJoystick.y = dy / maxDist;
                aimJoystickInner.style.transform = `translate(${dx - 20}px, ${dy - 20}px)`;
            }
        });

        aimJoystickEl.addEventListener('touchend', () => {
            aimJoystick.active = false;
            aimJoystick.x = 0;
            aimJoystick.y = 0;
            aimJoystickInner.style.transform = 'translate(-50%, -50%)';
        });

        fireButton.addEventListener('touchstart', (e) => {
            e.preventDefault();
            if (!gameOver && player.fireCooldown <= 0) shootShell('player');
        });

        function shootShell(source, x, y, angle, tank) {
            shootSound.play();
            shells.push({
                x: x,
                y: y,
                angle: angle,
                speed: 8,
                source: source,
                tank: tank,
                radius: 5
            });
            if (source === 'player') player.fireCooldown = player.reloadTime;
            if (tank) tank.fireCooldown = 60;
        }

        function spawnTank(team, x, y) {
            return {
                x: x,
                y: y,
                angle: team === 'ally' ? 0 : Math.PI,
                turretAngle: team === 'ally' ? 0 : Math.PI,
                speed: 1.5,
                rotationSpeed: 0.03,
                health: 100,
                fireCooldown: 0,
                team: team
            };
        }

        function spawnWalls() {
            walls = [
                { x: 200, y: 200, width: 20, height: 100 }, // Vertical wall
                { x: 600, y: 400, width: 20, height: 100 }, // Vertical wall
                { x: 400, y: 300, width: 100, height: 20 }, // Horizontal wall
                { x: 0, y: 0, width: canvas.width, height: 10 }, // Top border
                { x: 0, y: canvas.height - 10, width: canvas.width, height: 10 }, // Bottom border
                { x: 0, y: 0, width: 10, height: canvas.height }, // Left border
                { x: canvas.width - 10, y: 0, width: 10, height: canvas.height } // Right border
            ];
        }

        function updateUI() {
            document.getElementById('score').textContent = score;
            document.getElementById('health').textContent = player.health;
            document.getElementById('teamCount').textContent = allies.length + 1;
            document.getElementById('enemyCount').textContent = enemies.length;
            document.getElementById('finalScore').textContent = score;
        }

        function restartGame() {
            gameOver = false;
            document.getElementById('gameOverMenu').style.display = 'none';
            score = 0;
            player = spawnTank('ally', 100, 500);
            allies = [
                spawnTank('ally', 150, 450),
                spawnTank('ally', 100, 400)
            ];
            enemies = [
                spawnTank('enemy', 700, 100),
                spawnTank('enemy', 650, 150),
                spawnTank('enemy', 700, 200)
            ];
            shells = [];
            spawnWalls();
            updateUI();
        }

        function checkCollision(x, y, width, height) {
            for (let wall of walls) {
                if (x < wall.x + wall.width && x + width > wall.x &&
                    y < wall.y + wall.height && y + height > wall.y) {
                    return true;
                }
            }
            return false;
        }

        function update() {
            if (gameOver) {
                document.getElementById('gameOverMenu').style.display = 'block';
                moveSound.pause();
                return;
            }

            // Player movement
            let isMoving = false;
            if (isMobile) {
                if (moveJoystick.y < -0.2) {
                    let newX = player.x + Math.cos(player.angle) * player.speed;
                    let newY = player.y + Math.sin(player.angle) * player.speed;
                    if (!checkCollision(newX - 20, newY - 20, 40, 40)) {
                        player.x = newX;
                        player.y = newY;
                        isMoving = true;
                    }
                } else if (moveJoystick.y > 0.2) {
                    let newX = player.x - Math.cos(player.angle) * player.speed;
                    let newY = player.y - Math.sin(player.angle) * player.speed;
                    if (!checkCollision(newX - 20, newY - 20, 40, 40)) {
                        player.x = newX;
                        player.y = newY;
                        isMoving = true;
                    }
                }
                if (aimJoystick.x !== 0 || aimJoystick.y !== 0) {
                    player.turretAngle = Math.atan2(aimJoystick.y, aimJoystick.x);
                }
            } else {
                if (keys['KeyW']) {
                    let newX = player.x + Math.cos(player.angle) * player.speed;
                    let newY = player.y + Math.sin(player.angle) * player.speed;
                    if (!checkCollision(newX - 20, newY - 20, 40, 40)) {
                        player.x = newX;
                        player.y = newY;
                        isMoving = true;
                    }
                }
                if (keys['KeyS']) {
                    let newX = player.x - Math.cos(player.angle) * player.speed;
                    let newY = player.y - Math.sin(player.angle) * player.speed;
                    if (!checkCollision(newX - 20, newY - 20, 40, 40)) {
                        player.x = newX;
                        player.y = newY;
                        isMoving = true;
                    }
                }
                if (keys['KeyA']) player.angle -= player.rotationSpeed;
                if (keys['KeyD']) player.angle += player.rotationSpeed;
                player.turretAngle = Math.atan2(mouse.y - player.y, mouse.x - player.x);
            }
            if (isMoving && !moveSound.playing) moveSound.play();
            else if (!isMoving) moveSound.pause();

            // Update allies
            allies.forEach(ally => {
                let nearestEnemy = enemies.reduce((nearest, enemy) => {
                    let dist = Math.hypot(enemy.x - ally.x, enemy.y - ally.y);
                    return dist < nearest.dist ? { tank: enemy, dist: dist } : nearest;
                }, { tank: null, dist: Infinity }).tank;
                if (nearestEnemy) {
                    ally.turretAngle = Math.atan2(nearestEnemy.y - ally.y, nearestEnemy.x - ally.x);
                    if (ally.fireCooldown <= 0 && Math.random() < 0.02) {
                        shootShell('ally', ally.x, ally.y, ally.turretAngle, ally);
                    }
                }
                ally.fireCooldown--;
            });

            // Update enemies
            enemies.forEach((enemy, index) => {
                let target = Math.random() < 0.5 ? player : allies[Math.floor(Math.random() * allies.length)];
                if (target && target.health > 0) {
                    let dx = target.x - enemy.x;
                    let dy = target.y - enemy.y;
                    let desiredAngle = Math.atan2(dy, dx);
                    enemy.angle += Math.sign(desiredAngle - enemy.angle) * enemy.rotationSpeed;
                    let newX = enemy.x + Math.cos(enemy.angle) * enemy.speed;
                    let newY = enemy.y + Math.sin(enemy.angle) * enemy.speed;
                    if (!checkCollision(newX - 20, newY - 20, 40, 40)) {
                        enemy.x = newX;
                        enemy.y = newY;
                    }
                    enemy.turretAngle = desiredAngle;
                    if (enemy.fireCooldown <= 0 && Math.random() < 0.02) {
                        shootShell('enemy', enemy.x, enemy.y, enemy.turretAngle, enemy);
                    }
                }
                enemy.fireCooldown--;
            });

            // Update shells
            shells.forEach((shell, index) => {
                shell.x += Math.cos(shell.angle) * shell.speed;
                shell.y += Math.sin(shell.angle) * shell.speed;
                if (shell.x < 0 || shell.x > canvas.width || shell.y < 0 || shell.y > canvas.height) {
                    shells.splice(index, 1);
                    return;
                }
                for (let wall of walls) {
                    if (shell.x - shell.radius < wall.x + wall.width &&
                        shell.x + shell.radius > wall.x &&
                        shell.y - shell.radius < wall.y + wall.height &&
                        shell.y + shell.radius > wall.y) {
                        shells.splice(index, 1);
                        return;
                    }
                }
            });

            // Check collisions
            shells.forEach((shell, sIndex) => {
                if (shell.source !== 'player' && Math.hypot(shell.x - player.x, shell.y - player.y) < 25) {
                    player.health -= 20;
                    shells.splice(sIndex, 1);
                    updateUI();
                    if (player.health <= 0) gameOver = true;
                }
                if (shell.source !== 'ally') {
                    allies.forEach((ally, aIndex) => {
                        if (Math.hypot(shell.x - ally.x, shell.y - ally.y) < 25) {
                            ally.health -= 20;
                            shells.splice(sIndex, 1);
                            if (ally.health <= 0) allies.splice(aIndex, 1);
                            updateUI();
                        }
                    });
                }
                if (shell.source !== 'enemy') {
                    enemies.forEach((enemy, eIndex) => {
                        if (Math.hypot(shell.x - enemy.x, shell.y - enemy.y) < 25) {
                            enemy.health -= 20;
                            shells.splice(sIndex, 1);
                            if (enemy.health <= 0) {
                                enemies.splice(eIndex, 1);
                                score += 10;
                                updateUI();
                            }
                        }
                    });
                }
            });

            // Check game over
            if (enemies.length === 0) {
                score += 50;
                restartGame();
            } else if (player.health <= 0 && allies.length === 0) {
                gameOver = true;
            }

            // Update cooldowns
            if (player.fireCooldown > 0) player.fireCooldown--;
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Draw background (grass)
            ctx.fillStyle = '#4CAF50';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Draw walls
            ctx.fillStyle = '#8B4513';
            walls.forEach(wall => {
                ctx.fillRect(wall.x, wall.y, wall.width, wall.height);
            });

            // Draw player
            ctx.save();
            ctx.translate(player.x, player.y);
            ctx.rotate(player.angle);
            ctx.fillStyle = '#2E8B57';
            ctx.fillRect(-20, -15, 40, 30); // Body
            ctx.fillStyle = '#228B22';
            ctx.fillRect(-15, -10, 30, 20); // Tracks
            ctx.restore();
            ctx.save();
            ctx.translate(player.x, player.y);
            ctx.rotate(player.turretAngle);
            ctx.fillStyle = '#2E8B57';
            ctx.fillRect(0, -5, 25, 10); // Turret
            ctx.restore();
            if (player.health < 100) {
                ctx.fillStyle = 'red';
                ctx.fillRect(player.x - 20, player.y - 25, player.health / 2.5, 5);
            }

            // Draw allies
            allies.forEach(ally => {
                ctx.save();
                ctx.translate(ally.x, ally.y);
                ctx.rotate(ally.angle);
                ctx.fillStyle = '#2E8B57';
                ctx.fillRect(-20, -15, 40, 30);
                ctx.fillStyle = '#228B22';
                ctx.fillRect(-15, -10, 30, 20);
                ctx.restore();
                ctx.save();
                ctx.translate(ally.x, ally.y);
                ctx.rotate(ally.turretAngle);
                ctx.fillStyle = '#2E8B57';
                ctx.fillRect(0, -5, 25, 10);
                ctx.restore();
                if (ally.health < 100) {
                    ctx.fillStyle = 'red';
                    ctx.fillRect(ally.x - 20, ally.y - 25, ally.health / 2.5, 5);
                }
            });

            // Draw enemies
            enemies.forEach(enemy => {
                ctx.save();
                ctx.translate(enemy.x, enemy.y);
                ctx.rotate(enemy.angle);
                ctx.fillStyle = '#8B0000';
                ctx.fillRect(-20, -15, 40, 30);
                ctx.fillStyle = '#A52A2A';
                ctx.fillRect(-15, -10, 30, 20);
                ctx.restore();
                ctx.save();
                ctx.translate(enemy.x, enemy.y);
                ctx.rotate(enemy.turretAngle);
                ctx.fillStyle = '#8B0000';
                ctx.fillRect(0, -5, 25, 10);
                ctx.restore();
                if (enemy.health < 100) {
                    ctx.fillStyle = 'red';
                    ctx.fillRect(enemy.x - 20, enemy.y - 25, enemy.health / 2.5, 5);
                }
            });

            // Draw shells
            shells.forEach(shell => {
                ctx.fillStyle = shell.source === 'enemy' ? '#FFA500' : '#FFFF00';
                ctx.beginPath();
                ctx.arc(shell.x, shell.y, shell.radius, 0, Math.PI * 2);
                ctx.fill();
            });
        }

        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }

        // Start the game
        restartGame();
        gameLoop();
    </script>
</body>
</html>