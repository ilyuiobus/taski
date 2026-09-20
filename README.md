# Taski

Taski — приложение для планирования своих задач. Пользователь может создавать и управлять задачами через веб-интерфейс.

Проект состоит из backend на Django, frontend и gateway на Nginx. Для запуска и развёртывания используются Docker и Docker Compose. Сборка Docker-образов и деплой выполняются автоматически с помощью GitHub Actions.

## Возможности

- создание задач;
- просмотр задач;
- редактирование задач;
- удаление задач;
- работа с API;
- административная панель Django.

## Стек технологий

### Backend

- Python 3.12;
- Django 5.1.1;
- Django REST Framework;
- PostgreSQL;
- django-cors-headers;
- Gunicorn;
- python-dotenv.

### Frontend

- React;
- Node.js;
- npm.

### Инфраструктура

- Docker;
- Docker Compose;
- Nginx;
- GitHub Actions;
- Docker Hub;
- Telegram для уведомлений о деплое.

## Структура проекта

```text
taski/
├── backend/                   # Backend на Django
├── frontend/                 # Frontend
├── gateway/                  # Nginx gateway
├── docker-compose.yml        # Локальный запуск
├── docker-compose.production.yml
├── .github/
│   └── workflows/
│       └── main.yml          # CI/CD workflow
└── README.md
```

## Запуск проекта локально

Для локального запуска необходимы Docker и Docker Compose.

В корне проекта создайте файл `.env`:

```dotenv
POSTGRES_USER=volinaiiv
POSTGRES_PASSWORD=123456
POSTGRES_DB=taski

DB_HOST=db
DB_PORT=5432

USE_SQLITE=false
SECRET_KEY=your-secret-key
DEBUG=false
ALLOWED_HOSTS=localhost,127.0.0.1
```

Для локального запуска на SQLite укажите:

```dotenv
USE_SQLITE=true
```

В этом случае Django будет использовать SQLite вместо PostgreSQL.

Запустите проект:

```bash
docker compose up -d --build
```

Gateway будет доступен по адресу:

```text
http://localhost:8000
```

Для остановки контейнеров:

```bash
docker compose down
```

## Переменные окружения

Используются следующие переменные:

| Переменная | Назначение |
|---|---|
| `POSTGRES_USER` | пользователь PostgreSQL |
| `POSTGRES_PASSWORD` | пароль PostgreSQL |
| `POSTGRES_DB` | имя базы данных PostgreSQL |
| `DB_HOST` | адрес базы данных |
| `DB_PORT` | порт PostgreSQL |
| `USE_SQLITE` | выбор SQLite (`true`) или PostgreSQL (`false`) |
| `SECRET_KEY` | секретный ключ Django |
| `DEBUG` | режим отладки Django |
| `ALLOWED_HOSTS` | список разрешённых хостов Django |

Пример переменных окружения находится в файле `.env.example`.

Файл `.env` содержит локальные значения переменных окружения и не должен добавляться в репозиторий.

## Тестирование

Для локального запуска тестов создайте виртуальное окружение:

```bash
python -m venv venv
```

Для Windows PowerShell:

```powershell
venv\Scripts\Activate.ps1
```

Установите зависимости:

```bash
pip install -r backend/requirements.txt
```

Проверка качества Python-кода:

```bash
flake8 backend/
```

## CI/CD

После push в ветку `main` GitHub Actions автоматически:

1. устанавливает зависимости;
2. запускает проверку backend;
3. собирает Docker-образы backend, frontend и gateway;
4. публикует Docker-образы в Docker Hub;
5. копирует `docker-compose.production.yml` на сервер;
6. создаёт `.env` на сервере из переменных окружения и GitHub Secrets;
7. загружает актуальные Docker-образы;
8. запускает контейнеры;
9. выполняет миграции базы данных;
10. собирает статические файлы Django;
11. копирует собранные статические файлы в общий Docker volume;
12. проверяет запущенные контейнеры;
13. отправляет сообщение об успешном деплое в Telegram.

Docker-образы проекта:

```text
volinaiiv/taski_backend
volinaiiv/taski_frontend
volinaiiv/taski_gateway
```

Workflow:

```text
.github/workflows/main.yml
```

## Production-развёртывание

Production-версия запускается с помощью:

```text
docker-compose.production.yml
```

В production используются готовые Docker-образы из Docker Hub.

Сервисы проекта:

```text
db        → PostgreSQL
backend   → Django
frontend  → frontend
gateway   → Nginx
```

Во время деплоя автоматически выполняются:

```bash
python manage.py migrate
python manage.py collectstatic --noinput
```

После сборки статики содержимое `collected_static` копируется в общий Docker volume, который используется gateway для раздачи статических файлов.

## Доступ к проекту

Taski:

https://volinaiivtaski.duckdns.org

## Автор

GitHub: https://github.com/ilyuiobus

Проект выполнен в рамках учебного проекта Яндекс Практикума.
