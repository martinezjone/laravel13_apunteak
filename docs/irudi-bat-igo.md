# Irudi bat igo

### Eremu berri bat sartzen (ikasles taulan)

Ikaslearen argazkia igotzeko orain arte egindako adibidea erabiliko dugu. Horretarako gure datu baseko pupils taula aldatuko dugu "photo" izeneko eremu berria gehituz.

Gogoratu hori egin ahal izateko migrazio berri bat sortu behar dugula. Oraingoan ere Laraveleko konbentzioak erabiliko ditugu: Taula sortzeko create\_xxx\_table erabiltzen genuen bezala, zutabe berriak gehitzeko, formatu hau erabiliko dugu:

php artisan make:migration add\_photo\_to\_pupils\_table

Ikusten dugunez, migrazioa prest dago dagokion eremua gehitzeko. up() metodoan gehituko dugu eta down() metodoan ezabatu.

$table->string('photo')->nullable()->after('address');

Honekin esaten diogu "photo" izeneko eremu berria sortzea, nullable() esaten diogu, null izan daitekelako (lehengo datuek ez dezaten akatsik eman) eta after('address'), address eremuaren ondoren gehitzeko timestamps eremuak baino lehen.

Down() metodoan, alderantzizko ekintza zehazten dugu; kasu horretan, ezabatu up() metodoan gehitu dugun zutabea:

$table->dropColumn('photo');

![](Irudi%20bat%20igo/e5469230605c1f9ba765a0cf3d64f7ff.jpg)

### File Storage

Laravel dokumentazioa: <https://laravel.com/docs/12.x/filesystem#main-content>

Flysystem integrazioak, ematen dio Laraveleri fitxategiekin lan egiteko kontroladore pilo. Storage-k emango digu aukera beraz fitxategiekin lan egiteko.

Konfigurazioa config/filesystems.php fitxategian egiten dira. Hemen topatuko ditugu diska desberdinak, honek balio digu hainbat sistema ezberdinekin lan egiteko.

Disko bakoitzak biltegiratze-kontrolatzaile bat eta biltegiratze-kokaleku partikular bat irudikatzen ditu.

Gure fitxategian, default moduan locale  diska erabiliko duela esaten du, baina guk hori public izatera aldatuko dugu:

'default' => env('FILESYSTEM\_DISK', 'public'),

Orain gauza izango litzateke jakitea non gorde behar ditugun gure irudiak. Hasiera baten guk edozein lekutan gorde ditzakegu, baina berez Laravelek badu prestatuta "storage" karpeta gure fitxategian gordetzeko, beraz hor gordeko ditugu guk ere.  Eta storage barruan app/public karpeta barruan.

Baina horregaz arazo bat sortzen zaigu, zeren eta guk dakigun bezala, nabigatzaileek public karpeta irakurri dezake soilik. Hori konpontzeko badago aukera bat, eta izango da gure public karpetan storage karpetaren "enlace simbólico" bat sortzea. Hori gure terminalaren bidez egin dezakegu.

php artisan storage:link

Horrela public/storage → storage/app/public lotuko du.

### Irudia igo (Bista eta kontroladorea) - Storage

Lehenengo eta behin "pupils.create" bistan fitxategia igotzeko aukera emango dugu:

Argazkia: <input type="file" name="photo" id="photo" accept="image/\*">

Gogoratu ere form etiketan beti jarri beharko dugula enctype="multipart/form-data"

Konturatu zarienez, hemen, accept-en esan diogu irudiak soilik igo ditzakeela, baina honegaz bakarrik ez da  nahiko izango, kontroladorean ere balidazioa egin beharko genuke. Gure kasuan PupilController.php-en egingo dugu:

$request->validate([

'name' => 'required|max:75',

'age' => 'required|integer',

'phone' => 'required|max:35',

'address' => 'required|max:75',

]);

Gauza biak idatzita, ziurtatuko dugu gure datu basean irudian bakarrik igoko dituztela.

Storage Facade erabili ahal izateko, gogoratu, gehitu behar dugula: use Illuminate\Support\Facades\Storage;

Bistan, gure irudia ikusteko url() metodoa ereabiliko dugu:

<img src="{{ url('storage/' . $pupil->photo)}}" alt="" width="80px" height="80px"/>

### Irudia igo (Kontroladorea) - move

public function store(StorePupil $request){

$new\_pupil = $request->validated();

// Irudia igoko dugu

if ($request->hasFile('photo')) {

$file = $request->file('photo');

$destinationPath = public\_path('storage/pupils');

$filename = $file->getClientOriginalName();

$file->move($destinationPath, $filename);

$new\_pupil['photo'] = 'pupils/' . $filename; // Bide erlatiboa gordetzen dugu

}

Pupil::create($new\_pupil);

return redirect()->route('pupils.index')->with('success', 'Ikaslea ondo sortu da.');

}
