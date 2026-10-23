# ESKOLA  ADIBIDEA

### N-M erlazioa

Aurreko datu basearekin jarraituz, ikusi dezakegu ikasturte eta ikasleen arteko erlazioa N-M erlazioa da, hau da, ikasle batek hainbat kurtso eman ditzake eta aldiz ikasturte batek hainbat ikasle izango ditu.

Beraz, kasu honetan beste taula bat izango da beharrezkoa, pivot taula.

Horretarako migrazio berri bat egingo dugu, Laravelen konbentzioa jarraituz, erlazionatzen diren bi taulen izenak izango ditu, singularrean eta alfabetikoki ordenaduta, kasu honetan pupils eta courses erlazionatuko ditugunez, taula course\_pupil izango da.

Migrazioa sortzeko:

php artisan make:migration create\_course\_pupil\_table

Migrazioan idatziko duguna, alde batetik id biak sortu eta bestetik klabe foraneak direla esango dugu:

Schema::create('course\_pupil', function (Blueprint $table) {

$table->id();

// N-M erlazioa (Modu 1 - zaharra)

$table->unsignedBigInteger('course\_id');

$table->foreign('course\_id')->references('id')->on('courses')->onDelete('cascade');

$table->unsignedBigInteger('pupil\_id');

$table->foreign('pupil\_id')->references('id')->on('pupils')->onDelete('cascade');

// N-M erlazioa (Modu 2 - modernoa)

$table->foreignId('course\_id')->constrained()->onDelete('cascade');

$table->foreignId('pupil\_id')->constrained()->onDelete('cascade');

$table->timestamps();

});

Ohartu zelan kasu honetan onDelete() egitareakoan "cascade" jarri dugula, kasu honetan, bai ikastaro bat, bai ikasle bat ezabatzerakoan gure dugulako taula honetatik ere ezabatu daitezen.

Behin eginda, gogoratu exekutatzen.

php artisan migrate

![](Erlazioak%20N-M/d3f908b413f8c5df0204ed448148b720.jpg)

### Eloquent erlazioak

Erlazioak modeloetan adierazi behar dira ere. Horretarako hainbat gauza izan behar ditugu kontutan

- Ikasle batek hainbat kurtsotan egon daiteke eta kurtso bakoitzean hainabt ikasle daude, beraz badugu N-M erlazioa bat.

![](Erlazioak%20N-M/454d84617f4971038fa4301bef27836b.jpg)

Hori definitzeko, Pupil modeloan, belongsToMany metodoa erabiliko dugu, metodo honek definitzen bait du N-M erlazioak.

public function courses(){

return $this->belongsToMany(Course::class);

}

Eta modu berean ere, Course modeloan ere Ikasleekiko erlazioa definituko dugu:

public function pupils(){

return $this->belongsToMany(Pupil::class);

}

### Seeders - pupil\_course

"Courses" Seeder

php artisan make:seeder CourseSeeder

Gogoratu modelo biak gehitu behar ditugula.

use App\Models\Course;

use App\Models\Pupil;

Pivot taulan datuak sartzeko, gure kasuan course\_pupil, Eloquent baditu bere funtzioak, eta euren artean attach() dugu.

$course1->pupils()->attach([1, 2, 3]);

$course2->pupils()->attach([2, 4]);

$course3->pupils()->attach([1, 3, 5]);

DataBaseSeeder-a exekutatuko dugu, horretarako beharrezkoa den deia jarriko dugu:

$this->call(CourseSeeder::class);

Eta orain bai exekutatu dezakegula gure Seederra:

php artisan db:seed

![](Erlazioak%20N-M/f32d76c068263a7fdf8ca44861babab3.jpg)

Ikastaroak jada sortuta izatekotan, hartuko ditugu ikastaroren baten bat eta guk nahi ditugun ikasle gehituko ditugu, horretarako ondo legoke beste seeder berri bat sortu, CoursePupilSeeder adibidez.

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

### Attach - Detach - Sync

Ikasle baten ikastaroak eguneratzeko, bi eragiketa nagusi egin daitezke pivot taulan:

Ikastaro berriak gehitu → attach()

Ikastaroak kendu → detach()

Adibidea: formulario batetik datozen ikastaroak

Demagun formulario batek course\_ids array bat bidaltzen duela: course\_ids = [1, 3, 5] eta Ikasle hori ikastaro horietan matrikulatu nahi dugula.

### attach() – erlazioak gehitzeko

attach() metodoak pivot taulan erlazio berriak gehitzen ditu:

$pupil->courses()->attach([1, 3, 5]);

Aurretik erlazioa bazegoen, bikoizketak sor daitezke, funtzioak ez du kontuan hartzen zein ikastaro kendu behar diren, horregatik, eguneraketa egiteko bakarrik ez da nahikoa. Beraz update egin bahi izatekotan, lehenago detach egin beharko genuke.

### detach() – erlazioak kentzeko

detach() erabil dezakegu erlazioak ezabatzeko:

$pupil->courses()->detach([2, 4]);

Honek ikaslea 2. eta 4. ikastaroetatik desmatrikulatzen du.

Era berean, erlazio guztiak ezabatzeko horrela egingo genuke:

$pupil->courses()->detach();

Matrikularen edizioa egiteko attach + detach konbinatu bearko genuke

Beraz ikaslearen matrikulazioa eguneratzeko, logika hau egin genezake:

- Uneko ikastaroak lortu
- Zein kendu behar diren kalkulatu
- Zein gehitu behar diren kalkulatu
- attach() eta detach() erabili

Horrelako zeozer egin beharko genuke horretarako:

$selectedCourses = $request->input('course\_ids', []);

$currentCourses = $pupil->courses->pluck('id')->toArray();

$toAttach = array\_diff($selectedCourses, $currentCourses);

$toDetach = array\_diff($currentCourses, $selectedCourses);

$pupil->courses()->attach($toAttach);

$pupil->courses()->detach($toDetach);

Baina horrela eginez, kodea luzea eta konplexua bihurtzen da, beraz sync funtzioa erabiliko genuke.

### sync() -  attach eta detach sinkronizatzen ditu

attach() eta detach() erabiliz, ematen zaizkion ID-ak bakarrik utziko ditu pivot taulan eta beste erlazio guztiak automatikoki ezabatuko ditu.

public function enrollUpdate(Request $request, Pupil $pupil)

{

$pupil->courses()->sync($request->input('course\_ids', []));

return redirect()->route('pupils.index')

->with('success', 'Ikaslearen matrikulazioa eguneratu da!');

}

### Pivot taulan atributuak gehitzea

Batzuetan N-M erlazioan ez da nahikoa bi IDak gordetzea. Adibidez, ikasle batek ikastaro batean noiz matrikulatu den, nota, edo rola (ordezkaria, arrunta…) gorde nahi izan dezakegu.

Horretarako, pivot taulan atributu gehigarriak gehituko ditugu: enrolled\_at eta grade, bat date motatakoa eta bigarrena integer motakoa.

Schema::create('course\_pupil', function (Blueprint $table) {

$table->id();

$table->unsignedBigInteger('course\_id');

$table->foreign('course\_id')->references('id')->on('courses')->onDelete('cascade');

$table->unsignedBigInteger('pupil\_id');

$table->foreign('pupil\_id')->references('id')->on('pupils')->onDelete('cascade');

// Pivot atributuak

$table->date('enrolled\_at')->nullable();

$table->integer('grade')->nullable();

$table->timestamps();

});

Gogoratu, taula dagoeneko sortuta badago, migrazio berri bat sortu beharko genuke add\_fields\_to\_course\_pupil\_table izenekoa erabiliz.

### Pupil modeloa

public function courses()

{

return $this->belongsToMany(Course::class)

->withPivot('enrolled\_at', 'grade')

->withTimestamps(); // Horrela created\_at eta updated\_at gordeko ditu attach egitean.

}

### Course modeloa

public function pupils()

{

return $this->belongsToMany(Pupil::class)

->withPivot('enrolled\_at', 'grade')

->withTimestamps();

}

### attach atributuekin

Lehen horrela egiten genuena:

$course1->pupils()->attach([1, 2, 3]);

Orain, atributuak izatean horrela egin beharko dugu:

$course1->pupils()->attach([

1 => ['enrolled\_at' => '2024-09-01', 'grade' => 8],

2 => ['enrolled\_at' => '2024-09-01', 'grade' => 6],

3 => ['enrolled\_at' => '2024-09-01', 'grade' => 9],

]);

### sync() funtzioa

Lehen genuena:

$course1->pupils()->sync([1, 2, 4]);

Orain horrela egin beharko dugu:

$course1->pupils()->sync([

1 => ['enrolled\_at' => '2024-09-01', 'grade' => 7],

2 => ['enrolled\_at' => '2024-09-01', 'grade' => 8],

4 => ['enrolled\_at' => '2024-09-10', 'grade' => null],

]);

### Pivot taulan dagoen atributoa berrezkuratzeko

Pivot taulan dauden datuak hartzeko:

$enrolledPupil->pivot->enrolled\_at

$enrolledPupil->pivot->grade

Erabiltzen dugu ->pivot, enrolled\_at eta grade pivot taulan dauden datuak direlako.

Aldiz, $enrolledPupil->courses erabiliko genuke, ->pivot erabili barik, courses Pupil Modeloan definituta dagoen erlazioa delako.
