<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <title>HANA OT MOMMY</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      overflow: hidden;
    }
    html, body {
      width: 100%;
      height: 100%;
      background: black;
      color: white;
      font-family: 'Arial Black', sans-serif;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      background: radial-gradient(ellipse at center, #000 0%, #111 100%);
      position: relative;
    }
    body::before {
      content: '';
      position: absolute;
      width: 200%;
      height: 200%;
      background: transparent url('https://media.giphy.com/media/3o7aD2saalBwwftBIY/giphy.gif') repeat;
      background-size: cover;
      animation: moveStars 60s linear infinite;
      z-index: 0;
    }
    @keyframes moveStars {
      0% { transform: translate(0, 0); }
      100% { transform: translate(-500px, -500px); }
    }
    h1 {
      font-size: 3em;
      text-align: center;
      font-weight: bold;
      color: #fff;
      text-shadow: 0 0 10px #f0f, 0 0 20px #0ff, 0 0 30px #0ff;
      z-index: 1;
    }
    .button {
      margin-top: 50px;
      padding: 20px 60px;
      font-size: 2em;
      color: #fff;
      background: linear-gradient(to bottom, #ff00cc, #3333ff);
      border: none;
      border-radius: 15px;
      box-shadow: 0 5px #222, 0 10px 20px rgba(0, 0, 0, 0.5);
      cursor: pointer;
      transition: transform 0.2s, box-shadow 0.2s;
      text-shadow: 2px 2px 4px #000;
      z-index: 1;
    }
    .button:active {
      transform: translateY(4px);
      box-shadow: 0 2px #111;
    }
    #passwordSection, #fileSection {
      margin-top: 20px;
      z-index: 1;
      text-align: center;
    }
    input[type="password"], input[type="file"] {
      font-size: 1.2em;
      padding: 10px;
      border-radius: 10px;
      border: none;
      margin-top: 10px;
    }
  </style>
</head>
<body>
  <h1>HANA OT MOMMY ЖМИ PLAY</h1>
  <button class="button" id="playBtn">ИГРАТЬ</button>

  <div id="passwordSection" style="display:none;">
    <p>Введите пароль:</p>
    <input type="password" id="passwordInput" placeholder="Пароль..." />
    <button class="button" onclick="checkPassword()">OK</button>
  </div>

  <div id="fileSection" style="display:none;">
    <p>Выберите MP3-файл:</p>
    <input type="file" id="fileInput" />
  </div>

  <script>
    let clickCount = 0;
    let audio = null;
    const playBtn = document.getElementById('playBtn');

    const savedAudio = localStorage.getItem('customAudio');
    if (savedAudio) {
      audio = new Audio(savedAudio);
      audio.loop = true;
    }

    playBtn.addEventListener('click', () => {
      clickCount++;
      if (clickCount > 30) clickCount = 1;

      if (audio) audio.play();

      if (clickCount === 30) {
        document.getElementById('passwordSection').style.display = 'block';
      }
    });

    function checkPassword() {
      const input = document.getElementById('passwordInput').value;
      if (input === "MAYER999") {
        document.getElementById('fileSection').style.display = 'block';
      } else {
        alert("Неверный пароль!");
      }
    }

    document.getElementById('fileInput').addEventListener('change', function(e) {
      const file = e.target.files[0];
      if (!file) return;

      if (file.type === "audio/mpeg" || file.name.endsWith(".mp3")) {
        const reader = new FileReader();
        reader.onload = function(event) {
          const dataUrl = event.target.result;
          localStorage.setItem('customAudio', dataUrl);
          audio = new Audio(dataUrl);
          audio.loop = true;
          alert("Звук успешно применён! Теперь он будет использоваться.");
        };
        reader.readAsDataURL(file);
      } else {
        alert("Ошибка: выберите только mp3-файл.");
      }
    });
  </script>
</body>
</html>