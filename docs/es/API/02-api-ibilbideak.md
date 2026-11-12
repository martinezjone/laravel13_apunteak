# 2. Rutas de API

## Activar las rutas de API

Si en una instalación nueva de Laravel 13 no existe el archivo `routes/api.php`, ejecuta:

```bash
php artisan install:api
```

El comando prepara las rutas de API e instala Sanctum. Si el comando solicita ejecutar las migraciones, sigue las instrucciones de la terminal.

## `web.php` y `api.php`

| `routes/web.php` | `routes/api.php` |
|---|---|
| Páginas web habituales | Endpoints de API |
| Normalmente Blade/HTML | Normalmente JSON |
| Grupo de middleware `web` | Grupo de middleware `api` |
| Flujo web habitual con sesión y CSRF | API sin sesión de forma predeterminada; puede usar sesiones con la configuración SPA de Sanctum |

Las rutas del archivo `routes/api.php` reciben el prefijo `/api` de forma predeterminada.

```php
<?php

use Illuminate\Support\Facades\Route;

Route::get('/agurra', function () {
    return response()->json(['mezua' => 'Kaixo, API!']);
});
```

Pruébalo en `http://localhost:8000/api/agurra`, con `php artisan serve` en ejecución.

## Ver las rutas

```bash
php artisan route:list --path=api
```

!!! warning "Error habitual"
    No escribas `/api/agurra` como ruta dentro de `api.php` si se añade el prefijo automáticamente. De lo contrario, se puede crear `/api/api/agurra`.

!!! tip "Ejercicio"
    Crea el endpoint `GET /api/egoera` y devuelve un objeto JSON que contenga los campos `egoera`, `bertsioa` y `aktiboa`.
