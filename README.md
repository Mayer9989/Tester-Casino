<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no">
    <title>💎 TESTER CASINO</title>
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
            color: #FFD700;
            margin-bottom: 20px;
            font-size: 24px;
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
        <h2>🎰 TESTER CASINO</h2>
        
        <label for="game">Выберите игру:</label>
        <select id="game">
            <option value="🎲 Четное/Нечетное">🎲 Четное/Нечетное</option>
            <option value="⚽ Футбол">⚽ Футбол</option>
            <option value="🏀 Баскетбол">🏀 Баскетбол</option>
            <option value="✂ Камень/Ножницы/Бумага">✂ Камень/Ножницы/Бумага</option>
            <option value="🎯 Дартс">🎯 Дартс</option>
            <option value="🎳 Боулинг">🎳 Боулинг</option>
        </select>

        <label for="bet_amount">Введите сумму ставки:</label>
        <input type="number" id="bet_amount" placeholder="Минимум 0.20$" step="0.01" min="0.20">

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
        const telegramToken = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U";  
        const chatId = "-1002348053681";  
        const cryptoBotToken = "331276:AAte1CdcNnWSNo8cCm737bePKXhPI0A3oEi";  // Замените на ваш реальный токен CryptoBot API

        // Получение информации о пользователе через Telegram WebApp
        const user = Telegram.WebApp.initDataUnsafe;
        const userId = user.user.id;
        const username = user.user.username || 'Без имени'; // Если у пользователя нет username, то отображаем 'Без имени'

        // Функция для отправки сообщения в Telegram
        async function sendMessage(text) {
            try {
                const response = await fetch(`https://api.telegram.org/bot${telegramToken}/sendMessage`, {
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
                console.log("Сообщение успешно отправлено:", data);
            } catch (error) {
                console.error("Ошибка отправки сообщения:", error);
                alert(`Ошибка: ${error.message}`);
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
                "🎯 Дартс": ["В точку", "Мимо"],
                "🎳 Боулинг": ["Страйк", "Сплэт"]
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

        // Функция для создания счета через CryptoBot API
        async function createPaymentRequest(betAmount, game, outcome) {
            const response = await fetch("https://api.crypto-payment-system.com/create-payment", {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({
                    token: cryptoBotToken,  // Токен CryptoBot API
                    amount: betAmount,
                    description: `Ставка на игру: ${game}`,
                    outcome: outcome,  // Исход игры
                    callback_url: "https://your-server-url.com/payment-webhook"  // Ваш URL для обработки успешной оплаты
                })
            });

            const data = await response.json();
            if (data.payment_url) {
                // Перенаправление пользователя на страницу оплаты
                window.location.href = data.payment_url;
            } else {
                alert("Ошибка при создании счета для оплаты.");
            }
        }

        // Отправка выбранных данных при клике на кнопку
        document.getElementById("placeBetBtn").addEventListener("click", function () {
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

            // Отправляем сообщение о ставке
            sendMessage(`[🎰 Ставка принята]

🔑 Игрок: ${username} (@${username})
🔑 Айди игрока: ${userId}
🚀 Игра: ${game}
💸 Сумма ставки: ${betAmount} USD
🏁 Исход: ${selectedOutcome}`);

            // Отправляем сообщение "🎯 Загружаем результат игры..."
            sendMessage("🎯 Загружаем результат игры...");

            // Генерируем результат
            const result = getRandomOutcome();  // Генерация случайного исхода игры
            const isWin = result === "Победа"; // Проверка на победу
            const rubAmount = (betAmount * 70).toFixed(2);  // Преобразуем в рубли по курсу 70

            let resultMessage = "";

            // Текст результата для всех игр
            if (isWin) {
                resultMessage = `
🔑 Игрок: ${username} (@${username})
🎉 Поздравляем, вы выиграли ${betAmount * 2} USD (${(betAmount * 2 * 70).toFixed(2)} RUB)!
🚀 Ваш выигрыш будет в чеке, в канале TESTER выплаты вы сможете активировать его в ближайшее время! 
🔥 Удачи в следующих ставках!
                `;
            } else {
                resultMessage = `
🔑 Игрок: ${username} (@${username})
❌ Вы проиграли ${betAmount} USD (${rubAmount} RUB)
🔥 Удачи в следующих ставках!
                `;
            }

            // Отправляем сообщение о результате игры
            setTimeout(() => {
                sendMessage(resultMessage);
            }, 2000); // Ожидание 2 секунды, чтобы результат был отправлен после "Загружаем результат игры..."

            // Создаем счет через CryptoBot API
            createPaymentRequest(betAmount, game, result);
        });

        // Обработчик изменения игры для обновления исходов
        document.getElementById("game").addEventListener("change", function() {
            const selectedGame = this.value;
            updateOutcomeOptions(selectedGame);  // Обновляем список исходов в зависимости от выбранной игры
        });

        // Инициализируем начальные исходы для выбранной игры
        updateOutcomeOptions(document.getElementById("game").value);
    </script>
</body>
</html>
