# Enviar mensajes

Laravel ofrece la posibilidad de enviar mensajes al invocar una ruta. Esto resulta muy útil para saber si se ha cumplido lo que hemos indicado.

Podemos verlo con un ejemplo. En nuestro ejercicio Aula, al eliminar un "Profesor", debemos comprobar si ese profesor tiene cursos, porque si los tiene no permitiremos eliminarlo.

Para hacerlo así, primero haremos la comprobación en el controlador:

```php
public function destroy(Profesor $profesor){

$cursosDelProfesor = $profesor->cursos;

// Ikastaroak egotekotan ezingo dugu ezabatu
```

if ($cursosDelProfesor->isNotEmpty()) {

```php
return redirect()->route('profesores.index')

->with('error', 'Irakasleak ikastaroak ditu eta ezin da ezabatu.');

}

// Ikastarorik ez badago, irakaslea ezaba daiteke

$profesor->delete();

return redirect()->route('profesores.index')

->with('success', 'Irakaslea ongi ezabatu da');

}
```

Como vemos en el ejemplo, invocaremos el método with pasando "error" o "success".

Una vez hecho esto, en nuestra vista indicaremos que escriba uno u otro si corresponde:

```php
@if(session('error'))

<div class="alert alert-danger">

{{ session('error') }}

</div>

@endif

@if(session('success'))

<div class="alert alert-success">

{{ session('success') }}

</div>

@endif
```

Como vemos, el método with guarda las variables error y success en una sesión. En este caso se han utilizado esas, que son las más habituales, pero en realidad podríamos pasar cualquier variable.
