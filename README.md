[![](https://github.com/DociDog/api_yamdb/actions/workflows/yamdb_workflow.yml/badge.svg)](https://github.com/DociDog/api_yamdb/actions/workflows/yamdb_workflow.yml)

## Описание
Настроены для приложения Continuous Integration и Continuous Deployment: автоматический запуск тестов, обновление образов на Docker Hub, автоматический деплой на боевой сервер при пуше в главную ветку main.

Проект Yamdb_final создан для демонстрации методики DevOps (Development Operations) и идеи Continuous Integration (CI),
суть которых заключается в интеграции и автоматизации следующих процессов:
* синхронизация изменений в коде
* сборка, запуск и тестерование приложения в среде, аналогичной среде боевого сервера
* деплой на сервер после успешного прохождения всех тестов
* уведомление об успешном прохождении всех этапов

## Начало

Клонирование проекта:
```
HTTPS - git clone https://github.com/DociDog/yamdb_final.git
SSH - git clone git@github.com:DociDog/yamdb_final.git
GitHub CLI - git clone gh repo clone DociDog/yamdb_final
```
Для добавления файла .env с настройками базы данных на сервер необходимо:

* Установить соединение с сервером по протоколу ssh:
    ```
    ssh username@server_address
    ```
    Где username - имя пользователя, под которым будет выполнено подключение к серверу.
    
    server_address - IP-адрес сервера или доменное имя.
    

* В домашней директории проекта
    Создать папку www/:
    ```
    mkdir www
    ```
    В ней создать папку yamdb_final/:
    ```
    mkdir www/yamdb_final
    ```
    В папке yamdb_final создать файл .env:
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
Комманда git push является триггером workflow проекта.
При выполнении команды git push запустится набор блоков комманд jobs (см. файл yamdb_workflow.yaml).
Последовательно будут выполнены следующие блоки:
* tests - тестирование проекта на соответствие PEP8 и тестам pytest.
* build_and_push_to_docker_hub - при успешном прохождении тестов собирается образ (image) для docker контейнера 
и отправлятеся в DockerHub
* deploy - после отправки образа на DockerHub начинается деплой проекта на сервере.
Происходит копирование следующих файлов с репозитория на сервер:
  - docker-compose.yaml, необходимый для сборки трех контейнеров:
    + postgres - контейнер базы данных
    + web - контейнер Django приложения + wsgi-сервер gunicorn
    + nginx - веб-сервер
  - nginx/default.conf - файл кофигурации nginx сервера
  - static - папка со статическими файлами проекта
  
  После копировния происходит установка docker и docker-compose на сервере
  и начинается сборка и запуск контейнеров.
* send_message - после сборки и запуска контейнеров происходит отправка сообщения в 
  телеграм об успешном окончании workflow
  
Для обращения к API проекта:


* http://smyamdbfinal.students.nomoredomains.icu/api/v1/auth/token/
* http://smyamdbfinal.students.nomoredomains.icu/api/v1/users/
* http://smyamdbfinal.students.nomoredomains.icu/api/v1/categories/
* http://smyamdbfinal.students.nomoredomains.icu/api/v1/genres/
* http://smyamdbfinal.students.nomoredomains.icu/api/v1/titles/
* http://smyamdbfinal.students.nomoredomains.icu/api/v1/titles/{title_id}/reviews/
* http://smyamdbfinal.students.nomoredomains.icu/api/v1/titles/{title_id}/reviews/{review_id}/
* http://smyamdbfinal.students.nomoredomains.icu/api/v1/titles/{title_id}/reviews/{review_id}/comments/

