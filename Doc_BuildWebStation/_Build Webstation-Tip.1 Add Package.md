## Build Webstation: NO.1 Build Server system
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
## 0x01 - Voyager :   
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
----
## 0x02 - Laravel Model :  
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
