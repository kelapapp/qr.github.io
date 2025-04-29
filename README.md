<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistem Absensi QR Code</title>
    <script src="https://cdn.rawgit.com/davidshimjs/qrcodejs/gh-pages/qrcode.min.js"></script>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f5f5f5;
            color: #333;
        }
        
        .container {
            max-width: 1000px;
            margin: 0 auto;
            padding: 20px;
        }
        
        header {
            background-color: #4285f4;
            color: white;
            padding: 20px 0;
            text-align: center;
            border-radius: 8px 8px 0 0;
            margin-bottom: 30px;
        }
        
        h1 {
            margin: 0;
            font-size: 28px;
        }
        
        .card {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            padding: 20px;
            margin-bottom: 20px;
        }
        
        .form-group {
            margin-bottom: 15px;
        }
        
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        
        input[type="text"],
        input[type="datetime-local"] {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            font-size: 16px;
        }
        
        button {
            background-color: #4285f4;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            transition: background-color 0.3s;
        }
        
        button:hover {
            background-color: #3367d6;
        }
        
        #qrcode-container {
            margin: 20px 0;
            display: flex;
            justify-content: center;
        }
        
        .download-section {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
        }
        
        .scan-link {
            display: inline-block;
            padding: 10px 20px;
            background-color: #34a853;
            color: white;
            text-decoration: none;
            border-radius: 4px;
            transition: background-color 0.3s;
        }
        
        .scan-link:hover {
            background-color: #2d8e47;
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
            background-color: #f2f2f2;
            font-weight: bold;
        }
        
        tr:hover {
            background-color: #f9f9f9;
        }
        
        .tab-container {
            display: flex;
            margin-bottom: 20px;
        }
        
        .tab {
            padding: 10px 20px;
            background-color: #e0e0e0;
            cursor: pointer;
            border-radius: 4px 4px 0 0;
            margin-right: 5px;
        }
        
        .tab.active {
            background-color: #4285f4;
            color: white;
        }
        
        .tab-content {
            display: none;
        }
        
        .tab-content.active {
            display: block;
        }
        
        #scanner-container {
            width: 100%;
            max-width: 500px;
            margin: 0 auto;
            position: relative;
        }
        
        #scanner {
            width: 100%;
            border: 2px solid #4285f4;
            border-radius: 8px;
        }
        
        #scan-result {
            margin-top: 20px;
            padding: 10px;
            background-color: #e8f5e9;
            border-radius: 4px;
            display: none;
        }
        
        @media (max-width: 768px) {
            .container {
                padding: 10px;
            }
            
            .download-section {
                flex-direction: column;
            }
        }
    </style>
</head>
<body>
    <header>
        <div class="container">
            <h1>Sistem Absensi QR Code</h1>
        </div>
    </header>
    
    <div class="container">
        <div class="tab-container">
            <div class="tab active" onclick="openTab('admin')">Admin</div>
            <div class="tab" onclick="openTab('scan')">Scan Absen</div>
        </div>
        
        <!-- Admin Tab -->
        <div id="admin" class="tab-content active">
            <div class="card">
                <h2>Generator QR Code Absensi</h2>
                
                <div class="form-group">
                    <label for="event-name">Nama Acara/Kelas:</label>
                    <input type="text" id="event-name" placeholder="Contoh: Meeting Tim, Kelas Matematika">
                </div>
                
                <div class="form-group">
                    <label for="event-time">Waktu Acara:</label>
                    <input type="datetime-local" id="event-time">
                </div>
                
                <button onclick="generateQR()">Generate QR Code</button>
                
                <div id="qrcode-container">
                    <!-- QR Code akan muncul di sini -->
                </div>
                
                <div class="download-section">
                    <button onclick="downloadQR()">Download QR Code</button>
                    <a href="#scan" class="scan-link" onclick="openTab('scan')">Ke Halaman Scan</a>
                </div>
            </div>
            
            <div class="card">
                <h2>Daftar Hadir</h2>
                <table id="attendance-table">
                    <thead>
                        <tr>
                            <th>No</th>
                            <th>Nama</th>
                            <th>NIM/NIK</th>
                            <th>Waktu Absen</th>
                            <th>Acara</th>
                        </tr>
                    </thead>
                    <tbody>
                        <!-- Data absensi akan diisi oleh JavaScript -->
                    </tbody>
                </table>
            </div>
        </div>
        
        <!-- Scan Tab -->
        <div id="scan" class="tab-content">
            <div class="card">
                <h2>Absensi Peserta</h2>
                
                <div class="form-group">
                    <label for="participant-name">Nama Lengkap:</label>
                    <input type="text" id="participant-name" placeholder="Masukkan nama Anda">
                </div>
                
                <div class="form-group">
                    <label for="participant-id">NIM/NIK:</label>
                    <input type="text" id="participant-id" placeholder="Masukkan NIM atau NIK">
                </div>
                
                <div id="scanner-container">
                    <video id="scanner"></video>
                </div>
                
                <div id="scan-result">
                    <h3>Absensi Berhasil!</h3>
                    <p id="result-details"></p>
                </div>
                
                <button onclick="startScanner()">Mulai Scan QR Code</button>
            </div>
        </div>
    </div>

    <script>
        // Tab functionality
        function openTab(tabName) {
            const tabs = document.getElementsByClassName('tab');
            const tabContents = document.getElementsByClassName('tab-content');
            
            for (let i = 0; i < tabs.length; i++) {
                tabs[i].classList.remove('active');
                tabContents[i].classList.remove('active');
            }
            
            document.getElementById(tabName).classList.add('active');
            event.currentTarget.classList.add('active');
        }
        
        // QR Code Generation
        function generateQR() {
            const eventName = document.getElementById('event-name').value;
            const eventTime = document.getElementById('event-time').value;
            
            if (!eventName || !eventTime) {
                alert('Harap isi nama acara dan waktu acara!');
                return;
            }
            
            const qrData = `ABSENSI:${eventName}:${eventTime}:${Date.now()}`;
            
            document.getElementById('qrcode-container').innerHTML = '';
            new QRCode(document.getElementById('qrcode-container'), {
                text: qrData,
                width: 200,
                height: 200,
                colorDark: "#000000",
                colorLight: "#ffffff",
                correctLevel: QRCode.CorrectLevel.H
            });
            
            // Simpan data acara ke localStorage
            localStorage.setItem('current_event', JSON.stringify({
                name: eventName,
                time: eventTime,
                qrData: qrData
            }));
            
            // Refresh daftar hadir
            loadAttendance();
        }
        
        function downloadQR() {
            const qrContainer = document.getElementById('qrcode-container');
            if (!qrContainer.hasChildNodes()) {
                alert('Generate QR Code terlebih dahulu!');
                return;
            }
            
            const canvas = qrContainer.querySelector('canvas');
            const link = document.createElement('a');
            link.download = 'qrcode-absen.png';
            link.href = canvas.toDataURL('image/png');
            link.click();
        }
        
        // Load attendance data
        function loadAttendance() {
            // Dalam implementasi nyata, ini akan mengambil data dari server/database
            // Contoh sederhana menggunakan localStorage
            const attendanceData = JSON.parse(localStorage.getItem('attendance_data')) || [];
            const tableBody = document.querySelector('#attendance-table tbody');
            tableBody.innerHTML = '';
            
            attendanceData.forEach((entry, index) => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${index + 1}</td>
                    <td>${entry.name}</td>
                    <td>${entry.id}</td>
                    <td>${new Date(entry.timestamp).toLocaleString()}</td>
                    <td>${entry.eventName}</td>
                `;
                tableBody.appendChild(row);
            });
        }
        
        // QR Code Scanner
        let scanner = null;
        
        function startScanner() {
            const participantName = document.getElementById('participant-name').value;
            const participantId = document.getElementById('participant-id').value;
            
            if (!participantName || !participantId) {
                alert('Harap isi nama dan NIM/NIK terlebih dahulu!');
                return;
            }
            
            // Dalam implementasi nyata, gunakan library seperti Instascan.js
            // Ini hanya simulasi untuk contoh
            document.getElementById('scan-result').style.display = 'none';
            
            // Simulasi scan QR code (5 detik)
            setTimeout(() => {
                const eventData = JSON.parse(localStorage.getItem('current_event'));
                
                if (!eventData) {
                    alert('Tidak ada QR code absensi yang valid!');
                    return;
                }
                
                // Simpan data absensi
                const attendanceData = JSON.parse(localStorage.getItem('attendance_data')) || [];
                attendanceData.push({
                    name: participantName,
                    id: participantId,
                    eventName: eventData.name,
                    eventTime: eventData.time,
                    timestamp: Date.now()
                });
                
                localStorage.setItem('attendance_data', JSON.stringify(attendanceData));
                
                // Tampilkan hasil
                document.getElementById('result-details').innerHTML = `
                    <strong>Nama:</strong> ${participantName}<br>
                    <strong>NIM/NIK:</strong> ${participantId}<br>
                    <strong>Acara:</strong> ${eventData.name}<br>
                    <strong>Waktu:</strong> ${new Date().toLocaleString()}
                `;
                
                document.getElementById('scan-result').style.display = 'block';
                
                // Refresh daftar hadir di tab admin
                loadAttendance();
            }, 5000);
            
            alert('Scanner akan membaca QR code. Arahkan kamera ke QR code yang valid.');
        }
        
        // Load attendance data when page loads
        window.onload = function() {
            loadAttendance();
        };
    </script>
</body>
</html>
