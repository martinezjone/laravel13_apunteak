# Factories

## ¿Qué son las factories?

Las **factories** son clases que Laravel ofrece para generar datos de prueba. Normalmente se utilizan durante el desarrollo para llenar la base de datos con datos ficticios.

En la sección anterior hemos visto los **seeders**. Aunque ambos componentes se utilizan para generar datos, cada uno tiene una función diferente:

- **Seeders:** especifican qué datos insertar en la base de datos y cómo organizar su carga.
- **Factories:** definen cómo generar los atributos de un modelo, normalmente utilizando datos aleatorios.

Por tanto, podemos utilizar ambos a la vez: **un seeder puede llamar a una factory para generar muchos datos automáticamente.**

Por ejemplo, si queremos crear 100 registros en la tabla `posts`, no tendremos que escribir cada uno manualmente. Podemos generarlos automáticamente mediante una factory.

!!! note
    En los proyectos nuevos de Laravel, normalmente ya está creada la clase `UserFactory`.

    En nuestro caso, crearemos una factory nueva para el modelo `Post`.

## Crear una factory

Para crear una factory nueva, utilizaremos el siguiente comando de Artisan:

```bash
php artisan make:factory PostFactory
```

Este comando creará el archivo `PostFactory.php` en la carpeta `database/factories`.

La clase generada tendrá la siguiente estructura:

```php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class PostFactory extends Factory
{
    public function definition(): array
    {
        return [
            //
        ];
    }
}
```

Como podemos ver, la clase `PostFactory` extiende la clase `Factory` de Laravel.

**En el método `definition()` especificaremos qué valor tendrá cada atributo del modelo.**

## Generar datos utilizando Faker

Laravel utiliza la biblioteca **FakerPHP** para generar datos ficticios.

Faker puede generar automáticamente distintos tipos de datos: nombres, direcciones de correo electrónico, números de teléfono, textos, fechas, números y más.

Para ello, podemos utilizar la función `fake()`.

En nuestra clase `PostFactory`, rellenaremos los campos `title` y `content` de la tabla `posts`:

```php
public function definition(): array
{
    return [
        'title' => fake()->sentence(),
        'content' => fake()->paragraph(),
    ];
}
```

En este caso:

- **`fake()->sentence()`**: genera una frase aleatoria para el campo `title`.
- **`fake()->paragraph()`**: genera un párrafo aleatorio para el campo `content`.

Cada vez que utilicemos la factory, Faker generará valores nuevos.

### Métodos habituales de Faker

Faker permite generar muchos tipos de datos. Estos son algunos de los más útiles:

| Método | Dato que genera |
|---|---|
| `fake()->name()` | Nombre y apellidos |
| `fake()->firstName()` | Nombre |
| `fake()->lastName()` | Apellido |
| `fake()->safeEmail()` | Dirección de correo electrónico ficticia |
| `fake()->sentence()` | Una frase |
| `fake()->paragraph()` | Un párrafo |
| `fake()->text(200)` | Texto de hasta 200 caracteres |
| `fake()->numberBetween(1, 100)` | Número entero entre 1 y 100 |
| `fake()->randomFloat(2, 1, 100)` | Número con dos decimales entre 1 y 100 |
| `fake()->date()` | Una fecha |
| `fake()->boolean()` | Valor `true` o `false` |
| `fake()->unique()->safeEmail()` | Dirección de correo electrónico ficticia que no se repite |

!!! tip "Documentación de Faker"
    Faker ofrece muchos más métodos. Puedes consultar los tipos de datos y formatos que se pueden generar en la documentación oficial:

    [Documentación de FakerPHP](https://fakerphp.org/)

## Relacionar la factory con el modelo

Para utilizar la factory, nuestro modelo `Post` debe utilizar el trait **`HasFactory`**.

Para ello, abriremos el archivo `app/Models/Post.php` y lo definiremos de la siguiente manera:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;

class Post extends Model
{
    use HasFactory;

    protected $fillable = ['title', 'content'];
}
```

Gracias al trait `HasFactory`, podremos utilizar la factory correspondiente desde el modelo `Post`.

```php
Post::factory()
```

Laravel relacionará el modelo `Post` con la clase `PostFactory` utilizando las convenciones de nombres.

!!! note "Convenciones de nombres"
    Laravel relaciona automáticamente el modelo `Post` con la clase `PostFactory` si utilizamos esos nombres.

    Por eso, conviene nombrar las factories siguiendo la estructura `NombreDelModeloFactory`.

## Utilizar las factories

Después de crear la factory y relacionarla con el modelo, podemos generar registros automáticamente.

Para ello, utilizaremos los métodos `factory()`, `count()`, `make()` y `create()` que ofrece Eloquent.

### Crear registros: `make()` y `create()`

La diferencia entre estos dos métodos es importante.

**Método `make()`:**

```php
$posts = Post::factory()->count(10)->make();
```

Crea diez objetos `Post`, pero **no los guarda en la base de datos**.

**Método `create()`:**

```php
$posts = Post::factory()->count(10)->create();
```

Crea diez objetos `Post` y **los guarda en la tabla `posts` de la base de datos**.

| Método | ¿Crea objetos? | ¿Los guarda en la base de datos? |
|---|---|---|
| `make()` | Sí | No |
| `create()` | Sí | Sí |

### ¿Cuántos registros queremos crear?

Con el método `count()` podemos indicar cuántos registros queremos crear.

Por ejemplo:

```php
Post::factory()->count(10)->create();
```

Creará diez registros.

```php
Post::factory()->count(100)->create();
```

Creará cien registros.

Si queremos crear un solo registro, no hace falta utilizar `count()`:

```php
Post::factory()->create();
```

## Utilizar factories desde DatabaseSeeder

Normalmente utilizaremos las factories desde la clase `DatabaseSeeder` para llenar la base de datos con datos de prueba.

Para ello, importaremos el modelo `Post` en el archivo `database/seeders/DatabaseSeeder.php`:

```php
use App\Models\Post;
```

Después, llamaremos a la factory desde el método `run()`:

```php
public function run(): void
{
    Post::factory()->count(10)->create();
}
```

En este caso, cuando ejecutemos `php artisan db:seed`, se crearán diez registros nuevos en la tabla `posts`.

### Utilizar seeders y factories juntos

En la sección anterior creamos `PostSeeder` para insertar datos manualmente.

Ahora podemos utilizar ese seeder y la factory juntos:

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\Post;

class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        // Insertar los datos definidos manualmente
        $this->call(PostSeeder::class);

        // Crear 10 registros aleatorios
        Post::factory()->count(10)->create();
    }
}
```

En este caso:

1. Primero se ejecutará `PostSeeder` y se insertarán los dos registros definidos en él.
2. Después, se utilizará `PostFactory` para crear otros diez registros.

Por tanto, si al principio la tabla `posts` está vacía, tendremos **12 registros** en total.

## Llenar la base de datos

Una vez preparado `DatabaseSeeder`, utilizaremos el siguiente comando para insertar los datos:

```bash
php artisan db:seed
```

Este comando ejecutará los seeders y las factories a las que llamen para crear registros.

Si queremos volver a crear la base de datos desde cero y llenarla con datos de prueba:

```bash
php artisan migrate:fresh --seed
```

Este comando:

1. Elimina todas las tablas de la base de datos.
2. Ejecuta todas las migraciones.
3. Ejecuta `DatabaseSeeder`, que utilizará los seeders y las factories definidos en él.

!!! warning "¡Cuidado!"
    El comando `migrate:fresh --seed` elimina todas las tablas de la base de datos y los datos que contienen.

    Es adecuado para utilizarlo en un entorno de desarrollo, pero no cuando necesitamos conservar los datos.

!!! tip
    Si ejecutamos `php artisan db:seed` varias veces, las factories crearán nuevos registros cada vez.

    Si queremos devolver la base de datos a su estado inicial y volver a llenarla, podemos utilizar `migrate:fresh --seed` en un entorno de desarrollo.

Documentación oficial: [Eloquent Factories – Laravel 13](https://laravel.com/docs/13.x/eloquent-factories)
