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

        // Функция для создания счета через CryptoPay
        async function createCryptoPayInvoice(amount, currency) {
            const apiToken = "331276:AAte1CdcNnWSNo8cCm737bePKXhPI0A3oEi";
            try {
                const response = await fetch("https://api.cryptopay.me/v1/invoice/create", {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({
                        api_token: apiToken,
                        amount: amount,
                        currency: currency,
                        description: "Ставка в казино"
                    })
                });

                const data = await response.json();
                if (!response.ok || !data.url) {
                    throw new Error(data.message || "Не удалось создать инвойс. Попробуйте снова.");
                }
                return data.url; // Возвращаем ссылку для оплаты
            } catch (error) {
                throw new Error(`Ошибка при создании инвойса: ${error.message}`);
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

            // Создаем инвойс для оплаты
            try {
                const invoiceUrl = await createCryptoPayInvoice(betAmount, 'USDT');
                
                // Отправляем сообщение о ставке
                const betMessageId = await sendMessage(`[🎉 Ваша ставка принята]

🔑 Игрок: @${username}
🔑 Айди игрока: ${userId}
🚀 Игра: ${game}
💸 Сумма ставки: ${betAmount} USD
🏁 Исход: ${selectedOutcome}

💰 Перейдите по ссылке для оплаты: ${invoiceUrl}`);

                // Отправляем сообщение "🎯 Загружаем результат игры..."
                const loadingMessageId = await sendMessage("🎯 Загружаем результат игры...");

                // Удаляем сообщение "🎯 Загружаем результат игры..."
                setTimeout(async () => {
                    await deleteMessage(loadingMessageId);

                    // Генерируем результат игры
                    const result = Math.random() < 0.4 ? "Победа" : "Проигрыш";  
                    const isWin = result === "Победа"; 
                    const rubAmount = (betAmount * 70).toFixed(2);  

                    let resultMessage = "";

                    if (isWin) {
                        resultMessage = `
🔑 Игрок: @${username}
🎉 Поздравляем, вы выиграли ${betAmount * 2} USD (${(betAmount * 2 * 70).toFixed(2)} RUB)!
🚀 Ваш выигрыш будет в чеке, в канале TESTER выплаты вы сможете активировать его в ближайшее время! 
🔥 Удачи в следующих ставках!
                        `;
                    } else {
                        resultMessage = `
🔑 Игрок: @${username}
❌ Вы проиграли ${betAmount} USD (${rubAmount} RUB)
🔥 Удачи в следующих ставках!
                        `;
                    }

                    // Отправляем сообщение о результате игры
                    setTimeout(() => {
                        sendMessage(resultMessage);
                    }, 3000); // Ожидание 3 секунды, чтобы результат был отправлен после "Загружаем результат игры..."
                }, 2000); // Удаляем через 2 секунды
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
