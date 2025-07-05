
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Real Python Terminal</title>
    <style>
        body {
            margin: 0;
            padding: 10px;
            background-color: #000;
            color: #0f0;
            font-family: 'Courier New', Courier, monospace;
        }
        #terminal {
            height: 80vh;
            overflow-y: auto;
            padding: 10px;
        }
        #output {
            white-space: pre-wrap;
        }
        #input {
            width: 90%;
            background: #000;
            color: #0f0;
            border: none;
            outline: none;
            font-family: 'Courier New', Courier, monospace;
        }
    </style>
</head>
<body>
    <div id="terminal">
        <div id="output">
            Welcome to Real Python Terminal!<br>
            Use 'pip install <package>' to install packages.<br>
            Type Python code and press Enter to run.<br>
            $
        </div>
        <input type="text" id="input" autofocus>
    </div>

    <script>
        const output = document.getElementById('output');
        const input = document.getElementById('input');

        input.addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                const command = input.value.trim();
                output.innerHTML += `<br>$ ${command}`;
                processCommand(command);
                input.value = '';
                output.scrollTop = output.scrollHeight;
            }
        });

        function processCommand(command) {
            if (command.startsWith('pip install ')) {
                fetch('/install', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ package: command.split(' ')[2] })
                })
                .then(response => response.json())
                .then(data => output.innerHTML += `<br>${data.output}`);
            } else {
                fetch('/run', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ code: command })
                })
                .then(response => response.json())
                .then(data => output.innerHTML += `<br>${data.output}`);
            }
        }
    </script>
</body>
</html>