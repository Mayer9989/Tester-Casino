<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Terminal (Termux Style)</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background-color: #000;
            color: #fff;
            font-family: 'Roboto Mono', monospace;
            font-size: 14px;
            line-height: 1.4;
        }
        #terminal {
            padding: 10px;
            height: 100vh;
            overflow-y: auto;
            box-sizing: border-box;
            white-space: pre-wrap;
            word-break: break-all;
        }
        #input-line {
            display: flex;
        }
        #prompt {
            color: #fff;
            margin-right: 5px;
        }
        #cmd-input {
            background: transparent;
            border: none;
            color: #fff;
            font-family: 'Roboto Mono', monospace;
            font-size: 14px;
            flex-grow: 1;
            outline: none;
            caret-color: #fff;
            width: calc(100% - 100px);
        }
        .command {
            margin-bottom: 5px;
        }
        .output {
            margin-bottom: 10px;
            white-space: pre-wrap;
        }
        .error {
            color: #ff5555;
        }
    </style>
</head>
<body>
    <div id="terminal">
        <div>Welcome to Termux!</div>
        <div>Wiki:            https://wiki.termux.com</div>
        <div>Community forum: https://termux.com/community</div>
        <div>Gitter chat:     https://gitter.im/termux/termux</div>
        <div>IRC channel:     #termux on libera.chat</div>
        <div><br></div>
        <div>Working with packages:</div>
        <div>* Search packages:   pkg search &lt;query&gt;</div>
        <div>* Install a package: pkg install &lt;package&gt;</div>
        <div>* Upgrade packages:  pkg upgrade</div>
        <div><br></div>
        <div id="history"></div>
        <div id="input-line">
            <span id="prompt">$</span>
            <input type="text" id="cmd-input" autofocus>
        </div>
    </div>

    <script>
        const terminal = document.getElementById('terminal');
        const history = document.getElementById('history');
        const cmdInput = document.getElementById('cmd-input');
        const prompt = document.getElementById('prompt');

        // Имитация базовых команд Termux
        function executeCommand(cmd) {
            // Добавляем команду в историю
            const commandElement = document.createElement('div');
            commandElement.className = 'command';
            commandElement.textContent = '$ ' + cmd;
            history.appendChild(commandElement);

            // Обработка команд
            let output = '';
            if (cmd === 'clear' || cmd === 'cls') {
                history.innerHTML = '';
                return;
            } else if (cmd === 'help') {
                output = [
                    'Available commands:',
                    'help      - Show this help',
                    'clear     - Clear terminal',
                    'ls        - List directory contents',
                    'pkg       - Package manager',
                    'python    - Run Python interpreter',
                    'exit      - Close terminal',
                    '',
                    'Note: This is a simulation. Real commands won\'t work.'
                ].join('\n');
            } else if (cmd === 'ls') {
                output = [
                    'bin   lib   tmp',
                    'etc   usr   var',
                    'home  proc  storage'
                ].join('\n');
            } else if (cmd.startsWith('pkg')) {
                output = 'pkg: command not found\nInstall Termux from Play Store for real package management';
            } else if (cmd === 'python') {
                output = 'Python 3.10.5 (default, Jun 25 2022, 17:14:04)\n[Clang 14.0.6 ] on linux\nType "help", "copyright", "credits" or "license" for more information.';
            } else if (cmd === '') {
                // Пустая команда - ничего не делаем
            } else {
                output = `-bash: ${cmd}: command not found`;
            }

            // Выводим результат
            if (output) {
                const outputElement = document.createElement('div');
                outputElement.className = 'output';
                outputElement.textContent = output;
                history.appendChild(outputElement);
            }

            // Прокрутка вниз
            terminal.scrollTop = terminal.scrollHeight;
        }

        // Обработка ввода
        cmdInput.addEventListener('keydown', function(e) {
            if (e.key === 'Enter') {
                executeCommand(cmdInput.value.trim());
                cmdInput.value = '';
            }
        });

        // Автофокус на инпуте при клике в любом месте терминала
        terminal.addEventListener('click', function() {
            cmdInput.focus();
        });

        // Начальное приветствие
        terminal.scrollTop = terminal.scrollHeight;
    </script>
</body>
</html>