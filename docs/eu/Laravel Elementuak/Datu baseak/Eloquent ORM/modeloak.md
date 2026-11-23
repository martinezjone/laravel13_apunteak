# Modeloak

## Zer dira modeloak?

Modeloak **MVC (Model-View-Controller)** arkitekturaren barruan datuekin lan egiteko erabiltzen diren osagaiak dira.

Datuak hainbat iturritatik etor daitezke, hala nola APIetatik, fitxategietatik edo datu-baseetatik. Gure kasuan, normalean **datu-baseetako datuekin** lan egingo dugu.

Laravel-ek **Eloquent ORM** (*Object-Relational Mapping*) erabiltzen du datu-basearekin lan egiteko.

Eloquent-i esker, datu-baseko taulak PHP klaseen bidez erabil ditzakegu, eta taula horietako erregistroak objektu moduan kudeatu. Horrela, ez dugu SQL kontsultarik zuzenean idatzi behar ohiko eragiketak egiteko.

Adibidez, `posts` taulako erregistro guztiak lortzeko, SQL erabilita honako kontsulta egingo genuke:

```sql
SELECT * FROM posts;
```

Eloquent erabilita, berriz:

```php
$posts = Post::all();
```

Bi kasuetan, `posts` taulako erregistro guztiak lortuko ditugu, baina Eloquent-ek `Post` modeloko objektuen bilduma bat itzuliko digu.

Dokumentazio ofiziala: [Eloquent ORM – Laravel 13](https://laravel.com/docs/13.x/eloquent)

## Modeloa sortzea

Modelo berri bat sortzeko, Artisan komando hau erabiliko dugu:

```bash
php artisan make:model Post
```

Komando honek `app/Models` karpetan `Post.php` fitxategia sortuko du.

Sortutako klaseak honako egitura izango du:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    //
}
```

Ikus dezakegunez, `Post` klasea Eloquent-en `Model` klasetik hedatzen da (`extends Model`).

Horri esker, `Model` klaseak eskaintzen dituen metodoak erabil ditzakegu, adibidez, `all()`, `find()`, `create()`, `update()` edo `delete()`.

!!! note "Kontuan izan"
    `make:model` komandoak modeloa sortzen du, baina **ez du datu-baseko taula sortzen**.

    Taula sortzeko, migrazio bat erabili beharko dugu, aurreko atalean ikusi dugun bezala.

## Modeloen eta taulen arteko erlazioa

Laravel-ek izendapen-konbentzio batzuk erabiltzen ditu modeloak datu-baseko taulekin automatikoki erlazionatzeko.

Adibidez, `Post` modeloa sortzen dugunean, Eloquent-ek `posts` izeneko taularekin lan egingo duela suposatzen du.

Konbentzio nagusiak hauek dira:

| Elementua | Konbentzioa | Adibidea |
|---|---|---|
| Modeloa | Singularrean eta PascalCase formatuan | `Post` |
| Taula | Pluralean, minuskulaz eta snake_case formatuan | `posts` |
| Gako primarioa | `id` | `id` |
| Sortze-data | `created_at` | `created_at` |
| Eguneratze-data | `updated_at` | `updated_at` |

Beraz, `Post` modeloa erabiltzen dugunean, Laravel-ek automatikoki `posts` taula bilatuko du, eta erregistro bakoitzaren identifikatzailea `id` zutabean dagoela suposatuko du.

### Zer gertatzen da konbentzioak jarraitzen ez baditugu?

Demagun `Post` modeloa dugula, baina datu-baseko taularen izena `blog_entries` dela.

Kasu horretan, Laravel-i zein taula erabili behar duen adierazi beharko diogu, `$table` propietatea erabiliz:

```php
class Post extends Model
{
    protected $table = 'blog_entries';
}
```

Horrela, `Post` modeloak `blog_entries` taularekin lan egingo du, `posts` taularekin egin beharrean.

!!! tip
    Laravel-en izendapen-konbentzioak jarraitzen baditugu, ez dugu `$table` propietatea definitu beharrik izango.

    Horregatik, komeni da modeloen eta taulen izenak konbentzio horien arabera sortzea.

## Modeloen propietateak

Modeloaren barruan hainbat propietate defini ditzakegu, Eloquent-ek datu-basearekin nola lan egingo duen zehazteko.

Ohikoenetako batzuk hauek dira:

| Propietatea | Deskribapena |
|---|---|
| `$table` | Modeloari dagokion datu-baseko taularen izena zehazten du. |
| `$primaryKey` | Taularen gako primarioaren izena zehazten du. |
| `$fillable` | Esleipen masiboaren bidez (`create()`, `update()`...) bete daitezkeen atributuak zehazten ditu. |

### `$table` eta `$primaryKey`

Lehen ikusi dugun bezala, Laravel-ek konbentzio batzuk erabiltzen ditu. Hala ere, beharrezkoa bada, balio horiek alda ditzakegu:

```php
protected $table = 'posts';
protected $primaryKey = 'id';
```

Kasu honetan, bi propietateak soberan egongo lirateke, Laravel-ek lehenespenez balio horiek erabiltzen dituelako.

### `$fillable`

`$fillable` propietateak **esleipen masiboaren bidez zein atributu bete edo aldatu daitezkeen** zehazten du.

Esleipen masiboa hainbat atributuren balioak aldi berean esleitzea da, array bat erabiliz.

Adibidez, `posts` taulak `title` eta `content` zutabeak baditu, honela defini dezakegu modeloa:

```php
class Post extends Model
{
    protected $fillable = ['title', 'content'];
}
```

Orain, erregistro berri bat sortzeko, `create()` metodoa erabil dezakegu:

```php
Post::create([
    'title' => 'My first post',
    'content' => 'This is the content of my first post.'
]);
```

Eloquent-ek arrayko datuak hartu eta `posts` taulan erregistro berri bat sortuko du.

!!! warning "Zergatik da garrantzitsua `$fillable`?"
    Demagun gure taulak `is_admin` izeneko zutabe bat duela.

    Erabiltzaile batek formulario baten bidez datuak bidaltzen baditu, ez genuke nahi `is_admin` atributua nahi bezala aldatzeko aukera izatea.

    `$fillable` erabilita, esleipen masiboaren bidez alda daitezkeen atributuak mugatu ditzakegu.

    Kontuan izan `$fillable` **ez dela datuen balidazioa**: horretarako, formulariotik jasotako datuak aparte balidatu beharko ditugu.

## Eloquent-en oinarrizko metodoak

Eloquent-ek hainbat metodo eskaintzen dizkigu datu-baseko erregistroak kontsultatzeko, sortzeko, aldatzeko eta ezabatzeko.

Hauek dira gehien erabiliko ditugun metodoetako batzuk:

| Metodoa | Deskribapena |
|---|---|
| `Post::all()` | Taulako erregistro guztiak lortzen ditu. |
| `Post::find(1)` | Gako primarioaren bidez erregistro bat bilatzen du. Ez badago, `null` itzultzen du. |
| `Post::findOrFail(1)` | Erregistro bat bilatzen du. Ez badago, salbuespen bat sortzen du, Laravel-ek normalean 404 erantzun bihurtzen duena. |
| `Post::where('title', 'Laravel')->get()` | Baldintza bat betetzen duten erregistroak lortzen ditu. |
| `Post::create([...])` | Erregistro berri bat sortzen du. |
| `$post->update([...])` | Existitzen den erregistro bat eguneratzen du. |
| `$post->delete()` | Erregistro bat ezabatzen du. |

### Erregistro guztiak lortu: `all()`

`posts` taulako erregistro guztiak lortzeko:

```php
$posts = Post::all();
```

Metodo honek `Post` objektuen bilduma bat (*Collection*) itzuliko du.

Adibidez, kontroladore batean:

```php
public function index()
{
    $posts = Post::all();

    return view('posts.index', compact('posts'));
}
```

Kasu honetan, erregistro guztiak lortu eta `posts.index` bistara bidaltzen ditugu.

### Erregistro bat bilatu: `find()`

Erregistro bat bere IDaren bidez bilatzeko:

```php
$post = Post::find(1);
```

Honek `id = 1` duen erregistroa bilatuko du.

Erregistroa existitzen ez bada, `null` itzuliko du.

Bestalde, `findOrFail()` erabil dezakegu:

```php
$post = Post::findOrFail(1);
```

Kasu honetan, erregistroa existitzen ez bada, Laravel-ek normalean **404 errorea** erakutsiko du web eskaera batean.

### Baldintza batekin bilatu: `where()`

Baldintza bat betetzen duten erregistroak lortzeko, `where()` erabil dezakegu:

```php
$posts = Post::where('title', 'Laravel')->get();
```

Kontsulta honek `title` zutabean `Laravel` balioa duten erregistro guztiak lortuko ditu.

!!! note
    `where()` metodoak kontsulta prestatzen du, baina `get()` metodoak exekutatzen du eta emaitzak bilduma batean itzultzen ditu.

    Erregistro bakarra nahi badugu, `first()` ere erabil dezakegu.

## Non idatzi behar ditugu kontsultak?

MVC arkitekturan, kontroladoreek erabiltzailearen eskaerak kudeatzen dituzte, eta modeloek datuekin lan egiteko aukera eskaintzen dute.

Eragiketa sinpleak egiteko, kontroladoreek zuzenean erabil ditzakete Eloquent-en metodoak.

Adibidez:

```php
use App\Models\Post;

public function index()
{
    $posts = Post::all();

    return view('posts.index', compact('posts'));
}
```

Hemen:

1. Kontroladoreak `Post` modeloa erabiltzen du.
2. Eloquent-ek datu-baseko erregistroak lortzen ditu.
3. Kontroladoreak datuak bistara bidaltzen ditu.

Bestalde, datuekin lotutako logika propioa edo berrerabilgarria behar badugu, modeloan ere metodoak defini ditzakegu.

Oraingoz, Eloquent-ek eskaintzen dituen oinarrizko metodoak erabiliko ditugu zuzenean kontroladoreetatik. Aurrerago, CRUD aplikazio bat garatzean, erregistroak sortzeko, eguneratzeko eta ezabatzeko eragiketak sakonago landuko ditugu.

Hurrengoa: Seeders