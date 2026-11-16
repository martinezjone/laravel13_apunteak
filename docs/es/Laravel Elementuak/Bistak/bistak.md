# Vistas

Todas las vistas de Laravel se guardan en la carpeta ```/resources/views ```. Y normalmente, por convención, el nombre de la vista será el mismo que el nombre del método que la ha invocado.

Las vistas de Laravel pueden utilizar php o blade; la extensión de la vista nos indicará cuál de los dos se está utilizando.

```php
nombre\_vista.php

nombre\_vista.blade.php // Siempre trabajaremos con vistas blade
```

Blade es un motor de plantillas sencillo pero potente que viene incluido con Laravel. En este caso, nuestras plantillas blade también admiten código php.

Podemos invocar las vistas tanto desde las rutas como desde el controlador, pero normalmente las invocarán los controladores.

```php
Route::get('user', function(){

// Mostraremos la vista user.php o user.blade.php

return view('user');

});
```

Se recomienda utilizar subcarpetas para tener organizadas nuestras vistas. Para invocar estas vistas podemos utilizar tanto la barra como el punto. Una vista guardada en ```user/index.blade.php``` se puede invocar de las siguientes formas:

```php
Route::get('user', function(){

return view('user/index');

return view('user.index'); // Normalmente se utiliza así

});
```

## Pasar variables a una vista

Para pasar variables a una vista lo haremos mediante un array asociativo: `return view('grupo.vista', $arrayAsociativo);`

Por ejemplo:

```php
return view('user.index', ['name' => 'James']);
```

```php
return view('blog.history', [

'mes' => $mes,

'ano' => $ano,

'posts' => $posts

]);
```

También existe la opción de hacerlo mediante with:

```php
return view('greeting')->with('name', 'Victoria');
```

También se puede utilizar ```compact```. Da igual de qué manera pasemos los datos; normalmente depende de la forma de trabajar del programador o de la convención del equipo, pero esta es la más utilizada:

```php
    return view('blog.history', compact('mes', 'ano', 'posts'));

    // Es equivalente a lo siguiente:

    return view('blog.history', [
    'mes' => $mes,
    'ano' => $ano,
    'posts' => $posts

    ]);
```
**compact()** recibe los nombres de las variables como cadenas, sin el símbolo $. La función crea un array asociativo: la clave es el nombre de la variable y el valor es el valor de la variable.

Por ejemplo, escribir ```compact('mes')``` equivale a escribir ```['mes' => $mes]```.
Si queremos utilizar otro nombre en la vista, podemos especificar la clave manualmente. Por ejemplo, al pasar ```['mesEnVista' => $mes]```, en la plantilla Blade utilizaremos ```{{ $mesEnVista }} ```.


## Vistas de Laravel - Blade

Laravel tiene un motor de plantillas llamado Blade, que nos será muy útil para trabajar con vistas.

Esta es la página oficial de Laravel: <a href ="https://laravel.com/framework/docs/13.x/blade" target="_blank">https://laravel.com/framework/docs/13.x/blade</a>

- Las vistas de Laravel se pueden crear mezclando php y html.
- A veces, este código híbrido puede resultar difícil de entender.
- Por eso se utilizan plantillas; de este modo, será más fácil insertar en html el código que contiene variables.

### Mostrar datos

Mostrar datos en Blade es muy sencillo; para ello solo hay que utilizar llaves dobles {{ }} e incluir dentro la variable o función que queremos mostrar.

```php
Hola {{ $name }}.

La hora actual es: {{ time() }}.
```

Todo el código php debe escribirse dentro de esas llaves.

### Mostrar un dato solo si existe

Para asegurarnos de que existe una variable o un dato concreto, podemos utilizar el siguiente método:

```php
{{ isset($name) ? $name : 'Valor predeterminado' }}
```

O también podemos utilizar esta notación de Blade:

```php
{{ $name ?? 'Valor predeterminado' }}
```

| Estado de `$name` | Lo que aparece en la vista |
|---|---|
| `'Jon'` | `Jon` |
| Sin definir | `Valor predeterminado` |
| `null` | `Valor predeterminado` |
| `''` (cadena vacía) | No aparece ningún texto |
| `0` | `0` |

Si queremos comprobar si la cadena está vacía:

```php
{{ isset($name) && $name !== '' ? $name : 'Valor predeterminado' }}
```


### Insertar código PHP

Para insertar código php en nuestra vista en un momento dado, debemos utilizar las siguientes etiquetas:

```php
@php

    // Aquí iría el código

@endphp
```

### Comentarios

Para escribir comentarios dentro de Laravel utilizaremos {{-- y --}}. Estos comentarios no se mostrarán en el html:

```php
{{-- Este comentario no se mostrará en el html --}}
```


### Estructuras de control

En Blade podemos utilizar las estructuras **IF** de la siguiente manera:

```php
    @if (count($users) === 1)
        ¡Solo hay un usuario!
    @elseif (count($users) > 1)
        ¡Hay más de un usuario!
    @else
        No hay ningún usuario :(
    @endif
```

### if(!condicion){} - If negativo

Para utilizar un if negativo, sería así:

```php
    @unless (count($users) > 0)
        No hay ningún usuario.
    @endunless
```

La directiva @unless muestra el contenido que hay dentro cuando la condición es falsa. En este ejemplo, la condición count($users) > 0 es falsa cuando el número de usuarios es 0. Entonces aparece el mensaje «No hay ningún usuario».

### Bucles (for, while y foreach)

Así los utilizaríamos dentro de Blade:

```php
@for ($i = 0; $i < 10; $i++)

    El valor de la variable es: {{ $i }}

@endfor
```

```php
@foreach ($users as $user)

    <p>El usuario {{ $user->name }} con su id: {{ $user->id }}</p>

@endforeach
```

**@forelse** funciona como un @foreach y, si la lista está vacía, muestra el mensaje del bloque @empty.

```php
@forelse ($users as $user)

    <li>{{ $user->name }}</li>

@empty

    <p>No hay ningún usuario</p>

@endforelse
```

```php
@while (true)

    <p>¡Soy un bucle infinito!</p>

@endwhile
```

Para ver más opciones, consulta el enlace de Laravel: <a href="https://laravel.com/framework/docs/13.x/blade" target="_blank">https://laravel.com/framework/docs/13.x/blade</a>
