<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Телеграм-Бот Ставки</title>
    <style>
        /* Стили как раньше */
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f9;
            margin: 0;
            padding: 0;
        }
        .container {
            width: 100%;
            max-width: 500px;
            margin: 50px auto;
            background: white;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            padding: 20px;
        }
        h1 {
            text-align: center;
            color: #333;
        }
        .section {
            margin-bottom: 30px;
        }
        input[type="number"], input[type="text"] {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border-radius: 5px;
            border: 1px solid #ddd;
            font-size: 16px;
        }
        select {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border-radius: 5px;
            border: 1px solid #ddd;
            font-size: 16px;
        }
        button {
            width: 100%;
            padding: 15px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            font-size: 18px;
            cursor: pointer;
        }
        button:hover {
            background-color: #45a049;
        }
        .message {
            text-align: center;
            margin-top: 20px;
            font-size: 18px;
            color: #f44336;
        }
        .balance {
            text-align: center;
            font-size: 20px;
            margin: 10px;
        }
    </style>
</head>
<body>

    <div class="container">
        <h1>Ставки с Telegram-ботом</h1>

        <!-- Проверка баланса -->
        <div class="section">
            <h2>Проверить баланс</h2>
            <form id="balanceForm">
                <input type="text" id="user_id" placeholder="Введите ваш ID в Telegram" required>
                <button type="submit">Проверить баланс</button>
            </form>
            <div class="balance" id="balanceMessage"></div>
        </div>

        <!-- Сделать ставку -->
        <div class="section">
            <h2>Сделать ставку</h2>
            <form id="betForm">
                <input type="text" id="username" placeholder="Введите ваше имя пользователя" required>
                <select id="game" required>
                    <option value="dart">Дартс</option>
                    <option value="bowling">Боулинг</option>
                    <option value="higher_lower">Больше/Меньше</option>
                    <option value="even_odd">Четное/Нечетное</option>
                    <option value="blackjack">Блек Джек</option>
                    <option value="football">Футбол</option>
                    <option value="basketball">Баскетбол</option>
                </select>
                <input type="number" id="bet_amount" placeholder="Сумма ставки" required min="1">
                <div id="outcomeContainer"></div>
                <button type="submit">Сделать ставку</button>
            </form>
            <div class="message" id="betMessage"></div>
        </div>
    </div>

    <script>
        // Функция для обновления исходов в зависимости от выбранной игры
        document.getElementById('game').addEventListener('change', function () {
            const game = this.value;
            const outcomeContainer = document.getElementById('outcomeContainer');
            outcomeContainer.innerHTML = ''; // Очистить предыдущие исходы

            if (game === 'dart') {
                outcomeContainer.innerHTML = `
                    <select id="outcome" required>
                        <option value="win">Победа</option>
                        <option value="lose">Проигрыш</option>
                    </select>
                `;
            } else if (game === 'bowling') {
                outcomeContainer.innerHTML = `
                    <select id="outcome" required>
                        <option value="strike">Страйк</option>
                        <option value="spare">Спэр</option>
                        <option value="miss">Промах</option>
                    </select>
                `;
            } else if (game === 'higher_lower') {
                outcomeContainer.innerHTML = `
                    <select id="outcome" required>
                        <option value="higher">Больше</option>
                        <option value="lower">Меньше</option>
                    </select>
                `;
            } else if (game === 'even_odd') {
                outcomeContainer.innerHTML = `
                    <select id="outcome" required>
                        <option value="even">Четное</option>
                        <option value="odd">Нечетное</option>
                    </select>
                `;
            } else if (game === 'blackjack') {
                outcomeContainer.innerHTML = `
                    <select id="outcome" required>
                        <option value="win">Победа</option>
                        <option value="lose">Проигрыш</option>
                    </select>
                `;
            } else if (game === 'football') {
                outcomeContainer.innerHTML = `
                    <select id="outcome" required>
                        <option value="win">Победа</option>
                        <option value="draw">Ничья</option>
                        <option value="lose">Проигрыш</option>
                    </select>
                `;
            } else if (game === 'basketball') {
                outcomeContainer.innerHTML = `
                    <select id="outcome" required>
                        <option value="win">Победа</option>
                        <option value="lose">Проигрыш</option>
                    </select>
                `;
            }
        });

        // Сделать ставку
        document.getElementById('betForm').addEventListener('submit', function (e) {
            e.preventDefault();
            const userId = document.getElementById('username').value;
            const game = document.getElementById('game').value;
            const betAmount = parseFloat(document.getElementById('bet_amount').value);
            const outcome = document.getElementById('outcome').value;

            const betData = {
                user_id: userId,
                game: game,
                bet_amount: betAmount,
                outcome: outcome
            };

            fetch('http://127.0.0.1:5000/bet', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(betData)
            })
            .then(response => response.json())
            .then(data => {
                if (data.status === 'ok') {
                    document.getElementById('betMessage').textContent = 'Ваша ставка принята!';
                } else {
                    document.getElementById('betMessage').textContent = 'Ошибка: ' + data.message;
                }
            })
            .catch(error => {
                document.getElementById('betMessage').textContent = 'Ошибка при отправке данных.';
            });
        });
    </script>
</body>
</html>
