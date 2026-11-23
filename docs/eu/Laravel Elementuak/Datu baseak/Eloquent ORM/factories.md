# Factories

## Zer dira Factory-ak?

**Factory-ak** Laravel-ek probetarako datuak sortzeko eskaintzen dituen klaseak dira. Normalean, garapen-prozesuan erabiltzen dira, datu-basea fikziozko datuekin betetzeko.

Aurreko atalean **Seeder-ak** ikusi ditugu. Bi osagaiak datuak sortzeko erabiltzen badira ere, bakoitzak funtzio desberdina du:

- **Seeder-ak:** datu-basean zer datu sartu eta datuen karga nola antolatu zehazten dute.
- **Factory-ak:** modelo baten atributuak nola sortu definitzen dute, normalean ausazko datuak erabiliz.

Beraz, biak batera erabil ditzakegu: **Seeder batek Factory bati deitu diezaioke datu asko automatikoki sortzeko.**

Adibidez, `posts` taulan 100 erregistro sortu nahi baditugu, ez dugu bakoitza eskuz idatzi beharrik izango. Factory baten bidez automatikoki sor ditzakegu.

!!! note
    Laravel-eko proiektu berrietan, normalean, `UserFactory` klasea sortuta egoten da.

    Gure kasuan, `Post` modelorako Factory berri bat sortuko dugu.

## Factory bat sortzea

Factory berri bat sortzeko, Artisan komando hau erabiliko dugu:

```bash
php artisan make:factory PostFactory
```

Komando honek `database/factories` karpetan `PostFactory.php` fitxategia sortuko du.

Sortutako klaseak honako egitura izango du:

```php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class PostFactory extends Factory
{
    public function definition(): array
    {
        return [
            //
        ];
    }
}
```

Ikus dezakegunez, `PostFactory` klasea Laravel-en `Factory` klasetik hedatzen da.

**`definition()` metodoan zehaztuko dugu modeloaren atributu bakoitzak zer balio izango duen.**

## Datuak sortzea Faker erabiliz

Laravel-ek **FakerPHP** liburutegia erabiltzen du fikziozko datuak sortzeko.

Faker-ek hainbat motatako datuak sor ditzake automatikoki: izenak, helbide elektronikoak, telefono-zenbakiak, testuak, datak, zenbakiak eta abar.

Horretarako, `fake()` funtzioa erabil dezakegu.

Gure `PostFactory` klasean, `posts` taulako `title` eta `content` eremuak beteko ditugu:

```php
public function definition(): array
{
    return [
        'title' => fake()->sentence(),
        'content' => fake()->paragraph(),
    ];
}
```

Kasu honetan:

- **`fake()->sentence()`**: ausazko esaldi bat sortzen du, `title` eremurako.
- **`fake()->paragraph()`**: ausazko paragrafo bat sortzen du, `content` eremurako.

Factory-a erabiltzen dugun bakoitzean, Faker-ek balio berriak sortuko ditu.

### Faker-en metodo ohikoenak

Faker-ek datu mota ugari sortzeko aukera ematen du. Hauek dira erabilgarrienetako batzuk:

| Metodoa | Sortzen duen datua |
|---|---|
| `fake()->name()` | Izen-abizenak |
| `fake()->firstName()` | Izena |
| `fake()->lastName()` | Abizena |
| `fake()->safeEmail()` | Fikziozko helbide elektronikoa |
| `fake()->sentence()` | Esaldi bat |
| `fake()->paragraph()` | Paragrafo bat |
| `fake()->text(200)` | Gehienez 200 karaktereko testua |
| `fake()->numberBetween(1, 100)` | 1 eta 100 arteko zenbaki osoa |
| `fake()->randomFloat(2, 1, 100)` | Bi hamartarreko zenbakia, 1 eta 100 artean |
| `fake()->date()` | Data bat |
| `fake()->boolean()` | `true` edo `false` balioa |
| `fake()->unique()->safeEmail()` | Errepikatzen ez den fikziozko helbide elektronikoa |

!!! tip "Faker-en dokumentazioa"
    Faker-ek askoz metodo gehiago eskaintzen ditu. Sortu daitezkeen datu motak eta formatuak dokumentazio ofizialean kontsulta ditzakezue:

    [FakerPHP dokumentazioa](https://fakerphp.org/)

## Factory-a modeloarekin lotzea

Factory-a erabiltzeko, gure `Post` modeloak **`HasFactory`** trait-a erabili behar du.

Horretarako, `app/Models/Post.php` fitxategia ireki eta honela definituko dugu:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;

class Post extends Model
{
    use HasFactory;

    protected $fillable = ['title', 'content'];
}
```

`HasFactory` trait-ari esker, `Post` modelotik dagokion Factory-a erabili ahal izango dugu.

```php
Post::factory()
```

Laravel-ek izendapen-konbentzioak erabiliz `Post` modeloa `PostFactory` klasearekin erlazionatuko du.

!!! note "Izendapen-konbentzioak"
    Laravel-ek automatikoki erlazionatzen ditu `Post` modeloa eta `PostFactory` klasea, izen horiek erabiltzen baditugu.

    Horregatik, komeni da Factory-ak `ModeloarenIzenaFactory` egiturarekin izendatzea.

## Factory-ak erabiltzea

Factory-a sortu eta modeloarekin lotu ondoren, erregistroak automatikoki sor ditzakegu.

Horretarako, Eloquent-ek eskaintzen dituen `factory()`, `count()`, `make()` eta `create()` metodoak erabiliko ditugu.

### Erregistroak sortzea: `make()` eta `create()`

Bi metodo horien arteko desberdintasuna garrantzitsua da.

**`make()` metodoa:**

```php
$posts = Post::factory()->count(10)->make();
```

Hamar `Post` objektu sortzen ditu, baina **ez ditu datu-basean gordetzen**.

**`create()` metodoa:**

```php
$posts = Post::factory()->count(10)->create();
```

Hamar `Post` objektu sortzen ditu eta **datu-baseko `posts` taulan gordetzen ditu**.

| Metodoa | Objektuak sortzen ditu? | Datu-basean gordetzen ditu? |
|---|---|---|
| `make()` | Bai | Ez |
| `create()` | Bai | Bai |

### Zenbat erregistro sortu nahi ditugu?

`count()` metodoaren bidez, zenbat erregistro sortu nahi ditugun adieraz dezakegu.

Adibidez:

```php
Post::factory()->count(10)->create();
```

Hamar erregistro sortuko ditu.

```php
Post::factory()->count(100)->create();
```

Ehun erregistro sortuko ditu.

Erregistro bakarra sortu nahi badugu, ez dugu `count()` erabili beharrik:

```php
Post::factory()->create();
```

## Factory-ak DatabaseSeeder-etik erabiltzea

Normalean, Factory-ak `DatabaseSeeder` klasetik erabiliko ditugu, datu-basea probetarako datuekin betetzeko.

Horretarako, `database/seeders/DatabaseSeeder.php` fitxategian `Post` modeloa inportatuko dugu:

```php
use App\Models\Post;
```

Ondoren, `run()` metodoan Factory-ari deituko diogu:

```php
public function run(): void
{
    Post::factory()->count(10)->create();
}
```

Kasu honetan, `php artisan db:seed` exekutatzen dugunean, hamar erregistro berri sortuko dira `posts` taulan.

### Seeder-ak eta Factory-ak batera erabiltzea

Aurreko atalean `PostSeeder` sortu genuen, datuak eskuz sartzeko.

Orain, Seeder hori eta Factory-a batera erabil ditzakegu:

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\Post;

class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        // Eskuz definitutako datuak sartu
        $this->call(PostSeeder::class);

        // Ausazko 10 erregistro sortu
        Post::factory()->count(10)->create();
    }
}
```

Kasu honetan:

1. Lehenengo, `PostSeeder` exekutatuko da eta bertan definitutako bi erregistroak sartuko dira.
2. Ondoren, `PostFactory` erabilita, beste hamar erregistro sortuko dira.

Beraz, hasieran `posts` taula hutsik badago, guztira **12 erregistro** izango ditugu.

## Datu-basea betetzea

Behin `DatabaseSeeder` prestatuta, datuak sartzeko komando hau erabiliko dugu:

```bash
php artisan db:seed
```

Komando honek Seeder-ak exekutatuko ditu eta horietatik deitutako Factory-ek erregistroak sortuko dituzte.

Datu-basea hutsetik berriro sortu eta probetarako datuekin bete nahi badugu:

```bash
php artisan migrate:fresh --seed
```

Komando honek:

1. Datu-baseko taula guztiak ezabatzen ditu.
2. Migrazio guztiak exekutatzen ditu.
3. `DatabaseSeeder` exekutatzen du, eta bertan definitutako Seeder-ak eta Factory-ak erabiltzen ditu.

!!! warning "Kontuz!"
    `migrate:fresh --seed` komandoak datu-baseko taula guztiak eta bertan gordetako datuak ezabatzen ditu.

    Garapen-ingurunean erabiltzeko egokia da, baina ez datuak mantendu behar ditugunean.

!!! tip
    `php artisan db:seed` hainbat aldiz exekutatzen badugu, Factory-ak erregistro berriak sortuko ditu aldi bakoitzean.

    Datu-basea hasierako egoerara itzuli eta berriro bete nahi badugu, `migrate:fresh --seed` erabil dezakegu garapen-ingurunean.

Dokumentazio ofiziala: [Eloquent Factories – Laravel 13](https://laravel.com/docs/13.x/eloquent-factories)