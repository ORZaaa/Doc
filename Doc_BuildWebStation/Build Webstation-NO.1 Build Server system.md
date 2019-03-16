# Build Webstation: NO.1 Build Server system
That's a document about how to build WebStation with Ubuntu and Laravel.
Use VM 15 pro.
---
## 0x00 Verison :
- Ubuntu : 18.04 LTS
- Tasksel : latest by Apt-get
- LAMP : lastest by Tasksel 
- Node.js : V8.12.0
- Npm : V6.4.1
- Yarn : V1.10.1
- Composer : V1.7.2
- Laravel : V5.5
- Voyager : V1.0
----
## 0x01 Build Step :
- Install Ubuntu 
- Install Tasksel
- Install LAMP
- Install Nodejs & Npm
- Install Yarn
- Install composer
- Install Laravel
- Install Voyager
- Install Laravel Model
### Step 1 - Ubuntu :
- Install Ubuntu
```sh{.line-numbers}
$ sudo apt-get update && apt-get upgrade
```
- Set Root User
```sh{.line-numbers}
$ sudo passwd root
```
- Link WinSCP
```sh{.line-numbers}
$ nano /etc/ssh/sshd_config
```
Find
```php {.line-numbers}
LoginGraceTime 2m
PermitRootLogin without-password
StrictModes yes
```
Change to
```php {.line-numbers}
LoginGraceTime 2m
PermitRootLogin yes
StrictModes yes
```
> Tips:
> Must know,
> if "PermitRootLogin yes", the system safety have Hidden risk
- Restart System
```sh{.line-numbers}
$ sudo reboot
```
### Step 2 - Tasksel :
- Install Tasesel
```sh{.line-numbers}
$ sudo apt-get install tasksel
```
### Step 3 - LAMP :
- Install LAMP
```sh{.line-numbers}
$ sudo tasksel install lamp-server
```
- Install & Setting Phpmyadmin
```sh{.line-numbers}
$ sudo apt-get install phpmyadmin
$ sudo ln -s /usr/share/phpmyadmin/ /var/www/ 
$ sudo /etc/init.d/apache2 restart 
```
- Set MySQL User
```sh{.line-numbers}
$ nano /etc/mysql/debian.cnf
```
Find 
```php {.line-numbers}
user     = debian-sys-maint
password = **********************
```
> Tips:
> Login phpmyadmin by "debian-sys-maint"
> Change MySQL User permissions(change localhost@root and add %@SQLAdmin)

Find
```sh
$ /etc/mysql/mysql.conf.d/mysqld.cnf
```
```sh
$ bind-address  = 127.0.0.1
```
to
```sh
#bind-address  = 127.0.0.1
```
or
```sh
bind-address  = 0.0.0.0
```


- Change Apache AllowOverride
```
$ nano /etc/apache2/apache2.conf
```
Find
```c {.line-numbers} 
Options FollowSymLinks
AllowOverride None
Require all denied
```
change to 

```c
Options Indexes FollowSymLinks
AllowOverride All
Require all granted
```
start Rewrite
```sh
$ sudo a2enmod rewrite
$ sudo /etc/init.d/apache2 restart
```
### Step 4 - Node.js & Npm :  
- Install Node.js
```sh
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
$ sudo apt-get install nodejs
$ node -v
v8.5.*
$ npm -v
v6.*
```
### Step 5 - Yarn :  
- Install Yarn
```sh
$ curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
$ echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
$ sudo apt-get update && sudo apt-get install yarn
$ yarn --version
```
### Step 6 - Composer :  
- Install Composer
```sh
$ curl -sS https://getcomposer.org/installer | php
$ mv composer.phar /usr/local/bin/composer
$ chmod +x /usr/local/bin/composer
```

### Step 7 - Laravel :   
#### 01.Install Laravel

```sh
$ cd /var/www/
$ composer create-project --prefer-dist laravel/laravel blog "5.5.*"
```
#### 02.Install node_models

```sh
$ npm install
```
or
```sh
$ yarn install
```
> Tips:
> If npm install not work,we can choose use yarn.
#### 03.Setting Env
- find 
```sh{.line-numbers}
/.env
```
Set
```php {.line-numbers}
    APP_NAME=Laravel
    APP_URL=http://localhost
    
    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=homestead
    DB_USERNAME=homestead
    DB_PASSWORD=secret
    
    MAIL_DRIVER=smtp
    MAIL_HOST=smtp.mailtrap.io
    MAIL_PORT=2525
    MAIL_USERNAME=null
    MAIL_PASSWORD=null
    MAIL_ENCRYPTION=null
```

Find 
```sh{.line-numbers}
../config/app.php
```
set to 
```sh {.line-numbers}
    'timezone' => 'Asia/Shanghai',
    'locale' => 'zh_CN',
```
#### 04.Change files permission
```sh{.line-numbers}
$ chmod -R 777 storage/
$ chmod -R 777 bootstrap/cache/
```
#### 05.Set Root URL
-    
```sh{.line-numbers}
$ nano /etc/apache2/sites-available/000-default.conf
```
find 
```php {.line-numbers}
<VirtualHost *:80>

ServerAdmin webmaster@localhost
DocumentRoot /var/www/

ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Change to 
```php {.line-numbers}
<VirtualHost *:80>

ServerAdmin webmaster@localhost
DocumentRoot /var/www/blog/public //change

ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Restart apache
```sh
$ /etc/init.d/apache2 restart
```
> Tips:
> There was an example
> - `$ git clone https://github.com/ORZaaa/TestWebStation.git blog`
> - `$ cp .env.example .env`
> - `$ php artisan key:generate`
> - `$ php artisan migrate`
> - `$ composer dump-autoload`
> - `$ npm run watch-poll`
