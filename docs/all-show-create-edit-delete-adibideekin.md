# ESKOLA ADIBIDEA

### Ikasle erregistro guztiak erakutsi

Gure ikasleen zerrenda bistaratzeko hurrengo pausuak eman beharko ditugu:

- Ruta sortu
- PupilController kontroladorea sortu
- Kontroladorean beharrezkoa den metodoa sortu eta datu basean dauden ikasle guztiak bilatu all() metodoarekin
- Bista sortu aurkitutako ikasleak bistaratzeko

1. Ruta edo ibilbidea honako hau izango zen:

//Ikasle guztiak ikusteko

Route::get('/pupils', [PupilController::class, 'index'])->name('pupils.index');

2. PupilController sortu
3. PupilController-en hasiko dugu beharrezko metodoak sortzen. Momentuz ikasle guztian ateratzeko index() metodoa sortuko dugu:

public function index(){

$pupils= Pupil::all();

return view('pupils.index',compact('pupils'));

}

4. Oraingoan bista sortu beharko genuke. Foreach batekin bilatuko dugu $pupils aldagaian dauden datu guztiak.

Laravelen, badaude beste konbentzio pare bat bistak sortzeko, alde batetik bistara deitzen duen metodoaren izen berdina jarri ohi zaio fitxategiari eta bestetik kontroladore batek deitzen dituen bista guztiak azpikarpetan sortzen dira, kontroladore horrek duen izen berdinarekin, kasu honetan "pupils" karpeta izango da.

@foreach($pupils as $pupil)

<label><strong>Izen abizenak:</strong> {{ $pupil->name}}</label><br>

<label><strong>Adina:</strong> {{ $pupil->age}}</label><br>

<label><strong>Telefonoa:</strong> {{ $pupil->phone}}</label><br>

<label><strong>Helbidea:</strong> {{ $pupil->address}}</label>

@endforeach

5. Kasu honetan gure erregistro guztiak erakutsi ditugu, horretarako all() metodoa erabiliz. Baina baditugu hainbat metodo gure kontsultetan erabili ahal izateko. Query-ak zelan sortu ditzakegu Laravel dokumentazioa ikusi dezakezue, [Query builder](https://laravel.com/docs/12.x/queries).
6. Adibide batzuk:

// Izenetik ordenatu

$pupils = Pupil::orderBy('name')->get();

// Ikasle guztiak hartzen ditu baina bakarrik izena eta adina

$pupils = Pupil::select('name', 'age')->get();

// Where bat erabiliz

$pupils = Pupil::where('address', 'Bilbao')->get();

// Where konbinatuak: adina > 18 edo helbidea = 'Bilbao'

$pupils = Pupil::where('age', '>', 18)

->orWhere('address', '=', 'Bilbao')

->get();

// Name like '%Jon%'

$pupils = Pupil::where('name', 'like', '%Jon%')->get();

// Adina > 20 guztiak

$pupils = Pupil::where('age', '>', 15)->get();

// Adina > 20, lehenengoa bakarrik

$pupil = Pupil::where('age', '>', 20)->first();

// Zenbat erregistro dauden

$pupilCount = Pupil::count();

// Lehenengo 10 ikasleak

$pupils = Pupil::limit(10)->get();

// Adin maximoa

$maxAge = Pupil::max('age');

// Adin minimoa

$minAge = Pupil::min('age');

// Adin batazbestekoa

$avgAge = Pupil::avg('age');

// Adinen batura

$totalAge = Pupil::sum('age');

### Erregistro bat ikustaraztea

- Ruta sortu
- Kontroladorean beharrezkoa den metodoa sortu eta datu basean ikaslea atera find() metodoarekin
- Bista sortu aurkitutako ikasleak bistaratzeko
- Aurreko bistan, ikasle bakoitzari gehitu ikasle zehatza ikusteko esteka

Erregistro bakar bat erekusteko, show() metodoa erabiliko dugu:

1. IkasleController kontrolatzailea irekiz hasiko gara. Show() metodoaren barruan, $alumno motako objektu bat lortuko duen aldagai bat abiaraziko dugu. find() metodoari $alumno parametro bat emango diogu erregistro jakin bat berreskuratzeko, eta, azkenik, ikaslea datuak berreskuratuta ikusiko dugu.

public function show(Pupil $pupil){

return view('pupils.show', compact('pupil'));

}

2. Ibilbidea honako hau izango zen:

Route::get('pupils/{pupil}', [PupilController::class, 'show'])->name('pupils.show');

3. Bista: show.blade.php

<label><strong>Izen abizenak:</strong> {{ $pupil->name}}</label><br>

<label><strong>Adina:</strong> {{ $pupil->age}}</label><br>

<label><strong>Telefonoa:</strong> {{ $pupil->phone}}</label><br>

<label><strong>Helbidea:</strong> {{ $pupil->address}}</label>

4. Aurreko bistan esteka gehitu:

<a href="{{ route('pupils.show', $pupil->id) }}">{{ $pupil->name }}</a>

### Ikasle berri bat sortu: Create eta store metodoak

- Ruta sortu
- Kontroladorean beharrezkoa diren metodoak sortu eta datu basean ikaslea sortu: create() eta store() metodoak erabiliz.
- Bista sortu ikaslea sortzeko formularioarekin
- Menu bat sortu index eta ikaslea sortu aukerekin, horretarako layouts erabiliko ditugu

Oraingo honetan, ikasle berri bat sortuko dugu gure datu basean, horretarako metodo bi erabiltzen dira, create() eta store(); lehenengoan formulario sortuko dugu eta bigarrenean datuak gure datu basean sartuko ditugu.

1. Lehenengo gauza beraz bista berri bat sortuko dugu, "create" izenekoa eta horren ruta sortuko dugu jarraian.
2. Bista, "alumnos" karpetan sortuko dugu "create.blade.php" izenarekin
3. Hurrengo hau izango da formulario izango duen bistaren kodea:

<form action="{{ route('pupils.store') }}" method="POST">

@csrf

Izen Abizena <input type="text" name="izena" value=""><br>

Adina <input type="text" name="adina" value=""><br>

Telefonoa <input type="text" name="telefonoa" value=""><br>

Helbidea<input type="text" name="helbidea"  value=""><br>

<button type="submit">Bidali</button>

</form>

4. Fijatu, zelan formularioaren barruan, Laravelek  @csrf tokena sartzea eskatzen digu, hau segurtasunagatik egiten da, atakeak sahiesteko erabiltzen da. Horrelako token bat sortuko dugu formulario bakoitzeko. Laravelek derrigorrez eskatzen digu token hau izatea
5. Rutak. Bi beharko ditugu, get bat formularioari deitzeko eta beste bat, post metodoarekin formularioaren datuak DB-an gordetzeko:

Route::get('pupils/create',[PupilController::class,'create'])->name('pupils.create');

Route::post('pupils/store',[PupilController::class,'store'])->name('pupils.store');

6. IkasleController:

Create() metodoa ikasle berri bat sortzeko behar dugun formularioa erakusteko:

public function create(){

return view('pupils.create');

}

Store() metodoa formularioak bidalitako informazioa datu basean gordetzeko:

public function store(Request $request){

Pupil::create([

'name' => $request->name,

'age' => $request->age,

'phone' => $request->phone,

'address' => $request->address,

]);

return redirect()->route('pupils.all');

}

7. Index bistan ikasle berri bat sortzeko esteka jarriko dugu ere:

<a href="{{route('pupils.create')}}">Ikasle berria</a>

### Ikasle baten datuak aldatu: Edit eta update metodoak

- Ruta sortu
- Kontroladorean beharrezkoa diren metodoak sortu eta datu basean ikaslea sortu: edit() eta update() metodoak erabiliz.
- Bista sortu ikaslea sortzeko formularioarekin
- Ikaslea aldatzeko esteka jarri, index eta show aukeretan

1. Lehenengo edit() funtzioan aldatu nahi dugun ikaslea bilatuko dugu, horretarako find($id) metodoa erabiliko dugu. Datuak edit bistan kargatuko ditugu. Formulario honek update funtzioari deituko dio, datuak DBan aldatuko dituena.

PupilController

public function edit(Pupil $pupil){

return view('pupils.edit', compact('pupil'));

}

Ikusi dezakezuenez, orain, aldagai arrunta jaso beharrean, Alumno jarri dugu aurretik, horrela jartzerakoan Laravelek ulertzen du $alumno Alumno klasearen instantzia bat dela.

2. Metodo honi deitzen dion ruta beharko dugu:

Route::get('pupils/edit/{pupil}',[PupilController::class,'edit'])->name('pupils.edit');

3. Ikaslearen datuak aldatzeko beharezkoa den formularioa bsita baten sortuko dugu ere, baina formulario honetan gauza pare bat izango ditugu, alde batetik, oraingoan valuen ikasle horrek dituen datuak erakutsiko ditugu, eta bestetik metodoa aldatu behar izango dugu, Laravelek updateak egiterakoan rutak POST metodo izan beharrean nahiago du PUT metodoa izatea, eta hori horrela izatean formularioan adierazi behar diogu ere:

<form action="{{ route('pupils.update', $pupil->id) }}" method="POST">

@csrf

@method('put')

<input type="text" id="izena" name="izena" value="{{$pupil->name}}">

<input type="number" id="adina" name="adina" value="{{$pupil->age}}">

<input type="tel" id="telefonoa" name="telefonoa" value="{{$pupil->phone}}">

<textarea id="helbidea" name="helbidea" required>{{$pupil->address}}</textarea>

<button type="submit">Gorde</button>

</form>

4. Updatea egiteko ruta horrelako izango litzateke, put metodoa erabiliz:

Route::put('pupils/update/{pupil}',[PupilController::class,'update'])->name('pupils.update');

5. Kontroladorean, prestatuko dugu ere update funtzioa, honek, DBa bidalitako datuekin aldatuko du, hau da edit bistak hurrengo funtzio honi deituko dio, kasu honetan metodoak alde batetik formularioak bidalitakoa jasoko du, eta bestetik aldatu behar dugun objektua ere.

public function update(Request $request, Pupil $pupil) {

$pupil->update([

'name' => $request->name,

'age' => $request->age,

'phone' => $request->phone,

'address' => $request->address,

]);

return redirect()->route('pupils.all');

}

### Ikasle bat ezabatu: destroy() metodoa

- Ruta sortu
- Kontroladorean beharrezkoa den metodoa sortu, destroy() metodoa izago da hau
- Ikaslea ezabatzeko esteka jarri, index eta show aukeretan

1. Ruta sortu:

Route::delete('pupils/{pupil}',[PupilController::class,'destroy'])->name('pupils.destroy');

Ikusten dugunez, ruta hau delete metodoari deitzen dio, hau da ezabatzeko Laravelek erabiltzen duen metodoa

2. IkasleController barruan sortuko dugu erregistro bat ezabatzeko metodoa,  destroy() metodoa izango da hau, honen barruan delete() metodoari deituko diogu.

public function destroy(Pupil $pupil){

$pupil->delete();

return redirect()->route('pupils.all');

}

3. Ikaslea ezabatzeko esteka. Ruta egiterakoan ikusi dugunez, Laravelek delete metodoa erabiltze du datu baseko erregistro bat ezabatzeko, beraz, ruta hori deitzerakoan, html-an, metodo hori erabiliko dugula adierazi beharko dugu, eta hori formulario baten barruan soilik egin dezakegu, beraz, destroy ruta bati beti formulario baten bidez egin behar dugu.

<form action="{{ route('pupils.destroy', $pupil->id) }}" method="POST" class="inline-block">

@csrf

@method('DELETE')

<button type="submit">

Delete

</button>

</form>

4. Ikusten dugunez, honek zuzenean ezabatzen du erregistroa.

### Ikasle bat ezabatu - Ezabatzeko konfirmazioa eskatu

Baina, normalean badakigu zeozer ezabatu baino lehen aukera eman behar dugula ziurtatzeko erregistro hori ezabatu nahi dugula, zelan egingo genuke hori ordun?

- Ruta berria sortu
- Kontroladorean konfirmazio bistari deitu
- Konfirmazio bista sortu

1. Ruta sortu:

Route::get('pupils/delete/{pupil}',[PupilController::class,'delete'])->name('pupils.delete');

2. PupilController barruan sortuko dugu erregistro bat konfirmatzeko metodoa.

public function delete(Pupil $pupil){

return view('pupils.delete', compact('pupil'));

}

3. confirm.blade.php - Bista bat sortuko dugu formulario batekin, honek ezabatzeko botoia izango du eta Cancel esteka ere.

Ziur zaude ezabatu nahi duzula?

<form action="{{ route('pupils.destroy', $pupil->id) }}" method="POST" class="inline-block">

@csrf

@method('DELETE')

<button type="submit">

Delete

</button>

</form>

### Resource rutak

Konbentzio guztiak jarraituz gero, ruta guzti hauek laburtu ditzakegu ruta bakar bategaz:

Route::resource('pupils',PupilController::class);

Proiektuak sortuta dituen ruta guztiak ikusi ahal izateko, artisan-en bidez zerrendatu ditzakegu:

php artisan route:list

Resource bat baino gehiago sortu nahi badugu, gure resource rutari array baten pasatu dizkiokegu guk nahi dugun beste elementu.

Route::resources([

'photos' => PhotoController::class,

'posts' => PostController::class,

]);

Honen inguruko dokumentazio ofiziala: <https://laravel.com/docs/12.x/controllers#resource-controllers>
