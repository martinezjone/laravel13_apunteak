# Sesiones

<https://laravel.com/framework/docs/13.x/session>

Como las solicitudes HTTP son efímeras y no dejan rastro, es importante disponer de un sistema que permita memorizar información entre dos solicitudes. Ese es precisamente el objetivo de las sesiones.

La configuración de las sesiones se encuentra en el archivo de configuración config/session.php. En el archivo .env encontraremos la definición del controlador y el tiempo durante el que se conservará la información:

```dotenv
SESSION_DRIVER=file

SESSION_LIFETIME=120
```

De forma predeterminada, la información de la sesión se memoriza en un archivo (storage/framework/sessions ), pero también puedes utilizar cookies, base de datos, tabla (utilizada para pruebas), memcached, redis…

Guardar datos (put)

Independientemente del controlador utilizado, los helpers de sesión de Laravel permiten simplificar la gestión de sesiones. Por tanto, a partir de la solicitud, puedes crear una variable de sesión:

```php
$request->session()->put('clave' , 'valor');
```

O su helper (mediante helper):

session ([ 'clave' = > 'valor' ]) ;

Leer datos (get)

Para obtener su valor:

```php
$valor = $request->session()->get('clave'); edo $valor = session ( 'clave' ) ;
```

Se puede establecer un valor predeterminado:

```php
$valor = session ( 'clave' , 'balio lehenetsia' ) ;
```

Comprobar si existe un dato

Para saber si una variable concreta está en la sesión, lo haríamos así:

if (session()->has('error')) ...

Esa información se conserva para el mismo cliente en todas las solicitudes. Laravel se encarga de ello; solo le damos un par único clave-valor y se ocupa del resto.

Uso de las sesiones

Podemos acceder a los datos guardados en la sesión de tres maneras:

\* Inyectando Request: para realizar la solicitud, es necesario inyectar la clase:

```php
public function showProfile(Request $request, $id){

$value = $request->session()->get('key');

}
```

\* Session Facade

```php
$value = Session::get('key');
```

\* La función helper session()

```php
$value = session('key');
```

Ver todos los datos de una sesión

Para obtener todos los datos de una sesión:

```php
$data = $request->session()->all();

$data = Session::all(); //usando el Facade
```

Guardar los datos de la sesión

Podemos guardar los datos de la sesión de dos maneras:

```php
$request->session()->put('email', 'me@example.com');
```

session(['email' => 'me@example.com']); //usando el helper

Eliminar datos

También podemos eliminar los datos de la sesión:

```php
$request->session()->forget('key'); //elementu bakarra

$request->session()->flush(); // datu guztiak
```

Session::flush(); 77todos los datos mediante facade

- Solo guarda los datos durante una solicitud (request).
- Para los datos antiguos (old).

Datos flash (solo para 1 request)

Los datos flash duran una sola solicitud; se utilizan mucho en:

- mensajes de error de formularios
- mensajes with('success') o with('error')

Para mensajes de error

//flash datuak eskaera bakarrean irauten dute

```php
$request->session()->flash('mensaje', '¡El usuario fue eliminado!');

//Flash datuak eskaera 1 baino gehiago irauteko

$request->session()->reflash();

//Flash datu zehatz batzuk mantentzeko

$request->session()->keep(['mensaje', 'email']);
```

Aspectos importantes que hay que tener en cuenta

Estos son los métodos básicos de las sesiones; encontrarás toda la información adicional en la documentación: <https://laravel.com/framework/docs/13.x/session>

Hay varios aspectos que conviene tener en cuenta:

- Los parámetros de configuración de las sesiones se definen en config/session.php.
- Es importante el almacenamiento y la duración de la sesión.
- Con Laravel no es necesario utilizar directamente la variable $\_SESSION.
- No debemos utilizar las funciones session\_start() ni session\_destroy().
- El middleware es necesario para utilizar sesiones:

  - Se incluye automáticamente en el grupo de middleware Web (routes/web.php).
  - No está en el middleware API, así que las rutas API no utilizan sesiones de forma predeterminada.

