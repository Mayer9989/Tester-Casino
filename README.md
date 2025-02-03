<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ставки через Telegram</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            padding: 20px;
        }
        .btn {
            padding: 10px 20px;
            margin: 10px;
            background-color: #4CAF50;
            color: white;
            text-decoration: none;
            border-radius: 5px;
        }
        .btn:hover {
            background-color: #45a049;
        }
        .bet-container {
            margin-top: 30px;
        }
        .bet-option {
            padding: 10px 20px;
            margin: 5px;
            background-color: #008CBA;
            color: white;
            border-radius: 5px;
            cursor: pointer;
        }
        .bet-option:hover {
            background-color: #007B9F;
        }
    </style>
</head>
<body>

    <h1>Добро пожаловать в игру!</h1>
    <p>Выберите сумму для ставки:</p>
    
    <div class="bet-container">
        <div class="bet-option" onclick="createInvoice('0.1')">0.1 USDT</div>
        <div class="bet-option" onclick="createInvoice('0.5')">0.5 USDT</div>
        <div class="bet-option" onclick="createInvoice('1')">1 USDT</div>
        <div class="bet-option" onclick="createInvoice('5')">5 USDT</div>
    </div>

    <div id="payment-link-container" style="display:none;">
        <p>Перейдите по следующей ссылке для оплаты:</p>
        <a id="payment-link" class="btn" target="_blank">Оплатить</a>
    </div>

    <script>
        function createInvoice(amount) {
            // Здесь должна быть отправка запроса на сервер для создания инвойса
            fetch(`https://your-server.com/create-invoice?amount=${amount}`)
                .then(response => response.json())
                .then(data => {
                    if (data.success) {
                        document.getElementById('payment-link-container').style.display = 'block';
                        document.getElementById('payment-link').href = data.pay_link;
                    } else {
                        alert('Ошибка при создании инвойса');
                    }
                })
                .catch(error => {
                    console.error('Ошибка:', error);
                    alert('Ошибка при создании инвойса');
                });
        }
    </script>

</body>
</html>
