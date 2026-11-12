# Crear un proyecto

## Aplicación escolar

Crearemos una aplicación escolar con Laravel. En esta aplicación tendremos estudiantes, profesores y cursos, y gestionaremos estas tres tablas.

Lo primero que haremos será crear nuestro proyecto Laravel.

## Crear un proyecto Laravel

Para crear un proyecto Laravel, debemos seguir estos pasos:

1. Iniciaremos XAMPP o MAMP.
2. También abriremos la consola que queramos, Git Bash o la que prefiramos.
3. Crearemos un proyecto llamado "eskola". Para ello, primero debemos situarnos en la carpeta que queramos desde la consola.

$ cd c/xampp/htdocs/laravel\_proiektuak

```bash
composer create-project laravel/laravel eskola
```

Este proceso tardará un poco, ya que se están creando muchas cosas.

4. Comprobaremos que todo se haya creado correctamente. Para ello, encontraremos una carpeta llamada eskola en la ubicación indicada. La abriremos en Visual Studio y veremos que contiene muchas carpetas.

Por otra parte, al introducir nuestra URL en el navegador, en mi caso: <http://localhost/laravel2025-2026/eskola>

Encontraremos una página como esta:

![Página inicial de Laravel](../../assets/images/Proiektu%20bat%20sortu/e097dbca9101d55bd0f3905ab758e1f5.jpg)

## Crear un proyecto Laravel con Laravel Installer

```bash
composer global require laravel/installer
```

laravel new eskola
