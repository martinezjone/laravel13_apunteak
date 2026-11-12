# Laravel Breeze

## Instalación

Laravel Breeze es un kit de Laravel que nos servirá para gestionar los registros de usuarios y la autenticación en nuestra aplicación.

Documentación oficial de Laravel (starter kits): <https://laravel.com/framework/docs/13.x/starter-kits#community-maintained-starter-kits>

Para instalar Laravel Breeze hay que seguir los siguientes pasos:

Primero se instalará el paquete Laravel Breeze:

```bash
composer require laravel/breeze --dev
```

El siguiente comando instala las rutas, los controladores, etc. que necesita la aplicación.

```bash
php artisan breeze:install

php artisan migrate

npm install

npm run dev
```

¡¡CUIDADO!! Si la aplicación ya está en marcha, guarda antes el archivo routes/web.php, porque al instalar este paquete se sobrescribe el archivo.

Durante la instalación nos hará varias preguntas. En nuestro caso utilizaremos Blade; hay varias opciones más, explicadas en detalle en la documentación oficial.

Ten en cuenta también que todas las vistas que crea Laravel Breeze están diseñadas con Tailwind.

Puedes personalizar cada parte de la interfaz de usuario editando los archivos de vista de la carpeta resources/view/auth; una parte de la interfaz está organizada en componentes Blade, que puedes encontrar en la carpeta resources/view/components.

## Rutas (web.php)

Como vemos, Breeze crea sus rutas, que ejecutan los ProfileController. También añadiremos las nuestras, así que las copiaremos aquí.

Como vemos, utiliza algunos middlewares al crear las rutas. ¿Qué son los middlewares?

Se encargan de filtrar las rutas; en este caso, por ejemplo, si un usuario no ha iniciado sesión, no podrá entrar en nuestro sitio web. Por tanto, aplicaremos el filtro auth a todas las rutas.

Breeze utiliza dos middlewares: auth y verified. Auth comprobará si un usuario ha iniciado sesión (autenticado) y verified comprobará si un usuario ha verificado su correo electrónico (verificado).

```php
Route::middleware('auth')->group(function () {

Route::get('/profile', [ProfileController::class, 'edit'])->name('profile.edit');

Route::patch('/profile', [ProfileController::class, 'update'])->name('profile.update');

Route::delete('/profile', [ProfileController::class, 'destroy'])->name('profile.destroy');

});
```
