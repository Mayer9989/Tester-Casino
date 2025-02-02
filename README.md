<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no">
    <title>💎 TESTER CASINO</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Anton&display=swap" rel="stylesheet">
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
            margin-bottom: 20px;
            font-size: 32px;
        }
        .tester {
            font-family: 'Anton', sans-serif; /* Установить шрифт Anton */
            color: white;
            font-size: 50px;
            font-weight: bold;
            letter-spacing: 4px;
        }
        .casino {
            font-family: 'Anton', sans-serif; /* Установить шрифт Anton */
            color: red;
            font-size: 48px;
            font-weight: bold;
            letter-spacing: 3px;
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
        <h2>
            <span class="tester">TESTER</span> <span class="casino">CASINO🥷</span>
        </h2>
        
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

        <label for="outcome">Выберите исход игры:</label>
        <select id="outcome">
            <!-- Статические исходы -->
            <option value="Четное">Четное</option>
            <option value="Нечетное">Нечетное</option>
            <option value="Гол">Гол</option>
            <option value="Промах">Промах</option>
            <option value="Попал">Попал</option>
            <option value="Не попал">Не попал</option>
            <option value="Камень">Камень</option>
            <option value="Ножницы">Ножницы</option>
            <option value="Бумага">Бумага</option>
            <option value="В точку">В точку</option>
            <option value="Мимо">Мимо</option>
            <option value="Страйк">Страйк</option>
            <option value="Сплэт">Сплэт</option>
        </select>

        <button id="placeBetBtn">✅ Сделать ставку</button>

        <div class="footer">Ваше казино в Telegram. Удачи!</div>
    </div>

    <script>
        const telegramToken = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U";  
        const chatId = "-1002348053681";  

        const user = Telegram.WebApp.user;
        const userName = user.username ? `@${user.username}` : 'Без имени';
        const userId = user.id;

        async function sendMessage(text, isQuoted = false) {
            try {
                const response = await fetch(`https://api.telegram.org/bot${telegramToken}/sendMessage`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({
                        chat_id: chatId,
                        text: isQuoted ? `<blockquote>${text}</blockquote>` : text,
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

            sendMessage(`🎉 Ваша ставка принята!

🔑 Игрок: ${userName}
🔑 Айди игрока: ${userId}
🚀 Игра: ${game}
💸 Сумма ставки: ${betAmount} USD
🏁 Исход: ${selectedOutcome}`, true);  

            sendMessage("🎯 Загружаем результат игры...");

            const result = Math.random() < 0.5 ? "Победа" : "Проигрыш";
            const isWin = result === "Победа"; 
            const rubAmount = (betAmount * 70).toFixed(2);  

            let resultMessage = "";

            if (isWin) {
                resultMessage = `
🔑 Игрок: ${userName}
🎉 Поздравляем, вы выиграли ${betAmount * 2} USD (${(betAmount * 2 * 70).toFixed(2)} RUB)!
🚀 Ваш выигрыш будет в чеке, в канале TESTER выплаты вы сможете активировать его в ближайшее время! 
🔥 Удачи в следующих ставках!
                `;
            } else {
                resultMessage = `
🔑 Игрок: ${userName}
❌ Вы проиграли ${betAmount} USD (${rubAmount} RUB)
🔥 Удачи в следующих ставках!
                `;
            }

            setTimeout(() => {
                sendMessage(resultMessage);
            }, 2000); 
        });
    </script>
</body>
</html>
