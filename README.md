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
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U";  
        const chatId = "-1002348053681";  

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
                console.log("Сообщение успешно отправлено:", data);
            } catch (error) {
                console.error("Ошибка отправки сообщения:", error);
                alert(`Ошибка: ${error.message}`);
            }
        }

        async function sendMessageWithButton(text) {
            const messageData = {
                chat_id: chatId,
                text: text,
                parse_mode: "HTML",
                reply_markup: {
                    inline_keyboard: [
                        [
                            {
                                text: "Сделать ставку ♣️",  
                                url: "https://your-webapp-url.com"  
                            }
                        ]
                    ]
                }
            };

            try {
                const response = await fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify(messageData)
                });

                const data = await response.json();
                if (!response.ok) throw new Error(data.description || 'Неизвестная ошибка');
                console.log("Сообщение успешно отправлено:", data);
            } catch (error) {
                console.error("Ошибка отправки сообщения:", error);
                alert(`Ошибка: ${error.message}`);
            }
        }

        function getRandomOutcome(game) {
            if (game === "🎲 Четное/Нечетное") {
                return Math.random() < 0.5 ? "Четное" : "Нечетное";
            }
            if (game === "⚽ Футбол") {
                return Math.random() < 0.5 ? "Гол" : "Промах";
            }
            if (game === "🏀 Баскетбол") {
                return Math.random() < 0.5 ? "Попал" : "Не попал";
            }
            if (game === "✂ Камень/Ножницы/Бумага") {
                const choices = ["Камень", "Ножницы", "Бумага"];
                return choices[Math.floor(Math.random() * choices.length)];
            }
            if (game === "🎯 Дартс") {
                return Math.random() < 0.5 ? "В точку" : "Мимо";
            }
            if (game === "🎳 Боулинг") {
                return Math.random() < 0.5 ? "Страйк" : "Сплэт";
            }
        }

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

        document.getElementById("game").addEventListener("change", function() {
            const selectedGame = this.value;
            updateOutcomeOptions(selectedGame);
        });

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

            let username = "Аноним";  
            let userId = "123456";  

            sendMessage(`[🎰 Ставка принята]

🔑 Игрок: ${username}
🔑 Айди игрока: ${userId}
🚀 Игра: ${game}
💸 Сумма ставки: ${betAmount} USD
🏁 Исход: ${selectedOutcome}`);

            sendMessage("🎯 Загружаем результат игры...");
            
            setTimeout(() => {
                const result = getRandomOutcome(game);
                const isWin = result === selectedOutcome;

                const resultMessage = isWin ?
                    `🎉 Поздравляем, вы выиграли ${betAmount * 2} USD (${(betAmount * 2 * 70).toFixed(2)} RUB)! 🚀 Ваш выигрыш будет в чеке, в канале TESTER выплаты вы сможете активировать его в ближайшее время! 🔥 Удачи в следующих ставках!` :
                    `❌ Вы проиграли ${betAmount} USD (${(betAmount * 70).toFixed(2)} RUB). 🔥 Удачи в следующих ставках!`;

                sendMessage(resultMessage);  // ОТПРАВКА ТЕКСТА ПОБЕДЫ/ПРОИГРЫША
                sendMessageWithButton("Хотите сделать новую ставку? 🎯");
            }, 3000);
        });
    </script>
</body>
</html>
