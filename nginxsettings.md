## Контролированное скачивание файлов.

Например у вас на сайте есть файлы скачивание которых должно быть доступно только для авторизованных   
пользователей и для авторизации вы не используете Basic access authentication.   

#### И так логика работы:

1. Клиент нажимает на ссылку   
2. Запрос обрабатывает веб сервер Nginx   
3. Nginx перенаправляет запрос скрипту php где происходит проверка на разрешения скачки и устанавливает заголовок X-Accel-Redirect   
4. Запрос с заголовком X-Accel-Redirect обрабатывает специальный внутренний location сервера Nginx   
В примере мы рассматриваем что у нас веб сервер LEMP (Linux + NGINX + PHP- FPM + Mysql), корень сайта у нас /var/www/site :   

Подготавливаем файл download.php :

```php
<?php
   # Тут вставляем свою обработку 
   # по каким критериям отдавать файлы 
   $path = $_GET['file'];
   # возвращаем запрос с установленным заголовком X-Accel-Redirect 
   header("X-Accel-Redirect: /access_files/" . $path);
?>
```
И так все файлы доступные для авторизованных пользователей мы храним например в /upload/private,  
а наш файл download.php лежит в /upload   

### Конфигурация Nginx:

```golang
server {
    listen 80 default_server;
    root /var/www/site;
    index index.php;
    client_max_body_size 50M;
    server_name _;
 
    location /upload/private/ {
        rewrite ^/upload/private/(.*) /upload/download.php?file=$1 last;
    }
 
    location /access_files {
            alias /var/www/site/upload/private;
            add_header Content-type application/octet-stream;
            internal;
    }
 
    location / {
        if (!-e $request_filename) {
            rewrite ^/(.*)$ /index.php?q=$1 last;
        }
    }
 
    location ~ \.php$ {
      try_files $uri =404;
      fastcgi_split_path_info ^(.+\.php)(/.+)$;
      fastcgi_pass php-fpm;
      fastcgi_index index.php;
      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
      include       fastcgi_params;
    }
 
}
```

В location /access_files, прописана директива alias, указывающая путь на диске, до папки с файлами   
и заменяющая собой соответствующий location, и ключевое слово, Internal, говорящее, что данный location,      
обрабатывает только внутренние запросы сервера Nginx и запросы X-Accel-Redirect, то есть внешние запросы    
в этот location не попадут.



