# 3. Respuestas JSON

## `response()->json()`

```php
Route::get('/mezua', function () {
    return response()->json([
        'mezua' => 'Datuak behar bezala jaso dira',
        'kopurua' => 3,
    ], 200);
});
```

El segundo parámetro es el código de estado HTTP.

## Devolver datos de Eloquent

```php
use App\Models\Jarduera;

Route::get('/jarduerak', function () {
    return Jarduera::all();
});
```

Laravel puede convertir automáticamente una colección de Eloquent a JSON. Aun así, en los proyectos se recomienda usar **API Resource** para controlar los campos que se envían al exterior.

## Ejemplos de respuestas

```php
return response()->json(['mezua' => 'Sortuta'], 201);
```

```php
return response()->noContent(); // 204
```

!!! danger "No envíes todos los datos automáticamente"
    No siempre es adecuado devolver directamente todos los campos de un modelo. Usa Resources y protege los datos personales y los campos internos.

!!! tip "Ejercicio"
    Crea un endpoint que devuelva una sola actividad y comprueba la cabecera `Content-Type: application/json` de la respuesta.
