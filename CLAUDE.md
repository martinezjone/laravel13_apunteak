# Laravel 13 apunteak

Apuntes de clase de Laravel 13 para DWS (Desarrollo Web en entorno Servidor) en MkDocs Material. Curso 2026-2027.
El resto de apuntes de DWS (PHP) están en el repo `dws_apunteak`.

## Forma de trabajar
- Trabajo desde dos ordenadores (casa y trabajo). Al empezar: `git pull`. Al terminar: commit + `git push`. Nunca dejar cambios sin subir.
- Contenido bilingüe con `mkdocs-static-i18n`: `docs/eu/` (idioma por defecto) y `docs/es/`. Cada cambio de contenido se hace en las dos versiones.
- Mensajes de commit en euskera, cortos (p. ej. "CRUD 2 eu eta Es").

## Estructura y herramientas
- `site/` es HTML generado y se commitea: no editar a mano, regenerar con `mkdocs build` antes de subir.
- Previsualizar con `mkdocs serve` (`pip install -r requirements.txt`).
