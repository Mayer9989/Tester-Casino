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
            max-width: 90%; /* Ограничение ширины */
            width: 400px; /* Фиксированная ширина */
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

        /* Ограничиваем прокрутку для WebApp */
        body, html {
            overflow: hidden; /* Отключаем прокрутку */
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
            let betAmount = document.getElementById("bet_amount").value;

            if (betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20$");
                return;
            }

            // Обновленная ссылка на оплату через CryptoBot
            let paymentUrl = `http://t.me/send?start=IVyytgNj3snE&amount=${betAmount}`;

            // Отправляем данные в Telegram-бота
            let data = `${game} | ${betAmount}`;
            Telegram.WebApp.sendData(data);

            // Отправляем ставку в канал через Telegram Bot API
            let token = '7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U';  // Замените на ваш Token
            let chatId = '1002348053681';  // Замените на ваш Chat ID или @username канала
            let username = Telegram.WebApp.initDataUnsafe.user.username || 'Аноним';
            let message = `🔑 Игрок: ${username}\n🚀 Режим: ${game}\n💸 Сумма ставки: ${betAmount} USD`;

            fetch(`https://api.telegram.org/bot${token}/sendMessage?chat_id=${chatId}&text=${encodeURIComponent(message)}`);

            // Закрываем WebApp
            Telegram.WebApp.close();

            // Открываем ссылку на оплату
            window.open(paymentUrl, "_blank");

            // Имитация результата игры и отправка результата в канал
            setTimeout(() => {
                let resultMessage = "";
                let emoji = "";
                let win = false;

                // Логика для разных игр
                if (game === "🎳 Боулинг") {
                    emoji = "🎳";
                    // Имитируем сбитие кеглей (например, рандомный результат)
                    let pins = Math.random() < 0.5 ? "Поражение" : "Выигрыш";
                    resultMessage = `🔑 Игрок: ${username}\n🚀 Режим: Боулинг — ${pins}\n💸 Сумма ставки: ${betAmount} USD`;
                    win = pins === "Выигрыш";
                } else if (game === "🎲 Четное/Нечетное" || game === "🎲 Больше/Меньше") {
                    emoji = "🎲";
                    // Имитируем результат
                    let playerGuess = Math.random() < 0.5 ? "Больше" : "Меньше";
                    let result = Math.random() < 0.5 ? "Меньше" : "Больше";
                    let win = playerGuess === result;
                    resultMessage = `🔑 Игрок: ${username}\n🚀 Режим: ${game} — ${win ? 'Выигрыш' : 'Поражение'}\n💸 Сумма ставки: ${betAmount} USD`;
                } else if (game === "⚽ Футбол") {
                    emoji = "⚽";
                    // Имитируем результат игры
                    let winGoal = Math.random() < 0.5;
                    resultMessage = `🔑 Игрок: ${username}\n🚀 Режим: Футбол — ${winGoal ? 'Выигрыш' : 'Поражение'}\n💸 Сумма ставки: ${betAmount} USD`;
                    win = winGoal;
                } else if (game === "🏀 Баскетбол") {
                    emoji = "🏀";
                    // Имитируем попадание в кольцо
                    let winShot = Math.random() < 0.5;
                    resultMessage = `🔑 Игрок: ${username}\n🚀 Режим: Баскетбол — ${winShot ? 'Выигрыш' : 'Поражение'}\n💸 Сумма ставки: ${betAmount} USD`;
                    win = winShot;
                }

                // Отправляем результат игры в канал
                fetch(`https://api.telegram.org/bot${token}/sendMessage?chat_id=${chatId}&text=${encodeURIComponent(resultMessage)}`);

                // Если выигрыш
                if (win) {
                    let prize = (betAmount * 1.5).toFixed(2); // Коефициент 1.5x
                    let prizeInRub = (prize * 75).toFixed(2); // Примерный курс рубля

                    let winMessage = `🎉 Поздравляем, вы выиграли ${prize} USD (${prizeInRub} RUB)!\n🚀 Ваш выигрыш будет в чеке, вам его скинут в лс!🔥 Удачи в следующих ставках!`;
                    fetch(`https://api.telegram.org/bot${token}/sendMessage?chat_id=${chatId}&text=${encodeURIComponent(winMessage)}`);
                }
            }, 5000); // Задержка для имитации завершения игры (5 секунд)
        }
    </script>

</body>
</html>
