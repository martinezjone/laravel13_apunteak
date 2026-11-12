# 7. Pruebas de API

## ¿Por qué probar antes de crear Vue?

Al probar el backend y el frontend por separado, es más fácil identificar el origen de los errores.

## Mediante `curl`

```bash
curl -i -H "Accept: application/json" http://127.0.0.1:8000/api/jarduerak
```

En Windows PowerShell, se puede usar `curl.exe` para llamar al ejecutable real de curl:

```powershell
curl.exe -i -H "Accept: application/json" http://127.0.0.1:8000/api/jarduerak
```

Ejemplo de una solicitud POST (PowerShell):

```powershell
curl.exe -i -X POST "http://127.0.0.1:8000/api/jarduerak" `
  -H "Accept: application/json" `
  -H "Content-Type: application/json" `
  -d '{"izena":"Dantza tailerra","plazak":20}'
```

!!! warning "PowerShell y JSON"
    En Windows, la interpretación de las comillas puede variar según el shell y la versión de curl. Si hay problemas, usa un archivo JSON: `curl.exe ... --data-binary '@jarduera.json'`.

## Casos que se deben probar

| Prueba | Resultado esperado |
|---|---|
| `GET /api/jarduerak` | 200 y una lista JSON |
| `GET /api/jarduerak/999999` | 404, si el ID no existe |
| `POST` con datos válidos | 201 |
| `POST` con datos no válidos | 422 |
| `DELETE` correcto | 204 |

## Prueba automatizada en Laravel

```bash
php artisan make:test JardueraApiTest
```

```php
public function test_jardueren_zerrenda_jaso_daiteke(): void
{
    $response = $this->getJson('/api/jarduerak');
    $response->assertOk();
}
```

!!! note "Para el aula"
    Si no se puede instalar Postman, se puede usar `curl.exe`, una extensión de solicitudes HTTP de VS Code (si está instalada) o las pruebas HTTP de Laravel.

!!! tip "Ejercicio"
    Crea una tabla de pruebas con cinco casos: solicitud, datos enviados, código esperado y código obtenido.
