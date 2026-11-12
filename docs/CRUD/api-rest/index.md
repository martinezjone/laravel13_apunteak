# API REST Laravel 13-rekin

## Zer ikasiko dugu?

Unitate honetan Laravel **backend** gisa erabiliko dugu: HTML orriak sortu beharrean, **JSON** datuak bidaliko dizkiogu Vue 3 aplikazioari.

**Aurretiko ezagutzak:** ibilbideak, kontrolatzaileak, Eloquent, migrazioak eta balidazioa.

| Blade bidez | API bidez |
|---|---|
| Zerbitzariak HTML sortzen du | Zerbitzariak JSON bidaltzen du |
| `return view(...)` | `return response()->json(...)` edo Resource bat |
| Nabigatzaileak HTML bistaratzen du | Vue-k JSON jaso eta interfazea sortzen du |

!!! info "Ikasketa-ibilbidea"
    Lehenengo API publikoa sortuko dugu. Ondoren, CRUDa, datuen formatua, erroreak eta probak landuko ditugu. Azkenik, Vue eta autentifikazioa gehituko ditugu.

## Edukien aurkibidea

1. [API REST eta JSON](01-api-rest-json.md)
2. [API ibilbideak](02-api-ibilbideak.md)
3. [JSON erantzunak](03-json-erantzunak.md)
4. [API kontrolatzaileak eta CRUD](04-kontrolatzaileak-crud.md)
5. [API Resources](05-api-resources.md)
6. [Balidazioa eta erroreak](06-balidazioa-erroreak.md)
7. [API probak](07-api-probak.md)
8. [Laravel + Vue + Sanctum](08-laravel-vue-sanctum.md)

## Erreferentzia ofizialak

- [Laravel 13 dokumentazioa](https://laravel.com/docs/13.x)
- [Routing](https://laravel.com/docs/13.x/routing)
- [Eloquent Resources](https://laravel.com/docs/13.x/eloquent-resources)
- [Sanctum](https://laravel.com/docs/13.x/sanctum)
