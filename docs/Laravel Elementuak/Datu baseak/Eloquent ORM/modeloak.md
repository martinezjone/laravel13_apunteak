# mODELOAK

Modeloak, MVC aplikazioen barruan datuekin lan egiten duten konponenteak dira. Datuak hainbat tokikoak izan daitezke, APIak, fitxategi sistema batekoak etab., baina normalean datu baseetako datuekin lan egingo dugu.

Modeloak sortzeko, hurrengo instrukzioa idatziko dugu:

```bash
php artisan make:model Post
```

Modeloa sortzerakoan, ikusi dezakegu, Eloquent erabiltzen duela, honi ezker, DB-ko taulak klase baten moduan erabili ditzakegu.

Eloquent dokumentazioa: <https://laravel.com/docs/12.x/eloquent>

Sortutako klasea, Post gure kasuan, Eloquent-en Models klasetik hedatzen da:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model

{

//

}
```

Honi ezker, Models klaseko metodo guztiak erabili ditzakegu: all(), find(), create(), update() etab.

Post::all();

Modeloan, gure taularen ezarpenak ezarriko ditugu:

```php
protected $table = "posts";

protected $primaryKey = "id";

protected $fillable = ['title', 'content', 'categoria'];

protected $hidden = ['id'];
```

Modeloan, kontroladoreak erabiliko dituen funtzioak ere sortu ditzakegu, baina normalena eta hoberena kontroladorean egitea izango da:

```php
public function postakLortu(){

return Post::all();

}

public function showPost($id){

return Post::find($id);

}
```

OHARRA: Nahi izatekotan gure modeloak Eloquent konbeniotik kanpo dagoen taula bat erabiltzea nahi baldin badugu, hau da, Ikasle modeloak, ikasleak taularekin lan egitean, ikasles taularekin lan egin ordez, adierazi beharko genioke $table propietatearen bidez:

```php
protected $table = "ikasleak";
```

Hurrengoa: Seeders
