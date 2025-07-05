<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Python Development Environment</title>
    <style>
        body {
            font-family: 'Courier New', Courier, monospace;
            background-color: #000;
            color: #0f0;
            margin: 0;
            padding: 10px;
        }
        #terminal {
            width: 100%;
            height: 80vh;
            overflow-y: auto;
            border: 1px solid #0f0;
            padding: 10px;
        }
        #input {
            width: 90%;
            background: #000;
            color: #0f0;
            border: none;
            outline: none;
        }
        #output {
            white-space: pre-wrap;
        }
    </style>
</head>
<body>
    <div id="terminal">
        <div id="output">
            Welcome to Python Dev Environment!<br>
            Type 'install <package>' to simulate module installation.<br>
            Type 'run <code>' to simulate running Python code.<br>
            Type 'exit' to simulate process termination.<br>
            $
        </div>
        <input type="text" id="input" autofocus>
    </div>

    <script>
        const output = document.getElementById('output');
        const input = document.getElementById('input');
        let runningProcesses = [];

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
            const [action, ...args] = command.split(' ');
            switch (action.toLowerCase()) {
                case 'install':
                    if (args.length > 0) {
                        output.innerHTML += `<br>Simulating installation of ${args.join(' ')}... Done!`;
                    } else {
                        output.innerHTML += `<br>Usage: install <package>`;
                    }
                    break;
                case 'run':
                    if (args.length > 0) {
                        const code = args.join(' ');
                        output.innerHTML += `<br>Running: ${code}<br>Output: Simulated result`;
                        runningProcesses.push(code); // Simulate persistent process
                    } else {
                        output.innerHTML += `<br>Usage: run <code>`;
                    }
                    break;
                case 'exit':
                    runningProcesses = [];
                    output.innerHTML += `<br>All processes terminated.`;
                    break;
                default:
                    output.innerHTML += `<br>Command not recognized. Try 'install', 'run', or 'exit'.`;
            }
        }

        // Simulate persistence (in reality, you'd need a server to maintain processes)
        window.onbeforeunload = function() {
            if (runningProcesses.length > 0) {
                output.innerHTML += `<br>Processes will continue running in the background (simulated).`;
            }
        };
    </script>
</body>
</html>