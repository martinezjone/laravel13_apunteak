# Relaciones entre tablas

## ¿Qué son las relaciones?

Hasta ahora hemos aprendido a crear tablas en la base de datos, utilizar modelos e insertar datos. Pero en una aplicación, normalmente, **las tablas no son independientes**, sino que están relacionadas entre sí.

Por ejemplo, en un blog:

- Un usuario puede escribir varias publicaciones.
- Cada publicación pertenece a un único usuario.
- Una publicación puede tener varias categorías, y una categoría puede tener varias publicaciones.

Estas relaciones se representan en la base de datos mediante **claves primarias (primary keys)** y **claves foráneas (foreign keys)**.

Laravel, mediante Eloquent ORM, nos ofrece métodos para trabajar con estas relaciones. Así podemos obtener fácilmente datos relacionados sin tener que escribir manualmente consultas SQL complejas.

## Tipos de relaciones

En las bases de datos hay varios tipos de relaciones. Las más habituales en Laravel son:

| Relación | Significado | Ejemplo |
|---|---|---|
| **1-1 (One to One)** | Un registro se relaciona con un único registro de otra tabla. | Un usuario tiene un perfil. |
| **1-N (One to Many)** | Un registro puede tener varios registros de otra tabla relacionados. | Un usuario tiene varias publicaciones. |
| **N-M (Many to Many)** | Varios registros de una tabla pueden relacionarse con varios registros de otra tabla. | Una publicación puede tener varias categorías, y una categoría puede tener varias publicaciones. |

### Relación 1-1 (One to One)

Un registro puede tener un único registro relacionado en otra tabla.

Por ejemplo, entre las tablas `users` y `profiles`:

- Un usuario puede tener un perfil.
- Cada perfil pertenece a un único usuario.

En este caso, tendremos la clave foránea `user_id` en la tabla `profiles`.

### Relación 1-N (One to Many)

Un registro puede tener varios registros relacionados en otra tabla, pero cada uno de ellos pertenece a un único registro de la primera tabla.

Por ejemplo, entre las tablas `users` y `posts`:

- Un usuario puede escribir varias publicaciones.
- Cada publicación pertenece a un único usuario.

Para ello, tendremos la clave foránea `user_id` en la tabla `posts`.

### Relación N-M (Many to Many)

Un registro de una tabla puede relacionarse con varios registros de otra tabla, y viceversa.

Por ejemplo, entre las tablas `posts` y `categories`:

- Una publicación puede tener varias categorías.
- Una categoría puede tener varias publicaciones.

En este caso, necesitamos una **tabla intermedia (pivot table)** para guardar las relaciones.

Por ejemplo, utilizaremos la tabla `category_post`, con las columnas `post_id` y `category_id`.

## Relaciones en Eloquent

En Laravel, las relaciones se definen **mediante métodos en los modelos**.

Según el tipo de relación, Eloquent ofrece distintos métodos:

| Relación | En un modelo | En el modelo relacionado |
|---|---|---|
| 1-1 | `hasOne()` | `belongsTo()` |
| 1-N | `hasMany()` | `belongsTo()` |
| N-M | `belongsToMany()` | `belongsToMany()` |

Por ejemplo, para obtener las publicaciones de un usuario, utilizaríamos `hasMany()` en el modelo `User`:

```php
public function posts()
{
    return $this->hasMany(Post::class);
}
```

Y en el modelo `Post`, para indicar a qué usuario pertenece cada publicación, utilizaríamos `belongsTo()`:

```php
public function user()
{
    return $this->belongsTo(User::class);
}
```

Una vez definidas las relaciones, podemos obtener todas las publicaciones de un usuario:

```php
$user = User::find(1);

$posts = $user->posts;
```

También podemos obtener el usuario de una publicación:

```php
$post = Post::find(1);

$user = $post->user;
```

!!! note "Nombres de las relaciones"
    Por convención, los nombres de los métodos de relación se escriben así:

    - **En singular**, para las relaciones que devuelven un solo registro: `user()`, `profile()`.
    - **En plural**, para las relaciones que devuelven varios registros: `posts()`, `categories()`.

    Al utilizar las relaciones, podemos usar el nombre del método como propiedad, sin paréntesis: `$user->posts`.

## Migraciones y modelos

Es importante distinguir dónde se define cada parte de una relación:

- **En las migraciones:** creamos las claves foráneas de la base de datos y las tablas intermedias necesarias.
- **En los modelos:** definimos las relaciones de Eloquent mediante métodos como `hasMany()`, `belongsTo()` y otros.

!!! tip
    Definir una relación en Eloquent no crea automáticamente la clave foránea en la base de datos. Para ello, debemos utilizar migraciones.

En el siguiente ejemplo práctico veremos cómo crear estas relaciones mediante migraciones, definirlas en los modelos y utilizarlas desde los controladores.

Documentación oficial: [Eloquent Relationships – Laravel 13](https://laravel.com/docs/13.x/eloquent-relationships)
