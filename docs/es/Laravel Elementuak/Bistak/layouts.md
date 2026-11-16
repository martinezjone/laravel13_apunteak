# Layouts

Un layout es una plantilla básica que comparten varias vistas. En él colocamos la estructura común de las páginas, como la cabecera, el menú de navegación y el pie de página. Cada vista define su propio contenido y Blade lo inserta en el lugar correspondiente dentro del layout.

## Crear un layout

Crea el archivo `resources/views/layouts/app.blade.php` con la estructura general del HTML.

```blade
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@yield('title', 'Mi blog')</title>
</head>
<body>
    <header>
        <p>Mi blog</p>
        <nav>
            <a href="/">Inicio</a>
            <a href="/users">Usuarios</a>
        </nav>
    </header>

    <main>
        @yield('content')
    </main>

    <footer>
        <p>Pie de página del sitio web</p>
    </footer>
</body>
</html>
```

La directiva `@yield` indica dónde se insertará una sección dentro del layout. El primer argumento es el nombre de la sección. El segundo argumento es opcional y sirve para especificar un valor predeterminado.

Por ejemplo, si la vista no define la sección `title`, en la pestaña del navegador aparecerá «Mi blog».

## Extender una plantilla

Para que una vista utilice un layout, usaremos la directiva `@extends`. Por ejemplo, crea el archivo `resources/views/home.blade.php`.

```blade
@extends('layouts.app')

@section('title', 'Inicio')

@section('content')
    <h1>¡Bienvenido a mi blog!</h1>
    <p>Esta es la página de inicio.</p>
@endsection
```

El nombre `layouts.app` indica el archivo `resources/views/layouts/app.blade.php`. El punto separa el nombre de la carpeta y el de la vista. En esta llamada de Blade escribimos el nombre sin la extensión del archivo.

La directiva `@section` define el contenido de una sección. El nombre de la sección debe coincidir con el nombre utilizado en la directiva `@yield` del layout. Si el contenido es un texto breve, podemos pasarlo como segundo argumento. Si tenemos que escribir HTML o varias líneas, abriremos la sección con `@section` y la cerraremos con `@endsection`.

Para utilizar el mismo layout en otra vista, crea el archivo `resources/views/users.blade.php`.

```blade
@extends('layouts.app')

@section('title', 'Usuarios')

@section('content')
    <h1>Usuarios</h1>

    <ul>
        @forelse ($users as $user)
            <li>{{ $user->name }}</li>
        @empty
            <li>No hay usuarios.</li>
        @endforelse
    </ul>
@endsection
```

Para probar ambas vistas, añade estas rutas al archivo `routes/web.php`.

```php
use Illuminate\Support\Facades\Route;

Route::view('/', 'home');

Route::get('/users', function () {
    $users = [
        (object) ['name' => 'Jon'],
        (object) ['name' => 'Ane'],
    ];

    return view('users', compact('users'));
});
```

Abre `/` y `/users` en el navegador. Ambas páginas tienen la misma cabecera, menú y pie de página. Elimina la línea `@section('title', 'Usuarios')` y comprueba que en la pestaña del navegador aparece «Mi blog».

## Vistas parciales (partials)

Podemos guardar algunas partes del layout, como el menú o el pie de página, en archivos separados. Estos archivos se llaman **vistas parciales** o *partials*. Para incluirlas en otra vista, utilizaremos la directiva `@include`.

Por ejemplo, extrae el pie de página al archivo `resources/views/partials/footer.blade.php`.

```blade
<footer>
    <p>Pie de página del sitio web</p>
</footer>
```

Después, sustituye el bloque `<footer>` del layout por esta línea.

```blade
@include('partials.footer')
```

## El helper asset()

Si tenemos los estilos de todo el sitio web en el archivo `public/css/style.css`, podemos enlazar la hoja de estilos en la sección `<head>` del layout.

```blade
<link rel="stylesheet" href="{{ asset('css/style.css') }}">
```

El helper `asset()` genera la URL de un archivo público. Como argumento, le pasamos la ruta del archivo desde la carpeta public. Por ejemplo, para enlazar el archivo `public/css/style.css`, utilizamos `asset('css/style.css')`. De esta manera, todas las páginas que utilizan el mismo layout reciben la misma URL para el archivo CSS.

## La directiva @stack

Si una vista concreta necesita un archivo CSS o JavaScript adicional (además de los que hemos incluido en el layout), podemos utilizar `@stack` y `@push`.

Para ello, añadiríamos `@stack('styles')` en la sección `<head>` del layout, después de la hoja de estilos común.

En la sección `<head>` del layout enlazamos el CSS que necesitan todas las páginas e indicamos dónde se insertarán los estilos adicionales; para ello utilizamos `@stack('styles')`.

```blade
    <link rel="stylesheet" href="{{ asset('css/style.css') }}">

    @stack('styles')
```

En la vista `users.blade.php`, añadimos la etiqueta `<link>` a la pila styles para cargar `users.css`. Puedes colocar el bloque después de la línea `@extends`.

```blade
    @extends('layouts.app')

    @push('styles')
        <link rel="stylesheet" href="{{ asset('css/users.css') }}">
    @endpush
```

`@push('styles')` indica qué se debe añadir y `@stack('styles')` dónde se debe insertar. Por eso, la etiqueta `<link>` escrita en la vista `users.blade.php` aparece en la sección `<head> ` del HTML final. Si la vista de inicio no añade nada a la pila styles, `@stack('styles')` no inserta nada.
