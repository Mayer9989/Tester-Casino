<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Tester Casino - WebApp</title>
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
            max-width: 90%;
            width: 400px;
            text-align: center;
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
            if (typeof Telegram === "undefined" || !Telegram.WebApp) {
                alert("Открывайте WebApp через Telegram!");
                return;
            }

            let game = document.getElementById("game").value;
            let betAmount = parseFloat(document.getElementById("bet_amount").value);

            if (betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20$");
                return;
            }

            let paymentUrl = `http://t.me/send?start=IVyytgNj3snE&amount=${betAmount}`;
            let username = Telegram.WebApp.initDataUnsafe?.user?.username || 'Аноним';
            let token = '7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U';
            let chatId = '1002348053681';

            let message = `🔑 Игрок: ${username}\n🚀 Режим: ${game}\n💸 Сумма ставки: ${betAmount} USD`;

            fetch(`https://api.telegram.org/bot${token}/sendMessage?chat_id=${chatId}&text=${encodeURIComponent(message)}`)
                .then(() => {
                    Telegram.WebApp.close();
                    location.href = paymentUrl;
                })
                .catch(err => console.error("Ошибка при отправке в канал:", err));

            setTimeout(() => {
                let win = Math.random() < 0.5;
                let resultMessage = `🔑 Игрок: ${username}\n🚀 Режим: ${game} — ${win ? 'Выигрыш' : 'Поражение'}\n💸 Сумма ставки: ${betAmount} USD`;

                fetch(`https://api.telegram.org/bot${token}/sendMessage?chat_id=${chatId}&text=${encodeURIComponent(resultMessage)}`);

                if (win) {
                    let prize = (betAmount * 1.5).toFixed(2);
                    let prizeInRub = (prize * 75).toFixed(2);
                    let winMessage = `🎉 Поздравляем, вы выиграли ${prize} USD (${prizeInRub} RUB)!\n🚀 Ваш выигрыш будет в чеке, вам его скинут в лс!🔥 Удачи в следующих ставках!`;

                    fetch(`https://api.telegram.org/bot${token}/sendMessage?chat_id=${chatId}&text=${encodeURIComponent(winMessage)}`);
                }
            }, 5000);
        }
    </script>

</body>
</html>
