# 8. Laravel + Vue 3 + Sanctum

## Arquitectura

```text
Vue 3 + Axios (frontend)
        | HTTP + JSON
        v
Laravel 13 (API + Eloquent)
        |
        v
Datu-basea
```

## Consumir una API pública desde Vue

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

!!! note "Estructura de la respuesta"
    Si el endpoint devuelve directamente una colección de Eloquent, la lista estará en `response.data`; con la estructura predeterminada de una colección Resource, estará en `response.data.data`.

## Autenticación SPA: Sanctum

Cuando Vue es nuestra SPA de primera parte, la **autenticación mediante cookies y sesiones de Sanctum** suele ser la opción habitual. No la confundas con los tokens de API que pueden utilizar clientes externos.

1. Prepara Sanctum mediante `php artisan install:api`.
2. Configura `statefulApi()` en el archivo `bootstrap/app.php`.
3. Ajusta CORS y los dominios stateful de Sanctum.
4. Configura las cookies y la protección XSRF en Axios.
5. Solicita `/sanctum/csrf-cookie` y, después, inicia sesión.
6. Usa `auth:sanctum` en las rutas protegidas.

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

!!! warning "La configuración no consiste solo en copiar"
    El ejemplo anterior **no crea por sí solo el endpoint `/login`**: las rutas de autenticación y la configuración de sesión deben estar preparadas. No mezcles `localhost` y `127.0.0.1`; elige hosts coherentes y configura `SANCTUM_STATEFUL_DOMAINS`, las cookies de sesión y CORS según el proyecto. La SPA y la API deben compartir el mismo dominio principal para compartir cookies, aunque pueden tener puertos distintos.

## CORS, CSRF y Sanctum: no son lo mismo

| Concepto | Función |
|---|---|
| CORS | Controla cómo permite el navegador las solicitudes entre orígenes diferentes |
| CSRF | Protege frente a la falsificación de solicitudes autenticadas mediante cookies |
| Sanctum | Integra la autenticación de la SPA con las sesiones de Laravel |

## Ruta protegida

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

Route::middleware('auth:sanctum')->get('/nire-profila', function (Request $request) {
    return $request->user();
});
```

Para usar el ejemplo anterior, hay que importar `Illuminate\Http\Request`. En producción, al devolver los datos del usuario, selecciona los campos que se deben exponer.

!!! tip "Reto final"
    Muestra en Vue una lista pública de actividades; solo los usuarios autenticados podrán crear, modificar o eliminar actividades. Añade validación, comprobaciones de permisos y visualización de errores.

## Documentación oficial

- [Sanctum](https://laravel.com/docs/13.x/sanctum)
- [CSRF Protection](https://laravel.com/docs/13.x/csrf)
- [HTTP Client / configuración de Axios (en la sección de Sanctum)](https://laravel.com/docs/13.x/sanctum#spa-authentication)
