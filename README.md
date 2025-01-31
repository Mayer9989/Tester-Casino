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
        .payment-options {
            display: flex;
            justify-content: space-between;
            margin-top: 20px;
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

        <h2>💰 Выберите сумму ставки</h2>
        <div class="payment-options">
            <button onclick="payAmount(0.20)">0.20 USD</button>
            <button onclick="payAmount(0.50)">0.50 USD</button>
            <button onclick="payAmount(1.00)">1.00 USD</button>
            <button onclick="payAmount(5.00)">5.00 USD</button>
        </div>

        <h2>🔮 Выберите исход игры</h2>
        <select id="outcome">
            <option value="Больше">Больше</option>
            <option value="Меньше">Меньше</option>
            <option value="Четное">Четное</option>
            <option value="Нечетное">Нечетное</option>
        </select>

        <button id="placeBetButton" disabled>✅ Сделать ставку</button>
    </div>

    <script>
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U"; // Укажите токен бота
        const chatId = "1002348053681"; // Укажите ID канала или группы
        const cryptobotApiUrl = "331276:AAte1CdcNnWSNo8cCm737bePKXhPI0A3oEi"; // Пример URL API для пополнения, замените на реальный

        let selectedBetAmount = 0;

        // Функция для инициирования платежа через Cryptobot
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
                    // Платеж успешно прошел, активируем кнопку ставки
                    selectedBetAmount = betAmount;
                    document.getElementById("placeBetButton").disabled = false;
                    alert(`Платеж на сумму ${betAmount} USD успешно выполнен!`);
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

        // Функция для обработки клика на одну из кнопок оплаты
        function payAmount(amount) {
            initiatePayment(amount);
        }

        // Функция для обработки клика на кнопку "Сделать ставку"
        function handlePlaceBetClick() {
            if (selectedBetAmount > 0) {
                sendBetToChannel(selectedBetAmount, "paymentId123"); // Заглушка для paymentId, заменить на реальный ID
            } else {
                alert("Сначала выполните платеж!");
            }
        }

        // Добавляем обработчик на кнопку
        document.getElementById("placeBetButton").addEventListener("click", handlePlaceBetClick);
    </script>
</body>
</html>
