<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Tester Casino - WebApp</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        body, html {
            height: 100%;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            background-color: black;
            font-family: Arial, sans-serif;
            color: white;
            overflow: hidden;
        }
        .container {
            background: rgba(0, 0, 0, 0.8);
            padding: 20px;
            border-radius: 10px;
            display: inline-block;
            max-width: 90%;
            width: 400px;
            text-align: center;
            position: relative;
        }
        h2 {
            font-size: 24px;
            margin-bottom: 20px;
        }
        select, input, button {
            display: block;
            width: 100%;
            margin: 10px auto;
            padding: 10px;
            font-size: 16px;
            border-radius: 5px;
        }
        button {
            background-color: #28a745;
            color: white;
            border: none;
            cursor: pointer;
        }
        button:hover {
            background-color: #218838;
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>🎰 Выберите игру</h2>
        <select id="game">
            <option value="🎳 Боулинг">🎳 Боулинг</option>
            <option value="🎲 Четное/Нечетное">🎲 Четное/Нечетное</option>
            <option value="🎲 Больше/Меньше">🎲 Больше/Меньше</option>
            <option value="⚽ Футбол">⚽ Футбол</option>
            <option value="🏀 Баскетбол">🏀 Баскетбол</option>
        </select>

        <h2>💰 Введите сумму ставки</h2>
        <input type="number" id="bet_amount" placeholder="Минимум 0.20$" step="0.01" min="0.20">

        <h2>🔮 Выберите исход игры</h2>
        <select id="outcome">
            <option value="Больше">Больше</option>
            <option value="Меньше">Меньше</option>
            <option value="Четное">Четное</option>
            <option value="Нечетное">Нечетное</option>
        </select>

        <button id="placeBetBtn">✅ Сделать ставку</button>
    </div>

    <script>
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U"; // Твой токен бота
        const chatId = "-1002348053681"; // ID канала, убедись, что перед ним стоит "-100" (если это канал)

        function placeBet() {
            const game = document.getElementById("game").value;
            const betAmount = parseFloat(document.getElementById("bet_amount").value);
            const outcome = document.getElementById("outcome").value;

            // Проверяем, указана ли ставка и минимальное значение
            if (isNaN(betAmount) || betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20$. Введите корректное значение.");
                return;
            }

            // Получаем данные пользователя из Telegram WebApp
            let username = "Аноним";
            if (window.Telegram && Telegram.WebApp.initDataUnsafe && Telegram.WebApp.initDataUnsafe.user) {
                username = Telegram.WebApp.initDataUnsafe.user.username || `ID ${Telegram.WebApp.initDataUnsafe.user.id}`;
            }

            // Формируем сообщение для канала
            const message = `[🎉 Ваша ставка принята]

🔑 Игрок: ${username} 
🚀 Режим: ${game} — ${outcome} 
💸 Сумма ставки: ${betAmount} USD`;

            console.log("Отправка сообщения:", message);

            // Отправка запроса в Telegram API
            fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({
                    chat_id: chatId,
                    text: message,
                    parse_mode: "HTML"
                })
            })
            .then(response => response.json())
            .then(data => {
                console.log("Ответ от Telegram API:", data);
                if (data.ok) {
                    alert("✅ Ставка принята!");
                    window.location.href = "http://t.me/send?start=IVyytgNj3snE"; // Ссылка на кошелек или оплату
                } else {
                    alert("❌ Ошибка при отправке ставки! Проверьте настройки.");
                }
            })
            .catch(error => {
                console.error("Ошибка при отправке запроса:", error);
                alert("❌ Ошибка соединения с Telegram API.");
            });
        }

        document.getElementById("placeBetBtn").addEventListener("click", placeBet);
    </script>
</body>
</html>
