# Crear un proyecto

## Aplicación de prueba

Para empezar, crearemos una aplicación de prueba.

## Crear un proyecto Laravel

Para crear un proyecto Laravel, debemos seguir estos pasos:

1. Iniciaremos MAMP para poner en marcha el servidor.
2. También abriremos la consola que queramos, Git Bash o la que prefiramos.
3. Crearemos un proyecto llamado "froga". Para ello, primero debemos situarnos en la carpeta que queramos desde la consola.

$ cd c/xampp/htdocs/laravel\_proiektuak

```bash
composer create-project laravel/laravel froga
```

Este proceso tardará un poco, ya que se están creando muchas cosas.

4. Comprobaremos que todo se haya creado correctamente. Para ello, encontraremos una carpeta llamada froga en la ubicación indicada. La abriremos en Visual Studio y veremos que contiene muchas carpetas.

Por otra parte, al introducir nuestra URL en el navegador, en mi caso: <http://localhost/2025-2026/laravel/froga/>

Encontraremos una página como esta:

![Página inicial de Laravel](../../assets/images/Laravel%20proiektua%20sortu/e097dbca9101d55bd0f3905ab758e1f5.jpg)

## Crear un proyecto Laravel con Laravel Installer

```bash
composer global require laravel/installer
```

laravel new eskola
