# Build Webstation
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
- Change Apache AllowOverride
    ```sh
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
    find 
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
    $ chmod -R 777 /storage
    $ chmod -R 777 /bootstrap/cache/
    ```
#### 05.Set Root URL
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
    > `$ git clone https://github.com/ORZaaa/TestWebStation.git blog`
    > `$ cp .env.example .env`
    > `$ php artisan key:generate`
    > `$ php artisan migrate`
    > `$ composer dump-autoload`
    > `$ npm run watch-poll`

#### Step 8 - Voyager :   
- Install Voyager
    ```sh{.line-numbers}
    $ composer require tcg/voyager
    $ php artisan voyager:install
    ```
    Set Admin User
    ```sh{.line-numbers}
    $ php artisan voyager:admin your@email.com
    ```
    Create new admin user
    ```sh{.line-numbers}
    $ php artisan voyager:admin your@email.com --create
    ```
- If change the model path(example: ```app/User.php``` to ```app/Models/User.php```)
    Find 
    `vendor\tcg\voyager\src\Commands\InstallCommand.php`
    ```php {.line-numbers}
    $this->info('Attempting to set Voyager User model as parent to App\User');
        if (file_exists(app_path('User.php'))) {
            $str = file_get_contents(app_path('User.php'));

            if ($str !== false) {
                $str = str_replace('extends Authenticatable', "extends \TCG\Voyager\Models\User", $str);

                file_put_contents(app_path('User.php'), $str);
            }
    ```
    Change to 
    ```php {.line-numbers}
    $userPath = config('voyager.user.default_path', app_path('User.php'));
        $this->info('Attempting to set Voyager User model as parent to ' . $userPath);        
        if (file_exists($userPath)) {
            $str = file_get_contents($userPath);

            if ($str !== false) {
                $str = str_replace('extends Authenticatable', "extends \TCG\Voyager\Models\User", $str);

                file_put_contents($userPath, $str);
            }
    ```
    Then,
- ```config/voyager.php```
    ```php {.line-numbers}
        'user' => [
        'add_default_role_on_register' => true,
        'default_role'                 => 'user',
        'namespace'                    => App\Models\User::class,       //Change
        'default_avatar'               => 'users/default.png',       
        'default_path'                 => app_path('Models/User.php'),  // Add
    ],
    ```
- ```app/User.php```
    ```php
    namespace App;
    ```
    Change to 
    ```php
    namespace App\Models;
    ```
- ```config/auth.php```
    ```php
    'model' => App\User::class,
    ```
    Change to
    ```php
    'model' => App\Models\User::class,
    ```
    
    Finally,
    ```sh
    $ composer dump-autoload
    $ php artisan voyager:install 
    ```
    > Tips:
    > Voyager password: password
#### Step 9 - Laravel Model :  
- ##### Install Laravel-debugbar
    ```sh
    $ composer require barryvdh/laravel-debugbar
    ```
    At ``` config/app.php ```
    ```php {.line-numbers} 
    'providers' => [
        ...
        Barryvdh\Debugbar\ServiceProvider::class, //add
    ],
    ```
    ```php {.line-numbers}
    'aliases' => [
            ...
            'Debugbar' => Barryvdh\Debugbar\Facade::class, //add
    ]
    ```
    Create ```config/debugbar.php```
    ```sh
    $ php artisan vendor:publish --provider="Barryvdh\Debugbar\ServiceProvider"
    ```
    Open the ```config/debugbar.php```
    ```php 
    'enabled' => env('APP_DEBUG', false), //change
    ```
    Now, the debugbar status will be decided by ```APP_DEBUG``` in ```.env``` file.

- ##### Install Laravel-ide-helper
    ```sh
    $ composer require barryvdh/laravel-ide-helper
    ```
    At ```config/app.php```
    ```php {.line-numbers}
    'providers' => [
        ...
        Barryvdh\LaravelIdeHelper\IdeHelperServiceProvider::class, //add
    ],
    ```
    Create file.
    ```sh
    $ php artisan ide-helper:generate
    ```
    > Tips:
    > If use git,
    > add 
    > ```.idea```
    > ```_ide_helper.php```
    > ```_ide_helper_models.php```
    > ```.phpstorm.meta.php```
    > to ```.gitignore``` file
