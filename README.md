# Домашнее задание к занятию 5. «Практическое применение Docker»

### Инструкция к выполнению

1. Для выполнения заданий обязательно ознакомьтесь с [инструкцией](https://github.com/netology-code/devops-materials/blob/master/cloudwork.MD) по экономии облачных ресурсов. Это нужно, чтобы не расходовать средства, полученные в результате использования промокода.
3. **Своё решение к задачам оформите в вашем GitHub репозитории.**
4. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.
5. Сопроводите ответ необходимыми скриншотами.

---
## Примечание: Ознакомьтесь со схемой виртуального стенда [по ссылке](https://github.com/netology-code/shvirtd-example-python/blob/main/schema.pdf)

---

## Задача 0
1. Убедитесь что у вас НЕ(!) установлен ```docker-compose```, для этого получите следующую ошибку от команды ```docker-compose --version```
```
Command 'docker-compose' not found, but can be installed with:

sudo snap install docker          # version 24.0.5, or
sudo apt  install docker-compose  # version 1.25.0-1

See 'snap info docker' for additional versions.
```
В случае наличия установленного в системе ```docker-compose``` - удалите его.  
2. Убедитесь что у вас УСТАНОВЛЕН ```docker compose```(без тире) версии не менее v2.24.X, для это выполните команду ```docker compose version```  
###  **Своё решение к задачам оформите в вашем GitHub репозитории!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!**

---

## Задача 1
1. Сделайте в своем GitHub пространстве fork [репозитория](https://github.com/netology-code/shvirtd-example-python).

2. Создайте файл ```Dockerfile.python``` на основе существующего `Dockerfile`:
   - Используйте базовый образ ```python:3.12-slim```
   - Обязательно используйте конструкцию ```COPY . .``` в Dockerfile
   - Создайте `.dockerignore` файл для исключения ненужных файлов
   - Используйте ```CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]``` для запуска
   - Протестируйте корректность сборки
2.1 Используйте multistage сборку вместо single stage.
3. (Необязательная часть, *) Изучите инструкцию в проекте и запустите web-приложение без использования docker, с помощью venv. (Mysql БД можно запустить в docker run).
4. (Необязательная часть, *) Изучите код приложения и добавьте управление названием таблицы через ENV переменную.
---
### ВНИМАНИЕ!
!!! В процессе последующего выполнения ДЗ НЕ изменяйте содержимое файлов в fork-репозитории! Ваша задача ДОБАВИТЬ 5 файлов: ```Dockerfile.python```, ```compose.yaml```, ```.gitignore```, ```.dockerignore```,```bash-скрипт```. Если вам понадобилось внести иные изменения в проект - вы что-то делаете неверно!
---

## Задача 2 (*)
1. Создайте в yandex cloud container registry с именем "test" с помощью "yc tool" . [Инструкция](https://cloud.yandex.ru/ru/docs/container-registry/quickstart/?from=int-console-help)
2. Настройте аутентификацию вашего локального docker в yandex container registry.
3. Соберите и залейте в него образ с python приложением из задания №1.
4. Просканируйте образ на уязвимости.
5. В качестве ответа приложите отчет сканирования.

## Задача 3
1. Изучите файл "proxy.yaml"
2. Создайте в репозитории с проектом файл ```compose.yaml```. С помощью директивы "include" подключите к нему файл "proxy.yaml".
3. Опишите в файле ```compose.yaml``` следующие сервисы: 

- ```web```. Образ приложения должен ИЛИ собираться при запуске compose из файла ```Dockerfile.python``` ИЛИ скачиваться из yandex cloud container registry(из задание №2 со *). Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.5```. Сервис должен всегда перезапускаться в случае ошибок.
Передайте необходимые ENV-переменные для подключения к Mysql базе данных по сетевому имени сервиса ```web``` 

- ```db```. image=mysql:8. Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.10```. Явно перезапуск сервиса в случае ошибок. Передайте необходимые ENV-переменные для создания: пароля root пользователя, создания базы данных, пользователя и пароля для web-приложения.Обязательно используйте уже существующий .env file для назначения секретных ENV-переменных!

2. Запустите проект локально с помощью docker compose , добейтесь его стабильной работы: команда ```curl -L http://127.0.0.1:8090``` должна возвращать в качестве ответа время и локальный IP-адрес. Если сервисы не стартуют воспользуйтесь командами: ```docker ps -a ``` и ```docker logs <container_name>``` . Если вместо IP-адреса вы получаете информационную ошибку --убедитесь, что вы шлете запрос на порт ```8090```, а не 5000.

5. Подключитесь к БД mysql с помощью команды ```docker exec -ti <имя_контейнера> mysql -uroot -p<пароль root-пользователя>```(обратите внимание что между ключем -u и логином root нет пробела. это важно!!! тоже самое с паролем) . Введите последовательно команды (не забываем в конце символ ; ): ```show databases; use <имя вашей базы данных(по-умолчанию virtd, как это указано в .env)>; show tables; SELECT * from requests LIMIT 10;```. Примечание: таблица в БД создается после первого поступившего запроса к приложению.

6. Остановите проект. В качестве ответа приложите скриншот sql-запроса.

## Задача 4
1. Запустите в Yandex Cloud ВМ (вам хватит 2 Гб Ram).
2. Подключитесь к Вм по ssh и установите docker.
3. Напишите bash-скрипт, который скачает ваш fork-репозиторий в каталог /opt и запустит проект целиком.
4. Зайдите на сайт проверки http подключений, например(или аналогичный): ```https://check-host.net/check-http``` и запустите проверку вашего сервиса ```http://<внешний_IP-адрес_вашей_ВМ>:8090```. Таким образом трафик будет направлен в ingress-proxy. Трафик должен пройти через цепочки: Пользователь → Internet → Nginx → HAProxy → FastAPI(запись в БД) → HAProxy → Nginx → Internet → Пользователь
5. (Необязательная часть) Дополнительно настройте remote ssh context к вашему серверу. Отобразите список контекстов и результат удаленного выполнения ```docker ps -a```
6. Повторите SQL-запрос на сервере и приложите скриншот и ссылку на fork.

## Задача 5 (*)
1. Напишите и задеплойте на вашу облачную ВМ bash скрипт, который произведет резервное копирование БД mysql в директорию "/opt/backup" с помощью запуска в сети "backend" контейнера из образа ```schnitzler/mysqldump``` при помощи ```docker run ...``` команды. Подсказка: "документация образа."
2. Протестируйте ручной запуск
3. Настройте выполнение скрипта раз в 1 минуту через cron, crontab или systemctl timer. Придумайте способ не светить логин/пароль в git!!
4. Предоставьте скрипт, cron-task и скриншот с несколькими резервными копиями в "/opt/backup"

## Задача 6
Скачайте docker образ ```hashicorp/terraform:latest``` и скопируйте бинарный файл ```/bin/terraform``` на свою локальную машину, используя dive и docker save.
Предоставьте скриншоты  действий .

## Задача 6.1
Добейтесь аналогичного результата, используя docker cp.  
Предоставьте скриншоты  действий .

## Задача 6.2 (**)
Предложите способ извлечь файл из контейнера, используя только команду docker build и любой Dockerfile.  
Предоставьте скриншоты  действий .

## Задача 7 (***)
Запустите ваше python-приложение с помощью runC, не используя docker или containerd.  
Предоставьте скриншоты  действий .


Ответ
## Задача 1

.dockerignore:

```bash
.env
.git
__pycache__
venv
.gitignore
```
Dockerfile.python:

```bash
# Используем данный образ(from-взять готовый образ и строить на его основе) и даем ему аллиас(as builder) для дальнейшего обращения к данной стадии сборки.
FROM python:3.12-bullseye as builder
# Рабочая директория, все дальнейшие команды будут выполняться внутри контейнера в этой папке.
WORKDIR /app

# Устанавливаем  системные пакеты (RUN выполняет команды внутри образа, каждый RUN это новый слой, в данном случае && использовали для экономии, т.к. их кол-во ограничено.)
RUN apt-get update && \
    apt-get install -y --no-install-recommends gcc

# venv это виртуальное оуркжение, т.е изолированная копия питона со своими пакетами, если было бы несколько проектов на питоне. без виртуалки были бы конфликты.
RUN python -m venv /app/venv
# далее задается системная переменная PATH, в случае поиска python, запрос шел бы в первую очередь по этому пути.
ENV PATH="/app/venv/bin:$PATH"
# тут копируем по пути откуда и куда, т.е копируем из указанного файла в рабочую директори /app.
COPY requirements.txt ./
# Кэширование для ускорение повторных сборок, при повторной сбокре будет брать отсюда ~/.cache/pip 
RUN --mount=type=cache,target=~/.cache/pip pip install -r requirements.txt

FROM python:3.12-slim as worker
WORKDIR /app
# создание непривилегированного пользователя, приложение не будет работать от рута.
RUN addgroup --system python && \
    adduser --system --disabled-password  --ingroup python python && chown python:python /app
# переключились на этого пользователя
USER python
#тут копирование виртуального оркужения из builder, --chown=python:python устанавливаем владельца, затем берем файлы по алиусу первого стейджа --from=builder, копируем папку /app/venv в ./venv
COPY --chown=python:python --from=builder /app/venv ./venv
#тут копирование кода
COPY --chown=python:python . .

ENV PATH="/app/venv/bin:$PATH"
# команды по-умолчанию
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]
```
![1](https://github.com/Nikich828/hw_docker/blob/main/1.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/2.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/3.jpg)

## Задача 3

compose.yaml:

```bash
x-restart: &restart #тут мы создаем шаблон для использования в ниже описанных контейнерах, чтобы несколько раз не вставлять один и тот же код. называется даный шаблон yaml-якорь.
  restart: always

include: #Данная настройка позволяет прочитать внешний файл, докер-композ прочитает его и добавит в текущую конфигурацию.
  - proxy.yaml

services:
  db: # Название первого сервиса, база данных.
    image: mysql:8 # образ подгрузит с докер хаба
    container_name: docker-ht-db # название контейнера, если не выставим сами, то докер сделает это за нас.
    <<: [*restart] # использование шаблона
    networks: # одно из преимуществ докер-композа - он умеет сам автоматом выирать подсеть из определенного диапозона, назначать ip каждому контейнеру в этой подсети и настраивает DNS опять же внутри этой же сети (благодяря этому контейнеры могут обращаться друг другу по именам сервисов/доменну)
      backend: # в нашем случае по заданию выделена подсеть 172.20.0.0/24 и для каждого сервиса выделена статика.
        ipv4_address: 172.20.0.10 # поэтому нам надо задать их руками.
    environment: # перменные окружения которые будут внутри контейнера, при первом запуске mysql их прочитает и
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD} # создать пароль рута
      - MYSQL_DATABASE=${MYSQL_DATABASE} # создат БД
      - MYSQL_USER=${MYSQL_USER} # создаст юзера
      - MYSQL_PASSWORD=${MYSQL_PASSWORD} # и пароль к нему
    volumes: # вольюмы нужны для монтирование томов, для сохранения базы вне контейнера, по умолчанию db_data находится /var/lib/docker/volumes/ главная папка докера в линуксе.
      - db_data:/var/lib/mysql # левая часть от двоеточия то что хранится на хосте, а правая часть путь внутри контейнера.
    healthcheck: # проверка доступности
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"] # пинг внутри контейнера
      interval: 10s # каждые 10 сек докер запускает првоерку
      timeout: 5s # если проверка дольше 5 сек, то считается прваленной
      retries: 3  # кол-во попыток

  web:
    build: # команда на сбор образа на основе написанного мной докерфайла
      context: . # указывает источник файлов для сборки, в данном случае текущая папка
      dockerfile: Dockerfile.python # название докерфайла
    container_name: docker-ht-web
    <<: [*restart]
    networks:
      backend:
        ipv4_address: 172.20.0.5
    environment: # переменные для пайтон приложения, чтобы оно могло подключиться к БД
      - DB_HOST=db
      - DB_USER=${MYSQL_USER}
      - DB_PASSWORD=${MYSQL_PASSWORD}
      - DB_NAME=${MYSQL_DATABASE}
    depends_on: # данная команда указывает порядок запуска, в нашем случае сервис web запуститься только успешной првоерки хелфчека
      db:
        condition: service_healthy

volumes: # тут глобально объявляются все используемые тома
  db_data:

#networks: # а тут глобально использвуемы сети
 # backend: # имя сети 
 #   name: backend # а это как называется сеть в самом докере
 #   driver: bridge  # сетевое подключение типа мост, изолированная сеть для контейнеров на одной машине
 #   ipam: # IP Address Management
 #     config:
  #      - subnet: 172.20.0.0/24 # наша подсеть
```
![1](https://github.com/Nikich828/hw_docker/blob/main/4.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/5.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/6.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/7.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/8.jpg)


## Задача 4

123.sh
```bash
#!/bin/bash

# === НАСТРОЙКИ (ЗАМЕНИТЕ НА СВОИ!) ===
REPO_URL="https://github.com/Nikich828/shvirtd-example-python.git"
PROJECT_DIR="/opt/myproject"
# =====================================

echo "1. Клонируем репозиторий."
sudo rm -rf $PROJECT_DIR 2>/dev/null
sudo git clone $REPO_URL $PROJECT_DIR
cd $PROJECT_DIR

echo "2. Запускаем проект."
sudo docker compose up -d

echo "3. Проверяем статус:"
sleep 5
sudo docker compose ps

echo "4. Проверяем доступность:"
curl -L http://localhost:8090 || echo "Сервис временно недоступен"

echo "5. Внешний адрес:"
curl -s ifconfig.me | xargs -I {} echo "http://{}:8090"
```
![1](https://github.com/Nikich828/hw_docker/blob/main/9.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/10.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/11.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/12.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/13.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/14.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/15.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/16.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/17.jpg)


Ссылка на форк: https://github.com/Nikich828/shvirtd-example-python

## Задача 6

![1](https://github.com/Nikich828/hw_docker/blob/main/18.jpg)
![1](https://github.com/Nikich828/hw_docker/blob/main/19.jpg)

## Задача 6.1
![1](https://github.com/Nikich828/hw_docker/blob/main/20.jpg)