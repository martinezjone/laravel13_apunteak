# 7. API probak

## Zergatik probatu Vue sortu aurretik?

Backend-a eta frontend-a bereizita probatuz, akatsen jatorria errazago identifikatzen dugu.

## `curl` bidez

```bash
curl -i -H "Accept: application/json" http://127.0.0.1:8000/api/jarduerak
```

Windows PowerShell-en, `curl.exe` erabili daiteke benetako curl exekutagarria deitzeko:

```powershell
curl.exe -i -H "Accept: application/json" http://127.0.0.1:8000/api/jarduerak
```

POST eskaera baten adibidea (PowerShell):

```powershell
curl.exe -i -X POST "http://127.0.0.1:8000/api/jarduerak" `
  -H "Accept: application/json" `
  -H "Content-Type: application/json" `
  -d '{"izena":"Dantza tailerra","plazak":20}'
```

!!! warning "PowerShell eta JSON"
    Windows-en komatxoen interpretazioa aldatu egin daiteke shell-aren eta curl bertsioaren arabera. Arazoak badaude, erabili JSON fitxategi bat: `curl.exe ... --data-binary '@jarduera.json'`.

## Probatu beharreko kasuak

| Proba | Espero den emaitza |
|---|---|
| `GET /api/jarduerak` | 200 eta JSON zerrenda |
| `GET /api/jarduerak/999999` | 404, IDa existitzen ez bada |
| `POST` datu egokiekin | 201 |
| `POST` datu baliogabeekin | 422 |
| `DELETE` arrakastatsua | 204 |

## Laravel-en test automatizatua

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

!!! note "Ikasgelarako"
    Postman instalatu ezin bada, `curl.exe`, VS Code-ko HTTP eskaeren luzapen bat (instalatuta badago) edo Laravel-en HTTP testak erabil daitezke.

!!! tip "Ariketa"
    Sortu bost kasuko proba-taula: eskaera, bidalitako datuak, espero den kodea eta lortutako kodea.
