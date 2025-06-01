future-stick-bot full project code

================= config.py =================

Add your bot token and admin chat ID here

BOT_TOKEN = '7639421680:AAGQy5V3Mr75O4lr0oMVOmN1w11eRMtW1dI' ADMIN_CHAT_ID = 6040563017  # Replace with your own Telegram user ID

================= rules.py =================

def violates_rules(text): if not text: return False bad_words = ['hack', 'cheat', 'mod menu', 'script', 'esp', 'aimbot'] for word in bad_words: if word in text.lower(): return True return False

================= scheduler.py =================

import threading import time from telegram import Bot

def schedule_delete(bot: Bot, chat_id, message_id, delay_seconds): def delete_message(): time.sleep(delay_seconds) try: bot.delete_message(chat_id=chat_id, message_id=message_id) except Exception: pass

threading.Thread(target=delete_message).start()

================= bot.py =================

from telegram import Update, Bot from telegram.ext import Updater, MessageHandler, Filters, CallbackContext from config import BOT_TOKEN, ADMIN_CHAT_ID from rules import violates_rules from scheduler import schedule_delete

def handle_message(update: Update, context: CallbackContext): message = update.effective_message chat_id = message.chat_id message_id = message.message_id text = message.text or message.caption

if violates_rules(text):
    context.bot.delete_message(chat_id=chat_id, message_id=message_id)
    context.bot.send_message(chat_id=ADMIN_CHAT_ID,
                             text=f"🚫 Deleted a violating message in {chat_id}\nContent: {text}")
else:
    # Delete safe messages after 3 days (259200 seconds)
    schedule_delete(context.bot, chat_id, message_id, 259200)

def main(): updater = Updater(token=BOT_TOKEN, use_context=True) dp = updater.dispatcher

dp.add_handler(MessageHandler(Filters.text | Filters.caption, handle_message))

updater.start_polling()
updater.idle()

if name == 'main': main()

================= requirements.txt =================

python-telegram-bot==13.15

================= README.md =================

Future Stick Bot

A Telegram bot that auto-deletes messages after 3 days and removes messages that violate Telegram's ToS (like hacks, cheats, etc).

Features

Auto-delete violating messages instantly

Auto-delete safe messages after 3 days

No database used (fully in-memory)

Notifies admin on violations


How to Run

1. Replace your bot token and admin ID in config.py


2. Install dependencies:



pip install -r requirements.txt

3. Run the bot:



python bot.py

Deploy to Render.com

Build Command:


pip install -r requirements.txt

Start Command:


python bot.py

