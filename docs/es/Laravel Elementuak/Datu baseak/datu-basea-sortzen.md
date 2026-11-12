# Crear la base de datos

Laravel 13 crea por defecto una base de datos SQLite al crear el proyecto. Si queremos trabajar con ella, podemos hacerlo directamente.

Sin embargo, Laravel puede trabajar con varios tipos de bases de datos; la documentación indica con cuáles:

<https://laravel.com/framework/docs/13.x/database>

En nuestro caso trabajaremos con MySQL.

Hay un archivo, config>database.php, donde Laravel buscará la información sobre las bases de datos; por tanto, ese sería el lugar para indicar qué base de datos vamos a utilizar. Sin embargo, ese archivo ofrece poca seguridad, porque, por ejemplo, al subir nuestro proyecto a GitHub, toda la información de la carpeta config quedaría visible.

Laravel tiene un método llamado env; este comprueba si existe la variable DB\_CONNECTION en el archivo ".env". Si existe, toma ese valor; de lo contrario, utiliza el valor predeterminado, es decir, 'mysql'.

¿Por qué es más seguro guardar esto en el archivo .env que en database.php?

Porque Laravel está configurado para no subir el archivo .env cuando se publica el proyecto.

Como vemos, Laravel ya nos ayuda a identificar los parámetros que necesitamos. Como hemos visto en database.php, la variable que tendrá en cuenta es DB\_CONNECTION:

![](../../../assets/images/Datu%20basea%20sortzen/28969677a32b8740f85e5811279d0c50.jpg)

Pero como trabajaremos con una base de datos MySQL, descomentaremos todas estas variables e introduciremos los datos de nuestra base de datos.

Para ello, primero crearemos nuestra base de datos en PhpMyAdmin; en el siguiente ejemplo la llamaremos blog.

Ten en cuenta que, si utilizas XAMPP, la contraseña debe quedar vacía y, si utilizas MAMP y no has cambiado nada, será root.

```php
DB_CONNECTION=mysql

DB_HOST=127.0.0.1

DB_PORT=3306

DB_DATABASE=blog

DB_USERNAME=root

DB_PASSWORD=root
```

¡Cuidado! Al crear la base de datos, hay que indicar qué "cotejamiento" queremos. Por defecto, phpMyAdmin establece utf8mb4\_unicode\_ci, el mismo que utiliza Laravel. Si lo cambiamos, también tendremos que indicarlo en nuestro archivo .env.

```php
'collation' => env('DB\_COLLATION', 'utf8mb4\_unicode\_ci'),
```

Siguiente: Migraciones
