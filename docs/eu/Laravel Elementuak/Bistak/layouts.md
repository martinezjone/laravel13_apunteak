# Layouts

Layouta hainbat bistak partekatzen duten oinarrizko txantiloia da. Bertan jartzen dugu orrialdeen egitura komuna, hala nola goiburua, nabigazio-menua eta orri-oina. Bista bakoitzak bere edukia definitzen du, eta Blade-k layoutaren barruan dagokion lekuan txertatzen du.

## Layouta sortzea

Sortu `resources/views/layouts/app.blade.php` fitxategia HTMLaren egitura orokorrarekin.

```blade
<!DOCTYPE html>
<html lang="eu">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@yield('title', 'Nire bloga')</title>
</head>
<body>
    <header>
        <p>Nire bloga</p>
        <nav>
            <a href="/">Hasiera</a>
            <a href="/users">Erabiltzaileak</a>
        </nav>
    </header>

    <main>
        @yield('content')
    </main>

    <footer>
        <p>Webgunearen orri-oina</p>
    </footer>
</body>
</html>
```

`@yield` direktibak atal bat layoutaren barruan non txertatuko den adierazten du. Lehenengo argumentua atalaren izena da. Bigarren argumentua aukerakoa da, eta balio lehenetsia zehazteko balio du.

Adibidez, bistak `title` atala definitzen ez badu, nabigatzaileko fitxan «Nire bloga» agertuko da.

## Txantiloi bat luzatzea

Bista batek layout bat erabiltzeko, `@extends` direktiba erabiliko dugu. Adibidez, sortu `resources/views/home.blade.php` fitxategia.

```blade
@extends('layouts.app')

@section('title', 'Hasiera')

@section('content')
    <h1>Ongi etorri nire blogera!</h1>
    <p>Hau da hasierako orria.</p>
@endsection
```

`layouts.app` izenak `resources/views/layouts/app.blade.php` fitxategia adierazten du. Puntuak karpeta eta bistaren izena bereizten ditu. Blade dei horretan fitxategiaren luzapena kenduta idazten dugu izena.

`@section` direktibak atalaren edukia definitzen du. Atalaren izenak layoutaren `@yield` direktiban erabilitako izenarekin bat etorri behar du. Edukia testu laburra bada, bigarren argumentu gisa eman dezakegu. HTMLa edo hainbat lerro idatzi behar baditugu, atala `@section` bidez ireki eta `@endsection` bidez itxiko dugu.

Layout bera beste bista batean erabiltzeko, sortu `resources/views/users.blade.php` fitxategia.

```blade
@extends('layouts.app')

@section('title', 'Erabiltzaileak')

@section('content')
    <h1>Erabiltzaileak</h1>

    <ul>
        @forelse ($users as $user)
            <li>{{ $user->name }}</li>
        @empty
            <li>Ez dago erabiltzailerik.</li>
        @endforelse
    </ul>
@endsection
```

Bi bistak probatzeko, `routes/web.php` fitxategian gehitu ibilbide hauek.

```php
use Illuminate\Support\Facades\Route;

Route::view('/', 'home');

Route::get('/users', function () {
    $users = [
        (object) ['name' => 'Jon'],
        (object) ['name' => 'Ane'],
    ];

    return view('users', compact('users'));
});
```

Ireki `/` eta `/users` nabigatzailean. Bi orriek goiburu, menu eta orri-oin bera dute. Kendu `@section('title', 'Erabiltzaileak')` lerroa eta egiaztatu nabigatzaileko fitxan «Nire bloga» agertzen dela.

## Partials

Layoutaren zati batzuk, hala nola menua edo orri-oina, aparteko fitxategietan gorde ditzakegu. Fitxategi horiei **bista partzialak** edo *partials* esaten zaie. Beste bista batean txertatzeko, `@include` direktiba erabiliko dugu.

Adibidez, atera orri-oina `resources/views/partials/footer.blade.php` fitxategira.

```blade
<footer>
    <p>Webgunearen orri-oina</p>
</footer>
```

Ondoren, ordezkatu layoutaren `<footer>` blokea lerro honekin.

```blade
@include('partials.footer')
```

## asset() laguntzailea

Webgune osoko estiloak `public/css/style.css` fitxategian baditugu, estilo-orria layoutaren `<head>` atalean lotu dezakegu.

```blade
<link rel="stylesheet" href="{{ asset('css/style.css') }}">
```

`asset()` laguntzaileak fitxategi publiko baten URLa sortzen du. Argumentu gisa, fitxategiaren bidea ematen diogu, public karpetatik hasita. Adibidez, `public/css/style.css` fitxategia lotzeko, `asset('css/style.css')` erabiltzen dugu. Horrela, layout bera erabiltzen duten orrialde guztiek CSS fitxategiaren URL bera jasotzen dute.

## @stack direktiba

Bista jakin batek CSS edo JavaScript fitxategi gehigarri bat behar badu (layout-en jarri dugunaren aparte), `@stack` eta `@push` erabil ditzakegu. 

Hori egiteko, gehituko genuke `@stack('styles')` layoutaren `<head>` atalean, estilo-orri komunaren ondoren.

Layoutaren <head> atalean, orri guztiek behar duten CSSa lotzen dugu, eta estilo gehigarriak non txertatuko diren adierazten dugu, horretarako erabiltzen dugu `@stack('styles')`.


```blade
    <link rel="stylesheet" href="{{ asset('css/style.css') }}">

    @stack('styles')
```

`users.blade.php` bistan, `users.css` kargatzeko `<link>` etiketa gehitzen dugu styles pilara. Blokea `@extends` lerroaren ondoren jar dezakezu.

```blade
    @extends('layouts.app')

    @push('styles')
        <link rel="stylesheet" href="{{ asset('css/users.css') }}">
    @endpush
```

`@push('styles')`-ek zer gehitu adierazten du, eta `@stack('styles')`-ek non txertatu. Horregatik, `users.blade.php` bistan idatzitako `<link>` etiketa azken HTMLaren `<head> ` atalean agertzen da. Hasierako bistak styles pilara ezer gehitzen ez badu, `@stack('styles')`-ek ez du ezer txertatzen.

