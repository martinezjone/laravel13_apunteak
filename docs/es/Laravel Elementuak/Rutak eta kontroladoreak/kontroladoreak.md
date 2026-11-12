# Controladores

En Laravel, los controladores contienen la lógica de nuestra aplicación.

Podemos crear los controladores manualmente, pero con Artisan nos resultará más fácil. El nombre del controlador puede ser cualquiera, aunque normalmente (por convención) se escribe con mayúscula inicial y en singular, seguido de la palabra Controller: HomeController, IkasleController, KategoriaController, etc.

```bash
php artisan make:controller HomeController
```

Artisan crea un archivo PHP con el nombre que hemos indicado en la carpeta app>Http>Controllers.

Como vemos, en el archivo crea un namespace que indica dónde se encuentra nuestro controlador. También crearía una clase HomeController.

En ese controlador crearemos los métodos que necesitemos.

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class HomeController extends Controller{

public function index(){

return "Hau da nire blog-aren lehenengo orria. Ongi etorri!!";

}
```

Si queremos utilizar un método de ese controlador, por ejemplo, para crear una ruta, primero tendremos que indicar que queremos utilizar un método de ese controlador; para ello añadiremos el namespace:

```php
use App\Http\Controllers\HomeController;
```

Y después podremos utilizar todos los métodos que tiene la clase de ese controlador. Por ejemplo, como se indica en este ejemplo index.php:

```php
Route::get('/', [HomeController::class, 'index']);
```

Normalmente cada controlador tendrá varios métodos, pero si solo tiene uno, en vez de llamar index a nuestro método podemos llamarlo __invoke(); en ese caso, al definir la ruta no hace falta escribir el nombre del método, porque se accederá directamente a él.

```php
class HomeController extends Controller{

public function __invoke(){

return "Hau da nire blog-aren lehenengo orria. Ongi etorri!!";

}
```

Y en la ruta pondríamos solo el nombre del controlador.

```php
Route::get('/', HomeController::class);
```

Controlador de recursos (Resource Controller)

Cuando vayamos a utilizar nuestro controlador para hacer un CRUD, podemos indicarle a Laravel mediante Artisan que ese será su propósito.

```bash
php artisan make:controller ProfesorController --resource
```

Al hacerlo, como verás, escribirá los métodos que se suelen utilizar para hacer un CRUD.
