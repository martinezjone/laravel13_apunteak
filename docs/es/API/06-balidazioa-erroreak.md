# 6. Validación y errores

## Validar en el servidor

La validación en Vue es útil para el usuario, pero **la validación en Laravel es imprescindible**.

```php
$datuak = $request->validate([
    'izena' => ['required', 'string', 'max:255'],
    'plazak' => ['required', 'integer', 'min:0'],
]);
```

Cuando la solicitud espera una respuesta JSON, si la validación falla, Laravel normalmente devuelve el código **422** y un objeto con los errores:

```json
{
  "message": "The izena field is required.",
  "errors": {
    "izena": ["The izena field is required."]
  }
}
```

El idioma de los mensajes depende de la localización de la aplicación.

## Error 404

Si el registro no existe en un enlace de modelo de ruta como `show(Jarduera $jarduera)`, Laravel genera una respuesta 404.

## 401 y 403

- **401 Unauthorized**: falta la autenticación o no es válida.
- **403 Forbidden**: el usuario puede estar identificado, pero no tiene permiso para realizar la acción.

## Solicitar JSON

Al probar la API, envía:

```http
Accept: application/json
Content-Type: application/json
```

La cabecera `Content-Type` es necesaria cuando se envía un cuerpo JSON; no es obligatoria en las solicitudes GET normales.

!!! warning "Seguridad"
    No muestres trazas de excepciones ni datos de configuración en producción. Configura `APP_DEBUG=false` en el entorno de producción.

!!! tip "Ejercicio"
    Envía `plazak: -3` y comprueba la respuesta 422. Después, solicita un ID que no exista y comprueba la respuesta 404.
