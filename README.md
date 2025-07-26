<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <title>HANA OT MOMMY</title>
</head>
<body style="margin:0; height:100vh; background: black; color: white; font-family: 'Arial Black', sans-serif; display:flex; flex-direction: column; justify-content: center; align-items: center; background: radial-gradient(ellipse at center, #000 0%, #111 100%);">

  <h1 style="font-size: 3em; text-align: center; font-weight: bold; color: #fff; text-shadow: 0 0 10px #f0f, 0 0 20px #0ff, 0 0 30px #0ff; margin-bottom: 40px; user-select:none;">
    HANA OT MOMMY ЖМИ PLAY
  </h1>

  <button id="playBtn" 
    style="margin-top: 50px; padding: 20px 60px; font-size: 2em; color: #fff; background: linear-gradient(to bottom, #ff00cc, #3333ff); border: none; border-radius: 15px; box-shadow: 0 5px #222, 0 10px 20px rgba(0,0,0,0.5); cursor: pointer; transition: transform 0.2s, box-shadow 0.2s; text-shadow: 2px 2px 4px #000; user-select:none;">
    ИГРАТЬ
  </button>

  <div id="passwordSection" style="margin-top: 20px; text-align: center; display: none; user-select:none;">
    <p>Введите пароль:</p>
    <input type="password" id="passwordInput" placeholder="Пароль..." 
      style="font-size: 1.2em; padding: 10px; border-radius: 10px; border: none; margin-top: 10px;" />
    <br />
    <button onclick="checkPassword()" 
      style="margin-top: 10px; padding: 10px 30px; font-size: 1.2em; color: #fff; background: linear-gradient(to bottom, #ff00cc, #3333ff); border: none; border-radius: 10px; cursor: pointer; user-select:none;">
      OK
    </button>
  </div>

  <div id="fileSection" style="margin-top: 20px; text-align: center; display: none; user-select:none;">
    <p>Выберите MP3-файл:</p>
    <input type="file" id="fileInput" 
      style="font-size: 1.2em; padding: 10px; border-radius: 10px; border: none; margin-top: 10px;" />
  </div>

  <script>
    let clickCount = 0;
    let audio = null;
    const playBtn = document.getElementById('playBtn');

    // Загружаем сохранённый звук (если есть)
    const savedAudio = localStorage.getItem('customAudio');
    if (savedAudio) {
      audio = new Audio(savedAudio);
      audio.loop = true;
    }

    playBtn.addEventListener('click', () => {
      clickCount++;
      if (clickCount > 30) clickCount = 1;

      if (audio) {
        audio.play();
      }

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
  </script>
</body>
</html>