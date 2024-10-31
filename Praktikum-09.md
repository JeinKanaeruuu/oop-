# Praktikum 10 Pemrograman Berorientasi Objek

---

Setelah mengetahui terkait dasar dari OOP dengan menggunakan PHP, pada praktikum kali ini kita akan mencoba melakukan implementasi oop dengan menggunakan php untuk melakukan CRUD. Hal ini agar pengorganisasian kode menjadi lebih baik dari praktikum CRUD sebelumnya.

Sebelum memulai membuat CRUD dengan mengggunakan OOP PHP kita akan membuat struktur file seperti ini
```
├── oop-php/
    ├── index.php
    ├── classes/
    |   ├── Database.php
    |   ├── CRUD.php
    ├── templates/
    |   ├── index.html

```

### 1. Database.php
Setelah selesai membuat struktur file, maka selanjutnya kita akan menulis kode pada file ```Database.php```. File ini berfunsgi untuk melakukan konektivitas ke dalam database.

Catatan: Pada praktikum ini pastikan anda telah membuat Database dan tabel users dengan isi tabelnya adalah:
1. id (PRIMARY KEY)
2. nama(VARCHAR)
3. email(VARCHAR)

Kemudian berikut ini adalah kode dari ```Database.php```
```php
<?php
class Database {
    private $host = 'localhost';
    private $username = 'root';
    private $password = '';
    private $dbname = 'oop';
    protected $conn;

    public function __construct() {
        $this->conn = new mysqli($this->host, $this->username, $this->password, $this->dbname);
        if ($this->conn->connect_error) {
            die("Koneksi database gagal: " . $this->conn->connect_error);
        }
    }
}
```

### 2. CRUD.php
File ```CRUD.php``` digunakan untuk membuat penanganan CRUD dengan menggunakan OOP.
```php
<?php
class CRUD extends Database {
    public function create($name, $email) {
        $query = "INSERT INTO users (name, email) VALUES ('$name', '$email')";
        return $this->conn->query($query);
    }

    public function read() {
        $query = "SELECT * FROM users";
        return $this->conn->query($query);
    }

    public function update($id, $name, $email) {
        $query = "UPDATE users SET name = '$name', email = '$email' WHERE id = $id";
        return $this->conn->query($query);
    }

    public function delete($id) {
        $query = "DELETE FROM users WHERE id = $id";
        return $this->conn->query($query);
    }
}
```

### 3. templates/index.html
```html
<!DOCTYPE html>
<html>
<head>
    <title>CRUD Application</title>
</head>
<body>
    <h1>CRUD Application</h1>

    <form action="index.php" method="POST">
        <input type="text" name="name" placeholder="Name">
        <input type="email" name="email" placeholder="Email">
        <button type="submit" name="create">Create</button>
    </form>

    <table border="1">
        <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Email</th>
            <th>Action</th>
        </tr>
        <!-- Data dari database akan ditampilkan di sini -->
    </table>
</body>
</html>
```

### 4. index.php
File ```index.php``` adalah file utama dalam praktikum OOP kali ini, file ini yang akan menampilkan antarmuka untuk berinteraksi dengan user.
```php
<?php
require 'classes/Database.php';
require 'classes/CRUD.php';

// Inisialisasi objek CRUD
$crud = new CRUD();

// Handle Create
if (isset($_POST['create'])) {
    $name = $_POST['name'];
    $email = $_POST['email'];
    $crud->create($name, $email);
}

// Tampilkan data dari database
$result = $crud->read();
?>

<!DOCTYPE html>
<html>
<head>
    <title>CRUD Application</title>
</head>
<body>
    <h1>CRUD Application</h1>

    <form action="index.php" method="POST">
        <input type="text" name="name" placeholder="Name">
        <input type="email" name="email" placeholder="Email">
        <button type="submit" name="create">Create</button>
    </form>

    <table border="1">
        <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Email</th>
            <th>Action</th>
        </tr>
        <?php
        if ($result->num_rows > 0) {
            while ($row = $result->fetch_assoc()) {
                echo "<tr>";
                echo "<td>" . $row['id'] . "</td>";
                echo "<td>" . $row['name'] . "</td>";
                echo "<td>" . $row['email'] . "</td>";
                echo "<td><a href='edit.php?id=" . $row['id'] . "'>Edit</a> | <a href='delete.php?id=" . $row['id'] . "'>Delete</a></td>";
                echo "</tr>";
            }
        } else {
            echo "Tidak ada data yang ditampilkan";
        }
        ?>
    </table>
</body>
</html>
```

### 5. edit.php
File edit digunakan untuk menangani permintaan edit data dan juga menampilkan antarmuka untuk user agar dapat melakukan edit data.

```php
<?php
require 'classes/Database.php';
require 'classes/CRUD.php';

// Inisialisasi objek CRUD
$crud = new CRUD();

if (isset($_GET['id'])) {
    $id = $_GET['id'];

    if (isset($_POST['update'])) {
        $name = $_POST['name'];
        $email = $_POST['email'];
        $crud->update($id, $name, $email);
        header("Location: index.php");
    }

    $result = $crud->read();
    $data = $result->fetch_assoc();
} else {
    header("Location: index.php");
}
?>

<!DOCTYPE html>
<html>
<head>
    <title>Edit Data</title>
</head>
<body>
    <h1>Edit Data</h1>
    <form action="edit.php?id=<?php echo $id; ?>" method="POST">
        <input type="text" name="name" placeholder="Name" value="<?php echo $data['name']; ?>">
        <input type="email" name="email" placeholder="Email" value="<?php echo $data['email']; ?>">
        <button type="submit" name="update">Update</button>
    </form>
</body>
</html>
```

### 6. delete.php
File ini digunakan untuk menangani permintaan delete data.

```php
<?php
require 'classes/Database.php';
require 'classes/CRUD.php';

// Inisialisasi objek CRUD
$crud = new CRUD();

if (isset($_GET['id'])) {
    $id = $_GET['id'];
    $crud->delete($id);
    header("Location: index.php");
} else {
    header("Location: index.php");
}
?>
```

## Tugas Praktikum
1. Membuat CRUD dengan menggunakan OOP PHP seperti yang sudah dipelajari.
2. Informasi data yang ada pada website adalah nama, nim, program studi, dan jurusan.
3. Menambahkan CSS/Bootstrap.
4. Penulisan laporan harus menampilkan penjelasan dari kode dan juga screenshoot hasil.
5. Laporan ditulis dengan menggunakan Markdown dan dikumpulkan dengan format pdf.

Untuk kode pada praktikum kali ini dapat anda lihat melalui:
https://github.com/mrfldi/SI201410-Object-Oriented-Programming
