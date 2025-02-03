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

        <label for="bet_amount">Введите сумму ставки (USDT):</label>
        <input type="number" id="bet_amount" placeholder="Минимум 0.20 USDT">

        <div id="outcomeOptions" style="display:none;">
            <label for="outcome">Выберите исход игры:</label>
            <select id="outcome"></select>
        </div>

        <button id="placeBetBtn">✅ Сделать ставку</button>

        <div class="footer">Ваше казино в Telegram. Удачи!</div>
    </div>

    <script>
        const CRYPTOBOT_API_TOKEN = "331276:AAte1CdcNnWSNo8cCm737bePKXhPI0A3oEi"; // Замени на свой API-токен от CryptoBot
        const TELEGRAM_BOT_TOKEN = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U"; // Замени на токен бота
        const CHAT_ID = "1002348053681"; // Замени на ID чата/канала
        const currency = "USDT";

        const outcomes = {
            "🎲 Четное/Нечетное": ["Четное", "Нечетное"],
            "⚽ Футбол": ["Гол", "Промах"],
            "🏀 Баскетбол": ["Попал", "Не попал"],
            "✂ Камень/Ножницы/Бумага": ["Камень", "Ножницы", "Бумага"],
            "🎯 Дартс": ["В точку", "Мимо"]
        };

        function updateOutcomeOptions(game) {
            const outcomeSelect = document.getElementById("outcome");
            outcomeSelect.innerHTML = '';

            outcomes[game].forEach(option => {
                const opt = document.createElement("option");
                opt.value = option;
                opt.textContent = option;
                outcomeSelect.appendChild(opt);
            });

            document.getElementById("outcomeOptions").style.display = "block";
        }

        async function createInvoice(amount) {
            const response = await fetch("https://pay.crypt.bot/api/createInvoice", {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                    "Authorization": `Bearer ${CRYPTOBOT_API_TOKEN}`
                },
                body: JSON.stringify({ asset: currency, amount: amount, description: "Ставка в казино" })
            });

            const data = await response.json();
            return data.ok ? data.result.pay_url : null;
        }

        async function sendMessage(text) {
            const response = await fetch(`https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage`, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ chat_id: CHAT_ID, text: text, parse_mode: "HTML" })
            });
            return response.ok ? (await response.json()).result.message_id : null;
        }

        async function deleteMessage(messageId) {
            if (!messageId) return;
            await fetch(`https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/deleteMessage`, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ chat_id: CHAT_ID, message_id: messageId })
            });
        }

        function getRandomOutcome() {
            return Math.random() < 0.4 ? "Победа" : "Проигрыш";  
        }

        document.getElementById("placeBetBtn").addEventListener("click", async function () {
            const game = document.getElementById("game").value;
            const betAmount = parseFloat(document.getElementById("bet_amount").value);
            const selectedOutcome = document.getElementById("outcome").value;

            if (isNaN(betAmount) || betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20 USDT. Введите корректное значение.");
                return;
            }

            const payUrl = await createInvoice(betAmount);
            if (payUrl) {
                window.location.href = payUrl;
            }
        });

        async function processPayment(username, game, betAmount, selectedOutcome) {
            const betMessageId = await sendMessage(`🎉 Ваша ставка принята\n\n🔑 Игрок: @${username}\n🚀 Игра: ${game}\n💸 Сумма ставки: ${betAmount} USDT\n🏁 Исход: ${selectedOutcome}`);

            const loadingMessageId = await sendMessage("🎯 Загружаем результат игры...");

            setTimeout(async () => {
                await deleteMessage(loadingMessageId);

                const result = getRandomOutcome();
                const isWin = result === "Победа";
                let resultMessage = isWin
                    ? `🔑 Игрок: @${username}\n🎉 Поздравляем, вы выиграли ${betAmount * 2} USDT!\n🚀 Ваш выигрыш будет в чеке!`
                    : `🔑 Игрок: @${username}\n❌ Вы проиграли ${betAmount} USDT\n🔥 Удачи в следующих ставках!`;

                setTimeout(() => sendMessage(resultMessage), 3000);
            }, 2000);
        }

        document.getElementById("game").addEventListener("change", function() {
            updateOutcomeOptions(this.value);
        });

        updateOutcomeOptions(document.getElementById("game").value);
    </script>
</body>
</html>
