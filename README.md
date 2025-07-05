<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Real Python Terminal</title>
    <style>
        body {
            background-color: #000;
            color: #0f0;
            font-family: 'Courier New', monospace;
            margin: 0;
            padding: 20px;
        }
        #terminal {
            width: 100%;
            height: 80vh;
            overflow-y: auto;
            border: 1px solid #333;
            padding: 10px;
            white-space: pre-wrap;
        }
        #input {
            background: #000;
            color: #0f0;
            border: 1px solid #0f0;
            font-family: 'Courier New', monospace;
            width: 80%;
            padding: 5px;
            outline: none;
        }
        #run-btn {
            background: #0f0;
            color: #000;
            border: none;
            padding: 5px 10px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <h1>🐍 Real Python Terminal (Pyodide)</h1>
    <div id="terminal"></div>
    <input type="text" id="input" placeholder="Введите Python код и нажмите Enter...">
    <button id="run-btn">Run</button>

    <!-- Загрузка Pyodide (настоящий Python в браузере) -->
    <script src="https://cdn.jsdelivr.net/pyodide/v0.23.4/full/pyodide.js"></script>
    <script>
        let term = document.getElementById("terminal");
        let input = document.getElementById("input");
        let runBtn = document.getElementById("run-btn");

        async function setupPython() {
            term.textContent = "Загрузка Python...\n";
            let pyodide = await loadPyodide({
                indexURL: "https://cdn.jsdelivr.net/pyodide/v0.23.4/full/"
            });
            term.textContent += "Готово! Пишите код Python.\n>>> ";

            function runCode() {
                let code = input.value;
                if (!code.trim()) return;

                term.textContent += code + "\n";
                try {
                    let result = pyodide.runPython(code);
                    if (result !== undefined) {
                        term.textContent += result + "\n";
                    }
                } catch (err) {
                    term.textContent += "Ошибка: " + err.message + "\n";
                }
                input.value = "";
                term.textContent += ">>> ";
                term.scrollTop = term.scrollHeight;
            }

            input.addEventListener("keypress", (e) => {
                if (e.key === "Enter") runCode();
            });
            runBtn.addEventListener("click", runCode);
        }

        setupPython();
    </script>
</body>
</html>