<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Tester Casino - WebApp</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        body, html {
            height: 100%;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            background-color: black;
            font-family: Arial, sans-serif;
            color: white;
            overflow: hidden;
        }

        .container {
            background: rgba(0, 0, 0, 0.8);
            padding: 20px;
            border-radius: 10px;
            display: inline-block;
            max-width: 90%;
            width: 400px;
            text-align: center;
            position: relative;
        }

        h2 {
            font-size: 24px;
            margin-bottom: 20px;
        }

        select, input, button {
            display: block;
            width: 100%;
            margin: 10px auto;
            padding: 10px;
            font-size: 16px;
            border-radius: 5px;
        }

        button {
            background-color: #28a745;
            color: white;
            border: none;
            cursor: pointer;
        }

        button:hover {
            background-color: #218838;
        }
    </style>
</head>
<body>

    <div class="container">
        <h2>🎰 Выберите игру</h2>
        <select id="game">
            <option value="🎳 Боулинг">🎳 Боулинг</option>
            <option value="🎲 Четное/Нечетное">🎲 Четное/Нечетное</option>
            <option value="🎲 Больше/Меньше">🎲 Больше/Меньше</option>
            <option value="⚽ Футбол">⚽ Футбол</option>
            <option value="🏀 Баскетбол">🏀 Баскетбол</option>
        </select>

        <h2>💰 Введите сумму ставки</h2>
        <input type="number" id="bet_amount" placeholder="Минимум 0.20$" step="0.01" min="0.20">

        <h2>🔮 Выберите исход игры</h2>
        <select id="outcome">
            <option value="Больше">Больше</option>
            <option value="Меньше">Меньше</option>
            <option value="Четное">Четное</option>
            <option value="Нечетное">Нечетное</option>
            <option value="Выигрыш">Выигрыш</option>
            <option value="Поражение">Поражение</option>
            <option value="Победа">Победа</option>
        </select>

        <button onclick="placeBet()">✅ Сделать ставку</button>
    </div>

    <script>
        function placeBet() {
            if (!window.Telegram || !Telegram.WebApp) {
                alert("Открывайте WebApp через Telegram!");
                return;
            }

            let game = document.getElementById("game").value;
            let betAmount = parseFloat(document.getElementById("bet_amount").value);
            let outcome = document.getElementById("outcome").value;

            if (betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20$");
                return;
            }

            let paymentUrl = `https://t.me/CryptoBot?start=IVyytgNj3snE&amount=${betAmount}`;

            let username = Telegram.WebApp.initDataUnsafe?.user?.username || "Аноним";
            let message = `[🎉 Ваша ставка принята]

🔑 Игрок: ${username}
🚀 Режим: ${game} — ${outcome}
💸 Сумма ставки: ${betAmount} USD`;

            let token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U";
            let chatId = "-1002348053681"; // ID канала или группы

            // Отправляем сообщение о ставке
            fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ chat_id: chatId, text: message })
            });

            // Открываем ссылку на оплату через CryptoBot
            Telegram.WebApp.openTelegramLink(paymentUrl);
            Telegram.WebApp.close();

            // Имитация результата игры
            setTimeout(() => {
                let resultMessage = "";
                let win = false;

                if (game === "🎲 Больше/Меньше") {
                    let firstDie = Math.floor(Math.random() * 6) + 1;
                    let secondDie = Math.floor(Math.random() * 6) + 1;

                    resultMessage = `🎲 Кубик 1: ${firstDie}\n🎲 Кубик 2: ${secondDie}`;

                    if ((outcome === "Больше" && firstDie > secondDie) || (outcome === "Меньше" && firstDie < secondDie)) {
                        win = true;
                        resultMessage += "\n🎉 Вы выиграли!";
                    } else {
                        resultMessage += "\n❌ Вы проиграли!";
                    }
                } else if (game === "🎲 Четное/Нечетное") {
                    let die = Math.floor(Math.random() * 6) + 1;

                    resultMessage = `🎲 Кубик: ${die}`;

                    if ((outcome === "Четное" && die % 2 === 0) || (outcome === "Нечетное" && die % 2 !== 0)) {
                        win = true;
                        resultMessage += "\n🎉 Вы выиграли!";
                    } else {
                        resultMessage += "\n❌ Вы проиграли!";
                    }
                } else if (game === "🎳 Боулинг") {
                    let score = Math.floor(Math.random() * 10) + 1;  // Имитация очков

                    resultMessage = `🎳 Ваши очки: ${score}`;

                    if (outcome === "Выигрыш" && score > 5 || outcome === "Поражение" && score <= 5) {
                        win = true;
                        resultMessage += "\n🎉 Вы выиграли!";
                    } else {
                        resultMessage += "\n❌ Вы проиграли!";
                    }
                } else if (game === "⚽ Футбол") {
                    let result = Math.random() < 0.5 ? "Победа" : "Поражение";

                    resultMessage = `⚽ Результат: ${result}`;

                    if (outcome === "Победа" && result === "Победа" || outcome === "Поражение" && result === "Поражение") {
                        win = true;
                        resultMessage += "\n🎉 Вы выиграли!";
                    } else {
                        resultMessage += "\n❌ Вы проиграли!";
                    }
                } else if (game === "🏀 Баскетбол") {
                    let result = Math.random() < 0.5 ? "Выиграл" : "Проиграл";

                    resultMessage = `🏀 Результат: ${result}`;

                    if (outcome === "Выиграл" && result === "Выиграл" || outcome === "Проиграл" && result === "Проиграл") {
                        win = true;
                        resultMessage += "\n🎉 Вы выиграли!";
                    } else {
                        resultMessage += "\n❌ Вы проиграли!";
                    }
                }

                // Отправляем результат игры в канал
                let resultMessageText = `🔑 Игрок: ${username}\n🚀 Режим: ${game} — ${outcome}\n💸 Сумма ставки: ${betAmount} USD\n${resultMessage}`;
                fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ chat_id: chatId, text: resultMessageText })
                });

            }, 5000); // Задержка для имитации завершения игры (5 секунд)
        }
    </script>

</body>
</html>
