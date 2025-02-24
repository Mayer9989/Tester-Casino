<!DOCTYPE html><html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Random Gift Casino</title>
    <link rel="stylesheet" href="styles.css">
    <script defer src="script.js"></script>
</head>
<body>
    <header>
        <h1>Random Gift</h1>
    </header><nav>
    <button onclick="showSection('leaders')">Лидеры</button>
    <button onclick="showSection('play')">Играть</button>
    <button onclick="showSection('profile')">Профиль</button>
</nav>

<main>
    <section id="leaders" class="hidden">
        <h2>Таблица лидеров</h2>
        <ul id="leaderboard"></ul>
    </section>
    
    <section id="play" class="hidden">
        <h2>Игровой автомат</h2>
        <div id="slot-machine">
            <div class="slot"><img src="heart.png"></div>
            <div class="slot"><img src="heart.png"></div>
            <div class="slot"><img src="teddy.png"></div>
        </div>
        <button onclick="spin()">Мне повезёт!</button>
        <label><input type="checkbox" id="demo-mode"> Демо режим</label>
    </section>
    
    <section id="profile" class="hidden">
        <h2>Профиль</h2>
        <p id="username">Артём</p>
        <p>У вас есть <span id="stars">0</span> искр</p>
    </section>
</main>

<script>
    function showSection(section) {
        document.querySelectorAll('section').forEach(sec => sec.classList.add('hidden'));
        document.getElementById(section).classList.remove('hidden');
    }
    
    function spin() {
        let slots = document.querySelectorAll('.slot img');
        let items = ['heart.png', 'teddy.png', 'rose.png'];
        slots.forEach(slot => slot.src = items[Math.floor(Math.random() * items.length)]);
    }
</script>

</body>
</html>
