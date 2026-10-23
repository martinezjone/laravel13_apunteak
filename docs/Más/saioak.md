# SAIOAK

<https://laravel.com/docs/12.x/session>

HTTP eskaerak iheskorrak direnez eta arrastorik uzten ez dutenez, garrantzitsua da bi eskaeren artean informazioa memorizatzea ahalbidetuko dion sistema bat izatea. Horixe da, hain zuzen ere, saioen helburua.

Saioaren konfigurazioa config/session.php konfigurazio-fitxategian dago. .env fitxategian kontrolatzailearen definizioa eta informazioaren zenbat denboratan egongo den aurkituko ditugu:

```dotenv
SESSION_DRIVER=file

SESSION_LIFETIME=120
```

Besterik adierazi ezean, saioari buruzko informazioa memorizatzen duen fitxategia da (storage/framework/sessions ), baina honako hauek ere erabil ditzakezu: cookie-ak, datu-basea, taula (probetarako erabilia), memcached, redis…

Datuak gordetzea (put)

Erabilitako kontrolatzailea edozein dela ere, Laravelen saio-laguntzaileak saio-kudeaketa sinplifikatua ahalbidetzen du. Beraz, eskaeratik abiatuta, saio-aldagai bat sor dezakezu:

```php
$request->session()->put('clave' , 'valor');
```

Edo bere laguntzailea (helper bidez):

session ([ 'clave' = > 'valor' ]) ;

Datuak irakurtza (get)

Bere balioa jasotzeko aldiz:

```php
$valor = $request->session()->get('clave'); edo $valor = session ( 'clave' ) ;
```

Lehenetsiko balio bat ezarri daiteke:

```php
$valor = session ( 'clave' , 'balio lehenetsia' ) ;
```

Datu bat existitzen den jakiteko

Jakiteko aldagai zehatz bat saioan dagoen edo ez, horrela egingo genuke:

if (session()->has('error')) ...

Informazio hori bezero berarentzat gordetzen da kontsulta guztietan. Laravel arduratzen da informazio horretaz, gako-balio bikote bakarra ematen diogu eta guztiaz arduratzen da.

Saioen erabilpena

Hiru modutan sar gaitezke saioan gordetako datu batera:

\* Request injektatuz: Eskaera egiteko, beharrezkoa da gelak injektatzea:

```php
public function showProfile(Request $request, $id){

$value = $request->session()->get('key');

}
```

\* Session Facade

```php
$value = Session::get('key');
```

\* session() helper funtzioa

```php
$value = session('key');
```

Saio baten datu guztiak ikustea

Saio baten datu guztiak lortzeko:

```php
$data = $request->session()->all();

$data = Session::all(); //usando el Facade
```

Saioko datuak gordetzea

Saioko datuak bi modutan gorde ditzakegu:

```php
$request->session()->put('email', 'me@example.com');
```

session(['email' => 'me@example.com']); //usando el helper

Datuak ezabatzea

Saioko datuak ezabatu ditzakegu ere:

```php
$request->session()->forget('key'); //elementu bakarra

$request->session()->flush(); // datu guztiak
```

Session::flush(); 77datu guztiak facade

- Eskaera (request) batean bakarrik gordetzen ditu datuak.
- Datu zaharretarako (old)

Flash datuak (request 1 bakarrirako)

Flash datuak eskaera bakarrean irauten dute; oso erabilia da:

- formularioen errore-mezuetan
- with('success') edo with('error') mezuetan

Errore-mezuetarako

//flash datuak eskaera bakarrean irauten dute

```php
$request->session()->flash('mensaje', '¡El usuario fue eliminado!');

//Flash datuak eskaera 1 baino gehiago irauteko

$request->session()->reflash();

//Flash datu zehatz batzuk mantentzeko

$request->session()->keep(['mensaje', 'email']);
```

Kontuan hartu beharreko gauza garrantzitsuak

Hauek dira saioetarako oinarrizko metodoak, dokumentazioan aurkituko duzu informazio osagarri guztia: <https://laravel.com/docs/12.x/session>

Kontutan izateko hainbat gauza:

- Saioak konfiguratzeko parametroak config/session.php.-en definitzen dira.
- Garrantzitsua da saioaren biltegiratzea eta iraupena.
- Laravelekin ez dago zuzenean $\_SESSION aldagaia erabili beharrik.
- Ez ditugu erabili behar session\_start() edo session\_destroy() funtzioak
- Middleware-a beharrezkoa da saioak erabiltzeko:

  - Web middleware taldean dator automatikoki (routes/web.php).
  - API middlewarean ez dago, beraz API ruterik ez du saiorik erabiltzen lehenetsita.
