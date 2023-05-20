# Представляю вашему вниманию Kittygram - площадку для размещения фотографий котов и просмотра фотографий котов других пользователей.

## Описание проекта
Пользователи могут регистрироваться, загружать фотографии своих котиков с кратким описанием(имена и достижения), и смотреть котиков других пользователей.

## Технологии

 - Python 3.9
 - Django==3.2.3
 - djangorestframework==3.12.4
 - gunicorn
 - Nginx
 - python-dotenv==1.0.0

## Установка проекта на локальный компьютер из репозитория 
 - Клонировать репозиторий командой `git clone`
 - перейти в директорию с клонированным репозиторием
 - установить виртуальное окружение `python3 -m venv env`
 - активировать `source venv/bin/activate`
 - установить зависимости: `pip install -r requirements.txt`
 - выполнить миграции `python manage.py migrate`
 - создать суперюзера `python manage.py createsuperuser`
 - в файле settings.py на сервере добавить `ALLOWED_HOSTS = ['<адрес_вашего_сервера>', '127.0.0.1', 'localhost', 'доменное имя(опционально)`]`


## Запуск frontend приложения
 - Устанавливить на сервер Node.js, для этого из любой директории выполним команды
  `curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\`  `sudo apt-get install -y nodejs `
 - Установить зависимости, для этого из директории  <проект>/frontend выполнить команду `npm i`

## Установка Gunicorn
 - установить пакет gunicorn командой `pip install gunicorn==20.1.0`
 - В директории /etc/systemd/system/ создать файл `gunicorn.service`
``` 
	    [Unit]
    
	    Description=gunicorn daemon
    
	    After=network.target
    
	    [Service]
    
	    User=<имя пользователя в системе>
    
	    WorkingDirectory=/home/<имя пользователя в системе>/<имя_проекта>/backend/
    
	    ExecStart=/home/<имя пользователя в системе>/<имя_проекта>/venv/bin/gunicorn --bind 0.0.0.0:8080 kittygram_backend.wsgi
    
	    [Install]
    
	    WantedBy=multi-user.target
```

## Установка Nginx

 - Из любой директории выполнить команду `sudo apt install nginx -y`
 - Указать файрволу, какие порты должны остаться открытыми 
 `sudo ufw allow 'Nginx Full'`
 `sudo ufw allow OpenSSH` 
 - включить файервол `sudo ufw enable`
 - перейти в директорию <имя_проекта>/frontend и выполнить команду по сбору статики frontend: `npm run build ` после чего скопировать статику в системную директорию `sudo cp -r .../<имя проекта>/frontend/build/. /var/www/<имя проекта>/ `
 - открыть файл конфигурации веб-сервера `sudo nano /etc/nginx/sites-enabled/default ` и заменить весь имеющийся код на
 ```
        server {

            listen 80;
            server_name публичный_ip_вашего_удалённого_сервера;
            
            location / {
                root   /var/www/<имя_проекта>;
                index  index.html index.htm;
                try_files $uri /index.html;
            }

        } 
```
        
 - проверить конфигурацию на ошибки `sudo nginx -t`
 - перезагрузить Nginx `sudo systemctl reload nginx`
 - добавить блоки location для перенаправления

 ```
 	        location /api/ {
	            proxy_pass http://127.0.0.1:8080;
	        }
	        
		 location /admin/ {
		    proxy_pass http://127.0.0.1:8000;
		}
```
- сохранить изменения, проверить на ошибки и перезагрузить конфигурацию Nginx.
- прописать параметры статики в файле settings.py
```
	    STATIC_URL = 'static_backend'
	    STATIC_ROOT = BASE_DIR / 'static_backend'
```
 - в директории с файлом manage.py выполнить команду `python manage.py collectstatic`
 - скопировать собранную статику в директорию проекта `sudo cp -r <имя_проекта>/backend/static_backend/ /var/www/<имя проекта>/
 
# Для конфиденциальной информации в проекте используются переменные окружения в файле .env, в них прописаны настройки и ключи доступа, для примера переменных окружения в папке kittygram_backend создан файл .env.example, где вы можете ознакомиться с примером использованных в проекте переменными. 
