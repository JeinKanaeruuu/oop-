# Modul PHP: Mengenal Konsep Namespace dan Autoload

## Pendahuluan
Dalam pengembangan aplikasi dengan PHP, seringkali kita akan memiliki banyak file, kelas, fungsi, dan konstanta. Hal ini dapat menyebabkan konflik nama ketika ada beberapa kelas atau fungsi dengan nama yang sama. Untuk mengatasi masalah tersebut, PHP menyediakan fitur **Namespace**. Selain itu, agar lebih mudah mengelola banyak file tanpa harus menyertakan (`include` atau `require`) secara manual, PHP mendukung fitur **Autoloading**.

Modul ini akan menjelaskan:
1. Konsep Namespace di PHP
2. Penerapan Namespace
3. Konsep Autoloading di PHP
4. Penerapan Autoloading dengan Composer

---

## 1. Konsep Namespace
**Namespace** adalah cara untuk mengelompokkan kode PHP sehingga tidak terjadi konflik nama (name conflicts), terutama ketika kita memiliki beberapa kelas atau fungsi dengan nama yang sama di dalam aplikasi.

### Manfaat Namespace
- **Mencegah Konflik Nama**: Dengan namespace, kita dapat memiliki kelas atau fungsi dengan nama yang sama di bagian lain dari aplikasi.
- **Memudahkan Organisasi Kode**: Namespace membantu kita mengelompokkan kode sesuai dengan fungsinya, misalnya dengan mengelompokkan kelas Model, Controller, dan lain-lain.

### Cara Membuat Namespace dan Autoload di PHP
Berikut adalah langkah-langkah dari awal : 

1. Buat susunan folder : 

```
project/
├── src/
│   ├── Animals/
│   │   ├── Cat.php
│   │   └── Dog.php
│   └── index.php
└── composer.json
```

2.  Buat File composer.json:

Agar Composer bisa mengatur autoloading, kita perlu file composer.json di dalam folder project. Isi file composer.json seperti berikut:
```json
{
    "autoload": {
        "psr-4": {
            "Animals\\": "src/Animals"
        }
    }
}
```
Setelah menulis file composer.json, jalankan perintah ini di terminal dari folder project untuk meng-generate file autoload:

```
composer install
```

3. Contoh Kode Namespace dan Autoloading: 

Pertama tama buat file Cat.php:
```php
<?php
namespace Animals;

class Cat {
    public function makeSound() {
        return "Meow!";
    }
}
```

Kedua buat file Dog.php: 
```php
<?php
namespace Animals;

class Dog {
    public function makeSound() {
        return "Woof!";
    }
}
```

Ketiga panggil Class di file index.php: 
```php
<?php
require __DIR__ . '/../vendor/autoload.php';

use Animals\Cat;
use Animals\Dog;

$cat = new Cat();
echo $cat->makeSound(); // Output: Meow!

$dog = new Dog();
echo $dog->makeSound(); // Output: Woof!
```

require __DIR__ . '/../vendor/autoload.php'; digunakan untuk memuat file autoload Composer, sehingga kelas Cat dan Dog dapat diakses langsung.

use Animals\Cat; dan use Animals\Dog; digunakan untuk memanggil kelas Cat dan Dog dari namespace Animals.

Lalu Terakhir jalankan kode di file index.php: 
```php
php index.php
```

Output:
```
Meow!
Woof!
```

## 2. Contoh kedua

Jika tanpa namespace maka akan muncul error. contohnya : 

LibraryA/User.php: 
```php
<?php
class User {
    public function sayHello() {
        return "Hello from LibraryA!";
    }
}
```

LibraryB/User.php:
```php
<?php
class User {
    public function sayHello() {
        return "Hello from LibraryB!";
    }
}
```

maka akan terjadi error seperti berikut: 

```
Fatal error: Cannot redeclare class User
```

Maka kita harus membuat namespace agar tidak terjadi error tersebut.

1. Buatlah susunan folder 

```
project/
├── src/
│   ├── LibraryA/
│   │   └── User.php
│   └── LibraryB/
│       └── User.php
    └── index.php
├── composer.json
└── vendor/
    └── autoload.php
```

2. Buat file composer.json:

```json
{
    "autoload": {
        "psr-4": {
            "LibraryA\\": "project/src/LibraryA/",
            "LibraryB\\": "project/src/LibraryB/"
        }
    }
}
```
Buatlah file autoload.php di folder project/src/libraryA dan libraryB, dengan isi seperti berikut:

LibraryA/User.php:
```php
<?php
namespace LibraryA;

class User {
    public function sayHello() {
        return "Hello from LibraryA!";
    }
}
```

LibraryB/User.php:
```php
<?php
namespace LibraryB;

class User {
    public function sayHello() {
        return "Hello from LibraryB!";
    }
}
```

index.php:
```php
<?php
require __DIR__ . '/../../vendor/autoload.php';// Memuat autoload dari Composer

use LibraryA\User as UserA;  // Alias untuk User dari LibraryA
use LibraryB\User as UserB;  // Alias untuk User dari LibraryB

$userA = new UserA();
echo $userA->sayHello(); // Output: Hello from LibraryA!

$userB = new UserB();
echo $userB->sayHello(); // Output: Hello from LibraryB!
```

## 3. Contoh Ketiga

1. Buatlah susunan folder

```
project/
├── src/
│   ├── LibraryA/
│   │   └── Book.php
│   ├── LibraryB/
│   │     └── Book.php
│   └── LibraryC/
│       └── Book.php
│   └── index.php
├── composer.json
└── vendor/
    └── autoload.php
```

2. Buat file composer.json:

```json
{
    "autoload": {
        "psr-4": {
            "LibraryA\\": "project/src/LibraryA/",
            "LibraryB\\": "project/src/LibraryB/",
            "LibraryC\\": "project/src/LibraryC/"
        }
    }
}
```

3. Buat file autoload.php di folder project/src/libraryA, libraryB, dan libraryC, dengan isi seperti berikut:

LibraryA/Book.php:
```php
<?php
namespace LibraryA;

class Book {
    private $title;
    private $author;
    private $year;

    public function __construct($title, $author, $year) {
        $this->title = $title;
        $this->author = $author;
        $this->year = $year;
    }

    public function getTitle() {
        return $this->title;
    }

    public function getAuthor() {
        return $this->author;
    }

    public function getYear() {
        return $this->year;
    }

    public function getBookInfo() {
        return "Title: {$this->title}, Author: {$this->author}, Year: {$this->year}";
    }
}
```

LibraryB/Book.php:
```php
<?php
namespace LibraryB;

class Book {
    private $title;
    private $author;
    private $year;

    public function __construct($title, $author, $year) {
        $this->title = $title;
        $this->author = $author;
        $this->year = $year;
    }

    public function getTitle() {
        return $this->title;
    }

    public function getAuthor() {
        return $this->author;
    }

    public function getYear() {
        return $this->year;
    }

    public function getBookInfo() {
        return "Title: {$this->title}, Author: {$this->author}, Year: {$this->year}";
    }
}
```

LibraryC/Book.php:
```php
<?php
namespace LibraryC;

class Book {
    private $title;
    private $author;
    private $year;

    public function __construct($title, $author, $year) {
        $this->title = $title;
        $this->author = $author;
        $this->year = $year;
    }

    public function getTitle() {
        return $this->title;
    }

    public function getAuthor() {
        return $this->author;
    }

    public function getYear() {
        return $this->year;
    }

    public function getBookInfo() {
        return "Title: {$this->title}, Author: {$this->author}, Year: {$this->year}";
    }
}
```

4. Buat file index.php:

```php
<?php
require __DIR__ . '/../../vendor/autoload.php'; // Memuat autoload dari Composer

use LibraryA\Book as BookA;
use LibraryB\Book as BookB;
use LibraryC\Book as BookC;

// Membuat objek buku dari LibraryA
$bookA = new BookA("The Great Gatsby", "F. Scott Fitzgerald", 1925);
echo $bookA->getBookInfo();  // Output: Title: The Great Gatsby, Author: F. Scott Fitzgerald, Year: 1925

echo "<br>";

// Membuat objek buku dari LibraryB
$bookB = new BookB("1984", "George Orwell", 1949);
echo $bookB->getBookInfo();  // Output: Title: 1984, Author: George Orwell, Year: 1949

echo "<br>";

// Membuat objek buku dari LibraryC
$bookC = new BookC("Brave New World", "Aldous Huxley", 1932);
echo $bookC->getBookInfo();  // Output: Title: Brave New World, Author: Aldous Huxley, Year: 1932
```


## 4. Kesimpulan
Dengan Namespace: Namespace menghindari konflik nama dan memungkinkan kita untuk mendefinisikan dua kelas dengan nama yang sama di ruang lingkup yang berbeda. Menggunakan Composer dengan autoload membuat pemuatan kelas menjadi otomatis dan lebih efisien.


Tanpa Namespace: Tanpa namespace, jika ada dua kelas dengan nama yang sama, kita akan mengalami konflik dan tidak bisa menggunakan keduanya dalam satu proyek.


## Tugas

buat penerapan Namespace dan Autoloading dengan Composer.terdiri dari dua bagian: FilmA, FilmB, FilmC, dan FilmD. Setiap Film memiliki atribut title, director, dan year. lalu panggil ketiganya di file index.php

Batas waktu pengerjaan: 15 Menit 
```
<=10 Menit = 100
>=10 menit | <=15 Menit = 86 
>15 Menit = 78
```
