# Tugas Terakhir

## Membuat Aplikasi CRUD Sederhana dengan MVC

### Deskripsi Tugas
Buatlah sebuah aplikasi CRUD (Create, Read, Update, Delete) menggunakan pola arsitektur MVC untuk manajemen buku. Aplikasi ini harus memungkinkan pengguna untuk menambah, melihat, mengedit, dan menghapus data buku yang tersimpan di database.

Langkah-Langkah: 
1. Buat Struktur MVC:
```tree
app/
    controllers/
        BookController.php
    models/
        BookModel.php
    views/
        books/
            index.php
            create.php
            edit.php
            show.php
public/
    index.php
config.php
```

2. Database:
```sql
CREATE TABLE books (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    author VARCHAR(255) NOT NULL,
    year INT NOT NULL,
    genre VARCHAR(100)
);
```

3. Controller dan Model:

- Tulis model untuk menangani interaksi dengan database, seperti mengambil, menambah, memperbarui, dan menghapus data buku
- Buat controller untuk menangani permintaan pengguna (misalnya, menampilkan daftar buku, menambah buku, mengedit buku, dan menghapus buku).

4. View:
- index.php: Tampilkan daftar buku dengan opsi untuk mengedit dan - menghapus buku.
- create.php: Formulir untuk menambah buku baru.
- edit.php: Formulir untuk mengedit informasi buku yang sudah ada.