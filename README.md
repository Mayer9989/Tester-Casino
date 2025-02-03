<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no">
    <title>TESTER CASINO</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        body, html {
            height: 100%;
            margin: 0;
            font-family: 'Arial', sans-serif;
            background: #141414;
            display: flex;
            justify-content: center;
            align-items: center;
            color: white;
        }
        .container {
            background: rgba(0, 0, 0, 0.9);
            border-radius: 15px;
            width: 350px;
            padding: 30px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.6);
        }
        h2 {
            text-align: center;
            font-size: 30px; /* Размер для обоих текстов */
            font-weight: bold;
            letter-spacing: 2px;
            margin-bottom: 10px;
        }
        h2 span {
            color: red; /* Цвет для CASINO */
        }
        select, input, button {
            width: 100%;
            padding: 15px;
            margin: 10px 0;
            font-size: 18px;
            border-radius: 10px;
            border: 2px solid #444;
            background: #222;
            color: white;
        }
        select {
            background: #333;
        }
        button {
            background: #28a745;
            border: none;
            color: white;
            cursor: pointer;
            font-size: 20px;
        }
        button:hover {
            background: #218838;
        }
        button:active {
            background: #1e7e34;
        }
        .footer {
            margin-top: 20px;
            font-size: 14px;
            text-align: center;
            color: #bbb;
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>TESTER <span>CASINO</span></h2>
        
        <label for="game">Выберите игру:</label>
        <select id="game">
            <option value="🎲 Четное/Нечетное">🎲 Четное/Нечетное</option>
            <option value="⚽ Футбол">⚽ Футбол</option>
            <option value="🏀 Баскетбол">🏀 Баскетбол</option>
            <option value="✂ Камень/Ножницы/Бумага">✂ Камень/Ножницы/Бумага</option>
            <option value="🎯 Дартс">🎯 Дартс</option>
        </select>

        <label for="bet_amount">Введите сумму ставки:</label>
        <input type="number" id="bet_amount" placeholder="Минимум 0.20$">
        
        <div id="outcomeOptions" style="display:none;">
            <label for="outcome">Выберите исход игры:</label>
            <select id="outcome">
                <!-- Исходы будут добавляться динамически -->
            </select>
        </div>

        <button id="placeBetBtn">✅ Сделать ставку</button>

        <div class="footer">Ваше казино в Telegram. Удачи!</div>
    </div>

    <script>
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U";  
        const chatId = "-1002348053681";  
        const cryptoToken = "331276:AAte1CdcNnWSNo8cCm737bePKXhPI0A3oEi";  // Ваш токен для CryptoBot API
        const webhookUrl = "https://hook.eu2.make.com/dyh9wamknd77wn8txtv3qgu3mdglp3sl";  // URL Webhook

        const username = Telegram.WebApp.initDataUnsafe?.user?.username || "Игрок";
        const userId = Telegram.WebApp.initDataUnsafe?.user?.id || "Неизвестный ID";  

        // Функция для отправки сообщения в Telegram
        async function sendMessage(text) {
            try {
                const response = await fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({
                        chat_id: chatId,
                        text: text,
                        parse_mode: "HTML"
                    })
                });

                const data = await response.json();
                if (!response.ok) throw new Error(data.description || 'Неизвестная ошибка');
                return data.result.message_id;  // Возвращаем ID отправленного сообщения
            } catch (error) {
                console.error("Ошибка отправки сообщения:", error);
                alert(`Ошибка: ${error.message}`);
            }
        }

        // Функция для создания счета и получения ссылки на оплату
        async function createPayment(amount) {
            console.log("Запрос на создание счета с суммой:", amount);
            
            try {
                const response = await fetch('https://api.cryptobot.io/create_payment', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        token: cryptoToken,
                        amount: amount,
                        currency: 'USDT',
                        user_id: userId,
                        callback_url: webhookUrl
                    })
                });

                if (!response.ok) {
                    throw new Error('Ошибка при создании счета: ' + response.statusText);
                }

                const data = await response.json();
                console.log("Ответ от API CryptoBot:", data);

                if (data.payment_url) {
                    console.log("Создана ссылка для оплаты:", data.payment_url);
                    return data.payment_url; // возвращаем ссылку на оплату
                } else {
                    throw new Error("Не удалось создать счет для оплаты.");
                }
            } catch (error) {
                console.error("Ошибка при запросе создания счета:", error);
                alert("Ошибка: " + error.message);
                throw error;  // Пробрасываем ошибку дальше
            }
        }

        // Функция для удаления сообщения
        async function deleteMessage(messageId) {
            try {
                const response = await fetch(`https://api.telegram.org/bot${token}/deleteMessage`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({
                        chat_id: chatId,
                        message_id: messageId
                    })
                });

                const data = await response.json();
                if (!response.ok) throw new Error(data.description || 'Неизвестная ошибка');
            } catch (error) {
                console.error("Ошибка удаления сообщения:", error);
            }
        }

        // Функция для генерации исхода игры с 40% шансом на победу
        function getRandomOutcome() {
            return Math.random() < 0.4 ? "Победа" : "Проигрыш";  
        }

        // Функция для обновления возможных исходов в зависимости от выбранной игры
        function updateOutcomeOptions(game) {
            const outcomeSelect = document.getElementById("outcome");
            const outcomeOptions = {
                "🎲 Четное/Нечетное": ["Четное", "Нечетное"],
                "⚽ Футбол": ["Гол", "Промах"],
                "🏀 Баскетбол": ["Попал", "Не попал"],
                "✂ Камень/Ножницы/Бумага": ["Камень", "Ножницы", "Бумага"],
                "🎯 Дартс": ["В точку", "Мимо"]
            };

            outcomeSelect.innerHTML = '';  // Очистить предыдущее содержимое

            outcomeOptions[game].forEach(option => {
                const opt = document.createElement("option");
                opt.value = option;
                opt.textContent = option;
                outcomeSelect.appendChild(opt);
            });

            document.getElementById("outcomeOptions").style.display = "block";
        }

        // Отправка выбранных данных при клике на кнопку
        document.getElementById("placeBetBtn").addEventListener("click", async function () {
            const game = document.getElementById("game").value;
            const betAmount = parseFloat(document.getElementById("bet_amount").value);
            const selectedOutcome = document.getElementById("outcome").value;

            if (isNaN(betAmount) || betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20$. Введите корректное значение.");
                return;
            }

            if (!selectedOutcome) {
                alert("❌ Пожалуйста, выберите исход игры.");
                return;
            }

            // Создаем счет для оплаты
            try {
                const paymentUrl = await createPayment(betAmount);
                window.location.href = paymentUrl; // Переход на страницу оплаты
            } catch (error) {
                console.error("Ошибка при создании счета:", error);
                alert("Ошибка при создании счета. Попробуйте снова.");
            }
        });

        // Обновление исходов игры, когда выбирается новая игра
        document.getElementById("game").addEventListener("change", function () {
            const game = this.value;
            updateOutcomeOptions(game);
        });

        // Инициализация при загрузке
        const initialGame = document.getElementById("game").value;
        updateOutcomeOptions(initialGame);
    </script>
</body>
</html>
