# Praktikum 13 : Validasi Kelas

### Tujuan Praktikum
1. Mengimplementasikan validasi input dengan membuat kelas validasi (validator).
2. Menerapkan CRUD (Create) untuk menyimpan data ke database setelah validasi.

# 1. Membuat Database dan Tabel
```sql
CREATE DATABASE php_validation;
```

```sql
USE php_validation;

CREATE TABLE feedback (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(32) NOT NULL,
    email VARCHAR(64) NOT NULL,
    message TEXT NOT NULL
);
```

# 2. Koneksi Database (File koneksi.php)
Menggunakan PDO untuk koneksi database, yang lebih aman dan fleksibel alias lebih cepet dan gampang.
PDOException digunakan untuk menangani error jika koneksi gagal.
```php
<?php
try {
    // Menggunakan PDO untuk koneksi database
    $pdo = new PDO("mysql:host=localhost;dbname=php_validation", "root", "");
    // Menetapkan mode error ke Exception
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
    // Jika koneksi gagal, tampilkan pesan error
    die("Database connection failed: " . $e->getMessage());
}
?>
```

#  3. Membuat Kelas Validator (File Validator.php)
Kelas ini digunakan untuk memvalidasi data input berdasarkan aturan yang telah ditentukan.
```php
<?php
class Validator {
    private $errors = [];

    // Fungsi untuk melakukan validasi data
    public function validate($data, $rules) {
        foreach ($rules as $rule) {
            $field = $rule['field'];
            $label = $rule['label'];
            $fieldRules = explode('|', $rule['rules']);

            foreach ($fieldRules as $fieldRule) {
                if ($fieldRule === 'required' && empty($data[$field])) {
                    $this->errors[$field][] = "$label is required.";
                }

                if (str_starts_with($fieldRule, 'max_length')) {
                    preg_match('/max_length\[(\d+)\]/', $fieldRule, $matches);
                    $maxLength = $matches[1] ?? 0;
                    if (strlen($data[$field]) > $maxLength) {
                        $this->errors[$field][] = "$label must not exceed $maxLength characters.";
                    }
                }

                if ($fieldRule === 'valid_email' && !filter_var($data[$field], FILTER_VALIDATE_EMAIL)) {
                    $this->errors[$field][] = "$label must be a valid email address.";
                }
            }
        }
        return empty($this->errors);
    }

    // Fungsi untuk mengambil error yang ditemukan
    public function getErrors() {
        return $this->errors;
    }
}
?>
```
1. Validator memeriksa setiap aturan validasi (misalnya required, max_length, dan valid_email).
2. validate(): Melakukan pengecekan terhadap data yang diterima dan aturan yang telah ditentukan.
3. getErrors(): Mengembalikan daftar error yang ditemukan selama validasi.

# 4. Membuat Model (File FeedbackModel.php)
Kelas ini bertanggung jawab untuk validasi dan penyimpanan data ke database.
```php
<?php
require_once 'Validator.php';

class FeedbackModel {
    private $pdo;

    // Konstruktor untuk menerima koneksi PDO
    public function __construct($pdo) {
        $this->pdo = $pdo;
    }

    // Menentukan aturan validasi untuk feedback
    public function rules() {
        return [
            [
                'field' => 'name',
                'label' => 'Name',
                'rules' => 'required|max_length[32]'
            ],
            [
                'field' => 'email',
                'label' => 'Email',
                'rules' => 'required|valid_email|max_length[64]'
            ],
            [
                'field' => 'message',
                'label' => 'Message',
                'rules' => 'required'
            ]
        ];
    }

    // Menyimpan data feedback ke dalam database
    public function saveFeedback($data) {
        $sql = "INSERT INTO feedback (name, email, message) VALUES (:name, :email, :message)";
        $stmt = $this->pdo->prepare($sql);
        $stmt->execute([
            ':name' => $data['name'],
            ':email' => $data['email'],
            ':message' => $data['message'],
        ]);
    }
}
?>
```
1. FeedbackModel mengelola data feedback, termasuk aturan validasi dan menyimpannya ke database.
2. rules(): Mengembalikan array aturan validasi untuk masing-masing field (name, email, message).
3. saveFeedback(): Menyimpan data feedback ke dalam tabel feedback

# 5. Menggunakan Model dan Validasi di File index.php
File utama yang menangani input data, validasi, dan penyimpanan ke database.

```php
<?php

require_once 'koneksi.php';
require_once 'classes/FeedbackModel.php';

$feedbackModel = new FeedbackModel($pdo);
$validator = new Validator();

// Simulasi data input
$data = [
    'name' => $_POST['name'] ?? '',
    'email' => $_POST['email'] ?? '',
    'message' => $_POST['message'] ?? '',
];

// Jika form disubmit
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $rules = $feedbackModel->rules();

    if ($validator->validate($data, $rules)) {
        // Data valid, simpan ke database
        $feedbackModel->saveFeedback($data);
        echo "Feedback submitted successfully!";
    } else {
        // Data tidak valid, tampilkan error
        $errors = $validator->getErrors();
        foreach ($errors as $field => $fieldErrors) {
            foreach ($fieldErrors as $error) {
                echo $error . "<br>";
            }
        }
    }
}
?>

<!DOCTYPE html>
<html>
<head>
    <title>Feedback Form</title>
</head>
<body>
    <h1>Feedback Form</h1>
    <form method="POST" action="">
        <label for="name">Name:</label><br>
        <input type="text" id="name" name="name"><br><br>

        <label for="email">Email:</label><br>
        <input type="email" id="email" name="email"><br><br>

        <label for="message">Message:</label><br>
        <textarea id="message" name="message"></textarea><br><br>

        <button type="submit">Submit</button>
    </form>
</body>

    <!-- Tampilkan Data -->
    <h2>Feedback List</h2>
    <ul>
        <?php
        $stmt = $pdo->query("SELECT * FROM feedback");
        while ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
            echo "<li><strong>{$row['name']}</strong>: {$row['message']} ({$row['email']})</li>";
        }
        ?>
    </ul>
    
</html>

```


# 6. T U G A S 

1. Buatlah aplikasi berbasis web yang dapat menerima masukan dari pengguna melalui form, memvalidasi data yang dimasukkan, menyimpannya ke dalam database, dan menampilkan data yang telah disimpan. Aplikasi ini harus memiliki form yang memungkinkan pengguna untuk memasukkan data feedback (Nama, Email, dan Pesan). Validasi input harus dilakukan sebelum data disimpan ke dalam database.

2. Setelah form disubmit, jika semua data valid, data akan disimpan dan ditampilkan dalam bentuk tabel seperti : 
```kotlin
Feedback submitted successfully!

ID   | Name  | Email            | Message
----------------------------------------------
1    | Jokowi  | jokowigaming@example.com  | This is a feedback message.
2    | Prabowo  | prabowogaming@example.com  | Another feedback message.
```
3. Jika terjadi kesalahan input, seperti email tidak valid atau nama kosong, akan ditampilkan pesan kesalahan berikut:

    Email harus valid email address.

    Name is dibutuhkan.

    Message is dibutuhkan.

4. Tambahkan Bootstrap Untuk Output Tablenya

5. Rubik Penilaian 

    Implementasi Koneksi Database: 20%

    Implementasi Validasi Input: 30%

    Desain dan Tampilan Web: 30%

    Waktu Pengerjaan : 

        1. <= 15 Menit : 20%

        2. > 15 Menit : 10%
        
