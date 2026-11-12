# 8. Laravel + Vue 3 + Sanctum

## Arkitektura

```text
Vue 3 + Axios (frontend)
        | HTTP + JSON
        v
Laravel 13 (API + Eloquent)
        |
        v
Datu-basea
```

## API publiko bat Vue-tik kontsumitu

```bash
npm install axios
```

```js
// src/services/api.js
import axios from 'axios'

export const api = axios.create({
  baseURL: 'http://localhost:8000',
  headers: { Accept: 'application/json' },
})
```

```vue
<script setup>
import { ref, onMounted } from 'vue'
import { api } from '@/services/api'

const jarduerak = ref([])
const errorea = ref('')

onMounted(async () => {
  try {
    const response = await api.get('/api/jarduerak')
    jarduerak.value = response.data.data // API Resource bildumaren bilgarri lehenetsia
  } catch {
    errorea.value = 'Ezin izan dira jarduerak kargatu.'
  }
})
</script>

<template>
  <p v-if="errorea">{{ errorea }}</p>
  <ul v-else>
    <li v-for="jarduera in jarduerak" :key="jarduera.id">
      {{ jarduera.izena }}
    </li>
  </ul>
</template>
```

!!! note "Erantzunaren egitura"
    Endpoint-ak Eloquent bilduma zuzenean itzultzen badu, `response.data` izango da zerrenda; Resource bildumaren lehenetsitako egiturarekin, `response.data.data`.

## SPA autentifikazioa: Sanctum

Vue gure lehen mailako SPA denean, **Sanctum-en cookie eta saio bidezko autentifikazioa** da ohiko aukera. Ez nahastu hori kanpoko bezeroentzat erabil daitezkeen API tokenekin.

1. `php artisan install:api` erabiliz Sanctum prestatu.
2. `bootstrap/app.php` fitxategian `statefulApi()` konfiguratu.
3. CORS eta Sanctum-en stateful domeinuak egokitu.
4. Axios-en cookieak eta XSRF babesa konfiguratu.
5. `/sanctum/csrf-cookie` eskatu, ondoren saioa hasi.
6. Babestutako ibilbideetan `auth:sanctum` erabili.

```php
// bootstrap/app.php (middleware konfigurazioaren barruan)
->withMiddleware(function (Middleware $middleware): void {
    $middleware->statefulApi();
})
```

```js
// src/services/api.js: aurreko axios instantziaren konfigurazioari gehitzeko
api.defaults.withCredentials = true
api.defaults.withXSRFToken = true
```

```js
await api.get('/sanctum/csrf-cookie')
await api.post('/login', { email, password })
const response = await api.get('/api/user')
```

!!! warning "Konfigurazioa ez da kopiatzea bakarrik"
    Aurreko adibideak **ez du berez `/login` endpoint-a sortzen**: autentifikazio-ibilbideak eta saioaren konfigurazioa prest egon behar dira. `localhost` eta `127.0.0.1` ez nahastu; aukeratu host koherenteak, eta konfiguratu `SANCTUM_STATEFUL_DOMAINS`, saioaren cookieak eta CORS proiektuaren arabera. SPA eta APIak cookieak partekatzeko domeinu nagusi bera izan behar dute, eta portuak desberdinak izan daitezke.

## CORS, CSRF eta Sanctum: ez dira gauza bera

| Kontzeptua | Eginkizuna |
|---|---|
| CORS | Nabigatzaileak jatorri desberdinen arteko eskaerak nola baimentzen dituen kontrolatzen du |
| CSRF | Cookie bidez autentifikatutako eskaerak faltsutzearen aurka babesten du |
| Sanctum | SPAren autentifikazioa Laravel saioekin integratzen du |

## Babestutako ibilbidea

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

Route::middleware('auth:sanctum')->get('/nire-profila', function (Request $request) {
    return $request->user();
});
```

Goiko adibidea erabiltzeko, `Illuminate\Http\Request` inportatu behar da. Produkzioan, erabiltzailearen datuak itzultzean, hautatu kanporatu beharreko eremuak.

!!! tip "Azken erronka"
    Jardueren zerrenda publiko bat erakutsi Vue-n; erabiltzaile autentifikatuek bakarrik sortu, aldatu edo ezabatu ahal izango dituzte jarduerak. Gehitu balidazioa, baimen-egiaztapenak eta erroreen bistaratzea.

## Dokumentazio ofiziala

- [Sanctum](https://laravel.com/docs/13.x/sanctum)
- [CSRF Protection](https://laravel.com/docs/13.x/csrf)
- [HTTP Client / Axios konfigurazioa (Sanctum atalean)](https://laravel.com/docs/13.x/sanctum#spa-authentication)
