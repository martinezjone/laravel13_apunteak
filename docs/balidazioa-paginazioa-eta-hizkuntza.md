# ESKOLA ADIBIDEA

### 1. Paginazioa

Laravel bere 9. bertsioan paginazioa egiteko oso modu erraza sortu zuen. Oraingoan gure ikasleak jasotzerakoan all() metodoari deitu beharrean, paginate() metodoari deituko diogu

1. PupilController, index metodoan all() metodoa paginte() metodoarekin aldatuko dugu:

$pupils = Pupil::paginate();

Aurrekoarekin soilik esan diogu gure ikasleak taldeka pasatzea, berez 15-naka pasatuko ditu datu baseko erregistroak, gure kasuan ikasleenak.

Baina hori aldatu dezakegu, paginate metodoari parametroz zenbat erregistro jaso nahi ditugu esanez:

$pupils = Pupil::paginate(10);

Beste metodo bat erabili dezakeguna orderBy izango da, hau beste metodoekin eraili dezakegu, adibidez:

$pupils = Pupil::orderBy('id','desc')->paginate();

2. index.blade.php bistan paginazioaren linkak jarriko ditugu hurrengo direktiba erabiliz::

{{ $pupils ->links() }}

3. Eta horregaz jada, paginazioa eginda egongo litzateke

### 2. Balidazioa

Laravelek baditu hainbat funtzio datuen balidazioak egiteko lagungarri egingo zaizkigunak.

Ezer baino lehen, begiratuko dugu gure datu basean zeintzuk datu dira derrigorrezkoak, zeintzuk dira datu horien mota etab.

![](Balidazioa%20paginazioa%20eta%20hizkuntza/be04c2230966cf48a0e364ae9578d7fe.jpg)

1. Akatsak store() metodoaren barruan kontrolatuko ditugu. Formularioaren datu-guztiak jasotzen dituen Request objektua erabiltzen dugu store() metodoko parametro gisa. Ondoren, validate() metodoari ematen diogu array bat, non formularioaren eremuak eta horien balidazio-erregelak zehazten diren.

Laravelek adierazi egiten du, momentu baten balidazioa betetzen ez baldin bada, ez da hurrengo kode lerrora pasatuko eta akatsa emango digu. Aldiz, balidazio guztiak ondo daudenean aurrera jarraituko du.

Gure kasuan, store() metodoan idatziko dugu, gure datu guztiak balidatuko ditugu:

$validated = $request->validate([

'name' => 'required|string|max:75',

'age' => 'required|integer|min:0',

'phone' => 'required|string|max:20',

'address' => 'required|string|max:75',

]);

Pupil::create($validated);

Akatsak mezuetan adierazi

2. Orain, gure inprimakiak ikusiko ditugu. Hor inprimatuko ditugu mezuak formularioko eremuetan, gure kontrolatzaileetan definitzen ditugun baliozkotze-akatsak daudenean.

3. Inprimakiak baliozkotzen hasi aurretik, css kodea erantsiko diegu gure ikuspegi guztiei, errore-mezuei estiloa emateko.

p { color: red; }

Inprimakia ikasleen ikuspegian (bistan) baliozkotzea

4. create.blade.php bistan sortuko ditugu balidazio guztiak.

Input elementuaren azpiko Izen-deiturak eremuan, eremu honetako baliozkotze-kodea gehitzen dugu Bladek eskaintzen digun zuzentarauarekin. @error aukerak eremu honi dagokion errore-mezua inprimatzeko aukera ematen digu.

@error('name')

<p class="error-message">{{ $message }}</p>

@enderror

5. Gure input-ari gehituko diogu old() metodoa, blade-ko direktiba bat dena. Honegaz lortuko dugu balidazioa egin baino lehen sartutako datua mantentzea.

<input type="text" name="name" placeholder="Izena" value="{{ old('name') }}">

6. Gauza berdina adina elementaun egingo dugu:

<label>Adina:</label>

<input type="text" name="age" placeholder="Adina" value="{{ old('age') }}">

@error('age')

<p class="error-message">{{ $message }}</p><br>

@enderror

7. Sartutako telefonoa eta helbidea ere mantenduko ditugu:

<label>Telefonoa:</label>

<input type="text" name="phone" placeholder="Telefonoa" value="{{ old('phone') }}">

<label>Helbidea:</label>

<input type="text" name="address" placeholder="Helbidea" value="{{ old('address') }}">

8.  Zer gertatzen da, balidazioa editatzerakoan egiten baldin badugu? Kasu horretan, formularioara sartzen garen lehenengo aldian, datu baseko datua atera nahi dugu, eta akatsa egotekoran old metodoan bueltatzen diguna, hori egiteko, old metodoari bigarren parametro bat sartuko diogu:

<label>Telefonoa:</label>

<input type="text" name="phone" placeholder="Telefonoa" value="{{ old('phone', $pupil->phone) }}">

Ikusten dugunez, mezuak ingeleraz aterako dira, hori aldatu nahi izatekotan, hizkuntza aldatu beharko dugu.

9. Badugu beste modu bat akats guztiak hasieratik erakusteko:

@if ($errors->any())

Errors:

<ul>

@foreach ($errors->all() as $error)

<li>{{ $error }}</li>

@endforeach

</ul>

@endif

### 3. Hizkuntza

Gure formularioko errore-mezuak probatzean ikusiko dugu nola ingelesez agertzen diren. Hori aldatzeko, Laravel-en hizkuntza konfiguratu behar dugu.

1. Lang fitxategiak publikatu

Terminalean exekutatu:

php artisan lang:publish

Honek lang karpetan en karpeta sortuko du.

2. Hizkuntzak instalatu

Ez dugu eskuz kopiatu eta itzuli behar; errazagoa da Laravel-Lang erabiliz hizkuntzak gehitzea:

composer require laravel-lang/common --dev

Ondoren, nahi ditugun hizkuntzak gehitzen ditugu:

php artisan lang:add es    // Gaztelera

php artisan lang:add eu    // Euskara

Horrek lang/es eta lang/eu karpetak sortuko ditu guztiz itzulita.

3. Laravel-i zein hizkuntza erabili behar duen esatea

.env fitxategian honako hau aldatzen dugu:

APP\_LOCALE=eu

APP\_FALLBACK\_LOCALE=eu

Horren ostean aplikazioa euskaraz egongo da.

4. Formularioen eremu-izenak pertsonalizatzea

Baliozkotze-mezuetan Laravelrek automatikoki erabiltzen ditu formularioetako eremu-izenak (name, email, izen\_abizen …).

Eremu horiek modu “politean” erakutsi nahi baditugu,

lang/eu/validation.php fitxategira joan eta bukaeran dagoen attributes atal honetan idazten ditugu:

'attributes' => [

'izen\_abizen' => 'Izen Abizenak',

'email' => 'Posta Elektronikoa',

'password' => 'Pasahitza',

'name' => 'Izena',

];

Horrela, errore-mezuak itzulita eta formatu egokian agertuko dira.

Ez da ezer gehiago egin behar (ez JSON eguneratu, ez ezer berriro kargatu).

5. Fallback hizkuntza gomendatua

Gaztelania ere instalatuta badago, gomendagarria izan daiteke:

APP\_FALLBACK\_LOCALE=es

Horrela euskarazko testuren bat falta bada, gaztelania erabiliko da, eta ez ingelesa.

## Laburpena

php artisan lang:publish

composer require laravel-lang/common --dev

php artisan lang:add es, php artisan lang:add eu

.env → APP\_LOCALE=eu

validation.php → attributes atalean eremu-izenak pertsonalizatu
