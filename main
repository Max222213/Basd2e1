import logging
import random
import asyncio
from pathlib import Path 
from aiogram import Bot, Dispatcher, types
from aiogram.filters import BaseFilter, Command
from aiogram.types import Message, FSInputFile 

# Настройка логирования
logging.basicConfig(level=logging.INFO)

# --- КОНСТАНТЫ ---
# Определяем базовую директорию скрипта для надежности
BASE_DIR = Path(__file__).resolve().parent

# Вставьте сюда ВАШ НОВЫЙ СЕКРЕТНЫЙ ТОКЕН!
API_TOKEN = '8460276527:AAGVgUzATemFlHCeVqLEQTXvevE-lO0wfCQ'
PHOTO_FILENAME = str(BASE_DIR / 'photo_2025-11-25_20-24-05.jpg')

# НОВЫЕ КОНСТАНТЫ ДЛЯ РЕКЛАМЫ
VIDEO_AD_FOLDER = 'video_ads' 
VIDEO_AD_FILENAME = str(BASE_DIR / VIDEO_AD_FOLDER / 'advertisement_video.mp4') 

# Сокращенный текст рекламы (используем HTML)
SHORT_AD_CAPTION = (
    "📈 <b>Твоя рыбалка</b> – твои миллионы!\n"
    "<b>ЩУКАКОМБАТ</b> – самый агрессивный и прибыльный кликер 2025.\n"
    "\n"
    "⚙️ <b>Автоматика:</b> Купи 'Щучью стаю' и наблюдай, как баланс растет сам.\n"
    "🔥 <b>Крит:</b> Вкачай 'Острые зубы' и увеличивай прибыль в два раза чаще.\n"
    "📜 <b>Статус:</b> Оформляй ценные бумаги, чтобы получать пассивный ЩукаКоин.\n"
    "\n"
    "🐟 <b>Запустить Щуку:</b> <a href=\"https://t.me/IIIUKINA_BOT\">@IIIUKINA_BOT</a> (Там нажми на кнопку Щука!)"
)

# Триггеры для ЦИТАТ (старый список)
QUOTE_TRIGGERS = [
    "щукина",
    "щука",
    "шлюхина",
    "шлюха",
    "сукина"
]

# Триггер для ВИДЕО-РЕКЛАМЫ (новый, точный)
AD_TRIGGER_PHRASE = "щука комбат"

GORE_OT_UMA_QUOTES = [
    "А судьи кто",
    "Счастливые часов не наблюдают.",
    "Служить бы рад, прислуживаться тошно",
    "Ах, злые языки страшнее пистолета.",
    "Набор на службу по контракту в МО РФ",
    "🎣 <b>Откройте все секреты удобства!</b>\n\nНе просто цитата, а совет: зайдите в нашего бота и нажмите на ту самую голубую кнопку «Щука» слева. Это наш новый секретный раздел, где всё устроено для вашего комфорта."
]

# ------------------

def format_quote_bold(quote: str) -> str:
    """Форматирует цитату, делая каждое слово жирным с помощью <b> HTML-тега."""
    words = quote.split()
    formatted_words = [f"<b>{word}</b>" for word in words]
    return " ".join(formatted_words)


def get_random_quote() -> str:
    """Возвращает случайную цитату, отформатированную жирным (HTML)."""
    quote = random.choice(GORE_OT_UMA_QUOTES)
    if '<' in quote:
        return quote
    return format_quote_bold(quote)


# Кастомный класс-фильтр для aiogram v3 для ЦИТАТ
class QuoteTriggerFilter(BaseFilter):
    """Проверяет, содержит ли сообщение любое слово из списка QUOTE_TRIGGERS."""

    def __init__(self, trigger_words: list):
        self.trigger_words = [w.lower() for w in trigger_words]

    async def __call__(self, message: Message) -> bool:
        if message.text:
            text_lower = message.text.lower()
            for word in self.trigger_words:
                if word in text_lower:
                    return True
        return False

# НОВЫЙ КЛАСС-ФИЛЬТР для ВИДЕО-РЕКЛАМЫ
class AdTriggerFilter(BaseFilter):
    """Проверяет, содержит ли сообщение точную фразу AD_TRIGGER_PHRASE."""

    async def __call__(self, message: Message) -> bool:
        if message.text:
            # Ищем точное совпадение с фразой
            return AD_TRIGGER_PHRASE in message.text.lower()
        return False


# НОВЫЙ ОБРАБОТЧИК: Отправляет ТОЛЬКО видео-рекламу
async def send_ad_video(message: Message):
    """Отвечает на сообщение с триггером "щука комбат", отправляя только рекламное видео."""

    try:
        video_file = FSInputFile(VIDEO_AD_FILENAME) 

        await message.answer_video(
            video=video_file,
            caption=SHORT_AD_CAPTION,
            parse_mode="HTML"
        )
        logging.info(f"Отправлено рекламное видео в чат {message.chat.id} по триггеру: {AD_TRIGGER_PHRASE}")

    except FileNotFoundError:
        error_message = f"Ошибка: Файл рекламного видео не найден по пути {VIDEO_AD_FILENAME}. Проверьте структуру папок."
        await message.answer(error_message)
        logging.error(error_message)
    
    except Exception as e:
        logging.error(f"Произошла ошибка при отправке видео: {e}")


# МОДИФИЦИРОВАННЫЙ ОБРАБОТЧИК: Отправляет ТОЛЬКО фото с цитатой
async def send_photo_with_quote(message: Message):
    """Отвечает на сообщение с триггером, отправляя только фото и цитату."""

    caption_text = get_random_quote()

    try:
        photo_file = types.FSInputFile(PHOTO_FILENAME)

        await message.reply_photo(
            photo=photo_file,
            caption=caption_text,
            parse_mode="HTML"
        )
        logging.info(f"Отправлено фото и цитата в чат {message.chat.id} по триггеру: {message.text}")

    except FileNotFoundError:
        error_message = f"Ошибка: Файл фото не найден по пути {PHOTO_FILENAME}. Проверьте путь."
        await message.reply(error_message)
        logging.error(error_message)

    except Exception as e:
        logging.error(f"Произошла ошибка при отправке фото: {e}")


# Обработчик команды /leave (без изменений)
async def leave_chat_command(message: Message, bot: Bot):
    """Обрабатывает команду /leave, заставляя бота покинуть чат."""
    chat_id = message.chat.id
    await message.reply("Хорошо, выполняю команду /leave. До свидания!")
    try:
        await bot.leave_chat(chat_id)
        logging.info(f"Бот покинул чат с ID: {chat_id}")
    except Exception as e:
        logging.error(f"Ошибка при попытке покинуть чат {chat_id}: {e}")
        await message.reply("Не удалось покинуть чат. Проверьте мои права.")


# Главная функция для асинхронного запуска
async def main():
    bot = Bot(token=API_TOKEN)
    dp = Dispatcher()

    # РЕГИСТРАЦИЯ ОБРАБОТЧИКОВ:
    
    # 1. Регистрация точного триггера "щука комбат" (самый высокий приоритет)
    dp.message.register(send_ad_video, AdTriggerFilter())
    
    # 2. Регистрация широкого триггера (второй приоритет)
    # Если сообщение содержит "щука комбат", оно будет обработано первым, 
    # и до этого обработчика дело не дойдет.
    dp.message.register(send_photo_with_quote, QuoteTriggerFilter(QUOTE_TRIGGERS))
    
    # 3. Регистрация команды /leave
    dp.message.register(leave_chat_command, Command("leave"))

    print("Бот (aiogram v3) запущен и готов к работе...")
    await dp.start_polling(bot, skip_updates=True)


if __name__ == '__main__':
    try:
        asyncio.run(main())
    except KeyboardInterrupt:
        print("Бот остановлен вручную.")
