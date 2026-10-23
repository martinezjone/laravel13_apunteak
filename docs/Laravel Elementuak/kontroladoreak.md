# Kontroladoreak

Kontroladoreek Laravelen, gure aplikazioaren logika izango dute.

Kontroladoreak, eskuz sortu ditzakegu, baina artisan erabilita errezagoa egingo zaigu. Kontroladorearen izena edozein izan daiteke, baina normalean (konbentzioz) izena letra larriz eta singularrean idazten da eta jarraian Controller hitza: HomeController, IkasleController, KategoriaController etab.

```bash
php artisan make:controller HomeController
```

Artisanek sortzen du php fitxategi bat guk ezarritako izenarekin app>Http>Controllers karpetan.

Ikusten dugunez, fitxategian namespace bat sortzen du, horrek gure kontroladorea non dagoen kokatuta adierazten du. Eta HomeController klase bat sortuko luke ere.

Kontroladore horretan guk behar izango ditugun metodoak sortuko ditugu.

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class HomeController extends Controller{

public function index(){

return "Hau da nire blog-aren lehenengo orria. Ongi etorri!!";

}
```

Gero kontroladore horretan dagoen metodo bat erabili nahi izatekotan, adibidez ruta bat egiteko, lehenengo esta behin esan beharko dugu kontroladore horren metodoa erabili nahi dugula, horretarako namespacea gehituko dugu:

```php
use App\Http\Controllers\HomeController;
```

Eta ondoren jada, erabili ahal izango ditugu kontroladore horren klaseak dituen metodo guztiak, adibidez index.php adibide honetan adierazten den moduan:

```php
Route::get('/', [HomeController::class, 'index']);
```

Normalean kontroladore bakoitzak hainbat metodo izango ditu, baina bakarra izatekotan, gure metodoari index deitu beharrean \_\_invoke() eta kasu horretan ruta definitzerakoan ez dugu zertan metodoaren izena jarri behar, zuzenean metodo horretara sartuko zelako.

```php
class HomeController extends Controller{

public function __invoke(){

return "Hau da nire blog-aren lehenengo orria. Ongi etorri!!";

}
```

Eta rutan, kontroladorearen izena jarriko genuke soilik

```php
Route::get('/', HomeController::class);
```

Resource Controller

Gure kontroladorea CRUD bat egiteko erabiliko dugunean, badugu aukera artisanen bidez laraveleri esateko horretarako erabiliko dugula

```bash
php artisan make:controller ProfesorController --resource
```

Horrela eginez gero, ikusiko duzuenez, CRUD bat egiteko erabiltzen ditugune metodoak idatziko dizkigu.
