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

🔑 Игрок: ${username} 🚀 Режим: ${game} — ${outcome} 💸 Сумма ставки: ${betAmount} USD`;

            let token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U";
            let chatId = "-1002348053681";

            // Отправляем сообщение о ставке
            fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ chat_id: chatId, text: message })
            }).then(response => {
                if (response.ok) {
                    console.log("Ставка успешно отправлена в канал.");

                    // После ставки запускаем игру
                    startGame(game, outcome, betAmount, chatId, token, username);
                } else {
                    console.error("Ошибка при отправке ставки в канал:", response.statusText);
                }
            }).catch(err => console.error("Ошибка отправки запроса:", err));
        }

        function startGame(game, outcome, betAmount, chatId, token, username) {
            console.log("Запуск игры...");

            setTimeout(() => {
                let emojiResult = "";
                let win = false;

                // Эмодзи для разных игр
                if (game === "🎳 Боулинг") {
                    emojiResult = Math.random() < 0.5 ? "🎳 Strike!" : "🎳 Miss!";
                    win = emojiResult === "🎳 Strike!";
                } else if (game === "🎲 Четное/Нечетное") {
                    let firstDie = Math.floor(Math.random() * 6) + 1;
                    let secondDie = Math.floor(Math.random() * 6) + 1;
                    emojiResult = `🎲 Кубик 1: ${firstDie}\n🎲 Кубик 2: ${secondDie}`;
                    if (outcome === "Четное") win = (firstDie + secondDie) % 2 === 0;
                    else win = (firstDie + secondDie) % 2 !== 0;
                } else if (game === "🎲 Больше/Меньше") {
                    let die = Math.floor(Math.random() * 6) + 1;
                    emojiResult = `🎲 Кубик: ${die}`;
                    win = (outcome === "Больше" && die > 3) || (outcome === "Меньше" && die <= 3);
                } else if (game === "⚽ Футбол" || game === "🏀 Баскетбол") {
                    emojiResult = Math.random() < 0.5 ? "Гол!" : "Промах!";
                    win = emojiResult === "Гол!";
                }

                // Отправляем Emoji в канал
                fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ chat_id: chatId, text: emojiResult })
                }).then(() => {
                    console.log("Эмодзи отправлены в канал.");

                    // Отправляем результат в канал
                    let resultMessage = win
                        ? `🎉 Поздравляем, вы выиграли ${betAmount * 2} USD!\n🚀 Ваш выигрыш будет зачислен в канале через CryptoBot!\n🔥 Удачи в следующих ставках!`
                        : `ID пользователя ${username}, К сожалению, вы проиграли🥲\n🔥 Удачи в следующих ставках!`;

                    fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                        method: "POST",
                        headers: { "Content-Type": "application/json" },
                        body: JSON.stringify({ chat_id: chatId, text: resultMessage })
                    }).then(() => {
                        console.log("Результат игры отправлен в канал.");
                    }).catch(err => console.error("Ошибка отправки результата игры:", err));
                }).catch(err => console.error("Ошибка отправки эмодзи:", err));
            }, 5000); // Задержка в 5 секунд
        }
    </script>
</body>
</html>
