from pyrogram.types import InlineKeyboardButton, InlineKeyboardMarkup, CallbackQuery
import random
from pyrogram import Client, filters
import string
import time 

api_id = "26238721"
api_hash = "64c932f9c9c5c4af41a2a6940cfc0c9a"
bot_token = "6369790377:AAGqr8xyuG0WAjBybkatgsCHjjYp3VPTJAk"
app = Client("rvhkvdlBOT", api_id=api_id, api_hash=api_hash, bot_token=bot_token)


def add_underscore(name):
    name_list = list(name)
    name_list.insert(1, "_")
    name_list.insert(3, "_")
    new_name = "".join(name_list)
    return new_name


def generate_username(length):
    characters = string.ascii_lowercase
    return add_underscore("".join(random.choice(characters) for _ in range(length)))


def check_username(username):
    try:
        app.get_chat(username)
        return False  # اسم المستخدم مستخدم بالفعل
    except Exception:
        return True  # اسم المستخدم غير مستخدم


def send_usernames_to_bot(bot_id, usernames):
    for username in usernames:
        app.send_message(bot_id, f"اسم المستخدم الجديد: @{username}")


@app.on_message()
def handle_message(client, message):
    if message.text == "/start":
        # إنشاء لوحة مفاتيح مستجيبة
        reply_markup = InlineKeyboardMarkup([
            [InlineKeyboardButton("اسم مستخدم ثلاثي",callback_data="triple_username")],
            [InlineKeyboardButton("اسم مستخدم رباعي", callback_data="quad_username")],
            [InlineKeyboardButton("اسم مستخدم خماسي", callback_data="penta_username")]])
        client.send_message(
            message.chat.id, "ما نوع اسم المستخدم الذي تريد إنشاؤه؟", reply_markup=reply_markup
        )


@app.on_callback_query()
def handle_button_click(client, callback_query):
  while True:
    if callback_query.data == "triple_username":
        usernames = [generate_username(3) for _ in range(50)]
    elif callback_query.data == "quad_username":
        usernames = [generate_username(4) for _ in range(50)]
    elif callback_query.data == "penta_username":
        usernames = [generate_username(5) for _ in range(50)]
    available_usernames = [username for username in usernames if check_username(username)]
    send_usernames_to_bot(-1001617936708, available_usernames[:50])
    client.send_message(
        callback_query.from_user.id, "تم إنشاء أسماء المستخدمين بنجاح!"
    )
    callback_query.answer()
    time.sleep(5)



print("run...")
app.run()
