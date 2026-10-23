# Zelan funtzionatzen du?

Esan bezala, Laravelek 4 kontzepturekin egiten du lan, hurrengo irudian ikusi dezakegun moduan:

![](../assets/images/Zelan%20funtzionatzen%20du/4f323cafbe17ccd736212210ae8e6cef.jpg)

## RUTAK

Rutak erabiltzea oso kontzeptu garrantzitsua da Laravel erabiltzen hasteko.

Erabiltzaile bat web baten URL batetik bestera nabigatzen ari denean, zerbitzariari eskaerak egiten ari da. Zerbitzariak modu batera edo bestera erantzungo dizu, eskatutako URLaren arabera.

Adibidez, erabiltzaile bat www.adib.com/perfil gunera sartzen bada, zerbitzariak profilaren orriarekin erantzungo dio, edo www.adib.com/blog gunera sartzen bada, zerbitzariak blogeko sarreren zerrenda ikusiko du.

Laravelen kudeaketa hori ibilbideen (ruten) bidez egiten du.

Laravelen ibilbideak erabiltzen dituzten bi fitxategi daude. Lehenengo fitxategia web.php da, eta bertan gure web guneko ibilbideak erregistratuko ditu. Bigarren fitxategia APIak sortzera bideratuta dago, eta api.php deritzo.

Ibilbidearen definizioan, bidea zer eskaera-motarekin lan egingo duen adieraz diezaiokegu. Adibidez, gure webgunerako eskaera estandarretarako GET ibilbideak definituko ditugu, baina, datuak gordez lan egin nahi badugu, POST motako eskaera gisa defini ditzakegu.

Aurrerago sakonduko dugu honetan, baina adibide bat jartzearren, hau izango zen get eskaera bat:

```php
Route::get('/ikasleak', [IkasleController::class, 'index'])  ->name('ikasleak.index');
```

## MODELOAK

![](../assets/images/Zelan%20funtzionatzen%20du/033cb0422ace1e74d71164280d73259d.jpg)

Ereduak arduratzen dira datuekin lan egiteaz. Horrek esan nahi du datu-basean erregistroren bat lortu, gorde edo eguneratu nahi baduzu ereduak erabiliko ditugula.

Gure datu-baserako sarbidea kudeatzeko, ereduek Eloquent erabiltzen dute.

Eloquent ORM bat da, eta, funtsean, gure datu-baseko datuak entidadeekin lotzeko aukera ematen digu. Horrela, asko arintzen du datu-basearekiko lana.

Datu-basea Eloquent-ekin erabiltzea oso erraza da, eta erregistroak gorde, ezabatu edo eguneratu ez ezik, zure ereduei funtzio konplexuagoak eman ahal izango dizkiezu.

Funtzionalitate horietako batzuk ereduen arteko erlazioak definitzea, scopeak sortzea edo atributu pertsonalizatuetarako sarbideak sortzea izan daitezke.

Laravelen, konbentzioz, datu-baseko taula bakoitzeko eredu bat sortzen da; beraz, taula bateko errenkada bakoitza objektu bati dagokio, objektuak lerro jakin bateko taulako atributuei dagozkien propietateak ditu, irudian ageri den bezala.

Eloquent eredua izanez gero, ereduak zuzenean lotuta daude entitate batekin eta, era berean, datu-baseko taula batekin. Beraz, User izeneko modelo bat zuzenean erlazionatuta dago datu-basean izen bera duen taula batekin, baina letra xehez eta pluraleko aketarekin, adibidez "users" izenarekin.

Kontua da, datu-baseko elementu guztiek objektu manipulagarri gisa duten irudikapena izango dute.

Eloquent-ekin, taula bat Model klasea handitzen duen klase baten bidez irudikatzen da.

## bistak

Laravelek bere txantiloi-sistema du, blade izenekoa.

Txantiloi horiek .blade.php luzapena izaten dute, eta, beraz, erraz identifika dezakezu zein fitxategi dagozkien ikuspegiei.

Blade bidez, fitxategi ordenatu eta garbi bat sortzea lortzen dugu

Datuak erakusteko modu sinpleago bat emateaz gain, layout-en sistema bat ere badu blade-k, eta, beraz, txantiloiak zabaldu, ataletan banatu... Azken batean, blade bidez, zure web gunearen eskeletoa era antolatu eta eraginkorrean eraiki ahal izango duzu.

Adibidez, pantailan zeozer idazteko giltz bikoitzagaz izango lizateke soilik:

```php
<td>{{ $ikasle->izen_abizen }}</td>
```

Orain, php kodea sartzeko, ez dugu php etiketa zabaldu eta itxi behar, aurreko adibidean ikusi dugun moduan, beste adibide bat hurrengo hau izan daiteke:

```php
@foreach ($ikasleak as $ikasle)
```

## kontroladoreak

Laraveleko kontrolatzaileak zure web aplikazioaren zati logiko gisa defini daitezke.

Ereduak gauzatuko dituzten ekintzak maneiatzen dituzte, eta zer ikuspegi itzuliko duten erabakitzen dute.

HTTP eskaera egiten denean (adibidez, zure web aplikazioaren URL batera joatea), Laravelek erregistratu dituzun ibilbideetan bilatuko du. Oro har, ibilbideak erregistratzen dituzunean, kontrolatzaile baten metodoa lotuko duzu.

Kontrolatzailearen funtzio horretan eraikiko duzu zure aplikazioaren logika.

### Laburbilduz

«Recetas» izeneko ruta bat erregistratua izan dezakezu, eta, erabiltzailea URL horretara sartzen denean, kontrolatzailearen mostrarRecetas () metodora deitu du.

Metodo honek, modeloaren bitartez, errezetetako datu guztiak jasoko ditu datu basetik eta, ondoren, "recetas" bista itzuliko luke.

![](../assets/images/Zelan%20funtzionatzen%20du/f30b33de598d5e5c5ee416f36b18c008.jpg)
