# TxurdiGest · Datu-basea

Lehenengo `students` taula prestatuko dugu, hurrengo atalean ikasleen CRUDa egiteko. Irakasleak, moduluak eta matrikulak gero gehituko ditugu.

## MySQL konfiguratu

Abiarazi MySQL MAMPen eta sortu `txurdigest` izeneko datu-base hutsa phpMyAdminen. Egokitu `.env` fitxategia zure instalazioko ataka eta kredentzialekin.

```dotenv
APP_NAME=TxurdiGest
APP_LOCALE=eu
APP_FALLBACK_LOCALE=en

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=txurdigest
DB_USERNAME=root
DB_PASSWORD=root //MAMP erabiltzen dugulako
```

## Migrazioa

Behin datu basearen konfigurazioa eginda dugula, migrazioa sortuko dugu, aurrekoan ikusi genuen bezela, horrela egingo genuke:

```bash
php artisan make:migration create_students_taula
```

Baina artisanek beste aukera bat ematen digu, modeloa eta migrazioa aldi bereaz sortzea, horretarako, modeloa sortuko dugu eta "-m" jartzean bere migrazioa ere sortuko du:

```bash
php artisan make:model Student -m
```

### create_students_table.php migrazioa

`database/migrations/…_create_students_table.php` fitxategia hartu eta ondorengo elementuak sartuko ditugu: name eta emaila

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('students', function (Blueprint $table) {
            $table->id();
            $table->string('name', 100);
            $table->string('email')->unique();
            $table->date('birth_date');
            $table->string('city', 100);
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('students');
    }
};
```

Orain migrazioa exekutatuko dugu:

```bash
php artisan migrate
```

## Student Modeloa

`app/Models/Student.php`

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Student extends Model
{
    use HasFactory;

    protected $fillable = ['name', 'email'];
}
```

Eloquentek `Student` modeloa `students` taularekin lotzen du. `$fillable` zerrendak `create()` eta `update()` deietan batera esleitu daitezkeen atributuak zehazten ditu.

## Seederra eta factorya

Seederrarekin datu ezagunak sartuko ditugu. Factoryarekin, zerrenda eta paginazioa probatzeko erregistroak sortuko ditugu.

```bash
php artisan make:seeder StudentSeeder
php artisan make:factory StudentFactory --model=Student
```

`database/seeders/StudentSeeder.php`

```php
<?php

namespace Database\Seeders;

use App\Models\Student;
use Illuminate\Database\Seeder;

class StudentSeeder extends Seeder
{
    public function run(): void
    {
        Student::firstOrCreate(['email' => 'jon@example.test'], ['name' => 'Jon Bilbao']);
        Student::firstOrCreate(['email' => 'ane@example.test'], ['name' => 'Ane Etxeberria']);
    }
}
```

`database/factories/StudentFactory.php`

```php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;

class StudentFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->name(),
            'email' => Str::uuid() . '@example.test',
        ];
    }
}
```

`database/seeders/DatabaseSeeder.php` fitxategian ordezkatu hasierako `run()` metodoa. Gehitu `use App\Models\Student;` eta kendu erabili gabe geratzen den `User` inportazioa.

```php
public function run(): void
{
    $this->call(StudentSeeder::class);

    if (Student::count() < 20) {
        Student::factory()->count(20 - Student::count())->create();
    }
}
```

```bash
php artisan db:seed
php artisan tinker
```

Tinker-en, probatu `App\Models\Student::where('email', 'jon@example.test')->first();`. Irten `exit` idatzita. `migrate:fresh --seed` aginduak taula guztiak ezabatzen eta berreraikitzen ditu; erabili soilik ariketako datuak berariaz berrabiarazi nahi dituzunean.

[Laravel 13 · Migrazioak](https://laravel.com/docs/13.x/migrations) · [Seederrak](https://laravel.com/docs/13.x/seeding) · [Factoryak](https://laravel.com/docs/13.x/eloquent-factories)

[Aurrekoa · Proiektua sortu](proiektu-bat-sortu.md) | [Hurrengoa · Ikasleen CRUDa](all-show-create-edit-delete-adibideekin.md)
