# TxurdiGest · N:M erlazioa eta matrikulak

Ikasle batek hainbat modulu egin ditzake, eta modulu bakoitzean hainbat ikasle egon daitezke. Lotura bakoitza `module_student` tarteko taulako errenkada bat da.

## Tarteko taula eta Eloquent erlazioak

```bash
php artisan make:migration create_module_student_table
```

Migrazioaren metodoak:

```php
public function up(): void
{
    Schema::create('module_student', function (Blueprint $table) {
        $table->foreignId('module_id')->constrained()->cascadeOnDelete();
        $table->foreignId('student_id')->constrained()->cascadeOnDelete();
        $table->primary(['module_id', 'student_id']);
    });
}

public function down(): void
{
    Schema::dropIfExists('module_student');
}
```

Gako konposatuak matrikula bikoiztuak saihesten ditu. Ikasle edo modulu bat ezabatzean, bere matrikula-errenkadak ere ezabatuko dira. Beste aldeko erregistro nagusiak gordeko dira.

Exekutatu `php artisan migrate`. Gehitu `use Illuminate\Database\Eloquent\Relations\BelongsToMany;` bi modeloetan.

`Student` modeloan:

```php
public function modules(): BelongsToMany
{
    return $this->belongsToMany(Module::class);
}
```

`Module` modeloan:

```php
public function students(): BelongsToMany
{
    return $this->belongsToMany(Student::class);
}
```

Eloquentek `module_student` izena ondorioztatzen du bi modeloen izen singularrak ordena alfabetikoan elkartuta.

## Matrikulen seederra

```bash
php artisan make:seeder EnrollmentSeeder
```

`database/seeders/EnrollmentSeeder.php`

```php
<?php

namespace Database\Seeders;

use App\Models\Module;
use App\Models\Student;
use Illuminate\Database\Seeder;

class EnrollmentSeeder extends Seeder
{
    public function run(): void
    {
        $jon = Student::where('email', 'jon@example.test')->firstOrFail();
        $ane = Student::where('email', 'ane@example.test')->firstOrFail();
        $server = Module::where('code', 'DWS')->firstOrFail();
        $client = Module::where('code', 'DWC')->firstOrFail();

        $jon->modules()->syncWithoutDetaching([$server->id, $client->id]);
        $ane->modules()->syncWithoutDetaching([$client->id]);
    }
}
```

Gehitu `$this->call(EnrollmentSeeder::class);` `DatabaseSeeder::run()` metodoaren bukaeran, ikasleak eta moduluak sortu ondoren. Exekutatu `php artisan db:seed`. Seederra berriz exekutatzean probako matrikulak berriz agertuko dira, eskuz kendu badituzu.

## Ikaslearen eta moduluaren fitxak

`StudentController::show()` metodoan, `return` aurretik, gehitu `$student->load('modules');`. Gehitu ondokoa `students/show.blade.php` fitxategiko edukiaren barruan:

```blade
<h2>Matrikulatutako moduluak</h2>
<ul>
    @forelse ($student->modules as $module)
        <li><a href="{{ route('modules.show', $module) }}">{{ $module->name }}</a></li>
    @empty
        <li>Ikaslea ez dago modulurik egiten.</li>
    @endforelse
</ul>
```

`ModuleController::show()` metodoan ordezkatu `$module->load('teacher');` lerroa `$module->load(['teacher', 'students']);` lerroarekin. Gehitu moduluaren fitxan:

```blade
<h2>Matrikulatutako ikasleak</h2>
<ul>
    @forelse ($module->students as $student)
        <li><a href="{{ route('students.show', $student) }}">{{ $student->name }}</a></li>
    @empty
        <li>Ez dago matrikulatutako ikaslerik.</li>
    @endforelse
</ul>
```

## Matrikulak editatu

```bash
php artisan make:controller EnrollmentController
```

Gehitu inportazioa eta bi ibilbideak `routes/web.php` fitxategian:

```php
use App\Http\Controllers\EnrollmentController;

Route::get('/students/{student}/enrollments/edit', [EnrollmentController::class, 'edit'])
    ->name('students.enrollments.edit');

Route::put('/students/{student}/enrollments', [EnrollmentController::class, 'update'])
    ->name('students.enrollments.update');
```

`app/Http/Controllers/EnrollmentController.php`

```php
<?php

namespace App\Http\Controllers;

use App\Models\Module;
use App\Models\Student;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;
use Illuminate\View\View;

class EnrollmentController extends Controller
{
    public function edit(Student $student): View
    {
        $modules = Module::orderBy('name')->get();
        $enrolledModuleIds = $student->modules()->pluck('modules.id')->all();

        return view('enrollments.edit', compact('student', 'modules', 'enrolledModuleIds'));
    }

    public function update(Request $request, Student $student): RedirectResponse
    {
        $validated = $request->validate([
            'module_id' => ['sometimes', 'array', 'list'],
            'module_id.*' => ['required', 'integer', 'distinct', 'exists:modules,id'],
        ]);

        DB::transaction(function () use ($student, $validated) {
            $lockedStudent = Student::query()->lockForUpdate()->findOrFail($student->id);
            $lockedStudent->modules()->sync($validated['module_id'] ?? []);
        });

        return to_route('students.show', $student)->with('status', 'Matrikulak gorde dira.');
    }
}
```

`module_id[]` izenak moduluko IDen zerrenda bidaltzen du eta `module_id` zutabearen izena mantentzen du. `student_id` ibilbidetik dator, `Student $student` parametroaren bidez. Laukitxo guztiak desmarkatzean nabigatzaileak `module_id` bidali gabe uzten du. Kasu horretan, `sync([])` deiak ikaslearen matrikula guztiak kentzen ditu. Transakzioak aldaketak batera gordetzen ditu; ikaslearen blokeoak ikasle beraren aldibereko formulario-bidalketak ordenatzen ditu.

`resources/views/enrollments/edit.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Matrikulak editatu')

@section('content')
    <h1>{{ $student->name }} · Matrikulak editatu</h1>

    @php
        $selectedModuleIds = old('enrollment_form')
            ? collect(old('module_id', []))->filter(fn ($id) => is_scalar($id))->map(fn ($id) => (int) $id)->all()
            : $enrolledModuleIds;
    @endphp

    <form method="POST" action="{{ route('students.enrollments.update', $student) }}">
        @csrf
        @method('PUT')
        <input type="hidden" name="enrollment_form" value="1">

        <fieldset>
            <legend>Moduluak</legend>
            @forelse ($modules as $module)
                <label for="module_id_{{ $module->id }}">
                    <input type="checkbox" id="module_id_{{ $module->id }}" name="module_id[]"
                        value="{{ $module->id }}" @checked(in_array($module->id, $selectedModuleIds))>
                    {{ $module->code }} · {{ $module->name }}
                </label>
            @empty
                <p>Ez dago modulurik. Sortu modulu bat lehenik.</p>
            @endforelse
        </fieldset>

        @foreach ($errors->all() as $error)
            <p class="error">{{ $error }}</p>
        @endforeach

        <button type="submit">Gorde</button>
        <a href="{{ route('students.show', $student) }}">Utzi</a>
    </form>
@endsection
```

`enrollment_form` ez da datu-baseko eremu bat. Aurretik formularioa bidali den jakiteko marka da, balidazio-errore baten ondoren hautapena leheneratzeko.

Gehitu esteka ikaslearen fitxan:

```blade
<a href="{{ route('students.enrollments.edit', $student) }}">Matrikulak editatu</a>
```

## attach(), detach() eta sync()

| Metodoa | Eragina |
| --- | --- |
| `attach($moduleId)` | Matrikula bat gehitu. Lotura dagoeneko existitzen bada, gako konposatuak bikoizketa eragozten du. |
| `detach($moduleId)` | Adierazitako lotura kendu. |
| `sync([$firstId, $secondId])` | Zerrendako loturak mantendu edo sortu, eta gainerakoak kendu. |
| `syncWithoutDetaching([$moduleId])` | Lotura erantsi falta bada, aurreko matrikulak gordez. |

Probatu Joni modulu bat kentzen, berriz eransten eta biak kentzen. Begiratu aldaketa moduluaren fitxan ere.

## Aukerako hedapena · Kalifikazioa eta matrikula-data

```bash
php artisan make:migration add_enrollment_details_to_module_student_table --table=module_student
```

```php
public function up(): void
{
    Schema::table('module_student', function (Blueprint $table) {
        $table->date('enrolled_at')->nullable();
        $table->unsignedTinyInteger('grade')->nullable();
    });
}

public function down(): void
{
    Schema::table('module_student', function (Blueprint $table) {
        $table->dropColumn(['enrolled_at', 'grade']);
    });
}
```

Exekutatu `php artisan migrate`. Gehitu `->withPivot('enrolled_at', 'grade')` bi `belongsToMany()` deien bukaeran. Lehendik dauden matrikulek `null` izango dute bi eremuetan.

Tinkerren, erabili dagoeneko matrikulatuta dagoen ikasle bat eta bere modulu baten IDa. `updateExistingPivot()` metodoak lotura horren datuak eguneratzen ditu:

```php
$student = App\Models\Student::where('email', 'jon@example.test')->firstOrFail();
$module = $student->modules()->firstOrFail();

$student->modules()->updateExistingPivot($module->id, [
    'enrolled_at' => now()->toDateString(),
    'grade' => 8,
]);
```

Ikaslearen fitxako moduluen begiztan erabil daiteke `{{ $module->pivot->grade ?? 'Kalifikatu gabe' }}`. Nota 0 izan daiteke; horregatik erabiltzen dugu `??`. Kalifikazio-formularioa gehituz gero, `name="grade"` erabili eta balidatu `nullable|integer|between:0,10`. Dagoeneko matrikulatuta dagoen modulua mantentzen bada, matrikula-formularioaren `sync()` deiak nota gordetzen du. Matrikula kentzean, haren nota ere ezabatzen da.

[Laravel 13 · N:M erlazioak](https://laravel.com/docs/13.x/eloquent-relationships#many-to-many)

[Aurrekoa · 1:N erlazioa](erlazioak-1-n.md) | [Hurrengoa · Paginazioa, balidazioa, hizkuntza eta irudiak](balidazioa-paginazioa-eta-hizkuntza.md)
