# Лабораторная работа №7: Создание многоконтейнерного приложения

## Цель работы

Ознакомиться с работой многоконтейнерного приложения на базе docker-compose.

## Задание

Создать php-приложение на базе трех контейнеров: nginx, php-fpm, mariadb, используя docker-compose.

## Подготовка

☑ Для выполнения данной работы я убедился, что на моем компьютере установлен Docker.
Работа выполнялась на базе лабораторной работы №5.

## Выполнение

☑ Я создал репозиторий `containers07` и скопировал его себе на компьютер.

### Сайт на php

В директории `containers07` я создал директорию mounts/site. В эту директорию я переписал сайт на php, который был создан в рамках предмета по php.
Я выбрал первую часть второй лабораторной по php.

![img](/images/image_1.png)

### Конфигурационные файлы

Я создал файл `.gitignore` в корне проекта и добавил в него строки:

```
# Ignore files and directories
mounts/site/*
```

![img](/images/image_2.png)

В директории `containers07` я создал файл `nginx/default.conf` со следующим содержимым:

![img](/images/image_3.png)

В директории `containers07` я создал файл `docker-compose.yml` со следующим содержимым:

![img](/images/image_4.png)

Я создал файл `mysql.env` в корне проекта и добавил в него строки:

![img](/images/image_5.png)

### Запуск и тестирование

Я запустил контейнеры командой:

![img](/images/image_6.png)

Я проверил работу сайта в браузере, перейдя по адресу `http://localhost`. Если отображалась базовая страница nginx, я перегрузил страницу.

![img](/images/image_7.png)

### Ответы на вопросы

1. **В каком порядке запускались контейнеры?**  
   Контейнеры запускались в следующем порядке: сначала поднимался контейнер `database` (mysql), так как он не зависит от других сервисов. Затем запускались контейнеры `backend` (php-fpm) и `frontend` (nginx), так как они зависят от сети `internal` и монтируемых томов, но не имеют явных зависимостей друг от друга. Docker-compose определял порядок автоматически на основе конфигурации.

2. **Где хранились данные базы данных?**  
   Данные базы данных хранились в именованном томе `db_data`, который был указан в секции `volumes` файла `docker-compose.yml`. Этот том монтировался в контейнер `database` по пути `/var/lib/mysql`.

3. **Как назывались контейнеры проекта?**  
   Контейнеры проекта назывались:  
   - `frontend` (nginx),  
   - `backend` (php-fpm),  
   - `database` (mysql).  
   Эти имена соответствовали названиям сервисов, заданным в файле `docker-compose.yml`.

4. **Мне необходимо было добавить еще один файл `app.env` с переменной окружения `APP_VERSION` для сервисов backend и frontend. Как я это сделал?**  
   Я создал файл `app.env` в корне проекта со следующей строкой:  

   ```
   APP_VERSION=1.0.0
   ```

   Затем я обновил файл `docker-compose.yml`, добавив `env_file` для сервисов `frontend` и `backend`:

   ```yaml
   services:
     frontend:
       image: nginx:1.19
       volumes:
         - ./mounts/site:/var/www/html
         - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
       ports:
         - "80:80"
       networks: 
         - internal
       env_file:
         - app.env #тут
     backend:
       image: php:7.4-fpm
       volumes:
         - ./mounts/site:/var/www/html
       networks:
         - internal
       env_file:
         - mysql.env
         - app.env # и тут
   ```

   После этого я перезапустил контейнеры командой `docker-compose up -d`, чтобы применить изменения. Переменная `APP_VERSION` стала доступна в окружении сервисов `frontend` и `backend`.

   ![img](/images/image_9.png)

   ## Вывод

    В ходе выполнения лабораторной работы я успешно создал многоконтейнерное приложение на базе Docker Compose, включающее контейнеры nginx, php-fpm и mysql. Я освоил настройку конфигурационных файлов, управление томами и переменными окружения, а также запуск и тестирование приложения. Работа позволила мне глубже понять принципы взаимодействия контейнеров и использование Docker Compose для оркестрации приложений. Все поставленные задачи были выполнены, а полученные знания помогут в дальнейшем создании подобных проектов.

  ## Библиография
  
  [https://moodle.usm.md/course](https://moodle.usm.md/course/view.php?id=6806)
