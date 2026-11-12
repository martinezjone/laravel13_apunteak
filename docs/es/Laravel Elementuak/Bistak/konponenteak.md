# Componentes

Los componentes de Laravel son una forma de combinar HTML y PHP de manera más clara y organizada. Estos componentes son, en esencia, bloques de código HTML y PHP que, una vez creados, se pueden reutilizar fácilmente en muchas otras plantillas o páginas. Esto facilita mucho el uso y el mantenimiento de los diseños sin repetir código.

Documentación oficial: <https://laravel.com/framework/docs/13.x/blade#components>

Para crear un componente, dentro de la carpeta view crearemos otra carpeta llamada components y, dentro de ella, crearíamos el archivo Blade de nuestro componente. Por ejemplo:

views/components/alert.blade.php

Por ejemplo, para crear una alerta podemos utilizar las alertas de Tailwind; para ello podemos visitar este sitio web: <https://flowbite.com/docs/components/alerts/>

Por otro lado, también tendremos que indicar en algún lugar que vamos a utilizar Tailwind. Encontrarás información sobre cómo añadirlo en este enlace: <https://tailwindcss.com/docs/installation/play-cdn>

Por tanto, para este ejemplo añadiríamos Tailwind a nuestra página de esta manera:

```html
<script src="<https://cdn.tailwindcss.com>"></script>
```

Y en nuestro archivo alert.blade.php añadiríamos nuestra alerta:

```html
<div class="p-4 mb-4 text-sm text-blue-800 rounded-lg bg-blue-50 dark:bg-gray-800 dark:text-blue-400" role="alert">

<span class="font-medium">Alerta!</span> Kontuz izan egiten duzunarekin

</div>
```

Así ya podríamos utilizar este componente en todos los lugares donde lo necesitemos. Para utilizarlo, primero escribiremos "x-" para indicar que es un componente y, a continuación, el nombre de nuestro componente; en este caso, x-alert. Recuerda que siempre debemos cerrar el componente al invocarlo:

```html
<x-alert/>
```

o

```html
<x-alert>

</x-alert>
```

Componentes con una variable

En este caso queremos pasar una variable a nuestro componente; para ello indicaremos en nuestra alerta dónde queremos mostrarla. Cuando enviamos una sola variable, enviamos un slot.

```php
<span class="font-medium">Alerta!</span> {{ $slot }}
```

Y, al invocarlo, enviaríamos el valor de esa variable entre las etiquetas x-alert:

```html
<x-alert>
```

Este es el contenido del mensaje de alerta

```html
</x-alert>
```

Componentes con más de una variable

Por un lado tendremos la variable general, llamada slot, y por otro tendremos named slots. Hay que asignar un nombre a estos slots; por ejemplo:

```html
<x-alert>

<x-slot name="title">
```

Este es el encabezado

```html
</x-slot>
```

Este es el contenido del mensaje de alerta

```html
</x-alert>
```

Y, al invocarlo, enviaríamos el valor de esa variable entre las etiquetas x-alert:

```php
<span class="font-medium">{{ $title ?? 'lehenetsiko titulua' }}</span> {{ $slot }}
```

¿Por qué hemos puesto los signos de interrogación y las comillas? Al escribirlo así indicamos que, si no se define title, tendrá el valor que aparece entre las comillas.

Parámetros de los componentes (componente único)

Además de pasar variables a un componente, también podemos pasar parámetros; lo haremos en la etiqueta del componente. Por ejemplo, pasaremos un parámetro llamado type para que, según el tipo de alerta, tenga un aspecto u otro.

Lo haríamos así:

```html
<x-alert type="info">

<x-slot name="title">
```

Este es el encabezado

```html
</x-slot>
```

Este es el contenido del mensaje de alerta

```html
</x-alert>
```

Y en nuestra alerta definiremos distintas alertas según cuál sea el tipo. Para ello, Blade tiene una directiva llamada props. Al invocarla, incluiré en un array los nombres de mis parámetros; Laravel creará una variable para cada parámetro que le haya pasado:

```php
@props(['title'])

@props(['type' => 'info') // Balio lehenetsia emateko
```

Ahora introduciremos código PHP dentro de nuestro componente para indicar qué alerta se mostraría para cada tipo.

```php
@php

switch ($type) {

    case 'info':

        $class = "bg-blue-50 text-blue-800";

        break;

    case 'danger':

        $class = "bg-red-50 text-red-800";

        break;

    case 'success':

        $class = "bg-green-50 text-green-800";

        break;

    case 'warning':

        $class = "bg-yellow-50 text-yellow-800";

        break;

    case 'dark':

        $class = "bg-gray-50 text-gray-800";

        break;

    default:

        # code...

        break;

}

@endphp

<div class="p-4 mb-4 text-sm {{ $class }}  rounded-lg  dark:bg-gray-800 dark:text-blue-400" role="alert">

    <span class="font-medium">{{ $title ?? '' }}</span> {{ $slot }}

</div>
```

Parámetros de los componentes (más de un componente)

Además de pasar variables a un componente, también podemos pasar parámetros; lo haremos en la etiqueta del componente. Por ejemplo, pasaremos un parámetro llamado type para que, según el tipo de alerta, tenga un aspecto u otro.

Lo haríamos así:

```html
<x-alert type="info" class="mb-4">

<x-slot name="title">
```

Este es el encabezado

```html
</x-slot>
```

Este es el contenido del mensaje de alerta

```php
</x-alert>

Kasu honetan bigarren parametro hori, promps-en jaso beharrean $attributes-en jasoko genuke. Adibide honetan, gure konponentean  {{$attributes}} idaztekotan, pantailatik zuznean class="mb-4" agertuko litzateke.
```

"Componentes de clase"

Hasta ahora hemos utilizado lo que se llama "componente anónimo", pero también existe la opción de crear componentes de clase. Para ello lo crearemos con Artisan. Un componente anónimo tendrá una sola vista, pero al crear un componente de clase se generará una clase para contener toda la lógica de la alerta y, por otro lado, una vista para definir el aspecto de ese componente.

```bash
php artisan make:component alert2
```

Se creará una clase en la carpeta app/View/Components. Se creará en minúsculas, pero para seguir las convenciones es mejor poner en mayúsculas tanto el archivo como la clase:

```php
class Alert2 extends Component
```

Ahora incluiríamos en el método __construct de la clase la lógica que escribimos antes, y dejaríamos en la vista únicamente el código HTML.
