# Kontroladoreak

Ibilbideak HTTP eskaera bat ekintza batekin lotzen du. Orain arte, ekintza hori `routes/web.php` fitxategian bertan idatzi dugu, funtzio anonimo baten barruan. **Kontroladoreek (*controllers*) erlazionatutako ekintzak klase berean antolatzeko aukera ematen digute.** Horrela, ibilbideak zein metodo deitu adierazten du, eta metodoak eskaera kudeatu eta erantzuna itzultzen du.

## Kontroladorea sortzea

Proiektuaren erroko direktorioan, exekutatu:

```bash
php artisan make:controller HomeController
```

Artisanek `app/Http/Controllers/HomeController.php` fitxategia sortuko du. Konbentzioz, kontroladorearen izena singularrean eta PascalCase formatuan idazten da, `Controller` atzizkiarekin. Adibidez, `HomeController`, `StudentController` eta `CategoryController`.

## Ibilbidetik kontroladorera

Gehitu `index()` metodoa `HomeController` klasean. Lehenengo proban, testu-mezu bat itzuliko dugu.

```php
<?php

namespace App\Http\Controllers;

class HomeController extends Controller
{
    public function index(): string
    {
        return 'Hau da nire blogaren hasierako orria. Ongi etorri!';
    }
}
```

`namespace App\Http\Controllers;` lerroak klasea zein izen-eremutan dagoen adierazten du. `extends Controller` adierazpenak `HomeController` klaseak oinarrizko `Controller` klasea heredatzen duela adierazten du. `public` hitzari esker, metodoa klasetik kanpo dei daiteke. `: string` adierazpenak metodoaren itzulera mota zehazten du.

`routes/web.php` fitxategian, gehitu `use App\Http\Controllers\HomeController;` lerroa gainerako inportazioekin batera. Gero, ordezkatu lehendik zegoen `GET /` ibilbidea honako honekin:

```php
Route::get('/', [HomeController::class, 'index']);
```

`use` lerroari esker, fitxategian `HomeController` izen laburra erabil dezakegu. Ibilbidearen bigarren argumentuko arrayak bi elementu ditu:

| Elementua | Zer adierazten du? |
| --- | --- |
| `HomeController::class` | Erabili beharreko kontroladorearen klasea. |
| `'index'` | Laravelek deituko duen metodo publikoa. |


## Ibilbideko parametroa kontroladorean jasotzea

Ordezkatu aurreko `/welcome/{name?}` ibilbideko funtzio anonimoa kontroladorearen metodoarekin. `routes/web.php` fitxategian:

```php
Route::get('/welcome/{name?}', [HomeController::class, 'welcome']);
```

Gehitu `welcome()` metodoa `HomeController` klasearen barruan:

```php
public function welcome(string $name = 'gonbidatua'): string
{
    return 'Kaixo, ' . e($name) . '!';
}
```

| Eskaera | `$name`-ren balioa | Erantzuna |
| --- | --- | --- |
| `GET /welcome/Jon` | `'Jon'` | `Kaixo, Jon!` |
| `GET /welcome` | `'gonbidatua'` | `Kaixo, gonbidatua!` |

`{name?}` adierazpenak URLaren segmentua aukerakoa dela adierazten du. Segmentua falta denean, metodoak `'gonbidatua'` balio lehenetsia erabiltzen du. `e()` laguntzaileak URLtik jasotako izena HTMLan testu gisa erakusteko prestatzen du.

## Kontroladoretik Blade bistara

Orain, `index()` metodoak hasierako orriaren izenburua eta mezua prestatuko ditu, eta Blade bistari pasatuko dizkio. Gehitu `use Illuminate\View\View;` kontroladorearen fitxategian, `namespace` lerroaren ondoren. Gero, ordezkatu `index()` metodoa:

```php
public function index(): View
{
    $title = 'Hasiera';
    $message = 'Hau da nire blogaren hasierako orria. Ongi etorri!';

    return view('home', [
        'title' => $title,
        'message' => $message,
    ]);
}
```

Sortu `resources/views/home.blade.php` fitxategia:

```blade
<h1>{{ $title }}</h1>
<p>{{ $message }}</p>
```

`view('home', ...)` deiak `home.blade.php` bista erabiltzen du. Arrayko `'title'` eta `'message'` gakoek Blade barruko `$title` eta `$message` aldagaien izenak zehazten dituzte. Blade-k `{{ ... }}` bidez erakusten dituen balioak automatikoki ihesten ditu HTMLrako.


## Ekintza bakarreko kontroladorea

Kontroladore bat ekintza bakarrerako diseinatu nahi dugunean, `__invoke()` metodoa erabil dezakegu. Adibidez, sortu `AboutController`:

```bash
php artisan make:controller AboutController --invokable
```

```php
<?php

namespace App\Http\Controllers;

class AboutController extends Controller
{
    public function __invoke(): string
    {
        return 'Blogari buruzko informazioa';
    }
}
```

Gehitu `use App\Http\Controllers\AboutController;` inportazioa `routes/web.php` fitxategiaren hasieran eta erregistratu ibilbidea:

```php
Route::get('/about', AboutController::class);
```

Kasu honetan, kontroladorearen klasea ematea nahikoa da. Laravelek `__invoke()` metodoari deituko dio. `/{number}` ibilbide orokorra aktibo badago, jarri `/about` haren aurretik.

## Baliabide-kontroladorea (*Resource controller*)

CRUD baterako, Artisanek ohiko ekintzen metodoak dituen kontroladorea sor dezake:

```bash
php artisan make:controller TeacherController --resource
```

Komandoak `index`, `create`, `store`, `show`, `edit`, `update` eta `destroy` metodoen egitura prestatzen du. Metodo bakoitzaren edukia guk idatziko dugu.

Ibilbideak erregistratzeko, gehitu `use App\Http\Controllers\TeacherController;` inportazioa eta deklarazio hau `routes/web.php` fitxategian:

```php
Route::resource('teachers', TeacherController::class);
```

`--resource` aukerak kontroladorearen metodoen egitura sortzen du, eta `Route::resource()` deiak ibilbideak erregistratzen ditu. Egiaztatu emaitza `php artisan route:list --path=teachers` komandoarekin. Ibilbide bakoitzaren HTTP metodoa, URLa eta ekintza [Rutak](rutak.md) atalean ikus ditzakezu.

[Laravel 13ren dokumentazio ofiziala · Controllers](https://laravel.com/framework/docs/13.x/controllers)
