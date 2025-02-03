<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no">
    <title>TESTER CASINO</title>
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
        
        <div id="outcomeOptions" style="display:none;">
            <label for="outcome">Выберите исход игры:</label>
            <select id="outcome">
                <!-- Исходы будут добавляться динамически -->
            </select>
        </div>

        <button id="placeBetBtn">✅ Сделать ставку</button>

        <div class="footer">Ваше казино в Telegram. Удачи!</div>
    </div>

    <script>
        const chatId = Telegram.WebApp.initDataUnsafe?.user?.id || "Неизвестный ID";
        const username = Telegram.WebApp.initDataUnsafe?.user?.username || "Игрок";

        // Укажите свой Webhook URL из Make
        const webhookUrl = "https://hook.eu2.make.com/dyh9wamknd77wn8txtv3qgu3mdglp3sl"; 

        // Функция для создания инвойса через Webhook Make
        async function createInvoiceThroughWebhook(amount, currency = "USDT") {
            try {
                // Подготовка данных для отправки в Webhook Make
                const data = {
                    amount: amount,
                    currency: currency,
                    user_id: chatId,
                    username: username,
                    game: document.getElementById("game").value,
                    bet_amount: amount,
                    outcome: document.getElementById("outcome").value,
                    callback_url: `https://t.me/${username}` // Ссылка для callback, можно изменить
                };

                // Отправка данных в Webhook Make
                const response = await fetch(webhookUrl, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify(data)
                });

                const responseData = await response.json();
                
                if (response.ok) {
                    if (responseData.status === "success" && responseData.invoice_url) {
                        return responseData.invoice_url;
                    } else {
                        throw new Error(responseData.message || "Ошибка при получении инвойса.");
                    }
                } else {
                    throw new Error(responseData.message || "Ошибка связи с сервером.");
                }
            } catch (error) {
                throw new Error(`Ошибка при создании инвойса: ${error.message}`);
            }
        }

        // Функция для отправки сообщений в Telegram WebApp
        async function sendMessage(text) {
            try {
                const response = await fetch(`https://api.telegram.org/bot${apiToken}/sendMessage`, {
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

        // Обработчик клика по кнопке "Сделать ставку"
        document.getElementById("placeBetBtn").addEventListener("click", async function () {
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

            // Создаем инвойс через Webhook Make
            try {
                const invoiceUrl = await createInvoiceThroughWebhook(betAmount);

                // Отправляем сообщение с инвойсом в Telegram WebApp
                const message = `[🎉 Ваша ставка принята]

🔑 Игрок: @${username}
🔑 Айди игрока: ${chatId}
🚀 Игра: ${game}
💸 Сумма ставки: ${betAmount} USD
🏁 Исход: ${selectedOutcome}

💰 Перейдите по ссылке для оплаты: [🔗 Оплатить](${invoiceUrl})`;

                await sendMessage(message);

                // Переходим к результату игры через некоторое время
                setTimeout(async () => {
                    const result = Math.random() < 0.4 ? "Победа" : "Проигрыш";  
                    const resultMessage = result === "Победа" ? `
🎉 Поздравляем, вы выиграли! Ваша ставка удвоилась!
                    ` : `
❌ К сожалению, вы проиграли вашу ставку.
                    `;
                    await sendMessage(resultMessage);
                }, 5000); // 5 секунд ожидания
            } catch (error) {
                alert(`Ошибка: ${error.message}`);
            }
        });

        // Обработчик изменения игры для обновления исходов
        document.getElementById("game").addEventListener("change", function() {
            const selectedGame = this.value;
            updateOutcomeOptions(selectedGame);
        });

        // Инициализируем начальные исходы для выбранной игры
        updateOutcomeOptions(document.getElementById("game").value);

        // Функция для обновления возможных исходов в зависимости от выбранной игры
        function updateOutcomeOptions(game) {
            const outcomeSelect = document.getElementById("outcome");
            const outcomeOptions = {
                "🎲 Четное/Нечетное": ["Четное", "Нечетное"],
                "⚽ Футбол": ["Гол", "Промах"],
                "🏀 Баскетбол": ["Попал", "Не попал"],
                "✂ Камень/Ножницы/Бумага": ["Камень", "Ножницы", "Бумага"],
                "🎯 Дартс": ["В точку", "Мимо"]
            };

            outcomeSelect.innerHTML = ''; 

            outcomeOptions[game].forEach(option => {
                const optionElement = document.createElement("option");
                optionElement.value = option;
                optionElement.textContent = option;
                outcomeSelect.appendChild(optionElement);
            });

            document.getElementById("outcomeOptions").style.display = "block";
        }
    </script>
</body>
</html>
