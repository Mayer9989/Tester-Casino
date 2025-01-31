<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Tester Casino - WebApp</title>
    <style>
        /* Общие стили */
        body, html {
            height: 100%;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            background-color: black; /* Полностью черный фон */
            font-family: Arial, sans-serif;
            color: white;
            overflow: hidden; /* Отключаем прокрутку */
        }
        
        .container {
            background: rgba(0, 0, 0, 0.8); /* Черный полупрозрачный фон */
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

        <button onclick="placeBet()">✅ Сделать ставку</button>
    </div>

    <script>
        function placeBet() {
            let game = document.getElementById("game").value;
            let betAmount = parseFloat(document.getElementById("bet_amount").value);

            if (betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20$");
                return;
            }

            let username = "Аноним"; // Замените на получение имени пользователя из Telegram WebApp API
            let token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U";  // Замените на свой токен бота
            let chatId = "-1002348053681"; // Замените на ID вашего канала (с минусом!)
            let paymentUrl = `https://t.me/CryptoBot?start=IVyytgNj3snE&amount=${betAmount}`;

            let message = `🔑 Игрок: ${username}\n🚀 Режим: ${game}\n💸 Сумма ставки: ${betAmount} USD`;

            // Отправка сообщения в канал
            fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ chat_id: chatId, text: message })
            });

            // Открытие ссылки на оплату
            window.location.href = paymentUrl;

            // Имитация результата игры через 5 секунд
            setTimeout(() => {
                let resultMessage = "";
                let win = Math.random() < 0.5; // 50% шанс на победу
                let emoji = "";

                if (game === "🎳 Боулинг") emoji = "🎳";
                if (game === "🎲 Четное/Нечетное" || game === "🎲 Больше/Меньше") emoji = "🎲";
                if (game === "⚽ Футбол") emoji = "⚽";
                if (game === "🏀 Баскетбол") emoji = "🏀";

                resultMessage = `🔑 Игрок: ${username}\n🚀 Режим: ${game} — ${win ? "Выигрыш" : "Поражение"}\n💸 Сумма ставки: ${betAmount} USD`;

                // Отправка результата в канал
                fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ chat_id: chatId, text: resultMessage })
                });

                // Если выиграл, отправляем сообщение о выигрыше
                if (win) {
                    let prize = (betAmount * 1.5).toFixed(2);
                    let prizeInRub = (prize * 75).toFixed(2);

                    let winMessage = `🎉 Поздравляем, вы выиграли ${prize} USD (${prizeInRub} RUB)!\n🚀 Ваш выигрыш будет в чеке, вам его скинут в лс!\n🔥 Удачи в следующих ставках!`;

                    fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                        method: "POST",
                        headers: { "Content-Type": "application/json" },
                        body: JSON.stringify({ chat_id: chatId, text: winMessage })
                    });
                }
            }, 5000);
        }
    </script>

</body>
</html>
