# TxurdiGest · 1:N erlazioa

Irakasle batek hainbat modulu izan ditzake. Modulu bakoitzak irakasle bat behar du. Horregatik, `teacher_id` kanpo-gakoa `modules` taulan jarriko dugu.

## Migrazioak eta modeloak

Sortu eta exekutatu irakasleen migrazioa lehenengo. Ondoren egin gauza bera moduluenekin, kanpo-gakoak jadanik existitzen den taula bat behar duelako.

```bash
php artisan make:model Teacher -mf
php artisan make:controller TeacherController --model=Teacher --resource
```

Irakasleen migrazioaren `up()` eta `down()` metodoak:

```php
public function up(): void
{
    Schema::create('teachers', function (Blueprint $table) {
        $table->id();
        $table->string('name', 100);
        $table->string('email')->unique();
        $table->string('department', 50);
        $table->timestamps();
    });
}

public function down(): void
{
    Schema::dropIfExists('teachers');
}
```

```bash
php artisan migrate
php artisan make:model Module -mf
php artisan make:controller ModuleController --model=Module --resource
```

Moduluen migrazioaren `up()` eta `down()` metodoak:

```php
public function up(): void
{
    Schema::create('modules', function (Blueprint $table) {
        $table->id();
        $table->string('name', 100);
        $table->string('code', 20)->unique();
        $table->foreignId('teacher_id')->constrained()->restrictOnDelete();
        $table->timestamps();
    });
}

public function down(): void
{
    Schema::dropIfExists('modules');
}
```

`restrictOnDelete()` aukerak irakaslea babesten du moduluak esleituta dituen bitartean. Irakaslea ezabatu aurretik, esleitu moduluak beste irakasle bati moduluen edizio-pantailan.

```bash
php artisan migrate
```

`app/Models/Teacher.php`

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;

class Teacher extends Model
{
    use HasFactory;

    protected $fillable = ['name', 'email'];

    public function modules(): HasMany
    {
        return $this->hasMany(Module::class);
    }
}
```

`app/Models/Module.php`

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Module extends Model
{
    use HasFactory;

    protected $fillable = ['name', 'code', 'teacher_id'];

    public function teacher(): BelongsTo
    {
        return $this->belongsTo(Teacher::class);
    }
}
```

## Factoryak eta hasierako datuak

`TeacherFactory` fitxategian gehitu `use Illuminate\Support\Str;` eta jarri metodo hau:

```php
public function definition(): array
{
    return [
        'name' => fake()->name(),
        'email' => Str::uuid() . '@example.test',
    ];
}
```

`ModuleFactory` fitxategian gehitu `use App\Models\Teacher;` eta `use Illuminate\Support\Str;`:

```php
public function definition(): array
{
    return [
        'name' => fake()->words(3, true),
        'code' => Str::upper(Str::random(12)),
        'teacher_id' => Teacher::factory(),
    ];
}
```

`Module::factory()->create()` deiak irakasle bat ere sortzen du. Irakasle bera partekatzeko, probatu `Module::factory()->count(2)->for($teacher)->create()`.

```bash
php artisan make:seeder ModuleSeeder
```

`database/seeders/ModuleSeeder.php`

```php
<?php

namespace Database\Seeders;

use App\Models\Module;
use App\Models\Teacher;
use Illuminate\Database\Seeder;

class ModuleSeeder extends Seeder
{
    public function run(): void
    {
        $teacher = Teacher::firstOrCreate(
            ['email' => 'maialen@example.test'],
            ['name' => 'Maialen Agirre'],
        );

        Module::firstOrCreate(['code' => 'DWS'], [
            'name' => 'Web-garapena zerbitzari-ingurunean',
            'teacher_id' => $teacher->id,
        ]);

        Module::firstOrCreate(['code' => 'DWC'], [
            'name' => 'Web-garapena bezero-ingurunean',
            'teacher_id' => $teacher->id,
        ]);
    }
}
```

`DatabaseSeeder::run()` metodoaren bukaeran gehitu `$this->call(ModuleSeeder::class);` eta exekutatu `php artisan db:seed`.

Tinkerren, probatu `App\Models\Module::where('code', 'DWS')->firstOrFail()->teacher` eta `App\Models\Teacher::where('email', 'maialen@example.test')->firstOrFail()->modules`. `$teacher->modules()` erabiliz kontsultari baldintzak gehi diezazkiokezu; `$teacher->modules` erabiliz emaitzen bilduma eskuratzen duzu.

## Ibilbideak eta irakasleen CRUDa

Gehitu inportazio hauek `routes/web.php` fitxategian eta erregistratu bi baliabideak:

```php
use App\Http\Controllers\ModuleController;
use App\Http\Controllers\TeacherController;

Route::resources([
    'teachers' => TeacherController::class,
    'modules' => ModuleController::class,
]);

Route::get('/teachers/{teacher}/delete', [TeacherController::class, 'delete'])->name('teachers.delete');
Route::get('/modules/{module}/delete', [ModuleController::class, 'delete'])->name('modules.delete');
```

Irakasleen CRUDak ikasleen egitura bera du. Moduluen zerrendan `with('teacher')` erabiliko dugu: zerrendako moduluen irakasleak batera kargatzen ditu, lerro bakoitzean kontsulta bat egin beharrean.

`app/Http/Controllers/TeacherController.php`

```php
<?php

namespace App\Http\Controllers;

use App\Models\Teacher;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\Validation\Rule;
use Illuminate\View\View;

class TeacherController extends Controller
{
    public function index(): View
    {
        $teachers = Teacher::all();

        return view('teachers.index', compact('teachers'));
    }

    public function show(Teacher $teacher): View
    {
        return view('teachers.show', compact('teacher'));
    }

    public function create(): View
    {
        return view('teachers.create');
    }

    public function store(Request $request): RedirectResponse
    {
        $teacher = Teacher::create($request->validate($this->rules()));

        return to_route('teachers.show', $teacher)->with('status', 'Irakaslea gorde da.');
    }

    public function edit(Teacher $teacher): View
    {
        return view('teachers.edit', compact('teacher'));
    }

    public function update(Request $request, Teacher $teacher): RedirectResponse
    {
        $teacher->update($request->validate($this->rules($teacher)));

        return to_route('teachers.show', $teacher)->with('status', 'Irakaslea gorde da.');
    }

    public function delete(Teacher $teacher): View
    {
        return view('teachers.delete', compact('teacher'));
    }

    public function destroy(Teacher $teacher): RedirectResponse
    {
        if ($teacher->modules()->exists()) {
            return to_route('teachers.show', $teacher)
                ->with('status', 'Esleitu moduluak beste irakasle bati lehenik.');
        }

        $teacher->delete();

        return to_route('teachers.index')->with('status', 'Irakaslea ezabatu da.');
    }

    private function rules(?Teacher $teacher = null): array
    {
        $unique = Rule::unique('teachers', 'email');
        if ($teacher !== null) {
            $unique->ignore($teacher);
        }

        return [
            'name' => ['required', 'string', 'max:100'],
            'email' => ['required', 'email', 'max:255', $unique],
        ];
    }
}
```

`resources/views/teachers/index.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Irakasleak')

@section('content')
    <h1>Irakasleak</h1>
    <a href="{{ route('teachers.create') }}">Irakasle berria</a>
    <ul>
        @forelse ($teachers as $teacher)
            <li><a href="{{ route('teachers.show', $teacher) }}">{{ $teacher->name }}</a></li>
        @empty
            <li>Ez dago erregistrorik.</li>
        @endforelse
    </ul>
@endsection
```

`resources/views/teachers/show.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Irakaslea · Fitxa')

@section('content')
    <h1>{{ $teacher->name }}</h1>
    <p>Helbide elektronikoa: {{ $teacher->email }}</p>
    <a href="{{ route('teachers.edit', $teacher) }}">Editatu</a>
    <a href="{{ route('teachers.delete', $teacher) }}">Ezabatu</a>
    <a href="{{ route('teachers.index') }}">Itzuli zerrendara</a>
@endsection
```

`resources/views/teachers/create.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Irakasle berria')

@section('content')
    <h1>Irakasle berria</h1>
    <form method="POST" action="{{ route('teachers.store') }}">
        @csrf
        @include('teachers._form')
        <button type="submit">Gorde</button>
    </form>
@endsection
```

`resources/views/teachers/edit.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Irakaslea editatu')

@section('content')
    <h1>Irakaslea editatu</h1>
    <form method="POST" action="{{ route('teachers.update', $teacher) }}">
        @csrf
        @method('PUT')
        @include('teachers._form')
        <button type="submit">Gorde</button>
    </form>
@endsection
```

`resources/views/teachers/_form.blade.php`

```blade
<div>
    <label for="name">Izen-abizenak</label>
    <input id="name" name="name" value="{{ old('name', $teacher->name ?? '') }}" required maxlength="100">
    @error('name')
        <p class="error">{{ $message }}</p>
    @enderror
</div>
<div>
    <label for="email">Helbide elektronikoa</label>
    <input type="email" id="email" name="email" value="{{ old('email', $teacher->email ?? '') }}" required maxlength="255">
    @error('email')
        <p class="error">{{ $message }}</p>
    @enderror
</div>
```

`resources/views/teachers/delete.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Ezabatu')

@section('content')
    <h1>Ezabatu</h1>
    <p>{{ $teacher->name }} ezabatu nahi duzu?</p>
    <form method="POST" action="{{ route('teachers.destroy', $teacher) }}">
        @csrf
        @method('DELETE')
        <button type="submit">Bai, ezabatu</button>
        <a href="{{ route('teachers.show', $teacher) }}">Utzi</a>
    </form>
@endsection
```

## Moduluen CRUDa

Sortu ondoko kontroladorea eta bistak. Gehitu irakasle bat modulu bat sortzen saiatu aurretik.

`app/Http/Controllers/ModuleController.php`

```php
<?php

namespace App\Http\Controllers;

use App\Models\Module;
use App\Models\Teacher;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\Validation\Rule;
use Illuminate\View\View;

class ModuleController extends Controller
{
    public function index(): View
    {
        $modules = Module::with('teacher')->orderBy('name')->get();

        return view('modules.index', compact('modules'));
    }

    public function show(Module $module): View
    {
        $module->load('teacher');

        return view('modules.show', compact('module'));
    }

    public function create(): View
    {
        $teachers = Teacher::orderBy('name')->get();
        return view('modules.create', compact('teachers'));
    }

    public function store(Request $request): RedirectResponse
    {
        $module = Module::create($request->validate($this->rules()));

        return to_route('modules.show', $module)->with('status', 'Modulua gorde da.');
    }

    public function edit(Module $module): View
    {
        $teachers = Teacher::orderBy('name')->get();
        return view('modules.edit', compact('module', 'teachers'));
    }

    public function update(Request $request, Module $module): RedirectResponse
    {
        $module->update($request->validate($this->rules($module)));

        return to_route('modules.show', $module)->with('status', 'Modulua gorde da.');
    }

    public function delete(Module $module): View
    {
        return view('modules.delete', compact('module'));
    }

    public function destroy(Module $module): RedirectResponse
    {
        $module->delete();

        return to_route('modules.index')->with('status', 'Modulua ezabatu da.');
    }

    private function rules(?Module $module = null): array
    {
        $unique = Rule::unique('modules', 'code');
        if ($module !== null) {
            $unique->ignore($module);
        }

        return [
            'name' => ['required', 'string', 'max:100'],
            'code' => ['required', 'string', 'max:20', $unique],
            'teacher_id' => ['required', 'integer', 'exists:teachers,id'],
        ];
    }
}
```

`resources/views/modules/index.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Moduluak')

@section('content')
    <h1>Moduluak</h1>
    <a href="{{ route('modules.create') }}">Modulu berria</a>
    <ul>
        @forelse ($modules as $module)
            <li><a href="{{ route('modules.show', $module) }}">{{ $module->name }}</a> · {{ $module->teacher->name }}</li>
        @empty
            <li>Ez dago erregistrorik.</li>
        @endforelse
    </ul>
@endsection
```

`resources/views/modules/show.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Modulua · Fitxa')

@section('content')
    <h1>{{ $module->name }}</h1>
    <p>Kodea: {{ $module->code }}</p>
    <p>Irakaslea: {{ $module->teacher->name }}</p>
    <a href="{{ route('modules.edit', $module) }}">Editatu</a>
    <a href="{{ route('modules.delete', $module) }}">Ezabatu</a>
    <a href="{{ route('modules.index') }}">Itzuli zerrendara</a>
@endsection
```

`resources/views/modules/create.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Modulu berria')

@section('content')
    <h1>Modulu berria</h1>
    <form method="POST" action="{{ route('modules.store') }}">
        @csrf
        @include('modules._form')
        <button type="submit">Gorde</button>
    </form>
@endsection
```

`resources/views/modules/edit.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Modulua editatu')

@section('content')
    <h1>Modulua editatu</h1>
    <form method="POST" action="{{ route('modules.update', $module) }}">
        @csrf
        @method('PUT')
        @include('modules._form')
        <button type="submit">Gorde</button>
    </form>
@endsection
```

`resources/views/modules/_form.blade.php`

```blade
<div>
    <label for="name">Moduluaren izena</label>
    <input id="name" name="name" value="{{ old('name', $module->name ?? '') }}" required maxlength="100">
    @error('name')
        <p class="error">{{ $message }}</p>
    @enderror
</div>
<div>
    <label for="code">Kodea</label>
    <input id="code" name="code" value="{{ old('code', $module->code ?? '') }}" required maxlength="20">
    @error('code')
        <p class="error">{{ $message }}</p>
    @enderror
</div>
<div>
    <label for="teacher_id">Irakaslea</label>
    <select id="teacher_id" name="teacher_id" required>
        <option value="">Aukeratu irakaslea</option>
        @foreach ($teachers as $teacher)
            <option value="{{ $teacher->id }}" @selected((string) old('teacher_id', $module->teacher_id ?? '') === (string) $teacher->id)>
                {{ $teacher->name }}
            </option>
        @endforeach
    </select>
    @error('teacher_id')
        <p class="error">{{ $message }}</p>
    @enderror
</div>
```

`resources/views/modules/delete.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Ezabatu')

@section('content')
    <h1>Ezabatu</h1>
    <p>{{ $module->name }} ezabatu nahi duzu?</p>
    <form method="POST" action="{{ route('modules.destroy', $module) }}">
        @csrf
        @method('DELETE')
        <button type="submit">Bai, ezabatu</button>
        <a href="{{ route('modules.show', $module) }}">Utzi</a>
    </form>
@endsection
```

## Irakaslearen fitxan bere moduluak erakutsi

Gehitu `$teacher->load('modules');` lerroa `TeacherController::show()` metodoan, `return` aurretik. Ondoren, gehitu fitxan:

```blade
<h2>Esleitutako moduluak</h2>
<ul>
    @forelse ($teacher->modules as $module)
        <li><a href="{{ route('modules.show', $module) }}">{{ $module->name }}</a></li>
    @empty
        <li>Irakasleak ez du modulurik esleituta.</li>
    @endforelse
</ul>
```

Egiaztatu Maialenen fitxan DWS eta DWC agertzen direla. Aldatu modulu baten irakaslea eta begiratu nola aldatzen diren bi fitxak. Saiatu moduluak dituen irakasle bat ezabatzen eta egiaztatu mezua.

[Laravel 13 · Eloquent erlazioak](https://laravel.com/docs/13.x/eloquent-relationships)

[Aurrekoa · Ikasleen CRUDa](all-show-create-edit-delete-adibideekin.md) | [Hurrengoa · N:M erlazioa eta matrikulak](erlazioak-n-m.md)
