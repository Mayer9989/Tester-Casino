<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">
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
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.6);
        }
        .casino-title {
            text-align: center;
            margin-bottom: 20px;
            font-size: 30px;
        }
        .casino-title span:first-child {
            color: #000; /* Ярко черный цвет */
            font-weight: bold;
        }
        .casino-title span:last-child {
            color: #FF0000; /* Ярко красный цвет */
            font-weight: bold;
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
        <div class="casino-title">
            <span>TESTER</span> <span>CASINO</span>
        </div>

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

        <div id="outcomeOptions" style="display: none;">
            <label for="outcome">Выберите исход:</label>
            <select id="outcome"></select>
        </div>

        <button id="placeBetBtn">✅ Сделать ставку</button>

        <div class="footer">TESTER CASINO. Удачи!</div>
    </div>

    <script>
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U"; 
        const chatId = "-1002348053681"; 

        const outcomes = {
            "🎲 Четное/Нечетное": ["Четное", "Нечетное"],
            "⚽ Футбол": ["Гол", "Промах"],
            "🏀 Баскетбол": ["Попал", "Не попал"],
            "✂ Камень/Ножницы/Бумага": ["Камень", "Ножницы", "Бумага"],
            "🎯 Дартс": ["В точку", "Мимо"],
            "🎳 Боулинг": ["Страйк", "Сплэт"]
        };

        function updateOutcomeOptions(game) {
            const outcomeSelect = document.getElementById("outcome");
            outcomeSelect.innerHTML = '';

            if (outcomes[game]) {
                outcomes[game].forEach(option => {
                    const opt = document.createElement("option");
                    opt.value = option;
                    opt.textContent = option;
                    outcomeSelect.appendChild(opt);
                });
                document.getElementById("outcomeOptions").style.display = "block";
            } else {
                document.getElementById("outcomeOptions").style.display = "none";
            }
        }

        document.getElementById("game").addEventListener("change", function () {
            updateOutcomeOptions(this.value);
        });

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

                if (!response.ok) {
                    const errorData = await response.json();
                    throw new Error(errorData.description || "Ошибка запроса");
                }

                alert("✅ Ставка успешно отправлена!");
            } catch (error) {
                alert(`❌ Ошибка: ${error.message}`);
                console.error("Ошибка отправки сообщения:", error);
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
            if (window.Telegram?.WebApp?.initDataUnsafe?.user) {
                username = Telegram.WebApp.initDataUnsafe.user.username || `ID ${Telegram.WebApp.initDataUnsafe.user.id}`;
            }

            const betMessage = `[🎰 Ставка принята]\n\n` +
                `🔑 Игрок: ${username}\n` +
                `🚀 Игра: ${game}\n` +
                `💸 Сумма ставки: ${betAmount.toFixed(2)} USD\n` +
                `🏁 Исход: ${selectedOutcome}`;

            sendMessage(betMessage);

            // Задержка перед результатом игры
            setTimeout(() => {
                const result = getRandomOutcome(game);
                sendMessage(`🎯 Результат игры: ${result}`);

                const isWin = result === selectedOutcome;
                const resultMessage = isWin ?
                    `🎉 Поздравляем, вы выиграли ${(betAmount * 1.5).toFixed(2)} USD!` :
                    `❌ Вы проиграли ${betAmount.toFixed(2)} USD.`;

                sendMessage(resultMessage);
            }, 2000);
        });
    </script>
</body>
</html>
