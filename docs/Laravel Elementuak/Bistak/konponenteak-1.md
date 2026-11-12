# Konponenteak

Blade konponenteak Laravel-ek eskaintzen duen mekanismo bat dira HTML edo Blade kodea berrerabilgarri bihurtzeko.

Askotan erabiltzen dira:

- Diseinuko elementuak behin definitu eta orri guztietan erabiltzeko.
- Formulario zatiak berrerabiltzeko (input, button, card…).
- Layout egiturak mantentzeko.
- Mezuak, alertak edo modalak modu bateratuan erakusteko.

Konponenteak resources > views > components karpetan gordetzen dira, eta blade.php luzapena izango dute.

Konponente motak

Konponenteak bi motatakoak izan daitezke:

- Konponente klasikoak (class-based components)

Konponenteak klase propioa dauka (PHP fitxategia), logika edo kodea barruan eduki dezakeena, kasu honetan ere logika eta diseinua bereizten dira

- Konponente soilak (anonymous components)

Hauek aldiz, ez daukate klase-fitxategirik, bakarrik Blade fitxategia, konponente sinple eta txikientzako erabiltzen da.

Konponente bat sortu

- Konponente klasikoak (class-based components)

```bash
php artisan make:component Alert
```

Sortzen du:

- app/View/Components/Alert.php ← logika
- resources/views/components/alert.blade.php ← HTMLa

Adibidez:

Alert.php:

```php
public function __construct(public $type = 'success')

{

}
```

HTMLa:

```php
<div class="alert alert-{{ $type }}">

{{ $slot }}

</div>
```

Erabilera:

```html
<x-alert type="error">Errorea gertatu da</x-alert>
```

- Konponente soilak (anonymous components)

Hauek aldiz eskuz sortuko ditugu:

resources/views/components/alert.blade.php

HTMLa:

```php
@props(['type' => 'success'])

<div class="alert alert-{{ $type }}">

{{ $slot }}

</div>
```

Erabilera (aurrekoaren berdina):

```html
<x-alert type="error">Errorea gertatu da</x-alert>
```

Parametroak pasatu

Parametroak props erabiliz pasatuko ditugu.

Konponentea adibidez horrelako zeozer izan zitekeen:

```php
{{-- alert.blade.php --}}

@props(['message', 'type' => 'success'])

<div>

{{ $type }} - {{ $message }}

</div>
```

Eta konponenteari deitzerakoan horrela egingo genuke:

```html
<x-alert :type="$mota" :message="$variableBat" />
```
