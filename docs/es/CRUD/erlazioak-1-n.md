# Ejemplo escolar

Una vez visto cómo se crea un proyecto y cómo podemos trabajar con él, seguiremos adelante...

Relacionaremos nuestra tabla "Pupil" con las tablas "Teacher" y "Course", siguiendo el siguiente diagrama entidad-relación...

Para seguir este ejemplo usaremos nombres de tablas en inglés y también sus propiedades.

![Diagrama entidad-relación](../../assets/images/Erlazioak%201-N/b31d03fcf0611b3ad5a7fb5f976b6b03.jpg)

## Relación 1-N

## Migración de Teacher

Empezaremos creando la migración Teacher y, al mismo tiempo, también el modelo. Como vemos, siguiendo la convención de Laravel, la tabla será "teachers".

## Generar un modelo y una migración, un factory, un seeder y un controlador...

```bash
php artisan make:model Teacher -mfsc
```

En la migración especificaremos cómo queremos que sea nuestra tabla:

```php
$table->string('fullName', 75);

$table->string('profession', 100);

$table->string('academicDegree', 35)->nullable();

$table->string('phone', 35)->nullable();
```

## Modelo Teacher

Una vez hecha la migración, crearemos nuestro modelo:

```php
use HasFactory;

protected $fillable = ['fullName', 'profession', 'academicDegree', 'phone'];
```

Esta vez ejecutaremos las migraciones con fresh.

```bash
php artisan migrate
```

## Migración de Course

Primero crearemos el modelo, en este caso con el nombre Course:

## Generar un modelo y una migración, un factory, un seeder y un controlador...

```bash
php artisan make:model Course -mfsc
```

En la migración especificaremos cómo queremos que sea nuestra tabla. En este caso tenemos un campo especial, teacher\_id, que será una clave foránea del id de la tabla teachers; para indicarlo lo escribiremos como en el siguiente ejemplo:

```php
$table->string('name', 75);

$table->string('level', 35);

$table->string('academicHours', 35)->nullable();

// 1-N erlazioa egiteko (Modu 1)

$table->unsignedBigInteger('teacher_id');

$table->foreign('teacher_id')->references('id')->on('teachers');

// 1-N erlazioa egiteko (Modu 2, modernoagoa)

$table->foreignId('teacher_id')

->nullable()

->constrained()

->cascadeOnDelete(); // o nullOnDelete()
```

## Modelo Course

Ahora modificaremos el modelo:

```php
use HasFactory;

protected $fillable = ['name', 'level', 'academicHours', 'teacher_id'];
```

Volveremos a ejecutar nuestras migraciones:

```bash
php artisan migrate
```

Como vemos, ha creado las dos tablas y también la relación entre ellas.

Ahora nos faltaría crear la relación entre estudiantes y cursos; en este caso tendremos que hacerlo de otra forma, porque es una relación N-M.

![Relación entre estudiantes y cursos](../../assets/images/Erlazioak%201-N/1e58eab060ff47b7ec7256d75f44bc31.jpg)

## Relaciones Eloquent en los modelos

También debemos indicar las relaciones en los modelos. Para ello, debemos tener en cuenta varias cosas:

- Un profesor puede impartir varios cursos y cada curso solo puede ser impartido por un profesor; por tanto, tenemos una relación 1-N.

![Relación profesor-cursos](../../assets/images/Erlazioak%201-N/153805d26b6892e6777d46992039a966.jpg)

Para definirla, usaremos el método hasMany en el modelo Teacher, ya que este método define relaciones 1-N.

```php
public function courses(){

return $this->hasMany(Course::class);

}
```

![Relación Eloquent en Teacher](../../assets/images/Erlazioak%201-N/c230cf1368baaf0302d958ffb337baa4.jpg)

![Relación Eloquent en Course](../../assets/images/Erlazioak%201-N/11e3f6a1513fd3911c0f0c1f78ec1ac7.jpg)

Y para definir la relación inversa, usaremos el método belongsTo en el modelo Course:

```php
public function teacher(){

return $this->belongsTo(Teacher::class);

}
```

## Factory - relaciones

Factory de Teacher

Crearemos nuestro Factory para tener más datos:

```bash
php artisan make:factory TeacherFactory
```

En el método definition de ese Factory definiremos todos los campos de nuestra tabla:

```php
public function definition(){

return [
```

'fullName' => $this->faker->name(),

'profession' => $this->faker->jobTitle(),

'academicDegree'=>$this->faker->randomElement(['Ing. Sistemas', 'Ing. Informatico', 'Adm. Empresas']),

'phone' => $this->faker->optional()->phoneNumber(),

];

}

Ahora modificaremos nuestro DataBaseSeeder para poder usar TeacherFactory:

Teacher::factory(10)->create();

Factory de Pupil

Crearemos nuestro Factory para tener más datos:

```bash
php artisan make:factory PupilFactory
```

El código será el siguiente:

```php
return [

// Ikasle taulan datuak sartzeko erregistroa
```

'name'=> $this->faker->firstName() . ' '. $this->faker->lastName(),

'age'=>$this->faker->randomElement([18,19,20,21]),

'phone'=>$this->faker->phoneNumber(),

'adress'=>$this->faker->address()

];

Modificaremos DataBaseSeeder:

Pupil::factory(50)->create();

Factory de Course

```bash
php artisan make:factory CourseFactory
```

Como en los casos anteriores, insertaremos los datos de nuestra tabla:

```php
return [
```

'name' => $this->faker->word(),

'level' => $this->faker->randomElement(['Basic', 'Intermediate', 'Advanced']),

'academicHours' => $this->faker->randomElement(['10 Hours', '40 Hours', '80 Hours']),

'teacher\_id' => Teacher::inRandomOrder()->first()->id,

];

Como vemos, en la última sentencia, para insertar un teacher\_id, obtendremos todos los datos del modelo Teacher y elegiremos uno de ellos aleatoriamente.

Para ello, debemos añadir el modelo: use App\Models\Teacher;

Modificaremos DataBaseSeeder:

Course::factory(10)->create();

Una vez hecho todo esto, ejecutaremos los Seeders:

```bash
php artisan db:seed
```

## Obtener el Teacher de un Course

Al crear los modelos Course y Teacher, hemos creado dos métodos para establecer la relación entre ambos. Por eso, cuando estamos en un Course, podemos averiguar quién es su teacher llamando al método teacher() de forma muy sencilla:

```php
IKASTAROA {{$course->name}}

Maila: {{$course->level}} <br>

Ordu akademikoak: {{$course->academicHours}}<br>

Irakaslea: {{$course->teacher->fullName}}
```

## Obtener todos los cursos de un Teacher

En este caso veremos si nuestros profesores tienen algún curso y, si es así, los mostraremos todos.

```php
@if ($teacher->courses->isNotEmpty())

<ul>

@foreach ($teacher->courses as $course)

<li>

<a href="{{ route('teachers.show', $course) }}">

{{ $course->name }}

</a>

</li>
```

@endforeach

```php
</ul>

@endif
```
