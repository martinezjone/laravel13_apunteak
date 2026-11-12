# Subir una imagen

## Añadir un campo nuevo (en la tabla ikasles)

Para subir la foto del alumno utilizaremos el ejemplo que hemos desarrollado hasta ahora. Para ello, modificaremos la tabla pupils de nuestra base de datos añadiendo un nuevo campo llamado "photo".

Recuerda que para hacerlo hay que crear una nueva migración. Esta vez también utilizaremos las convenciones de Laravel: igual que para crear una tabla utilizábamos create\_xxx\_table, para añadir columnas nuevas utilizaremos este formato:

```bash
php artisan make:migration add_photo_to_pupils_table
```

Como vemos, la migración está lista para añadir el campo correspondiente. Lo añadiremos en el método up() y lo eliminaremos en el método down().

```php
$table->string('photo')->nullable()->after('address');
```

Con esto indicamos que se cree un campo nuevo llamado "photo"; indicamos nullable() porque puede ser null (para que los datos anteriores no den error) y after('address') para añadirlo después del campo address y antes de timestamps.

En el método down() especificamos la acción inversa; en este caso, eliminamos la columna que añadimos en el método up():

```php
$table->dropColumn('photo');
```

![](../../../assets/images/Irudi%20bat%20igo/e5469230605c1f9ba765a0cf3d64f7ff.jpg)

## Almacenamiento de archivos

Documentación de Laravel: <https://laravel.com/framework/docs/13.x/filesystem#main-content>

La integración Flysystem proporciona a Laravel muchos controladores para trabajar con archivos. Storage nos permitirá trabajar con archivos.

La configuración se realiza en el archivo config/filesystems.php. Aquí encontraremos distintos discos, lo que nos permite trabajar con varios sistemas diferentes.

Cada disco representa un controlador de almacenamiento y una ubicación de almacenamiento específicos.

En nuestro archivo se indica que, de forma predeterminada, se utilizará el disco local, pero lo cambiaremos a public:

'default' => env('FILESYSTEM\_DISK', 'public'),

Ahora tendremos que saber dónde guardar nuestras imágenes. Al principio podríamos guardarlas en cualquier sitio, pero Laravel ya tiene preparada la carpeta "storage" para guardar nuestros archivos, así que también las guardaremos ahí, dentro de la carpeta storage/app/public.

Pero esto plantea un problema, porque, como sabemos, el navegador solo puede leer la carpeta public. Para solucionarlo, podemos crear un "enlace simbólico" a la carpeta storage dentro de nuestra carpeta public. Podemos hacerlo desde el terminal.

```bash
php artisan storage:link
```

Así se enlazará public/storage → storage/app/public.

## Subir una imagen (vista y controlador) - Storage

Primero daremos la opción de subir un archivo en la vista "pupils.create":

Foto: <input type="file" name="photo" id="photo" accept="image/\*">

Recuerda que en la etiqueta form siempre tendremos que incluir enctype="multipart/form-data".

Como habrás observado, aquí hemos indicado con accept que solo se pueden subir imágenes, pero esto no es suficiente; también tendremos que validar en el controlador. En nuestro caso lo haremos en PupilController.php:

```php
$request->validate([
```

'name' => 'required|max:75',

'age' => 'required|integer',

'phone' => 'required|max:35',

'address' => 'required|max:75',

]);

Al escribir ambas cosas nos aseguraremos de que en nuestra base de datos solo se suban imágenes.

Para poder utilizar Storage Facade, recuerda que hay que añadir: use Illuminate\Support\Facades\Storage;

En la vista también utilizaremos el método url() para ver nuestra imagen:

```php
<img src="{{ url('storage/' . $pupil->photo)}}" alt="" width="80px" height="80px"/>
```

## Subir una imagen (controlador) - move

```php
public function store(StorePupil $request){
```

$new\_pupil = $request->validated();

// Irudia igoko dugu

if ($request->hasFile('photo')) {

```php
$file = $request->file('photo');

$destinationPath = public_path('storage/pupils');

$filename = $file->getClientOriginalName();

$file->move($destinationPath, $filename);
```

$new\_pupil['photo'] = 'pupils/' . $filename; // Bide erlatiboa gordetzen dugu

}

Pupil::create($new\_pupil);

```php
return redirect()->route('pupils.index')->with('success', 'Ikaslea ondo sortu da.');

}
```
