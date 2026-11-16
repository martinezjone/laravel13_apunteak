# Konponenteak

Konponentea (osagaia) hainbat bistatan berrerabil dezakegun Blade txantiloi bat da. Adibidez, alerta baten HTMLa behin definitu dezakegu, eta gero orri bakoitzean mezu desberdina erakutsi osagai bera erabilita.

[Laravel 13ko dokumentazioa](https://laravel.com/docs/13.x/blade#components)

## Konponente anonimo bat sortzea

Sortu `resources/views/components/alert.blade.php` fitxategia. Konponente anonimo batek Blade bista bat dauka.

```blade
<div class="alerta" role="alert">
    <strong>Alerta!</strong> Kontuz!
</div>
```

Beste bista batean erabiltzeko, gehitu `x-` aurrizkia fitxategiaren izenari eta kendu `.blade.php` luzapena.

```blade
<x-alert />
```

Edukirik pasatu behar ez badugu, etiketa berean itxi dezakegu. Edukia pasatzeko, hasierako eta amaierako etiketak erabiliko ditugu.

## Slot lehenetsia

Konponentearen etiketen artean idatzitako edukia `$slot` aldagaiaren bidez jasotzen da. Eduki hori testua edo HTMLa izan daiteke.

Aldatu `alert.blade.php` fitxategia.

```blade
<div class="alerta" role="alert">
    <strong>Alerta!</strong>
    {{ $slot }}
</div>
```

Orain, alerta erabiltzen dugun bakoitzean mezu bat eman diezaiokegu.

```blade
<x-alert>
    Egiaztatu sartutako datuak.
</x-alert>
```

## Izendun slotak

Mezua eta izenburua konponentearen leku desberdinetan txertatzeko, izenburua izendun slot gisa pasa dezakegu. Adibide honetan, `<x-slot:title>` barruko edukia `$title` bidez jasotzen da eta gainerakoa `$slot` bidez.

```blade
<x-alert>
    <x-slot:title>
        Datuak gordetzeko arazoa
    </x-slot:title>

    Egiaztatu sartutako datuak.
</x-alert>
```

```blade
<div class="alerta" role="alert">
    <strong>{{ $title ?? 'Alerta!' }}</strong>
    {{ $slot }}
</div>
```

Bistak `title` slota ematen ez badu, «Alerta!» agertuko da.

## Konponenteen parametroak

Etiketan atributuak ere pasa ditzakegu. Adibidez, `type` erabil dezakegu alertaren itxura aukeratzeko.

```blade
<x-alert type="danger">
    <x-slot:title>
        Datuak gordetzeko arazoa
    </x-slot:title>

    Egiaztatu sartutako datuak.
</x-alert>
```

Konponente anonimoaren hasieran, `@props` direktibaren bidez adieraziko dugu zein atributu jaso nahi dugun. `@props(['type' => 'info'])` idatzita, osagaiak `$type` aldagaia izango du. Deian `type` ematen ez badugu, `info` erabiliko da.

Hona hemen `resources/views/components/alert.blade.php` fitxategi osoa.

```blade
@props(['type' => 'info'])

@php
    $klasea = match ($type) {
        'danger' => 'alerta-danger',
        'success' => 'alerta-success',
        default => 'alerta-info',
    };
@endphp

<div {{ $attributes->class(['alerta', $klasea]) }} role="alert">
    <strong>{{ $title ?? 'Alerta!' }}</strong>
    {{ $slot }}
</div>
```

Koloreak ikusteko, gehitu estilo hauek layoutari lotutako `public/css/style.css` fitxategian.

```css
.alerta {
    padding: 1rem;
    border-radius: 0.5rem;
}

.alerta-info {
    background-color: #dbeafe;
    color: #1e40af;
}

.alerta-danger {
    background-color: #fee2e2;
    color: #991b1b;
}

.alerta-success {
    background-color: #dcfce7;
    color: #166534;
}
```

## Atributu gehigarriak

`@props` bidez jasotzen ez diren atributuak `$attributes` bidez ditugu eskura. Adibidez, alerta jakin bati `id` bat eta CSS klase gehigarri bat eman diezazkiokegu.

```blade
<x-alert type="info" id="gordetze-mezua" class="nabarmendua">
    Aldaketak gordetzeko, sakatu «Gorde».
</x-alert>
```

`type` atributua `$type` aldagai gisa jasotzen da. `id` eta `class`, berriz, `$attributes` barruan daude. `$attributes->class(...)` erabilita, deian pasatutako `nabarmendua` klasea konponentearen `alerta` eta `alerta-info` klaseei gehitzen zaie. `id` atributua ere `<div>` elementuan txertatzen da.

## Klase bidezko konponenteak

Konponenteak PHP logika gehiago behar duenean, PHP klase bat eta Blade bista bat erabil ditzakegu. Adibidea probatzeko, sortu `Alert2` konponentea.

```bash
php artisan make:component Alert2
```

Komandoak `app/View/Components/Alert2.php` klasea eta `resources/views/components/alert2.blade.php` bista sortzen ditu. Klasearen eraikitzailean jasotako propietate publikoak Blade bistan erabil daitezke. Adibidez, alertaren CSS klasea eraikitzailean kalkula dezakegu.

```php
<?php

namespace App\View\Components;

use Illuminate\Contracts\View\View;
use Illuminate\View\Component;

class Alert2 extends Component
{
    public string $klasea;

    public function __construct(string $type = 'info')
    {
        $this->klasea = match ($type) {
            'danger' => 'alerta-danger',
            'success' => 'alerta-success',
            default => 'alerta-info',
        };
    }

    public function render(): View
    {
        return view('components.alert2');
    }
}
```

`resources/views/components/alert2.blade.php` fitxategian, `$klasea` zuzenean erabil dezakegu.

```blade
<div {{ $attributes->class(['alerta', $klasea]) }} role="alert">
    <strong>{{ $title ?? 'Alerta!' }}</strong>
    {{ $slot }}
</div>
```

```blade
<x-alert2 type="success">
    Datuak ondo gorde dira.
</x-alert2>
```

## Layouta konponente gisa erabiltzea

Layouten atalean `@extends`, `@section` eta `@yield` erabili ditugu orrien egitura komuna partekatzeko. Egitura bera konponente anonimo batean ere defini dezakegu. Kasu honetan, orriaren eduki nagusia `$slot` bidez jasoko dugu.

Sortu `resources/views/components/app-layout.blade.php` fitxategia.

```blade
<!DOCTYPE html>
<html lang="eu">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ $title ?? 'Nire bloga' }}</title>
    <link rel="stylesheet" href="{{ asset('css/style.css') }}">
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
        {{ $slot }}
    </main>

    @include('partials.footer')
</body>
</html>
```

Layouten atalean sortutako `resources/views/partials/footer.blade.php` berrerabili dugu orri-oina txertatzeko. Orain, `resources/views/home.blade.php` fitxategian layouta erabil dezakegu.

```blade
<x-app-layout>
    <x-slot:title>Hasiera</x-slot:title>

    <h1>Ongi etorri nire blogera!</h1>
    <p>Hau da hasierako orria.</p>

    <x-alert type="success">
        Datuak ondo gorde dira.
    </x-alert>
</x-app-layout>
```

`title` slotak nabigatzaileko fitxaren izenburua betetzen du. Gainerako edukia layoutaren `<main>` elementuan txertatzen da.
