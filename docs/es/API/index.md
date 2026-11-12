# API con Laravel 13

## ¿Qué vamos a aprender?

En esta unidad usaremos Laravel como **backend**: en lugar de generar páginas HTML, enviaremos datos **JSON** a una aplicación Vue 3.

**Conocimientos previos:** rutas, controladores, Eloquent, migraciones y validación.

| Mediante Blade | Mediante API |
|---|---|
| El servidor genera HTML | El servidor envía JSON |
| `return view(...)` | `return response()->json(...)` o un Resource |
| El navegador muestra HTML | Vue recibe JSON y crea la interfaz |

!!! info "Itinerario de aprendizaje"
    Primero crearemos una API pública. Después, veremos el CRUD, el formato de los datos, los errores y las pruebas. Por último, añadiremos Vue y la autenticación.

## Índice de contenidos

1. [API REST y JSON](01-api-rest-json.md)
2. [Rutas de API](02-api-ibilbideak.md)
3. [Respuestas JSON](03-json-erantzunak.md)
4. [Controladores de API y CRUD](04-kontrolatzaileak-crud.md)
5. [API Resources](05-api-resources.md)
6. [Validación y errores](06-balidazioa-erroreak.md)
7. [Pruebas de API](07-api-probak.md)
8. [Laravel + Vue + Sanctum](08-laravel-vue-sanctum.md)

## Referencias oficiales

- [Documentación de Laravel 13](https://laravel.com/docs/13.x)
- [Routing](https://laravel.com/docs/13.x/routing)
- [Eloquent Resources](https://laravel.com/docs/13.x/eloquent-resources)
- [Sanctum](https://laravel.com/docs/13.x/sanctum)
