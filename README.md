<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no">
    <title>Создание инвойса</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        /* Ваши стили */
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
        input, button {
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
    </style>
</head>
<body>
    <div class="container">
        <h2>Создание инвойса</h2>
        
        <label for="amount">Введите сумму для создания инвойса (в USDT):</label>
        <input type="number" id="amount" placeholder="Сумма в USDT" min="0.20">

        <button id="createInvoiceBtn">Создать инвойс</button>
    </div>

    <script>
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U";  // Ваш API токен
        const chatId = "-1002348053681";  // Ваш ID канала Telegram

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
                return data.result.message_id;
            } catch (error) {
                console.error("Ошибка отправки сообщения:", error);
                alert(`Ошибка: ${error.message}`);
            }
        }

        // Функция для создания инвойса и получения ссылки
        async function createInvoice(amount) {
            const apiUrl = "https://api.cryptobot.com/crypto-pay/create-invoice";  // Замените на правильный URL CryptoPay API
            try {
                const response = await fetch(apiUrl, {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json"
                    },
                    body: JSON.stringify({
                        token: "331276:AAfP3PQtUq28HDkbjTPJbGyI73nC2acFN0U",  // Ваш API токен
                        amount: amount, 
                        currency: "USDT",   
                        description: "Оплата для ставок"
                    })
                });

                const data = await response.json();
                if (!data.payment_url) {
                    throw new Error('Ошибка при создании инвойса');
                }

                // Возвращаем ссылку на инвойс
                return data.payment_url;
            } catch (error) {
                console.error("Ошибка создания инвойса:", error);
                alert(`Ошибка: ${error.message}`);
            }
        }

        // Обработчик кнопки "Создать инвойс"
        document.getElementById("createInvoiceBtn").addEventListener("click", async function() {
            const amount = parseFloat(document.getElementById("amount").value);

            if (isNaN(amount) || amount < 0.20) {
                alert("❌ Минимальная сумма инвойса — 0.20 USDT.");
                return;
            }

            // Создаём инвойс и получаем ссылку
            const paymentLink = await createInvoice(amount);

            if (paymentLink) {
                // Отправляем ссылку на инвойс в канал
                const message = `
                🧾 Новый инвойс:
                💸 Сумма: ${amount} USDT
                🖋 Ссылка на оплату: ${paymentLink}
                `;
                await sendMessage(message);
                alert("Ссылка на инвойс отправлена в канал.");
            }
        });
    </script>
</body>
</html>
