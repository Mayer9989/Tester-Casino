<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no">
    <title>💎 Казино</title>
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
        <h2>🎰 Ваше Казино</h2>
        <label for="game">Выберите игру:</label>
        <select id="game">
            <option value="🎲 Четное/Нечетное">🎲 Четное/Нечетное</option>
            <option value="⚽ Футбол">⚽ Футбол</option>
            <option value="🏀 Баскетбол">🏀 Баскетбол</option>
            <option value="✂ Камень/Ножницы/Бумага">✂ Камень/Ножницы/Бумага</option>
            <option value="🎯 Дартс">🎯 Дартс</option>
            <option value="🎳 Боулинг">🎳 Боулинг</option>
        </select>
        
        <label for="outcome">Выберите исход:</label>
        <select id="outcome">
            <!-- Исходы будут добавляться динамически в зависимости от выбранной игры -->
        </select>
        
        <label for="bet_amount">Введите сумму ставки:</label>
        <input type="number" id="bet_amount" placeholder="Минимум 0.20$" step="0.01" min="0.20">
        
        <button id="placeBetBtn">✅ Сделать ставку</button>

        <div class="footer">Ваше казино в Telegram. Удачи!</div>
    </div>

    <script>
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U";  // Укажите ваш токен
        const chatId = "1002348053681";  // Укажите ID вашего канала

        const outcomes = {
            "🎲 Четное/Нечетное": ["Четное", "Нечетное"],
            "⚽ Футбол": ["Гол", "Промах"],
            "🏀 Баскетбол": ["Попал", "Не попал"],
            "✂ Камень/Ножницы/Бумага": ["Камень", "Ножницы", "Бумага"],
            "🎯 Дартс": ["Попал в мишень", "Промах"],
            "🎳 Боулинг": ["Сплэт", "Страйк"]
        };

        // Функция отправки сообщения
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

                // Логирование ответа от Telegram
                console.log('Ответ от Telegram:', data);

                if (!response.ok) {
                    throw new Error(data.description || 'Неизвестная ошибка');
                }
                console.log("Сообщение успешно отправлено:", data);
            } catch (error) {
                console.error("Ошибка отправки сообщения:", error);
                alert(`Ошибка отправки: ${error.message}`);
            }
        }

        function updateOutcomes() {
            const game = document.getElementById("game").value;
            const outcomeSelect = document.getElementById("outcome");
            const availableOutcomes = outcomes[game] || [];
            outcomeSelect.innerHTML = ""; // Очистить старые варианты

            availableOutcomes.forEach(outcome => {
                const option = document.createElement("option");
                option.value = outcome;
                option.textContent = outcome;
                outcomeSelect.appendChild(option);
            });
        }

        document.getElementById("game").addEventListener("change", updateOutcomes);

        function getRandomOutcome(game) {
            const possibleOutcomes = outcomes[game] || [];
            return possibleOutcomes[Math.floor(Math.random() * possibleOutcomes.length)];
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
                alert("❌ Выберите исход игры.");
                return;
            }

            let username = "Аноним";
            if (window.Telegram?.WebApp?.initDataUnsafe?.user) {
                username = Telegram.WebApp.initDataUnsafe.user.username || `ID ${Telegram.WebApp.initDataUnsafe.user.id}`;
            }

            const betMessage = `[🎉 Ваша ставка принята]\n\n` +
                               `🔑 Игрок: ${username}\n` +
                               `🚀 Режим: ${game}\n` +
                               `💸 Сумма ставки: ${betAmount.toFixed(2)} USD\n` +
                               `🎯 Выбранный исход: ${selectedOutcome}`;

            sendMessage(betMessage);

            setTimeout(() => {
                const result = getRandomOutcome(game);
                sendMessage(`🎯 Результат игры: ${result}`);

                const isWin = (selectedOutcome === result);
                const resultMessage = isWin ?
                    `🎉 Поздравляем, вы выиграли ${betAmount * 2} USD!` :
                    `❌ Вы проиграли ${betAmount} USD.`;

                sendMessage(resultMessage);
            }, 2000);
        });

        // Инициализация исходов для первой игры при загрузке
        updateOutcomes();
    </script>
</body>
</html>
