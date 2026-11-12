# 1. API REST y JSON

## ¿Qué es una API?

Una **API** permite la comunicación entre aplicaciones. En nuestro caso, Laravel ofrece los datos y Vue se los muestra al usuario.

```text
Vue (nabigatzailea)  -- HTTP eskaera -->  Laravel API
Vue (nabigatzailea)  <-- JSON erantzuna --  Laravel API
```

## Formato JSON

JSON es un formato de texto. Los objetos se representan entre `{}` y las listas entre `[]`.

```json
{
  "id": 1,
  "izena": "Dantza tailerra",
  "plazak": 20,
  "aktiboa": true
}
```

!!! warning "Ten en cuenta"
    En JSON, las claves y los textos se escriben entre comillas dobles. `true`, `false`, `null` y los números no se escriben entre comillas.

## Métodos HTTP

| Método | Objetivo | Ejemplo |
|---|---|---|
| GET | Leer recursos | `GET /api/jarduerak` |
| POST | Crear un recurso nuevo | `POST /api/jarduerak` |
| GET | Leer un recurso | `GET /api/jarduerak/1` |
| PUT/PATCH | Actualizar el recurso | `PUT /api/jarduerak/1` |
| DELETE | Eliminar el recurso | `DELETE /api/jarduerak/1` |

## Códigos de estado habituales

| Código | Significado |
|---|---|
| 200 | La solicitud se ha completado correctamente |
| 201 | Se ha creado el recurso |
| 204 | La respuesta no tiene contenido |
| 401 | El usuario no está autenticado |
| 403 | El usuario no tiene permiso |
| 404 | No se ha encontrado el recurso |
| 422 | La validación ha fallado |
| 500 | Error del servidor |

!!! tip "Ejercicio"
    Piensa en una aplicación para gestionar actividades: ¿qué método HTTP utilizarías para ver todas las actividades, crear una nueva y eliminar una?
