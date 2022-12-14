# LEMP-debian
debian install LEMP
# Install Nginx, PHP 8, MariaDB, Laravel 8,Node js on Debian 10/11

**Tutorial works for DidgitalOcean,VScale.**

* Upgrade system

    ```
    sudo apt update
    sudo apt upgrade
    ```

* Enter `Y` few times and choise `Keep the Local version currenctly installed`.

* Install Nginx

    ```
    sudo apt install nginx
    ```

* Install MariaDB

    ```
    sudo apt install mariadb-server or mysql-server
    ```

* Open MariaDB

    ```
    sudo mariadb or sudo mysql 
    ```

* Create database

    ```
    create database DATABASE_NAME;
    ```

* Create new db user

    ```
    CREATE USER YOUR_DATABASE_USER IDENTIFIED BY 'YOUR_DATABASE_USER_PASSWORD';
    ```
* Grant privileges to this user

    ```
    GRANT ALL ON DATABASE_NAME.* TO YOUR_DATABASE_USER;
    ```

* Exit from MariaDB

    ```
    exit
    ```

* Add SURY repository, for PHP 8.

    ```
    sudo apt -y install lsb-release apt-transport-https ca-certificates 
    ```

    ```
    sudo wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
    ```

    ```
    echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/php.list
    ```

* Upgrade system

    ```
    sudo apt update
    sudo apt upgrade
    ```
* Install PHP and extensions

    ```
    sudo apt install php8.0-fpm php8.0-mysql php8.0-mbstring php8.0-xml php8.0-bcmath zip unzip curl git
    ```
* Go to www directory

    ```
    cd /var/www
    ```
* Clone your laravel repository

    ```
    git clone https://github.com/name/example.git example
    ```
    
    ```
    git config --global credential.helper store
    ```  

* Install composer 

    ```
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php -r "if (hash_file('sha384', 'composer-setup.php') === '756890a4488ce9024fc62c56153228907f1545c228516cbf63f885e036d37e9a59d27d63f46af1d4d07ee0f76181c7d3') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
    php composer-setup.php
    php -r "unlink('composer-setup.php');"
    ```

    ```
    mv composer.phar /usr/local/bin/composer
    ```
* Install composer dependencies

    ```
    composer install
    ```
* Install laravel with composer
    ```
    composer create-project laravel/laravel example-app (from documentation laravel.com)
    ```    
    ```
    composer global require laravel/installer (from documentation laravel.com)
    laravel new example-app
    ``` 
    
* Add db settings to `.env`.

    ```
    nano .env
    ```

    ```
    DB_DATABASE=database
    DB_USERNAME=user
    DB_PASSWORD=password
    ```

* Set permissions to directories

    ```
    sudo chown -R www-data.www-data /var/www/example
    sudo chown -R www-data.www-data /var/www/example/storage
    sudo chown -R www-data.www-data /var/www/example/bootstrap/cache
    ```

* Migrate db

    ```
    php artisan migrate
    ```
* If you need import existings sql dump:

    ```
    mariadb -uroot database -p < /var/www/example.sql
    ```

* Add nginx config to `/etc/nginx/sites-available`

    ```
    nano /etc/nginx/sites-available/example.com.conf
    ```
    ```
    server {
        listen 80;
        server_name example.com;
        root /var/www/example/public;
        add_header X-Frame-Options "SAMEORIGIN";
        add_header X-XSS-Protection "1; mode=block";
        add_header X-Content-Type-Options "nosniff";
        index index.html index.htm index.php;
        charset utf-8;
        location / {
            try_files $uri $uri/ /index.php?$query_string;
        }
        location = /favicon.ico { access_log off; log_not_found off; }
        location = /robots.txt  { access_log off; log_not_found off; }
        error_page 404 /index.php;
        location ~ \.php$ {
            fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
            include fastcgi_params;
        }
        location ~ /\.(?!well-known).* {
            deny all;
        }
    }
    ```

* Enable your Nginx config

    ```
    sudo ln -s /etc/nginx/sites-available/example.com.conf /etc/nginx/sites-enabled/
    ```
* Test nginx config

    ```
    nginx -t
    ```
* Reload nginx

    ```
    service nginx reload
    ```
* Install node.js and npm

    ```
    curl -fsSL https://deb.nodesource.com/setup_15.x | bash -
    apt-get install -y nodejs
    ```

    ```
    cd /var/www/example
    npm install
    npm run prod
    ```

## Helpful commands

`sudo etc/init.d/php8.0-fpm restart` - restart php

`sudo service nginx reload` - restart nginx

`sudo systemctl restart mariadb.service` - restart mariadb

`tar -cvpzf /backup.tar.gz --exclude=/backup.tar.gz --one-file-system /` - create system backup to zip archive



