<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no">
    <title>Tester Casino - WebApp</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        body, html {
            height: 100%;
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            background-color: black;
            background-image: url('ak47_white.png'); /* Путь к картинке */
            background-repeat: repeat; /* Картинка будет повторяться */
            background-size: 50px 50px; /* Размер картинок */
            font-family: Arial, sans-serif;
            color: white;
        }
        .container {
            background: rgba(0, 0, 0, 0.8);
            padding: 20px;
            border-radius: 10px;
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
            <option value="🎲 Четное/Нечетное">🎲 Четное/Нечетное</option>
            <option value="⚽ Футбол">⚽ Футбол</option>
            <option value="🏀 Баскетбол">🏀 Баскетбол</option>
            <option value="🎳 Боулинг">🎳 Боулинг</option>
            <option value="🔢 Больше/Меньше">🔢 Больше/Меньше</option>
        </select>
        
        <h2>💰 Введите сумму ставки</h2>
        <input type="number" id="bet_amount" placeholder="Минимум 0.20$" step="0.01" min="0.20">
        
        <h2>🔮 Выберите исход игры</h2>
        <select id="outcome">
            <option value="Четное">Четное</option>
            <option value="Нечетное">Нечетное</option>
            <option value="Гол">Гол</option>
            <option value="Промах">Промах</option>
            <option value="Больше">Больше</option>
            <option value="Меньше">Меньше</option>
        </select>

        <button id="placeBetBtn">✅ Сделать ставку</button>
    </div>

    <script>
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U"; 
        const chatId = "-1002348053681"; // Префикс для канала, укажите свой правильный chat_id

        // Функция отправки сообщения в Telegram
        async function sendMessage(text) {
            try {
                const response = await fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({
                        chat_id: chatId,
                        text: text,
                        parse_mode: "HTML"
                    })
                });

                const responseData = await response.json(); // Получаем ответ от Telegram API
                if (response.ok) {
                    console.log('Сообщение отправлено успешно!', responseData);
                } else {
                    console.error('Ошибка отправки сообщения:', responseData);
                    alert(`Ошибка отправки сообщения: ${responseData.description}`);
                }
            } catch (error) {
                console.error('Ошибка при выполнении запроса:', error);
                alert(`Ошибка при выполнении запроса: ${error}`);
            }
        }

        // Функция обработки ставки
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
                               `💸 Сумма ставки: ${betAmount.toFixed(2)} USD`;

            sendMessage(betMessage);

            // Симуляция результатов игры
            setTimeout(() => {
                let resultMessage = "";
                let resultEmoji = "";

                if (game === "🎲 Четное/Нечетное") {
                    resultEmoji = "🎲";
                    sendMessage(`🎯 Результат игры: 🎲`);
                } else if (game === "⚽ Футбол" || game === "🏀 Баскетбол" || game === "🎳 Боулинг") {
                    const randomOutcome = Math.random() > 0.5 ? "Гол" : "Промах";
                    resultEmoji = randomOutcome === "Гол" ? "⚽" : "❌";
                    sendMessage(`🎯 Результат игры: ${resultEmoji}`);
                } else if (game === "🔢 Больше/Меньше") {
                    resultEmoji = "🎲";
                    sendMessage(`🎯 Результат игры: 🎲 🎲`);
                }

                // Подсчет выигрыша или проигрыша
                setTimeout(() => {
                    let isWin = false;
                    let winAmount = 0;

                    if (game === "🎲 Четное/Нечетное") {
                        const diceResult = Math.floor(Math.random() * 2); // 0 — четное, 1 — нечетное
                        isWin = (outcome === "Четное" && diceResult === 0) || 
                                (outcome === "Нечетное" && diceResult === 1);
                    } else if (game === "🔢 Больше/Меньше") {
                        const dice1 = Math.floor(Math.random() * 6) + 1;
                        const dice2 = Math.floor(Math.random() * 6) + 1;
                        const isBigger = dice1 > dice2;

                        isWin = (outcome === "Больше" && isBigger) || (outcome === "Меньше" && !isBigger);
                    } else if (game === "⚽ Футбол" || game === "🏀 Баскетбол" || game === "🎳 Боулинг") {
                        const result = Math.random() > 0.5 ? "Гол" : "Промах";
                        isWin = (outcome === "Гол" && result === "Гол") || 
                                (outcome === "Промах" && result === "Промах");
                    }

                    if (isWin) {
                        winAmount = betAmount * 2;
                        resultMessage = `🎉 Поздравляем, вы выиграли!\n\n` +
                                        `💰 Выигрыш: ${winAmount.toFixed(2)} USD\n🔥 Удачи в следующих ставках!`;
                    } else {
                        resultMessage = `❌ Вы проиграли!🥲\n🔥 Удачи в следующих ставках!`;
                    }

                    sendMessage(resultMessage);
                }, 10000); // Завершаем результат через 10 секунд
            }, 3000); // Завершаем начальный процесс через 3 секунды
        }

        document.getElementById("placeBetBtn").addEventListener("click", placeBet);
    </script>
</body>
</html>
