<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no">
    <title>Сделать ставку</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
</head>
<body>
    <button id="placeBetBtn">Сделать ставку</button>

    <script>
        const token = "331276:AAfP3PQtUq28HDkbjTPJbGyI73nC2acFN0U";  // Ваш API токен CryptoBot
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
            } catch (error) {
                console.error("Ошибка отправки сообщения:", error);
            }
        }

        // Функция для создания инвойса через CryptoBot API
        async function createInvoice(amount) {
            const apiUrl = "https://api.cryptobot.com/create_invoice";  // Замените на актуальный URL

            try {
                const response = await fetch(apiUrl, {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json"
                    },
                    body: JSON.stringify({
                        amount: amount,  // Сумма инвойса
                        currency: "USDT",  // Валюта
                        description: "Оплата для ставки"
                    })
                });

                const data = await response.json();
                console.log(data);  // Логируем ответ от API для отладки

                if (!data.payment_url) {
                    throw new Error('Ошибка при создании инвойса');
                }

                return data.payment_url;  // Возвращаем ссылку на инвойс
            } catch (error) {
                console.error("Ошибка создания инвойса:", error);
                alert("Ошибка при создании инвойса: " + error.message);  // Отображаем ошибку пользователю
            }
        }

        // Обработчик кнопки "Сделать ставку"
        document.getElementById("placeBetBtn").addEventListener("click", async function() {
            const amount = 0.20;  // Фиксированная сумма для ставки, например, 0.20 USDT

            // Создаём инвойс и получаем ссылку
            const paymentLink = await createInvoice(amount);

            if (paymentLink) {
                // Отправляем ссылку на инвойс в канал Telegram
                const message = `
                    🎮 Новая ставка
                    💸 Сумма: ${amount} USDT
                    🖋 Ссылка на оплату: ${paymentLink}
                `;
                await sendMessage(message);
                alert("Ссылка на инвойс отправлена в канал.");
            } else {
                alert("Ошибка при создании инвойса.");
            }
        });
    </script>
</body>
</html>
