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

### Cara Membuat Namespace
Namespace dideklarasikan dengan menggunakan keyword `namespace`, yang harus ditulis di bagian atas file PHP sebelum kode lain.
