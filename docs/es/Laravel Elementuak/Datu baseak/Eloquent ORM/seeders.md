# Seeders

## ¿Qué son los seeders?

Los **seeders** son clases que Laravel ofrece para insertar datos en la base de datos.

Normalmente se utilizan en dos situaciones:

- **Durante el desarrollo:** para insertar datos de ejemplo y probar la aplicación.
- **Al iniciar la aplicación:** para insertar los datos iniciales que necesita, por ejemplo, categorías o roles.

Como hemos visto en las secciones anteriores, **las migraciones crean la estructura de las tablas de la base de datos**, y **los modelos permiten trabajar con los datos de esas tablas**.

Con los seeders, en cambio, insertaremos datos en esas tablas.

## Crear un seeder

Para crear un seeder nuevo, utilizaremos el siguiente comando de Artisan:

```bash
php artisan make:seeder PostSeeder
```

Este comando creará el archivo `PostSeeder.php` en la carpeta `database/seeders`.

La clase generada tendrá la siguiente estructura:

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class PostSeeder extends Seeder
{
    public function run(): void
    {
        //
    }
}
```

Como podemos ver, la clase `PostSeeder` extiende la clase `Seeder` de Laravel.

**En el método `run()` escribiremos qué datos queremos insertar en la base de datos.** Este método se utilizará al ejecutar el seeder.

## Insertar datos

Para insertar datos, utilizaremos el modelo `Post` que hemos creado anteriormente.

Para ello, primero añadiremos la referencia al modelo al principio del archivo:

```php
use App\Models\Post;
```

Con Eloquent podemos gestionar los registros de la base de datos como objetos.

Hay varias formas de insertar datos. Veremos dos opciones.

### 1. Crear un objeto y utilizar `save()`

La primera opción consiste en crear un objeto de la clase `Post` y rellenar sus propiedades una por una.

```php
public function run(): void
{
    $post = new Post();

    $post->title = 'Mi primer post';
    $post->content = 'Este será el contenido de mi post';

    $post->save();
}
```

En este código:

1. Creamos un objeto nuevo del modelo `Post` utilizando `new Post()`.
2. Asignamos valores a los atributos del objeto mediante `$post->title` y `$post->content`.
3. Guardamos el registro en la tabla `posts` de la base de datos con `$post->save()`.

!!! note
    La instrucción `new Post()` crea el objeto en memoria, pero todavía no lo guarda en la base de datos.

    Para insertar el registro en la base de datos, tenemos que ejecutar el método `save()`.

### 2. Utilizar el método `create()`

Otra opción es utilizar el método `create()` de Eloquent.

Con este método podemos crear un registro nuevo y guardarlo en la base de datos en una sola instrucción.

```php
public function run(): void
{
    Post::create([
        'title' => 'Mi primer post',
        'content' => 'Este será el contenido de mi post'
    ]);

    Post::create([
        'title' => 'Segundo post',
        'content' => 'Este será el contenido de mi segundo post'
    ]);
}
```

En este caso, se crearán dos registros en la tabla `posts`.

!!! warning "Recuerda `$fillable`"
    Como el método `create()` utiliza asignación masiva, debemos especificar en el modelo `Post` qué atributos se pueden rellenar.

    ```php
    protected $fillable = ['title', 'content'];
    ```

    En cambio, al asignar los atributos uno por uno utilizando `save()`, no se aplica `$fillable`.

### `PostSeeder` completo

Por tanto, utilizando `create()`, nuestro seeder tendría el siguiente aspecto:

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\Post;

class PostSeeder extends Seeder
{
    public function run(): void
    {
        Post::create([
            'title' => 'Aprendiendo Laravel',
            'content' => 'Aprendiendo modelos y migraciones en Laravel'
        ]);

        Post::create([
            'title' => 'Laravel avanzado',
            'content' => 'Este será otro post sobre Laravel'
        ]);
    }
}
```

## DatabaseSeeder

Laravel crea por defecto el archivo **`DatabaseSeeder.php`** en la carpeta `database/seeders`.

Este archivo es el seeder principal y se utiliza para ejecutar otros seeders.

Cuando ejecutamos el comando `php artisan db:seed`, Laravel llama por defecto al método `run()` de la clase `DatabaseSeeder`.

Para ejecutar nuestro `PostSeeder`, escribiremos lo siguiente dentro de la clase `DatabaseSeeder`:

```php
public function run(): void
{
    $this->call(PostSeeder::class);
}
```

Así, al ejecutar `DatabaseSeeder`, también se ejecutará `PostSeeder`.

### Ejecutar más de un seeder

En una aplicación podemos tener varios seeders, por ejemplo:

- `UserSeeder`
- `PostSeeder`
- `CategorySeeder`

Para ejecutarlos todos desde la clase `DatabaseSeeder`, podemos pasar un array al método `call()`:

```php
public function run(): void
{
    $this->call([
        UserSeeder::class,
        CategorySeeder::class,
        PostSeeder::class,
    ]);
}
```

Los seeders se ejecutarán **en el orden indicado en el array**.

Otra opción es llamar varias veces al método `call()`:

```php
public function run(): void
{
    $this->call(UserSeeder::class);
    $this->call(CategorySeeder::class);
    $this->call(PostSeeder::class);
}
```

Ambas opciones dan el mismo resultado.

!!! tip "Orden de ejecución"
    El orden de los seeders puede ser importante cuando hay relaciones entre las tablas.

    Por ejemplo, si cada registro de la tabla `posts` necesita el ID de un usuario, primero tendremos que crear los usuarios y, después, los posts.

## Ejecutar los seeders

Después de crear los seeders y registrarlos en la clase `DatabaseSeeder`, utilizaremos el siguiente comando para insertar datos en la base de datos:

```bash
php artisan db:seed
```

Este comando ejecutará el método `run()` de la clase `DatabaseSeeder` y también todos los seeders a los que llame.

### Ejecutar un seeder concreto

Si queremos ejecutar un solo seeder, podemos utilizar la opción `--class`:

```bash
php artisan db:seed --class=PostSeeder
```

En este caso, solo se ejecutará `PostSeeder`, sin pasar por la clase `DatabaseSeeder`.

### Ejecutar las migraciones y los seeders juntos

Durante el desarrollo, a menudo queremos volver a crear las tablas de la base de datos e insertar datos de ejemplo.

Para ello, podemos utilizar el siguiente comando:

```bash
php artisan migrate:fresh --seed
```

Este comando realiza tres pasos:

1. Elimina todas las tablas de la base de datos.
2. Vuelve a ejecutar todas las migraciones para crear las tablas.
3. Ejecuta `DatabaseSeeder` para rellenar las tablas con datos.

!!! danger "¡Cuidado!"
    El comando `migrate:fresh --seed` elimina todas las tablas de la base de datos y, con ellas, los datos almacenados.

    Por eso, es adecuado para utilizarlo en entornos de desarrollo, pero no cuando necesitamos conservar los datos.

!!! warning "Ejecutar los seeders más de una vez"
    El comando `php artisan db:seed` no elimina automáticamente los datos insertados anteriormente.

    Por tanto, si ejecutamos el mismo seeder varias veces, los registros podrían duplicarse.

    Por ejemplo, si nuestro `PostSeeder` crea dos registros, al ejecutarlo dos veces tendremos cuatro registros, siempre que la tabla estuviera vacía inicialmente y las restricciones no lo impidan.

## Resumen de los comandos de seeder

| Comando | ¿Qué hace? |
|---|---|
| `php artisan make:seeder PostSeeder` | Crea un seeder nuevo. |
| `php artisan db:seed` | Ejecuta `DatabaseSeeder` y los seeders a los que llama. |
| `php artisan db:seed --class=PostSeeder` | Ejecuta un seeder concreto. |
| `php artisan migrate:fresh --seed` | Elimina todas las tablas, ejecuta las migraciones e inserta los datos. |

Documentación oficial: [Database Seeding – Laravel 13](https://laravel.com/docs/13.x/seeding)

Siguiente: Factories
