# TxurdiGest · Paginazioa, balidazioa, hizkuntza eta irudiak

## 1. Paginazioa

`StudentController::index()` metodoan ordezkatu `Student::all()` kontsulta:

```php
$students = Student::query()->orderBy('name')->orderBy('id')->paginate(10);
```

`paginate(10)` metodoak orriko hamar ikasle berreskuratzen ditu. Izen bera duten ikasleak IDaren arabera ordenatzen dira. Gehitu ondokoa `resources/views/students/index.blade.php` fitxategian, zerrendaren azpian:

```blade
{{ $students->links('pagination.simple') }}
```

Ariketan CSS propioa erabili dugu. Sortu `resources/views/pagination/simple.blade.php` izeneko bista estekak euskaraz eta Tailwinden beharrik gabe erakusteko:

```blade
@if ($paginator->hasPages())
    <nav class="pagination" aria-label="Paginazioa">
        @if ($paginator->previousPageUrl())
            <a href="{{ $paginator->previousPageUrl() }}" rel="prev">Aurrekoa</a>
        @endif
        <span>{{ $paginator->currentPage() }} / {{ $paginator->lastPage() }}</span>
        @if ($paginator->nextPageUrl())
            <a href="{{ $paginator->nextPageUrl() }}" rel="next">Hurrengoa</a>
        @endif
    </nav>
@endif
```

Bista hau `paginate()` itzultzen duen orri kopurua dakien paginadorearekin erabiltzen da. Konprobatu bigarren orria seederrak sortutako ikasleekin. Bilaketa bat gehitzen baduzu, `->withQueryString()` erabil dezakezu iragazkia orri batetik bestera mantentzeko.

## 2. Balidazioa

TxurdiGestek hasieratik erabiltzen du `$request->validate()`. Hemen bere portaera aztertuko dugu. $request objektutik jasotako eremu guztien ordez, metodotik itzulitako eremu baliozkotuak bakarrik pasatzen zaizkie `create()` eta `update()` metodoei.

| Eremua | Zer egiaztatzen da |
| --- | --- |
| `name` | Derrigorrezko testua, 100 karaktere gehienez. |
| `email` | Helbide elektroniko balioduna eta dagokion taulan bakarra. Edizioan, uneko erregistroaren helbidea onartu behar da. |
| `code` | Modulu-kode derrigorrezkoa eta bakarra, gehienez 20 karakterekoa. |
| `teacher_id` | Existitzen den irakasle baten IDa. |
| `module_id.*` | Bidalitako ID bakoitza osoa eta bakarra da zerrendan, eta modulu bati dagokio. |

`Rule::unique('students', 'email')->ignore($student)` erabiltzean, salbuespena ibilbidetik kargatutako ikaslea da. Horrela, Jonen izena bakarrik aldatzeak ez du bikoiztasun-errorerik ematen. Migrazioetako `unique()` murriztapenek datu-basean ere bermatzen dute bakartasuna.

Balidazioak huts egiten badu, Laravelek formulariora itzultzen du erabiltzailea. `@error('email')` direktibak eremuaren mezua erakusten du, eta `old('email', $student->email ?? '')` deiak bidalitako balioa leheneratzen du. Balidatu gabeko hasierako edizioan, bigarren argumentuko balioa erakutsiko da.

Akats guztiak batera erakutsi nahi badituzu, jarri kode hau formularioaren hasieran. Eremuen azpiko mezuek errorea non gertatu den adierazten dute.

```blade
@if ($errors->any())
    <ul class="error" role="alert">
        @foreach ($errors->all() as $error)
            <li>{{ $error }}</li>
        @endforeach
    </ul>
@endif
```

Gelan, probatu helbide elektroniko bikoiztu batekin eta editatu ikasle bat posta aldatu gabe. Nabigatzaileko `required` edo `type="email"` atributuek balio bat bidaltzea eragozten badute, erabili aldi baterako `novalidate` formularioan zerbitzariko mezuak ikusteko.

## 3. Hizkuntza

### Balidazio-mezuak euskaraz

```bash
php artisan lang:publish
```

Ziurtatu `.env` fitxategian `APP_LOCALE=eu` eta `APP_FALLBACK_LOCALE=en` daudela. Sortu `lang/eu/validation.php`. Hona hemen ariketako arauetarako hiztegia, irudien ataleko arauak barne:

```php
<?php

return [
    'required' => ':attribute eremua bete behar da.',
    'string' => ':attribute eremuak testua izan behar du.',
    'email' => 'Idatzi baliozko helbide elektroniko bat.',
    'max' => [
        'string' => ':attribute eremuak gehienez :max karaktere izan ditzake.',
        'file' => 'Fitxategiak gehienez :max KB izan ditzake.',
    ],
    'between' => [
        'numeric' => ':attribute eremuak :min eta :max artekoa izan behar du.',
    ],
    'unique' => ':attribute hori dagoeneko erabilita dago.',
    'integer' => ':attribute eremuak zenbaki osoa izan behar du.',
    'exists' => 'Hautatutako :attribute ez da existitzen.',
    'array' => ':attribute eremuak zerrenda bat izan behar du.',
    'list' => ':attribute eremuak zerrenda ordenatua izan behar du.',
    'distinct' => ':attribute ezin da errepikatu.',
    'image' => 'Aukeratu irudi-fitxategi bat.',
    'mimes' => 'Aukeratu formatu hauetako fitxategi bat: :values.',
    'uploaded' => 'Ezin izan da fitxategia igo.',
    'attributes' => [
        'name' => 'izena',
        'email' => 'helbide elektronikoa',
        'code' => 'kodea',
        'teacher_id' => 'irakaslea',
        'module_id' => 'moduluen hautapena',
        'module_id.*' => 'modulua',
        'grade' => 'kalifikazioa',
        'photo_path' => 'argazkia',
    ],
];
```

Exekutatu `php artisan config:clear` eta probatu berriz posta bikoiztua. Mezuak euskaraz agertu behar du, nahiz eta zutabearen eta formulario-eremuaren izen teknikoa `email` izan.

### Interfazeko testuak itzulpen-fitxategira eraman

Interfazeko testu gisa zuzenean idatzitako «Gorde» hitza itzulpen-gako batekin ordezkatuko dugu. Gakoak ingelesez idatziko ditugu. Hasteko, gehitu `lang/eu/ui.php`:

```php
<?php

return [
    'home' => 'Hasiera',
    'students' => 'Ikasleak',
    'teachers' => 'Irakasleak',
    'modules' => 'Moduluak',
    'save' => 'Gorde',
    'cancel' => 'Utzi',
    'student_saved' => 'Ikaslea gorde da.',
];
```

`lang/es/ui.php` fitxategian, gako berak erabili:

```php
<?php

return [
    'home' => 'Inicio',
    'students' => 'Alumnado',
    'teachers' => 'Profesorado',
    'modules' => 'Módulos',
    'save' => 'Guardar',
    'cancel' => 'Cancelar',
    'student_saved' => 'Se ha guardado el registro del alumno.',
];
```

Ordezkatu, adibidez, botoia `<button type="submit">{{ __('ui.save') }}</button>` kodearekin. Ikaslea gordetzean, erabili `->with('status', __('ui.student_saved'))`. Probatu itzulpena Tinkerren, konfigurazio iraunkorra aldatu gabe:

```php
__('ui.save', [], 'eu');
__('ui.save', [], 'es');
```

Gelan, eraman nabigazioko testuak eta izenburuak dagozkien gakoetara. Gaztelaniazko fitxategia bigarren hizkuntza gehitzeko adibidea da. Ariketaren interfazearen lehenetsitako hizkuntza euskara da.

## 4. Aukerako hedapena · Ikaslearen argazkia

Gelan, erabili probako irudi asmatuak. Ariketa honetan `public` diskoa erabiliko dugu; disko horretako argazkiak URL zuzena duen edonork ikus ditzake.

```bash
php artisan make:migration add_photo_path_to_students_table --table=students
```

```php
public function up(): void
{
    Schema::table('students', function (Blueprint $table) {
        $table->string('photo_path')->nullable();
    });
}

public function down(): void
{
    Schema::table('students', function (Blueprint $table) {
        $table->dropColumn('photo_path');
    });
}
```

```bash
php artisan migrate
php artisan storage:link
php artisan make:controller StudentPhotoController --invokable
```

`routes/web.php` fitxategian gehitu inportazioa eta ibilbidea:

```php
use App\Http\Controllers\StudentPhotoController;

Route::post('/students/{student}/photo', StudentPhotoController::class)
    ->name('students.photo.store');
```

`app/Http/Controllers/StudentPhotoController.php`

```php
<?php

namespace App\Http\Controllers;

use App\Models\Student;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Storage;
use Illuminate\Validation\ValidationException;
use Throwable;

class StudentPhotoController extends Controller
{
    public function __invoke(Request $request, Student $student): RedirectResponse
    {
        $request->validate([
            'photo_path' => ['required', 'image', 'mimes:jpg,jpeg,png,webp', 'max:2048'],
        ]);

        $newPath = $request->file('photo_path')->store('students', 'public');

        if ($newPath === false) {
            throw ValidationException::withMessages([
                'photo_path' => 'Ezin izan da argazkia gorde.',
            ]);
        }

        try {
            $previousPath = DB::transaction(function () use ($student, $newPath) {
                $lockedStudent = Student::query()->lockForUpdate()->findOrFail($student->id);
                $previousPath = $lockedStudent->photo_path;
                $lockedStudent->photo_path = $newPath;
                $lockedStudent->save();

                return $previousPath;
            });
        } catch (Throwable $exception) {
            Storage::disk('public')->delete($newPath);

            throw $exception;
        }

        if ($previousPath !== null) {
            Storage::disk('public')->delete($previousPath);
        }

        return to_route('students.show', $student)->with('status', 'Argazkia gorde da.');
    }
}
```

Gehitu argazki-formularioa `students/edit.blade.php` fitxategian, ikaslearen datuen formularioa itxi eta gero:

```blade
<h2>Ikaslearen argazkia</h2>
<form method="POST" enctype="multipart/form-data" action="{{ route('students.photo.store', $student) }}">
    @csrf
    <label for="photo_path">Argazkia</label>
    <input type="file" id="photo_path" name="photo_path" accept="image/jpeg,image/png,image/webp" required>
    @error('photo_path')
        <p class="error">{{ $message }}</p>
    @enderror
    <button type="submit">Igo argazkia</button>
</form>
```

Gehitu irudia ikaslearen fitxan:

```blade
@if ($student->photo_path)
    <img src="{{ asset('storage/' . $student->photo_path) }}" alt="{{ $student->name }} · Argazkia" width="160">
@endif
```

Ikaslea ezabatzean argazkia ere kentzeko, gehitu `use Illuminate\Support\Facades\DB;` eta `use Illuminate\Support\Facades\Storage;` `StudentController` fitxategian eta ordezkatu `destroy()` metodoa:

```php
public function destroy(Student $student): RedirectResponse
{
    $photoPath = DB::transaction(function () use ($student) {
        $lockedStudent = Student::query()->lockForUpdate()->findOrFail($student->id);
        $photoPath = $lockedStudent->photo_path;
        $lockedStudent->delete();

        return $photoPath;
    });

    if ($photoPath !== null) {
        Storage::disk('public')->delete($photoPath);
    }

    return to_route('students.index')->with('status', 'Ikaslea ezabatu da.');
}
```

`photo_path` zutabean bidea gordetzen da; fitxategia diskoan gordetzen da. Probatu irudi bat igotzen, beste batekin ordezkatzen eta ikaslea ezabatzen. Erabili 2 MB baino gehiagoko irudi bat ere, balidazioa egiaztatzeko.

[Laravel 13 · Paginazioa](https://laravel.com/docs/13.x/pagination) · [Balidazioa](https://laravel.com/docs/13.x/validation) · [Hizkuntza](https://laravel.com/docs/13.x/localization) · [Fitxategiak](https://laravel.com/docs/13.x/filesystem)

[Aurrekoa · N:M erlazioa eta matrikulak](erlazioak-n-m.md)
