<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Fighter Jet 3D Game</title>
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
            display: block;
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
        #upgradeMenu, #gameOverMenu {
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
    <div id="gameOverMenu">
        <h2>Game Over</h2>
        <p>Final Score: <span id="finalScore">0</span></p>
        <button onclick="restartGame()">Restart</button>
    </div>
    <div id="joystick">
        <div id="joystickInner"></div>
    </div>
    <div id="fireButton">Fire</div>
    <div id="missileButton">Missile</div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r134/three.min.js"></script>
    <script>
        // Three.js setup
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer();
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // Resize handler
        window.addEventListener('resize', () => {
            renderer.setSize(window.innerWidth, window.innerHeight);
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
        });

        // Skybox
        const skyboxGeometry = new THREE.BoxGeometry(1000, 1000, 1000);
        const skyboxMaterial = new THREE.MeshBasicMaterial({ color: 0x87CEEB, side: THREE.BackSide });
        const skybox = new THREE.Mesh(skyboxGeometry, skyboxMaterial);
        scene.add(skybox);

        // Terrain
        const terrainGeometry = new THREE.PlaneGeometry(500, 500, 50, 50);
        const terrainMaterial = new THREE.MeshLambertMaterial({ color: 0x228B22 });
        const terrain = new THREE.Mesh(terrainGeometry, terrainMaterial);
        terrain.rotation.x = -Math.PI / 2;
        terrain.position.y = -50;
        scene.add(terrain);
        for (let i = 0; i < terrainGeometry.vertices.length; i++) {
            terrainGeometry.vertices[i].z = Math.sin(i / 50) * 5;
        }
        terrainGeometry.verticesNeedUpdate = true;

        // Lighting
        const ambientLight = new THREE.AmbientLight(0x404040);
        scene.add(ambientLight);
        const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5);
        directionalLight.position.set(1, 1, 1);
        scene.add(directionalLight);

        // Player
        let player = {
            mesh: createJetModel(0x4682B4, 0xFFD700),
            speed: 0.2,
            health: 100,
            fireRate: 10,
            fireCooldown: 0,
            missiles: 5,
            position: new THREE.Vector3(0, 0, -10),
            rotation: new THREE.Euler(0, 0, 0)
        };
        scene.add(player.mesh);

        // Game state
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
        let mouse = { x: 0, y: 0 };
        let isMobile = /Mobi|Android/i.test(navigator.userAgent);

        // Initialize joystick and buttons for mobile
        if (isMobile) {
            document.getElementById('joystick').style.display = 'block';
            document.getElementById('fireButton').style.display = 'block';
            document.getElementById('missileButton').style.display = 'block';
        }

        // Create jet model
        function createJetModel(bodyColor, cockpitColor) {
            const group = new THREE.Group();
            // Body
            const bodyGeometry = new THREE.CylinderGeometry(0.3, 0.5, 2, 16);
            const bodyMaterial = new THREE.MeshLambertMaterial({ color: bodyColor });
            const body = new THREE.Mesh(bodyGeometry, bodyMaterial);
            body.rotation.x = Math.PI / 2;
            group.add(body);
            // Wings
            const wingGeometry = new THREE.BoxGeometry(2, 0.1, 0.5);
            const wingMaterial = new THREE.MeshLambertMaterial({ color: bodyColor });
            const leftWing = new THREE.Mesh(wingGeometry, wingMaterial);
            leftWing.position.set(-1, 0, 0.5);
            group.add(leftWing);
            const rightWing = new THREE.Mesh(wingGeometry, wingMaterial);
            rightWing.position.set(1, 0, 0.5);
            group.add(rightWing);
            // Cockpit
            const cockpitGeometry = new THREE.SphereGeometry(0.2, 16, 16);
            const cockpitMaterial = new THREE.MeshLambertMaterial({ color: cockpitColor });
            const cockpit = new THREE.Mesh(cockpitGeometry, cockpitMaterial);
            cockpit.position.set(0, 0.3, -0.5);
            group.add(cockpit);
            return group;
        }

        // Create bullet
        function createBullet() {
            const geometry = new THREE.SphereGeometry(0.05, 8, 8);
            const material = new THREE.MeshBasicMaterial({ color: 0xFFFF00 });
            const mesh = new THREE.Mesh(geometry, material);
            return { mesh, velocity: new THREE.Vector3(0, 0, -1), source: 'player' };
        }

        // Create missile
        function createMissile() {
            const geometry = new THREE.CylinderGeometry(0.1, 0.1, 0.5, 8);
            const material = new THREE.MeshLambertMaterial({ color: 0xFFA500 });
            const mesh = new THREE.Mesh(geometry, material);
            mesh.rotation.x = Math.PI / 2;
            return { mesh, velocity: new THREE.Vector3(0, 0, -0.5), target: null };
        }

        // Create cloud
        function createCloud() {
            const group = new THREE.Group();
            for (let i = 0; i < 5; i++) {
                const geometry = new THREE.SphereGeometry(2 + Math.random() * 2, 8, 8);
                const material = new THREE.MeshLambertMaterial({ color: 0xFFFFFF, transparent: true, opacity: 0.7 });
                const sphere = new THREE.Mesh(geometry, material);
                sphere.position.set(Math.random() * 4 - 2, Math.random() * 4 - 2, Math.random() * 4 - 2);
                group.add(sphere);
            }
            return { mesh: group, velocity: new THREE.Vector3(0, 0, 0.05) };
        }

        // Controls
        document.addEventListener('keydown', (e) => {
            keys[e.code] = true;
            if (e.code === 'Space' && !gameOver && !gamePaused) shootBullet();
            if (e.code === 'KeyM' && player.missiles > 0 && !gameOver && !gamePaused) shootMissile();
        });
        document.addEventListener('keyup', (e) => keys[e.code] = false);
        document.addEventListener('mousemove', (e) => {
            mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;
        });

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
                const bullet = createBullet();
                bullet.mesh.position.copy(player.mesh.position);
                bullet.mesh.position.z -= 1;
                scene.add(bullet.mesh);
                bullets.push(bullet);
                player.fireCooldown = player.fireRate;
            }
        }

        function shootMissile() {
            if (player.missiles > 0) {
                const missile = createMissile();
                missile.mesh.position.copy(player.mesh.position);
                missile.mesh.position.z -= 1;
                missile.target = findNearestEnemy();
                scene.add(missile.mesh);
                missiles.push(missile);
                player.missiles--;
                updateUI();
            }
        }

        function findNearestEnemy() {
            let nearest = null;
            let minDist = Infinity;
            enemies.forEach(enemy => {
                let dist = enemy.mesh.position.distanceTo(player.mesh.position);
                if (dist < minDist) {
                    minDist = dist;
                    nearest = enemy;
                }
            });
            return nearest;
        }

        function spawnEnemy() {
            const enemy = {
                mesh: createJetModel(0xB22222, 0xFF4500),
                speed: 0.05 + level * 0.01,
                health: 50 + level * 10,
                fireCooldown: 0
            };
            enemy.mesh.position.set(Math.random() * 20 - 10, 0, 50);
            scene.add(enemy.mesh);
            enemies.push(enemy);
        }

        function spawnCloud() {
            const cloud = createCloud();
            cloud.mesh.position.set(Math.random() * 50 - 25, Math.random() * 10 - 5, 50);
            scene.add(cloud.mesh);
            clouds.push(cloud);
        }

        function updateUI() {
            document.getElementById('score').textContent = score;
            document.getElementById('level').textContent = level;
            document.getElementById('health').textContent = player.health;
            document.getElementById('missiles').textContent = player.missiles;
            document.getElementById('upgradePoints').textContent = upgradePoints;
            document.getElementById('finalScore').textContent = score;
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
            enemies.forEach(enemy => scene.remove(enemy.mesh));
            bullets.forEach(bullet => scene.remove(bullet.mesh));
            missiles.forEach(missile => scene.remove(missile.mesh));
            enemies = [];
            bullets = [];
            missiles = [];
            spawnEnemiesForLevel();
            updateUI();
        }

        function restartGame() {
            gameOver = false;
            gamePaused = false;
            document.getElementById('gameOverMenu').style.display = 'none';
            score = 0;
            level = 1;
            upgradePoints = 0;
            player.health = 100;
            player.missiles = 5;
            player.fireRate = 10;
            player.mesh.position.set(0, 0, -10);
            enemies.forEach(enemy => scene.remove(enemy.mesh));
            bullets.forEach(bullet => scene.remove(bullet.mesh));
            missiles.forEach(missile => scene.remove(missile.mesh));
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
                if (bullet.source === 'player') {
                    enemies.forEach((enemy, eIndex) => {
                        if (bullet.mesh.position.distanceTo(enemy.mesh.position) < 1) {
                            enemy.health -= 10;
                            scene.remove(bullet.mesh);
                            bullets.splice(bIndex, 1);
                            if (enemy.health <= 0) {
                                scene.remove(enemy.mesh);
                                enemies.splice(eIndex, 1);
                                score += 10;
                                upgradePoints += 5;
                                updateUI();
                            }
                        }
                    });
                } else {
                    if (bullet.mesh.position.distanceTo(player.mesh.position) < 1) {
                        player.health -= 5;
                        scene.remove(bullet.mesh);
                        bullets.splice(bIndex, 1);
                        updateUI();
                        if (player.health <= 0) gameOver = true;
                    }
                }
            });

            missiles.forEach((missile, mIndex) => {
                enemies.forEach((enemy, eIndex) => {
                    if (missile.mesh.position.distanceTo(enemy.mesh.position) < 1) {
                        scene.remove(enemy.mesh);
                        scene.remove(missile.mesh);
                        enemies.splice(eIndex, 1);
                        missiles.splice(mIndex, 1);
                        score += 20;
                        upgradePoints += 10;
                        updateUI();
                    }
                });
            });

            enemies.forEach((enemy, eIndex) => {
                if (player.mesh.position.distanceTo(enemy.mesh.position) < 2) {
                    player.health -= 10;
                    scene.remove(enemy.mesh);
                    enemies.splice(eIndex, 1);
                    updateUI();
                    if (player.health <= 0) gameOver = true;
                }
            });
        }

        function update() {
            if (gameOver || gamePaused) {
                if (gameOver) document.getElementById('gameOverMenu').style.display = 'block';
                return;
            }

            // Player movement
            if (isMobile) {
                player.mesh.position.x += joystick.x * player.speed;
                player.mesh.position.y += joystick.y * player.speed;
            } else {
                if (keys['KeyW']) player.mesh.position.y += player.speed;
                if (keys['KeyS']) player.mesh.position.y -= player.speed;
                if (keys['KeyA']) player.mesh.position.x -= player.speed;
                if (keys['KeyD']) player.mesh.position.x += player.speed;
                player.mesh.rotation.y = -mouse.x * 0.5;
                player.mesh.rotation.x = -mouse.y * 0.5;
            }
            player.mesh.position.x = Math.max(-20, Math.min(20, player.mesh.position.x));
            player.mesh.position.y = Math.max(-10, Math.min(10, player.mesh.position.y));

            // Update bullets
            bullets.forEach((bullet, index) => {
                bullet.mesh.position.add(bullet.velocity);
                if (bullet.mesh.position.z < -50 || bullet.mesh.position.z > 50) {
                    scene.remove(bullet.mesh);
                    bullets.splice(index, 1);
                }
            });

            // Update missiles
            missiles.forEach((missile, index) => {
                if (missile.target && missile.target.mesh) {
                    const direction = missile.target.mesh.position.clone().sub(missile.mesh.position).normalize();
                    missile.velocity.lerp(direction.multiplyScalar(0.5), 0.1);
                    missile.mesh.lookAt(missile.target.mesh.position);
                }
                missile.mesh.position.add(missile.velocity);
                if (missile.mesh.position.z < -50) {
                    scene.remove(missile.mesh);
                    missiles.splice(index, 1);
                }
            });

            // Update enemies
            enemies.forEach((enemy, index) => {
                enemy.mesh.position.z -= enemy.speed;
                if (enemy.mesh.position.z < -50) {
                    scene.remove(enemy.mesh);
                    enemies.splice(index, 1);
                    player.health -= 5;
                    updateUI();
                    if (player.health <= 0) gameOver = true;
                }
                // Enemy shooting
                if (enemy.fireCooldown <= 0) {
                    const bullet = createBullet();
                    bullet.mesh.position.copy(enemy.mesh.position);
                    bullet.mesh.position.z -= 1;
                    bullet.velocity.set(0, 0, 0.5);
                    bullet.source = 'enemy';
                    scene.add(bullet.mesh);
                    bullets.push(bullet);
                    enemy.fireCooldown = 30;
                }
                enemy.fireCooldown--;
            });

            // Update clouds
            clouds.forEach((cloud, index) => {
                cloud.mesh.position.add(cloud.velocity);
                if (cloud.mesh.position.z > 50) {
                    scene.remove(cloud.mesh);
                    clouds.splice(index, 1);
                }
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

            // Camera follow
            camera.position.set(player.mesh.position.x, player.mesh.position.y + 5, player.mesh.position.z + 10);
            camera.lookAt(player.mesh.position);

            checkCollisions();
        }

        function animate() {
            requestAnimationFrame(animate);
            update();
            renderer.render(scene, camera);
        }

        // Start the game
        for (let i = 0; i < 5; i++) spawnCloud();
        spawnEnemiesForLevel();
        animate();
    </script>
</body>
</html>