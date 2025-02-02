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
        const botToken = "331276:AAte1CdcNnWSNo8cCm737bePKXhPI0A3oEi";  // Замените на токен вашего CryptoBot
        const webhookUrl = "https://hook.eu2.make.com/dyh9wamknd77wn8txtv3qgu3mdglp3sl";  // Замените на URL вашего вебхука Make

        // Отправка данных через вебхук Make
        async function sendToWebhook(amount, game, outcome) {
            try {
                const response = await fetch(webhookUrl, {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json"
                    },
                    body: JSON.stringify({
                        amount: amount,
                        game: game,
                        outcome: outcome
                    })
                });

                const data = await response.json();
                if (response.ok) {
                    return data;  // Возвращаем данные, если все прошло успешно
                } else {
                    throw new Error(data.message || "Ошибка вебхука");
                }
            } catch (error) {
                alert("Ошибка отправки данных: " + error.message);
                throw error;
            }
        }

        // Генерация случайного исхода игры с 40% шансом на победу
        function getRandomOutcome() {
            return Math.random() < 0.4 ? "Победа" : "Проигрыш";
        }

        // Обновление возможных исходов игры
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

        // Обработка события нажатия на кнопку ставки
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
                // Отправка данных в Make
                const result = await sendToWebhook(betAmount, game, selectedOutcome);
                
                // Показать пользователю информацию
                alert("Ставка отправлена в систему.");
                
                // Генерация случайного результата
                const gameResult = getRandomOutcome();

                // Отправка результата в Telegram (если необходимо)
                const username = "Игрок_1";
                const resultMessage = gameResult === "Победа"
                    ? `🎉 Поздравляем, вы выиграли ${betAmount * 2} USDT!`
                    : `❌ Вы проиграли ${betAmount} USDT.`;

                // Здесь можно отправить сообщение в Telegram (это не обязательно, зависит от вашего использования)
                // await sendMessage(resultMessage);

            } catch (error) {
                console.error("Ошибка: ", error);
            }
        });

        document.getElementById("game").addEventListener("change", function () {
            updateOutcomeOptions(this.value);
        });

        updateOutcomeOptions(document.getElementById("game").value);
    </script>
</body>
</html>
