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
        canvas.width = 1200;
        canvas.height = 800;

        // Audio
        const moveSound = new Audio('https://cdn.pixabay.com/download/audio/2023/03/17/audio_1e2c5a8b5b.mp3'); // Placeholder tank move sound
        const shootSound = new Audio('https://cdn.pixabay.com/download/audio/2022/03/10/audio_4e1b6e5e3f.mp3'); // Placeholder shoot sound
        moveSound.loop = true;
        moveSound.volume = 0.3;
        shootSound.volume = 0.5;

        // Game state
        let player = {
            x: 100,
            y: 700,
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
        let obstacles = [];
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
            if (e.code === 'Space' && !gameOver && player.fireCooldown <= 0) {
                shootShell('player', player.x, player.y, player.turretAngle, player);
            }
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
            if (!gameOver && player.fireCooldown <= 0) {
                shootShell('player', player.x, player.y, player.turretAngle, player);
            }
        });

        function shootShell(source, x, y, angle, tank) {
            shootSound.play();
            shells.push({
                x: x + Math.cos(angle) * 25, // Offset from turret tip
                y: y + Math.sin(angle) * 25,
                angle: angle,
                speed: 8,
                source: source,
                tank: tank,
                radius: 5
            });
            if (source === 'player') player.fireCooldown = player.reloadTime;
            if (tank && tank !== player) tank.fireCooldown = 60;
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

        function spawnObstacles() {
            obstacles = [
                // Border walls
                { type: 'wall', x: 0, y: 0, width: canvas.width, height: 10 }, // Top
                { type: 'wall', x: 0, y: canvas.height - 10, width: canvas.width, height: 10 }, // Bottom
                { type: 'wall', x: 0, y: 0, width: 10, height: canvas.height }, // Left
                { type: 'wall', x: canvas.width - 10, y: 0, width: 10, height: canvas.height }, // Right
                // Inner walls
                { type: 'wall', x: 300, y: 200, width: 20, height: 150 },
                { type: 'wall', x: 900, y: 450, width: 20, height: 150 },
                { type: 'wall', x: 600, y: 300, width: 150, height: 20 },
                // Houses
                { type: 'house', x: 200, y: 100, width: 80, height: 80 },
                { type: 'house', x: 1000, y: 600, width: 80, height: 80 },
                // Anti-tank hedgehogs
                { type: 'hedgehog', x: 400, y: 400, width: 30, height: 30 },
                { type: 'hedgehog', x: 800, y: 200, width: 30, height: 30 }
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
            player = spawnTank('ally', 100, 700);
            allies = [
                spawnTank('ally', 150, 650),
                spawnTank('ally', 100, 600)
            ];
            enemies = [
                spawnTank('enemy', 1100, 100),
                spawnTank('enemy', 1050, 150),
                spawnTank('enemy', 1100, 200)
            ];
            shells = [];
            spawnObstacles();
            updateUI();
        }

        function checkCollision(x, y, width, height) {
            for (let obstacle of obstacles) {
                if (x < obstacle.x + obstacle.width && x + width > obstacle.x &&
                    y < obstacle.y + obstacle.height && y + height > obstacle.y) {
                    return true;
                }
            }
            return false;
        }

        function drawTank(x, y, angle, turretAngle, team, health) {
            ctx.save();
            ctx.translate(x, y);
            ctx.rotate(angle);
            // Tank body with camouflage pattern
            const gradient = ctx.createLinearGradient(-20, -15, 20, 15);
            gradient.addColorStop(0, team === 'ally' ? '#3C5F3A' : '#5C2E2E'); // Olive green or dark red
            gradient.addColorStop(0.5, team === 'ally' ? '#4A7048' : '#7A3C3C');
            gradient.addColorStop(1, team === 'ally' ? '#2E4A2C' : '#4A2E2E');
            ctx.fillStyle = gradient;
            ctx.fillRect(-20, -15, 40, 30);
            // Tracks
            ctx.fillStyle = '#333333';
            ctx.fillRect(-15, -10, 30, 5);
            ctx.fillRect(-15, 5, 30, 5);
            ctx.restore();
            // Turret
            ctx.save();
            ctx.translate(x, y);
            ctx.rotate(turretAngle);
            ctx.fillStyle = gradient;
            ctx.fillRect(0, -5, 25, 10);
            ctx.fillStyle = '#444444';
            ctx.fillRect(0, -2, 20, 4); // Barrel detail
            ctx.restore();
            // Health bar
            if (health < 100) {
                ctx.fillStyle = 'red';
                ctx.fillRect(x - 20, y - 25, health / 2.5, 5);
            }
        }

        function drawShell(shell) {
            ctx.save();
            ctx.translate(shell.x, shell.y);
            ctx.rotate(shell.angle);
            // Shell with metallic texture
            const gradient = ctx.createLinearGradient(-5, -5, 5, 5);
            gradient.addColorStop(0, shell.source === 'enemy' ? '#FF8C00' : '#FFD700'); // Orange or gold
            gradient.addColorStop(1, '#888888');
            ctx.fillStyle = gradient;
            ctx.beginPath();
            ctx.arc(0, 0, shell.radius, 0, Math.PI * 2);
            ctx.fill();
            ctx.restore();
        }

        function drawMap() {
            // Ground with grass and dirt pattern
            const gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
            gradient.addColorStop(0, '#355E3B');
            gradient.addColorStop(1, '#4A7048');
            ctx.fillStyle = gradient;
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            // Add dirt patches
            for (let i = 0; i < 20; i++) {
                ctx.fillStyle = 'rgba(139, 69, 19, 0.5)';
                ctx.beginPath();
                ctx.arc(Math.random() * canvas.width, Math.random() * canvas.height, 20 + Math.random() * 30, 0, Math.PI * 2);
                ctx.fill();
            }
        }

        function drawObstacle(obstacle) {
            ctx.save();
            if (obstacle.type === 'wall') {
                // Concrete wall texture
                const gradient = ctx.createLinearGradient(obstacle.x, obstacle.y, obstacle.x + obstacle.width, obstacle.y + obstacle.height);
                gradient.addColorStop(0, '#808080');
                gradient.addColorStop(1, '#A9A9A9');
                ctx.fillStyle = gradient;
                ctx.fillRect(obstacle.x, obstacle.y, obstacle.width, obstacle.height);
                // Add cracks
                ctx.strokeStyle = 'rgba(0, 0, 0, 0.3)';
                ctx.beginPath();
                ctx.moveTo(obstacle.x + 5, obstacle.y + 5);
                ctx.lineTo(obstacle.x + obstacle.width - 5, obstacle.y + obstacle.height - 5);
                ctx.stroke();
            } else if (obstacle.type === 'house') {
                // House with brick texture
                const gradient = ctx.createLinearGradient(obstacle.x, obstacle.y, obstacle.x, obstacle.y + obstacle.height);
                gradient.addColorStop(0, '#8B0000');
                gradient.addColorStop(1, '#A52A2A');
                ctx.fillStyle = gradient;
                ctx.fillRect(obstacle.x, obstacle.y, obstacle.width, obstacle.height);
                // Roof
                ctx.fillStyle = '#4B2E2E';
                ctx.beginPath();
                ctx.moveTo(obstacle.x, obstacle.y);
                ctx.lineTo(obstacle.x + obstacle.width / 2, obstacle.y - 20);
                ctx.lineTo(obstacle.x + obstacle.width, obstacle.y);
                ctx.closePath();
                ctx.fill();
            } else if (obstacle.type === 'hedgehog') {
                // Anti-tank hedgehog (metal cross)
                ctx.fillStyle = '#666666';
                ctx.fillRect(obstacle.x + 5, obstacle.y, 20, 30);
                ctx.fillRect(obstacle.x, obstacle.y + 5, 30, 20);
                ctx.fillStyle = '#888888';
                ctx.fillRect(obstacle.x + 7, obstacle.y + 2, 16, 26);
                ctx.fillRect(obstacle.x + 2, obstacle.y + 7, 26, 16);
            }
            ctx.restore();
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
                    player.angle += aimJoystick.x * player.rotationSpeed; // Rotate tank body with right joystick
                    player.turretAngle = player.angle; // Turret follows body for simplicity
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
            if (isMoving) {
                if (!moveSound.playing) moveSound.play();
            } else {
                moveSound.pause();
            }

            // Update allies
            allies.forEach(ally => {
                let nearestEnemy = enemies.reduce((nearest, enemy) => {
                    let dist = Math.hypot(enemy.x - ally.x, enemy.y - ally.y);
                    return dist < nearest.dist ? { tank: enemy, dist: dist } : nearest;
                }, { tank: null, dist: Infinity }).tank;
                if (nearestEnemy) {
                    let dx = nearestEnemy.x - ally.x;
                    let dy = nearestEnemy.y - ally.y;
                    ally.turretAngle = Math.atan2(dy, dx);
                    ally.angle += Math.sign(ally.turretAngle - ally.angle) * ally.rotationSpeed;
                    let newX = ally.x + Math.cos(ally.angle) * ally.speed;
                    let newY = ally.y + Math.sin(ally.angle) * ally.speed;
                    if (!checkCollision(newX - 20, newY - 20, 40, 40)) {
                        ally.x = newX;
                        ally.y = newY;
                    }
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
                for (let obstacle of obstacles) {
                    if (shell.x - shell.radius < obstacle.x + obstacle.width &&
                        shell.x + shell.radius > obstacle.x &&
                        shell.y - shell.radius < obstacle.y + obstacle.height &&
                        shell.y + shell.radius > obstacle.y) {
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

            // Draw map
            drawMap();

            // Draw obstacles
            obstacles.forEach(obstacle => drawObstacle(obstacle));

            // Draw player
            drawTank(player.x, player.y, player.angle, player.turretAngle, 'ally', player.health);

            // Draw allies
            allies.forEach(ally => {
                drawTank(ally.x, ally.y, ally.angle, ally.turretAngle, 'ally', ally.health);
            });

            // Draw enemies
            enemies.forEach(enemy => {
                drawTank(enemy.x, enemy.y, enemy.angle, enemy.turretAngle, 'enemy', enemy.health);
            });

            // Draw shells
            shells.forEach(shell => drawShell(shell));
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