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
            background: #000000; /* Очень черный фон */
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
            font-size: 36px;  /* Увеличиваем размер шрифта */
            font-weight: bold;  /* Заголовок жирный */
        }
        h2 span {
            color: white;
        }
        h2 .casino {
            color: red;  /* CASINO красным */
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
        <h2><span>TESTER </span><span class="casino">CASINO</span></h2>
        
        <label for="game">Выберите игру:</label>
        <select id="game">
            <option value="🎲 Четное/Нечетное">🎲 Четное/Нечетное</option>
            <option value="⚽ Футбол">⚽ Футбол</option>
        </select>

        <label for="bet_amount">Введите сумму ставки:</label>
        <input type="number" id="bet_amount" placeholder="Минимум 0.20$" step="0.01" min="0.20">

        <button id="placeBetBtn">✅ Сделать ставку</button>

        <div class="footer">Ваше казино в Telegram. Удачи!</div>
    </div>

    <script>
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U";  // Токен вашего бота
        const chatId = "1002348053681";  // ID вашего канала
        const botApiUrl = 'https://api.telegram.org/bot' + token + '/sendMessage';

        async function sendMessage(text) {
            try {
                const response = await fetch(botApiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        chat_id: chatId,
                        text: text,
                        parse_mode: 'HTML'
                    })
                });
                const data = await response.json();
                if (!response.ok) throw new Error(data.description || 'Ошибка при отправке сообщения');
            } catch (error) {
                console.error("Ошибка отправки сообщения:", error);
                alert(`Ошибка: ${error.message}`);
            }
        }

        document.getElementById("placeBetBtn").addEventListener("click", async function () {
            const betAmount = parseFloat(document.getElementById("bet_amount").value);
            const userId = Telegram.WebApp.initDataUnsafe.user.id; // Получаем ID пользователя из Telegram WebApp

            if (isNaN(betAmount) || betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20$. Введите корректное значение.");
                return;
            }

            // Проверяем баланс пользователя в боте
            const balanceResponse = await fetch(`/get_balance?user_id=${userId}`);
            const balanceData = await balanceResponse.json();
            const balance = balanceData.balance;

            if (betAmount > balance) {
                alert("❌ Недостаточно средств для ставки!");
                return;
            }

            // Списываем ставку с баланса в боте
            await fetch(`/place_bet?user_id=${userId}&bet_amount=${betAmount}`);

            // Отправляем сообщение о ставке
            sendMessage(`[🎉 Ваша ставка принята]

🔑 Игрок: ${Telegram.WebApp.initDataUnsafe.user.username}
🚀 Игра: ${document.getElementById("game").value}
💸 Сумма ставки: ${betAmount} USD`);

            // Генерация результата игры (победа или проигрыш)
            const win = Math.random() < 0.5; // 50% шанс на победу
            const coefficient = 2.0;
            const resultMessage = win ?
                `
🎉 Вы выиграли! Ваш выигрыш: ${betAmount * coefficient} USD.
🔥 Поздравляем! Ваш баланс будет обновлен.
` :
                `
❌ Вы проиграли ${betAmount} USD.
🔥 Попробуйте снова!
`;

            // Отправляем результат игры
            setTimeout(() => {
                sendMessage(resultMessage);
            }, 2000);
        });
    </script>
</body>
</html>
