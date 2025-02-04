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
            font-weight: bold;
            color: white;
            font-size: 32px;
        }
        #title {
            color: white;
            font-weight: bold;
            font-size: 50px;
        }
        #casino {
            color: red;
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
        <h2 id="title">🎰 <span id="casino">TESTER</span> CASINO</h2>
        <label for="user_id">Введите ваш ID в Telegram:</label>
        <input type="text" id="user_id" placeholder="Ваш ID пользователя">
        
        <label for="bet_amount">Введите сумму ставки:</label>
        <input type="number" id="bet_amount" placeholder="Минимум 0.20$" step="0.01" min="0.20">

        <button id="placeBetBtn">✅ Сделать ставку</button>

        <div id="balance"></div>

        <div class="footer">Ваше казино в Telegram. Удачи!</div>
    </div>

    <script>
        const apiUrl = "http://127.0.0.1:5000";  // URL вашего Flask-приложения

        // Функция для получения баланса
        async function getBalance(userId) {
            const response = await fetch(`${apiUrl}/get_balance/${userId}`);
            const data = await response.json();
            if (data.balance !== undefined) {
                document.getElementById("balance").textContent = `Ваш баланс: ${data.balance} USD`;
            } else {
                alert("Ошибка: Пользователь не найден!");
            }
        }

        // Функция для отправки ставки
        document.getElementById("placeBetBtn").addEventListener("click", async function () {
            const userId = document.getElementById("user_id").value;
            const betAmount = parseFloat(document.getElementById("bet_amount").value);

            if (isNaN(betAmount) || betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20$. Введите корректное значение.");
                return;
            }

            const response = await fetch(`${apiUrl}/place_bet`, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ user_id: userId, bet_amount: betAmount })
            });

            const data = await response.json();

            if (data.error) {
                alert(`Ошибка: ${data.error}`);
            } else {
                alert(`Результат ставки: ${data.result}`);
                getBalance(userId);  // Обновление баланса после ставки
            }
        });
    </script>
</body>
</html>
