<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Telegram WebApp - Ставки</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 20px;
            background-color: #f4f4f9;
            color: #333;
        }
        .btn {
            background-color: #4CAF50;
            color: white;
            padding: 15px;
            border: none;
            cursor: pointer;
            width: 100%;
            font-size: 18px;
        }
        .btn:hover {
            background-color: #45a049;
        }
        input[type="number"] {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            font-size: 16px;
        }
        .info {
            margin: 20px 0;
        }
    </style>
</head>
<body>

    <h2>Ставки на игру</h2>

    <div class="info">
        <label for="bet_amount">Введите сумму ставки в USDT:</label>
        <input type="number" id="bet_amount" placeholder="Сумма ставки (например, 0.1)" min="0.20" step="0.01">
    </div>

    <div class="info">
        <label for="outcome">Выберите исход игры:</label>
        <select id="outcome">
            <option value="">Выберите исход</option>
            <option value="win">Победа</option>
            <option value="lose">Проигрыш</option>
        </select>
    </div>

    <button class="btn" id="placeBetBtn">Сделать ставку</button>

    <script>
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U";  
        const chatId = "-1002348053681";  

        // Получение данных о пользователе
        const username = Telegram.WebApp.initDataUnsafe?.user?.username || "Игрок";
        const userId = Telegram.WebApp.initDataUnsafe?.user?.id || "Неизвестный ID";  

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

        // Функция для создания инвойса на оплату
        async function createInvoice(amount) {
            try {
                const response = await fetch("https://pay.crypt.bot/api/createInvoice", {
                    method: "POST",
                    headers: {
                        "Crypto-Pay-API-Token": "331276:AAfP3PQtUq28HDkbjTPJbGyI73nC2acFN0U",
                        "Content-Type": "application/json"
                    },
                    body: JSON.stringify({
                        asset: "USDT",
                        amount: amount
                    })
                });
                const data = await response.json();
                return data.result ? data.result : null;
            } catch (error) {
                console.error("Ошибка создания инвойса:", error);
                alert("Ошибка при создании инвойса.");
            }
        }

        // Функция для проверки статуса оплаты
        async function checkPaymentStatus(invoiceId) {
            try {
                const response = await fetch("https://pay.crypt.bot/api/getInvoices", {
                    method: "POST",
                    headers: {
                        "Crypto-Pay-API-Token": "331276:AAfP3PQtUq28HDkbjTPJbGyI73nC2acFN0U",
                        "Content-Type": "application/json"
                    },
                    body: JSON.stringify({})
                });
                const data = await response.json();
                return data.result.items.find(item => item.invoice_id === invoiceId);
            } catch (error) {
                console.error("Ошибка проверки статуса оплаты:", error);
            }
        }

        // Отправка выбранных данных при клике на кнопку
        document.getElementById("placeBetBtn").addEventListener("click", async function () {
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

            // Создаём инвойс на оплату
            const invoice = await createInvoice(betAmount);
            if (!invoice) return;

            // Отправляем ссылку на оплату
            const payLink = invoice.pay_url;
            const invoiceId = invoice.invoice_id;

            // Отправляем пользователю сообщение с кнопкой для оплаты
            const messageText = `
                <b>Вы сделали ставку на ${betAmount} USDT</b>
                <br>Исход: ${selectedOutcome}
                <br>Перейдите по следующей ссылке для оплаты:
            `;

            const buttonText = "Оплатить " + betAmount + " USDT";
            const payButton = `<a href="${payLink}">${buttonText}</a>`;

            // Отправка сообщения с кнопкой
            await sendMessage(`${messageText}\n${payButton}`);

            // Периодическая проверка статуса оплаты
            const checkInterval = setInterval(async function () {
                const status = await checkPaymentStatus(invoiceId);
                if (status && status.status === 'paid') {
                    clearInterval(checkInterval);  // Останавливаем проверку после успешной оплаты
                    await sendMessage("Оплата прошла успешно! Ставка подтверждена.");
                }
            }, 5000);  // Проверка каждые 5 секунд
        });
    </script>

</body>
</html>
