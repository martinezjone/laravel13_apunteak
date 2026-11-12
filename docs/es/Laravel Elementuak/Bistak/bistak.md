# Vistas

Todas las vistas de Laravel se guardan en la carpeta /resources/views. Por convención, el nombre de la vista suele ser el mismo que el del método que la invoca.

Las vistas de Laravel pueden utilizar PHP o Blade; la extensión de la vista indica cuál se está utilizando.

bista\_izena.php

bista\_izena.blade.php // Siempre trabajaremos con vistas Blade

BLADE es un motor de plantillas sencillo pero potente que viene incluido con Laravel. En este caso, nuestras plantillas Blade también admiten código PHP.

Podemos invocar las vistas tanto desde las rutas como desde los controladores, aunque normalmente las invocan los controladores.

```php
Route::get('user', function(){

//user.php edo user.blade.php bistaratuko dugu

return view('user');

});
```

Se recomienda utilizar subcarpetas para organizar las vistas. Para invocarlas, podemos utilizar tanto la barra como el punto. Una vista guardada en user/index.blade.php se puede invocar de las siguientes formas:

```php
Route::get('user', function(){

return view('user/index');

return view('user.index'); // Normalean horrela erabiltzen da

});
```

Pasar variables a una vista

Para pasar variables a una vista utilizaremos un array asociativo: `return view('grupo.vista', $arrayAsociativo);`

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

También existe la opción de hacerlo con with:

```php
return view('greeting')->with('name', 'Victoria');
```

También se puede usar compact. Da igual de qué manera pasemos los datos; normalmente depende de la forma de trabajar del programador o de la convención del equipo, aunque esta es la más utilizada:

```php
return view('users', compact('mes', 'ano', 'posts'));

//hurrengo honen berdina da:

return view('blog.history', [

'mes' => $mes,

'ano' => $ano,

'posts' => $posts

]);
```

Compact envía, en realidad, un array asociativo.

Vistas de Laravel - Blade

Laravel dispone de un motor de plantillas llamado Blade, que nos será muy útil para trabajar con vistas.

Esta es la página oficial de Laravel: <a href ="https://laravel.com/framework/docs/13.x/blade" target="_blank">https://laravel.com/framework/docs/13.x/blade</a>

- Las vistas de Laravel pueden crearse mezclando PHP y HTML.
- A veces, este código híbrido puede resultar difícil de entender.
- Por eso se utilizan plantillas: así resulta más sencillo insertar en HTML el código que contiene variables.

## Mostrar datos

Mostrar datos en Blade es muy sencillo: basta con utilizar llaves dobles {{ }} e incluir dentro la variable o función que queremos mostrar.

```php
Kaixo {{ $name }}.

Momentu honetako ordua da: {{ time() }}.
```

Todo el código PHP debe escribirse dentro de esas llaves.

## Mostrar un dato solo si existe

Para comprobar si existe una variable o un dato concreto, podemos utilizar el siguiente método:

```php
{{ isset($name) ? $name : 'Lehenetsiko balioa' }}
```

O también la siguiente notación de Blade:

```php
{{ $name or 'Lehenetsiko balioa' }}
```


## Insertar código PHP

Para insertar código PHP en una vista, debemos utilizar las siguientes etiquetas:

```php
@php

    // Hemen kodea egongo litzateke

@endphp
```

## Comentarios

Para escribir comentarios dentro de Laravel utilizaremos {{-- y --}}. Estos comentarios no se mostrarán en HTML:

```php
{{-- Iruzkin hau ez da html-an erakutsiko --}}
```


## Estructuras de control

En Blade podemos utilizar las estructuras IF de la siguiente manera:

```php
@if( count($users) == 1 )

    Erabiltzaile bakarra dago!

@elseif (count($users) > 1)

    Erabiltzaile asko daude!

@else

    Ez dago erabiltzailerik :(

@endif
```

// (if(!condicion){}) Para utilizar un if negativo, sería así

```php
@unless (Auth::check())

    You are not signed in.

@endunless
```

Los bucles (for, while y foreach) se utilizarían así dentro de Blade:

```php
@for ($i = 0; $i < 10; $i++)

    Aldagaiaren balioa da: {{ $i }}

@endfor
```

```php
@foreach ($users as $user)

    <p>Erabiltzailea {{ $user->name }} bere id-arekin: {{ $user->id }}</p>

@endforeach
```

```php
@forelse ($users as $user)

    <li>{{ $user->name }}</li>

@empty

    <p>Ez dago erabiltzailerik</p>

@endforelse
```

```php
@while (true)

    <p>Bukle infinito bat naiz!</p>

@endwhile
```

Para ver más opciones, consulta el enlace de Laravel: <a href="https://laravel.com/framework/docs/13.x/blade" target="_blank">https://laravel.com/framework/docs/13.x/blade</a>

Incluir una plantilla dentro de otra

En Laravel podemos indicar que una plantilla se incluya dentro de otra; para ello utilizaremos @include:

```php
@include('view_name')
```

También podemos pasarle un array en el segundo parámetro de include:

```php
@include('view_name', array('some'=>'data'))
```

Esto resulta muy útil para escribir código que utilizaremos más de una vez.

Siguiente: layouts
