# Componentes

Los componentes Blade son un mecanismo que ofrece Laravel para convertir código HTML o Blade en elementos reutilizables.

Se utilizan a menudo para:

- Definir elementos de diseño una sola vez y utilizarlos en todas las páginas.
- Reutilizar partes de formularios (input, button, card…).
- Mantener estructuras de layout.
- Mostrar mensajes, alertas o modales de forma coherente.

Los componentes se guardan en la carpeta resources > views > components y tendrán la extensión blade.php.

Tipos de componentes

Los componentes pueden ser de dos tipos:

- Componentes clásicos (class-based components)

El componente tiene su propia clase (un archivo PHP), que puede contener lógica o código; en este caso también se separan la lógica y el diseño.

- Componentes simples (anonymous components)

Estos, en cambio, no tienen un archivo de clase, solo un archivo Blade; se utilizan para componentes sencillos y pequeños.

Crear un componente

- Componentes clásicos (class-based components)

```bash
php artisan make:component Alert
```

Crea:

- app/View/Components/Alert.php ← lógica
- resources/views/components/alert.blade.php ← HTML

Por ejemplo:

Alert.php:

```php
public function __construct(public $type = 'success')

{

}
```

HTML:

```php
<div class="alert alert-{{ $type }}">

{{ $slot }}

</div>
```

Uso:

```html
<x-alert type="error">Errorea gertatu da</x-alert>
```

- Componentes simples (anonymous components)

Estos se crearán manualmente:

resources/views/components/alert.blade.php

HTML:

```php
@props(['type' => 'success'])

<div class="alert alert-{{ $type }}">

{{ $slot }}

</div>
```

Uso (igual que el anterior):

```html
<x-alert type="error">Errorea gertatu da</x-alert>
```

Pasar parámetros

Pasaremos los parámetros mediante props.

Por ejemplo, el componente podría ser algo así:

```php
{{-- alert.blade.php --}}

@props(['message', 'type' => 'success'])

<div>

{{ $type }} - {{ $message }}

</div>
```

Y para invocar el componente lo haríamos así:

```html
<x-alert :type="$mota" :message="$variableBat" />
```
