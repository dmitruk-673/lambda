import socket
import os
import json
import urllib.request

HOST = "194.44.149.125"
PORT = 22
TIMEOUT = 5

TELEGRAM_TOKEN = "YOUR_TELEGRAM_TOKEN"
TELEGRAM_CHAT_ID = YOUR_TELEGRAM_CHAT_ID

TELEGRAM_TOKEN = os.environ.get("TELEGRAM_TOKEN")
TELEGRAM_CHAT_ID = os.environ.get("TELEGRAM_CHAT_ID")


def send_telegram_message(message):
    print("DEBUG: TELEGRAM_TOKEN =", TELEGRAM_TOKEN)
    print("DEBUG: TELEGRAM_CHAT_ID =", TELEGRAM_CHAT_ID)

    url = f"https://api.telegram.org/bot{TELEGRAM_TOKEN}/sendMessage"
    print("DEBUG URL:", url)
    
    data = json.dumps({
        "chat_id": TELEGRAM_CHAT_ID,
        "text": message
    }).encode("utf-8")
    
    req = urllib.request.Request(
        url,
        data=data,
        headers={"Content-Type": "application/json"}
    )
    
    urllib.request.urlopen(req)


def check_port(host, port):
    try:
        with socket.create_connection((host, port), timeout=TIMEOUT):
            return True
    except Exception:
        return False


def lambda_handler(event, context):
    is_available = check_port(HOST, PORT)

    if is_available:
        message = f"✅ OK\nHost {HOST} на порту {PORT} доступний."
        send_telegram_message(message)
        return {"statusCode": 200}

    else:
        message = f"🚨 ALERT!\nHost {HOST} на порту {PORT} НЕ доступний!"
        send_telegram_message(message)
        return {"statusCode": 500}
