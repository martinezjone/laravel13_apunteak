# Erlazioak

## Zer dira erlazioak?

Orain arte, datu-baseko taulak sortzen, modeloak erabiltzen eta datuak sartzen ikasi dugu. Baina aplikazio batean, normalean, **taulak ez dira independenteak izaten**, elkarren artean erlazionatuta egoten dira.

Adibidez, blog batean:

- Erabiltzaile batek hainbat post idatz ditzake.
- Post bakoitza erabiltzaile bakar batena da.
- Post batek hainbat kategoria izan ditzake, eta kategoria batek hainbat post izan ditzake.

Erlazio horiek datu-basean **gako primarioen (primary key)** eta **atzerriko gakoen (foreign key)** bidez adierazten dira.

Laravel-ek, Eloquent ORM erabiliz, erlazio horiekin lan egiteko metodoak eskaintzen dizkigu. Horrela, erlazionatutako datuak erraz lor ditzakegu, SQL kontsulta konplexuak eskuz idatzi gabe.

## Erlazio motak

Datu-baseetan hainbat erlazio mota daude. Laravel-en ohikoenak hauek dira:

| Erlazioa | Esanahia | Adibidea |
|---|---|---|
| **1-1 (One to One)** | Erregistro bat beste taula bateko erregistro bakar batekin erlazionatzen da. | Erabiltzaile batek profil bat du. |
| **1-N (One to Many)** | Erregistro batek beste taula bateko hainbat erregistro izan ditzake. | Erabiltzaile batek hainbat post ditu. |
| **N-M (Many to Many)** | Taula bateko hainbat erregistro beste taula bateko hainbat erregistrorekin erlaziona daitezke. | Post batek hainbat kategoria izan ditzake, eta kategoria batek hainbat post. |

### 1-1 erlazioa (One to One)

Erregistro batek beste taula bateko erregistro bakar bat izan dezake.

Adibidez, `users` eta `profiles` taulen artean:

- Erabiltzaile batek profil bat izan dezake.
- Profil bakoitza erabiltzaile bakar bati dagokio.

Kasu honetan, `profiles` taulan `user_id` atzerriko gakoa izango dugu.

### 1-N erlazioa (One to Many)

Erregistro batek beste taula bateko hainbat erregistro izan ditzake, baina horietako bakoitza lehenengo taulako erregistro bakar bati dagokio.

Adibidez, `users` eta `posts` taulen artean:

- Erabiltzaile batek hainbat post idatz ditzake.
- Post bakoitza erabiltzaile bakar batena da.

Horretarako, `posts` taulan `user_id` atzerriko gakoa izango dugu.

### N-M erlazioa (Many to Many)

Taula bateko erregistro batek beste taula bateko hainbat erregistrorekin erlazioa izan dezake, eta alderantziz.

Adibidez, `posts` eta `categories` taulen artean:

- Post batek hainbat kategoria izan ditzake.
- Kategoria batek hainbat post izan ditzake.

Kasu honetan, **tarteko taula bat (pivot table)** behar dugu erlazioak gordetzeko.

Adibidez, `category_post` taula erabiliko dugu, `post_id` eta `category_id` zutabeekin.

## Erlazioak Eloquent-en

Laravel-en, erlazioak **modeloetako metodoen bidez** definitzen dira.

Erlazio motaren arabera, Eloquent-ek metodo desberdinak eskaintzen ditu:

| Erlazioa | Modelo batean | Erlazionatutako modeloan |
|---|---|---|
| 1-1 | `hasOne()` | `belongsTo()` |
| 1-N | `hasMany()` | `belongsTo()` |
| N-M | `belongsToMany()` | `belongsToMany()` |

Adibidez, erabiltzaile baten postak lortzeko, `User` modeloan `hasMany()` erabiliko genuke:

```php
public function posts()
{
    return $this->hasMany(Post::class);
}
```

Eta `Post` modeloan, post bakoitza zein erabiltzaileri dagokion adierazteko, `belongsTo()` erabiliko genuke:

```php
public function user()
{
    return $this->belongsTo(User::class);
}
```

Horrela, erlazioak definituta daudenean, erabiltzaile baten post guztiak lor ditzakegu:

```php
$user = User::find(1);

$posts = $user->posts;
```

Eta post baten erabiltzailea ere bai:

```php
$post = Post::find(1);

$user = $post->user;
```

!!! note "Erlazioen izenak"
    Konbentzioz, erlazio-metodoen izenak honela idazten dira:

    - **Singularrean**, erregistro bakarra itzultzen duten erlazioetan: `user()`, `profile()`.
    - **Pluralean**, hainbat erregistro itzultzen dituzten erlazioetan: `posts()`, `categories()`.

    Erlazioak erabiltzean, metodoaren izena propietate gisa erabil dezakegu, parentesirik gabe: `$user->posts`.

## Migrazioak eta modeloak

Garrantzitsua da bereiztea non definitzen den erlazioaren atal bakoitza:

- **Migrazioetan:** datu-baseko atzerriko gakoak eta beharrezko tarteko taulak sortzen ditugu.
- **Modeloetan:** Eloquent-en erlazioak definitzen ditugu, `hasMany()`, `belongsTo()` eta antzeko metodoen bidez.

!!! tip
    Erlazioak Eloquent-en definitzeak ez du automatikoki datu-baseko atzerriko gakoa sortzen. Horretarako, migrazioak erabili behar ditugu.

Hurrengo adibide praktikoan ikusiko dugu nola sortu erlazio horiek migrazioen bidez, nola definitu modeloetan eta nola erabili kontroladoreetatik.

Dokumentazio ofiziala: [Eloquent Relationships – Laravel 13](https://laravel.com/docs/13.x/eloquent-relationships)