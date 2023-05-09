![workflow status](https://github.com/DociDog/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)

## Описание
API YaMDb собирает отзывы пользователей на различные произведения такие как
фильмы, книги и музыка. Для приложения настроен Continuous Integration (CI) и
Continuous Deployment (CD).

Реализован:
* автоматический запуск тестов;
* обновление образов на DockerHub;
* автоматический деплой на боевой сервер при push-е в главную ветку main.

## Начало

* Клонировать репозиторий, перейти в директорию с проектом:
```
git clone https://github.com/DociDog/yamdb_final.git
```
```
cd api_yamdb
```

* Установить виртуальное окружение, активировать его:
```
python -m venv venv
```
```
. venv/scripts/activate
```

* Перейти в директорию с приложением ```api_yamdb```, установить зависимости:
```
pip install -r requirements.txt
```

* В папке yamdb_final создать файл .env:
    ```
    touch www/yamdb_final/.env
    ```

* Добавить настройки в файл .env:

    Пример добавляемых настроек:
    ```
    DB_ENGINE=django.db.backends.postgresql
    DB_NAME=postgres
    POSTGRES_USER=postgres
    POSTGRES_PASSWORD=postgres
    DB_HOST=postgres
    DB_PORT=5432
    ```

Также необходимо добавить Action secrets в репозитории на GitHub в разделе settings -> Secrets:
* DOCKER_PASSWORD - пароль от DockerHub;
* DOCKER_USERNAME - имя пользователя на DockerHub;
* HOST - ip-адрес сервера;
* SSH_KEY - приватный ssh ключ (публичный должен быть на сервере);
* TELEGRAM_TO - id своего телеграм-аккаунта (можно узнать у @userinfobot, команда /start)
* TELEGRAM_TOKEN - токен бота (получить токен можно у @BotFather, /token, имя бота)

### Проверка работоспособности

Теперь если внести любые изменения в проект и выполнить:
```
git add .
git commit -m "..."
git push
```
При push в ветку main автоматически отрабатывают сценарии:
* *tests* - проверка кода на соответствие стандарту PEP8 и запуск pytest.
Дальнейшие шаги выполняются только если push был в ветку main;
* *build_and_push_to_docker_hub* - сборка и доставка докер-образов на DockerHub
* *deploy* - автоматический деплой проекта на боевой сервер. Выполняется
копирование файлов из DockerHub на сервер;
* *send_message* - отправка уведомления в Telegram.
