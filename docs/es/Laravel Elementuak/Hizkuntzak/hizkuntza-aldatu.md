# Cambiar el idioma

Al probar los mensajes de error de nuestro formulario, veremos que aparecen en inglés. Para cambiarlo, hay que configurar el idioma en Laravel.

1. Publicar los archivos de idioma

Ejecuta en el terminal:

```bash
php artisan lang:publish
```

Esto creará una carpeta en en.

2. Instalar los idiomas

No hace falta copiarlos y traducirlos manualmente; es más fácil añadir los idiomas mediante Laravel-Lang:

```bash
composer require laravel-lang/common --dev
```

Después añadimos los idiomas que queramos:

```bash
php artisan lang:add es    // Gaztelera

php artisan lang:add eu    // Euskara
```

Esto creará las carpetas lang/es y lang/eu completamente traducidas.

3. Indicar a Laravel qué idioma debe utilizar

En el archivo .env cambiamos lo siguiente:

```dotenv
APP_LOCALE=eu

APP_FALLBACK_LOCALE=eu
```

Después de esto, la aplicación estará en euskera.

4. Personalizar los nombres de los campos de los formularios

En los mensajes de validación, Laravel utiliza automáticamente los nombres de los campos del formulario (name, email, izen\_abizen, etc.).

Si queremos mostrar estos campos de forma "amable",

vamos al archivo lang/eu/validation.php y los escribimos al final, en la sección attributes:

```php
'attributes' => [

'izen_abizen' => 'Izen Abizenak',

'email' => 'Posta Elektronikoa',

'password' => 'Pasahitza',

'name' => 'Izena',

];
```

Así, los mensajes de error aparecerán traducidos y con el formato adecuado.

No hay que hacer nada más (ni actualizar JSON ni volver a cargar nada).

5. Idioma fallback recomendado

Si también está instalado el español, puede ser recomendable:

```dotenv
APP_FALLBACK_LOCALE=es
```

Así, si falta algún texto en euskera, se utilizará el español y no el inglés.

## Resumen

```bash
php artisan lang:publish

composer require laravel-lang/common --dev

php artisan lang:add es, php artisan lang:add eu
```

.env → APP\_LOCALE=eu

validation.php → personalizar los nombres de los campos en la sección attributes

Enlace interesante: <https://laravel-lang.com/installation/>
