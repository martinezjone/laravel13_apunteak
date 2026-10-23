# Bistak

/resources/views karpetan gordetzen dira Laraveleko bista guztiak. Eta normalean, konbentzioz, bistaren izena, deitu duen metodoaren izen berdina  izango da.

Bistak Laravelen php edo blade erabili dezakete, bistaren luzapenak esango digu bata edo bestea erabiltzen ari duen.

bista\_izena.php

bista\_izena.blade.php // Guk blade bistekin egingo dugu lan beti

BLADE, Blade Laravel-ekin batera sartzen den txantiloi motor xume baina indartsua da. Kasu honetan, gure blade-ak ere php kodea onartzen du.

Bistak bai ruten bidez bai kontroladoran deitu ditzakegu, baina normalean kontroladoreek deituko dituzte.

```php
Route::get('user', function(){

//user.php edo user.blade.php bistaratuko dugu

return view('user');

});
```

Gomendatzen da azpikarpetak erabiltzea gure bista antolatuta izateko. Bista horiei deitzeko bai barra, bai puntua erabili dezakegu. user/index.blade.php-n gordeta dagoen bista hurrengo modutan deitu dezakegu:

```php
Route::get('user', function(){

return view('user/index');

return view('user.index'); // Normalean horrela erabiltzen da

});
```

Bista bati aldagaiak pasatu

Bista bati aldagaiak pasatzeko array asoziatibo bategaz egingo dugu: `return view('grupo.vista', $arrayAsociativo);`

Adibidez:

```php
return view('user.index', ['name' => 'James']);
```

```php
return view('blog.history', [

'mes' => $mes,

'ano' => $ano,

'posts' => $posts

]);
```

Badago aukera ere with bitartez egiteko:

```php
return view('greeting')->with('name', 'Victoria');
```

Edo compact erabilita ere, berdin dio zein modutan pasatzen ditugun, normalean programatzailearen lan egiteko eran edo programatzaile taldearen konbentzioa izaten da, baina hau izaten da gehien erabiltzen dena:

```php
return view('users', compact('mes', 'ano', 'posts'));

//hurrengo honen berdina da:

return view('blog.history', [

'mes' => $mes,

'ano' => $ano,

'posts' => $posts

]);
```

Compact berez array asoziatibo bat bidaltzen du.

Laraveleko bistak - blade

Laravelek badu txantiloi motore bat, blade izenekoa, oso erabilgarria egingo zaiguna bistekin lan egiteko.

Hau da Laraveleko orri ofiziala: <a href ="https://laravel.com/docs/12.x/blade" target="_blank">https://laravel.com/docs/12.x/blade</a>

- Bistak Laravelen, php eta html nahazten sortu daitezke.
- Batzutan kode hibrido hau ulertzeko zaila izan daiteke.
- Hori dela eta, txantiloiak erabiltzen dira, modu honetan aldagaia den kodea html-en sartzea errezagoa izango da.

### Datuak erakutsi

Bladen datuak erakustea oso erreza da, horretarako bakarrik giltza bikoitza erabili behar da {{ }} eta barruan erakutsi nahi dugun aldagaia edo funtzioa.

```php
Kaixo {{ $name }}.

Momentu honetako ordua da: {{ time() }}.
```

Php kode guztia giltza horen barruan idatzei behar dugu.

### Datua erakutsi soilik existitzekotan

Aldagai edo datu zehatz bat existitzen bada ziurtatzeko, hurrengo metodoa erabili dezakegu:

```php
{{ isset($name) ? $name : 'Lehenetsiko balioa' }}
```

Edo bestela ere,  blade-ek  duen notazio honen bidez:

```php
{{ $name or 'Lehenetsiko balioa' }}
```

###

### Php kodea sartzeko

Momentu baten gure bistan php kodea sartzeko, hurrengo etiketak erabili beharko ditugu:

```php
@php

    // Hemen kodea egongo litzateke

@endphp
```

### Iruzkinak

Laravelen barruan iruzkinak idazteko  {{-- eta --}} erabiliko dugu. Hauek iruzkinak ez dira HTML-n erakutsiko:

```php
{{-- Iruzkin hau ez da html-an erakutsiko --}}
```

###

### Kontrol estrukturak

Bladen IF estruktutak horrela erabili ditzakegu:

```php
@if( count($users) == 1 )

    Erabiltzaile bakarra dago!

@elseif (count($users) > 1)

    Erabiltzaile asko daude!

@else

    Ez dago erabiltzailerik :(

@endif
```

// (if(!condicion){})  If ezezkorra erabiltzeko, horrela izango litzateke

```php
@unless (Auth::check())

    You are not signed in.

@endunless
```

Bukleak (for, while eta foreach) horrela erabiliko genituzke blase barruan:

```php
@for ($i = 0; $i < 10; $i++)

    Aldagaiaren balioa da: {{ $i }}

@endfor
```

```php
@foreach ($users as $user)

    <p>Erabiltzailea {{ $user->name }} bere id-arekin: {{ $user->id }}</p>

@endforeach
```

```php
@forelse ($users as $user)

    <li>{{ $user->name }}</li>

@empty

    <p>Ez dago erabiltzailerik</p>

@endforelse
```

```php
@while (true)

    <p>Bukle infinito bat naiz!</p>

@endwhile
```

Aukera gehiago ikusteko, Laravelen estekan begiratu: <a href="https://laravel.com/docs/12.x/blade" target="_blank">https://laravel.com/docs/12.x/blade</a>

Txantiloi bat beste baten barruan sartu

Laravelen esan dezakegu, txantiloi bat beste barruan egon dadin, horretarako @include erabiliko dugu:

```php
@include('view_name')
```

Eta honi ere arraya pasatu diezaiokegu includeren 2. parametroan:

```php
@include('view_name', array('some'=>'data'))
```

Hau oso erabilgarria da behin baino gehiagotan erabiliko dugun kodea idazteko.

Hurrengoa: layouts
