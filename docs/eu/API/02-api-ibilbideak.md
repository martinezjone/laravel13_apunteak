# 2. API ibilbideak

## API ibilbideak aktibatu

Laravel 13ko instalazio berri batean `routes/api.php` fitxategia ez badago, exekutatu:

```bash
php artisan install:api
```

Komandoak API ibilbideak prestatzen ditu eta Sanctum instalatzen du. Komandoak migrazioak exekutatzea eskatzen badu, jarraitu terminaleko argibideei.

## `web.php` eta `api.php`

| `routes/web.php` | `routes/api.php` |
|---|---|
| Ohiko web orriak | API endpoint-ak |
| Normalean Blade/HTML | Normalean JSON |
| `web` middleware taldea | `api` middleware taldea |
| Saioaren eta CSRFren ohiko web-fluxua | API lehenespenez saiorik gabekoa; Sanctum SPA konfigurazioarekin saioak erabil ditzake |

`routes/api.php` fitxategiko ibilbideek `/api` aurrizkia jasotzen dute lehenespenez.

```php
<?php

use Illuminate\Support\Facades\Route;

Route::get('/agurra', function () {
    return response()->json(['mezua' => 'Kaixo, API!']);
});
```

Probatu `http://localhost:8000/api/agurra` helbidean, `php artisan serve` abiarazita.

## Ibilbideak ikusi

```bash
php artisan route:list --path=api
```

!!! warning "Ohiko akatsa"
    Ez idatzi `/api/agurra` `api.php` barruko bidean, aurrizkia automatikoki gehitzen bada. Bestela `/api/api/agurra` sor daiteke.

!!! tip "Ariketa"
    Sortu `GET /api/egoera` endpoint-a eta itzuli `egoera`, `bertsioa` eta `aktiboa` eremuak dituen JSON objektua.
