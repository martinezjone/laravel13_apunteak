# Ejemplo escolar

## 1. Paginación

Laravel creó una forma muy sencilla de paginar en su versión 9. Esta vez, al obtener nuestros estudiantes, en lugar de llamar al método all(), llamaremos al método paginate().

1. En el método index de PupilController, cambiaremos el método all() por el método paginate():

```php
$pupils = Pupil::paginate();
```

Con lo anterior solo hemos indicado que queremos obtener los estudiantes por grupos; por defecto, obtendrá los registros de la base de datos de 15 en 15, en nuestro caso los de estudiantes.

Pero podemos cambiarlo indicando como parámetro del método paginate cuántos registros queremos obtener:

```php
$pupils = Pupil::paginate(10);
```

Otro método que podemos usar es orderBy, que podemos combinar con otros métodos, por ejemplo:

```php
$pupils = Pupil::orderBy('id','desc')->paginate();
```

2. En la vista index.blade.php pondremos los enlaces de paginación usando la siguiente directiva:

```php
{{ $pupils ->links() }}
```

3. Con eso, la paginación ya estará hecha.

## 2. Validación

Laravel tiene varias funciones que nos ayudarán a validar datos.

Antes que nada, comprobaremos qué datos son obligatorios en nuestra base de datos, qué tipo tienen, etc.

![](../../assets/images/Balidazioa%20paginazioa%20eta%20hizkuntza/be04c2230966cf48a0e364ae9578d7fe.jpg)

1. Controlaremos los errores dentro del método store(). Usamos como parámetro del método store() el objeto Request, que recoge todos los datos del formulario. Después, pasamos al método validate() un array donde se especifican los campos del formulario y sus reglas de validación.

Laravel indica que, si en algún momento no se cumple una validación, no se pasará a la siguiente línea de código y se mostrará un error. En cambio, cuando todas las validaciones sean correctas, se continuará.

En nuestro caso, escribiremos esto en el método store() para validar todos nuestros datos:

```php
$validated = $request->validate([
    'name' => 'required|string|max:75',
    'age' => 'required|integer|min:0',
    'phone' => 'required|string|max:20',
    'address' => 'required|string|max:75',
]);

Pupil::create($validated);
```

Mostrar los errores en mensajes

2. Ahora veremos nuestros formularios. En ellos imprimiremos mensajes cuando haya errores de validación definidos en nuestros controladores para los campos del formulario.

3. Antes de empezar a validar los formularios, añadiremos código CSS a todas nuestras vistas para dar estilo a los mensajes de error.
```html
p { color: red; }
```

Validar el formulario en la vista de estudiantes

4. En la vista create.blade.php añadiremos todas las validaciones.

En el campo Nombre y apellidos situado debajo del elemento Input, añadiremos el código de validación de ese campo con la directiva que nos ofrece Blade. La opción @error permite imprimir el mensaje de error correspondiente a ese campo.

```php
@error('name')
    <p class="error-message">{{ $message }}</p>
@enderror
```

5. Añadiremos al input el método old(), una directiva de Blade. Así conseguiremos conservar el dato introducido antes de la validación.

```php
<input type="text" name="name" placeholder="Izena" value="{{ old('name') }}">
```

6. Haremos lo mismo con el campo de edad:

```php
<label>Adina:</label>

<input type="text" name="age" placeholder="Adina" value="{{ old('age') }}">

@error('age')
    <p class="error-message">{{ $message }}</p><br>
@enderror
```

7. También conservaremos el teléfono y la dirección introducidos:

```php
<label>Telefonoa:</label>
<input type="text" name="phone" placeholder="Telefonoa" value="{{ old('phone') }}">
<label>Helbidea:</label>
<input type="text" name="address" placeholder="Helbidea" value="{{ old('address') }}">
```

8. ¿Qué ocurre si hacemos la validación al editar? En ese caso, la primera vez que accedamos al formulario queremos obtener el dato de la base de datos y, si hay un error, el valor que nos devuelve el método old. Para hacerlo, pasaremos un segundo parámetro al método old:

```php
<label>Telefonoa:</label>

<input type="text" name="phone" placeholder="Telefonoa" value="{{ old('phone', $pupil->phone) }}">
```

Como vemos, los mensajes aparecerán en inglés. Si queremos cambiarlo, tendremos que cambiar el idioma.

9. Hay otra forma de mostrar todos los errores desde el principio:

```php
@if ($errors->any())
    Errors:

    <ul>
        @foreach ($errors->all() as $error)

         <li>{{ $error }}</li>

        @endforeach

    </ul>

@endif
```

## 3. Idioma

Al probar los mensajes de error de nuestro formulario, veremos que aparecen en inglés. Para cambiarlo, debemos configurar el idioma en Laravel.

1. Publicar los archivos de idioma

Ejecuta en el terminal:

```bash
php artisan lang:publish
```

Esto creará una carpeta en en dentro de lang.

2. Instalar los idiomas

No tenemos que copiarlos y traducirlos manualmente; es más fácil añadir los idiomas usando Laravel-Lang:

```bash
composer require laravel-lang/common --dev
```

Después añadimos los idiomas que queramos:

```bash
php artisan lang:add es    // Gaztelera

php artisan lang:add eu    // Euskara
```

Esto creará las carpetas lang/es y lang/eu completamente traducidas.

3. Indicar a Laravel qué idioma debe usar

En el archivo .env cambiaremos lo siguiente:

```dotenv
APP_LOCALE=eu

APP_FALLBACK_LOCALE=eu
```

Después de esto, la aplicación estará en euskera.

4. Personalizar los nombres de los campos de los formularios

En los mensajes de validación, Laravel usa automáticamente los nombres de los campos de los formularios (name, email, izen\_abizen, etc.).

Si queremos que estos campos se muestren de forma más “amable”,

iremos al archivo lang/eu/validation.php y los escribiremos en la sección attributes, situada al final:

```php
'attributes' => [
'izen\_abizen' => 'Izen Abizenak',
'email' => 'Posta Elektronikoa',
'password' => 'Pasahitza',
'name' => 'Izena',
];
```

Así, los mensajes de error aparecerán traducidos y con el formato adecuado.

No hay que hacer nada más (ni actualizar JSON ni volver a cargar nada).

5. Idioma de reserva recomendado

Si también está instalado el español, puede ser recomendable:

```dotenv
APP_FALLBACK_LOCALE=es
```

Así, si falta algún texto en euskera, se usará el español y no el inglés.

## Resumen

```bash
php artisan lang:publish
composer require laravel-lang/common --dev
php artisan lang:add es, php artisan lang:add eu
```


```php
.env → APP\_LOCALE=eu
```

validation.php → personalizar los nombres de los campos en la sección attributes
