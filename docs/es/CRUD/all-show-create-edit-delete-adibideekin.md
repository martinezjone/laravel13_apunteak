# TxurdiGest · CRUD de estudiantes

En el apartado anterior hemos creado la tabla `students` y algunos estudiantes de prueba.

Ahora, en cada paso, conectaremos **ruta → método del controlador → vista Blade**.

```bash
php artisan make:controller StudentController --resource
```

Añade la importación al principio del archivo `routes/web.php` y ve incorporando las rutas según el paso que estés trabajando. Conserva la ruta inicial del apartado anterior.

```php
use App\Http\Controllers\StudentController;
```

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
}
```

## 1. Lista de estudiantes

### Ruta

```php
// Lista de estudiantes
Route::get('/students', [StudentController::class, 'index'])->name('students.index');
```

### Controlador

```php
public function index(): View
{
    $students = Student::all();

    return view('students.index', compact('students'));
}
```

### resources/views/students/index.blade.php

```blade
@extends('layouts.app')

@section('title', 'Estudiantes')

@section('content')
    <h1>Estudiantes</h1>
    <a href="{{ route('students.create') }}">Nuevo estudiante</a>
    <ul>
        @forelse ($students as $student)
            <li>
                <a href="{{ route('students.show', $student) }}">{{ $student->name }}</a>
                · {{ $student->city }}
            </li>
        @empty
            <li>No hay registros.</li>
        @endforelse
    </ul>
@endsection
```

## 2. Ficha de un estudiante

### Ruta

```php
// Ver la ficha de un estudiante
Route::get('/students/{student}', [StudentController::class, 'show'])->name('students.show');
```

### Controlador

```php
public function show(Student $student): View
{
    return view('students.show', compact('student'));
}
```

### resources/views/students/show.blade.php

```blade
@extends('layouts.app')

@section('title', 'Estudiante · Ficha')

@section('content')
    <h1>{{ $student->name }}</h1>
    <p>Correo electrónico: {{ $student->email }}</p>
    <p>Fecha de nacimiento: {{ $student->birth_date }}</p>
    <p>Ciudad: {{ $student->city }}</p>
    <a href="{{ route('students.edit', $student) }}">Editar</a>
    <a href="{{ route('students.delete', $student) }}">Eliminar</a>
    <a href="{{ route('students.index') }}">Volver a la lista</a>
@endsection
```

## 3. Crear un estudiante

### Rutas

```php
// Crear un estudiante
Route::get('/students/create', [StudentController::class, 'create'])->name('students.create');
Route::post('/students', [StudentController::class, 'store'])->name('students.store');
```

### Controlador

```php
public function create(): View
{
    return view('students.create');
}

public function store(Request $request): RedirectResponse
{
    $student = Student::create($request->validate($this->rules()));

    return to_route('students.show', $student)->with('status', 'Estudiante guardado.');
}
```

### resources/views/students/create.blade.php

```blade
@extends('layouts.app')

@section('title', 'Nuevo estudiante')

@section('content')
    <h1>Nuevo estudiante</h1>
    <form method="POST" action="{{ route('students.store') }}">
        @csrf
        @include('students._form')
        <button type="submit">Guardar</button>
    </form>
@endsection
```

### resources/views/students/_form.blade.php

```blade
<div>
    <label for="name">Nombre y apellidos</label>
    <input type="text" id="name" name="name" value="{{ old('name', $student->name ?? '') }}" required maxlength="100">
    @error('name')
        <p class="error">{{ $message }}</p>
    @enderror
</div>
<div>
    <label for="email">Correo electrónico</label>
    <input type="email" id="email" name="email" value="{{ old('email', $student->email ?? '') }}" required maxlength="255">
    @error('email')
        <p class="error">{{ $message }}</p>
    @enderror
</div>
<div>
    <label for="birth_date">Fecha de nacimiento</label>
    <input type="date" id="birth_date" name="birth_date"
        value="{{ old('birth_date', $student->birth_date ?? '') }}" required>
    @error('birth_date')
        <p class="error">{{ $message }}</p>
    @enderror
</div>
<div>
    <label for="city">Ciudad</label>
    <input type="text" id="city" name="city"
        value="{{ old('city', $student->city ?? '') }}" required maxlength="100">
    @error('city')
        <p class="error">{{ $message }}</p>
    @enderror
</div>
```

`old()` recupera el valor introducido en el último envío si la validación falla. La primera vez que se abre el formulario de edición, muestra el valor guardado en la base de datos.

## 4. Editar un estudiante

### Rutas

```php
// Editar un estudiante
Route::get('/students/{student}/edit', [StudentController::class, 'edit'])->name('students.edit');
Route::put('/students/{student}', [StudentController::class, 'update'])->name('students.update');
```

### Controlador

```php
public function edit(Student $student): View
{
    return view('students.edit', compact('student'));
}

public function update(Request $request, Student $student): RedirectResponse
{
    $student->update($request->validate($this->rules($student)));

    return to_route('students.show', $student)->with('status', 'Estudiante guardado.');
}
```

### resources/views/students/edit.blade.php

```blade
@extends('layouts.app')

@section('title', 'Editar estudiante')

@section('content')
    <h1>Editar estudiante</h1>
    <form method="POST" action="{{ route('students.update', $student) }}">
        @csrf
        @method('PUT')
        @include('students._form')
        <button type="submit">Guardar</button>
    </form>
@endsection
```

## 5. Eliminar un estudiante

### Rutas

```php
// Eliminar un estudiante
Route::get('/students/{student}/delete', [StudentController::class, 'delete'])->name('students.delete');
Route::delete('/students/{student}', [StudentController::class, 'destroy'])->name('students.destroy');
```

### Controlador

```php
public function delete(Student $student): View
{
    return view('students.delete', compact('student'));
}

public function destroy(Student $student): RedirectResponse
{
    $student->delete();

    return to_route('students.index')->with('status', 'Estudiante eliminado.');
}
```

### resources/views/students/delete.blade.php

```blade
@extends('layouts.app')

@section('title', 'Eliminar')

@section('content')
    <h1>Eliminar</h1>
    <p>¿Quieres eliminar a {{ $student->name }}?</p>
    <form method="POST" action="{{ route('students.destroy', $student) }}">
        @csrf
        @method('DELETE')
        <button type="submit">Sí, eliminar</button>
        <a href="{{ route('students.show', $student) }}">Cancelar</a>
    </form>
@endsection
```

## Rutas resource

Para no tener que escribir todas las rutas, Laravel permite agrupar las rutas resource:

```php
use App\Http\Controllers\StudentController;
// Pantalla de confirmación de eliminación, que no incluye resource
Route::get('/students/{student}/delete', [StudentController::class, 'delete'])
    ->name('students.delete');

// Rutas CRUD de estudiantes
Route::resource('students', StudentController::class);
```

!!! warning "Pantalla de confirmación"
    Como se ve en el ejemplo anterior, la pantalla de confirmación de eliminación debe definirse por separado: `Route::resource` no la incluye, aunque sí genera las demás rutas.

Para comprobar las rutas, ejecuta:

```bash
php artisan route:list
```

[Laravel 13 · Controladores resource](https://laravel.com/docs/13.x/controllers#resource-controllers) · [Rutas](https://laravel.com/docs/13.x/routing)

[Anterior · Base de datos](datu-basea.md) | [Siguiente · Relación 1:N](erlazioak-1-n.md)
