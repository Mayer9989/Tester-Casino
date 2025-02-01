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
        const token = "7480442854:AAEs_EILlE85qomG5-hW6rZ9bvISLqaXm4U"; // Ваш API токен
        const chatId = "-1002348053681"; // Ваш ID канала

        // Функция отправки сообщения в Telegram
        async function sendMessage(text) {
            try {
                const response = await fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({
                        chat_id: chatId,  // Ваш chat_id канала
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

            sendMessage(betMessage); // Отправка сообщения о принятии ставки

            // Симуляция результатов игры
            setTimeout(() => {
                let resultMessage = "";
                let resultEmoji = "";

                // Для игры "Четное/Нечетное"
                if (game === "🎲 Четное/Нечетное") {
                    const diceResult = Math.floor(Math.random() * 2); // 0 — четное, 1 — нечетное
                    resultEmoji = diceResult === 0 ? "🔵" : "🟠"; // Отправляем эмодзи для четного/нечетного
                    resultMessage = `🎲 Результат игры: ${resultEmoji}`;
                } 
                // Для игры "Больше/Меньше"
                else if (game === "🔢 Больше/Меньше") {
                    const dice1 = Math.floor(Math.random() * 6) + 1;
                    const dice2 = Math.floor(Math.random() * 6) + 1;
                    resultEmoji = dice1 > dice2 ? "⬆️" : "⬇️"; // Эмодзи для больше/меньше
                    resultMessage = `🎲 Результат игры: ${dice1} ${dice2} ${resultEmoji}`;
                } 
                // Для игр "Футбол", "Баскетбол", "Боулинг"
                else if (game === "⚽ Футбол" || game === "🏀 Баскетбол" || game === "🎳 Боулинг") {
                    const randomOutcome = Math.random() > 0.5 ? "Гол" : "Промах";
                    resultEmoji = randomOutcome === "Гол" ? "⚽" : "❌"; // Гол или Промах
                    resultMessage = `🎯 Результат игры: ${resultEmoji}`;
                }

                // Отправка результата эмодзи
                sendMessage(resultMessage); // Отправка результата в канал

                // Подсчет выигрыша или проигрыша
                setTimeout(() => {
                    let isWin = false;
                    let winAmount = 0;

                    // Определение выигрыша по эмодзи
                    if (game === "🎲 Четное/Нечетное") {
                        isWin = (outcome === "Четное" && resultEmoji === "🔵") || 
                                (outcome === "Нечетное" && resultEmoji === "🟠");
                    } else if (game === "🔢 Больше/Меньше") {
                        const isBigger = resultEmoji === "⬆️";
                        isWin = (outcome === "Больше" && isBigger) || 
                                (outcome === "Меньше" && !isBigger);
                    } else if (game === "⚽ Футбол" || game === "🏀 Баскетбол" || game === "🎳 Боулинг") {
                        isWin = (outcome === "Гол" && resultEmoji === "⚽") || 
                                (outcome === "Промах" && resultEmoji === "❌");
                    }

                    // Выводим итог
                    resultMessage = isWin ? `🎉 Поздравляем, вы выиграли!` : `❌ Вы проиграли!`;
                    sendMessage(resultMessage); // Отправка итога
                }, 1000); // Завершаем результат через 1 секунду

            }, 3000); // Завершаем начальный процесс через 3 секунды
        }

        document.getElementById("placeBetBtn").addEventListener("click", placeBet);
    </script>
</body>
</html>
