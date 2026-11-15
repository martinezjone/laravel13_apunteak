# Controladores

Una ruta vincula una solicitud HTTP con una acción. Hasta ahora, hemos escrito esa acción directamente en el archivo `routes/web.php`, dentro de una función anónima. **Los controladores (*controllers*) nos permiten organizar acciones relacionadas en una misma clase.** Así, la ruta indica qué método se debe llamar, y el método gestiona la solicitud y devuelve la respuesta.

## Crear un controlador

En el directorio raíz del proyecto, ejecuta:

```bash
php artisan make:controller HomeController
```

Artisan creará el archivo `app/Http/Controllers/HomeController.php`. Por convención, el nombre del controlador se escribe en singular y en formato PascalCase, con el sufijo `Controller`. Por ejemplo, `HomeController`, `StudentController` y `CategoryController`.

## De la ruta al controlador

Añade el método `index()` a la clase `HomeController`. En la primera prueba, devolveremos un mensaje de texto.

```php
<?php

namespace App\Http\Controllers;

class HomeController extends Controller
{
    public function index(): string
    {
        return 'Esta es la página de inicio de mi blog. ¡Te doy la bienvenida!';
    }
}
```

La línea `namespace App\Http\Controllers;` indica en qué espacio de nombres está la clase. La expresión `extends Controller` indica que la clase `HomeController` hereda la clase base `Controller`. La palabra `public` permite llamar al método desde fuera de la clase. La expresión `: string` especifica el tipo de retorno del método.

En el archivo `routes/web.php`, añade la línea `use App\Http\Controllers\HomeController;` junto con las demás importaciones. Después, sustituye la ruta `GET /` existente por esta:

```php
Route::get('/', [HomeController::class, 'index']);
```

La línea `use` nos permite utilizar el nombre abreviado `HomeController` en el archivo. El array del segundo argumento de la ruta contiene dos elementos:

| Elemento | ¿Qué indica? |
| --- | --- |
| `HomeController::class` | La clase del controlador que se debe utilizar. |
| `'index'` | El método público al que llamará Laravel. |


## Recibir un parámetro de ruta en un controlador

Sustituye la función anónima de la ruta anterior `/welcome/{name?}` por un método del controlador. En el archivo `routes/web.php`:

```php
Route::get('/welcome/{name?}', [HomeController::class, 'welcome']);
```

Añade el método `welcome()` dentro de la clase `HomeController`:

```php
public function welcome(string $name = 'invitado'): string
{
    return '¡Hola, ' . e($name) . '!';
}
```

| Solicitud | Valor de `$name` | Respuesta |
| --- | --- | --- |
| `GET /welcome/Jon` | `'Jon'` | `¡Hola, Jon!` |
| `GET /welcome` | `'invitado'` | `¡Hola, invitado!` |

La expresión `{name?}` indica que el segmento de la URL es opcional. Si falta el segmento, el método utiliza el valor predeterminado `'invitado'`. El helper `e()` prepara el nombre recibido de la URL para mostrarlo como texto en HTML.

## Del controlador a una vista Blade

Ahora, el método `index()` preparará el título y el mensaje de la página de inicio y se los pasará a una vista Blade. Añade `use Illuminate\View\View;` en el archivo del controlador, después de la línea `namespace`. Después, sustituye el método `index()`:

```php
public function index(): View
{
    $title = 'Inicio';
    $message = 'Esta es la página de inicio de mi blog. ¡Te doy la bienvenida!';

    return view('home', [
        'title' => $title,
        'message' => $message,
    ]);
}
```

Crea el archivo `resources/views/home.blade.php`:

```blade
<h1>{{ $title }}</h1>
<p>{{ $message }}</p>
```

La llamada `view('home', ...)` utiliza la vista `home.blade.php`. Las claves `'title'` y `'message'` del array indican los nombres de las variables `$title` y `$message` dentro de Blade. Blade escapa automáticamente para HTML los valores que muestra mediante `{{ ... }}`.


## Controlador de una sola acción

Cuando queremos diseñar un controlador para una sola acción, podemos utilizar el método `__invoke()`. Por ejemplo, crea `AboutController`:

```bash
php artisan make:controller AboutController --invokable
```

```php
<?php

namespace App\Http\Controllers;

class AboutController extends Controller
{
    public function __invoke(): string
    {
        return 'Información sobre el blog';
    }
}
```

Añade la importación `use App\Http\Controllers\AboutController;` al principio del archivo `routes/web.php` y registra la ruta:

```php
Route::get('/about', AboutController::class);
```

En este caso, basta con proporcionar la clase del controlador. Laravel llamará al método `__invoke()`. Si la ruta general `/{number}` está activa, coloca `/about` antes de ella.

## Controlador de recursos (*Resource controller*)

Para un CRUD, Artisan puede crear un controlador con métodos para las acciones habituales:

```bash
php artisan make:controller TeacherController --resource
```

El comando prepara la estructura de los métodos `index`, `create`, `store`, `show`, `edit`, `update` y `destroy`. Nosotros escribiremos el contenido de cada método.

Para registrar las rutas, añade la importación `use App\Http\Controllers\TeacherController;` y esta declaración al archivo `routes/web.php`:

```php
Route::resource('teachers', TeacherController::class);
```

La opción `--resource` crea la estructura de los métodos del controlador, y la llamada `Route::resource()` registra las rutas. Comprueba el resultado con el comando `php artisan route:list --path=teachers`. Puedes consultar el método HTTP, la URL y la acción de cada ruta en la sección [Rutas](rutak.md).

[Documentación oficial de Laravel 13 · Controllers](https://laravel.com/framework/docs/13.x/controllers)
