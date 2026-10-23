# Zer da laravel eta zelan funtzionatzen du?

Laravel, PHPko frameworks erabilienetariko eta garatzaileen komunitateak hobekien baloratutakoetariko bat da.

Baina, zer da Framework bat? Lan-eskema bat da; proiektu bat egiteko, oinarrizko egitura bat eskaintzen du, softwarea antolatzeko eta garatzeko abiapuntu gisa balio duena. Batez ere, web-aplikazioen garapena errazten du Laravelek.

Haren software-diseinua MVC (Modelo Vista Controlador) ereduan oinarritzen da; beraz, garatzen hasteko oinarri egituratua emango dio zure kodeari.

### MVC

![Eskema MVC (Modelo-Vista-Controlador)](../assets/images/Zer%20da/b7657af9ea422752e0cc78138848640c.jpg)

MVC software-arkitekturako patroi bat da.

Agian, horrela esanda, hasieran, ulertzeko oso zaila dela irudituko zaizu, baina, berez, kodea egituratzeko modu bat besterik ez da.

Model (Eredua edo modelo): Aplikazioaren datuak kudeatzeaz arduratzen da. Normalean, datu horiek datu-base bati lotuta egoten dira. Adibidez, user-taulako datuekin lan egingo lukeen User eredu bat definitu dezakezu.

View (Ikusmena edo vista): Izenak adierazten duen bezala, proiektuaren ikus-zatiaz arduratzen da, hau da, ikuspegian eraikitzen da erabiltzaileak ikusiko duen interfazea. Adibidez, ikuspegi batek erabiltzaile baten profilaren atala erakutsi dezake.

Controller (Kontrolatzailea edo controlador): Aplikazioaren zati logikoari dagokio. Kontrolatzaileak bisten eta ereduen arteko lotura dira. Adibidez, erabiltzaile baten baimenen arabera ikuspegi bat edo bestea erakustea erabaki dezake kontrolatzaile batek.

![Modelo, vista. controlador zelan funztionatzen duen eskema](../assets/images/Zer%20da/3d2c97ea28b75e9248a2c702085a6fb4.jpg)

Laravelen abantaila handienetako bat da libreria eta modulu ugari dituela bere funtzionalitateak zabaltzeko.

Laravel framework bat da, eta zerbitzariaren zatian lan egitera bideratuta dago. Hala ere, Laravelek bere ikuspegi-sistema eskaintzen du, blade izenekoa, eta, horren bidez, Laravel erabiliz inolako arazorik gabe eraiki dezakezu zure interfazea.

Laravelek oinarrizko lau kontzeptu lantzen ditu, eta horiek ikasi egin behar dituzu Laravelen funtzionamendua ulertu nahi baduzu: ibilbideak (rutas), ereduak (modelos), ikuspegiak (vistas) eta kontrolatzaileak (controladores).

Laraveleko adibide bat:

![Laravel zelan funtzionatzen duen erakusten duen adibidea](../assets/images/Zer%20da/f30b33de598d5e5c5ee416f36b18c008.jpg)
