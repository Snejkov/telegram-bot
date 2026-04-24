# Инструкция по запуску бота RemnaWave Bedolaga

## 1. Подключение к серверу

```bash
ssh root@194.87.196.171
# пароль: V1JhG9oO5Z1k4Q4
```

## 2. Настройка переменных окружения

Отредактируйте файл `/opt/telegram-bot/.env`:

```bash
cd /opt/telegram-bot
nano .env
```

**Обязательные параметры:**
```env
BOT_TOKEN=        # Токен бота от @BotFather
ADMIN_IDS=        # Ваш Telegram ID (например 123456789)
REMNAWAVE_API_URL=https://ваша_панель.com
REMNAWAVE_API_KEY= # API ключ от панели RemnaWave
```

**Важные параметры для работы:**
```env
DATABASE_MODE=auto
POSTGRES_HOST=postgres
POSTGRES_PORT=5432
POSTGRES_DB=remnawave_bot
POSTGRES_USER=remnawave_user
POSTGRES_PASSWORD=secure_password_123
REDIS_URL=redis://redis:6379/0
TZ=Europe/Moscow
```

## 3. Запуск бота

```bash
cd /opt/telegram-bot

# Остановить старые контейнеры (если есть)
docker compose down

# Запустить бота
docker compose up -d --build

# Проверить статус
docker compose ps

# Посмотреть логи
docker compose logs bot --tail=100
```

## 4. Получение Telegram ID

1. Напишите боту @userinfobot или @getidsbot
2. Скопируйте ваш числовой ID

## 5. Получение API ключа RemnaWave

1. Зайдите в панель RemnaWave
2. Найдите раздел API или настройки
3. Скопируйте API ключ

## 6. Проверка работы

После запуска откройте Telegram и нажмите `/start` в боте.

## 7. Команды управления

```bash
# Перезапустить бота
docker compose restart bot

# Остановить
docker compose down

# Обновить код (из GitHub)
cd /opt/telegram-bot
git pull origin main
docker compose up -d --build

# Просмотр логов в реальном времени
docker compose logs -f bot

# Зайти в контейнер
docker exec -it remnawave_bot bash
```

## 8. Возможные проблемы

**Контейнер не запускается:**
```bash
docker compose logs bot
```

**Проверить статус всех контейнеров:**
```bash
docker compose ps -a
```

**Пересобрать с нуля:**
```bash
docker compose down -v
docker compose up -d --build
```

## 9. Настройка webhook (опционально)

Если бот не отвечает на команды:
```bash
# Проверить webhook
docker exec remnawave_bot python -c "from app.config import settings; print(settings.WEBHOOK_URL)"

# Установить webhook
docker exec remnawave_bot python -c "
import asyncio
from app.bot import bot
asyncio.run(bot.set_webhook('ВАШ_WEBHOOK_URL'))
"
```

## 10. Настройка домена (для webhook)

Рекомендуется использовать CloudFlare Tunnel или Nginx Proxy Manager
для проброса HTTPS на localhost:8080