# 4. API kontrolatzaileak eta CRUD

## Abiapuntua

Demagun `jarduerak` taula eta `Jarduera` Eloquent eredua aurretik sortuta daudela. Adibideek `izena`, `deskribapena` eta `plazak` eremuak erabiltzen dituzte.

Ereduan, esleipen masiboa erabiltzeko, definitu onartutako eremuak:

```php
// app/Models/Jarduera.php (klasearen barruan)
protected $fillable = ['izena', 'deskribapena', 'plazak'];
```

## Kontrolatzailea sortu

```bash
php artisan make:controller Api/JardueraController --api
```

`--api` aukerak `create` eta `edit` metodoak baztertzen ditu, API batek ez baititu normalean formularioen HTML bistak sortzen.

## Ibilbideak

```php
// routes/api.php
use App\Http\Controllers\Api\JardueraController;
use Illuminate\Support\Facades\Route;

Route::apiResource('jarduerak', JardueraController::class);
```

## CRUD adibidea

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

!!! note "Hurrengo urratsa"
    Adibide honek oinarrizko CRUDa erakusten du. Hurrengo atalean, ereduak zuzenean itzuli beharrean, **API Resources** erabiliko ditugu. Idazketa-eragiketak babestu eta baimenak egiaztatu behar dira benetako aplikazio batean.

!!! tip "Ariketa"
    Exekutatu `php artisan route:list --path=api` eta lotu sortutako bost ekintzak HTTP metodoekin.
