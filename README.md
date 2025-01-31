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

        <button id="placeBetButton">✅ Сделать ставку</button>
    </div>

    <script>
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U"; // Укажите токен бота
        const chatId = "1002348053681"; // Укажите ID канала или группы

        // Функция для открытия ссылки и подтверждения пополнения счета
        function openLinkAndPlaceBet() {
            if (!window.Telegram || !Telegram.WebApp) {
                alert("Открывайте WebApp через Telegram!");
                return;
            }

            const game = document.getElementById("game").value;
            const betAmount = parseFloat(document.getElementById("bet_amount").value);
            const outcome = document.getElementById("outcome").value;

            // Проверяем, указана ли ставка, и минимальное значение
            if (isNaN(betAmount) || betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20$. Введите корректное значение.");
                return;
            }

            // Открытие ссылки для пополнения счета через Cryptobot
            const cryptoLink = "http://t.me/send?start=IVyytgNj3snE";
            window.location.href = cryptoLink;

            // Здесь можно добавить логику для отслеживания пополнения счета (например, через API)

            // Предполагаем, что после успешного пополнения можно отправить ставку
            alert("Перенаправляем вас для пополнения счета...");
        }

        // Функция для отправки ставки в Telegram
        function placeBet(game, outcome, betAmount) {
            const username = Telegram.WebApp.initDataUnsafe?.user?.username || "Аноним";

            // Отправка информации о ставке
            let message = `[🎉 Ваша ставка принята]

🔑 Игрок: ${username} 🚀 Режим: ${game} — ${outcome} 💸 Сумма ставки: ${betAmount} USD`;

            fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ chat_id: chatId, text: message })
            }).then(() => {
                console.log("Ставка отправлена, начинаем игру...");
                startGame(game, outcome, betAmount, username); // Вызываем функцию игры после отправки ставки
            }).catch(err => {
                console.error("Ошибка отправки ставки:", err);
            });
        }

        // Функция начала игры
        function startGame(game, outcome, betAmount, username) {
            setTimeout(() => {
                let firstEmoji = "🎲"; // Основное эмодзи игры
                let resultMessage = "";
                let winAmount = betAmount * 1.5; // Коэффициент выигрыша 1.5x

                if (game === "🎳 Боулинг") {
                    firstEmoji = "🎳";
                    resultMessage = Math.random() > 0.5 ? `🎉 Поздравляем, вы выиграли! Ваш выигрыш: ${winAmount.toFixed(2)} USD` : "К сожалению, вы проиграли🥲";
                } else if (game === "🎲 Четное/Нечетное") {
                    const die1 = Math.floor(Math.random() * 6) + 1;
                    const die2 = Math.floor(Math.random() * 6) + 1;
                    fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                        method: "POST",
                        headers: { "Content-Type": "application/json" },
                        body: JSON.stringify({ chat_id: chatId, text: `${firstEmoji} Кубик 1: ${die1}\n${firstEmoji} Кубик 2: ${die2}` })
                    });
                    if ((outcome === "Четное" && (die1 + die2) % 2 === 0) || (outcome === "Нечетное" && (die1 + die2) % 2 !== 0)) {
                        resultMessage = `🎉 Поздравляем, вы выиграли! Ваш выигрыш: ${winAmount.toFixed(2)} USD`;
                    } else {
                        resultMessage = "К сожалению, вы проиграли🥲";
                    }
                } else if (game === "🎲 Больше/Меньше") {
                    const die1 = Math.floor(Math.random() * 6) + 1;
                    fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                        method: "POST",
                        headers: { "Content-Type": "application/json" },
                        body: JSON.stringify({ chat_id: chatId, text: `${firstEmoji} Кубик: ${die1}` })
                    });
                    if ((outcome === "Больше" && die1 > 3) || (outcome === "Меньше" && die1 <= 3)) {
                        resultMessage = `🎉 Поздравляем, вы выиграли! Ваш выигрыш: ${winAmount.toFixed(2)} USD`;
                    } else {
                        resultMessage = "К сожалению, вы проиграли🥲";
                    }
                }

                // Отправка результата
                fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ chat_id: chatId, text: resultMessage })
                }).catch(err => console.error("Ошибка отправки результата:", err));
            }, 5000); // Задержка в 5 секунд
        }

        // Слушатель для кнопки
        document.getElementById("placeBetButton").addEventListener("click", openLinkAndPlaceBet);
    </script>
</body>
</html>
