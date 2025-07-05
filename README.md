<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Termux-Style Python Terminal</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background-color: #000;
            font-family: 'Courier New', Courier, monospace;
            color: #0f0;
            display: flex;
            flex-direction: column;
            height: 100vh;
        }
        #terminal {
            flex: 1;
            padding: 10px;
            overflow-y: auto;
            border: none;
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
        .keyboard {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
            padding: 10px;
            background: #000;
            border-top: 1px solid #0f0;
        }
        .key {
            background: #fff;
            color: #000;
            padding: 5px 10px;
            margin: 2px;
            border: 1px solid #ccc;
            border-radius: 3px;
            font-size: 14px;
            text-align: center;
        }
        .key.active {
            background: #000;
            color: #0f0;
            border-color: #0f0;
        }
        .special-keys {
            display: flex;
            justify-content: space-between;
            width: 100%;
        }
    </style>
</head>
<body>
    <div id="terminal">
        <div id="output">
            Welcome to Termux-Style Python Terminal!<br>
            Working with packages:<br>
            - Search: pkg search <query><br>
            - Install: pkg install <package><br>
            - Upgrade: pkg upgrade<br>
            $ 
        </div>
        <input type="text" id="input" autofocus>
    </div>
    <div class="keyboard">
        <div class="special-keys">
            <span class="key">ESC</span>
            <span class="key">/</span>
            <span class="key">-</span>
            <span class="key">HOME</span>
            <span class="key">END</span>
            <span class="key">PGUP</span>
        </div>
        <div class="special-keys">
            <span class="key">TAB</span>
            <span class="key">CTRL</span>
            <span class="key">ALT</span>
            <span class="key">&larr;</span>
            <span class="key">&darr;</span>
            <span class="key">-</span>
            <span class="key">PGDN</span>
        </div>
        <!-- Simulate QWERTY layout -->
        <div class="key">q</div><div class="key">w</div><div class="key">e</div><div class="key">r</div><div class="key">t</div><div class="key">y</div><div class="key">u</div><div class="key">i</div><div class="key">o</div><div class="key">p</div>
        <div class="key">a</div><div class="key">s</div><div class="key">d</div><div class="key">f</div><div class="key">g</div><div class="key">h</div><div class="key">j</div><div class="key">k</div><div class="key">l</div>
        <div class="key">z</div><div class="key">x</div><div class="key">c</div><div class="key">v</div><div class="key">b</div><div class="key">n</div><div class="key">m</div>
        <div class="key">7123</div>
        <div class="key" style="background: #000; color: #0f0; border-color: #0f0;">[1]</div>
        <div class="key">&larr;</div>
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
            const [action, ...args] = command.split(' ');
            switch (action.toLowerCase()) {
                case 'pkg':
                    if (args[0] === 'install') {
                        output.innerHTML += `<br>Simulating installation of ${args.slice(1).join(' ')}... Done!`;
                    } else if (args[0] === 'search') {
                        output.innerHTML += `<br>Simulating search for ${args.slice(1).join(' ')}... Results found!`;
                    } else if (args[0] === 'upgrade') {
                        output.innerHTML += `<br>Simulating upgrade... Done!`;
                    } else {
                        output.innerHTML += `<br>Usage: pkg install <package> or pkg search <query> or pkg upgrade`;
                    }
                    break;
                default:
                    output.innerHTML += `<br>Command not recognized. Try 'pkg install <package>', 'pkg search <query>', or 'pkg upgrade'.`;
            }
        }
    </script>
</body>
</html>