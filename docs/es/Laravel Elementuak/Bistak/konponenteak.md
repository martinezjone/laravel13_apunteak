# Componentes

Un componente es una plantilla Blade que podemos reutilizar en varias vistas. Por ejemplo, podemos definir una vez el HTML de una alerta y luego mostrar un mensaje distinto en cada página utilizando el mismo componente.

[Documentación de Laravel 13](https://laravel.com/docs/13.x/blade#components)

## Crear un componente anónimo

Crea el archivo `resources/views/components/alert.blade.php`. Un componente anónimo consta de una vista Blade.

```blade
<div class="alerta" role="alert">
    <strong>¡Alerta!</strong> ¡Ten cuidado!
</div>
```

Para utilizarlo en otra vista, añade el prefijo `x-` al nombre del archivo y elimina la extensión `.blade.php`.

```blade
<x-alert />
```

Si no tenemos que pasar contenido, podemos cerrar la etiqueta en la misma línea. Para pasar contenido, utilizaremos las etiquetas de apertura y cierre.

### Slot predeterminado

El contenido escrito entre las etiquetas del componente se recibe mediante la variable `$slot`. Este contenido puede ser texto o HTML.

Modifica el archivo `alert.blade.php`.

```blade
<div class="alerta" role="alert">
    <strong>¡Alerta!</strong>
    {{ $slot }}
</div>
```

Ahora podemos proporcionar un mensaje cada vez que utilicemos la alerta.

```blade
<x-alert>
    Comprueba los datos introducidos.
</x-alert>
```

### Slots con nombre

Para insertar el mensaje y el título en lugares diferentes del componente, podemos pasar el título como un slot con nombre. En este ejemplo, el contenido de `<x-slot:title>` se recibe mediante `$title` y el resto mediante `$slot`.

```blade
<x-alert>
    <x-slot:title>
        Problema al guardar los datos
    </x-slot:title>

    Comprueba los datos introducidos.
</x-alert>
```

```blade
<div class="alerta" role="alert">
    <strong>{{ $title ?? '¡Alerta!' }}</strong>
    {{ $slot }}
</div>
```

Si la vista no proporciona el slot `title`, aparecerá «¡Alerta!».

### Parámetros de los componentes

También podemos pasar atributos en la etiqueta. Por ejemplo, podemos utilizar `type` para elegir el aspecto de la alerta.

```blade
<x-alert type="danger">
    <x-slot:title>
        Problema al guardar los datos
    </x-slot:title>

    Comprueba los datos introducidos.
</x-alert>
```

Al principio del componente anónimo, indicaremos mediante la directiva `@props` qué atributos queremos recibir. Al escribir `@props(['type' => 'info'])`, el componente tendrá la variable `$type`. Si no proporcionamos `type` en la llamada, se utilizará `info`.

Este es el archivo completo `resources/views/components/alert.blade.php`.

```blade
@props(['type' => 'info'])

@php
    $clase = match ($type) {
        'danger' => 'alerta-danger',
        'success' => 'alerta-success',
        default => 'alerta-info',
    };
@endphp

<div {{ $attributes->class(['alerta', $clase]) }} role="alert">
    <strong>{{ $title ?? '¡Alerta!' }}</strong>
    {{ $slot }}
</div>
```

Para ver los colores, añade estos estilos al archivo `public/css/style.css` asociado al layout.

```css
.alerta {
    padding: 1rem;
    border-radius: 0.5rem;
}

.alerta-info {
    background-color: #dbeafe;
    color: #1e40af;
}

.alerta-danger {
    background-color: #fee2e2;
    color: #991b1b;
}

.alerta-success {
    background-color: #dcfce7;
    color: #166534;
}
```

### Atributos adicionales

Los atributos que no se reciben mediante `@props` están disponibles a través de `$attributes`. Por ejemplo, podemos asignar un `id` y una clase CSS adicional a una alerta concreta.

```blade
<x-alert type="info" id="mensaje-guardado" class="resaltado">
    Para guardar los cambios, pulsa «Guardar».
</x-alert>
```

El atributo `type` se recibe como la variable `$type`. En cambio, `id` y `class` están dentro de `$attributes`. Al utilizar `$attributes->class(...)`, la clase `resaltado` pasada en la llamada se añade a las clases `alerta` y `alerta-info` del componente. El atributo `id` también se inserta en el elemento `<div>`.

## Componentes basados en clases

Cuando un componente necesita más lógica PHP, podemos utilizar una clase PHP y una vista Blade. Para probar el ejemplo, crea el componente `Alert2`.

```bash
php artisan make:component Alert2
```

El comando crea la clase `app/View/Components/Alert2.php` y la vista `resources/views/components/alert2.blade.php`. Las propiedades públicas recibidas en el constructor se pueden utilizar en la vista Blade. Por ejemplo, podemos calcular la clase CSS de la alerta en el constructor.

```php
<?php

namespace App\View\Components;

use Illuminate\Contracts\View\View;
use Illuminate\View\Component;

class Alert2 extends Component
{
    public string $clase;

    public function __construct(string $type = 'info')
    {
        $this->clase = match ($type) {
            'danger' => 'alerta-danger',
            'success' => 'alerta-success',
            default => 'alerta-info',
        };
    }

    public function render(): View
    {
        return view('components.alert2');
    }
}
```

En el archivo `resources/views/components/alert2.blade.php`, podemos utilizar `$clase` directamente.

```blade
<div {{ $attributes->class(['alerta', $clase]) }} role="alert">
    <strong>{{ $title ?? '¡Alerta!' }}</strong>
    {{ $slot }}
</div>
```

```blade
<x-alert2 type="success">
    Los datos se han guardado correctamente.
</x-alert2>
```

## Utilizar un layout como componente

En la sección sobre layouts hemos utilizado `@extends`, `@section` y `@yield` para compartir la estructura común de las páginas. También podemos definir la misma estructura en un componente anónimo. En este caso, recibiremos el contenido principal de la página mediante `$slot`.

Crea el archivo `resources/views/components/app-layout.blade.php`.

```blade
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ $title ?? 'Mi blog' }}</title>
    <link rel="stylesheet" href="{{ asset('css/style.css') }}">
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
        {{ $slot }}
    </main>

    @include('partials.footer')
</body>
</html>
```

Hemos reutilizado el archivo `resources/views/partials/footer.blade.php`, creado en la sección sobre layouts, para incluir el pie de página. Ahora podemos utilizar el layout en el archivo `resources/views/home.blade.php`.

```blade
<x-app-layout>
    <x-slot:title>Inicio</x-slot:title>

    <h1>¡Bienvenido a mi blog!</h1>
    <p>Esta es la página de inicio.</p>

    <x-alert type="success">
        Los datos se han guardado correctamente.
    </x-alert>
</x-app-layout>
```

El slot `title` establece el título de la pestaña del navegador. El resto del contenido se inserta en el elemento `<main>` del layout.
