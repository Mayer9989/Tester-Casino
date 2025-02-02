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
        <input type="number" id="bet_amount" placeholder="Минимум 0.20 USDT" step="0.01" min="0.20">

        <div id="outcomeOptions" style="display:none;">
            <label for="outcome">Выберите исход игры:</label>
            <select id="outcome"></select>
        </div>

        <button id="placeBetBtn">✅ Сделать ставку</button>

        <div class="footer">Ваше казино в Telegram. Удачи!</div>
    </div>

    <script>
        const botToken = "331276:AAte1CdcNnWSNo8cCm737bePKXhPI0A3oEi";  // Токен CryptoBot
        const cryptoPayApiUrl = "https://pay.crypt.bot/api/";
        const telegramBotToken = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U";  // Токен Telegram-бота
        const telegramChatId = "-1002348053681";  // ID канала или группы Telegram

        // Функция создания платежа через CryptoBot
        async function createPayment(amount) {
            try {
                const response = await fetch(`${cryptoPayApiUrl}createInvoice`, {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                        "Authorization": `Bearer ${botToken}`
                    },
                    body: JSON.stringify({
                        amount: parseFloat(amount).toFixed(2),
                        currency: "USDT",
                        description: `Оплата ставки ${amount} USDT`
                    })
                });

                const data = await response.json();
                if (data.ok) {
                    return data.result.pay_url;
                } else {
                    throw new Error(data.error.message || "Ошибка создания платежа");
                }
            } catch (error) {
                alert("Ошибка создания платежа: " + error.message);
                throw error;
            }
        }

        // Функция отправки сообщений в Telegram
        async function sendMessage(text) {
            try {
                const response = await fetch(`https://api.telegram.org/bot${telegramBotToken}/sendMessage`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({
                        chat_id: telegramChatId,
                        text: text,
                        parse_mode: "HTML"
                    })
                });

                if (!response.ok) {
                    const data = await response.json();
                    throw new Error(data.description || "Ошибка отправки сообщения");
                }
            } catch (error) {
                alert(`Ошибка отправки сообщения: ${error.message}`);
            }
        }

        // Функция для генерации случайного исхода игры с 40% шансом на победу
        function getRandomOutcome() {
            return Math.random() < 0.4 ? "Победа" : "Проигрыш";
        }

        // Функция обновления исходов для выбранной игры
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

            outcomeSelect.innerHTML = '';
            outcomeOptions[game].forEach(option => {
                const opt = document.createElement("option");
                opt.value = option;
                opt.textContent = option;
                outcomeSelect.appendChild(opt);
            });

            document.getElementById("outcomeOptions").style.display = "block";
        }

        // Обработчик события для кнопки "Сделать ставку"
        document.getElementById("placeBetBtn").addEventListener("click", async function () {
            const game = document.getElementById("game").value;
            const betAmount = parseFloat(document.getElementById("bet_amount").value);
            const selectedOutcome = document.getElementById("outcome").value;

            if (isNaN(betAmount) || betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20 USDT");
                return;
            }

            if (!selectedOutcome) {
                alert("❌ Пожалуйста, выберите исход игры.");
                return;
            }

            try {
                const paymentUrl = await createPayment(betAmount);
                alert("Перенаправляем вас на оплату...");
                window.location.href = paymentUrl;

                const username = "Игрок_1";
                const userId = "123456";

                sendMessage(`[🎰 Ставка принята]

🔑 Игрок: ${username}
🔑 Айди игрока: ${userId}
🚀 Игра: ${game}
💸 Сумма ставки: ${betAmount} USDT
🏁 Исход: ${selectedOutcome}`);

                sendMessage("🎯 Загружаем результат игры...");

                const result = getRandomOutcome();
                const rubAmount = (betAmount * 70).toFixed(2);  

                const resultMessage = result === "Победа"
                    ? `
🔑 Игрок: ${username}
🎉 Поздравляем, вы выиграли ${betAmount * 2} USDT (${(betAmount * 2 * 70).toFixed(2)} RUB)!
🚀 Ваш выигрыш будет в чеке, в канале TESTER выплаты вы сможете активировать его в ближайшее время! 
🔥 Удачи в следующих ставках!
`
                    : `
🔑 Игрок: ${username}
❌ Вы проиграли ${betAmount} USDT (${rubAmount} RUB)
🔥 Удачи в следующих ставках!
`;

                setTimeout(() => {
                    sendMessage(resultMessage);
                }, 2000);
            } catch (error) {
                console.error("Ошибка оплаты:", error);
            }
        });

        document.getElementById("game").addEventListener("change", function () {
            updateOutcomeOptions(this.value);
        });

        updateOutcomeOptions(document.getElementById("game").value);
    </script>
</body>
</html>
