# Ejemplo escolar

## Mostrar todos los registros de estudiantes

Para mostrar la lista de nuestros estudiantes, debemos seguir estos pasos:

- Crear una ruta
- Crear el controlador PupilController
- Crear en el controlador el método necesario y buscar con el método all() todos los estudiantes que hay en la base de datos
- Crear una vista para mostrar los estudiantes encontrados

1. La ruta sería la siguiente:

//Para ver todos los estudiantes

```php
Route::get('/pupils', [PupilController::class, 'index'])->name('pupils.index');
```

2. Crear PupilController
3. En PupilController empezaremos a crear los métodos necesarios. Por ahora, crearemos el método index() para obtener todos los estudiantes:

```php
public function index(){
    $pupils= Pupil::all();

    return view('pupils.index',compact('pupils'));
}
```

4. Ahora debemos crear la vista. Con un foreach buscaremos todos los datos de la variable $pupils.

En Laravel, hay un par de convenciones más para crear vistas: por un lado, se suele dar al archivo el mismo nombre que al método que lo invoca y, por otro, todas las vistas que invoca un controlador se crean en una subcarpeta con el mismo nombre que el controlador; en este caso, será la carpeta "pupils".

```php
@foreach($pupils as $pupil)
    <label><strong>Izen abizenak:</strong> {{ $pupil->name}}</label><br>

    <label><strong>Adina:</strong> {{ $pupil->age}}</label><br>

    <label><strong>Telefonoa:</strong> {{ $pupil->phone}}</label><br>

    <label><strong>Helbidea:</strong> {{ $pupil->address}}</label>
@endforeach
```

5. En este caso hemos mostrado todos nuestros registros usando el método all(). Sin embargo, tenemos varios métodos que podemos utilizar en nuestras consultas. Para ver cómo crear consultas, podéis consultar la documentación de Laravel: [Query builder](https://laravel.com/framework/docs/13.x/queries).
6. Algunos ejemplos:

```php

// Izenetik ordenatu
$pupils = Pupil::orderBy('name')->get();

// Ikasle guztiak hartzen ditu baina bakarrik izena eta adina
$pupils = Pupil::select('name', 'age')->get();

// Where bat erabiliz
$pupils = Pupil::where('address', 'Bilbao')->get();

// Where konbinatuak: adina > 18 edo helbidea = 'Bilbao'
$pupils = Pupil::where('age', '>', 18)->orWhere('address', '=', 'Bilbao')->get();

// Name like '%Jon%'
$pupils = Pupil::where('name', 'like', '%Jon%')->get();

// Adina > 20 guztiak
$pupils = Pupil::where('age', '>', 15)->get();

// Adina > 20, lehenengoa bakarrik
$pupil = Pupil::where('age', '>', 20)->first();

// Zenbat erregistro dauden
$pupilCount = Pupil::count();

// Lehenengo 10 ikasleak
$pupils = Pupil::limit(10)->get();

// Adin maximoa
$maxAge = Pupil::max('age');

// Adin minimoa
$minAge = Pupil::min('age');

// Adin batazbestekoa
$avgAge = Pupil::avg('age');

// Adinen batura
$totalAge = Pupil::sum('age');
```

## Mostrar un registro

- Crear una ruta
- Crear en el controlador el método necesario y obtener el estudiante de la base de datos con el método find()
- Crear una vista para mostrar el estudiante encontrado
- En la vista anterior, añadir a cada estudiante un enlace para ver el estudiante concreto

Para mostrar un solo registro, usaremos el método show():

1. Empezaremos abriendo el controlador PupilController. Dentro del método show(), inicializaremos una variable que obtendrá un objeto del tipo $alumno. Pasaremos el parámetro $alumno al método find() para recuperar un registro concreto y, por último, mostraremos los datos del estudiante recuperados.

```php
public function show(Pupil $pupil){

    return view('pupils.show', compact('pupil'));

}
```

2. La ruta sería la siguiente:

```php
Route::get('pupils/{pupil}', [PupilController::class, 'show'])->name('pupils.show');
```

3. Vista: show.blade.php

```php
<label><strong>Izen abizenak:</strong> {{ $pupil->name}}</label><br>

<label><strong>Adina:</strong> {{ $pupil->age}}</label><br>

<label><strong>Telefonoa:</strong> {{ $pupil->phone}}</label><br>

<label><strong>Helbidea:</strong> {{ $pupil->address}}</label>
```

4. Añadir un enlace en la vista anterior:

```php
<a href="{{ route('pupils.show', $pupil->id) }}">{{ $pupil->name }}</a>
```

## Crear un nuevo estudiante: métodos create y store

- Crear una ruta
- Crear los métodos necesarios en el controlador y crear el estudiante en la base de datos usando los métodos create() y store()
- Crear una vista con el formulario para crear el estudiante
- Crear un menú con las opciones de índice y crear estudiante; para ello usaremos layouts

Esta vez crearemos un nuevo estudiante en nuestra base de datos. Para ello se usan dos métodos: create() y store(); con el primero crearemos el formulario y con el segundo insertaremos los datos en nuestra base de datos.

1. Lo primero será crear una vista nueva llamada "create" y, a continuación, crear su ruta.
2. Crearemos la vista en la carpeta "alumnos" con el nombre "create.blade.php".
3. Este será el código de la vista que contendrá el formulario:

```html
<form action="{{ route('pupils.store') }}" method="POST">

    @csrf

    Izen Abizena <input type="text" name="izena" value=""><br>
    Adina <input type="text" name="adina" value=""><br>
    Telefonoa <input type="text" name="telefonoa" value=""><br>
    Helbidea<input type="text" name="helbidea"  value=""><br>

    <button type="submit">Bidali</button>

</form>
```

4. Fijaos en que Laravel nos pide incluir el token @csrf dentro del formulario. Esto se hace por seguridad y se usa para evitar ataques. Crearemos un token de este tipo para cada formulario. Laravel exige que este token esté presente.
5. Rutas. Necesitaremos dos: una GET para acceder al formulario y otra con el método POST para guardar en la base de datos los datos del formulario:

```php
Route::get('pupils/create',[PupilController::class,'create'])->name('pupils.create');

Route::post('pupils/store',[PupilController::class,'store'])->name('pupils.store');
```

6. PupilController:

El método create() mostrará el formulario que necesitamos para crear un nuevo estudiante:

```php
public function create(){

    return view('pupils.create');

}
```

El método store() guardará en la base de datos la información enviada por el formulario:

```php
public function store(Request $request){
    Pupil::create([
    'name' => $request->name,
    'age' => $request->age,
    'phone' => $request->phone,
    'address' => $request->address,
    ]);

return redirect()->route('pupils.all');

}
```

7. También pondremos en la vista index un enlace para crear un nuevo estudiante:

```php
<a href="{{route('pupils.create')}}">Ikasle berria</a>
```

## Modificar los datos de un estudiante: métodos edit y update

- Crear una ruta
- Crear los métodos necesarios en el controlador y modificar los datos del estudiante en la base de datos usando los métodos edit() y update()
- Crear una vista con el formulario para modificar el estudiante
- Añadir un enlace para modificar el estudiante en las opciones index y show

1. Primero buscaremos en la función edit() el estudiante que queremos modificar; para ello usaremos el método find($id). Cargaremos los datos en la vista edit. Este formulario llamará a la función update, que modificará los datos en la base de datos.

PupilController

```php
public function edit(Pupil $pupil){
    return view('pupils.edit', compact('pupil'));
}
```

Como podéis ver, ahora, en lugar de recibir una variable corriente, hemos puesto Alumno delante. Al escribirlo así, Laravel entiende que $alumno es una instancia de la clase Alumno.

2. Necesitaremos una ruta que invoque este método:

```php
Route::get('pupils/edit/{pupil}',[PupilController::class,'edit'])->name('pupils.edit');
```

3. También crearemos en una vista el formulario necesario para modificar los datos del estudiante. En este formulario tendremos un par de particularidades: por un lado, esta vez mostraremos en los valores los datos que ya tiene ese estudiante y, por otro, tendremos que cambiar el método. Al realizar actualizaciones, Laravel prefiere que la ruta use el método PUT en lugar de POST, así que también debemos indicarlo en el formulario:

```php
<form action="{{ route('pupils.update', $pupil->id) }}" method="POST">
    @csrf

    @method('put')
    <input type="text" id="izena" name="izena" value="{{$pupil->name}}">
    <input type="number" id="adina" name="adina" value="{{$pupil->age}}">
    <input type="tel" id="telefonoa" name="telefonoa" value="{{$pupil->phone}}">
    <textarea id="helbidea" name="helbidea" required>{{$pupil->address}}</textarea>
    <button type="submit">Gorde</button>
</form>
```

4. La ruta para la actualización sería así, usando el método PUT:

```php
Route::put('pupils/update/{pupil}',[PupilController::class,'update'])->name('pupils.update');
```

5. En el controlador también prepararemos la función update, que modificará la base de datos con los datos enviados. Es decir, la vista edit llamará a la siguiente función; en este caso, el método recibirá tanto los datos enviados por el formulario como el objeto que tenemos que modificar.

```php
public function update(Request $request, Pupil $pupil) {

    $pupil->update([
    'name' => $request->name,
    'age' => $request->age,
    'phone' => $request->phone,
    'address' => $request->address,
    ]);

    return redirect()->route('pupils.all');
}
```

## Eliminar un estudiante: método destroy()

- Crear una ruta
- Crear en el controlador el método necesario, que será destroy()
- Añadir un enlace para eliminar al estudiante en las opciones index y show

1. Crear la ruta:

```php
Route::delete('pupils/{pupil}',[PupilController::class,'destroy'])->name('pupils.destroy');
```

Como vemos, esta ruta invoca el método delete, que es el método que Laravel usa para eliminar.

2. Dentro de PupilController crearemos el método para eliminar un registro; será destroy(), y dentro de este llamaremos al método delete().

```php
public function destroy(Pupil $pupil){
    $pupil->delete();
    return redirect()->route('pupils.all');
}
```

3. Enlace para eliminar al estudiante. Como hemos visto al crear la ruta, Laravel usa el método delete para eliminar un registro de la base de datos. Por tanto, al llamar a esa ruta debemos indicar en HTML que vamos a usar ese método. Solo podemos hacerlo dentro de un formulario, así que siempre debemos llamar a una ruta destroy mediante un formulario.

```php
<form action="{{ route('pupils.destroy', $pupil->id) }}" method="POST" class="inline-block">
    @csrf

    @method('DELETE')

    <button type="submit">Delete</button>
</form>
```

4. Como vemos, esto elimina directamente el registro.

## Eliminar un estudiante: pedir confirmación antes de eliminar

Sin embargo, normalmente sabemos que, antes de eliminar algo, debemos dar la opción de confirmar que queremos eliminar ese registro. Entonces, ¿cómo lo haríamos?

- Crear una nueva ruta
- Llamar a la vista de confirmación desde el controlador
- Crear la vista de confirmación

1. Crear la ruta:

```php
Route::get('pupils/delete/{pupil}',[PupilController::class,'delete'])->name('pupils.delete');
```

2. Dentro de PupilController crearemos un método para confirmar la eliminación de un registro.

```php
public function delete(Pupil $pupil){

    return view('pupils.delete', compact('pupil'));

}
```

3. confirm.blade.php - Crearemos una vista con un formulario que tendrá un botón para eliminar y también un enlace para cancelar.

¿Seguro que quieres eliminarlo?

```php
<form action="{{ route('pupils.destroy', $pupil->id) }}" method="POST" class="inline-block">
    @csrf

    @method('DELETE')
    <button type="submit">Delete</button>
</form>
```

## Rutas resource

Si seguimos todas las convenciones, podemos resumir todas estas rutas en una sola:

```php
Route::resource('pupils',PupilController::class);
```

Para ver todas las rutas creadas por el proyecto, podemos listarlas mediante Artisan:

```bash
php artisan route:list
```

Si queremos crear más de un resource, podemos pasar a nuestra ruta resource un array con tantos elementos como queramos.

```php
Route::resources([
    'photos' => PhotoController::class,
    'posts' => PostController::class,
]);
```

Documentación oficial sobre este tema: <https://laravel.com/framework/docs/13.x/controllers#resource-controllers>
