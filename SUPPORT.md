# Установка

Клонирование репозитория:
```bash
sudo git clone --depth 1 https://github.com/EugeneTomato/panel.git /opt/pasarguard/panel
```

Сборка фронта:
```bash
cd /opt/pasarguard/panel/dashboard/
bun install
bun run build --outDir build --assetsDir statics
cp build/index.html build/404.html
```

Запуск установочного скрипта:
```bash
curl -fsSL https://github.com/EugeneTomato/scripts/raw/main/pasarguard.sh -o /tmp/pg.sh \
  && sudo bash /tmp/pg.sh install
```

# Запуск проекта в dev-режиме

Для начала клонируйте проект в удобный для вас каталог, а затем перейдите в проект

Настройка .env файла:
```bash
cp .env.example .env
```

Содержимое .env файла должно быть следующим (порт можете указать какой удобно):
```ini
UVICORN_HOST = "0.0.0.0"
UVICORN_PORT = 8000

SUDO_USERNAME = "admin"
SUDO_PASSWORD = "admin"

SQLALCHEMY_DATABASE_URL = "sqlite+aiosqlite:///db.sqlite3"

DOCS=True
DEBUG=True
JWT_ACCESS_TOKEN_EXPIRE_MINUTES=1440
```

Конфигурационный файл, хранит в себе имя и пароль для администратора панели. Измените эти параметры при необходимости

Создание базы данных:
```bash
uv run alembic upgrade heads
```

Сборка фронта:
```bash
cd dashboard
rm -rf node_modules bun.lockb # Если есть (если требуется пересобрать фронт, то перед сборкой необходимо удалить эти файлы)
bun add -D ajv@8.17.1
bun install
cd ../ # Для возвращения в корень проекта
```

Запуск проекта:
```bash
make run # Из корня проекта
```

При добавлении новых полей в бд, вы вносите изменения в файлы по пути /app/db (к примеру в /app/db/models.py, скорее всего вы ограничитесь только этим). После необходимо сгенерировать (генерируется в /app/db/migrations/versions/) специальный файл изменения бд (см. формат команды в оф. документации):
```bash
uv run alembic revision --autogenerate -m "Add suspend_until field to users" # Из корня проекта
uv run alembic upgrade heads
```

Для проверки текущей версии бд:
```bash
uv run alembic current
```

Для проверки последней версии бд (согласно файлам изменения бд):
```bash
uv run alembic heads
```

Для обновления до версии heads:
```bash
uv run alembic upgrade heads
```