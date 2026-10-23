# Datu basea sortzen

Laravel 12k berez, datu base bat sortuko du proiektua sortzerakoan, sqlite datu basea. Horregaz lan egin nahi izatekotan, zuzenean egin genezakeen.

Baina, Laravelek hainbat datu base motekin egin dezake lan, dokumentazioan esaten digu zeinekin egin dezakeen:

<https://laravel.com/docs/12.x/database>

Gure kasuan Mysql-rekin egingo dugu.

Badago fitxategi bat, config>database.php non Laravelek bilatuko duen datu baseen inguruko informazioa, beraz hori izan beharko litzateke lekua guk datu basea zein den idazteko. Baina fitxategi horrek segurtasun gutxi du, zeren eta gure proiektua github-era igo orduko adibidez, config karpetan dagoen informazio guztia ikusgai egongo litzateke.

Laravelek badu metodo bat, env izenekoa; metodo honek begiratuko du ".env" fitxategian DB\_CONNECTION aldagaia baldin badago, egotekotan balio hori hartuko du, bestela default balioa hartuko luke, hau da, 'mysql' balioa.

Zergatik da seguruago .env fitxategian igotzea database.php baino?

Laravel konfiguratuta dagoelako proiektua nonbaitera igo ezkero .env fitxategia ez igotzeko.

Ikusten dugunez, Laravelek jada laguntzen digu guk behar ditugun parametroak identifikatzen, database.php-en ikusi dugunez DB\_CONNECTION da kontutan izango duen aldagaia:

![](../../assets/images/Datu%20basea%20sortzen/28969677a32b8740f85e5811279d0c50.jpg)

Baina guk mysql datu base batekin egingo dugunez lan, guk aldagai guzti hauek deskomentatu ditugu eta gure datu basearen datuak idatziko ditugu.

Horretarako, lehenengo eta behin gure datu basea sortuko dugu PhpyAdmin-en, hurrengo adibidean blog izenarekin.

Kontutan izan XAMP erabiltzen baduzue password hutsik geratu behar dela era MAMP erabiliz gero, aldaketarik ez baduzue egin, root izango dela.

```dotenv
DB_CONNECTION=mysql

DB_HOST=127.0.0.1

DB_PORT=3306

DB_DATABASE=blog

DB_USERNAME=root

DB_PASSWORD=root
```

Kontuz! Datu basea sortzerakoan, zein "cotejamiento" nahi dugun adierazi behar da, berez phpMyAdmin-en utf8mb4\_unicode\_ci jartzen du, Laravel-ek jartzen duen berdina, hori aldatzekotan, gure .env fitxategian ere adierazi beharko genuke.

'collation' => env('DB\_COLLATION', 'utf8mb4\_unicode\_ci'),

Hurrengoa: Migrazioak
