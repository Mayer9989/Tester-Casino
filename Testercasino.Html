<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Starstrike: Cosmic Battle</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: #0a0a1a;
            display: flex;
            flex-direction: column;
            align-items: center;
            font-family: 'Arial', sans-serif;
            color: #fff;
            touch-action: none;
        }
        canvas {
            border: 2px solid #00ffcc;
            max-width: 100%;
            max-height: 80vh;
            display: none;
        }
        #mainMenu, #shopMenu, #upgradeMenu, #gameOverMenu {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(10, 10, 26, 0.9);
            padding: 20px;
            border: 2px solid #00ffcc;
            border-radius: 10px;
            text-align: center;
            color: #fff;
            display: none;
        }
        #mainMenu, #shopMenu {
            width: 400px;
        }
        #gameUI {
            width: 100%;
            max-width: 1200px;
            background: rgba(0, 0, 0, 0.7);
            padding: 10px;
            font-size: 18px;
            display: none;
            text-align: center;
            color: #00ffcc;
            border-top: 2px solid #00ffcc;
        }
        #gameUI div {
            display: inline-block;
            margin: 0 20px;
        }
        h1 {
            font-size: 36px;
            margin: 10px 0;
            color: #00ffcc;
            text-shadow: 0 0 10px #00ffcc;
        }
        h2 {
            font-size: 24px;
            margin: 10px 0;
            color: #00ffcc;
        }
        button {
            margin: 10px;
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            background: #00ffcc;
            color: #0a0a1a;
            border: none;
            border-radius: 5px;
            transition: background 0.3s;
        }
        button:hover {
            background: #00cc99;
        }
        #shopMenu button.shop-nav {
            width: 40%;
            display: inline-block;
        }
        #shopMenu button.item-button {
            width: 80%;
            display: block;
            margin: 5px auto;
        }
        #joystick {
            position: fixed;
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
        #fireButton, #missileButton, #specialButton {
            position: fixed;
            bottom: 20px;
            width: 60px;
            height: 60px;
            border-radius: 50%;
            display: none;
            text-align: center;
            line-height: 60px;
            font-size: 14px;
            color: #fff;
        }
        #fireButton {
            right: 20px;
            background: #ff4444;
        }
        #missileButton {
            right: 100px;
            background: #ffa500;
        }
        #specialButton {
            right: 180px;
            background: #00ccff;
        }
        .shop-item {
            background: rgba(255, 255, 255, 0.1);
            padding: 10px;
            margin: 5px 0;
            border-radius: 5px;
        }
        .shop-item span {
            display: block;
            font-size: 14px;
            color: #00ffcc;
        }
    </style>
</head>
<body>
    <div id="mainMenu">
        <h1>Starstrike: Cosmic Battle</h1>
        <button onclick="startGame()">Play</button>
        <button onclick="openShop()">Shop</button>
    </div>
    <div id="shopMenu">
        <h2>Starship Shop</h2>
        <p>Credits: $<span id="credits">1000</span></p>
        <button class="shop-nav" onclick="scrollShop(-1)">Up</button>
        <button class="shop-nav" onclick="scrollShop(1)">Down</button>
        <div id="shopItems"></div>
        <button onclick="backToMenu()">Back</button>
    </div>
    <canvas id="gameCanvas"></canvas>
    <div id="gameUI">
        <div>Score: <span id="score">0</span></div>
        <div>Level: <span id="level">1</span></div>
        <div>Health: <span id="health">100</span></div>
        <div>Missiles: <span id="missiles">5</span></div>
        <div>Credits: $<span id="creditsUI">1000</span></div>
    </div>
    <div id="upgradeMenu">
        <h2>Upgrade Menu</h2>
        <p>Upgrade Points: <span id="upgradePoints">0</span></p>
        <button onclick="upgradeFireRate()">Upgrade Fire Rate ($10)</button>
        <button onclick="upgradeHealth()">Upgrade Max Health ($10)</button>
        <button onclick="upgradeMissiles()">Add Missiles ($15)</button>
        <button onclick="startNextLevel()">Continue</button>
    </div>
    <div id="gameOverMenu">
        <h2>Game Over</h2>
        <p>Final Score: <span id="finalScore">0</span></p>
        <button onclick="restartGame()">Restart</button>
        <button onclick="backToMenu()">Main Menu</button>
    </div>
    <div id="joystick">
        <div id="joystickInner"></div>
    </div>
    <div id="fireButton">Fire</div>
    <div id="missileButton">Missile</div>
    <div id="specialButton">Special</div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        canvas.width = 1200;
        canvas.height = 800;

        // Game state
        let gameState = 'menu';
        let player = null;
        let enemies = [];
        let lasers = [];
        let missiles = [];
        let stars = [];
        let nebulae = [];
        let explosions = [];
        let score = 0;
        let level = 1;
        let upgradePoints = 0;
        let credits = 1000;
        let gameOver = false;
        let gamePaused = false;
        let keys = {};
        let joystick = { active: false, x: 0, y: 0, baseX: 0, baseY: 0 };
        let mouse = { x: canvas.width / 2, y: canvas.height / 2 };
        let isMobile = /Mobi|Android/i.test(navigator.userAgent);
        let shopScrollIndex = 0;
        const itemsPerPage = 2;

        // Ship configurations
        const ships = [
            {
                id: 'falcon',
                name: 'Star Falcon',
                cost: 0,
                health: 100,
                speed: 5,
                fireRate: 10,
                missileCount: 5,
                missileDamage: 50,
                laserDamage: 5,
                special: 'boost',
                specialCooldown: 300,
                color: '#4682B4',
                cockpitColor: '#FFD700'
            },
            {
                id: 'raptor',
                name: 'Cosmo Raptor',
                cost: 500,
                health: 120,
                speed: 4,
                fireRate: 8,
                missileCount: 3,
                missileDamage: 75,
                laserDamage: 7,
                special: 'shield',
                specialCooldown: 600,
                color: '#228B22',
                cockpitColor: '#00FF00'
            },
            {
                id: 'phoenix',
                name: 'Phoenix Blaze',
                cost: 1000,
                health: 80,
                speed: 6,
                fireRate: 12,
                missileCount: 8,
                missileDamage: 40,
                laserDamage: 4,
                special: 'nova',
                specialCooldown: 900,
                color: '#B22222',
                cockpitColor: '#FFA500'
            }
        ];

        let ownedShips = ['falcon'];
        let selectedShipId = 'falcon';

        // Initialize controls
        function toggleGameControls(show) {
            const display = show && isMobile ? 'block' : 'none';
            document.getElementById('joystick').style.display = display;
            document.getElementById('fireButton').style.display = display;
            document.getElementById('missileButton').style.display = display;
            document.getElementById('specialButton').style.display = display;
            document.getElementById('gameUI').style.display = show ? 'block' : 'none';
        }

        // Input handling
        document.addEventListener('keydown', (e) => {
            keys[e.code] = true;
            if (gameState === 'game' && !gameOver && !gamePaused) {
                if (e.code === 'Space') shootLaser();
                if (e.code === 'KeyM' && player.missiles > 0) shootMissile();
                if (e.code === 'KeyE' && player.specialCooldown <= 0) useSpecial();
            }
        });
        document.addEventListener('keyup', (e) => keys[e.code] = false);
        canvas.addEventListener('mousemove', (e) => {
            const rect = canvas.getBoundingClientRect();
            mouse.x = e.clientX - rect.left;
            mouse.y = e.clientY - rect.top;
        });

        const joystickEl = document.getElementById('joystick');
        const joystickInner = document.getElementById('joystickInner');
        const fireButton = document.getElementById('fireButton');
        const missileButton = document.getElementById('missileButton');
        const specialButton = document.getElementById('specialButton');

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
            if (gameState === 'game' && !gameOver && !gamePaused) shootLaser();
        });

        missileButton.addEventListener('touchstart', (e) => {
            e.preventDefault();
            if (gameState === 'game' && !gameOver && !gamePaused && player.missiles > 0) shootMissile();
        });

        specialButton.addEventListener('touchstart', (e) => {
            e.preventDefault();
            if (gameState === 'game' && !gameOver && !gamePaused && player.specialCooldown <= 0) useSpecial();
        });

        // Game functions
        function initPlayer(shipId) {
            const ship = ships.find(s => s.id === shipId);
            return {
                x: canvas.width / 2,
                y: canvas.height - 100,
                width: 40,
                height: 50,
                vx: 0,
                vy: 0,
                angle: -Math.PI / 2,
                speed: ship.speed,
                maxHealth: ship.health,
                health: ship.health,
                fireRate: ship.fireRate,
                fireCooldown: 0,
                missileCount: ship.missileCount,
                missiles: ship.missileCount,
                missileDamage: ship.missileDamage,
                laserDamage: ship.laserDamage,
                special: ship.special,
                specialCooldown: 0,
                specialTimer: 0,
                color: ship.color,
                cockpitColor: ship.cockpitColor,
                drag: 0.98,
                thrust: 0.2
            };
        }

        function shootLaser(source = player, isEnemy = false) {
            if (source.fireCooldown <= 0) {
                const targetX = isMobile && !isEnemy ? (findNearestEnemy()?.x + findNearestEnemy()?.width / 2 || canvas.width / 2) : mouse.x;
                const targetY = isMobile && !isEnemy ? (findNearestEnemy()?.y + findNearestEnemy()?.height / 2 || 0) : mouse.y;
                const angle = isEnemy ? source.angle : Math.atan2(targetY - (source.y + source.height / 2), targetX - (source.x + source.width / 2));
                lasers.push({
                    x: source.x + source.width / 2,
                    y: source.y + source.height / 2,
                    vx: Math.cos(angle) * 15,
                    vy: Math.sin(angle) * 15,
                    length: 50,
                    damage: source.laserDamage,
                    lifetime: 10,
                    isEnemy: isEnemy
                });
                source.fireCooldown = source.fireRate;
            }
        }

        function shootMissile(source = player, isEnemy = false) {
            if (source.missiles > 0) {
                const missile = {
                    x: source.x + source.width / 2,
                    y: source.y,
                    vx: 0,
                    vy: 0,
                    speed: 7,
                    width: 10,
                    height: 20,
                    target: isEnemy ? player : findNearestEnemy(),
                    damage: source.missileDamage,
                    isEnemy: isEnemy,
                    trail: []
                };
                missiles.push(missile);
                source.missiles--;
                updateUI();
            }
        }

        function useSpecial() {
            if (player.specialCooldown <= 0) {
                switch (player.special) {
                    case 'boost':
                        player.specialTimer = 120;
                        player.speed *= 2;
                        break;
                    case 'shield':
                        player.specialTimer = 300;
                        break;
                    case 'nova':
                        spawnExplosion(player.x + player.width / 2, player.y + player.height / 2, 100);
                        enemies.forEach(enemy => {
                            let dist = Math.hypot(enemy.x - player.x, enemy.y - player.y);
                            if (dist < 150) {
                                enemy.health -= 50;
                                if (enemy.health <= 0) {
                                    enemies.splice(enemies.indexOf(enemy), 1);
                                    score += enemy.scoreValue;
                                    credits += enemy.creditValue;
                                    spawnExplosion(enemy.x + enemy.width / 2, enemy.y + enemy.height / 2, 40);
                                }
                            }
                        });
                        break;
                }
                player.specialCooldown = ships.find(s => s.id === selectedShipId).specialCooldown;
            }
        }

        function spawnExplosion(x, y, size = 30) {
            explosions.push({
                x: x,
                y: y,
                size: size,
                lifetime: 30,
                particles: Array.from({ length: 30 }, () => ({
                    x: 0,
                    y: 0,
                    vx: (Math.random() - 0.5) * 6,
                    vy: (Math.random() - 0.5) * 6,
                    radius: 2 + Math.random() * 4,
                    alpha: 1
                }))
            });
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

        function spawnEnemy(type = 'drone') {
            const enemyTypes = {
                drone: { width: 40, height: 50, speed: 2, health: 50, laserDamage: 3, fireRate: 30, scoreValue: 10, creditValue: 5 },
                cruiser: { width: 60, height: 70, speed: 1, health: 100, laserDamage: 5, fireRate: 20, scoreValue: 20, creditValue: 10 },
                bomber: { width: 50, height: 60, speed: 1.5, health: 80, missileDamage: 20, missileCount: 3, fireRate: 60, scoreValue: 15, creditValue: 8 }
            };
            const config = enemyTypes[type];
            enemies.push({
                x: Math.random() * (canvas.width - config.width),
                y: -config.height,
                width: config.width,
                height: config.height,
                vx: 0,
                vy: config.speed + level * 0.2,
                angle: Math.PI / 2,
                health: config.health + level * 10,
                fireRate: config.fireRate,
                fireCooldown: 0,
                laserDamage: config.laserDamage || 0,
                missileDamage: config.missileDamage || 0,
                missileCount: config.missileCount || 0,
                missiles: config.missileCount || 0,
                scoreValue: config.scoreValue,
                creditValue: config.creditValue,
                type: type,
                color: type === 'drone' ? '#B22222' : type === 'cruiser' ? '#4B0082' : '#FFA500',
                cockpitColor: '#FF4500'
            });
        }

        function spawnStar() {
            stars.push({
                x: Math.random() * canvas.width,
                y: Math.random() * canvas.height,
                radius: 0.5 + Math.random(),
                speed: 0.5 + Math.random()
            });
        }

        function spawnNebula() {
            nebulae.push({
                x: Math.random() * canvas.width,
                y: Math.random() * canvas.height,
                radius: 50 + Math.random() * 100,
                color: `hsl(${Math.random() * 360}, 50%, 20%)`
            });
        }

        function updateUI() {
            document.getElementById('score').textContent = score;
            document.getElementById('level').textContent = level;
            document.getElementById('health').textContent = Math.max(0, player.health);
            document.getElementById('missiles').textContent = player.missiles;
            document.getElementById('credits').textContent = credits;
            document.getElementById('creditsUI').textContent = credits;
            document.getElementById('upgradePoints').textContent = upgradePoints;
            document.getElementById('finalScore').textContent = score;
        }

        function updateShop() {
            const shopItems = document.getElementById('shopItems');
            shopItems.innerHTML = '';
            const startIndex = shopScrollIndex * itemsPerPage;
            const endIndex = Math.min(startIndex + itemsPerPage, ships.length);
            for (let i = startIndex; i < endIndex; i++) {
                const ship = ships[i];
                const item = document.createElement('div');
                item.className = 'shop-item';
                item.innerHTML = `
                    <h3>${ship.name}</h3>
                    <span>Health: ${ship.health}</span>
                    <span>Speed: ${ship.speed}</span>
                    <span>Fire Rate: ${ship.fireRate}</span>
                    <span>Missiles: ${ship.missileCount}</span>
                    <span>Special: ${ship.special.charAt(0).toUpperCase() + ship.special.slice(1)}</span>
                    <span>Cost: $${ship.cost}</span>
                `;
                const button = document.createElement('button');
                button.className = 'item-button';
                if (ownedShips.includes(ship.id)) {
                    button.textContent = ship.id === selectedShipId ? 'Selected' : 'Select';
                    button.disabled = ship.id === selectedShipId;
                    button.onclick = () => selectShip(ship.id);
                } else {
                    button.textContent = `Buy ($${ship.cost})`;
                    button.disabled = credits < ship.cost;
                    button.onclick = () => buyShip(ship.id);
                }
                item.appendChild(button);
                shopItems.appendChild(item);
            }
        }

        function scrollShop(direction) {
            shopScrollIndex = Math.max(0, Math.min(Math.floor((ships.length - 1) / itemsPerPage), shopScrollIndex + direction));
            updateShop();
        }

        function buyShip(id) {
            const ship = ships.find(s => s.id === id);
            if (credits >= ship.cost) {
                credits -= ship.cost;
                ownedShips.push(id);
                selectedShipId = id;
                updateShop();
                updateUI();
            }
        }

        function selectShip(id) {
            selectedShipId = id;
            updateShop();
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
                player.maxHealth += 20;
                player.health = Math.min(player.maxHealth, player.health + 20);
                upgradePoints -= 10;
                updateUI();
            }
        }

        function upgradeMissiles() {
            if (upgradePoints >= 15) {
                player.missileCount += 3;
                player.missiles += 3;
                upgradePoints -= 15;
                updateUI();
            }
        }

        function startGame() {
            gameState = 'game';
            document.getElementById('mainMenu').style.display = 'none';
            canvas.style.display = 'block';
            toggleGameControls(true);
            score = 0;
            level = 1;
            upgradePoints = 0;
            gameOver = false;
            gamePaused = false;
            player = initPlayer(selectedShipId);
            enemies = [];
            lasers = [];
            missiles = [];
            explosions = [];
            stars = [];
            nebulae = [];
            for (let i = 0; i < 200; i++) spawnStar();
            for (let i = 0; i < 5; i++) spawnNebula();
            spawnEnemiesForLevel();
            updateUI();
        }

        function openShop() {
            gameState = 'shop';
            document.getElementById('mainMenu').style.display = 'none';
            document.getElementById('shopMenu').style.display = 'block';
            shopScrollIndex = 0;
            updateShop();
            toggleGameControls(false);
        }

        function backToMenu() {
            gameState = 'menu';
            document.getElementById('shopMenu').style.display = 'none';
            document.getElementById('gameOverMenu').style.display = 'none';
            document.getElementById('mainMenu').style.display = 'block';
            canvas.style.display = 'none';
            toggleGameControls(false);
        }

        function startNextLevel() {
            gamePaused = false;
            document.getElementById('upgradeMenu').style.display = 'none';
            level++;
            enemies = [];
            lasers = [];
            missiles = [];
            explosions = [];
            player.missiles = player.missileCount;
            player.health = player.maxHealth;
            spawnEnemiesForLevel();
            updateUI();
        }

        function restartGame() {
            startGame();
            document.getElementById('gameOverMenu').style.display = 'none';
        }

        function spawnEnemiesForLevel() {
            const count = level * 3;
            for (let i = 0; i < count; i++) {
                const type = Math.random() < 0.3 ? 'bomber' : Math.random() < 0.5 ? 'cruiser' : 'drone';
                spawnEnemy(type);
            }
        }

        function checkCollisions() {
            lasers.forEach((laser, lIndex) => {
                if (laser.isEnemy) {
                    if (Math.hypot(laser.x - (player.x + player.width / 2), laser.y - (player.y + player.height / 2)) < player.width / 2 &&
                        player.special !== 'shield' && player.specialTimer <= 0) {
                        player.health -= laser.damage;
                        lasers.splice(lIndex, 1);
                        spawnExplosion(laser.x, laser.y, 20);
                        if (player.health <= 0) {
                            spawnExplosion(player.x + player.width / 2, player.y + player.height / 2, 50);
                            gameOver = true;
                        }
                        updateUI();
                    }
                } else {
                    enemies.forEach((enemy, eIndex) => {
                        if (Math.hypot(laser.x - (enemy.x + enemy.width / 2), laser.y - (enemy.y + enemy.height / 2)) < enemy.width / 2) {
                            enemy.health -= laser.damage;
                            lasers.splice(lIndex, 1);
                            spawnExplosion(laser.x, laser.y, 20);
                            if (enemy.health <= 0) {
                                enemies.splice(eIndex, 1);
                                score += enemy.scoreValue;
                                credits += enemy.creditValue;
                                spawnExplosion(enemy.x + enemy.width / 2, enemy.y + enemy.height / 2, 40);
                            }
                            updateUI();
                        }
                    });
                }
            });

            missiles.forEach((missile, mIndex) => {
                if (missile.isEnemy) {
                    if (missile.x < player.x + player.width &&
                        missile.x + missile.width > player.x &&
                        missile.y < player.y + player.height &&
                        missile.y + missile.height > player.y &&
                        player.special !== 'shield' && player.specialTimer <= 0) {
                        player.health -= missile.damage;
                        missiles.splice(mIndex, 1);
                        spawnExplosion(missile.x, missile.y, 40);
                        if (player.health <= 0) {
                            spawnExplosion(player.x + player.width / 2, player.y + player.height / 2, 50);
                            gameOver = true;
                        }
                        updateUI();
                    }
                } else {
                    enemies.forEach((enemy, eIndex) => {
                        if (missile.x < enemy.x + enemy.width &&
                            missile.x + missile.width > enemy.x &&
                            missile.y < enemy.y + enemy.height &&
                            missile.y + missile.height > enemy.y) {
                            enemy.health -= missile.damage;
                            missiles.splice(mIndex, 1);
                            spawnExplosion(missile.x, missile.y, 40);
                            if (enemy.health <= 0) {
                                enemies.splice(eIndex, 1);
                                score += enemy.scoreValue;
                                credits += enemy.creditValue;
                                spawnExplosion(enemy.x + enemy.width / 2, enemy.y + enemy.height / 2, 40);
                            }
                            updateUI();
                        }
                    });
                }
            });

            enemies.forEach((enemy, eIndex) => {
                if (player.x < enemy.x + enemy.width &&
                    player.x + player.width > enemy.x &&
                    player.y < enemy.y + enemy.height &&
                    player.y + player.height > enemy.y &&
                    player.special !== 'shield' && player.specialTimer <= 0) {
                    player.health -= 10;
                    enemies.splice(eIndex, 1);
                    spawnExplosion(enemy.x + enemy.width / 2, enemy.y + enemy.height / 2, 40);
                    updateUI();
                    if (player.health <= 0) {
                        spawnExplosion(player.x + player.width / 2, player.y + player.height / 2, 50);
                        gameOver = true;
                    }
                }
            });
        }

        function update() {
            if (gameState !== 'game' || gameOver || gamePaused) return;

            // Player physics
            if (isMobile) {
                player.vx += joystick.x * player.thrust;
                player.vy += joystick.y * player.thrust;
                const nearestEnemy = findNearestEnemy();
                if (nearestEnemy) {
                    player.angle = Math.atan2(
                        (nearestEnemy.y + nearestEnemy.height / 2) - (player.y + player.height / 2),
                        (nearestEnemy.x + nearestEnemy.width / 2) - (player.x + player.width / 2)
                    );
                }
            } else {
                let ax = 0, ay = 0;
                if (keys['KeyW']) ay -= player.thrust;
                if (keys['KeyS']) ay += player.thrust;
                if (keys['KeyA']) ax -= player.thrust;
                if (keys['KeyD']) ax += player.thrust;
                player.vx += ax;
                player.vy += ay;
                player.angle = Math.atan2(mouse.y - (player.y + player.height / 2), mouse.x - (player.x + player.width / 2));
            }
            player.vx *= player.drag;
            player.vy *= player.drag;
            player.x += player.vx;
            player.y += player.vy;
            player.x = Math.max(0, Math.min(player.x, canvas.width - player.width));
            player.y = Math.max(0, Math.min(player.y, canvas.height - player.height));

            // Special ability timer
            if (player.specialTimer > 0) {
                player.specialTimer--;
                if (player.specialTimer === 0 && player.special === 'boost') {
                    player.speed /= 2;
                }
            }
            if (player.specialCooldown > 0) player.specialCooldown--;

            // Update lasers
            lasers.forEach((laser, index) => {
                laser.x += laser.vx;
                laser.y += laser.vy;
                laser.lifetime--;
                if (laser.lifetime <= 0 || laser.x < 0 || laser.x > canvas.width || laser.y < 0 || laser.y > canvas.height) {
                    lasers.splice(index, 1);
                }
            });

            // Update missiles
            missiles.forEach((missile, index) => {
                if (missile.target && !missile.target.removed) {
                    let dx = missile.target.x + missile.target.width / 2 - missile.x;
                    let dy = missile.target.y + missile.target.height / 2 - missile.y;
                    let angle = Math.atan2(dy, dx);
                    missile.vx = Math.cos(angle) * missile.speed;
                    missile.vy = Math.sin(angle) * missile.speed;
                }
                missile.x += missile.vx;
                missile.y += missile.vy;
                // Add fire trail particle
                missile.trail.push({
                    x: missile.x,
                    y: missile.y,
                    lifetime: 10,
                    alpha: 1
                });
                missile.trail = missile.trail.filter(p => p.lifetime > 0);
                missile.trail.forEach(p => p.lifetime--);
                if (missile.x < 0 || missile.x > canvas.width || missile.y < 0 || missile.y > canvas.height) {
                    missiles.splice(index, 1);
                }
            });

            // Update enemies
            enemies.forEach((enemy, index) => {
                enemy.y += enemy.vy;
                let dx = player.x - enemy.x;
                let dy = player.y - enemy.y;
                enemy.angle = Math.atan2(dy, dx);
                if (Math.random() < 0.02) {
                    if (enemy.type === 'bomber' && enemy.missiles > 0) {
                        shootMissile(enemy, true);
                    } else {
                        shootLaser(enemy, true);
                    }
                }
                enemy.fireCooldown--;
                if (enemy.y > canvas.height) {
                    enemies.splice(index, 1);
                    player.health -= 2;
                    updateUI();
                    if (player.health <= 0) {
                        spawnExplosion(player.x + player.width / 2, player.y + player.height / 2, 50);
                        gameOver = true;
                    }
                }
            });

            // Update stars
            stars.forEach(star => {
                star.y += star.speed;
                if (star.y > canvas.height) star.y -= canvas.height;
            });

            // Update explosions
            explosions.forEach((explosion, eIndex) => {
                explosion.lifetime--;
                explosion.particles.forEach(p => {
                    p.x += p.vx;
                    p.y += p.vy;
                    p.alpha -= 0.03;
                });
                if (explosion.lifetime <= 0) {
                    explosions.splice(eIndex, 1);
                }
            });

            // Spawn new enemies
            if (Math.random() < 0.01 * level) {
                const type = Math.random() < 0.3 ? 'bomber' : Math.random() < 0.5 ? 'cruiser' : 'drone';
                spawnEnemy(type);
            }

            // Check for level completion
            if (enemies.length === 0) {
                gamePaused = true;
                document.getElementById('upgradeMenu').style.display = 'block';
            }

            checkCollisions();
        }

        function drawShip(x, y, width, height, bodyColor, cockpitColor, angle, isPlayer) {
            ctx.save();
            ctx.translate(x + width / 2, y + height / 2);
            ctx.rotate(angle);
            const gradient = ctx.createLinearGradient(-width / 2, -height / 2, width / 2, height / 2);
            gradient.addColorStop(0, bodyColor);
            gradient.addColorStop(1, darkenColor(bodyColor, 0.7));
            ctx.fillStyle = gradient;
            ctx.beginPath();
            ctx.moveTo(0, -height / 2);
            ctx.lineTo(-width / 4, height / 4);
            ctx.lineTo(-width / 6, height / 2);
            ctx.lineTo(width / 6, height / 2);
            ctx.lineTo(width / 4, height / 4);
            ctx.closePath();
            ctx.fill();
            ctx.fillStyle = gradient;
            ctx.beginPath();
            ctx.moveTo(-width / 2, 0);
            ctx.lineTo(-width / 4, -height / 4);
            ctx.lineTo(-width / 4, height / 4);
            ctx.closePath();
            ctx.fill();
            ctx.beginPath();
            ctx.moveTo(width / 2, 0);
            ctx.lineTo(width / 4, -height / 4);
            ctx.lineTo(width / 4, height / 4);
            ctx.closePath();
            ctx.fill();
            if (isPlayer && (Math.abs(player.vx) > 0.1 || Math.abs(player.vy) > 0.1)) {
                ctx.fillStyle = `rgba(255, 165, 0, ${Math.random() * 0.5 + 0.5})`;
                ctx.beginPath();
                ctx.moveTo(-width / 6, height / 2 + 5);
                ctx.lineTo(-width / 8, height / 2 + 15);
                ctx.lineTo(width / 8, height / 2 + 15);
                ctx.lineTo(width / 6, height / 2 + 5);
                ctx.closePath();
                ctx.fill();
            }
            ctx.fillStyle = cockpitColor;
            ctx.beginPath();
            ctx.ellipse(0, -height / 4, width / 8, height / 8, 0, 0, Math.PI * 2);
            ctx.fill();
            ctx.restore();
        }

        function drawExplosion(explosion) {
            ctx.save();
            ctx.translate(explosion.x, explosion.y);
            explosion.particles.forEach(p => {
                const gradient = ctx.createRadialGradient(p.x, p.y, 0, p.x, p.y, p.radius);
                gradient.addColorStop(0, `rgba(255, ${Math.random() * 100 + 155}, 0, ${p.alpha})`);
                gradient.addColorStop(1, `rgba(255, ${Math.random() * 100 + 155}, 0, 0)`);
                ctx.fillStyle = gradient;
                ctx.beginPath();
                ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
                ctx.fill();
            });
            ctx.restore();
        }

        function drawBackground() {
            ctx.fillStyle = '#0a0a1a';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            nebulae.forEach(nebula => {
                const gradient = ctx.createRadialGradient(nebula.x, nebula.y, 0, nebula.x, nebula.y, nebula.radius);
                gradient.addColorStop(0, nebula.color);
                gradient.addColorStop(1, 'rgba(0, 0, 0, 0)');
                ctx.fillStyle = gradient;
                ctx.beginPath();
                ctx.arc(nebula.x, nebula.y, nebula.radius, 0, Math.PI * 2);
                ctx.fill();
            });
            ctx.fillStyle = '#fff';
            stars.forEach(star => {
                ctx.beginPath();
                ctx.arc(star.x, star.y, star.radius, 0, Math.PI * 2);
                ctx.fill();
            });
        }

        function darkenColor(hex, factor) {
            let r = parseInt(hex.slice(1, 3), 16) * factor;
            let g = parseInt(hex.slice(3, 5), 16) * factor;
            let b = parseInt(hex.slice(5, 7), 16) * factor;
            return `rgb(${Math.floor(r)}, ${Math.floor(g)}, ${Math.floor(b)})`;
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            if (gameState !== 'game') return;

            drawBackground();

            if (player) {
                if (player.special === 'shield' && player.specialTimer > 0) {
                    ctx.strokeStyle = `rgba(0, 255, 255, ${Math.random() * 0.5 + 0.5})`;
                    ctx.lineWidth = 2;
                    ctx.beginPath();
                    ctx.arc(player.x + player.width / 2, player.y + player.height / 2, player.width, 0, Math.PI * 2);
                    ctx.stroke();
                }
                drawShip(player.x, player.y, player.width, player.height, player.color, player.cockpitColor, player.angle, true);
            }

            enemies.forEach(enemy => {
                drawShip(enemy.x, enemy.y, enemy.width, enemy.height, enemy.color, enemy.cockpitColor, enemy.angle, false);
            });

            // Draw lasers
            lasers.forEach(laser => {
                ctx.strokeStyle = laser.isEnemy ? 'rgba(255, 0, 0, 0.8)' : 'rgba(0, 255, 0, 0.8)';
                ctx.lineWidth = 3;
                ctx.beginPath();
                ctx.moveTo(laser.x, laser.y);
                ctx.lineTo(laser.x - laser.vx * laser.length / 15, laser.y - laser.vy * laser.length / 15);
                ctx.stroke();
            });

            // Draw missiles
            missiles.forEach(missile => {
                // Draw trail
                missile.trail.forEach(p => {
                    ctx.fillStyle = `rgba(255, 165, 0, ${p.alpha * 0.5})`;
                    ctx.beginPath();
                    ctx.arc(p.x, p.y, 2, 0, Math.PI * 2);
                    ctx.fill();
                });
                // Draw missile
                ctx.save();
                ctx.translate(missile.x, missile.y);
                const angle = Math.atan2(missile.vy, missile.vx);
                ctx.rotate(angle);
                const gradient = ctx.createLinearGradient(-missile.width / 2, -missile.height / 2, missile.width / 2, missile.height / 2);
                gradient.addColorStop(0, missile.isEnemy ? '#FF4500' : '#FFA500');
                gradient.addColorStop(1, '#888888');
                ctx.fillStyle = gradient;
                ctx.beginPath();
                ctx.moveTo(0, -missile.height / 2);
                ctx.lineTo(-missile.width / 2, missile.height / 2);
                ctx.lineTo(missile.width / 2, missile.height / 2);
                ctx.closePath();
                ctx.fill();
                ctx.restore();
            });

            explosions.forEach(explosion => drawExplosion(explosion));

            if (gameOver) {
                document.getElementById('gameOverMenu').style.display = 'block';
            }
        }

        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }

        document.getElementById('mainMenu').style.display = 'block';
        toggleGameControls(false);
        gameLoop();
    </script>
</body>
</html>