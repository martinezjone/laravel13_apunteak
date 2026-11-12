# 6. Balidazioa eta erroreak

## Zerbitzarian balidatu

Vue-n balidatzea lagungarria da erabiltzailearentzat, baina **Laravel-en balidazioa ezinbestekoa da**.

```php
$datuak = $request->validate([
    'izena' => ['required', 'string', 'max:255'],
    'plazak' => ['required', 'integer', 'min:0'],
]);
```

Eskaerak JSON erantzuna espero duenean, balidazioak huts eginez gero Laravel-ek normalean **422** kodea eta erroreen objektua itzultzen ditu:

```json
{
  "message": "The izena field is required.",
  "errors": {
    "izena": ["The izena field is required."]
  }
}
```

Mezuen hizkuntza aplikazioaren lokalizazioaren araberakoa da.

## 404 errorea

`show(Jarduera $jarduera)` bezalako route model binding batean erregistroa ez bada existitzen, Laravel-ek 404 erantzuna sortzen du.

## 401 eta 403

- **401 Unauthorized**: autentifikazioa falta da edo ez da baliozkoa.
- **403 Forbidden**: erabiltzailea identifikatuta egon daiteke, baina ez du ekintza egiteko baimenik.

## JSON eskatu

APIa probatzean, bidali:

```http
Accept: application/json
Content-Type: application/json
```

`Content-Type` goiburua JSON gorputza bidaltzen denean da beharrezkoa; GET arruntetan ez da derrigorrezkoa.

!!! warning "Segurtasuna"
    Ez erakutsi salbuespenen trazak edo konfigurazio-datuak produkzioan. Konfiguratu `APP_DEBUG=false` produkzio-ingurunean.

!!! tip "Ariketa"
    Bidali `plazak: -3` eta egiaztatu 422 erantzuna. Ondoren, eskatu existitzen ez den ID bat eta egiaztatu 404 erantzuna.
