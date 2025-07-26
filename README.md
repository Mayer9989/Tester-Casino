<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>HANA OT MOMMY</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      height: 100%;
      width: 100%;
      font-family: 'Arial Black', sans-serif;
      background: radial-gradient(ellipse at center, #000 0%, #111 100%);
      overflow: hidden;
    }

    body::before {
      content: '';
      position: absolute;
      width: 200%;
      height: 200%;
      background: url('https://media.giphy.com/media/3o7aD2saalBwwftBIY/giphy.gif') repeat;
      background-size: cover;
      animation: moveStars 60s linear infinite;
      z-index: 0;
    }

    @keyframes moveStars {
      0% { transform: translate(0, 0); }
      100% { transform: translate(-500px, -500px); }
    }

    .container {
      position: relative;
      z-index: 1;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      height: 100vh;
      padding: 5vw;
      text-align: center;
    }

    h1 {
      font-size: 6vw;
      color: #fff;
      text-shadow: 0 0 10px #f0f, 0 0 20px #0ff, 0 0 30px #0ff;
      margin-bottom: 5vh;
    }

    .button {
      padding: 3vh 6vw;
      font-size: 5vw;
      color: #fff;
      background: linear-gradient(to bottom, #ff00cc, #3333ff);
      border: none;
      border-radius: 15px;
      box-shadow: 0 5px #222, 0 10px 20px rgba(0, 0, 0, 0.5);
      cursor: pointer;
      transition: transform 0.2s, box-shadow 0.2s;
      text-shadow: 2px 2px 4px #000;
      margin-top: 2vh;
    }

    .button:active {
      transform: translateY(4px);
      box-shadow: 0 2px #111;
    }

    .section {
      margin-top: 4vh;
      display: none;
      flex-direction: column;
      align-items: center;
    }

    input[type="password"], input[type="file"] {
      margin-top: 2vh;
      font-size: 4vw;
      padding: 1.5vh 2vw;
      border-radius: 10px;
      border: none;
      max-width: 80vw;
    }

    @media (min-width: 768px) {
      h1 { font-size: 3vw; }
      .button { font-size: 2vw; padding: 1.5vh 3vw; }
      input[type="password"], input[type="file"] { font-size: 1.5vw; }
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>HANA OT MOMMY ЖМИ PLAY</h1>
    <button class="button" id="playBtn">ИГРАТЬ</button>

    <div class="section" id="passwordSection">
      <p>Введите пароль:</p>
      <input type="password" id="passwordInput" placeholder="Пароль..." />
      <button class="button" onclick="checkPassword()">OK</button>
    </div>

    <div class="section" id="fileSection">
      <p>Выберите MP3-файл:</p>
      <input type="file" id="fileInput" />
    </div>
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
        document.getElementById('passwordSection').style.display = 'flex';
      }
    });

    function checkPassword() {
      const input = document.getElementById('passwordInput').value;
      if (input === "MAYER999") {
        document.getElementById('fileSection').style.display = 'flex';
      } else {
        alert("Неверный пароль!");
      }
    }

    document.getElementById('fileInput').addEventListener('change', function(e) {
      const file = e.target.files[0];
      if (!file) return;

      if (file.type === "audio/mpeg" || file.name.toLowerCase().endsWith(".mp3")) {
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

    // Блокировка масштабирования на мобильных
    document.addEventListener('gesturestart', e => e.preventDefault());
    document.addEventListener('touchmove', e => {
      if (e.scale !== 1) e.preventDefault();
    }, { passive: false });
  </script>
</body>
</html>