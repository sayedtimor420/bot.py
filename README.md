from aiogram import Bot, Dispatcher, types
from aiogram.types import ReplyKeyboardMarkup, KeyboardButton
from aiogram.utils import executor
import logging

TOKEN = "7565786901:AAEp6BHSs8EbQimFHuhCFP6kG4X91K1IzaA"
WALLET_ADDRESS = "0x4cf647ec9f76b148a593f83cf3d7be93b89cf5df"

bot = Bot(token=TOKEN)
dp = Dispatcher(bot)
logging.basicConfig(level=logging.INFO)

# دکمه‌های زبان
language_menu = ReplyKeyboardMarkup(resize_keyboard=True)
language_menu.add(KeyboardButton("🇮🇷 فارسی"), KeyboardButton("🇬🇧 English"))

# دکمه‌های اصلی
main_menu_fa = ReplyKeyboardMarkup(resize_keyboard=True)
main_menu_fa.add(KeyboardButton("📊 سیگنال"), KeyboardButton("💰 مدیریت سرمایه"))

main_menu_en = ReplyKeyboardMarkup(resize_keyboard=True)
main_menu_en.add(KeyboardButton("📊 Signal"), KeyboardButton("💰 Investment Management"))

def get_back_button():
    return ReplyKeyboardMarkup(resize_keyboard=True).add(KeyboardButton("🔙 بازگشت"))

def get_back_button_en():
    return ReplyKeyboardMarkup(resize_keyboard=True).add(KeyboardButton("🔙 Back"))

def get_payment_button():
    return ReplyKeyboardMarkup(resize_keyboard=True).add(KeyboardButton("✅ پرداخت انجام شد"), KeyboardButton("🔙 بازگشت"))

def get_payment_button_en():
    return ReplyKeyboardMarkup(resize_keyboard=True).add(KeyboardButton("✅ Payment Done"), KeyboardButton("🔙 Back"))

@dp.message_handler(commands=['start'])
async def start(message: types.Message):
    await message.answer("سلام! لطفاً یکی از گزینه‌های زیر را انتخاب کنید:\n\nHello! Please choose one of the options below:", reply_markup=language_menu)

@dp.message_handler(lambda message: message.text == "🇮🇷 فارسی")
async def choose_farsi(message: types.Message):
    await message.answer("زبان فارسی انتخاب شد.", reply_markup=main_menu_fa)

@dp.message_handler(lambda message: message.text == "🇬🇧 English")
async def choose_english(message: types.Message):
    await message.answer("English language selected.", reply_markup=main_menu_en)

@dp.message_handler(lambda message: message.text == "📊 سیگنال" or message.text == "📊 Signal")
async def signal_info(message: types.Message):
    if message.text == "📊 سیگنال":
        await message.answer(f"💡 *پکیج سیگنال‌ها:*\n📅 هفته‌ای 5 روز\n📈 روزانه 4 سیگنال دقیق طلا\n💵 هزینه: 50$ در ماه\n\n💳 لطفاً مبلغ را به آدرس زیر واریز کنید و اسکرین‌شات ارسال نمایید:\n```\n{WALLET_ADDRESS}```", parse_mode="Markdown", reply_markup=get_payment_button())
    else:
        await message.answer(f"💡 *Signal Package:*\n📅 5 days a week\n📈 4 accurate gold signals daily\n💵 Cost: 50$ per month\n\n💳 Please send payment to the address below and send the screenshot:\n```\n{WALLET_ADDRESS}```", parse_mode="Markdown", reply_markup=get_payment_button_en())

@dp.message_handler(lambda message: message.text == "✅ پرداخت انجام شد" or message.text == "✅ Payment Done")
async def payment_confirmation(message: types.Message):
    if message.text == "✅ پرداخت انجام شد":
        await message.answer("✅ لطفاً اسکرین‌شات پرداخت خود را ارسال کنید.", reply_markup=get_back_button())
    else:
        await message.answer("✅ Please send the screenshot of your payment.", reply_markup=get_back_button_en())

@dp.message_handler(content_types=types.ContentType.PHOTO)
async def handle_screenshot(message: types.Message):
    await message.answer("📩 اسکرین‌شات دریافت شد! پس از بررسی، حساب شما فعال خواهد شد.", reply_markup=main_menu_fa)
    await message.answer("📩 Screenshot received! After verification, your account will be activated.", reply_markup=main_menu_en)

@dp.message_handler(lambda message: message.text == "💰 مدیریت سرمایه" or message.text == "💰 Investment Management")
async def investment_info(message: types.Message):
    if message.text == "💰 مدیریت سرمایه":
        await message.answer("📌 لطفاً اطلاعات حساب بروکر خود را ارسال کنید (ایمیل یا کد بروکر).", reply_markup=get_back_button())
    else:
        await message.answer("📌 Please send your broker account details (email or broker code).", reply_markup=get_back_button_en())

@dp.message_handler(lambda message: message.text == "🔙 بازگشت" or message.text == "🔙 Back")
async def go_back(message: types.Message):
    if message.text == "🔙 بازگشت":
        await start(message)
    else:
