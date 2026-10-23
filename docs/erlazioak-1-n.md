# ESKOLA  ADIBIDEA

Behin ikusita zelan sortzen den proiektu bat eta zelan egin dezakegun lan horregaz, jarraituko dugu aurrera....

Gure taula "Pupil", "Teacher" eta "Course" taulekin erlazionatuko dugu. Hurrengo entidad-relacion jarraituz...

Adibide hau jarraitzeko taulak ingeleraz erabiliko ditugu eta baita euren propietateak ere.

![](Erlazioak%201-N/b31d03fcf0611b3ad5a7fb5f976b6b03.jpg)

### 1-N erlazioa

### Teacher Migrazioa

Hasiko dugu beraz Teacher migrazioa sortzen, modeloa ere aldi berean egingo dugu, ikusten dugun moduan, laravel-en konbentzioa jarraitzerakoan, taula "teachers" izango da.

# Generate a model and a migration, factory, seeder, and controller...

php artisan make:model Teacher -mfsc

Eta migrazioa, zehaztuko diogu zelakoa gure dugu gure taula izatea:

$table->string('fullName', 75);

$table->string('profession', 100);

$table->string('academicDegree', 35)->nullable();

$table->string('phone', 35)->nullable();

### Teacher Moduloa

Behin migrazioa eginda, gure moduloa sortuko dugu:

use HasFactory;

protected $fillable = ['fullName', 'profession', 'academicDegree', 'phone'];

Oraingoan migrazioak fresh batekin exekutatuko ditugu.

php artisan migrate

### Curso Migrazioa

Lehenengo esta behin Modeloa sortuko dugu, kasu honetan Course izenarekin:

# Generate a model and a migration, factory, seeder, and controller...

php artisan make:model Course -mfsc

Eta migrazioa, zehaztuko diogu zelakoa gure dugu gure taula izatea, kasu honetan eremu berezi bat dugu, gure teacher\_id eremua, kasu honetan foreign key bat izango dena, teachers taularen id; hori zehazteko hurrengo adibidean dugun moduan jarriko dugu:

$table->string('name', 75);

$table->string('level', 35);

$table->string('academicHours', 35)->nullable();

// 1-N erlazioa egiteko (Modu 1)

$table->unsignedBigInteger('teacher\_id');

$table->foreign('teacher\_id')->references('id')->on('teachers');

// 1-N erlazioa egiteko (Modu 2, modernoagoa)

$table->foreignId('teacher\_id')

->nullable()

->constrained()

->cascadeOnDelete(); // o nullOnDelete()

### Course Modeloa

Modeloa aldatzera goaz orain:

use HasFactory;

protected $fillable = ['name', 'level', 'academicHours', 'teacher\_id'];

Exekutatuko ditugu gure migrazioak berriro:

php artisan migrate

Ikusten dugunez, gure taula biak sortu ditu eta baita euren arteko erlazioa ere.

Orain faltako litzaiguke ikasle eta ikastaroen-en artekoa sortzen; kasu honetan ezberdin egin beharko dugu, N-M erlazioa dalako.

![](Erlazioak%201-N/1e58eab060ff47b7ec7256d75f44bc31.jpg)

### Eloquent erlazioak - Modeloetan

Erlazioak modeloetan adierazi behar dira ere. Horretarako hainbat gauza izan behar ditugu kontutan

- Irakasle batek hainbat kurtso eman ditzake eta kurtso bat irakasle batek bakarrik eman dezake, beraz badugu 1-N erlazioa bat.

![](Erlazioak%201-N/153805d26b6892e6777d46992039a966.jpg)

Hori definitzeko, Teacher modeloan, hasMany metodoa erabiliko dugu, metodo honek definitzen bait du 1-N erlazioak.

public function courses(){

return $this->hasMany(Course::class);

}

![](Erlazioak%201-N/c230cf1368baaf0302d958ffb337baa4.jpg)

![](Erlazioak%201-N/11e3f6a1513fd3911c0f0c1f78ec1ac7.jpg)

Eta kontrakoa definitzeko, hau da, N-M erlazioa, belongsTo metodoa erabiliko dugu, hori Course modeloan definituko dugu:

public function teacher(){

return $this->belongsTo(Teacher::class);

}

### Factory - erlazioak

Factory Teacher

Sortuko dugu gure Factory-a datu gehiago izateko:

php artisan make:factory TeacherFactory

Factory horren definition metodoan, gure taularen eremu guztiak definituko ditugu:

public function definition(){

return [

'fullName' => $this->faker->name(),

'profession' => $this->faker->jobTitle(),

'academicDegree'=>$this->faker->randomElement(['Ing. Sistemas', 'Ing. Informatico', 'Adm. Empresas']),

'phone' => $this->faker->optional()->phoneNumber(),

];

}

Orain, gure TeacherFactory erabili ahal izateko, gure DataBaseSeeder aldatuko dugu:

Teacher::factory(10)->create();

Factory Pupil

Sortuko dugu gure Factory-a datu gehiago izateko:

php artisan make:factory PupilFactory

Kodea hurrengo hau izanik:

return [

// Ikasle taulan datuak sartzeko erregistroa

'name'=> $this->faker->firstName() . ' '. $this->faker->lastName(),

'age'=>$this->faker->randomElement([18,19,20,21]),

'phone'=>$this->faker->phoneNumber(),

'adress'=>$this->faker->address()

];

DataBaseSeeder aldatuko dugu:

Pupil::factory(50)->create();

Factory Course

php artisan make:factory CourseFactory

Aurrekoetan bezala, gure taularen datuak sartuko ditugu:

return [

'name' => $this->faker->word(),

'level' => $this->faker->randomElement(['Basic', 'Intermediate', 'Advanced']),

'academicHours' => $this->faker->randomElement(['10 Hours', '40 Hours', '80 Hours']),

'teacher\_id' => Teacher::inRandomOrder()->first()->id,

];

Ikusten dugunez azkeneko sententzian, teacher\_id bat sartzeko, hartuko dugu gure Teacher modelotik datu guztiak eta euren artean bat hartuko dugu modu aleatorio batean.

Horretarako, modeloa gehitu behar diogu: use App\Models\Teacher;

DataBaseSeeder aldatuko dugu:

Course::factory(10)->create();

Hori guztia egin ostean, Seederrak exekutatuko ditugu:

php artisan db:seed

### Course baten Teacher-a ateratzeko

Course eta Teacher modeloak sortzerakoan, bi metodo sortu ditugu euren arteko erlazioa egiteko, hori dela eta, course baten gaudenean, bere teacher zein den jakiteko, teacher() metodoari deitu dezakegu, hori oso modu errezean egingo dugu:

IKASTAROA {{$course->name}}

Maila: {{$course->level}} <br>

Ordu akademikoak: {{$course->academicHours}}<br>

Irakaslea: {{$course->teacher->fullName}}

### Teacher baten course guztiak ateratzeko

Kasu honetan ikusiko dugu ia gure irakasleak ikastarorik duen a la ez, izatekotan, denak aterako ditugu

@if ($teacher->courses->isNotEmpty())

<ul>

@foreach ($teacher->courses as $course)

<li>

<a href="{{ route('teachers.show', $course) }}">

{{ $course->name }}

</a>

</li>

@endforeach

</ul>

@endif
