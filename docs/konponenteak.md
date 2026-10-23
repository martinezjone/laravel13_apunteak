# KONPONENTEAK

Laravel-en konponenteak HTML-a eta PHP-a modu garbiago eta antolatuago batean konbinatzeko modu bat dira. Konponente hauek, berez, HTML eta PHP kode-blokeak dira eta, behin sortuta, erraz berrerabili daitezke beste plantilla edo orri askotan. Honek kodea errepikatu gabe diseinuak erabili eta mantentzea askoz ere errazago egiten du.

Dokumentazio ofiziala: <https://laravel.com/docs/11.x/blade#components>

Konponente bat sortzeko, view karpetaren barruan, beste karpeta bat sortuko dugu components izenarekin, eta honen barruan gure konponentearen blade-a sortuko genuke, adibidez:

views/components/alert.blade.php

Alert bat sortzeko, adibidez, tailwind-en alertak erabili ditzakegu, horretarako web honetara sartu gaitzeke: <https://flowbite.com/docs/components/alerts/>

Eta bestetik, tailwind erabili ahal izateko ere, nonbaiten deitu beharko diogu libreria horreri. Nola gehitu jakiteko, hurrengo estekan duzue informazioa: <https://tailwindcss.com/docs/installation/play-cdn>

Beraz, adibide honetarako, gure orrian tailwind sartuko genuke modu honetan:

<script src="<https://cdn.tailwindcss.com>"></script>

Eta gure alert.blade.php fitxategian gure alerta sartuko genuke:

<div class="p-4 mb-4 text-sm text-blue-800 rounded-lg bg-blue-50 dark:bg-gray-800 dark:text-blue-400" role="alert">

<span class="font-medium">Alerta!</span> Kontuz izan egiten duzunarekin

</div>

Modu honetan jada, guk behar dugun leku guztietan erabili ahal izango genuke konponente hau, erabiltzeko lehenengo "x-" jarriko genuke, konponente bat dela esateko eta bestetik, gure konponentaren izena, kasu honetan  x-alert. Gogoratu konponente bat deitzerakoan beti itxi behar dugula:

<x-alert/>

edo

<x-alert>

</x-alert>

Konponenteak aldagai batekin

Kasu honetan gure konponenteari aldagai bat bidali nahiko diogu, horretarako, gure alertan, aldagaia non erakutsi nahi dugun esango dugu, aldagai bakarra bidaltzen dugunean, slot bat bidaltzen dugu.

<span class="font-medium">Alerta!</span> {{ $slot }}

eta deitzerakoan aldiz, x-alert etiketen artean aldagai horren balioa bidaliko genuke:

<x-alert>

Hau da alerta mezuaren edukia

</x-alert>

Konponenteak aldagai bat baino gehiagorekin

Alde batetik, aldagai orokorra izango dugu, slot izenekoa eta bestetik named slots izango ditugu, slots hauei izen bat jarri behar diegu,adibidez:

<x-alert>

<x-slot name="title">

Hau da goiburua

</x-slot>

Hau da alerta mezuaren edukia

</x-alert>

eta deitzerakoan aldiz, x-alert etiketen artean aldagai horren balioa bidaliko genuke:

<span class="font-medium">{{ $title ?? 'lehenetsiko titulua' }}</span> {{ $slot }}

Zergaitik jarri ditugu galdera ikurrak eta komillak? Horrela jartzerakoan, gaude esaten title ez definitzekotan, komillen artean dagoen balioa izango duela.

Konponenteen parametroak (konponente bakarra)

Konponente bati aldagaiak pasatzeaz gain, parametroak ere pasatu ditzakegu, hori konponentearen etiketan egingo dugu, adibidez guk type izeneko parametro bat pasatuko dugu, modu honetan, segun eta zein mota den gure alerta, itxura bat edo bestea izango du.

Horrela egingo genuke:

<x-alert type="info">

<x-slot name="title">

Hau da goiburua

</x-slot>

Hau da alerta mezuaren edukia

</x-alert>

Eta gure alert-ean alerta ezberdinak definituko ditugu segun eta tipoa zein den, horretarako blade badu direktiba bat, props izenekoa.  Beraz guk direktiva horreri deitzerakoan, array baten nire parametroen izenak sartuko ditut, eta Laravelek egingo duena da aldagai bat sortu nik pasatutako parametro bakoitzarekin:

@props(['title'])

@props(['type' => 'info') // Balio lehenetsia emateko

Eta orain gure konponente barruan php kodea sartuko dugu esateko zein alerta aterako lukeen mota bakoitzeko.

@php

switch ($type) {

case 'info':

$class = "bg-blue-50 text-blue-800";

break;

case 'danger':

$class = "bg-red-50 text-red-800";

break;

case 'success':

$class = "bg-green-50 text-green-800";

break;

case 'warning':

$class = "bg-yellow-50 text-yellow-800";

break;

case 'dark':

$class = "bg-gray-50 text-gray-800";

break;

default:

# code...

break;

}

@endphp

<div class="p-4 mb-4 text-sm {{ $class }}  rounded-lg  dark:bg-gray-800 dark:text-blue-400" role="alert">

<span class="font-medium">{{ $title ?? '' }}</span> {{ $slot }}

</div>

Konponenteen parametroak (konponente bat baino gehiago)

Konponente bati aldagaiak pasatzeaz gain, parametroak ere pasatu ditzakegu, hori konponentearen etiketan egingo dugu, adibidez guk type izeneko parametro bat pasatuko dugu, modu honetan, segun eta zein mota den gure alerta, itxura bat edo bestea izango du.

Horrela egingo genuke:

<x-alert type="info" class="mb-4">

<x-slot name="title">

Hau da goiburua

</x-slot>

Hau da alerta mezuaren edukia

</x-alert>

Kasu honetan bigarren parametro hori, promps-en jaso beharrean $attributes-en jasoko genuke. Adibide honetan, gure konponentean  {{$attributes}} idaztekotan, pantailatik zuznean class="mb-4" agertuko litzateke.

Componentes de clase

Orain arte erabili duguna "componente anónimo" deitzen zaio, baina badago aukera gelako konponenteak egiteko. Horretarako artisan-en bidez sortuko dugu. Konponente anonimo baten, bista bat soilik zango dugu, baina gelako  konponentea sortzerakoan, alde batetik klase bat sortuko du, alertaren logika guztia izateko, eta bestetik bista bat, koponente horren itxura izateko.

php artisan make:component alert2

Sortuko dugu klase bat: app/View/Components karpetan, sortuko du minuskuletan, baina konbentzioak jarraitzeko, hobeto maiuskuletan sortzen badugu, bai fitxategia, eta baita klasea ere:

class Alert2 extends Component

Orain egingo genukeena izango litzateke klaseko \_\_construct metodoan, lehen idatzitako logika sartuko genuke eta bista soilik html kodea.
