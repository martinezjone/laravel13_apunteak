# TxurdiGest · Ikasleen CRUDa

Aurreko atalean `students` taula eta probako ikasleak sortu ditugu. Orain, urrats bakoitzean **ibilbidea → kontroladorearen metodoa → Blade bista** lotuko ditugu.

```bash
php artisan make:controller StudentController --model=Student --resource
```

## Ibilbideak banan-banan

Gehitu inportazioa `routes/web.php` fitxategiaren goialdean eta joan ibilbideak eransten lantzen duzun urratsaren arabera. Mantendu aurreko ataleko hasierako ibilbidea.

```php
use App\Http\Controllers\StudentController;

Route::get('/students', [StudentController::class, 'index'])->name('students.index');
Route::get('/students/create', [StudentController::class, 'create'])->name('students.create');
Route::post('/students', [StudentController::class, 'store'])->name('students.store');
Route::get('/students/{student}', [StudentController::class, 'show'])->name('students.show');
Route::get('/students/{student}/edit', [StudentController::class, 'edit'])->name('students.edit');
Route::put('/students/{student}', [StudentController::class, 'update'])->name('students.update');
Route::get('/students/{student}/delete', [StudentController::class, 'delete'])->name('students.delete');
Route::delete('/students/{student}', [StudentController::class, 'destroy'])->name('students.destroy');
```

| Urratsa | Kodea | Zer probatu |
| --- | --- | --- |
| 1. Zerrenda | `index()` eta `index.blade.php` | Ireki `/students` eta egiaztatu seederraren izenak. |
| 2. Fitxa | `show()` eta `show.blade.php` | Ireki Jonen fitxa. Existitzen ez den ID batek 404 eman behar du. |
| 3. Alta | `create()`, `store()`, `create.blade.php` eta `_form.blade.php` | Sortu ikasle bat. |
| 4. Edizioa | `edit()`, `update()` eta `edit.blade.php` | Aldatu izena, posta bera mantenduta. |
| 5. Baja | `delete()`, `destroy()` eta `delete.blade.php` | Utzi behin eta baieztatu hurrengoan. |

Kode osoa behean dago. Urrats bat bakarrik probatzeko, gehitu momentuz behar dituzun metodoak eta bistak. Zerrendan eta fitxan, gerorako estekak dagozkien ibilbideak gehitzean kopiatu.

- `index()` metodoan `Student::all()` erabiliko dugu. Probatu baita `Student::orderBy('name')->get()`, `Student::where('name', 'like', '%Jon%')->get()` eta `Student::count()` ere.
- `show(Student $student)` metodoak **route model binding** erabiltzen du. `{student}` parametroa eta `$student` argumentua izenez bat datoz; Laravelek IDari dagokion erregistroa kargatzen du. Eskuzko baliokidea `Student::findOrFail($id)` da.
- `create()` eta `edit()` metodoek formularioa irekitzen dute; `store()` eta `update()` metodoek bidalitako datuak gordetzen dituzte. `@csrf` behar da idazketa-formularioetan. HTMLko `POST` eskaerari `@method('PUT')` edo `@method('DELETE')` gehitzen zaio, dagokion ibilbidera iristeko.
- `delete()` metodoak baieztapen-pantaila erakusten du. `destroy()` metodoak erregistroa ezabatzen du.
- Gutxieneko zerbitzari-balidazioa hasieratik dago, formularioak datu baliodunak bakarrik gorde ditzan. [Balidazioaren atalean](balidazioa-paginazioa-eta-hizkuntza.md) aztertuko dugu xeheago.

## Kontroladorea eta bistak

`app/Http/Controllers/StudentController.php`

```php
<?php

namespace App\Http\Controllers;

use App\Models\Student;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\Validation\Rule;
use Illuminate\View\View;

class StudentController extends Controller
{
    public function index(): View
    {
        $students = Student::all();

        return view('students.index', compact('students'));
    }

    public function show(Student $student): View
    {
        return view('students.show', compact('student'));
    }

    public function create(): View
    {
        return view('students.create');
    }

    public function store(Request $request): RedirectResponse
    {
        $student = Student::create($request->validate($this->rules()));

        return to_route('students.show', $student)->with('status', 'Ikaslea gorde da.');
    }

    public function edit(Student $student): View
    {
        return view('students.edit', compact('student'));
    }

    public function update(Request $request, Student $student): RedirectResponse
    {
        $student->update($request->validate($this->rules($student)));

        return to_route('students.show', $student)->with('status', 'Ikaslea gorde da.');
    }

    public function delete(Student $student): View
    {
        return view('students.delete', compact('student'));
    }

    public function destroy(Student $student): RedirectResponse
    {
        $student->delete();

        return to_route('students.index')->with('status', 'Ikaslea ezabatu da.');
    }

    private function rules(?Student $student = null): array
    {
        $unique = Rule::unique('students', 'email');
        if ($student !== null) {
            $unique->ignore($student);
        }

        return [
            'name' => ['required', 'string', 'max:100'],
            'email' => ['required', 'email', 'max:255', $unique],
        ];
    }
}
```

`resources/views/students/index.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Ikasleak')

@section('content')
    <h1>Ikasleak</h1>
    <a href="{{ route('students.create') }}">Ikasle berria</a>
    <ul>
        @forelse ($students as $student)
            <li><a href="{{ route('students.show', $student) }}">{{ $student->name }}</a></li>
        @empty
            <li>Ez dago erregistrorik.</li>
        @endforelse
    </ul>
@endsection
```

`resources/views/students/show.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Ikaslea · Fitxa')

@section('content')
    <h1>{{ $student->name }}</h1>
    <p>Helbide elektronikoa: {{ $student->email }}</p>
    <a href="{{ route('students.edit', $student) }}">Editatu</a>
    <a href="{{ route('students.delete', $student) }}">Ezabatu</a>
    <a href="{{ route('students.index') }}">Itzuli zerrendara</a>
@endsection
```

`resources/views/students/create.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Ikasle berria')

@section('content')
    <h1>Ikasle berria</h1>
    <form method="POST" action="{{ route('students.store') }}">
        @csrf
        @include('students._form')
        <button type="submit">Gorde</button>
    </form>
@endsection
```

`resources/views/students/edit.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Ikaslea editatu')

@section('content')
    <h1>Ikaslea editatu</h1>
    <form method="POST" action="{{ route('students.update', $student) }}">
        @csrf
        @method('PUT')
        @include('students._form')
        <button type="submit">Gorde</button>
    </form>
@endsection
```

`resources/views/students/_form.blade.php`

```blade
<div>
    <label for="name">Izen-abizenak</label>
    <input id="name" name="name" value="{{ old('name', $student->name ?? '') }}" required maxlength="100">
    @error('name')
        <p class="error">{{ $message }}</p>
    @enderror
</div>
<div>
    <label for="email">Helbide elektronikoa</label>
    <input type="email" id="email" name="email" value="{{ old('email', $student->email ?? '') }}" required maxlength="255">
    @error('email')
        <p class="error">{{ $message }}</p>
    @enderror
</div>
```

`resources/views/students/delete.blade.php`

```blade
@extends('layouts.app')

@section('title', 'Ezabatu')

@section('content')
    <h1>Ezabatu</h1>
    <p>{{ $student->name }} ezabatu nahi duzu?</p>
    <form method="POST" action="{{ route('students.destroy', $student) }}">
        @csrf
        @method('DELETE')
        <button type="submit">Bai, ezabatu</button>
        <a href="{{ route('students.show', $student) }}">Utzi</a>
    </form>
@endsection
```

## Ibilbideak laburtu baliabide batekin

CRUD osoa egiaztatu ondoren, ordezkatu eskuzko zazpi ibilbideak `Route::resource()` dei bakar batekin. Mantendu baieztapen-pantailarako ibilbide gehigarria.

```php
Route::resource('students', StudentController::class);
Route::get('/students/{student}/delete', [StudentController::class, 'delete'])->name('students.delete');
```

Exekutatu `php artisan route:list --path=students` eta parekatu ibilbide bakoitza bere kontroladore-metodoarekin.

[Laravel 13 · Baliabide-kontroladoreak](https://laravel.com/docs/13.x/controllers#resource-controllers) · [Ibilbideak](https://laravel.com/docs/13.x/routing)

[Aurrekoa · Datu-basea](datu-basea.md) | [Hurrengoa · 1:N erlazioa](erlazioak-1-n.md)
