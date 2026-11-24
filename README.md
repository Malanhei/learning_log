# Learning Log

Учебный проект на Django: личный журнал обучения. Пользователь регистрируется,
создаёт темы и добавляет по каждой теме записи. Каждый пользователь видит
только свои темы и записи.

## Возможности

- регистрация, вход и выход пользователей;
- создание, просмотр и редактирование тем;
- добавление и редактирование записей внутри темы;
- ограничение доступа к личным данным через `@login_required` и проверку владельца;
- оформление на Bootstrap 4 (`django-bootstrap4`);
- автоматические тесты доступа к темам;
- запуск в Docker через Gunicorn и WhiteNoise.

## Стек

- Python 3.12
- Django 5.2 LTS
- SQLite
- Bootstrap 4 (`django-bootstrap4`)
- Gunicorn, WhiteNoise
- Docker Compose

## Структура проекта

```
learning_log/
├── manage.py
├── requirements.txt
├── Dockerfile
├── compose.yaml
├── learning_log/        # настройки проекта
├── learning_logs/       # темы и записи
└── accounts/            # регистрация, вход, выход
```

## Локальный запуск

Из папки с `manage.py`:

```bash
py -3.12 -m venv ll_env
ll_env\Scripts\activate        # Windows
source ll_env/bin/activate     # Linux / macOS
```

Установка зависимостей и подготовка базы:

```bash
python -m pip install -r requirements.txt
python manage.py migrate
python manage.py runserver
```

Приложение откроется по адресу <http://127.0.0.1:8000/>.

Создание администратора (по желанию):

```bash
python manage.py createsuperuser
```

Административный сайт: <http://127.0.0.1:8000/admin/>.

## Тесты

```bash
python manage.py test
```

Ожидаемый результат: `Ran 4 tests` и `OK`.

## Запуск в Docker

1. Создайте файл `.env` в корне проекта (см. переменные ниже). В репозиторий
   его добавлять нельзя.
2. Соберите и запустите контейнер:

```bash
docker compose up -d --build
```

3. Проверьте приложение по адресу `http://<IP-сервера>:<APP_PORT>/`.

При запуске контейнер автоматически применяет миграции и собирает статические
файлы. База SQLite хранится в именованном томе `learning_log_data`
(внутри контейнера — `/app/data/db.sqlite3`) и сохраняется при пересоздании
контейнера.

Остановка:

```bash
docker compose down
```

Полное удаление вместе с базой (данные будут потеряны):

```bash
docker compose down -v
```

## Переменные окружения (`.env`)

| Переменная | Назначение | Пример |
| --- | --- | --- |
| `DJANGO_SECRET_KEY` | секретный ключ Django; на сервере обязателен длинный случайный | `replace-with-a-long-random-secret` |
| `DJANGO_DEBUG` | режим отладки; на сервере `False` | `False` |
| `DJANGO_ALLOWED_HOSTS` | разрешённые хосты через запятую | `192.168.1.11,localhost,127.0.0.1` |
| `DJANGO_CSRF_TRUSTED_ORIGINS` | доверенные источники для CSRF | `http://192.168.1.11:20010` |
| `DJANGO_USE_HTTPS` | включать secure-cookies при работе через HTTPS | `False` |
| `SQLITE_PATH` | путь к базе внутри контейнера | `/app/data/db.sqlite3` |
| `APP_BIND_IP` | адрес, на котором публикуется порт | `0.0.0.0` |
| `APP_PORT` | внешний порт приложения | `20010` |

Порт `20010` — пример. Используйте порт, выданный преподавателем.

Пример `.env`:

```dotenv
DJANGO_SECRET_KEY=replace-with-a-long-random-secret
DJANGO_DEBUG=False
DJANGO_ALLOWED_HOSTS=192.168.1.11,localhost,127.0.0.1
DJANGO_CSRF_TRUSTED_ORIGINS=http://192.168.1.11:20010
DJANGO_USE_HTTPS=False
SQLITE_PATH=/app/data/db.sqlite3
APP_BIND_IP=0.0.0.0
APP_PORT=20010
```

## Что не попадает в репозиторий

`.env`, виртуальное окружение `ll_env/`, файл базы `db.sqlite3`, каталог
`staticfiles/` и `__pycache__/` исключены через `.gitignore`. На сервере база
создаётся заново, поэтому пробные пользователи и темы переносятся вручную.
