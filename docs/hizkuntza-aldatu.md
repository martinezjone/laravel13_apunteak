# HIZKUNTZA ALDATU

Gure formularioko errore-mezuak probatzean ikusiko dugu nola ingelesez agertzen diren. Hori aldatzeko, Laravel-en hizkuntza konfiguratu behar dugu.

1. Lang fitxategiak publikatu

Terminalean exekutatu:

php artisan lang:publish

Honek lang karpetan en karpeta sortuko du.

2. Hizkuntzak instalatu

Ez dugu eskuz kopiatu eta itzuli behar; errazagoa da Laravel-Lang erabiliz hizkuntzak gehitzea:

composer require laravel-lang/common --dev

Ondoren, nahi ditugun hizkuntzak gehitzen ditugu:

php artisan lang:add es    // Gaztelera

php artisan lang:add eu    // Euskara

Horrek lang/es eta lang/eu karpetak sortuko ditu guztiz itzulita.

3. Laravel-i zein hizkuntza erabili behar duen esatea

.env fitxategian honako hau aldatzen dugu:

APP\_LOCALE=eu

APP\_FALLBACK\_LOCALE=eu

Horren ostean aplikazioa euskaraz egongo da.

4. Formularioen eremu-izenak pertsonalizatzea

Baliozkotze-mezuetan Laravelrek automatikoki erabiltzen ditu formularioetako eremu-izenak (name, email, izen\_abizen …).

Eremu horiek modu “politean” erakutsi nahi baditugu,

lang/eu/validation.php fitxategira joan eta bukaeran dagoen attributes atal honetan idazten ditugu:

'attributes' => [

'izen\_abizen' => 'Izen Abizenak',

'email' => 'Posta Elektronikoa',

'password' => 'Pasahitza',

'name' => 'Izena',

];

Horrela, errore-mezuak itzulita eta formatu egokian agertuko dira.

Ez da ezer gehiago egin behar (ez JSON eguneratu, ez ezer berriro kargatu).

5. Fallback hizkuntza gomendatua

Gaztelania ere instalatuta badago, gomendagarria izan daiteke:

APP\_FALLBACK\_LOCALE=es

Horrela euskarazko testuren bat falta bada, gaztelania erabiliko da, eta ez ingelesa.

## Laburpena

php artisan lang:publish

composer require laravel-lang/common --dev

php artisan lang:add es, php artisan lang:add eu

.env → APP\_LOCALE=eu

validation.php → attributes atalean eremu-izenak pertsonalizatu

Esteka interesgarria: <https://laravel-lang.com/installation/>
