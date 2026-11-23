# Seederrak

## Zer dira Seederrak?

**Seeder-ak** Laravel-ek datu-basean datuak sartzeko eskaintzen dituen klaseak dira.

Normalean, bi egoeratan erabiltzen dira:

- **Garapen-prozesuan:** aplikazioa probatzeko adibidezko datuak sartzeko.
- **Aplikazioaren hasieran:** aplikazioak funtzionatzeko behar dituen hasierako datuak sartzeko, adibidez, kategoriak edo rolak.

Aurreko ataletan ikusi dugun bezala, **migrazioek datu-baseko taulen egitura sortzen dute**, eta **modeloek taula horietako datuekin lan egiteko aukera ematen digute**.

Seeder-en bidez, berriz, taula horietan datuak sartuko ditugu.

## Seeder bat sortzea

Seeder berri bat sortzeko, Artisan komando hau erabiliko dugu:

```bash
php artisan make:seeder PostSeeder
```

Komando honek `database/seeders` karpetan `PostSeeder.php` fitxategia sortuko du.

Sortutako klaseak honako egitura izango du:

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class PostSeeder extends Seeder
{
    public function run(): void
    {
        //
    }
}
```

Ikus dezakegunez, `PostSeeder` klasea Laravel-en `Seeder` klasetik hedatzen da.

**`run()` metodoan idatziko dugu datu-basean zer datu sartu nahi ditugun.** Metodo hori Seeder-a exekutatzen dugunean erabiliko da.

## Datuak sartzea

Datuak sartzeko, aurretik sortutako `Post` modeloa erabiliko dugu.

Horretarako, lehenik modeloaren erreferentzia gehituko dugu fitxategiaren hasieran:

```php
use App\Models\Post;
```

Eloquent erabilita, datu-baseko erregistroak objektu moduan kudeatu ditzakegu.

Datuak sartzeko hainbat modu daude. Bi aukera ikusiko ditugu.

### 1. Objektu bat sortu eta `save()` erabiltzea

Lehenengo aukera `Post` klaseko objektu bat sortzea eta bere propietateak banan-banan betetzea da.

```php
public function run(): void
{
    $post = new Post();

    $post->title = 'Nire lehenego Post-a';
    $post->content = 'Hau izango da nire Post-aren edukia';

    $post->save();
}
```

Kode honetan:

1. `new Post()` erabilita, `Post` modeloko objektu berri bat sortzen dugu.
2. `$post->title` eta `$post->content` erabilita, objektuaren atributuei balioak esleitzen dizkiegu.
3. `$post->save()` erabilita, erregistroa datu-baseko `posts` taulan gordetzen dugu.

!!! note
    `new Post()` instrukzioak objektua memorian sortzen du, baina ez du oraindik datu-basean gordetzen.

    Erregistroa datu-basean sartzeko, `save()` metodoa exekutatu behar dugu.

### 2. `create()` metodoa erabiltzea

Beste aukera bat Eloquent-en `create()` metodoa erabiltzea da.

Metodo honen bidez, erregistro berri bat sortu eta datu-basean gorde dezakegu instrukzio bakar batean.

```php
public function run(): void
{
    Post::create([
        'title' => 'Nire lehenego Post-a',
        'content' => 'Hau izango da nire Post-aren edukia'
    ]);

    Post::create([
        'title' => 'Bigarren Post-a',
        'content' => 'Hau izango da nire bigarren Post-aren edukia'
    ]);
}
```

Kasu honetan, bi erregistro sortuko dira `posts` taulan.

!!! warning "Gogoratu `$fillable`"
    `create()` metodoak esleipen masiboa erabiltzen duenez, `Post` modeloan zein atributu bete daitezkeen zehaztu behar dugu.

    ```php
    protected $fillable = ['title', 'content'];
    ```

    `save()` erabilita atributuak banan-banan esleitzen ditugunean, aldiz, `$fillable` ez da aplikatzen.

### `PostSeeder` osoa

Beraz, `create()` erabilita, gure Seeder-ak honako itxura izango luke:

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\Post;

class PostSeeder extends Seeder
{
    public function run(): void
    {
        Post::create([
            'title' => 'Laravel ikasten',
            'content' => 'Laravelen modeloak eta migrazioak ikasten'
        ]);

        Post::create([
            'title' => 'Laravel aurreratua',
            'content' => 'Beste Laravel post-a iznago da'
        ]);
    }
}
```

## DatabaseSeeder

Laravel-ek `database/seeders` karpetan **`DatabaseSeeder.php`** izeneko fitxategia sortzen du lehenespenez.

Fitxategi hau Seeder nagusia da, eta beste Seeder batzuk exekutatzeko erabiltzen da.

`php artisan db:seed` komandoa exekutatzen dugunean, Laravel-ek `DatabaseSeeder` klaseko `run()` metodoari deitzen dio lehenespenez.

Gure `PostSeeder` exekutatzeko, `DatabaseSeeder` klasearen barruan honako hau idatziko dugu:

```php
public function run(): void
{
    $this->call(PostSeeder::class);
}
```

Horrela, `DatabaseSeeder` exekutatzean, `PostSeeder` ere exekutatuko da.

### Seeder bat baino gehiago exekutatzea

Aplikazio batean hainbat Seeder izan ditzakegu, adibidez:

- `UserSeeder`
- `PostSeeder`
- `CategorySeeder`

Horiek guztiak `DatabaseSeeder` klasetik exekutatzeko, `call()` metodoari array bat pasa diezaiokegu:

```php
public function run(): void
{
    $this->call([
        UserSeeder::class,
        CategorySeeder::class,
        PostSeeder::class,
    ]);
}
```

Seeder-ak **arrayan adierazitako ordenan** exekutatuko dira.

Beste aukera bat `call()` metodoari hainbat aldiz deitzea da:

```php
public function run(): void
{
    $this->call(UserSeeder::class);
    $this->call(CategorySeeder::class);
    $this->call(PostSeeder::class);
}
```

Bi aukerek emaitza bera izango dute.

!!! tip "Exekuzio-ordena"
    Seeder-en ordena garrantzitsua izan daiteke taulen artean erlazioak daudenean.

    Adibidez, `posts` taulako erregistro bakoitzak erabiltzaile baten IDa behar badu, lehenengo erabiltzaileak sortu beharko ditugu, eta ondoren postak.

## Seederrak exekutatzea

Seeder-ak sortu eta `DatabaseSeeder` klasean erregistratu ondoren, datu-basean datuak sartzeko komando hau erabiliko dugu:

```bash
php artisan db:seed
```

Komando honek `DatabaseSeeder` klaseko `run()` metodoa exekutatuko du eta bertatik deitutako Seeder guztiak ere bai.

### Seeder zehatz bat exekutatzea

Seeder bakar bat exekutatu nahi badugu, `--class` aukera erabil dezakegu:

```bash
php artisan db:seed --class=PostSeeder
```

Kasu honetan, `PostSeeder` bakarrik exekutatuko da, `DatabaseSeeder` klasetik pasatu gabe.

### Migrazioak eta Seederrak batera exekutatzea

Garapen-prozesuan, askotan datu-baseko taulak berriro sortu eta adibidezko datuak sartu nahi izaten ditugu.

Horretarako, komando hau erabil dezakegu:

```bash
php artisan migrate:fresh --seed
```

Komando honek hiru urrats egiten ditu:

1. Datu-baseko taula guztiak ezabatzen ditu.
2. Migrazio guztiak berriro exekutatzen ditu, taulak sortzeko.
3. `DatabaseSeeder` exekutatzen du, taulak datuekin betetzeko.

!!! danger "Kontuz!"
    `migrate:fresh --seed` komandoak datu-baseko taula guztiak ezabatzen ditu, eta horiekin batera gordetako datuak ere bai.

    Horregatik, garapen-inguruneetan erabiltzeko egokia da, baina ez datuak mantendu behar ditugunean.

!!! warning "Seeder-ak behin baino gehiagotan exekutatzea"
    `php artisan db:seed` komandoak ez ditu aurretik sartutako datuak automatikoki ezabatzen.

    Beraz, Seeder bera hainbat aldiz exekutatzen badugu, erregistroak errepikatu daitezke.

    Adibidez, gure `PostSeeder`-ak bi erregistro sortzen baditu, bi aldiz exekutatzean lau erregistro izango ditugu, aurretik taula hutsik bazegoen eta murriztapenek eragozten ez badute.

## Seeder-en komandoen laburpena

| Komandoa | Zer egiten du? |
|---|---|
| `php artisan make:seeder PostSeeder` | Seeder berri bat sortzen du. |
| `php artisan db:seed` | `DatabaseSeeder` eta bertatik deitutako Seeder-ak exekutatzen ditu. |
| `php artisan db:seed --class=PostSeeder` | Seeder zehatz bat exekutatzen du. |
| `php artisan migrate:fresh --seed` | Taula guztiak ezabatu, migrazioak exekutatu eta datuak sartzen ditu. |

Dokumentazio ofiziala: [Database Seeding – Laravel 13](https://laravel.com/docs/13.x/seeding)

Hurrengoa: Factories