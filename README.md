<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no">
    <title>💎 TESTER CASINO</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@700&display=swap" rel="stylesheet">
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
            font-size: 120px;
            font-family: 'Roboto', sans-serif;
            font-weight: bold;
            letter-spacing: 5px;
            color: white;
            text-shadow: 3px 3px 6px rgba(255, 0, 0, 0.7), 0 0 25px red, 0 0 5px darkred;
        }
        h2 span {
            color: #ff0000;
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
        <h2>TESTER <span>CASINO🥷</span></h2> 
        
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
                <option value="win">Победа</option>
                <option value="lose">Проигрыш</option>
            </select>
        </div>

        <button id="placeBetBtn">✅ Сделать ставку</button>

        <div class="footer">Ваше казино в Telegram. Удачи!</div>
    </div>

    <script>
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U";  
        const chatId = "-1002348053681";  

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
                console.log("Message sent:", data.result);  // Проверка успешной отправки
                return data.result.message_id;  // Возвращаем ID отправленного сообщения
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
        function updateOutcomeOptions() {
            const game = document.getElementById("game").value;
            const outcomeOptions = document.getElementById("outcomeOptions");
            const outcomeSelect = document.getElementById("outcome");

            if (game === "🎲 Четное/Нечетное") {
                outcomeOptions.style.display = "block";
                outcomeSelect.innerHTML = "<option value='win'>Победа</option><option value='lose'>Проигрыш</option>";
            } else {
                outcomeOptions.style.display = "none";
            }
        }

        // Обработчик для изменения игры
        document.getElementById("game").addEventListener("change", updateOutcomeOptions);

        // Обработчик для ставки
        document.getElementById("placeBetBtn").addEventListener("click", async function() {
            const betAmount = parseFloat(document.getElementById("bet_amount").value);
            const game = document.getElementById("game").value;
            const outcome = document.getElementById("outcome") ? document.getElementById("outcome").value : getRandomOutcome();

            // Выводим сообщение о загрузке
            const loadingMessage = await sendMessage("🎯 Загружаем результат игры...");
            console.log("Loading message sent", loadingMessage);

            setTimeout(async () => {
                // Удаляем сообщение "Загружаем результат игры..."
                await sendMessage(`🛑 Результат игры: ${game}`);

                // Отправляем сообщение о победе или проигрыше
                let resultMessage;
                if (outcome === "win") {
                    resultMessage = `🎉 Поздравляем! Вы выиграли ${betAmount} USD.`;
                } else {
                    resultMessage = `❌ Вы проиграли ${betAmount} USD.`;
                }

                await sendMessage(resultMessage);
                console.log("Result message sent", resultMessage);
            }, 3000);  // Отправляем результат через 3 секунды
        });

        // Инициализация
        updateOutcomeOptions();
    </script>
</body>
</html>
