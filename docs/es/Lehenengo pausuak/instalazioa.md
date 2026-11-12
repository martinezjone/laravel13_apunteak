# Guía para empezar con Laravel 13 · Windows

En esta guía prepararás tu ordenador y abrirás tu primera aplicación Laravel 13 en el navegador. Todos los comandos deben ejecutarse en el terminal **PowerShell**. En VS Code, abre el terminal con **Terminal → New Terminal**.

En cada paso, comprueba qué tienes instalado. Si te falta una herramienta o tienes una versión demasiado antigua, instálala o actualízala y repite la comprobación antes de pasar al siguiente paso.

## 1. Comprueba PHP

```powershell
php -v
where.exe php
php --ini
```

| Resultado | Qué hacer |
| --- | --- |
| Tienes PHP 8.3, 8.4 o 8.5 | Cumples la versión mínima de Laravel 13. Comprueba las extensiones de PHP. |
| Tienes PHP 8.2 o anterior | Si MAMP, XAMPP o Laragon ya incluye una versión compatible de PHP, coloca la carpeta que contiene su archivo `php.exe` antes que la de la versión antigua en la variable `Path`. Si no, instala una versión compatible de PHP. |
| PowerShell no reconoce `php` | Si PHP ya está instalado, añade a la variable `Path` la carpeta donde está `php.exe`. Si no, utiliza una de las opciones de instalación que aparecen abajo. |
| Aparecen varias rutas | El comando `where.exe php` muestra los ejecutables encontrados. Usa `Get-Command php` para ver cuál selecciona PowerShell y `php -v` para comprobar su versión. |

Para cambiar `Path`, busca en el menú Inicio de Windows la opción para editar las variables de entorno de la cuenta. Edita **Path**, añade la carpeta de PHP y, si es necesario, cambia el orden de las entradas. Cierra y vuelve a abrir PowerShell. Si utilizas el terminal de VS Code, reinicia también VS Code. Después, repite las comprobaciones.

**Si necesitas instalar PHP**, elige una opción:

- **Laravel Herd para Windows.** Descarga y ejecuta el instalador gráfico. Incluye PHP, Composer y el instalador de Laravel. Al terminar, abre un terminal nuevo y repite las comprobaciones. [Documentación de instalación de Herd](https://herd.laravel.com/docs/windows/getting-started/installation)
- **Instalación oficial rápida.** En la documentación de Laravel 13, selecciona **Windows PowerShell** en la sección de instalación de PHP y Composer, y ejecuta el comando que aparece allí. Instala PHP 8.5, Composer y el instalador de Laravel. Si ya los habías instalado mediante `php.new`, puedes volver a ejecutar el mismo comando para actualizar los tres. [Documentación de instalación de Laravel 13](https://laravel.com/framework/docs/13.x/installation#installing-php)

### Comprueba las extensiones de PHP

```powershell
php -m
```

Laravel 13 necesita estas extensiones: Ctype, cURL, DOM, Fileinfo, Filter, Hash, Mbstring, OpenSSL, PCRE, PDO, Session, Tokenizer y XML. Para el proyecto de esta guía, comprueba también que aparezca **pdo_sqlite**. Esta extensión permite que PHP utilice una base de datos SQLite.

Si falta alguna extensión, usa `php --ini` para ver qué archivo `php.ini` carga PHP. Si la extensión está disponible en tu instalación, actívala en ese archivo. Por ejemplo, si encuentras la línea `;extension=pdo_sqlite`, quita el punto y coma inicial. Si la extensión no está incluida en la instalación, instálala según las instrucciones de tu distribución de PHP. Repite `php -m`. [Requisitos de PHP para Laravel 13](https://laravel.com/framework/docs/13.x/deployment#server-requirements)

## 2. Comprueba Composer

Composer instala las dependencias PHP del proyecto.

```powershell
composer --version
```

- **Si tienes Composer 2.x**, pasa al paso 3.
- **Si tienes Composer 1.x** y lo instalaste como PHAR, ejecuta `composer self-update --2` y vuelve a comprobar la versión. Si otra herramienta gestiona la instalación, úsala para actualizarlo.
- **Si el comando no funciona**, abre la documentación de Composer para Windows, descarga **Composer-Setup.exe** y ejecútalo. Si te pide la ruta de PHP, selecciona el `php.exe` compatible del paso 1. Abre un terminal nuevo y repite la comprobación.

[Instalar Composer en Windows](https://getcomposer.org/doc/00-intro.md#installation-windows) · [Actualizar Composer](https://getcomposer.org/download/#download-channels)

## 3. Comprueba Node.js y npm

Node.js ejecuta las herramientas del frontend. npm instala sus dependencias y Vite prepara los archivos CSS y JavaScript.

```powershell
node -v
npm -v
```

- **Para las clases, utiliza Node.js 24 LTS.** Si ya tienes la versión 22.12 o posterior de la rama 22, cumples el requisito de Vite. La documentación de Vite también admite versiones a partir de la 20.19 de la rama 20.
- **Si no tienes Node.js o tu versión es demasiado antigua**, instala Node.js 24 LTS con el instalador para Windows. Si gestionas Node.js mediante Herd o un administrador de versiones, instálalo y selecciónalo desde esa herramienta. Abre un terminal nuevo y vuelve a ejecutar ambos comandos.
- **Si PowerShell bloquea `npm.ps1`**, prueba `npm.cmd -v`. Si funciona, utiliza `npm.cmd` en los comandos npm de esta guía. Si tienes varias instalaciones, puedes ver sus rutas con `where.exe node` y `where.exe npm`.

[Descargar Node.js](https://nodejs.org/en/download) · [Versiones de Node.js compatibles con Vite](https://vite.dev/guide/#scaffolding-your-first-vite-project)

## 4. Instala o actualiza el instalador de Laravel

```powershell
laravel --version
```

El comando muestra la **versión del instalador**. Comprobarás la **versión del framework** dentro de la carpeta del proyecto, en el paso 5.

- Si el comando no existe y ya tienes PHP y Composer instalados, ejecuta `composer global require laravel/installer`.
- Si lo instalaste mediante Composer y quieres actualizarlo, ejecuta `composer global update laravel/installer`.
- Si Composer indica que está instalado pero PowerShell no encuentra el comando, ejecuta `composer global config bin-dir --absolute`. Añade a la variable `Path` la carpeta que muestra y abre otro terminal. Si lo instalaste mediante Herd o `php.new`, utiliza el mismo método de instalación para actualizarlo.

[Instalador oficial de Laravel](https://laravel.com/framework/docs/13.x/installation#installing-php)

## 5. Crea tu primer proyecto

Abre PowerShell en la carpeta donde guardas tus ejercicios. Si utilizas VS Code, abre esa carpeta y crea un terminal. Ejecuta:

```powershell
laravel new lehen-laravel
```

Si el instalador te pregunta, elige **sin starter kit** y selecciona **SQLite**. Para las pruebas, puedes aceptar la opción predeterminada del framework. SQLite guarda los datos en un archivo, por lo que no necesitas iniciar un servidor de bases de datos para este ejercicio.

```powershell
cd lehen-laravel
php artisan --version
composer check-platform-reqs
php artisan migrate:status
```

Comprueba que Artisan muestra **Laravel Framework 13.x**, que Composer valida los requisitos de PHP y sus extensiones, y que aparecen las migraciones de SQLite. Durante la instalación, Laravel crea el archivo `database/database.sqlite` y ejecuta las migraciones iniciales. Si queda alguna migración pendiente, ejecuta `php artisan migrate` y vuelve a comprobar el estado.

**Si necesitas seleccionar Laravel 13 específicamente**, crea el proyecto mediante Composer. Ejecuta el comando desde la carpeta principal de los ejercicios y utiliza un nombre de carpeta que esté libre. Después, entra en el nuevo proyecto y repite las comprobaciones anteriores.

```powershell
composer create-project laravel/laravel lehen-laravel-13 "^13.0"
cd lehen-laravel-13
php artisan --version
```

[Crear un proyecto](https://laravel.com/framework/docs/13.x/installation#creating-an-application) · [SQLite y migraciones](https://laravel.com/framework/docs/13.x/installation#databases-and-migrations) · [Composer create-project](https://getcomposer.org/doc/03-cli.md#create-project)

## 6. Inicia Laravel y abre el navegador

En la carpeta del proyecto, donde están los archivos `artisan`, `composer.json` y `package.json`, ejecuta estos comandos uno por uno. Espera a que termine cada uno antes de ejecutar el siguiente.

```powershell
npm install
npm run build
composer run dev
```

El comando `composer run dev` inicia el servidor de desarrollo de Laravel, el proceso que gestiona las tareas de la cola y Vite. Deja abierto el terminal y ve a [http://localhost:8000](http://localhost:8000). Si ves la página de bienvenida de Laravel, la instalación está lista. Para detener el servidor, pulsa **Ctrl+C**. En la siguiente sesión, vuelve a la misma carpeta y ejecuta `composer run dev`.

| Problema | Qué comprobar |
| --- | --- |
| `Could not open input file: artisan` | Comprueba que estás en la carpeta del proyecto. Ejecuta `Get-Location` y `Get-ChildItem artisan`. |
| `could not find driver` al usar SQLite | Comprueba que PHP en el terminal tiene `pdo_sqlite` activado. Usa `php --ini` y `php -m`. |
| `EBADENGINE` al instalar dependencias | Comprueba el resultado de `node -v` y selecciona una versión compatible con el rango indicado por npm. |
| Error relacionado con el manifiesto de Vite | Ejecuta `npm install` y `npm run build` en la carpeta del proyecto. Si la compilación falla, corrige el error que muestra npm. |
| El puerto 8000 está en uso | Detén el servidor que está utilizando ese puerto. Si los recursos ya están compilados, también puedes ejecutar `php artisan serve --port=8001`. Después, abre [http://localhost:8001](http://localhost:8001). |

Como utilizas el servidor de desarrollo de Laravel, también puedes guardar el proyecto fuera de la carpeta `htdocs`. [Documentación para iniciar la aplicación](https://laravel.com/framework/docs/13.x/installation#creating-an-application)
