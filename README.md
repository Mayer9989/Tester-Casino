<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no">
    <title>Tester Casino - WebApp</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        body, html { height: 100%; margin: 0; display: flex; justify-content: center; align-items: center; background-color: black; font-family: Arial, sans-serif; color: white; }
        .container { background: rgba(0, 0, 0, 0.8); padding: 20px; border-radius: 10px; width: 400px; text-align: center; }
        h2 { font-size: 24px; margin-bottom: 20px; }
        select, input, button { display: block; width: 100%; margin: 10px auto; padding: 10px; font-size: 16px; border-radius: 5px; }
        button { background-color: #28a745; color: white; border: none; cursor: pointer; }
        button:hover { background-color: #218838; }
    </style>
</head>
<body>
    <div class="container">
        <h2>🎰 Выберите игру</h2>
        <select id="game">
            <option value="🎲 Четное/Нечетное">🎲 Четное/Нечетное</option>
            <option value="🎲 Больше/Меньше">🎲 Больше/Меньше</option>
        </select>

        <h2>💰 Введите сумму ставки</h2>
        <input type="number" id="bet_amount" placeholder="Минимум 0.20$" step="0.01" min="0.20">

        <h2>🔮 Выберите исход игры</h2>
        <select id="outcome">
            <option value="Четное">Четное</option>
            <option value="Нечетное">Нечетное</option>
            <option value="Больше">Больше (от 4 до 6)</option>
            <option value="Меньше">Меньше (от 1 до 3)</option>
        </select>

        <button id="placeBetBtn">✅ Сделать ставку</button>
    </div>

    <script>
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U"; // Токен бота
        const chatId = "-1002348053681"; // ID канала
        const webAppUrl = "https://t.me/YourBot?start=casino"; // Ссылка на WebApp

        function placeBet() {
            const game = document.getElementById("game").value;
            const betAmount = parseFloat(document.getElementById("bet_amount").value);
            const outcome = document.getElementById("outcome").value;

            if (isNaN(betAmount) || betAmount < 0.20) {
                alert("❌ Минимальная ставка — 0.20$. Введите корректное значение.");
                return;
            }

            let username = "Аноним";
            if (window.Telegram?.WebApp?.initDataUnsafe?.user) {
                username = Telegram.WebApp.initDataUnsafe.user.username || `ID ${Telegram.WebApp.initDataUnsafe.user.id}`;
            }

            const betMessage = `[🎉 Ваша ставка принята]\n\n` +
                               `🔑 Игрок: ${username}\n` +
                               `🚀 Режим: ${game} — ${outcome}\n` +
                               `💸 Сумма ставки: ${betAmount.toFixed(2)} USD [${(betAmount * 90).toFixed(2)} RUB]`;

            fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify({ chat_id: chatId, text: betMessage, parse_mode: "HTML" })
            });

            setTimeout(() => {
                const rolledNumber1 = Math.floor(Math.random() * 6) + 1;
                fetch(`https://api.telegram.org/bot${token}/sendDice`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({ chat_id: chatId, emoji: "🎲" })
                });

                setTimeout(() => {
                    let rolledNumber2 = rolledNumber1;
                    if (game.includes("Больше/Меньше")) {
                        rolledNumber2 = Math.floor(Math.random() * 6) + 1;
                        fetch(`https://api.telegram.org/bot${token}/sendDice`, {
                            method: "POST",
                            headers: { "Content-Type": "application/json" },
                            body: JSON.stringify({ chat_id: chatId, emoji: "🎲" })
                        });
                    }

                    setTimeout(() => {
                        let isWin = false;
                        if (game.includes("Четное/Нечетное")) {
                            isWin = (outcome === "Четное" && rolledNumber1 % 2 === 0) || (outcome === "Нечетное" && rolledNumber1 % 2 !== 0);
                        } else if (game.includes("Больше/Меньше")) {
                            isWin = (outcome === "Больше" && rolledNumber2 >= rolledNumber1) || (outcome === "Меньше" && rolledNumber2 < rolledNumber1);
                        }

                        let resultMessage;
                        let buttonText;
                        if (isWin) {
                            const winAmount = betAmount * 1.5;
                            resultMessage = `🎉 Поздравляем, вы выиграли!\n\n` +
                                            `💰 Выигрыш: ${winAmount.toFixed(2)} USD [${(winAmount * 90).toFixed(2)} RUB]\n\n` +
                                            `🚀 Ваш выигрыш в чеке, активируйте его!\n🔥 Удачи в следующих ставках!`;
                            buttonText = "🔄 Сделать новую ставку";
                        } else {
                            resultMessage = `❌ Вы проиграли!🥲\n🔥 Удачи в следующих ставках!`;
                            buttonText = "🔄 Попробовать снова";
                        }

                        fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                            method: "POST",
                            headers: { "Content-Type": "application/json" },
                            body: JSON.stringify({
                                chat_id: chatId,
                                text: resultMessage,
                                parse_mode: "HTML",
                                reply_markup: {
                                    inline_keyboard: [[{ text: buttonText, web_app: { url: webAppUrl } }]]
                                }
                            })
                        });

                    }, 1000);

                }, 5000);

            }, 1000);
        }

        document.getElementById("placeBetBtn").addEventListener("click", placeBet);
    </script>
</body>
</html>
