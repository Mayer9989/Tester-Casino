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
            }).then(response => {
                if (response.ok) {
                    console.log("Ставка успешно отправлена в канал.");

                    // Открываем ссылку на оплату через CryptoBot
                    let paymentUrl = `https://t.me/CryptoBot?start=IVyytgNj3snE&amount=${betAmount}`;
                    Telegram.WebApp.openTelegramLink(paymentUrl);
                    Telegram.WebApp.close();

                    // Запускаем игру после принятия ставки
                    startGame(game, outcome, betAmount, chatId, token);
                } else {
                    console.error("Ошибка при отправке ставки в канал:", response.statusText);
                }
            }).catch(err => console.error("Ошибка отправки запроса:", err));
        }

        function startGame(game, outcome, betAmount, chatId, token) {
            console.log("Запуск игры...");

            // Имитация результата игры: генерируем два кубика
            setTimeout(() => {
                let firstDie = Math.floor(Math.random() * 6) + 1; // Первый кубик
                let secondDie = Math.floor(Math.random() * 6) + 1; // Второй кубик

                // Отправляем кубики в канал
                let diceMessage = `🎲 Кубик 1: ${firstDie}\n🎲 Кубик 2: ${secondDie}`;
                fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ chat_id: chatId, text: diceMessage })
                }).then(() => {
                    console.log("Кубики отправлены в канал.");

                    // Проверка результата
                    let resultMessage = "";
                    if (game === "🎲 Больше/Меньше") {
                        if ((outcome === "Больше" && firstDie > secondDie) || (outcome === "Меньше" && firstDie < secondDie)) {
                            resultMessage = "🎉 Вы выиграли!";
                        } else {
                            resultMessage = "❌ Вы проиграли!";
                        }
                    } else if (game === "🎲 Четное/Нечетное") {
                        if ((outcome === "Четное" && firstDie % 2 === 0) || (outcome === "Нечетное" && firstDie % 2 !== 0)) {
                            resultMessage = "🎉 Вы выиграли!";
                        } else {
                            resultMessage = "❌ Вы проиграли!";
                        }
                    } else if (game === "🎳 Боулинг") {
                        let pins = Math.random() < 0.5 ? "Поражение" : "Выигрыш";
                        resultMessage = `🔑 Игрок: ${username}\n🚀 Режим: Боулинг — ${pins}\n💸 Сумма ставки: ${betAmount} USD`;
                    } else if (game === "⚽ Футбол" || game === "🏀 Баскетбол") {
                        let winGoal = Math.random() < 0.5;
                        resultMessage = `🔑 Игрок: ${username}\n🚀 Режим: ${game} — ${winGoal ? 'Выигрыш' : 'Поражение'}\n💸 Сумма ставки: ${betAmount} USD`;
                    }

                    // Отправляем результат в канал
                    fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                        method: "POST",
                        headers: { "Content-Type": "application/json" },
                        body: JSON.stringify({ chat_id: chatId, text: resultMessage })
                    }).then(() => {
                        console.log("Результат игры отправлен в канал.");
                    }).catch(err => console.error("Ошибка отправки результата игры:", err));
                }).catch(err => console.error("Ошибка отправки кубиков:", err));
            }, 5000); // Задержка в 5 секунд перед отправкой результата
        }
    </script>

</body>
</html>
