# 5. API Resources

## ¿Por qué usar un Resource?

Un API Resource convierte un modelo de Eloquent en la **estructura JSON que se enviará al cliente**. Así separamos la estructura de la base de datos del contrato de la API.

## Crear un Resource

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

## Usarlo en el controlador

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

De forma predeterminada, la respuesta de un Resource puede tener la clave `data`:

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

!!! info "Importante para Vue"
    Hay que acordar la estructura JSON. Por ejemplo, el envoltorio `data` influye en cómo se leen los datos en Vue (`response.data.data`).

## Relaciones

Al añadir relaciones, ten en cuenta el problema de las consultas N+1. Usa carga anticipada y `whenLoaded()` cuando sea necesario.

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

!!! tip "Ejercicio"
    Modifica el Resource para que no envíe `created_at` y añade el campo booleano `plazak_badaude`.
