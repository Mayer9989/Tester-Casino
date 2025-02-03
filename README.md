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
        h2 span { color: red; }
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
        select { background: #333; }
        button {
            background: #28a745;
            border: none;
            color: white;
            cursor: pointer;
            font-size: 20px;
        }
        button:hover { background: #218838; }
        button:active { background: #1e7e34; }
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

        <div id="outcomeOptions">
            <label for="outcome">Выберите исход игры:</label>
            <select id="outcome"></select>
        </div>

        <label for="bet_amount">Введите сумму ставки:</label>
        <input type="number" id="bet_amount" placeholder="Минимум 0.20$">
        
        <button id="placeBetBtn">✅ Сделать ставку</button>

        <div class="footer">Ваше казино в Telegram. Удачи!</div>
    </div>

    <script>
        const webhookUrl = "https://hook.eu2.make.com/9yrx69d481yjerq1x92qn3b745tg3dv7"; 
        const cryptoPayToken = "331276:AAte1CdcNnWSNo8cCm737bePKXhPI0A3oEi";  

        // Данные пользователя
        const username = Telegram.WebApp.initDataUnsafe?.user?.username || "Игрок";
        const userId = Telegram.WebApp.initDataUnsafe?.user?.id || "Неизвестный ID";  

        // Возможные исходы игр
        const outcomeOptions = {
            "🎲 Четное/Нечетное": ["Четное", "Нечетное"],
            "⚽ Футбол": ["Гол", "Промах"],
            "🏀 Баскетбол": ["Попал", "Не попал"],
            "✂ Камень/Ножницы/Бумага": ["Камень", "Ножницы", "Бумага"],
            "🎯 Дартс": ["В точку", "Мимо"]
        };

        function updateOutcomeOptions(game) {
            const outcomeSelect = document.getElementById("outcome");
            outcomeSelect.innerHTML = '';

            if (outcomeOptions[game]) {
                outcomeOptions[game].forEach(option => {
                    const opt = document.createElement("option");
                    opt.value = option;
                    opt.textContent = option;
                    outcomeSelect.appendChild(opt);
                });
            }
        }

        document.getElementById("game").addEventListener("change", function() {
            updateOutcomeOptions(this.value);
        });

        updateOutcomeOptions(document.getElementById("game").value);

        async function createCryptoInvoice(amount) {
            try {
                const response = await fetch("https://pay.crypt.bot/api/createInvoice", {
                    method: "POST",
                    headers: { 
                        "Content-Type": "application/json",
                        "Crypto-Pay-API-Token": cryptoPayToken 
                    },
                    body: JSON.stringify({
                        asset: "USDT",
                        amount: amount.toFixed(2),
                        description: "Ставка в TESTER CASINO",
                        paid_btn_name: "viewItem",
                        allow_anonymous: false
                    })
                });

                const data = await response.json();
                if (!data.ok) throw new Error(data.error);

                return data.result.pay_url;
            } catch (error) {
                console.error("Ошибка создания инвойса:", error);
                alert("Ошибка создания инвойса. Попробуйте позже.");
                return null;
            }
        }

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

            const invoiceUrl = await createCryptoInvoice(betAmount);
            if (!invoiceUrl) return;

            alert("💰 Оплатите ставку, вас сейчас перенаправит на CryptoBot!");
            window.location.href = invoiceUrl;

            await fetch(webhookUrl, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({
                    user_id: userId,
                    username: username,
                    game: game,
                    bet_amount: betAmount,
                    selected_outcome: selectedOutcome,
                    invoice_url: invoiceUrl
                })
            });
        });
    </script>
</body>
</html>
