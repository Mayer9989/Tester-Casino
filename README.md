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
            font-size: 30px; /* Размер для обоих текстов */
            font-weight: bold;
            letter-spacing: 2px;
            margin-bottom: 10px;
        }
        h2 span {
            color: red; /* Цвет для CASINO */
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
                return data.result.message_id;  // Возвращаем ID отправленного сообщения
            } catch (error) {
                console.error("Ошибка отправки сообщения:", error);
                alert(`Ошибка: ${error.message}`);
            }
        }

        // Функция для удаления сообщения
        async function deleteMessage(messageId) {
            try {
                const response = await fetch(`https://api.telegram.org/bot${token}/deleteMessage`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({
                        chat_id: chatId,
                        message_id: messageId
                    })
                });

                const data = await response.json();
                if (!response.ok) throw new Error(data.description || 'Неизвестная ошибка');
            } catch (error) {
                console.error("Ошибка удаления сообщения:", error);
            }
        }

        // Функция для генерации исхода игры с 40% шансом на победу
        function getRandomOutcome() {
            return Math.random() < 0.4 ? "Победа" : "Проигрыш";  
        }

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

            outcomeSelect.innerHTML = '';  // Очистить предыдущее содержимое

            outcomeOptions[game].forEach(option => {
                const opt = document.createElement("option");
                opt.value = option;
                opt.textContent = option;
                outcomeSelect.appendChild(opt);
            });

            document.getElementById("outcomeOptions").style.display = "block";
        }

        // Функция для создания счета через CryptoBot
        async function createCryptoPayment(amount) {
            const cryptoBotToken = "331276:AAte1CdcNnWSNo8cCm737bePKXhPI0A3oEi";
            const url = "https://api.cryptobot.com/create_invoice";  // Убедитесь, что этот URL правильный
            try {
                const response = await fetch(url, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({
                        token: cryptoBotToken,
                        amount: amount,
                        currency: "USDT",
                    })
                });

                const data = await response.json();
                if (data.success) {
                    return data.payment_url; // Возвращаем ссылку на оплату
                } else {
                    throw new Error(data.error || 'Ошибка при создании счета');
                }
            } catch (error) {
                console.error("Ошибка создания счета:", error);
                alert(`Ошибка: ${error.message}`);
            }
        }

        // Обработчик ставки
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

            // 1. Отправляем сообщение "Загружаем результат игры..."
            const loadingMessage = await sendMessage("🔄 Загружаем результат игры...");
            const messageId = loadingMessage.message_id;

            // 2. Создаем счет через CryptoBot
            const paymentUrl = await createCryptoPayment(betAmount);
            if (!paymentUrl) return;

            // 3. Отправляем сообщение с ссылкой на оплату
            await sendMessage(`
                📬 Для завершения ставки, перейдите по следующей ссылке для оплаты: 
                ${paymentUrl}
            `);

            // 4. После завершения оплаты (выиграл или проиграл)
            setTimeout(async () => {
                // Генерируем результат (победа или проигрыш)
                const outcome = getRandomOutcome();
                let resultMessage = '';

                if (outcome === "Победа") {
                    resultMessage = `
🔑 Игрок: @${username}
🎉 Поздравляем, вы выиграли ${betAmount * 2} USD!
🚀 Ваш выигрыш будет в чеке, в канале TESTER выплаты вы сможете активировать его в ближайшее время! 
🔥 Удачи в следующих ставках!
                    `;
                } else {
                    resultMessage = `
🔑 Игрок: @${username}
❌ Вы проиграли ${betAmount} USD
🔥 Удачи в следующих ставках!
                    `;
                }

                // 5. Отправляем сообщение о результате игры
                await sendMessage(resultMessage);

                // 6. Удаляем сообщение "Загружаем результат игры..."
                await deleteMessage(messageId);
            }, 2000); // Задержка в 2 секунды перед отправкой результата
        });

        // Обработчик изменения игры для обновления исходов
        document.getElementById("game").addEventListener("change", function() {
            const selectedGame = this.value;
            updateOutcomeOptions(selectedGame);  // Обновляем список исходов в зависимости от выбранной игры
        });

        // Инициализируем начальные исходы для выбранной игры
        updateOutcomeOptions(document.getElementById("game").value);
    </script>
</body>
</html>
