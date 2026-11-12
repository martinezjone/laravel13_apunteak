# Ejemplo escolar

## Relación N-M

Continuando con la base de datos anterior, podemos ver que la relación entre los cursos y los estudiantes es N-M; es decir, un estudiante puede cursar varios cursos y, a su vez, un curso tendrá varios estudiantes.

Por tanto, en este caso necesitaremos otra tabla: una tabla pivote.

Para ello crearemos una nueva migración. Siguiendo la convención de Laravel, tendrá los nombres de las dos tablas relacionadas en singular y ordenados alfabéticamente. Como vamos a relacionar pupils y courses, la tabla será course\_pupil.

Para crear la migración:

```bash
php artisan make:migration create_course_pupil_table
```

En la migración crearemos ambos id e indicaremos que son claves foráneas:

```php
Schema::create('course_pupil', function (Blueprint $table) {

$table->id();

// N-M erlazioa (Modu 1 - zaharra)

$table->unsignedBigInteger('course_id');

$table->foreign('course_id')->references('id')->on('courses')->onDelete('cascade');

$table->unsignedBigInteger('pupil_id');

$table->foreign('pupil_id')->references('id')->on('pupils')->onDelete('cascade');

// N-M erlazioa (Modu 2 - modernoa)

$table->foreignId('course_id')->constrained()->onDelete('cascade');

$table->foreignId('pupil_id')->constrained()->onDelete('cascade');

$table->timestamps();

});
```

Fijaos en que en este caso, al configurar onDelete(), hemos puesto "cascade", porque queremos que, al eliminar un curso o un estudiante, también se eliminen sus registros de esta tabla.

Una vez hecho, recordad ejecutarla.

```bash
php artisan migrate
```

![Tabla pivote](../../assets/images/Erlazioak%20N-M/d3f908b413f8c5df0204ed448148b720.jpg)

## Relaciones Eloquent

También debemos indicar las relaciones en los modelos. Para ello, debemos tener en cuenta varias cosas:

- Un estudiante puede estar matriculado en varios cursos y cada curso tiene varios estudiantes; por tanto, tenemos una relación N-M.

![Relación entre estudiantes y cursos](../../assets/images/Erlazioak%20N-M/454d84617f4971038fa4301bef27836b.jpg)

Para definirla, usaremos el método belongsToMany en el modelo Pupil, ya que este método define relaciones N-M.

```php
public function courses(){

return $this->belongsToMany(Course::class);

}
```

Del mismo modo, definiremos también en el modelo Course la relación con los estudiantes:

```php
public function pupils(){

return $this->belongsToMany(Pupil::class);

}
```

## Seeders - pupil\_course

Seeder de "Courses"

```bash
php artisan make:seeder CourseSeeder
```

Recordad que debemos añadir los dos modelos.

```php
use App\Models\Course;

use App\Models\Pupil;
```

Para insertar datos en la tabla pivote, en nuestro caso course\_pupil, Eloquent tiene sus propias funciones; entre ellas está attach().

```php
$course1->pupils()->attach([1, 2, 3]);

$course2->pupils()->attach([2, 4]);

$course3->pupils()->attach([1, 3, 5]);
```

Ejecutaremos DataBaseSeeder; para ello, añadiremos la llamada necesaria:

```php
$this->call(CourseSeeder::class);
```

Ahora sí podemos ejecutar nuestro Seeder:

```bash
php artisan db:seed
```

![Datos de la tabla pivote](../../assets/images/Erlazioak%20N-M/f32d76c068263a7fdf8ca44861babab3.jpg)

Si ya tenemos creados los cursos, elegiremos uno y añadiremos los estudiantes que queramos. Para ello, sería conveniente crear otro seeder nuevo, por ejemplo CoursePupilSeeder.

```php
use App\Models\Course;

use App\Models\Pupil;

class CoursePupilSeeder extends Seeder

{

public function run(): void

{

$course1 = Course::find(1);

$course1->pupils()->attach([1,2,3]);

$course2 = Course::find(2);

$course2->pupils()->attach([4,6,10]);

$course3 = Course::find(3);

$course3->pupils()->attach([1,2,3,9]);

}
```

## Attach - Detach - Sync

Para actualizar los cursos de un estudiante, se pueden realizar dos operaciones principales en la tabla pivote:

Añadir cursos nuevos → attach()

Quitar cursos → detach()

Ejemplo: cursos recibidos desde un formulario

Supongamos que un formulario envía un array course\_ids: course\_ids = [1, 3, 5], y que queremos matricular a ese estudiante en esos cursos.

## attach() – añadir relaciones

El método attach() añade nuevas relaciones a la tabla pivote:

```php
$pupil->courses()->attach([1, 3, 5]);
```

Si ya existía una relación, pueden producirse duplicados. La función no tiene en cuenta qué cursos deben eliminarse, por lo que no basta para realizar una actualización. Por eso, si queremos actualizar, primero deberíamos ejecutar detach.

## detach() – eliminar relaciones

Podemos usar detach() para eliminar relaciones:

```php
$pupil->courses()->detach([2, 4]);
```

Esto da de baja al estudiante de los cursos 2 y 4.

Del mismo modo, para eliminar todas las relaciones haríamos lo siguiente:

```php
$pupil->courses()->detach();
```

Para editar la matrícula, tendríamos que combinar attach + detach.

Por tanto, para actualizar la matrícula del estudiante, podríamos hacer lo siguiente:

- Obtener los cursos actuales
- Calcular cuáles hay que quitar
- Calcular cuáles hay que añadir
- Usar attach() y detach()

Para ello, haríamos algo así:

```php
$selectedCourses = $request->input('course_ids', []);

$currentCourses = $pupil->courses->pluck('id')->toArray();

$toAttach = array_diff($selectedCourses, $currentCourses);

$toDetach = array_diff($currentCourses, $selectedCourses);

$pupil->courses()->attach($toAttach);

$pupil->courses()->detach($toDetach);
```

Pero así el código se vuelve largo y complejo, así que usaríamos la función sync.

## sync() - sincroniza attach y detach

Al usar attach() y detach(), solo dejará en la tabla pivote los ID que se le pasen y eliminará automáticamente las demás relaciones.

```php
public function enrollUpdate(Request $request, Pupil $pupil)

{

$pupil->courses()->sync($request->input('course_ids', []));

return redirect()->route('pupils.index')

->with('success', 'Ikaslearen matrikulazioa eguneratu da!');

}
```

## Añadir atributos a la tabla pivote

A veces, en una relación N-M no basta con guardar los dos ID. Por ejemplo, podríamos querer guardar cuándo se matriculó un estudiante en un curso, su nota o su función (representante, estudiante corriente...).

Para ello, añadiremos atributos adicionales a la tabla pivote: enrolled\_at y grade; el primero será de tipo date y el segundo, integer.

```php
Schema::create('course_pupil', function (Blueprint $table) {

$table->id();

$table->unsignedBigInteger('course_id');

$table->foreign('course_id')->references('id')->on('courses')->onDelete('cascade');

$table->unsignedBigInteger('pupil_id');

$table->foreign('pupil_id')->references('id')->on('pupils')->onDelete('cascade');

// Pivot atributuak

$table->date('enrolled_at')->nullable();

$table->integer('grade')->nullable();

$table->timestamps();

});
```

Recordad que, si la tabla ya está creada, debemos crear una nueva migración llamada add\_fields\_to\_course\_pupil\_table.

## Modelo Pupil

```php
public function courses()

{

return $this->belongsToMany(Course::class)

->withPivot('enrolled_at', 'grade')

->withTimestamps(); // Horrela created_at eta updated_at gordeko ditu attach egitean.

}
```

## Modelo Course

```php
public function pupils()

{

return $this->belongsToMany(Pupil::class)

->withPivot('enrolled_at', 'grade')

->withTimestamps();

}
```

## attach con atributos

Antes lo hacíamos así:

```php
$course1->pupils()->attach([1, 2, 3]);
```

Ahora, al tener atributos, tendremos que hacerlo así:

```php
$course1->pupils()->attach([
```

1 => ['enrolled\_at' => '2024-09-01', 'grade' => 8],

2 => ['enrolled\_at' => '2024-09-01', 'grade' => 6],

3 => ['enrolled\_at' => '2024-09-01', 'grade' => 9],

]);

## Función sync()

Antes teníamos esto:

```php
$course1->pupils()->sync([1, 2, 4]);
```

Ahora tendremos que hacerlo así:

```php
$course1->pupils()->sync([
```

1 => ['enrolled\_at' => '2024-09-01', 'grade' => 7],

2 => ['enrolled\_at' => '2024-09-01', 'grade' => 8],

4 => ['enrolled\_at' => '2024-09-10', 'grade' => null],

]);

## Recuperar un atributo de la tabla pivote

Para obtener los datos que hay en la tabla pivote:

```php
$enrolledPupil->pivot->enrolled_at

$enrolledPupil->pivot->grade
```

Usamos ->pivot porque enrolled\_at y grade son datos de la tabla pivote.

En cambio, usaríamos $enrolledPupil->courses sin usar ->pivot, porque courses es una relación definida en el modelo Pupil.
