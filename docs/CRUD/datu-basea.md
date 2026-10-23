# Datu baseak laravelen

Segun eta ze Laravel bertsio erabiltzen den pausu hau ez da egin behar, berak zuzenean datu basea sortzen duelako, horrela ez izatekotan, hurrengo pausu hauek ezinbestekoak izango dira:

Datu base bat sortuko dugu, proiektu honetarako, gure phpAdmin-en datu base bat "eskola" izeneko datu basea sortuko dugu, momentuz inolako taularik gabe. Bakarrik databasea sortuko dugu, gainontzekoa, taulak, datuak etab. Artisanekin egingo dugu.

Orain, Laraveleri, esan behar diogu zein den erabiliko dugun data basea, horretarako, erroko direktorioan, badago fitxategi bat ".env" izenekoa. Horretan esango diogu gure datu basea non dagoen esta izena ere.

```dotenv
DB_CONNECTION=mysql

DB_HOST=127.0.0.1

DB_PORT=3306

DB_DATABASE=eskola

DB_USERNAME=root

DB_PASSWORD=root
```

Gure DB-ak 3 taula izango ditu, hurrengo eskeman ikusi dezakezuenez, baina, momentuz guk "Alumnos" taularekin hasiko dugu. Gororatu laravelek ingeleseko nomenklatura erabiliko duela, beraz guk ere taulen izenak ingeleraz sortuko ditugu:

pupils, courses, teachers eta users.

Laravel-en jarraipenetan, taulen izenak beti pluralean idatziko dira.

![](../assets/images/Datu%20basea/b31d03fcf0611b3ad5a7fb5f976b6b03.jpg)

### Migrazioa

1. Behin hori eginda, migrazioa egingo dugu. Honi ezker, Laravelek guk adierazitako taulak sortuko ditu gure datu basean sql sententziak erabili barik. Hau guztia Artisani ezker oso errez egingo dugu.

Hau modeloarekin edo modelo barik egin daiteke. Erosoena modeloarekin egitea izango da, baina modu honetan, Laraveleko konbentzioaren arabera egingo da, hau da, taula modeloaren izena letra larriz eta singularrean eta aldiz taularen izena letra txikiz eta pluralean izango da, baina noski, ingelerazko plurala erabiliko du.

Gogoratu migrazioa egiterakoan beti dagokigun direktorioan egon behar dugula kokatuta.

- Migrazioa + modeloa:

```bash
php artisan make:Model Pupil -m
```

- Migrazioa bakarrik sortu nahi izango bagenu eran egingo dugu:

```bash
php artisan make:migration create_pupils_table
```

Aurreko migrazioan jarritako izena konbentzio bat da, modu honetan, migrazioa sortzerakoan, up() metodoaren barruan create metodoa sortuko du, eta honen barruan pupils taula.

```bash
php artisan make:migration pupils
```

Horrela bakarrik jartzekotan, ez lituzke metodo horiek sortuko.

![](../assets/images/Datu%20basea/0de43c3e50d9fac12c2ca5bfb60b8411.jpg)

Konprobatuko dugu zelan migrazioa ondo sortu duen, horretarako bagoaz: database > migrations > 2025\_11\_10\_060212\_create\_pupils\_table.php fitxategia zabaltzera.

Momentuz data basean ez da ezer sortu.

2. Gure php honetan sartuko gara eta taula zelakoa izango den adieraziko dugu. Ikusten dugunez, Laravelek, up metodoan kodea sortu du jada, kasu honetan ikasles taula bat sortuko luke bi eremurekin, id eta timestamps eramuak. Aldatuko dugu hori guk nahi dugun datuekin:

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

Orain bai, artisanekin, migrazioa exekutatuko dugu:

```bash
php artisan migrate
```

Ondo egin duen ala ez jakiteko, datu basera sartuko gara. Fijatu zeitzuk diren sortutako eremuak.

OHARRA: Ikusi dezakegunez, migrazioa sortzerakoan, Laravelek 3 zutabe sortuko ditu, lehenengoa id eta beste biak timestams-en bidez sortuko dituen beste bi, created\_at eta updated\_at. Bi zutabe hauek, datuak sortu eta aldatu egiten dituenean eguneratuko egiten ditu, datu horiek sartu edo aldatu diren data eta orduarekin.

Hori nahi ez izatekotan, migrazioan ezabatu beharko dugu, baina gogoratu ere, modeloa sortzerakoan eremu hori false jartzen:

```php
public $timestamps = false;
```

Ikusten dugunez, migrazioa sortzerakoan, gure Migration klaseak bi funtzio izango ditu, up() eta down(), lehenengoan taula sortuko da guk esandako eramuekin. Aldiz down() funtzioari deituko zaio migrazioa dezegin nahi izatekotan, hori dela eta funtzio honetan soilik taula ezabatuko da.

### Seeder

Seeder-ak Laraveleko konponente batzuk dira datu basean datuak sartu ahal izateko. Hauek normalean garapen prozesuan datuak sartzeko edo aplikazioren hasieran behar diren datuak sartzeko balio dute.

Zelan egingo genuke hau?

Gure direktorioan egonda, artisaneko sententzia hau idatziko genuke IkasleSeeder sortzeko:

```bash
php artisan make:seeder PupilSeeder
```

database > seeders-en sortuko du fitxategia.

Fitxategian, gure modeloa zein den adieraziko diogu, horretarako Ikasle klasearen erreferentzia sortuko dugu:

```php
use App\Models\Pupil;
```

$pupil aldagaia sortu, Ikasle klasea ezarriz eta bere propietate guztiak beteko ditugu irudian agertzen den moduan, ikusten duzuenez gure datu baseko taulak objeto moduan erabiliko ditugu.

![](../assets/images/Datu%20basea/37045aa61b6b7aa5ba214898b3cdea4b.jpg)

![](../assets/images/Datu%20basea/9abb0fc000f9bb1c46cf80827465225f.jpg)

Artisanek, Seederak exekutatzerakoan, beti DatabaseSeeder klasea deituko du; beraz, guk gure seederra exekutatzeko, klase horretako run() metodotik gure klasea "deituko" (instanciar) dugu.

Bat baino gehiago deitu daiteke metodo honetatik, eskuz idatzitako ordenean exekutatko litzateke.

```php
$this->call(PupilSeeder::class);
```

Behin hori guztia eginda, gure datu basean datuak kargatzeko, seeder-a exekutatuko dugu:

```bash
php artisan db:seed
```

### Factories

Dokumentazioa: <https://laravel.com/docs/12.x/seeding>

Seederrak aukera onak dira oso datu gutxi ditugunerako, baina badugu beste aukera bat, frogetarako datu asko behar ditugunerako, horretarako Factoriak ditugu, garapenerako pentsatutako klase batzuk dira hauek.

Factoriak nahiko errezak dira erabiltzeko, hurrengo pausuak jarraitu behar dira:

1. IkasleFactory deitutako klase bat sortuko dugu:

```bash
php artisan make:factory PupilFactory
```

2. Ikusten dugunez, database > factories karpetan fitxategi at sortu du PupilFactory.php
3. Definition funtzio barruan, taulako eremu bakoitzeko balio aleatorio bat emango diogu, hau Faker konponentari ezker egin dezakegu.
4. Faker, PHP libreria bat da, datu aleatorioak sortzen dituena, sortu ditzakegu, izenak, helbidek, telefonoan etab...

Faker sortu ahal dituen datu guztiak ikusteko [esteka honetan sartu](https://github.com/fzaninotto/Faker)

5. Gure adibidearekin jarraituz, guk horrelako erregistro bat sortuko genuke:

![](../assets/images/Datu%20basea/0a5678144436ee140eefad70a19d36d7.jpg)

6. Baina orain, DatubaseSeeder-en adierazi behar diogu lehenengo Seederra deitu beharrean Factorya exekutatzea. Kodean ikusi daitekenez, adierazi diezaiokugu zenbat biderretan egingo duen:

```php
use App\Models\Pupil;

public function run()    {

// $this->call(PupilSeeder::class);
```

Pupil::factory(10)->create();

}

7. Behin hori guztia eginda dugula gure modeloan ere esan behar dugu factory erabiliko dela eta zeintzuk izango diren bete ahal izango diren eremuak, hau da, zeintzuk izango diren filleable

```php
use HasFactory;

protected $fillable = ['name', 'age', 'phone', 'address'];
```

![](../assets/images/Datu%20basea/96c5b823146a03e885e9737fcb695443.jpg)

Behin hau eginda, seederra exekutatuko dugu berriro:

```bash
php artisan db:seed
```
