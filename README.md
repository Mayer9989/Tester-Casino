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
            background: url('https://i.imgur.com/mIKMiRM.jpg'); /* Ссылка на картину с Микки Маусом */
            background-size: cover;
            background-position: center;
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

            // Создаем ссылку на оплату через CryptoBot
            let paymentUrl = `http://t.me/send?start=IVAV0gxMA81Y&amount=${betAmount}`;

            // Отправляем данные в Telegram-бота
            let data = `${game} | ${betAmount}`;
            Telegram.WebApp.sendData(data);

            // Закрываем WebApp
            Telegram.WebApp.close();

            // Открываем ссылку на оплату
            window.open(paymentUrl, "_blank");
        }
    </script>

</body>
</html>