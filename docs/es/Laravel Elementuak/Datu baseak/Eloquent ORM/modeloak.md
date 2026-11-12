# Modelos

Los modelos son componentes que trabajan con datos dentro de las aplicaciones MVC. Los datos pueden proceder de distintos lugares, como API, sistemas de archivos, etc., aunque normalmente trabajaremos con datos de bases de datos.

Para crear modelos, escribiremos la siguiente instrucción:

```bash
php artisan make:model Post
```

Al crear el modelo podemos ver que utiliza Eloquent; gracias a ello podemos utilizar las tablas de la base de datos como si fueran clases.

Documentación de Eloquent: <https://laravel.com/framework/docs/13.x/eloquent>

La clase creada, Post en nuestro caso, extiende la clase Models de Eloquent:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model

{

//

}
```

Gracias a esto podemos utilizar todos los métodos de la clase Models: all(), find(), create(), update(), etc.

Post::all();

En el modelo configuraremos los ajustes de nuestra tabla:

```php
protected $table = "posts";

protected $primaryKey = "id";

protected $fillable = ['title', 'content', 'categoria'];

protected $hidden = ['id'];
```

También podemos crear en el modelo funciones que utilizarán los controladores, aunque lo más habitual y recomendable es hacerlo en el controlador:

```php
public function postakLortu(){

return Post::all();

}

public function showPost($id){

return Post::find($id);

}
```

NOTA: Si queremos que nuestros modelos utilicen una tabla que no siga la convención de Eloquent, por ejemplo, que el modelo Ikasle trabaje con la tabla ikasleak en lugar de con la tabla ikasles, tendremos que indicarlo mediante la propiedad $table:

```php
protected $table = "ikasleak";
```

Siguiente: Seeders
