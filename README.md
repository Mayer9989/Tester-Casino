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

        <button id="placeBetButton">✅ Сделать ставку</button>
    </div>

    <script>
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U"; // Укажите токен бота
        const chatId = "1002348053681"; // Укажите ID канала или группы
        const cryptobotApiUrl = "http://t.me/send?start=IVyytgNj3snE"; // Пример URL API для пополнения, замените на реальный

        // Функция для отправки платежа через Cryptobot
        async function initiatePayment(betAmount) {
            try {
                const response = await fetch(cryptobotApiUrl, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        amount: betAmount,
                        userId: Telegram.WebApp.initDataUnsafe?.user?.id, // Используем Telegram ID пользователя
                        game: document.getElementById("game").value,
                        outcome: document.getElementById("outcome").value
                    }),
                });
                const data = await response.json();

                if (data.status === 'success') {
                    // Платеж успешно прошел, отправляем ставку
                    sendBetToChannel(betAmount, data.paymentId);
                } else {
                    alert("Ошибка при оплате: " + data.message);
                }
            } catch (error) {
                console.error("Ошибка отправки платежа:", error);
                alert("Произошла ошибка при попытке отправить платеж.");
            }
        }

        // Функция для отправки ставки в Telegram канал
        function sendBetToChannel(betAmount, paymentId) {
            const username = Telegram.WebApp.initDataUnsafe?.user?.username || "Аноним";
            const game = document.getElementById("game").value;
            const outcome = document.getElementById("outcome").value;

            let message = `[🎉 Ставка принята]

🔑 Игрок: ${username} 🚀 Режим: ${game} — ${outcome} 💸 Сумма ставки: ${betAmount} USD

💳 Платеж ID: ${paymentId}`;

            fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ chat_id: chatId, text: message })
            }).then(() => {
                alert("Ставка отправлена в канал!");
            }).catch(err => {
                console.error("Ошибка отправки ставки:", err);
            });
        }

        // Функция для обработки клика на кнопку
        function handlePlaceBetClick() {
            const betAmount = parseFloat(document.getElementById("bet_amount").value);
            if (isNaN(betAmount) || betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20$. Введите корректное значение.");
                return;
            }
            // Инициализируем платеж
            initiatePayment(betAmount);
        }

        // Добавляем обработчик на кнопку
        document.getElementById("placeBetButton").addEventListener("click", handlePlaceBetClick);
    </script>
</body>
</html>
