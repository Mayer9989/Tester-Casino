<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GTA RP Форум | Матрёшка & Black Rush</title>
    <style>
        :root {
            --main-dark: #121212;
            --secondary-dark: #1E1E1E;
            --accent-red: #E74C3C;
            --accent-blue: #3498DB;
            --text-light: #ECF0F1;
            --text-gray: #BDC3C7;
        }
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background-color: var(--main-dark);
            color: var(--text-light);
            line-height: 1.6;
        }
        
        header {
            background: linear-gradient(135deg, var(--accent-red), #C0392B);
            padding: 1rem 0;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
            position: sticky;
            top: 0;
            z-index: 100;
        }
        
        .container {
            width: 90%;
            max-width: 1200px;
            margin: 0 auto;
        }
        
        .header-content {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        
        .logo {
            display: flex;
            align-items: center;
            gap: 15px;
        }
        
        .logo img {
            height: 50px;
        }
        
        .logo-text h1 {
            font-size: 1.8rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 1px;
        }
        
        .logo-text p {
            font-size: 0.9rem;
            color: rgba(255, 255, 255, 0.8);
        }
        
        nav ul {
            display: flex;
            list-style: none;
            gap: 20px;
        }
        
        nav a {
            color: white;
            text-decoration: none;
            font-weight: 600;
            padding: 8px 12px;
            border-radius: 4px;
            transition: all 0.3s ease;
        }
        
        nav a:hover {
            background-color: rgba(255, 255, 255, 0.2);
        }
        
        .auth-buttons {
            display: flex;
            gap: 10px;
        }
        
        .btn {
            padding: 8px 20px;
            border-radius: 4px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            border: none;
        }
        
        .btn-login {
            background-color: transparent;
            color: white;
            border: 2px solid white;
        }
        
        .btn-register {
            background-color: white;
            color: var(--accent-red);
        }
        
        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }
        
        main {
            padding: 2rem 0;
        }
        
        .server-tabs {
            display: flex;
            margin-bottom: 2rem;
            border-bottom: 1px solid #333;
        }
        
        .tab {
            padding: 12px 24px;
            cursor: pointer;
            font-weight: 600;
            position: relative;
        }
        
        .tab.active {
            color: var(--accent-red);
        }
        
        .tab.active::after {
            content: '';
            position: absolute;
            bottom: -1px;
            left: 0;
            width: 100%;
            height: 3px;
            background-color: var(--accent-red);
        }
        
        .tab-content {
            display: none;
        }
        
        .tab-content.active {
            display: block;
        }
        
        .forum-sections {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 20px;
            margin-bottom: 2rem;
        }
        
        .section-card {
            background-color: var(--secondary-dark);
            border-radius: 8px;
            overflow: hidden;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
            transition: transform 0.3s ease;
        }
        
        .section-card:hover {
            transform: translateY(-5px);
        }
        
        .section-header {
            background: linear-gradient(135deg, #2C3E50, #4CA1AF);
            padding: 15px;
            display: flex;
            align-items: center;
            gap: 15px;
        }
        
        .section-icon {
            font-size: 1.5rem;
            color: white;
        }
        
        .section-title h3 {
            font-size: 1.2rem;
            margin-bottom: 5px;
        }
        
        .section-title p {
            font-size: 0.8rem;
            color: var(--text-gray);
        }
        
        .section-body {
            padding: 15px;
        }
        
        .section-stats {
            display: flex;
            justify-content: space-between;
            font-size: 0.8rem;
            color: var(--text-gray);
            border-top: 1px solid #333;
            padding-top: 10px;
            margin-top: 10px;
        }
        
        .recent-threads {
            background-color: var(--secondary-dark);
            border-radius: 8px;
            padding: 20px;
            margin-bottom: 2rem;
        }
        
        .section-heading {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }
        
        .section-heading h2 {
            font-size: 1.5rem;
            color: var(--accent-blue);
        }
        
        .view-all {
            color: var(--accent-blue);
            text-decoration: none;
            font-weight: 600;
        }
        
        .thread-list {
            list-style: none;
        }
        
        .thread-item {
            padding: 15px 0;
            border-bottom: 1px solid #333;
        }
        
        .thread-item:last-child {
            border-bottom: none;
        }
        
        .thread-title {
            font-size: 1.1rem;
            margin-bottom: 5px;
        }
        
        .thread-title a {
            color: var(--text-light);
            text-decoration: none;
        }
        
        .thread-title a:hover {
            color: var(--accent-blue);
            text-decoration: underline;
        }
        
        .thread-meta {
            display: flex;
            font-size: 0.8rem;
            color: var(--text-gray);
            gap: 15px;
        }
        
        .online-users {
            background-color: var(--secondary-dark);
            border-radius: 8px;
            padding: 20px;
        }
        
        .online-count {
            font-size: 1.2rem;
            color: #2ECC71;
            margin-bottom: 15px;
        }
        
        .user-list {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            list-style: none;
        }
        
        .user-badge {
            background-color: #333;
            padding: 5px 10px;
            border-radius: 20px;
            font-size: 0.8rem;
            display: flex;
            align-items: center;
            gap: 5px;
        }
        
        .user-avatar {
            width: 20px;
            height: 20px;
            border-radius: 50%;
            background-color: #555;
        }
        
        footer {
            background-color: #111;
            padding: 2rem 0;
            margin-top: 2rem;
        }
        
        .footer-content {
            display: flex;
            justify-content: space-between;
            flex-wrap: wrap;
            gap: 30px;
        }
        
        .footer-section {
            flex: 1;
            min-width: 200px;
        }
        
        .footer-section h3 {
            color: var(--accent-blue);
            margin-bottom: 15px;
            font-size: 1.2rem;
        }
        
        .footer-links {
            list-style: none;
        }
        
        .footer-links li {
            margin-bottom: 8px;
        }
        
        .footer-links a {
            color: var(--text-gray);
            text-decoration: none;
            transition: color 0.3s ease;
        }
        
        .footer-links a:hover {
            color: var(--accent-blue);
        }
        
        .social-links {
            display: flex;
            gap: 15px;
            margin-top: 15px;
        }
        
        .social-icon {
            width: 40px;
            height: 40px;
            background-color: #333;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.3s ease;
        }
        
        .social-icon:hover {
            background-color: var(--accent-blue);
            transform: translateY(-3px);
        }
        
        .copyright {
            text-align: center;
            margin-top: 2rem;
            padding-top: 1rem;
            border-top: 1px solid #333;
            color: var(--text-gray);
            font-size: 0.9rem;
        }
        
        @media (max-width: 768px) {
            .header-content {
                flex-direction: column;
                gap: 20px;
            }
            
            nav ul {
                flex-wrap: wrap;
                justify-content: center;
            }
            
            .forum-sections {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <header>
        <div class="container">
            <div class="header-content">
                <div class="logo">
                    <img src="https://via.placeholder.com/50" alt="Логотип">
                    <div class="logo-text">
                        <h1>GTA RP Форум</h1>
                        <p>Матрёшка | Black Rush</p>
                    </div>
                </div>
                
                <nav>
                    <ul>
                        <li><a href="#" class="active">Главная</a></li>
                        <li><a href="#">Форум</a></li>
                        <li><a href="#">Правила</a></li>
                        <li><a href="#">Донат</a></li>
                        <li><a href="#">Поддержка</a></li>
                    </ul>
                </nav>
                
                <div class="auth-buttons">
                    <button class="btn btn-login">Войти</button>
                    <button class="btn btn-register">Регистрация</button>
                </div>
            </div>
        </div>
    </header>
    
    <main class="container">
        <div class="server-tabs">
            <div class="tab active" data-tab="matreshka">Матрёшка RP</div>
            <div class="tab" data-tab="blackrush">Black Rush</div>
        </div>
        
        <div class="tab-content active" id="matreshka">
            <div class="forum-sections">
                <div class="section-card">
                    <div class="section-header">
                        <div class="section-icon">📢</div>
                        <div class="section-title">
                            <h3>Новости</h3>
                            <p>Последние обновления проекта</p>
                        </div>
                    </div>
                    <div class="section-body">
                        <p>Обсуждайте последние новости и обновления сервера</p>
                        <div class="section-stats">
                            <span>Темы: 124</span>
                            <span>Сообщения: 1,245</span>
                        </div>
                    </div>
                </div>
                
                <div class="section-card">
                    <div class="section-header">
                        <div class="section-icon">💬</div>
                        <div class="section-title">
                            <h3>Общение</h3>
                            <p>Общие обсуждения</p>
                        </div>
                    </div>
                    <div class="section-body">
                        <p>Место для свободного общения игроков</p>
                        <div class="section-stats">
                            <span>Темы: 542</span>
                            <span>Сообщения: 12,874</span>
                        </div>
                    </div>
                </div>
                
                <div class="section-card">
                    <div class="section-header">
                        <div class="section-icon">👮</div>
                        <div class="section-title">
                            <h3>Фракции</h3>
                            <p>Государственные организации</p>
                        </div>
                    </div>
                    <div class="section-body">
                        <p>Обсуждение работы государственных структур</p>
                        <div class="section-stats">
                            <span>Темы: 87</span>
                            <span>Сообщения: 3,214</span>
                        </div>
                    </div>
                </div>
                
                <div class="section-card">
                    <div class="section-header">
                        <div class="section-icon">💰</div>
                        <div class="section-title">
                            <h3>Бизнесы</h3>
                            <p>Предпринимательство в игре</p>
                        </div>
                    </div>
                    <div class="section-body">
                        <p>Обсуждение бизнесов и экономики сервера</p>
                        <div class="section-stats">
                            <span>Темы: 156</span>
                            <span>Сообщения: 4,532</span>
                        </div>
                    </div>
                </div>
                
                <div class="section-card">
                    <div class="section-header">
                        <div class="section-icon">🎮</div>
                        <div class="section-title">
                            <h3>Геймплей</h3>
                            <p>Игровые механики</p>
                        </div>
                    </div>
                    <div class="section-body">
                        <p>Обсуждение игрового процесса и механик</p>
                        <div class="section-stats">
                            <span>Темы: 231</span>
                            <span>Сообщения: 7,845</span>
                        </div>
                    </div>
                </div>
                
                <div class="section-card">
                    <div class="section-header">
                        <div class="section-icon">🛠️</div>
                        <div class="section-title">
                            <h3>Техподдержка</h3>
                            <p>Помощь игрокам</p>
                        </div>
                    </div>
                    <div class="section-body">
                        <p>Решение технических проблем и вопросов</p>
                        <div class="section-stats">
                            <span>Темы: 342</span>
                            <span>Сообщения: 5,678</span>
                        </div>
                    </div>
                </div>
            </div>
            
            <div class="recent-threads">
                <div class="section-heading">
                    <h2>Последние темы</h2>
                    <a href="#" class="view-all">Все темы →</a>
                </div>
                
                <ul class="thread-list">
                    <li class="thread-item">
                        <div class="thread-title">
                            <a href="#">Обновление 1.5 - новые бизнесы и улучшения</a>
                        </div>
                        <div class="thread-meta">
                            <span>Автор: Admin</span>
                            <span>Дата: 15.06.2023</span>
                            <span>Ответы: 42</span>
                        </div>
                    </li>
                    
                    <li class="thread-item">
                        <div class="thread-title">
                            <a href="#">Конкурс на лучший РП-пост</a>
                        </div>
                        <div class="thread-meta">
                            <span>Автор: Moderator</span>
                            <span>Дата: 12.06.2023</span>
                            <span>Ответы: 28</span>
                        </div>
                    </li>
                    
                    <li class="thread-item">
                        <div class="thread-title">
                            <a href="#">Как открыть свой бизнес - гайд для новичков</a>
                        </div>
                        <div class="thread-meta">
                            <span>Автор: BusinessGuru</span>
                            <span>Дата: 10.06.2023</span>
                            <span>Ответы: 15</span>
                        </div>
                    </li>
                    
                    <li class="thread-item">
                        <div class="thread-title">
                            <a href="#">Отчет о работе LSPD за неделю</a>
                        </div>
                        <div class="thread-meta">
                            <span>Автор: Chief_Police</span>
                            <span>Дата: 08.06.2023</span>
                            <span>Ответы: 37</span>
                        </div>
                    </li>
                    
                    <li class="thread-item">
                        <div class="thread-title">
                            <a href="#">Ищем активных игроков в банду</a>
                        </div>
                        <div class="thread-meta">
                            <span>Автор: GangLeader</span>
                            <span>Дата: 05.06.2023</span>
                            <span>Ответы: 19</span>
                        </div>
                    </li>
                </ul>
            </div>
            
            <div class="online-users">
                <div class="online-count">Онлайн: 247 игроков</div>
                <ul class="user-list">
                    <li class="user-badge">
                        <div class="user-avatar"></div>
                        <span>User123</span>
                    </li>
                    <li class="user-badge">
                        <div class="user-avatar"></div>
                        <span>ProGamer</span>
                    </li>
                    <li class="user-badge">
                        <div class="user-avatar"></div>
                        <span>RP_Lover</span>
                    </li>
                    <li class="user-badge">
                        <div class="user-avatar"></div>
                        <span>NewPlayer</span>
                    </li>
                    <li class="user-badge">
                        <div class="user-avatar"></div>
                        <span>Veteran</span>
                    </li>
                    <li class="user-badge">
                        <div class="user-avatar"></div>
                        <span>+242</span>
                    </li>
                </ul>
            </div>
        </div>
        
        <div class="tab-content" id="blackrush">
            <div class="forum-sections">
                <div class="section-card">
                    <div class="section-header">
                        <div class="section-icon">📢</div>
                        <div class="section-title">
                            <h3>Новости Black Rush</h3>
                            <p>Обновления и анонсы</p>
                        </div>
                    </div>
                    <div class="section-body">
                        <p>Последние новости проекта Black Rush</p>
                        <div class="section-stats">
                            <span>Темы: 87</span>
                            <span>Сообщения: 945</span>
                        </div>
                    </div>
                </div>
                
                <div class="section-card">
                    <div class="section-header">
                        <div class="section-icon">💬</div>
                        <div class="section-title">
                            <h3>Общение</h3>
                            <p>Обсуждения игроков</p>
                        </div>
                    </div>
                    <div class="section-body">
                        <p>Место для свободного общения на сервере</p>
                        <div class="section-stats">
                            <span>Темы: 321</span>
                            <span>Сообщения: 8,754</span>
                        </div>
                    </div>
                </div>
                
                <div class="section-card">
                    <div class="section-header">
                        <div class="section-icon">🏎️</div>
                        <div class="section-title">
                            <h3>Автомобили</h3>
                            <p>Тюнинг и гонки</p>
                        </div>
                    </div>
                    <div class="section-body">
                        <p>Все об автомобилях и гонках в Black Rush</p>
                        <div class="section-stats">
                            <span>Темы: 143</span>
                            <span>Сообщения: 3,456</span>
                        </div>
                    </div>
                </div>
                
                <div class="section-card">
                    <div class="section-header">
                        <div class="section-icon">🔫</div>
                        <div class="section-title">
                            <h3>Криминал</h3>
                            <p>Теневой мир</p>
                        </div>
                    </div>
                    <div class="section-body">
                        <p>Обсуждение криминального геймплея</p>
                        <div class="section-stats">
                            <span>Темы: 98</span>
                            <span>Сообщения: 2,789</span>
                        </div>
                    </div>
                </div>
            </div>
            
            <div class="recent-threads">
                <div class="section-heading">
                    <h2>Последние темы Black Rush</h2>
                    <a href="#" class="view-all">Все темы →</a>
                </div>
                
                <ul class="thread-list">
                    <li class="thread-item">
                        <div class="thread-title">
                            <a href="#">Новые автомобили в обновлении 2.3</a>
                        </div>
                        <div class="thread-meta">
                            <span>Автор: DevTeam</span>
                            <span>Дата: 14.06.2023</span>
                            <span>Ответы: 31</span>
                        </div>
                    </li>
                    
                    <li class="thread-item">
                        <div class="thread-title">
                            <a href="#">Гонки на этой неделе - расписание</a>
                        </div>
                        <div class="thread-meta">
                            <span>Автор: RaceMaster</span>
                            <span>Дата: 11.06.2023</span>
                            <span>Ответы: 24</span>
                        </div>
                    </li>
                    
                    <li class="thread-item">
                        <div class="thread-title">
                            <a href="#">Лучшие тюнинг-ателье - рейтинг</a>
                        </div>
                        <div class="thread-meta">
                            <span>Автор: TuningExpert</span>
                            <span>Дата: 09.06.2023</span>
                            <span>Ответы: 18</span>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </main>
    
    <footer>
        <div class="container">
            <div class="footer-content">
                <div class="footer-section">
                    <h3>О проекте</h3>
                    <ul class="footer-links">
                        <li><a href="#">О нас</a></li>
                        <li><a href="#">Команда</a></li>
                        <li><a href="#">Правила</a></li>
                        <li><a href="#">История</a></li>
                    </ul>
                </div>
                
                <div class="footer-section">
                    <h3>Помощь</h3>
                    <ul class="footer-links">
                        <li><a href="#">FAQ</a></li>
                        <li><a href="#">Техподдержка</a></li>
                        <li><a href="#">Гайды</a></li>
                        <li><a href="#">Баги и отчеты</a></li>
                    </ul>
                </div>
                
                <div class="footer-section">
                    <h3>Сообщество</h3>
                    <ul class="footer-links">
                        <li><a href="#">Наша команда</a></li>
                        <li><a href="#">Мероприятия</a></li>
                        <li><a href="#">Конкурсы</a></li>
                        <li><a href="#">Вакансии</a></li>
                    </ul>
                </div>
                
                <div class="footer-section">
                    <h3>Мы в соцсетях</h3>
                    <div class="social-links">
                        <a href="#" class="social-icon">VK</a>
                        <a href="#" class="social-icon">TG</a>
                        <a href="#" class="social-icon">YT</a>
                        <a href="#" class="social-icon">DS</a>
                    </div>
                </div>
            </div>
            
            <div class="copyright">
                © 2023 GTA RP Форум | Матрёшка RP & Black Rush. Все права защищены.
            </div>
        </div>
    </footer>
    
    <script>
        // Переключение между вкладками серверов
        document.querySelectorAll('.tab').forEach(tab => {
            tab.addEventListener('click', () => {
                // Удаляем активный класс у всех вкладок
                document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
                document.querySelectorAll('.tab-content').forEach(c => c.classList.remove('active'));
                
                // Добавляем активный класс к выбранной вкладке
                tab.classList.add('active');
                const tabId = tab.getAttribute('data-tab');
                document.getElementById(tabId).classList.add('active');
            });
        });
        
        // Модальное окно авторизации
        document.querySelector('.btn-login').addEventListener('click', () => {
            alert('Форма входа будет здесь');
        });
        
        // Модальное окно регистрации
        document.querySelector('.btn-register').addEventListener('click', () => {
            alert('Форма регистрации будет здесь');
        });
    </script>
</body>
</html>
