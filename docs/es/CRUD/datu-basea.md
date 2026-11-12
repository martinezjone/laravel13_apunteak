# Bases de datos en Laravel

Según la versión de Laravel que se use, no será necesario realizar este paso porque la propia versión crea la base de datos. Si no es así, estos pasos serán imprescindibles:

Crearemos una base de datos para este proyecto. En phpMyAdmin crearemos una base de datos llamada "eskola", por ahora sin ninguna tabla. Solo crearemos la base de datos; el resto —tablas, datos, etc.— lo haremos con Artisan.

Ahora debemos indicar a Laravel cuál es la base de datos que usaremos. Para ello, en el directorio raíz hay un archivo llamado ".env". En él indicaremos dónde está y cuál es el nombre de nuestra base de datos.

```dotenv
DB_CONNECTION=mysql

DB_HOST=127.0.0.1

DB_PORT=3306

DB_DATABASE=eskola

DB_USERNAME=root

DB_PASSWORD=root
```

Nuestra base de datos tendrá 3 tablas, como podéis ver en el esquema siguiente, aunque por ahora empezaremos con la tabla "Alumnos". Recordad que Laravel usará la nomenclatura inglesa, así que también crearemos los nombres de las tablas en inglés:

pupils, courses, teachers y users.

En las convenciones de Laravel, los nombres de las tablas siempre se escriben en plural.

![Esquema de la base de datos](../../assets/images/Datu%20basea/b31d03fcf0611b3ad5a7fb5f976b6b03.jpg)

## Migración

1. Una vez hecho esto, crearemos la migración. Gracias a ella, Laravel creará las tablas que indiquemos en nuestra base de datos sin usar sentencias SQL. Todo esto será muy fácil con Artisan.

Se puede hacer con o sin modelo. Lo más cómodo será hacerlo con el modelo, pero así se seguirá la convención de Laravel: el nombre del modelo irá en singular y con mayúscula inicial, mientras que el nombre de la tabla irá en minúsculas y en plural. Por supuesto, se usará el plural inglés.

Recordad que, al crear una migración, siempre debemos estar ubicados en el directorio correspondiente.

- Migración + modelo:

```bash
php artisan make:Model Pupil -m
```

- Si solo queremos crear la migración, lo haremos así:

```bash
php artisan make:migration create_pupils_table
```

El nombre indicado en la migración anterior es una convención. De esta forma, al crear la migración, se generará el método create dentro de up(), que creará la tabla pupils.

```bash
php artisan make:migration pupils
```

Si solo ponemos esto, no se crearían esos métodos.

![Archivo de migración](../../assets/images/Datu%20basea/0de43c3e50d9fac12c2ca5bfb60b8411.jpg)

Comprobaremos que la migración se haya creado correctamente. Para ello, abriremos el archivo database > migrations > 2025\_11\_10\_060212\_create\_pupils\_table.php.

Por ahora no se ha creado nada en la base de datos.

2. Abriremos este archivo PHP e indicaremos cómo será la tabla. Como vemos, Laravel ya ha generado código en el método up(); en este caso, crearía una tabla de estudiantes con dos campos: id y timestamps. Lo cambiaremos por los datos que queremos:

```php
Schema::create('pupils', function (Blueprint $table) {

$table->id();

$table->string('name', 75);

$table->integer('age');

$table->string('phone', 35)->nullable();

$table->string('address', 75)->nullable();

$table->timestamps();

});
```

Ahora sí, ejecutaremos la migración con Artisan:

```bash
php artisan migrate
```

Para saber si se ha realizado correctamente, entraremos en la base de datos. Fijaos en los campos que se han creado.

NOTA: Como vemos, al crear la migración, Laravel crea 3 columnas: la primera es id y las otras dos se crean mediante timestamps: created\_at y updated\_at. Estas dos columnas se actualizan cuando se crean o modifican los datos, con la fecha y hora en que se han insertado o cambiado.

Si no queremos esto, tendremos que eliminarlo de la migración, pero recordad también establecer ese campo en false al crear el modelo:

```php
public $timestamps = false;
```

Como vemos, al crear la migración, nuestra clase Migration tendrá dos funciones: up() y down(). En la primera se creará la tabla con los campos que indiquemos. En cambio, se llamará a la función down() si queremos deshacer la migración; por eso, esta función solo elimina la tabla.

## Seeders

Los seeders son componentes de Laravel que permiten insertar datos en la base de datos. Normalmente sirven para introducir datos durante el desarrollo o para cargar los datos necesarios al iniciar una aplicación.

¿Cómo lo haríamos?

Desde nuestro directorio, escribiríamos esta instrucción de Artisan para crear PupilSeeder:

```bash
php artisan make:seeder PupilSeeder
```

El archivo se creará en database > seeders.

En el archivo indicaremos cuál es nuestro modelo; para ello, crearemos la referencia a la clase Pupil:

```php
use App\Models\Pupil;
```

Crearemos la variable $pupil, le asignaremos la clase Pupil y rellenaremos todas sus propiedades como se muestra en la imagen. Como veis, usaremos las tablas de nuestra base de datos como objetos.

![Configuración del seeder](../../assets/images/Datu%20basea/37045aa61b6b7aa5ba214898b3cdea4b.jpg)

![Datos del seeder](../../assets/images/Datu%20basea/9abb0fc000f9bb1c46cf80827465225f.jpg)

Al ejecutar los seeders, Artisan siempre llama a la clase DatabaseSeeder; por tanto, para ejecutar nuestro seeder, "llamaremos" (instanciaremos) nuestra clase desde el método run() de esa clase.

Se puede llamar a más de uno desde este método; se ejecutarán en el orden en que se escriban manualmente.

```php
$this->call(PupilSeeder::class);
```

Una vez hecho todo esto, ejecutaremos el seeder para cargar los datos en nuestra base de datos:

```bash
php artisan db:seed
```

## Factories

Documentación: <https://laravel.com/framework/docs/13.x/seeding>

Los seeders son una buena opción cuando tenemos pocos datos, pero hay otra opción para cuando necesitamos muchos datos de prueba: los factories. Son clases pensadas para el desarrollo.

Los factories son bastante fáciles de usar; hay que seguir estos pasos:

1. Crearemos una clase llamada PupilFactory:

```bash
php artisan make:factory PupilFactory
```

2. Como vemos, se ha creado el archivo PupilFactory.php en la carpeta database > factories.
3. Dentro de la función Definition asignaremos un valor aleatorio a cada campo de la tabla; podemos hacerlo gracias al componente Faker.
4. Faker es una biblioteca de PHP que genera datos aleatorios: nombres, direcciones, teléfonos, etc.

Para ver todos los datos que puede generar Faker, visita [este enlace](https://github.com/fzaninotto/Faker).

5. Siguiendo con nuestro ejemplo, crearíamos un registro como este:

![Ejemplo de Factory](../../assets/images/Datu%20basea/0a5678144436ee140eefad70a19d36d7.jpg)

6. Ahora, en DatabaseSeeder, debemos indicar que se ejecute el Factory en lugar de llamar primero al Seeder. Como se puede ver en el código, podemos indicar cuántas veces lo hará:

```php
use App\Models\Pupil;

public function run()    {

// $this->call(PupilSeeder::class);
```

Pupil::factory(10)->create();

}

7. Una vez hecho todo esto, también debemos indicar en nuestro modelo que se usará un factory y cuáles serán los campos que se podrán rellenar, es decir, cuáles serán fillable:

```php
use HasFactory;

protected $fillable = ['name', 'age', 'phone', 'address'];
```

![Modelo y campos fillable](../../assets/images/Datu%20basea/96c5b823146a03e885e9737fcb695443.jpg)

Una vez hecho esto, volveremos a ejecutar el seeder:

```bash
php artisan db:seed
```
