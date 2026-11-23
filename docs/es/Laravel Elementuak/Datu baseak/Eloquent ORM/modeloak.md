# Modelos

## ¿Qué son los modelos?

Los modelos son componentes que se utilizan para trabajar con datos dentro de la arquitectura **MVC (Model-View-Controller)**.

Los datos pueden proceder de distintas fuentes, como API, archivos o bases de datos. En nuestro caso, normalmente trabajaremos con **datos de bases de datos**.

Laravel utiliza **Eloquent ORM** (*Object-Relational Mapping*) para trabajar con la base de datos.

Gracias a Eloquent, podemos utilizar las tablas de la base de datos mediante clases PHP y gestionar sus registros como objetos. Así, no tenemos que escribir directamente consultas SQL para realizar las operaciones habituales.

Por ejemplo, para obtener todos los registros de la tabla `posts`, con SQL escribiríamos la siguiente consulta:

```sql
SELECT * FROM posts;
```

Con Eloquent, en cambio:

```php
$posts = Post::all();
```

En ambos casos obtendremos todos los registros de la tabla `posts`, pero Eloquent nos devolverá una colección de objetos del modelo `Post`.

Documentación oficial: [Eloquent ORM – Laravel 13](https://laravel.com/docs/13.x/eloquent)

## Crear un modelo

Para crear un modelo nuevo, utilizaremos el siguiente comando de Artisan:

```bash
php artisan make:model Post
```

Este comando creará el archivo `Post.php` en la carpeta `app/Models`.

La clase generada tendrá la siguiente estructura:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    //
}
```

Como podemos ver, la clase `Post` extiende la clase `Model` de Eloquent (`extends Model`).

Gracias a ello, podemos utilizar los métodos que ofrece la clase `Model`, por ejemplo, `all()`, `find()`, `create()`, `update()` o `delete()`.

!!! note "Ten en cuenta"
    El comando `make:model` crea el modelo, pero **no crea la tabla en la base de datos**.

    Para crear la tabla tendremos que utilizar una migración, como hemos visto en la sección anterior.

## Relación entre los modelos y las tablas

Laravel utiliza convenciones de nombres para relacionar automáticamente los modelos con las tablas de la base de datos.

Por ejemplo, cuando creamos el modelo `Post`, Eloquent supone que trabajará con la tabla llamada `posts`.

Las principales convenciones son las siguientes:

| Elemento | Convención | Ejemplo |
|---|---|---|
| Modelo | En singular y formato PascalCase | `Post` |
| Tabla | En plural, minúsculas y formato snake_case | `posts` |
| Clave primaria | `id` | `id` |
| Fecha de creación | `created_at` | `created_at` |
| Fecha de actualización | `updated_at` | `updated_at` |

Por tanto, cuando utilizamos el modelo `Post`, Laravel buscará automáticamente la tabla `posts` y supondrá que el identificador de cada registro está en la columna `id`.

### ¿Qué ocurre si no seguimos las convenciones?

Imaginemos que tenemos el modelo `Post`, pero que la tabla de la base de datos se llama `blog_entries`.

En ese caso, tendremos que indicar a Laravel qué tabla debe utilizar mediante la propiedad `$table`:

```php
class Post extends Model
{
    protected $table = 'blog_entries';
}
```

Así, el modelo `Post` trabajará con la tabla `blog_entries` en lugar de con la tabla `posts`.

!!! tip
    Si seguimos las convenciones de nombres de Laravel, no tendremos que definir la propiedad `$table`.

    Por eso, conviene crear los nombres de los modelos y las tablas siguiendo esas convenciones.

## Propiedades de los modelos

Dentro del modelo podemos definir varias propiedades para especificar cómo trabajará Eloquent con la base de datos.

Algunas de las más habituales son:

| Propiedad | Descripción |
|---|---|
| `$table` | Especifica el nombre de la tabla de la base de datos asociada al modelo. |
| `$primaryKey` | Especifica el nombre de la clave primaria de la tabla. |
| `$fillable` | Especifica qué atributos se pueden rellenar mediante asignación masiva (`create()`, `update()`...). |

### `$table` y `$primaryKey`

Como hemos visto, Laravel utiliza algunas convenciones. Sin embargo, si es necesario, podemos cambiar esos valores:

```php
protected $table = 'posts';
protected $primaryKey = 'id';
```

En este caso, ambas propiedades serían innecesarias, porque Laravel utiliza esos valores por defecto.

### `$fillable`

La propiedad `$fillable` especifica **qué atributos se pueden rellenar o modificar mediante asignación masiva**.

La asignación masiva consiste en asignar a la vez los valores de varios atributos mediante un array.

Por ejemplo, si la tabla `posts` tiene las columnas `title` y `content`, podemos definir el modelo de la siguiente manera:

```php
class Post extends Model
{
    protected $fillable = ['title', 'content'];
}
```

Ahora podemos utilizar el método `create()` para crear un registro nuevo:

```php
Post::create([
    'title' => 'My first post',
    'content' => 'This is the content of my first post.'
]);
```

Eloquent tomará los datos del array y creará un registro nuevo en la tabla `posts`.

!!! warning "¿Por qué es importante `$fillable`?"
    Imaginemos que nuestra tabla tiene una columna llamada `is_admin`.

    Si un usuario envía datos mediante un formulario, no queremos que pueda cambiar el atributo `is_admin` a su voluntad.

    Con `$fillable` podemos limitar los atributos que se pueden modificar mediante asignación masiva.

    Ten en cuenta que `$fillable` **no valida los datos**: para eso tendremos que validar por separado los datos recibidos desde el formulario.

## Métodos básicos de Eloquent

Eloquent ofrece varios métodos para consultar, crear, modificar y eliminar registros de la base de datos.

Estos son algunos de los métodos que más utilizaremos:

| Método | Descripción |
|---|---|
| `Post::all()` | Obtiene todos los registros de la tabla. |
| `Post::find(1)` | Busca un registro por su clave primaria. Si no existe, devuelve `null`. |
| `Post::findOrFail(1)` | Busca un registro. Si no existe, genera una excepción que Laravel normalmente convierte en una respuesta 404. |
| `Post::where('title', 'Laravel')->get()` | Obtiene los registros que cumplen una condición. |
| `Post::create([...])` | Crea un registro nuevo. |
| `$post->update([...])` | Actualiza un registro existente. |
| `$post->delete()` | Elimina un registro. |

### Obtener todos los registros: `all()`

Para obtener todos los registros de la tabla `posts`:

```php
$posts = Post::all();
```

Este método devolverá una colección (*Collection*) de objetos `Post`.

Por ejemplo, en un controlador:

```php
public function index()
{
    $posts = Post::all();

    return view('posts.index', compact('posts'));
}
```

En este caso, obtenemos todos los registros y los enviamos a la vista `posts.index`.

### Buscar un registro: `find()`

Para buscar un registro mediante su ID:

```php
$post = Post::find(1);
```

Esto buscará el registro que tenga `id = 1`.

Si el registro no existe, devolverá `null`.

También podemos utilizar `findOrFail()`:

```php
$post = Post::findOrFail(1);
```

En este caso, si el registro no existe, Laravel normalmente mostrará un **error 404** en una solicitud web.

### Buscar con una condición: `where()`

Para obtener los registros que cumplen una condición, podemos utilizar `where()`:

```php
$posts = Post::where('title', 'Laravel')->get();
```

Esta consulta obtendrá todos los registros que tengan el valor `Laravel` en la columna `title`.

!!! note
    El método `where()` prepara la consulta, pero el método `get()` la ejecuta y devuelve los resultados en una colección.

    Si queremos un solo registro, también podemos utilizar `first()`.

## ¿Dónde debemos escribir las consultas?

En la arquitectura MVC, los controladores gestionan las solicitudes de los usuarios y los modelos permiten trabajar con los datos.

Para operaciones sencillas, los controladores pueden utilizar directamente los métodos de Eloquent.

Por ejemplo:

```php
use App\Models\Post;

public function index()
{
    $posts = Post::all();

    return view('posts.index', compact('posts'));
}
```

En este caso:

1. El controlador utiliza el modelo `Post`.
2. Eloquent obtiene los registros de la base de datos.
3. El controlador envía los datos a la vista.

Por otro lado, si necesitamos lógica propia o reutilizable relacionada con los datos, también podemos definir métodos en el modelo.

Por ahora, utilizaremos directamente desde los controladores los métodos básicos que ofrece Eloquent. Más adelante, al desarrollar una aplicación CRUD, trabajaremos con más detalle las operaciones para crear, actualizar y eliminar registros.

Siguiente: Seeders
