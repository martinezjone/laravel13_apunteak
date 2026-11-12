# Laravel 13rekin hasteko gida · Windows

Gida honetan, ordenagailua prestatuko duzu eta zure lehen Laravel 13 aplikazioa nabigatzailean irekiko duzu. Komando guztiak **PowerShell** terminalean exekutatzeko dira. VS Code-n, ireki terminala **Terminal → New Terminal** aukerarekin.

Urrats bakoitzean, egiaztatu zer duzun instalatuta. Tresna falta bada edo bertsioa zaharregia bada, instalatu edo eguneratu, eta errepikatu egiaztapena hurrengo urratsera igaro aurretik.

## 1. Egiaztatu PHP

```powershell
php -v
where.exe php
php --ini
```

| Emaitza | Zer egin |
| --- | --- |
| PHP 8.3, 8.4 edo 8.5 duzu | Laravel 13ren gutxieneko bertsioa betetzen duzu. Egiaztatu PHPren hedapenak. |
| PHP 8.2 edo zaharragoa duzu | MAMP, XAMPP edo Laragonen PHP bertsio bateragarri bat baduzu, jarri haren `php.exe` fitxategia duen karpeta bertsio zaharrarenaren aurretik `Path` aldagaian. Bestela, instalatu PHP bertsio bateragarri bat. |
| PowerShell-ek ez du `php` ezagutzen | PHP instalatuta badago, gehitu `php.exe` dagoen karpeta `Path` aldagaian. Bestela, erabili beheko instalazio-aukeretako bat. |
| Hainbat bide agertzen dira | `where.exe php` komandoak aurkitutako exekutagarriak erakusten ditu. Erabili `Get-Command php` PowerShell-ek zein hautatzen duen ikusteko, eta `php -v` haren bertsioa egiaztatzeko. |

`Path` aldatzeko, bilatu Windowsen Hasiera menuan erabiltzailearen ingurune-aldagaiak editatzeko aukera. Editatu **Path**, gehitu PHPren karpeta eta, beharrezkoa bada, aldatu sarreren ordena. Itxi eta ireki berriro PowerShell. VS Code-ko terminala erabiltzen baduzu, berrabiarazi VS Code ere. Ondoren, errepikatu egiaztapenak.

**PHP instalatu behar baduzu**, aukeratu bide bat:

- **Laravel Herd Windows-erako.** Deskargatu eta exekutatu instalatzaile grafikoa. PHP, Composer eta Laravel-en instalatzailea dakartza. Amaitutakoan, ireki terminal berri bat eta errepikatu egiaztapenak. [Herd instalatzeko dokumentazioa](https://herd.laravel.com/docs/windows/getting-started/installation)
- **Instalazio azkar ofiziala.** Laravel 13ren dokumentazioan, aukeratu **Windows PowerShell** PHP eta Composer instalatzeko atalean, eta exekutatu bertako komandoa. PHP 8.5, Composer eta Laravel-en instalatzailea instalatzen ditu. Lehenago `php.new` bidez instalatu bazenituen, komando bera berriz exekuta dezakezu hirurak eguneratzeko. [Laravel 13 instalatzeko dokumentazioa](https://laravel.com/framework/docs/13.x/installation#installing-php)

### Egiaztatu PHPren hedapenak

```powershell
php -m
```

Laravel 13k hedapen hauek behar ditu: Ctype, cURL, DOM, Fileinfo, Filter, Hash, Mbstring, OpenSSL, PCRE, PDO, Session, Tokenizer eta XML. Gida honetako proiekturako, egiaztatu **pdo_sqlite** ere agertzen dela. Hedapen horri esker, PHPk SQLite datu-basea erabil dezake.

Hedapen bat falta bada, erabili `php --ini` PHPk zein `php.ini` fitxategi kargatzen duen ikusteko. Hedapena zure instalazioan badago, aktibatu fitxategi horretan. Adibidez, `;extension=pdo_sqlite` lerroa aurkitzen baduzu, kendu hasierako puntu eta koma. Hedapena instalazioan ez badago, instalatu zure PHP banaketak eskaintzen duen moduan. Errepikatu `php -m`. [Laravel 13ren PHP eskakizunak](https://laravel.com/framework/docs/13.x/deployment#server-requirements)

## 2. Egiaztatu Composer

Composer-ek proiektuaren PHP mendekotasunak instalatzen ditu.

```powershell
composer --version
```

- **Composer 2.x baduzu**, joan 3. urratsera.
- **Composer 1.x baduzu** eta PHAR gisa instalatu bazenuen, exekutatu `composer self-update --2` eta egiaztatu bertsioa berriro. Beste tresna batek kudeatzen badu instalazioa, erabili tresna hori eguneratzeko.
- **Komandoa ez badabil**, ireki Windows-erako Composer-en dokumentazioa, deskargatu **Composer-Setup.exe** eta exekutatu. PHPren bidea eskatzen badizu, aukeratu 1. urratseko `php.exe` bateragarria. Ireki terminal berri bat eta errepikatu egiaztapena.

[Composer Windows-en instalatzea](https://getcomposer.org/doc/00-intro.md#installation-windows) · [Composer eguneratzea](https://getcomposer.org/download/#download-channels)

## 3. Egiaztatu Node.js eta npm

Node.js-ek frontendeko tresnak exekutatzen ditu. npm-k haien mendekotasunak instalatzen ditu, eta Vite-k CSS eta JavaScript fitxategiak prestatzen ditu.

```powershell
node -v
npm -v
```

- **Ikasgelarako, erabili Node.js 24 LTS.** Dagoeneko 22 adarreko 22.12 bertsioa edo berriagoa baduzu, Vite-ren eskakizuna betetzen duzu. Vite-ren dokumentazioak 20 adarrean 20.19tik aurrerako bertsioak ere onartzen ditu.
- **Node.js falta bada edo bertsioa zaharregia bada**, instalatu Node.js 24 LTS Windows-erako instalatzailearekin. Node.js Herd edo bertsio-kudeatzaile baten bidez kudeatzen baduzu, instalatu eta hautatu Node.js 24 tresna horretan. Ireki terminal berri bat eta exekutatu bi komandoak berriz.
- **PowerShell-ek `npm.ps1` blokeatzen badu**, probatu `npm.cmd -v`. Funtzionatzen badu, erabili `npm.cmd` gida honetako npm komandoetan. Hainbat instalazio badituzu, `where.exe node` eta `where.exe npm` komandoekin haien bideak ikus ditzakezu.

[Node.js deskargatzea](https://nodejs.org/en/download) · [Vite-rekin bateragarriak diren Node.js bertsioak](https://vite.dev/guide/#scaffolding-your-first-vite-project)

## 4. Instalatu edo eguneratu Laravel-en instalatzailea

```powershell
laravel --version
```

Komandoak **instalatzailearen bertsioa** erakusten du. **Framework-aren bertsioa** proiektuaren karpetan egiaztatuko duzu, 5. urratsean.

- Komandoa falta bada eta PHP eta Composer instalatuta badituzu, exekutatu `composer global require laravel/installer`.
- Composer bidez instalatu bazenuen eta eguneratu nahi baduzu, exekutatu `composer global update laravel/installer`.
- Composer-ek instalatuta dagoela esaten badu baina PowerShell-ek komandoa aurkitzen ez badu, exekutatu `composer global config bin-dir --absolute`. Gehitu erakusten duen karpeta `Path` aldagaian eta ireki beste terminal bat. Herd edo `php.new` bidez instalatu bazenuen, erabili instalazio-bide bera eguneratzeko.

[Laravel-en instalatzaile ofiziala](https://laravel.com/framework/docs/13.x/installation#installing-php)

## 5. Sortu lehen proiektua

Ireki PowerShell ariketak gordetzen dituzun karpetan. VS Code erabiltzen baduzu, ireki karpeta hori eta sortu terminal bat. Exekutatu:

```powershell
laravel new lehen-laravel
```

Instalatzaileak galdetzen badizu, aukeratu **starter kit-ik gabe** eta hautatu **SQLite**. Probetarako framework-aren aukera lehenetsia onar dezakezu. SQLite-k datuak fitxategi batean gordetzen ditu, eta praktika hau egiteko ez duzu datu-basearen zerbitzaririk abiarazi behar.

```powershell
cd lehen-laravel
php artisan --version
composer check-platform-reqs
php artisan migrate:status
```

Egiaztatu Artisan-ek **Laravel Framework 13.x** erakusten duela, Composer-ek PHPren eta hedapenen eskakizunak baliozkotzen dituela eta SQLite-ren migrazioak agertzen direla. Instalazioan, Laravel-ek `database/database.sqlite` fitxategia sortu eta hasierako migrazioak exekutatzen ditu. Migrazioren bat egiteko badago, exekutatu `php artisan migrate` eta egiaztatu egoera berriro.

**Laravel 13 berariaz hautatu behar baduzu**, sortu proiektua Composer bidez. Exekutatu komandoa ariketen karpeta nagusitik eta erabili libre dagoen karpeta-izen bat. Ondoren, sartu proiektu berrian eta errepikatu goiko egiaztapenak.

```powershell
composer create-project laravel/laravel lehen-laravel-13 "^13.0"
cd lehen-laravel-13
php artisan --version
```

[Proiektu bat sortzea](https://laravel.com/framework/docs/13.x/installation#creating-an-application) · [SQLite eta migrazioak](https://laravel.com/framework/docs/13.x/installation#databases-and-migrations) · [Composer create-project](https://getcomposer.org/doc/03-cli.md#create-project)

## 6. Abiarazi Laravel eta ireki nabigatzailea

Proiektuaren karpetan, `artisan`, `composer.json` eta `package.json` fitxategiak dauden lekuan, exekutatu komando hauek banan-banan. Itxaron bakoitza amaitu arte hurrengoa exekutatzeko.

```powershell
npm install
npm run build
composer run dev
```

`composer run dev` komandoak Laravel-en garapen-zerbitzaria, ilarako atazak prozesatzeko programa eta Vite abiarazten ditu. Utzi terminala irekita eta joan [http://localhost:8000](http://localhost:8000) helbidera. Laravel-en ongietorri-orria ikusten baduzu, instalazioa prest dago. Zerbitzaria gelditzeko, sakatu **Ctrl+C**. Hurrengo saioan, sartu karpeta berean eta exekutatu berriro `composer run dev`.

| Arazoa | Zer egiaztatu |
| --- | --- |
| `Could not open input file: artisan` | Egiaztatu proiektuaren karpetan zaudela. Exekutatu `Get-Location` eta `Get-ChildItem artisan`. |
| `could not find driver` SQLite erabiltzean | Egiaztatu terminaleko PHPk `pdo_sqlite` aktibatuta duela. Erabili `php --ini` eta `php -m`. |
| `EBADENGINE` mendekotasunak instalatzean | Begiratu `node -v` komandoaren emaitza eta hautatu npm-k adierazitako tartearekin bateragarria den bertsioa. |
| Vite-ren manifestuari buruzko errorea | Exekutatu `npm install` eta `npm run build` proiektuaren karpetan. Konpilazioak huts egiten badu, konpondu npm-k erakusten duen errorea. |
| 8000 ataka erabilita dago | Gelditu ataka erabiltzen ari den zerbitzaria. Baliabideak dagoeneko konpilatuta badituzu, `php artisan serve --port=8001` ere exekuta dezakezu. Ondoren, ireki [http://localhost:8001](http://localhost:8001). |

Laravel-en garapen-zerbitzaria erabiltzen duzunez, proiektua `htdocs` karpetatik kanpo ere gorde dezakezu. [Aplikazioa abiarazteko dokumentazioa](https://laravel.com/framework/docs/13.x/installation#creating-an-application)
