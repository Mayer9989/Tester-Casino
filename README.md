<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no">
    <title>TESTER CASINO</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        /* Стиль для интерфейса */
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
            font-size: 30px;
            font-weight: bold;
            letter-spacing: 2px;
            margin-bottom: 10px;
        }
        h2 span {
            color: red;
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
        }
        button:hover {
            background: #218838;
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
        <h2>TESTER <span>CASINO</span></h2>
        
        <label for="game">Выберите игру:</label>
        <select id="game">
            <option value="🎲 Четное/Нечетное">🎲 Четное/Нечетное</option>
            <option value="⚽ Футбол">⚽ Футбол</option>
            <option value="🏀 Баскетбол">🏀 Баскетбол</option>
            <option value="✂ Камень/Ножницы/Бумага">✂ Камень/Ножницы/Бумага</option>
            <option value="🎯 Дартс">🎯 Дартс</option>
        </select>

        <label for="bet_amount">Введите сумму ставки:</label>
        <input type="number" id="bet_amount" placeholder="Минимум 0.20$">
        
        <button id="placeBetBtn">✅ Сделать ставку</button>

        <div class="footer">Ваше казино в Telegram. Удачи!</div>
    </div>

    <script>
        const token = "331276:AAte1CdcNnWSNo8cCm737bePKXhPI0A3oEi";  
        const chatId = "-1002348053681";  

        // Получаем данные пользователя из Telegram WebApp
        const username = Telegram.WebApp.initDataUnsafe?.user?.username || "Игрок";
        const userId = Telegram.WebApp.initDataUnsafe?.user?.id || "Неизвестный ID";  

        // Функция для отправки сообщений в Telegram
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
                return data.result.message_id; 
            } catch (error) {
                console.error("Ошибка отправки сообщения:", error);
                alert(`Ошибка: ${error.message}`);
            }
        }

        // Функция для создания счета на оплату через CryptoBot API
        async function createPayment(amount) {
            const response = await fetch('https://cryptobot.api/create_payment', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({
                    amount: amount,
                    currency: 'USDT',
                    user_id: userId,
                    callback_url: 'https://hook.eu2.make.com/dyh9wamknd77wn8txtv3qgu3mdglp3sl'
                })
            });

            const data = await response.json();
            return data.payment_url;  // возвращаем ссылку на оплату
        }

        // Функция для обработки ставки
        document.getElementById("placeBetBtn").addEventListener("click", async function () {
            const game = document.getElementById("game").value;
            const betAmount = parseFloat(document.getElementById("bet_amount").value);

            if (isNaN(betAmount) || betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20$. Введите корректное значение.");
                return;
            }

            // Создаем счет для оплаты через CryptoBot
            const paymentUrl = await createPayment(betAmount);

            // Отправляем сообщение в канал с ссылкой на оплату
            const paymentMessage = `
🔑 Игрок: @${username}
🚀 Игра: ${game}
💸 Сумма ставки: ${betAmount} USD

🖱️ <a href="${paymentUrl}">Перейти к оплате</a>
`;
            const betMessageId = await sendMessage(paymentMessage);

            // Ждем завершения оплаты через Webhook
            fetch("https://hook.eu2.make.com/dyh9wamknd77wn8txtv3qgu3mdglp3sl", {
                method: "POST",
                body: JSON.stringify({
                    amount: betAmount,
                    user: username,
                    status: "completed"
                })
            }).then(async (response) => {
                const result = await response.json();
                if (result.status === "completed") {
                    const outcome = Math.random() < 0.4 ? "Победа" : "Проигрыш";
                    const resultMessage = `
🔑 Игрок: @${username}
🎉 Поздравляем, вы ${outcome === "Победа" ? "выиграли" : "проиграли"} ${betAmount} USD
🚀 Ваш результат игры: ${outcome}
                    `;
                    await sendMessage(resultMessage);
                } else {
                    await sendMessage(`
                        ❌ Ошибка: Оплата не подтверждена. Пожалуйста, попробуйте снова.
                    `);
                }
            }).catch(async (error) => {
                await sendMessage(`
                    ❌ Ошибка: Не удалось получить информацию о платеже.
                `);
            });
        });
    </script>
</body>
</html>
