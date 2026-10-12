# Hosting Xpiliot — Cloud Deployment Starter

This bundle is intended for a Linux VPS/cloud server (Ubuntu/Debian). It includes the cleaned bot script,
Python dependencies, an environment-variable template, and a systemd service template.

## Important security notes
- Use a NEW Telegram bot token if the old token was ever exposed. Generate/revoke tokens via the official @BotFather.
- Do not commit `.env` or share it.
- This bot executes uploaded Python/JavaScript scripts. Running untrusted uploads on a public server is risky.
  Use only with trusted users unless you have proper sandboxing, resource limits, and access controls.
- The Flask endpoint is only a basic status response; it is not a secure management panel.

## 1. Install on Ubuntu/Debian VPS

Upload/extract this folder on the server, then run:

```bash
sudo apt update
sudo apt install -y python3 python3-venv python3-pip
mkdir -p ~/hosting-xpiliot
cd ~/hosting-xpiliot
# Put the files from this ZIP in this folder first.
python3 -m venv .venv
. .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
nano .env
```

Edit `.env` and set your real values. OWNER_ID and ADMIN_ID are prefilled as 7476269445. Keep them numeric, without extra text. Replace BOT_TOKEN with a NEW token from @BotFather; the token shared in chat should be revoked first.

## 2. Start manually

Activate the virtual environment and export variables from `.env`:

```bash
cd ~/hosting-xpiliot
. .venv/bin/activate
set -a
. ./.env
set +a
python Hosting_Xpiliot_English_Cleaned.py
```

Keep the process running in your server session for a first test. Send `/start` to your bot in Telegram.

## 3. Keep it running with systemd (Ubuntu/Debian VPS)

1. Copy `hosting-xpiliot.service.example` to `/etc/systemd/system/hosting-xpiliot.service`.
2. Edit the service file and replace `YOUR_LINUX_USER` with your Linux username.
3. Put the project at `/home/YOUR_LINUX_USER/hosting-xpiliot`.
4. Set correct permissions on `.env`:
   `chmod 600 /home/YOUR_LINUX_USER/hosting-xpiliot/.env`
5. Then run:
   `sudo systemctl daemon-reload`
   `sudo systemctl enable --now hosting-xpiliot`
   `sudo systemctl status hosting-xpiliot`
6. Logs: `sudo journalctl -u hosting-xpiliot -f`

## Platform notes
- For a generic VPS, use the systemd method above.
- `Procfile` is included for platforms that support worker processes, but not every cloud host supports persistent Telegram polling.
- The app binds its basic Flask status endpoint to `0.0.0.0:$PORT` (default 8080).
- This package is a deployment starter, not a guarantee of production security or 24/7 uptime. Test before moving real users.
