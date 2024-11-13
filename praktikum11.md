# Praktikum 11 : Dasar-dasar PDO di PHP 

## Pendahuluan PDO
PDO (PHP Data Objects) adalah antarmuka (interface) PHP untuk mengakses berbagai database dengan cara yang aman, efisien, dan fleksibel. PDO mendukung berbagai jenis database seperti MySQL, PostgreSQL, SQLite, dan lainnya.

## Tujuan Pembelanjaan PDO
- Mengenal apa itu PDO dan mengapa penggunaannya dianjurkan.
- Menghubungkan aplikasi PHP ke database menggunakan PDO.
- Melakukan operasi CRUD (Create, Read, Update, Delete) dengan aman menggunakan PDO dan prepared statements.

## Mengapa Harus PDO ? 

1. Dukungan Multi-Database

- PDO: PDO mendukung berbagai jenis database seperti MySQL, PostgreSQL, SQLite, dan lain-lain. Kita hanya perlu mengganti string koneksi, dan sebagian besar kode tetap sama jika ingin berpindah database.

```php
<?php
try {
    $pdo = new PDO("mysql:host=localhost;dbname=testdb", "username", "password");
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

    $stmt = $pdo->prepare("SELECT * FROM users WHERE id = :id");
    $stmt->execute([':id' => 1]);
    $user = $stmt->fetch();
    echo $user['name'];
} catch (PDOException $e) {
    echo "Error: " . $e->getMessage();
}
```

- Tanpa PDO (MySQLi): Jika menggunakan MySQLi, kita hanya bisa terhubung dengan MySQL atau MariaDB. Jika berpindah ke database lain, kita perlu menyesuaikan seluruh kode.

```php
<?php
$conn = new mysqli("localhost", "username", "password", "testdb");

if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

$id = 1;
$stmt = $conn->prepare("SELECT * FROM users WHERE id = ?");
$stmt->bind_param("i", $id);
$stmt->execute();
$result = $stmt->get_result();
$user = $result->fetch_assoc();
echo $user['name'];

$stmt->close();
$conn->close();
```

# Koneksi ke Database
1. Buat Database di MySQL

```sql
CREATE DATABASE sekolah;
USE sekolah;

CREATE TABLE siswa (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nama VARCHAR(50),
    kelas VARCHAR(10),
    umur INT
);
```

2. Buat Koneksi Database Menggunakan PDO, Buat file baru bernama `koneksi.php`

```php
<?php
$host = 'localhost';
$dbname = 'sekolah';
$username = 'root';
$password = '';

try {
    $pdo = new PDO("mysql:host=$host;dbname=$dbname", $username, $password);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    echo "Koneksi berhasil!";
} catch (PDOException $e) {
    echo "Koneksi gagal: " . $e->getMessage();
}
```

3. Jalankan file `koneksi.php` dengan perintah `php koneksi.php` pada terminal atau melalui browser, jika sudah berhasil akan menampilkan pesan "Koneksi berhasil!" modifikasi kembali `koneksi.php` dengan menghapus Koneksi Berhasil! dan Koneksi Gagal! seperti ini 

```php
<?php
$host = 'localhost';
$dbname = 'sekolah';
$username = 'root';
$password = '';

try {
    $pdo = new PDO("mysql:host=$host;dbname=$dbname", $username, $password);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    echo "";
} catch (PDOException $e) {
    echo "" . $e->getMessage();
}
```

# CRUD dengan PDO 
1. Membuat Data (Create), buat file PHP bernama `create.php` untuk menambahkan data ke database

```php
<?php
require 'koneksi.php';

$nama = "Varel Maulana Ngurah Norbetino Siregar";
$kelas = "XII AMBATUKAM 1";
$umur = 69;

$sql = "INSERT INTO siswa (nama, kelas, umur) VALUES (:nama, :kelas, :umur)";
$stmt = $pdo->prepare($sql);
$stmt->bindParam(':nama', $nama);
$stmt->bindParam(':kelas', $kelas);
$stmt->bindParam(':umur', $umur);
$stmt->execute();

echo "Data berhasil ditambahkan!";
```

2. Membuat Data (Read), buat file PHP bernama `read.php` untuk membaca data dari database

```php
<?php
require 'koneksi.php';

$sql = "SELECT * FROM siswa";
$stmt = $pdo->query($sql);
$siswa = $stmt->fetchAll(PDO::FETCH_ASSOC);

foreach ($siswa as $row) {
    echo "ID: " . $row['id'] . " - Nama: " . $row['nama'] . " - Kelas: " . $row['kelas'] . " - Umur: " . $row['umur'] . "<br>";
}
```

3. Membuat Data (Update), buat file PHP bernama `update.php` untuk memperbarui data di database

```php
<?php
require 'koneksi.php';

$id = 1;  // Misalkan ID siswa yang ingin diperbarui
$namaBaru = "Amazia Raisha Haikal Malik Ibrahim";
$kelasBaru = "XII PENGERAJIN 1";

$sql = "UPDATE siswa SET nama = :nama, kelas = :kelas WHERE id = :id";
$stmt = $pdo->prepare($sql);
$stmt->bindParam(':nama', $namaBaru);
$stmt->bindParam(':kelas', $kelasBaru);
$stmt->bindParam(':id', $id);
$stmt->execute();

echo "Data berhasil diperbarui!";
```

4. Membuat Data (Delete), buat file PHP bernama `delete.php` untuk menghapus data di database

```php
<?php
require 'koneksi.php';

$id = 1;  // ID siswa yang ingin dihapus

$sql = "DELETE FROM siswa WHERE id = :id";
$stmt = $pdo->prepare($sql);
$stmt->bindParam(':id', $id);
$stmt->execute();

echo "Data berhasil dihapus!";
```

