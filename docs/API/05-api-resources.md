# 5. API Resources

## Zergatik erabili Resource bat?

API Resource batek Eloquent eredua **bezeroari bidaliko zaion JSON egituran** bihurtzen du. Horrela, datu-basearen egitura eta APIaren kontratua bereizten ditugu.

## Resource bat sortu

```bash
php artisan make:resource JardueraResource
```

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class JardueraResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'izena' => $this->izena,
            'deskribapena' => $this->deskribapena,
            'plazak' => $this->plazak,
        ];
    }
}
```

## Kontrolatzailean erabili

```php
use App\Http\Resources\JardueraResource;

public function index()
{
    return JardueraResource::collection(Jarduera::all());
}

public function show(Jarduera $jarduera)
{
    return new JardueraResource($jarduera);
}
```

Lehenespenez, Resource baten erantzunak `data` gakoa izan dezake:

```json
{
  "data": {
    "id": 1,
    "izena": "Dantza tailerra",
    "deskribapena": "Hasiberrientzat",
    "plazak": 20
  }
}
```

!!! info "Vue-rako garrantzitsua"
    JSONen egitura adostu behar da. Adibidez, `data` bilgarriak Vue-n datuak irakurtzeko moduan eragina du (`response.data.data`).

## Harremanak

Harremanak gehitzean, kontuan hartu N+1 kontsulten arazoa. Erabili eager loading eta `whenLoaded()` behar denean.

```php
// Kontrolatzailean
$jarduerak = Jarduera::with('kategoria')->get();
return JardueraResource::collection($jarduerak);
```

```php
// Resource-aren toArray() barruan (kategoria erlazioa badago)
'kategoria' => $this->whenLoaded('kategoria', fn () => [
    'id' => $this->kategoria->id,
    'izena' => $this->kategoria->izena,
]),
```

!!! tip "Ariketa"
    Aldatu Resource-a `created_at` ez bidaltzeko, eta gehitu `plazak_badaude` eremu boolearra.
