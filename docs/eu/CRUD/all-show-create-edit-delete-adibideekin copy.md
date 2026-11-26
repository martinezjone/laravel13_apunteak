# TxurdiGest · Ikasleen CRUDa

Aurreko atalean `students` taula eta probako ikasleak sortu ditugu. 

Orain, urrats bakoitzean **ibilbidea → kontroladorearen metodoa → Blade bista** lotuko ditugu.

```bash
php artisan make:controller StudentController --resource
```

## Ibilbideak banan-banan

Gehitu inportazioa `routes/web.php` fitxategiaren goialdean eta joan ibilbideak eransten lantzen duzun urratsaren arabera. Mantendu aurreko ataleko hasierako ibilbidea.

```php
use App\Http\Controllers\StudentController;

// Ikasleen zerrenda
Route::get('/students', [StudentController::class, 'index'])->name('students.index');

// Ikasle bati alta emateko
Route::get('/students/create', [StudentController::class, 'create'])->name('students.create');
Route::post('/students', [StudentController::class, 'store'])->name('students.store');

// Ikasle baten fitxa ikusteko
Route::get('/students/{student}', [StudentController::class, 'show'])->name('students.show');

// Ikasle bat aldatzeko
Route::get('/students/{student}/edit', [StudentController::class, 'edit'])->name('students.edit');
Route::put('/students/{student}', [StudentController::class, 'update'])->name('students.update');

// Ikasle bat ezabatzeko
Route::get('/students/{student}/delete', [StudentController::class, 'delete'])->name('students.delete');
Route::delete('/students/{student}', [StudentController::class, 'destroy'])->name('students.destroy');
```

### Ibilbideak resource
Ruta guztiak ez izateko, Laravelen aukera ematen digu resource bista guztiak elkarrekin izateko: 

```php
use App\Http\Controllers\StudentController;
// Ezabatu aurreko baieztapen-pantaila
Route::get('/students/{student}/delete', [StudentController::class, 'delete'])
    ->name('students.delete');

// Ikasleen CRUD ibilbideak
Route::resource('students', StudentController::class);
```
!!! Warning Baieztapen pantaila
    Aurreko abidibean ikusten duzuen moduan delete baieztapen pantaila aparte jarri behar izan dugu, rosourcek ez bait du egiten, gainontzeko guztiak bai.

Konprobatu nahi izanez gero erabili:

```bash
    php artisan route:list
```

## Kontroladorea eta bistak

Momentuz hasiko dugu kontroladorearen metodoak sortzen.

### app/Http/Controllers/StudentController.php

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

}
```

### resources/views/students/index.blade.php

```blade
@extends('layouts.app')

@section('title', 'Ikasleak')

@section('content')
    <h1>Ikasleak</h1>
    <a href="{{ route('students.create') }}">Ikasle berria</a>
    <ul>
        @forelse ($students as $student)
            <li>
                <a href="{{ route('students.show', $student) }}">{{ $student->name }}</a>
                · {{ $student->city }}
            </li>
        @empty
            <li>Ez dago erregistrorik.</li>
        @endforelse
    </ul>
@endsection
```

### resources/views/students/show.blade.php

```blade
@extends('layouts.app')

@section('title', 'Ikaslea · Fitxa')

@section('content')
    <h1>{{ $student->name }}</h1>
    <p>Helbide elektronikoa: {{ $student->email }}</p>
    <p>Jaiotze-data: {{ $student->birth_date }}</p>
    <p>Udalerria: {{ $student->city }}</p>
    <a href="{{ route('students.edit', $student) }}">Editatu</a>
    <a href="{{ route('students.delete', $student) }}">Ezabatu</a>
    <a href="{{ route('students.index') }}">Itzuli zerrendara</a>
@endsection
```

### resources/views/students/create.blade.php

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

### resources/views/students/edit.blade.php

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

### resources/views/students/_form.blade.php

```blade
<div>
    <label for="name">Izen-abizenak</label>
    <input type="text" id="name" name="name" value="{{ old('name', $student->name ?? '') }}" required maxlength="100">
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
<div>
    <label for="birth_date">Jaiotze-data</label>
    <input type="date" id="birth_date" name="birth_date"
        value="{{ old('birth_date', $student->birth_date ?? '') }}" required>
    @error('birth_date')
        <p class="error">{{ $message }}</p>
    @enderror
</div>
<div>
    <label for="city">Udalerria</label>
    <input type="text" id="city" name="city"
        value="{{ old('city', $student->city ?? '') }}" required maxlength="100">
    @error('city')
        <p class="error">{{ $message }}</p>
    @enderror
</div>
```

`old()`-ek azken bidalketan idatzitako balioa berreskuratzen du balidazioak huts egiten badu. Editatzeko formularioa lehen aldiz irekitzean, datu-baseko balioa erakusten du.

### resources/views/students/delete.blade.php

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

[Laravel 13 · Baliabide-kontroladoreak](https://laravel.com/docs/13.x/controllers#resource-controllers) · [Ibilbideak](https://laravel.com/docs/13.x/routing)

[Aurrekoa · Datu-basea](datu-basea.md) | [Hurrengoa · 1:N erlazioa](erlazioak-1-n.md)
