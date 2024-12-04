# Model View Controller
## Tujuan Pembelajaran
1. Memahami konsep dasar MVC (Model-View-Controller).
2. Mampu memisahkan logika aplikasi, tampilan, dan pengolahan data.
3. Mengimplementasikan pola MVC menggunakan PHP untuk membuat aplikasi sederhana.

# Pendahuluan
MVC (Model-View-Controller) adalah pola desain (design pattern) yang memisahkan aplikasi menjadi tiga komponen utama:

- Model: Mengelola logika bisnis dan berinteraksi dengan database.
- View: Bertanggung jawab untuk tampilan atau antarmuka pengguna.
- Controller: Berfungsi sebagai perantara antara Model dan View, menangani input pengguna, dan memproses permintaan.

Manfaat MVC:

- Pemisahan logika dan tampilan.
- Memudahkan pengelolaan kode.
- Memungkinkan pengembangan tim lebih terstruktur.

# Analogi MVC

![Analog MVC](analogi.jpg)

# Langkah Implermentasi MVC
# 1. Struktur Folder MVC
```tree
nama folder kalian/
├── app/
│   ├── controllers/
│   │   └── StudentController.php
│   ├── models/
│   │   └── StudentModel.php
│   ├── views/
│   │   ├── students/
│   │       ├── create.php
│   │       ├── edit.php
│   │       └── index.php
│   │  
├── public/
│   └── index.php
├── config.php
```

# 2. Konfigurasi Awal

1. ***config.php***

```php
<?php
// Konfigurasi koneksi PDO ke database
define('DB_HOST', 'localhost');
define('DB_NAME', 'mvc');
define('DB_USER', 'root');
define('DB_PASS', '');

try {
    // Membuat koneksi ke database dengan PDO
    $pdo = new PDO("mysql:host=" . DB_HOST . ";dbname=" . DB_NAME, DB_USER, DB_PASS);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION); // Mengaktifkan error mode
} catch (PDOException $e) {
    die("Connection failed: " . $e->getMessage()); // Jika koneksi gagal
}
```
Fungsi: File ini digunakan untuk mengkonfigurasi koneksi ke database dengan PDO. Di sini, kita mendefinisikan pengaturan database (host, nama database, user, dan password) yang digunakan oleh seluruh aplikasi.


2. ***sql***

```sql
CREATE DATABASE mvc_basic;

USE mvc_basic;

CREATE TABLE students (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    major VARCHAR(50) NOT NULL,
    semester INT NOT NULL
);

```


# 3. Model 

***StudentModel.php***

```php
<?php

class StudentModel {
    private $pdo;

    public function __construct($pdo) {
        $this->pdo = $pdo;
    }

    // Mendapatkan semua data mahasiswa
    public function getAll() {
        $stmt = $this->pdo->query("SELECT * FROM students");
        return $stmt->fetchAll(PDO::FETCH_ASSOC);
    }

    // Menambahkan data mahasiswa baru
    public function add($name, $major, $semester) {
        $stmt = $this->pdo->prepare("INSERT INTO students (name, major, semester) VALUES (?, ?, ?)");
        return $stmt->execute([$name, $major, $semester]);
    }

    // Mengupdate data mahasiswa
    public function update($id, $name, $major, $semester) {
        $stmt = $this->pdo->prepare("UPDATE students SET name = ?, major = ?, semester = ? WHERE id = ?");
        return $stmt->execute([$name, $major, $semester, $id]);
    }

    // Menghapus data mahasiswa
    public function delete($id) {
        $stmt = $this->pdo->prepare("DELETE FROM students WHERE id = ?");
        return $stmt->execute([$id]);
    }
}

```
Model ini bertugas mengelola data mahasiswa dalam database. Di dalam model ini, kita menulis query untuk menambah, mengambil, mengubah, dan menghapus data mahasiswa, Model memisahkan logika untuk mengakses data, sehingga controller bisa fokus pada alur logika aplikasi, dan view hanya untuk menampilkan data.

# 4. Controller

***StudentController.php***

```php
<?php

require_once '../config.php'; // Menggunakan koneksi dari config.php
require_once '../app/models/StudentModel.php'; // Memanggil model

class StudentController {
    private $model;

    public function __construct() {
        $this->model = new StudentModel($GLOBALS['pdo']); // Inisialisasi model dengan PDO
    }

    // Menampilkan daftar mahasiswa
    public function index() {
        $students = $this->model->getAll();
        require_once '../app/views/students/index.php'; // Menampilkan view index
    }

    // Menambahkan data mahasiswa
    public function create() {
        if ($_SERVER['REQUEST_METHOD'] === 'POST') {
            $name = $_POST['name'];
            $major = $_POST['major'];
            $semester = $_POST['semester'];

            if ($this->model->add($name, $major, $semester)) {
                header("Location: index.php"); // Kembali ke halaman utama setelah menambah data
                exit();
            } else {
                echo "Failed to add student.";
            }
        } else {
            require_once '../app/views/students/create.php'; // Menampilkan form create
        }
    }

    // Menampilkan form edit dan mengupdate data mahasiswa
    public function edit($id) {
        if ($_SERVER['REQUEST_METHOD'] === 'POST') {
            $name = $_POST['name'];
            $major = $_POST['major'];
            $semester = $_POST['semester'];

            if ($this->model->update($id, $name, $major, $semester)) {
                header("Location: index.php"); // Kembali ke halaman utama setelah update
                exit();
            } else {
                echo "Failed to update student.";
            }
        } else {
            // Ambil data mahasiswa berdasarkan ID untuk pre-fill form
            $stmt = $this->model->getAll();
            $student = null;
            foreach ($stmt as $s) {
                if ($s['id'] == $id) {
                    $student = $s;
                    break;
                }
            }
            if ($student) {
                require_once '../app/views/students/edit.php'; // Menampilkan form edit
            } else {
                echo "Student not found.";
            }
        }
    }

    // Menghapus data mahasiswa
    public function delete($id) {
        if ($this->model->delete($id)) {
            header("Location: index.php"); // Kembali ke halaman utama setelah delete
            exit();
        } else {
            echo "Failed to delete student.";
        }
    }
}

```
Controller ini menangani permintaan pengguna dan menghubungkan antara model dan view. Setiap aksi (seperti create, edit, delete) akan diproses di controller ini. Setelah aksi diproses, controller akan menentukan view mana yang harus ditampilkan, Controller berfungsi mengatur flow dari aplikasi, menentukan aksi yang dilakukan, dan memanggil model serta view.

# 5. View

***index.php***

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Students List</title>
</head>
<body>
    <h1>Students List</h1>

    <!-- Navbar untuk navigasi -->
    <nav>
        <a href="index.php">Home</a> | 
        <a href="index.php?action=create">Add Student</a>
    </nav>

    <table border="1" cellspacing="0" cellpadding="10">
        <thead>
            <tr>
                <th>ID</th>
                <th>Name</th>
                <th>Major</th>
                <th>Semester</th>
                <th>Action</th>
            </tr>
        </thead>
        <tbody>
            <?php foreach ($students as $student): ?>
                <tr>
                    <td><?= $student['id'] ?></td>
                    <td><?= $student['name'] ?></td>
                    <td><?= $student['major'] ?></td>
                    <td><?= $student['semester'] ?></td>
                    <td>
                        <a href="index.php?action=edit&id=<?= $student['id'] ?>">Edit</a> |
                        <a href="index.php?action=delete&id=<?= $student['id'] ?>">Delete</a>
                    </td>
                </tr>
            <?php endforeach; ?>
        </tbody>
    </table>
</body>
</html>
```

***create.php***

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Add Student</title>
</head>
<body>
    <h1>Add New Student</h1>
    <form method="POST" action="index.php?action=create">
        <label for="name">Name:</label><br>
        <input type="text" id="name" name="name" required><br><br>

        <label for="major">Major:</label><br>
        <input type="text" id="major" name="major" required><br><br>

        <label for="semester">Semester:</label><br>
        <input type="number" id="semester" name="semester" required><br><br>

        <button type="submit">Add Student</button>
    </form>
    <br>
    <a href="index.php">Back to Students List</a>
</body>
</html>

```

***edit.php***

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Edit Student</title>
</head>
<body>
    <h1>Edit Student</h1>
    <form method="POST" action="index.php?action=edit&id=<?= $student['id'] ?>">
        <label for="name">Name:</label><br>
        <input type="text" id="name" name="name" value="<?= $student['name'] ?>" required><br><br>

        <label for="major">Major:</label><br>
        <input type="text" id="major" name="major" value="<?= $student['major'] ?>" required><br><br>

        <label for="semester">Semester:</label><br>
        <input type="number" id="semester" name="semester" value="<?= $student['semester'] ?>" required><br><br>

        <button type="submit">Update Student</button>
    </form>
    <br>
    <a href="index.php">Back to Students List</a>
</body>
</html>

```

# 6. Public

***index.php***

```php
<?php

require_once '../app/controllers/StudentController.php'; // Memanggil controller

$controller = new StudentController();

// Mendapatkan action yang ingin dijalankan
$action = $_GET['action'] ?? 'index';

// Jika method yang diminta ada di controller, jalankan
if (method_exists($controller, $action)) {
    $controller->$action($_GET['id'] ?? null);
} else {
    echo "404 Not Found"; // Menampilkan pesan error jika method tidak ditemukan
}

```

