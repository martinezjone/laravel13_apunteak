# Laravel Brezze

### Instalazioa

Laravel Breeze Laravelen kit bat da gure aplikazioan erabiltzaileen erregistroak kudeatzeko eta autentikatzeko balioko diguna.

Laravel dokumentazio ofiziala: <https://laravel.com/docs/12.x/starter-kits#laravel-breeze>

Larabel Breeze instalatzeko hurrengo pausuak jarraitu behar dira:

Lehenengo pausua Laravel Breeze paketea instalatuko da:

```bash
composer require laravel/breeze --dev
```

Hurrengo komandoak egiten duena aplikazioak behar dituen rutak, kontroladoreak etab instalatu

```bash
php artisan breeze:install

php artisan migrate

npm install

npm run dev
```

KONTUZ!!!! Aplikazioa jada martxan baldin badugu, routes/web.php fitxategia gorde lehenago, pakete hau intalatzerakoan fitxategia berridazten bait du

Intalatzerakoan hainbat galdera egingo dizkigu, gure kasuan blade erabiliko dugu, badaude beste hainbat aukera instalatzeko, dokumentazio ofizialean ondo azalduta daudenak.

Kontutan izan ere laravel breezek sortzen dituen bista guztiak Tailwinekin diseinatutak daudela.

Erabiltzaile-interfazearen zati bakoitza pertsonaliza dezakezu, resources/view/auth karpetan bista-fitxategiak editatuta; erabiltzaile-interfazearen zati bat Blade osagaietan dago antolatuta, eta resources/view/components karpetan aurki dezakezu.

### Rutak (web.php)

Ikusten dugun moduan, Breezek bere rutak sortzen ditu, ProfileController-ek exekutatzen dituen rutak. Guk gureak sartuko ditugu ere, beraz hemen kopiatuko ditugu.

Ikusten dugunez middleware batzuk erabiltzen ditu rutak sortzerakoan, zer dira Middlewareak?

Hauek rutak filtratzeaz arduratzen dira, kasu honetan adibidez, erabiltzaile bat logeatuta ez izatekotan, ezingo da gure webgunean sartu, beraz rutak guztiei auth filtroa jarriko diegu.

Breezek bi middleware erabiltzen ditu: auth eta verified. Auth-ek erabiltzaile bat logeatuta dagoen ala ez konprobatuko du (autenticado) eta verified-ek erabiltzaile batek bere emaila berifikatuta duen edo ez konprobatzen du  (verificado)

```php
Route::middleware('auth')->group(function () {

Route::get('/profile', [ProfileController::class, 'edit'])->name('profile.edit');

Route::patch('/profile', [ProfileController::class, 'update'])->name('profile.update');

Route::delete('/profile', [ProfileController::class, 'destroy'])->name('profile.destroy');

});
```
