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
project/
├── src/
│   ├── Animals/
│   │   ├── Cat.php
│   │   └── Dog.php
│   └── index.php
└── composer.json

2.  Buat File composer.json
Agar Composer bisa mengatur autoloading, kita perlu file composer.json di dalam folder project. Isi file composer.json seperti berikut:
```
{
    "autoload": {
        "psr-4": {
            "Animals\\": "src/Animals"
        }
    }
}
```
