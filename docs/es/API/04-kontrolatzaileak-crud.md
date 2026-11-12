# 4. Controladores de API y CRUD

## Punto de partida

Supongamos que ya se han creado la tabla `jarduerak` y el modelo Eloquent `Jarduera`. Los ejemplos utilizan los campos `izena`, `deskribapena` y `plazak`.

En el modelo, para usar la asignación masiva, define los campos permitidos:

```php
// app/Models/Jarduera.php (klasearen barruan)
protected $fillable = ['izena', 'deskribapena', 'plazak'];
```

## Crear un controlador

```bash
php artisan make:controller Api/JardueraController --api
```

La opción `--api` excluye los métodos `create` y `edit`, porque normalmente una API no genera vistas HTML para formularios.

## Rutas

```php
// routes/api.php
use App\Http\Controllers\Api\JardueraController;
use Illuminate\Support\Facades\Route;

Route::apiResource('jarduerak', JardueraController::class);
```

## Ejemplo de CRUD

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Jarduera;
use Illuminate\Http\Request;

class JardueraController extends Controller
{
    public function index()
    {
        return Jarduera::all();
    }

    public function store(Request $request)
    {
        $datuak = $request->validate([
            'izena' => ['required', 'string', 'max:255'],
            'deskribapena' => ['nullable', 'string'],
            'plazak' => ['required', 'integer', 'min:0'],
        ]);

        $jarduera = Jarduera::create($datuak);

        return response()->json($jarduera, 201);
    }

    public function show(Jarduera $jarduera)
    {
        return $jarduera;
    }

    public function update(Request $request, Jarduera $jarduera)
    {
        $datuak = $request->validate([
            'izena' => ['sometimes', 'required', 'string', 'max:255'],
            'deskribapena' => ['sometimes', 'nullable', 'string'],
            'plazak' => ['sometimes', 'required', 'integer', 'min:0'],
        ]);

        $jarduera->update($datuak);

        return $jarduera;
    }

    public function destroy(Jarduera $jarduera)
    {
        $jarduera->delete();

        return response()->noContent();
    }
}
```

!!! note "Siguiente paso"
    Este ejemplo muestra un CRUD básico. En la siguiente sección, usaremos **API Resources** en lugar de devolver los modelos directamente. En una aplicación real, hay que proteger las operaciones de escritura y comprobar los permisos.

!!! tip "Ejercicio"
    Ejecuta `php artisan route:list --path=api` y relaciona las cinco acciones generadas con los métodos HTTP.
