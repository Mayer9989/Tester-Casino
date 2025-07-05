<!DOCTYPE html>
<html>
<head>
    <title>Real Python Terminal</title>
    <style>
        body {
            background: black;
            color: #00ff00;
            font-family: monospace;
            margin: 0;
            padding: 10px;
            overflow: hidden;
        }
        #terminal {
            height: 95vh;
            overflow-y: auto;
        }
        #input {
            background: black;
            color: #00ff00;
            border: none;
            outline: none;
            font-family: monospace;
            width: 80%;
        }
        .prompt {
            color: #00ff00;
        }
        .output {
            white-space: pre-wrap;
        }
        .error {
            color: #ff5555;
        }
    </style>
</head>
<body>
    <div id="terminal">
        <div class="output">Loading Python...</div>
    </div>
    <div>
        <span class="prompt">>>> </span>
        <input type="text" id="input" autofocus>
    </div>

    <!-- Pyodide (настоящий Python в браузере) -->
    <script src="https://cdn.jsdelivr.net/pyodide/v0.23.4/full/pyodide.js"></script>
    <script>
        const terminal = document.getElementById('terminal');
        const input = document.getElementById('input');
        let pyodide;

        async function initPython() {
            terminal.innerHTML += 'Initializing Python...<br>';
            pyodide = await loadPyodide({
                indexURL: "https://cdn.jsdelivr.net/pyodide/v0.23.4/full/"
            });
            terminal.innerHTML += 'Python ready!<br>>> ';
            
            // Добавляем историю команд
            const commandHistory = [];
            let historyIndex = -1;

            input.addEventListener('keydown', async (e) => {
                if (e.key === 'Enter') {
                    const code = input.value.trim();
                    if (!code) return;

                    // Добавляем в историю
                    commandHistory.push(code);
                    historyIndex = commandHistory.length;

                    // Выводим команду в терминал
                    terminal.innerHTML += `<span class="prompt">>>> </span>${code}<br>`;

                    try {
                        // Выполняем код
                        const result = await pyodide.runPythonAsync(code);
                        if (result !== undefined) {
                            terminal.innerHTML += `<div class="output">${result}</div>`;
                        }
                    } catch (err) {
                        terminal.innerHTML += `<div class="error">${err}</div>`;
                    }

                    input.value = '';
                    terminal.innerHTML += '<span class="prompt">>>> </span>';
                    terminal.scrollTop = terminal.scrollHeight;
                }

                // Навигация по истории (стрелки вверх/вниз)
                if (e.key === 'ArrowUp') {
                    if (historyIndex > 0) {
                        historyIndex--;
                        input.value = commandHistory[historyIndex];
                    }
                } else if (e.key === 'ArrowDown') {
                    if (historyIndex < commandHistory.length - 1) {
                        historyIndex++;
                        input.value = commandHistory[historyIndex];
                    } else {
                        historyIndex = commandHistory.length;
                        input.value = '';
                    }
                }
            });
        }

        initPython();
    </script>
</body>
</html>