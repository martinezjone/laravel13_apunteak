# 1. API REST eta JSON

## Zer da API bat?

**API** batek aplikazioen arteko komunikazioa ahalbidetzen du. Gure kasuan, Laravel-ek datuak eskaintzen ditu eta Vue-k erabiltzaileari erakusten dizkio.

```text
Vue (nabigatzailea)  -- HTTP eskaera -->  Laravel API
Vue (nabigatzailea)  <-- JSON erantzuna --  Laravel API
```

## JSON formatua

JSON testu-formatu bat da. Objektuak `{}` artean eta zerrendak `[]` artean adierazten dira.

```json
{
  "id": 1,
  "izena": "Dantza tailerra",
  "plazak": 20,
  "aktiboa": true
}
```

!!! warning "Kontuan izan"
    JSONen gakoak eta testuak komatxo bikoitzen artean idazten dira. `true`, `false`, `null` eta zenbakiak ez dira komatxo artean jartzen.

## HTTP metodoak

| Metodoa | Helburua | Adibidea |
|---|---|---|
| GET | Baliabideak irakurri | `GET /api/jarduerak` |
| POST | Baliabide berria sortu | `POST /api/jarduerak` |
| GET | Baliabide bat irakurri | `GET /api/jarduerak/1` |
| PUT/PATCH | Baliabidea eguneratu | `PUT /api/jarduerak/1` |
| DELETE | Baliabidea ezabatu | `DELETE /api/jarduerak/1` |

## Ohiko egoera-kodeak

| Kodea | Esanahia |
|---|---|
| 200 | Eskaera ondo burutu da |
| 201 | Baliabidea sortu da |
| 204 | Erantzunak ez du edukirik |
| 401 | Erabiltzailea ez dago autentifikatuta |
| 403 | Erabiltzaileak ez du baimenik |
| 404 | Baliabidea ez da aurkitu |
| 422 | Balidazioak huts egin du |
| 500 | Zerbitzariaren errorea |

!!! tip "Ariketa"
    Pentsatu jardueren kudeaketa-aplikazio batean: zein HTTP metodo erabiliko zenuke jarduera guztiak ikusteko, berri bat sortzeko eta bat ezabatzeko?
