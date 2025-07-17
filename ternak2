<?php
// Koneksi database
$host = 'localhost';
$db   = 'jual_beli_ternak';
$user = 'root';
$pass = '';
$charset = 'utf8mb4';

$dsn = "mysql:host=$host;dbname=$db;charset=$charset";
$options = [
    PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION,
    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
    PDO::ATTR_EMULATE_PREPARES   => false,
];

try {
    $pdo = new PDO($dsn, $user, $pass, $options);
} catch (\PDOException $e) {
    throw new \PDOException($e->getMessage(), (int)$e->getCode());
}

// Fungsi CRUD
function addTernak($data) {
    global $pdo;
    $stmt = $pdo->prepare("INSERT INTO ternak (jenis_ternak, harga, stok, deskripsi, gambar) VALUES (?, ?, ?, ?, ?)");
    return $stmt->execute([$data['jenis_ternak'], $data['harga'], $data['stok'], $data['deskripsi'], $data['gambar']]);
}

function updateTernak($id, $data) {
    global $pdo;
    $stmt = $pdo->prepare("UPDATE ternak SET jenis_ternak=?, harga=?, stok=?, deskripsi=?, gambar=? WHERE id=?");
    return $stmt->execute([$data['jenis_ternak'], $data['harga'], $data['stok'], $data['deskripsi'], $data['gambar'], $id]);
}

function deleteTernak($id) {
    global $pdo;
    $stmt = $pdo->prepare("DELETE FROM ternak WHERE id=?");
    return $stmt->execute([$id]);
}

function getTernakById($id) {
    global $pdo;
    $stmt = $pdo->prepare("SELECT * FROM ternak WHERE id=?");
    $stmt->execute([$id]);
    return $stmt->fetch();
}

function getAllTernak() {
    global $pdo;
    $stmt = $pdo->query("SELECT * FROM ternak ORDER BY jenis_ternak");
    return $stmt->fetchAll();
}

// Proses form submission
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $action = $_POST['action'] ?? '';
    $data = [
        'jenis_ternak' => $_POST['jenis_ternak'],
        'harga' => $_POST['harga'],
        'stok' => $_POST['stok'],
        'deskripsi' => $_POST['deskripsi'],
        'gambar' => $_POST['gambar']
    ];

    switch ($action) {
        case 'create':
            addTernak($data);
            break;
        case 'update':
            updateTernak($_POST['id'], $data);
            break;
        case 'delete':
            deleteTernak($_POST['id']);
            break;
    }
    
    header("Location: ".$_SERVER['PHP_SELF']);
    exit();
}

// Buat tabel jika belum ada
$pdo->exec("CREATE TABLE IF NOT EXISTS ternak (
    id INT AUTO_INCREMENT PRIMARY KEY,
    jenis_ternak VARCHAR(50) NOT NULL,
    harga DECIMAL(10,2) NOT NULL,
    stok INT NOT NULL,
    deskripsi TEXT,
    gambar VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
)");
?>
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aplikasi Jual Beli Ternak</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            line-height: 1.6;
            margin: 0;
            padding: 0;
            background-color: #f5f5f5;
            color: #333;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        header {
            background-color: #4CAF50;
            color: white;
            padding: 20px 0;
            text-align: center;
            border-radius: 5px;
            margin-bottom: 20px;
        }
        h1 {
            margin: 0;
        }
        .card {
            background: white;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            margin-bottom: 20px;
            padding: 20px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        th, td {
            padding: 12px 15px;
            text-align: left;
            border-bottom: 1px solid #ddd;
        }
        th {
            background-color: #4CAF50;
            color: white;
        }
        tr:hover {
            background-color: #f5f5f5;
        }
        .btn {
            display: inline-block;
            background: #4CAF50;
            color: white;
            padding: 8px 16px;
            text-decoration: none;
            border-radius: 4px;
            border: none;
            cursor: pointer;
            margin-right: 5px;
        }
        .btn-danger {
            background: #f44336;
        }
        .btn-edit {
            background: #2196F3;
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        input, textarea, select {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
        }
        .ternak-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }
        .ternak-card {
            background: white;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            overflow: hidden;
        }
        .ternak-img {
            width: 100%;
            height: 200px;
            object-fit: cover;
        }
        .ternak-details {
            padding: 15px;
        }
        .ternak-price {
            color: #4CAF50;
            font-weight: bold;
            font-size: 1.2em;
            margin: 10px 0;
        }
        .ternak-stock {
            color: #666;
        }
        .form-container {
            background: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        .hidden {
            display: none;
        }
        @media (max-width: 768px) {
            .ternak-grid {
                grid-template-columns: repeat(auto-fill, minmax(100%, 1fr));
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>Aplikasi Jual Beli Ternak</h1>
            <p>Kelola Ketersediaan Ternak (Entog, Ayam, Bebek, dll)</p>
        </header>

        <div class="card">
            <button id="toggleFormBtn" class="btn">Tambah Ternak Baru</button>

            <div id="ternakForm" class="form-container hidden">
                <h2 id="formTitle">Tambah Ternak Baru</h2>
                <form method="POST" id="submitForm">
                    <input type="hidden" name="id" id="ternakId">
                    <input type="hidden" name="action" id="action" value="create">
                    
                    <div class="form-group">
                        <label for="jenis_ternak">Jenis Ternak</label>
                        <input type="text" name="jenis_ternak" id="jenis_ternak" required>
                    </div>
                    
                    <div class="form-group">
                        <label for="harga">Harga (Rp)</label>
                        <input type="number" name="harga" id="harga" required>
                    </div>
                    
                    <div class="form-group">
                        <label for="stok">Stok Tersedia</label>
                        <input type="number" name="stok" id="stok" required>
                    </div>
                    
                    <div class="form-group">
                        <label for="gambar">URL Gambar</label>
                        <input type="text" name="gambar" id="gambar" placeholder="https://example.com/gambar.jpg">
                    </div>
                    
                    <div class="form-group">
                        <label for="deskripsi">Deskripsi</label>
                        <textarea name="deskripsi" id="deskripsi" rows="3"></textarea>
                    </div>
                    
                    <button type="submit" class="btn">Simpan</button>
                    <button type="button" id="cancelBtn" class="btn btn-danger">Batal</button>
                </form>
            </div>
        </div>

        <div class="card">
            <h2>Daftar Ternak Tersedia</h2>
            
            <div class="ternak-grid">
                <?php foreach (getAllTernak() as $ternak): ?>
                    <div class="ternak-card">
                        <img src="<?= htmlspecialchars($ternak['gambar'] ?? 'https://placehold.co/400x300?text='.urlencode($ternak['jenis_ternak'])) ?>" 
                             alt="<?= htmlspecialchars($ternak['jenis_ternak']) ?>" class="ternak-img">
                        <div class="ternak-details">
                            <h3><?= htmlspecialchars($ternak['jenis_ternak']) ?></h3>
                            <div class="ternak-price">Rp <?= number_format($ternak['harga'], 0, ',', '.') ?></div>
                            <div class="ternak-stock">Stok: <?= $ternak['stok'] ?> ekor</div>
                            <p><?= htmlspecialchars($ternak['deskripsi']) ?></p>
                            <button onclick="editTernak(<?= $ternak['id'] ?>)" class="btn btn-edit">Edit</button>
                            <button onclick="if(confirm('Yakin hapus?')) deleteTernak(<?= $ternak['id'] ?>)" class="btn btn-danger">Hapus</button>
                        </div>
                    </div>
                <?php endforeach; ?>
            </div>
        </div>
    </div>

    <script>
        // Toggle form visibility
        document.getElementById('toggleFormBtn').addEventListener('click', function() {
            const form = document.getElementById('ternakForm');
            form.classList.toggle('hidden');
            if (!form.classList.contains('hidden')) {
                document.getElementById('formTitle').textContent = 'Tambah Ternak Baru';
                document.getElementById('action').value = 'create';
                document.getElementById('submitForm').reset();
                document.getElementById('ternakId').value = '';
            }
        });

        // Cancel button
        document.getElementById('cancelBtn').addEventListener('click', function() {
            document.getElementById('ternakForm').classList.add('hidden');
        });

        // Edit function
        function editTernak(id) {
            fetch('?action=get&id=' + id)
                .then(response => response.json())
                .then(data => {
                    document.getElementById('ternakForm').classList.remove('hidden');
                    document.getElementById('formTitle').textContent = 'Edit Ternak';
                    document.getElementById('action').value = 'update';
                    document.getElementById('ternakId').value = data.id;
                    document.getElementById('jenis_ternak').value = data.jenis_ternak;
                    document.getElementById('harga').value = data.harga;
                    document.getElementById('stok').value = data.stok;
                    document.getElementById('gambar').value = data.gambar;
                    document.getElementById('deskripsi').value = data.deskripsi;
                });
        }

        // Delete function
        function deleteTernak(id) {
            const form = document.createElement('form');
            form.method = 'POST';
            form.action = '';
            
            const idInput = document.createElement('input');
            idInput.type = 'hidden';
            idInput.name = 'id';
            idInput.value = id;
            
            const actionInput = document.createElement('input');
            actionInput.type = 'hidden';
            actionInput.name = 'action';
            actionInput.value = 'delete';
            
            form.appendChild(idInput);
            form.appendChild(actionInput);
            document.body.appendChild(form);
            form.submit();
        }

        // Handle get request for edit
        if (window.location.search.includes('action=get')) {
            const urlParams = new URLSearchParams(window.location.search);
            const id = urlParams.get('id');
            
            fetch('?action=get&id=' + id)
                .then(response => response.json())
                .then(data => {
                    document.getElementById('ternakForm').classList.remove('hidden');
                    document.getElementById('formTitle').textContent = 'Edit Ternak';
                    document.getElementById('action').value = 'update';
                    document.getElementById('ternakId').value = data.id;
                    document.getElementById('jenis_ternak').value = data.jenis_ternak;
                    document.getElementById('harga').value = data.harga;
                    document.getElementById('stok').value = data.stok;
                    document.getElementById('gambar').value = data.gambar;
                    document.getElementById('deskripsi').value = data.deskripsi;
                });
        }
    </script>
</body>
</html>
