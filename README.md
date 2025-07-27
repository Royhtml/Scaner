# Scanner - Dokumentasi Lengkap

## Installation

1. pip install webscan2
2. webscan

## keunggulan webscan2

- Keunggulan webscan2 adalah untuk mendeteksi berbagai jenis kerentanan pada website. Aplikasi ini menggunakan teknik pemindaian otomatis untuk mengidentifikasi masalah keamanan seperti SQL Injection, XSS, CSRF, dan lainnya. Dibangun dengan antarmuka yang user-friendly menggunakan Tkinter, tool ini cocok untuk pengembang web, pentester, dan administrator sistem.

- Fitur Utama:
   - Pemindaian Multi-Kerentanan:
      - SQL Injection
      - Cross-Site Scripting (XSS)
      - Cross-Site Request Forgery (CSRF)
      - Directory Traversal
      - Command Injection
      - Analisis Header Keamanan
      - Pemeriksaan SSL/TLS
   - Fitur Tambahan:
      - Web crawling otomatis
      - Integrasi dengan WSL (Windows Subsystem for Linux)
      - Tampilan hasil dengan klasifikasi tingkat keparahan
      - Progress bar real-time
      - Ekspor hasil pemindaian
   - Antarmuka Pengguna:
      - Pilihan pemindaian yang dapat dikustomisasi
      - Tampilan hasil terorganisir dalam tabel dan detail teks   
      - Warna berbeda berdasarkan tingkat keparahan kerentanan

## Daftar Isi
1. [Deskripsi](#deskripsi)
2. [Fitur Utama](#fitur-utama)
3. [Algoritma dan Teknik Pemindaian](#algoritma-dan-teknik-pemindaian)
4. [Kelebihan](#kelebihan)
5. [Perbandingan dengan Tools Sejenis](#perbandingan-dengan-tools-sejenis)
6. [Instalasi](#instalasi)
7. [Penggunaan](#penggunaan)
8. [Screenshots](#screenshots)
9. [Berkontribusi](#berkontribusi)
10. [Lisensi](#lisensi)

## Deskripsi

Advanced Vulnerability Scanner adalah aplikasi GUI berbasis Python yang dirancang untuk mendeteksi berbagai jenis kerentanan keamanan pada website. Aplikasi ini menggunakan teknik pemindaian otomatis untuk mengidentifikasi masalah keamanan seperti SQL Injection, XSS, CSRF, dan lainnya. Dibangun dengan antarmuka yang user-friendly menggunakan Tkinter, tool ini cocok untuk pengembang web, pentester, dan administrator sistem.

## Fitur Utama

1. **Pemindaian Multi-Kerentanan**:
   - SQL Injection
   - Cross-Site Scripting (XSS)
   - Cross-Site Request Forgery (CSRF)
   - Directory Traversal
   - Command Injection
   - Analisis Header Keamanan
   - Pemeriksaan SSL/TLS

2. **Fitur Tambahan**:
   - Web crawling otomatis
   - Integrasi dengan WSL (Windows Subsystem for Linux)
   - Tampilan hasil dengan klasifikasi tingkat keparahan
   - Progress bar real-time
   - Ekspor hasil pemindaian

3. **Antarmuka Pengguna**:
   - Pilihan pemindaian yang dapat dikustomisasi
   - Tampilan hasil terorganisir dalam tabel dan detail teks
   - Warna berbeda berdasarkan tingkat keparahan kerentanan

## Algoritma dan Teknik Pemindaian

### 1. SSL/TLS Check
**Algoritma**:
1. Membuat koneksi SSL ke server target
2. Mengekstrak informasi sertifikat
3. Memeriksa tanggal kedaluwarsa sertifikat
4. Mendeteksi protokol SSL/TLS yang lemah (SSLv2, SSLv3, TLS 1.0, TLS 1.1)

**Kode Penting**:
```python
context = ssl.create_default_context()
with socket.create_connection((domain, 443)) as sock:
    with context.wrap_socket(sock, server_hostname=domain) as ssock:
        cert = ssock.getpeercert()
```

### 2. Header Analysis
**Algoritma**:
1. Mengambil header HTTP dari respons
2. Memeriksa keberadaan header keamanan penting:
   - X-XSS-Protection
   - X-Content-Type-Options
   - X-Frame-Options
   - Content-Security-Policy
   - Strict-Transport-Security
   - Referrer-Policy
3. Memeriksa konfigurasi CORS yang terlalu permisif

**Kode Penting**:
```python
security_headers = {
    'X-XSS-Protection': '1; mode=block',
    'X-Content-Type-Options': 'nosniff',
    'X-Frame-Options': ['DENY', 'SAMEORIGIN'],
    # ... lainnya
}
```

### 3. SQL Injection Test
**Algoritma**:
1. Mengekstrak parameter URL
2. Menyuntikkan payload SQL khusus ke setiap parameter
3. Menganalisis respons untuk pesan kesalahan database
4. Mendeteksi pola respons yang menunjukkan kerentanan

**Payload Contoh**:
```python
payloads = [
    "'", "\"", "' OR '1'='1", "' OR 1=1--", 
    "' OR 1=1#", "' OR 1=1/*", "' UNION SELECT null,version()--"
]
```

### 4. XSS Test
**Algoritma**:
1. Mengekstrak parameter URL
2. Menyuntikkan payload XSS ke setiap parameter
3. Memeriksa apakah payload muncul dalam respons tanpa sanitasi

**Payload Contoh**:
```python
payloads = [
    "<script>alert(1)</script>", 
    "<img src=x onerror=alert(1)>",
    "\"><script>alert(1)</script>",
    "javascript:alert(1)",
    "onmouseover=alert(1)"
]
```

### 5. CSRF Test
**Algoritma**:
1. Mengambil semua form dari halaman web
2. Memeriksa keberadaan token CSRF
3. Mengidentifikasi form yang tidak memiliki perlindungan CSRF

**Kode Penting**:
```python
for form in forms:
    if not form.find('input', {'name': 'csrf_token'}) and \
       not form.find('input', {'name': 'csrfmiddlewaretoken'}):
        vulnerable = True
```

### 6. Directory Traversal Test
**Algoritma**:
1. Membuat URL dengan berbagai variasi path traversal
2. Memeriksa apakah server mengembalikan file sistem yang seharusnya tidak dapat diakses
3. Mencari indikator file sistem seperti "/etc/passwd" dalam respons

**Payload Contoh**:
```python
payloads = [
    "../../../../etc/passwd",
    "..%2F..%2F..%2Fetc%2Fpasswd",
    "....//....//etc/passwd"
]
```

### 7. Command Injection Test
**Algoritma**:
1. Menyuntikkan perintah sistem melalui parameter URL
2. Mencari output perintah dalam respons
3. Mendeteksi indikator seperti "uid=" atau output ping

**Payload Contoh**:
```python
payloads = [
    ";id", "|id", "`id`", "$(id)", 
    "|| ping -c 1 localhost", "&& ping -c 1 localhost"
]
```

### 8. Web Crawling
**Algoritma**:
1. Memulai dari URL dasar
2. Mengekstrak semua link dari halaman
3. Mengunjungi link baru secara rekursif
4. Membatasi kedalaman dan jumlah halaman yang dikunjungi

**Kode Penting**:
```python
while to_visit and len(visited) < max_pages:
    url = to_visit.pop()
    response = requests.get(url, timeout=5)
    soup = BeautifulSoup(response.text, 'html.parser')
    # Ekstrak link dan tambahkan ke antrian
```

## Kelebihan

1. **Multi-Threading**: Pemindaian berjalan di thread terpisah sehingga GUI tetap responsif
2. **Klasifikasi Kerentanan**: Hasil diklasifikasikan berdasarkan tingkat keparahan (High, Medium, Low, Info)
3. **Antarmuka Intuitif**: Tampilan GUI yang mudah digunakan dengan progress bar dan tab hasil
4. **Modular**: Setiap jenis tes dapat diaktifkan/nonaktifkan sesuai kebutuhan
5. **Integrasi WSL**: Akses cepat ke terminal Linux dari dalam aplikasi (untuk Windows)
6. **Ringan**: Tidak memerlukan server atau dependensi berat selain Python standar

## Perbandingan dengan Tools Sejenis

| Fitur                     | Advanced Vulnerability Scanner | OWASP ZAP | Burp Suite Community | Nikto |
|---------------------------|--------------------------------|-----------|-----------------------|-------|
| GUI                       | ✅                             | ✅         | ✅                     | ❌     |
| SQL Injection Test        | ✅                             | ✅         | ✅                     | ✅     |
| XSS Test                  | ✅                             | ✅         | ✅                     | ✅     |
| CSRF Detection            | ✅                             | ✅         | ✅                     | ❌     |
| SSL/TLS Analysis          | ✅                             | ✅         | ✅                     | ✅     |
| Header Analysis           | ✅                             | ✅         | ✅                     | ✅     |
| Command Injection Test    | ✅                             | ❌         | ❌                     | ❌     |
| Web Crawling              | ✅ (Basic)                     | ✅         | ✅                     | ❌     |
| Automation                | ✅                             | ✅         | ✅                     | ✅     |
| Open Source               | ✅                             | ✅         | ❌                     | ✅     |
| Harga                     | Gratis                         | Gratis     | Gratis (Versi Community) | Gratis |

**Kelebihan dibanding tools lain**:
1. Lebih ringan dan cepat dibanding ZAP/Burp Suite
2. Antarmuka lebih sederhana untuk pemula
3. Fokus pada kerentanan umum yang paling kritis
4. Tidak memerlukan konfigurasi kompleks

**Kekurangan**:
1. Tidak memiliki fitur proxy intercept seperti Burp Suite
2. Crawling lebih dasar dibanding ZAP
3. Tidak memiliki database kerentanan selengkap Nikto

## Instalasi

### Persyaratan Sistem
- Python 3.6 atau lebih baru
- pip (Python package manager)
- Sistem Operasi: Windows, Linux, atau macOS

### Langkah-langkah Instalasi

1. **Clone repository** (jika menggunakan versi source code):
   ```bash
   git clone https://github.com/username/advanced-vulnerability-scanner.git
   cd advanced-vulnerability-scanner
   ```

2. **Buat virtual environment (disarankan)**:
   ```bash
   python -m venv venv
   source venv/bin/activate  # Linux/macOS
   venv\Scripts\activate     # Windows
   ```

3. **Instal dependensi**:
   ```bash
   pip install -r requirements.txt
   ```

   Atau instal manual:
   ```bash
   pip install requests beautifulsoup4 pillow tk
   ```

4. **Jalankan aplikasi**:
   ```bash
   python gui.py
   ```

### Untuk Build Executable (Opsional)

1. Instal PyInstaller:
   ```bash
   pip install pyinstaller
   ```

2. Build executable:
   ```bash
   pyinstaller --onefile --windowed --icon=icon.ico gui.py
   ```

3. File executable akan berada di folder `dist`

## Penggunaan

1. **Memulai Pemindaian**:
   - Masukkan URL target (harus dimulai dengan http:// atau https://)
   - Pilih jenis tes yang ingin dilakukan dengan mencentang opsi yang tersedia
   - Klik tombol "Scan"

2. **Membaca Hasil**:
   - Hasil akan ditampilkan dalam tabel di tab "Vulnerabilities"
   - Detail lengkap tersedia di tab "Details"
   - Kerentanan diwarnai berdasarkan tingkat keparahan:
     - Merah: High
     - Kuning: Medium
     - Hijau: Low
     - Biru: Info

3. **Fitur WSL**:
   - Tombol "WSL" akan membuka terminal WSL (hanya untuk Windows dengan WSL terinstal)

4. **Menyimpan Hasil**:
   - Salin teks dari tab "Details" secara manual (fitur ekspor otomatis akan ditambahkan di versi mendatang)

## Screenshots

- Antarmuka Utama
![Antarmuka Utama](ui.png)

## Berkontribusi

Kontribusi terbuka untuk:
- Menambahkan jenis tes kerentanan baru
- Memperbaiki false positive/negative
- Meningkatkan antarmuka pengguna
- Menambahkan fitur ekspor hasil
- Menerjemahkan ke bahasa lain

Langkah kontribusi:
1. Fork repository
2. Buat branch fitur (`git checkout -b fitur-baru`)
3. Commit perubahan (`git commit -am 'Menambahkan fitur baru'`)
4. Push ke branch (`git push origin fitur-baru`)
5. Buat Pull Request

## Lisensi

Proyek ini dilisensikan di bawah MIT License - lihat file [LICENSE](LICENSE) untuk detail lengkap.
