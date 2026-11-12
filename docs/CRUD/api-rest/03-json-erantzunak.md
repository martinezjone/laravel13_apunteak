# 3. JSON erantzunak

## `response()->json()`

```php
Route::get('/mezua', function () {
    return response()->json([
        'mezua' => 'Datuak behar bezala jaso dira',
        'kopurua' => 3,
    ], 200);
});
```

Bigarren parametroa HTTP egoera-kodea da.

## Eloquent datuak itzultzea

```php
use App\Models\Jarduera;

Route::get('/jarduerak', function () {
    return Jarduera::all();
});
```

Laravel-ek Eloquent bilduma JSON bihur dezake automatikoki. Hala ere, proiektuetan **API Resource** erabiltzea gomendatzen da, kanpora bidaltzen diren eremuak kontrolatzeko.

## Erantzunen adibideak

```php
return response()->json(['mezua' => 'Sortuta'], 201);
```

```php
return response()->noContent(); // 204
```

!!! danger "Ez bidali datu guztiak automatikoki"
    Eredu bateko eremu guztiak zuzenean itzultzea ez da beti egokia. Erabili Resources, eta zaindu datu pertsonalak eta barne-eremuak.

!!! tip "Ariketa"
    Sortu jarduera bakarra itzultzen duen endpoint-a eta probatu erantzunaren `Content-Type: application/json` goiburua.
