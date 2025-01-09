# medicine_UAS

### Nama : Ranti Nurmala Sari 
### NIM : 21.01.55.0016

Melanjutkan UTS sebelumnya yaitu membuat frontend pada studi kasus medicine. Pada pembuatan frontend ini menggunakan Bootstrap 5.
Untuk keamanan website memerlukan aktivitas login sebelum memasuki halaman index.

Tujuannya yaitu menampilkan data pada database serta dapat menambah data, mengubah data, menghapus data serta mencari data
Sebelum membuat tampilan pertama memerlukan database `login_db` pada `http://localhost/phpmyadmin` yang berisi tabel `users` gunanya untuk login ke website yang akan dibuat
```sql
CREATE TABLE `users` (
  `id` int(11) NOT NULL,
  `username` varchar(50) NOT NULL,
  `password` varchar(25) NOT NULL,
  `name` varchar(25) NOT NULL
)
```

1. Pertama menambahkan file `login.php` pada folder `uas_medicine` yang tersimpan pada direktori `xampp/htdocs`. Dalam folder tersebut berisi api yang sudah dibuat sebelumnya.
2. Selanjutnya buka file `login.php` pada text editor seperti visual studio code atau sublime text dan salin code berikut :
   ```php
   <?php
    session_start();
    require_once 'config.php';
    
    if(isset($_POST['login'])) {
        $username = $_POST['username'];
        $password = $_POST['password'];
        
        // Gunakan fungsi PASSWORD() untuk mencocokkan dengan hash di database
        $query = "SELECT * FROM users WHERE username='$username' AND password=PASSWORD('$password')";
        $result = mysqli_query($conn, $query);
        
        if(mysqli_num_rows($result) == 1) {
            $row = mysqli_fetch_assoc($result);
            $_SESSION['user_id'] = $row['id'];
            $_SESSION['username'] = $row['username'];
            $_SESSION['name'] = $row['name'];
                
            header("Location: index.php");
            exit();
        } else {
            $error = "Username atau Password salah!";
        }
    }
    ?>
    
    <!DOCTYPE html>
    <html>
    <head>
        <title>Login</title>
        <meta charset="utf-8">
      <meta name="viewport" content="width=device-width, initial-scale=1">
      <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
      <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
        <style>
            body{
                background-color : #D3D3D3;
            }
        </style>
    </head>
    <body>
    <div class="container-md p-5 my-5 bg-dark text-white">
        <h2 class="text-center">Login</h2>
        <?php if(isset($error)) { ?>
            <p style="color: red;"><?php echo $error; ?></p>
        <?php } ?>
        
        <form action="" method="POST" class="was-validated">
        <div class="mb-3 mt-3">
          <label for="username" class="form-label">Username</label>
          <input type="text" class="form-control" id="username" placeholder="Enter username" name="username" required>
          <div class="valid-feedback">Valid.</div>
          <div class="invalid-feedback">Please fill out this field.</div>
        </div>
        <div class="mb-3 mt-3">
          <label for="password" class="form-label">Password</label>
          <input type="password" class="form-control" id="password" placeholder="Enter password" name="password" required>
          <div class="valid-feedback">Valid.</div>
          <div class="invalid-feedback">Please fill out this field.</div>
        </div>
            <!-- <p>
                Username: <input type="text" name="username" required>
            </p>
            <p>
                Password: <input type="password" name="password" required>
            </p> -->
            <div class="text-center">
                <button type="submit" class="col-sm-4 btn btn-outline-secondary" name="login">Login</button>
            </div>
            
        </form>
        </div>
    </body>
    </html>
   ```
3. Agar login bisa berfungsi harus membuat koneksi antara database dan php membuat file bernama `config.php` pada folder
   ```php
   <?php
    $host = "localhost";
    $dbuser = "root";
    $dbpass = "";
    $dbname = "login_db";
    
    $conn = mysqli_connect($host, $dbuser, $dbpass, $dbname);
    if (!$conn) {
        die("Connection failed: " . mysqli_connect_error());
    }
    ?>
   ```
5. Selanjutnya buat file `index.php` pada folder yang sama dan buka di text editor. salin code berikut :
   ```php
   <?php
    session_start();
    
    // Cek apakah user sudah login
    if(!isset($_SESSION['user_id'])) {
        header("Location: login.php");
        exit();
    }
    ?>
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Daftar Medicines</title>
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
        <style>
            body{
                background-color : #D3D3D3;
            }
        </style>
    </head>
    <body>
    <div class="container mt-3 text-center">
        <div class="card bg-dark text-white">
        <div class="card-body">
    <div class="card-title"><h2>Welcome, <?php echo $_SESSION['name']; ?>!</h2></div>
    <div class="card-text"><p>username : <?php echo $_SESSION['username']; ?></p></div>
    <div ><a href="form.php"><button type="button" class="col-sm-4 btn btn-outline-secondary" data-bs-dismiss="modal">Daftar Medicine</button></a></div>
    <br>
    <div ><a href="logout.php"><button type="button" class="col-sm-4 btn btn-outline-secondary" data-bs-dismiss="modal">Logout</button></a></div>
    </div>
        </div>
        </div>
    </body>
    </html>
   ```
   pada tampilan index sama seperti dashboard yang berfungsi sebagai transisi antara tampilan data dengan login yang berisi menu.
6. Selanjutnya agar aktivitas logout berhasil buat file `logout.php` pada folder dengan code
   ```php
   <?php
    session_start();
    
    // Hapus semua data session
    session_destroy();
    
    // Redirect ke halaman login
    header("Location: login.php");
    exit();
    ?>
   ```
8. Lalu pada halaman tampilan data, buat file bernama `form.php` pada folder, dengan code :
   ```php
   <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Daftar Medicines</title>
            <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
        <style>
            body{
                background-color : #D3D3D3;
            }
        </style>
    </head>
    <body>
    <div class="container mt-5">
    <a href="index.php"><button type="button" class="btn btn-outline-dark btn-sm">Kembali</button></a>
        </div>
    <div class="container mt-5">
            <h2 class="mb-4">Daftar Medicine</h2>
            
            <!-- Form Pencarian dan Tombol Tambah -->
            <div class="row mb-3">
            <div class="col">
                <input type="text" id="searchInput" class="form-control" placeholder="Cari berdasarkan ID">
            </div>
            <div class="col-auto">
                <button onclick="searchmedicine()" class="btn btn-outline-dark">Cari</button>
            </div>
            <div class="col-auto">
                <button type="button" class="btn btn-outline-primary" data-bs-toggle="modal" data-bs-target="#medicineModal">
                Tambah Medicine
                </button>
            </div>
        </div>
    
            <div class="table-responsive">
                <table class="table table-dark table-striped">
                    <thead class="table-dark">
                        <tr>
                            <th>ID</th>
                            <th>Nama</th>
                            <th>Type</th>
                            <th>Price</th>
                            <th>Stock</th>
                            <th>Aksi</th>
                        </tr>
                    </thead>
                    <tbody id="medicineList" >
                        <?php
                        try {
                            // Set URL API
                            $api_url = 'http://localhost/uas_medicine/medicines_api.php';
                            
                            // Cek jika ada pencarian
                            if(isset($_GET['search_id']) && !empty($_GET['search_id'])) {
                                $api_url .= '/' . $_GET['search_id'];
                            }
                            
                            // Konfigurasi timeout
                            $ctx = stream_context_create([
                                'http' => ['timeout' => 5]
                            ]);
                            
                            // Ambil data dari API
                            $response = file_get_contents($api_url, false, $ctx);
                            
                            // Validasi response
                            if ($response === false) {
                                throw new Exception('Gagal mengambil data dari API');
                            }
                            
                            // Validasi JSON
                            $data = json_decode($response, true);
                            if (json_last_error() !== JSON_ERROR_NONE) {
                                throw new Exception('Invalid JSON response');
                            }
    
                            // Tampilkan data berdasarkan jenis request
                            if(isset($_GET['search_id'])) {
                                if($data && !isset($data['message'])) {
                                    // Tampilkan satu Medicine
                                    echo "<tr>";
                                    echo "<td>{$data['id']}</td>";
                                    echo "<td>{$data['name']}</td>";
                                    echo "<td>{$data['type']}</td>";
                                    echo "<td>{$data['price']}</td>";
                                    echo "<td>{$data['stock']}</td>";
                                    echo "</tr>";
                                } else {
                                    // Tampilkan pesan tidak ditemukan
                                    echo "<tr><td colspan='4' class='text-center'>";
                                    echo "Medicine dengan ID tersebut tidak ditemukan";
                                    echo "</td></tr>";
                                }
                            } else {
                                // Cek apakah ada data
                                if(!empty($data)) {
                                    // Loop untuk setiap Medicine
                                    foreach($data as $db) {
                                        echo "<tr>";
                                        echo "<td>{$db['id']}</td>";
                                        echo "<td>{$db['name']}</td>";
                                        echo "<td>{$db['type']}</td>";
                                        echo "<td>{$db['price']}</td>";
                                        echo "<td>{$db['stock']}</td>";
                                        echo "</tr>";
                                    }
                                } else {
                                    // Tampilkan pesan jika tidak ada data
                                    echo "<tr><td colspan='4' class='text-center'>";
                                    echo "Tidak ada data Medicine";
                                    echo "</td></tr>";
                                }
                            }
                            
                        } catch (Exception $e) {
                            echo "<tr><td colspan='4' class='text-center text-danger'>";
                            echo "Error: " . $e->getMessage();
                            echo "</td></tr>";
                        }
                        ?>
                    </tbody>
                </table>
            </div>
        </div>        
        <!-- Modal for Add/Edit medicine -->
        <div class="modal fade" id="medicineModal" tabindex="-1">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-name" id="modalName">Tambah Medicine</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                    </div>
                    <div class="modal-body">
                        <form id="medicineForm">
                            <input type="hidden" id="medicineId">
                            <div class="mb-3">
                                <label for="nama" class="form-label">Nama</label>
                                <input type="text" class="form-control" id="name" required>
                            </div>
                            <div class="mb-3">
                                <label for="type" class="form-label">Type</label>
                                <input type="text" class="form-control" id="type" required>
                            </div>
                            <div class="mb-3">
                                <label for="price" class="form-label">Price</label>
                                <input type="text" class="form-control" id="price" value="Rp " required>
                            </div>
                            <div class="mb-3">
                                <label for="stock" class="form-label">Stock</label>
                                <input type="number" class="form-control" id="stock" required>
                            </div>
                        </form>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-outline-secondary" data-bs-dismiss="modal">Batal</button>
                        <button type="button" class="btn btn-outline-primary" onclick="savemedicine()">Simpan</button>
                    </div>
                </div>
            </div>
        </div>
                    </div>
    
        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
        <script>
            const API_URL = 'http://localhost/uas_medicine/medicines_api.php';
            let medicineModal;
    
            document.addEventListener('DOMContentLoaded', function() {
                medicineModal = new bootstrap.Modal(document.getElementById('medicineModal'));
                loadmedicines();
            });
    
            function loadmedicines() {
                fetch(API_URL)
                    .then(response => response.json())
                    .then(medicines => {
                        const medicineList = document.getElementById('medicineList');
                        medicineList.innerHTML = '';
                        medicines.forEach(medicine => {
                            medicineList.innerHTML += `
                                <tr>
                                    <td>${medicine.id}</td>
                                    <td>${medicine.name}</td>
                                    <td>${medicine.type}</td>
                                    <td>${medicine.price}</td>
                                    <td>${medicine.stock}</td>
                                    <td class="btn-group-action">
                                        <button class="btn btn-sm btn-outline-warning me-1" onclick="editmedicine(${medicine.id})">Edit</button>
                                        <button class="btn btn-sm btn-outline-danger" onclick="deletemedicine(${medicine.id})">Hapus</button>
                                    </td>
                                </tr>
                            `;
                        });
                    })
                    .catch(error => alert('Error loading medicines: ' + error));
            }
    
            function searchmedicine() {
                const id = document.getElementById('searchInput').value;
                if (!id) {
                    loadmedicines();
                    return;
                }
                
                fetch(`${API_URL}/${id}`)
                    .then(response => response.json())
                    .then(medicine => {
                        const medicineList = document.getElementById('medicineList');
                        if (medicine.message) {
                            alert('medicine not found');
                            return;
                        }
                        medicineList.innerHTML = `
                            <tr>
                                <td>${medicine.id}</td>
                                <td>${medicine.name}</td>
                                <td>${medicine.type}</td>
                                <td>${medicine.price}</td>
                                <td>${medicine.stock}</td>
                                <td class="btn-group-action">
                                    <button class="btn btn-sm btn-outline-warning me-1" onclick="editmedicine(${medicine.id})">Edit</button>
                                    <button class="btn btn-sm btn-outline-danger" onclick="deletemedicine(${medicine.id})">Hapus</button>
                                </td>
                            </tr>
                        `;
                    })
                    .catch(error => alert('Error searching medicine: ' + error));
            }
    
            function editmedicine(id) {
                fetch(`${API_URL}/${id}`)
                    .then(response => response.json())
                    .then(medicine => {
                        document.getElementById('medicineId').value = medicine.id;
                        document.getElementById('name').value = medicine.name;
                        document.getElementById('type').value = medicine.type;
                        document.getElementById('price').value = medicine.price;
                        document.getElementById('stock').value = medicine.stock;
                        document.getElementById('modalName').textContent = 'Edit Medicine';
                        medicineModal.show();
                    })
                    .catch(error => alert('Error loading medicine details: ' + error));
            }
    
            function deletemedicine(id) {
                if (confirm('Are you sure you want to delete this medicine?')) {
                    fetch(`${API_URL}/${id}`, {
                        method: 'DELETE'
                    })
                    .then(response => response.json())
                    .then(data => {
                        alert('medicine deleted successfully');
                        loadmedicines();
                    })
                    .catch(error => alert('Error deleting medicine: ' + error));
                }
            }
    
            function savemedicine() {
                const medicineId = document.getElementById('medicineId').value;
                const medicineData = {
                    name: document.getElementById('name').value,
                    type: document.getElementById('type').value,
                    price: document.getElementById('price').value,
                    stock: document.getElementById('stock').value
                };
    
                const method = medicineId ? 'PUT' : 'POST';
                const url = medicineId ? `${API_URL}/${medicineId}` : API_URL;
    
                fetch(url, {
                    method: method,
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify(medicineData)
                })
                .then(response => response.json())
                .then(data => {
                    alert(medicineId ? 'medicine updated successfully' : 'medicine added successfully');
                    medicineModal.hide();
                    loadmedicines();
                    resetForm();
                })
                .catch(error => alert('Error saving medicine: ' + error));
            }
    
            function resetForm() {
                document.getElementById('medicineId').value = '';
                document.getElementById('medicineForm').reset();
                document.getElementById('modalName').textContent = 'Tambah Medicine';
            }
    
            // Reset form when modal is closed
            document.getElementById('medicineModal').addEventListener('hidden.bs.modal', resetForm);
        </script>
        
        <!-- Bootstrap JS -->
        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    </body>
    </html>
   ```
