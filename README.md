# SMC Signal Alerter

A Python-based notification utility for structured trading signals. It parses messages containing market setups, creates visual trade cards, and delivers urgent notifications through **Telegram** and **ntfy**.

> **Scope:** This repository is an alerting and message-forwarding utility, not a verified profitable trading strategy or an exchange order-execution engine.

## Features

- Parse structured signals for trading pair, direction, order type, entry, stop-loss, and take-profit.
- Extract additional context including confluence, risk sizing, volatility, and trend information when supplied.
- Generate annotated PNG signal cards with entry, stop-loss, target, and estimated reward-to-risk ratio.
- Publish urgent push notifications through an ntfy endpoint.
- Reply to Telegram messages with generated signal cards.
- Optionally run a separate Telegram-forwarding process using Telethon.

## Architecture

```text
Signal message
     |
     v
Telegram listener (signal_alerter.py)
     |
     +--> Parse trading signal --> ntfy push notification
     |
     +--> Generate annotated chart --> Telegram reply

Optional Telegram source bot
     |
     v
Forwarder (forwarder.py) --> Bridge group
```

## Technology

Python, python-telegram-bot, Telethon, httpx, asyncio, Matplotlib, NumPy, and python-dotenv.

## Setup

**Requirements:** Python compatible with the versions of the libraries listed in `requirements.txt`; a Telegram bot token; optional Telegram API application credentials for the forwarder; and an ntfy topic or server.

1. Clone the repository and enter the project directory.
2. Create a virtual environment and install the dependencies.
3. Copy `.env.example` to `.env` and supply your own configuration. **Never commit `.env`.**
4. Start the listener or optional forwarder.

```bash
git clone https://github.com/nftinfluencer/smc-alerter.git
cd smc-alerter
python -m venv .venv
source .venv/bin/activate  # Windows PowerShell: .venv\Scripts\Activate.ps1
pip install -r requirements.txt
cp .env.example .env       # Windows: copy .env.example .env
python signal_alerter.py
```

For the optional forwarding component, configure `TELEGRAM_API_ID`, `TELEGRAM_API_HASH`, `BRIDGE_GROUP_ID`, and `MODEL_BOT_USERNAME` first, then run:

```bash
python forwarder.py
```

The forwarder may need Telegram sign-in or a properly secured session string depending on how it is deployed.

## Expected signal format

The parser expects messages with a pair and order type/direction similar to:

```text
BTCUSDT: LIMIT BUY @ 82000
SL: 81750 | TP: 82750
Confluence: 7/10
Size: 1% risk
Mode: PAPER
Vol: Normal | Trend: bullish
```

This is an *illustrative input format*, not a trading recommendation.

## Project layout

| File | Purpose |
| --- | --- |
| `signal_alerter.py` | Signal parsing, notifications, image generation, Telegram listener |
| `forwarder.py` | Optional Telegram message forwarding |
| `requirements.txt` | Python dependencies |
| `Procfile` | Process deployment configuration |
| `.env.example` | Safe configuration template |

## Security and limitations

- Never commit bot tokens, Telegram session strings, API hashes, or credentials. The `.gitignore` excludes local environment and session files.
- Use replacement credentials if any were previously exposed.
- Notification delivery and Telegram functionality require properly configured external services.
- This is a project showcase, not a production security audit. Unit tests and automated CI are not yet included in this repository.

## Future improvements

- Add automated tests for signal parsing and message handling.
- Validate input fields and handle malformed or incomplete signals more explicitly.
- Add configurable logging, retry/backoff behavior, and deployment checks.
- Include example signal-card screenshots without revealing private account information.
