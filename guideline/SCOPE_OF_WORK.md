# SCOPE OF WORK - SISTEM MANAJEMEN YAYASAN
## Dokumentasi untuk Backend Developer

---

## 1. OVERVIEW SISTEM

### 1.1 Deskripsi Umum
Website Admin Yayasan adalah sistem manajemen operasional untuk yayasan yang menjalankan **Program 9 ASNAB** dengan fokus pada pemberian bantuan berupa **barang bukan tunai**. Sistem ini menggunakan **SOP 7 tahap** untuk setiap program bantuan reguler dan memiliki jalur khusus untuk **Reaksi Cepat Bencana Alam**.

### 1.2 Target Pengguna
- Administrator Yayasan
- Staff berbagai divisi (23 role berbeda)
- Multi-region (38 provinsi di Indonesia)

### 1.3 Teknologi Frontend
- React + TypeScript
- Tailwind CSS v4
- localStorage (sementara, harus diganti dengan backend database)

---

## 2. SISTEM AUTHENTICATION

### 2.1 Halaman: Login (AuthPage)

#### Fitur Login:
- **Input Fields:**
  - Email (required, email format validation)
  - Password (required, show/hide toggle)

- **Button "Masuk":**
  - Validasi email dan password
  - Check kredensial di database
  - Create session token
  - Redirect ke Dashboard
  - Error handling untuk kredensial salah

- **Link "Lupa Password?":**
  - Navigate ke halaman Forgot Password

#### Halaman Lupa Password (ForgotPasswordPage):
- **Input Fields:**
  - Email (required, email format validation)

- **Button "Kirim Link Reset Password":**
  - Validasi email terdaftar di database
  - Generate reset token (1 jam expiry)
  - Kirim email reset password
  - Show success message
  - Backend: `POST /api/auth/forgot-password`

- **Button "Kembali ke Login":**
  - Navigate kembali ke login page

#### Registrasi Akun Baru:
**PENTING:** Tidak ada halaman register publik. Registrasi akun baru HANYA dapat dilakukan oleh:
- User yang sudah login dengan role "Ketua HRD/Administrasi" atau "Anggota HRD/Administrasi"
- Melalui menu "Human Resource" → Tab "Input Data Anggota"
- Form akan membuat user account sekaligus HR member data

#### Default Admin Account:
```json
{
  "email": "admin@admin.com",
  "password": "admin",
  "role": "Ketua"
}
```

**Backend Requirements:**
- Endpoint: `POST /api/auth/login`
- Endpoint: `POST /api/auth/logout`
- Endpoint: `POST /api/auth/forgot-password`
- Endpoint: `POST /api/auth/reset-password`
- Endpoint: `GET /api/auth/verify-token`
- Endpoint: `POST /api/auth/refresh-token`
- JWT token dengan expiry
- Refresh token mechanism
- Password hashing dengan salt
- Email service untuk reset password

---

## 3. DASHBOARD UTAMA

### 3.1 Komponen Header

#### User Info Bar:
- Tampilkan email user yang sedang login
- Ambil dari session/token

#### Button "Logout":
- Confirmation dialog: "Apakah Anda yakin ingin keluar?"
- Clear session token
- Redirect ke halaman login
- Backend: Invalidate token

### 3.2 Province Selector

**Dropdown "Pilih Provinsi":**
- **List 39 opsi (1 opsi khusus + 38 provinsi Indonesia):**
  ```
  ** INDONESIA ** (BOLD & KAPITAL - Opsi Khusus)
  1. Nanggroe Aceh Darussalam
  2. Sumatera Utara
  3. Sumatera Barat
  4. Riau
  5. Kepulauan Riau
  6. Jambi
  7. Bengkulu
  8. Sumatera Selatan
  9. Kepulauan Bangka Belitung
  10. Lampung
  11. Banten
  12. DKI Jakarta
  13. Jawa Barat
  14. Jawa Tengah
  15. DI Yogyakarta
  16. Jawa Timur
  17. Bali
  18. Nusa Tenggara Barat
  19. Nusa Tenggara Timur
  20. Kalimantan Barat
  21. Kalimantan Tengah
  22. Kalimantan Selatan
  23. Kalimantan Timur
  24. Kalimantan Utara
  25. Sulawesi Utara
  26. Gorontalo
  27. Sulawesi Tengah
  28. Sulawesi Barat
  29. Sulawesi Selatan
  30. Sulawesi Tenggara
  31. Maluku
  32. Maluku Utara
  33. Papua
  34. Papua Barat
  35. Papua Tengah
  36. Papua Pegunungan
  37. Papua Selatan
  38. Papua Barat Daya
  ```
  
- **Opsi "INDONESIA":**
  - Tampil pertama di dropdown
  - Styling: BOLD & KAPITAL
  - Ketika dipilih: Menampilkan data GABUNGAN dari SELURUH provinsi
  - Use case: Untuk melihat summary nasional atau manage data lintas provinsi
  
- **REQUIRED untuk akses SEMUA module:**
  - Home Page (Dashboard)
  - Program 9 ASNAB
  - Anggaran
  - Persetujuan
  - Logistik
  - Dokumentasi
  - Reaksi Cepat
  - Manajemen Aset
  - Human Resource
  
- Alert jika belum pilih provinsi: "Silakan pilih provinsi terlebih dahulu"
- Store selected province di session

**Backend Requirements:**
- Data provinsi sebaiknya di-cache
- User dapat memiliki akses ke multiple provinsi (role-based)
- Jika province = "INDONESIA", API harus return data dari SEMUA provinsi (aggregate)
- Query optimization untuk aggregation queries

### 3.3 Menu Cards (8 Modules)

#### 1. **Logistik** (Blue)
- Tidak perlu pilih provinsi
- Navigasi ke LogistikPage
- Icon: Truck

#### 2. **Reaksi Cepat** (Orange)
- Tidak perlu pilih provinsi
- Navigasi ke ReaksiCepatPage
- Icon: Zap

#### 3. **Dokumentasi** (Purple)
- Tidak perlu pilih provinsi
- Navigasi ke DokumentasiPage
- Icon: Camera

#### 4. **Manajemen Aset** (Green)
- Tidak perlu pilih provinsi
- Navigasi ke ManajemenAsetPage
- Icon: Building

#### 5. **Human Resource** (Pink)
- Tidak perlu pilih provinsi
- Navigasi ke HumanResourcePage
- Icon: Users

#### 6. **Program 9 ASNAB** (Indigo)
- **HARUS pilih provinsi dulu**
- Alert jika provinsi belum dipilih
- Navigasi ke Program9AsnabPage
- Icon: Target

#### 7. **Anggaran** (Emerald)
- **HARUS pilih provinsi dulu**
- Alert jika provinsi belum dipilih
- Navigasi ke AnggaranPage
- Icon: DollarSign

#### 8. **Persetujuan** (Gray)
- Tidak perlu pilih provinsi
- Navigasi ke PersetujuanPage
- Icon: ClipboardCheck

---

## 4. MODULE: PROGRAM 9 ASNAB

### 4.1 Overview
Program bantuan dengan 9 kategori yang mengikuti **SOP 7 tahap**:
1. Verifikasi Pertama
2. Verifikasi Kedua
3. Verifikasi Kesamaan
4. Planning Meet
5. Logistik
6. Eksekusi
7. Dokumentasi

**PENTING:** Bencana Alam TIDAK masuk di sini, ada di module Reaksi Cepat.

### 4.2 9 Kategori Bantuan
```
1. Yatim Piatu
2. Panti Jompo
3. Kaum Dhuafa
4. Pendidikan
5. Kesehatan
6. Rumah Ibadah
7. Pemberdayaan Ekonomi
8. Lingkungan
9. Sosial Kemasyarakatan
```

### 4.3 Tabs (9 kategori)

#### Button per kategori:
- Menampilkan daftar pengajuan untuk kategori tersebut
- Filter berdasarkan provinsi yang dipilih
- Data per provinsi terpisah

### 4.4 Form Pengajuan Baru

**Button "Tambah Pengajuan":**
- Buka modal/form

**Form Fields:**
```javascript
{
  "namaLembaga": "string (required)",
  "kontakPerson": "string (required)",
  "nomorTelepon": "string (required, phone format)",
  "alamatLengkap": "string (required)",
  "deskripsiKebutuhan": "string (required, textarea)",
  "kategori": "enum (9 kategori)",
  "provinsi": "string (auto-filled from selected)",
  "tanggalPengajuan": "datetime (auto)",
  "status": "VERIFIKASI_PERTAMA (default)",
  "kodeVerifikasi1": null,
  "kodeVerifikasi2": null,
  "verifikator1": null,
  "verifikator2": null,
  "isVerified": false
}
```

**Button "Submit Pengajuan":**
- Validasi semua required fields
- Generate unique ID
- Status awal: "VERIFIKASI_PERTAMA"
- Simpan ke database
- Refresh list
- Show success notification
- Backend: `POST /api/pengajuan`

### 4.5 Detail Pengajuan Card

Setiap card menampilkan:
- Nama Lembaga
- Kontak Person
- Status (badge dengan warna)
- Tanggal Pengajuan
- Kode Verifikasi 1 & 2 (jika ada)

**Status Colors:**
- VERIFIKASI_PERTAMA: Yellow
- VERIFIKASI_KEDUA: Orange
- VERIFIKASI_KESAMAAN: Blue
- PLANNING_MEET: Purple
- LOGISTIK: Cyan
- EKSEKUSI: Indigo
- DOKUMENTASI: Green
- SELESAI: Gray

**Button "Lihat Detail":**
- Expand untuk lihat semua informasi
- Tampilkan deskripsi kebutuhan
- Tampilkan alamat lengkap
- Tampilkan history perubahan status

**Backend Requirements:**
- Endpoint: `GET /api/pengajuan?provinsi={provinsi}&kategori={kategori}`
- Endpoint: `GET /api/pengajuan/{id}`
- Endpoint: `POST /api/pengajuan`
- Endpoint: `PUT /api/pengajuan/{id}`
- Endpoint: `DELETE /api/pengajuan/{id}`
- Pagination untuk list
- Filter & search functionality

---

## 5. MODULE: PERSETUJUAN (VERIFIKASI)

### 5.1 Overview
Halaman untuk admin melakukan verifikasi dua tahap dan menggabungkan hasil verifikasi.

### 5.2 Tab "Verifikasi Pertama"

**Tampilkan:**
- Semua pengajuan dengan status "VERIFIKASI_PERTAMA"
- Filter by kategori (dropdown)
- Filter by provinsi (dropdown)

**Button "Verifikasi":**
- Generate kode verifikasi 8 karakter alphanumerik (unique)
- Contoh: "A3F9K2L8"
- Save kodeVerifikasi1
- Save verifikator1 (user ID yang melakukan verifikasi)
- Save timestamp
- Update status ke "VERIFIKASI_KEDUA"
- Backend: `POST /api/verifikasi/pertama/{pengajuanId}`

**Kode Verifikasi:**
```javascript
{
  "kodeVerifikasi1": "A3F9K2L8",
  "verifikator1": "user_id",
  "verifikasi1Timestamp": "2026-01-09T10:30:00Z",
  "verifikasi1Notes": "string (optional)"
}
```

### 5.3 Tab "Verifikasi Kedua"

**Tampilkan:**
- Semua pengajuan dengan status "VERIFIKASI_KEDUA"
- Filter by kategori
- Filter by provinsi
- Tampilkan kodeVerifikasi1

**Button "Verifikasi Kedua":**
- Generate kode verifikasi 8 karakter alphanumerik (unique)
- Save kodeVerifikasi2
- Save verifikator2 (user ID yang melakukan verifikasi)
- Save timestamp
- Status tetap "VERIFIKASI_KEDUA" (belum digabung)
- Backend: `POST /api/verifikasi/kedua/{pengajuanId}`

**IMPORTANT:**
- Verifikator 2 HARUS berbeda dari verifikator 1
- Backend harus validate ini

### 5.4 Tab "Gabung Verifikasi"

**Tampilkan:**
- Pengajuan yang sudah punya kodeVerifikasi1 DAN kodeVerifikasi2
- Status masih "VERIFIKASI_KEDUA"

**Form Gabung:**
- Input "Kode Verifikasi 1" (8 karakter)
- Input "Kode Verifikasi 2" (8 karakter)
- Display: Nama Lembaga, Kategori

**Button "Validasi & Gabung":**
- Validasi kode 1 cocok dengan kodeVerifikasi1 di database
- Validasi kode 2 cocok dengan kodeVerifikasi2 di database
- Jika keduanya benar:
  - isVerified = true
  - Update status ke "PLANNING_MEET"
  - Show success: "Verifikasi berhasil digabungkan!"
- Jika salah:
  - Show error: "Kode verifikasi tidak cocok!"
  - Log failed attempt (security)
- Backend: `POST /api/verifikasi/gabung/{pengajuanId}`

**Backend Requirements:**
- Endpoint: `GET /api/verifikasi/pertama?kategori={}&provinsi={}`
- Endpoint: `GET /api/verifikasi/kedua?kategori={}&provinsi={}`
- Endpoint: `GET /api/verifikasi/ready-to-merge`
- Endpoint: `POST /api/verifikasi/pertama/{id}`
- Endpoint: `POST /api/verifikasi/kedua/{id}`
- Endpoint: `POST /api/verifikasi/gabung/{id}`
- Unique code generator
- Validation logic
- Audit trail untuk semua verifikasi

---

## 6. MODULE: PLANNING MEET

### 6.1 Overview
Halaman untuk planning meeting dengan penerima bantuan.

### 6.2 Tab "Belum Planning"

**Tampilkan:**
- Pengajuan dengan status "PLANNING_MEET"
- isVerified = true
- Filter by kategori
- Filter by provinsi

**Button "Atur Planning":**
- Buka form modal

**Form Fields:**
```javascript
{
  "tanggalMeeting": "date (required)",
  "waktuMeeting": "time (required)",
  "lokasiMeeting": "string (required)",
  "agendaMeeting": "string (required, textarea)",
  "pesertaMeeting": "string[] (multiple input)",
  "picMeeting": "user_id (required)"
}
```

**Button "Simpan Planning":**
- Save planning data
- Status tetap "PLANNING_MEET"
- Move ke tab "Sudah Planning"
- Backend: `POST /api/planning/{pengajuanId}`

### 6.3 Tab "Sudah Planning"

**Tampilkan:**
- Pengajuan yang sudah ada planning data
- Tampilkan: Tanggal, Waktu, Lokasi, PIC

**Button "Lihat Detail Planning":**
- Tampilkan semua info planning

**Button "Edit Planning":**
- Buka form edit
- Pre-fill dengan data existing
- Backend: `PUT /api/planning/{pengajuanId}`

**Button "Selesai Planning":**
- Confirmation: "Planning sudah dilaksanakan?"
- Jika yes:
  - Isi hasil planning (textarea)
  - Upload notulen (file upload - PDF)
  - Update status ke "ANGGARAN" (untuk isi anggaran)
  - Backend: `POST /api/planning/{id}/complete`

**Backend Requirements:**
- Endpoint: `GET /api/planning?status=pending&kategori={}&provinsi={}`
- Endpoint: `GET /api/planning?status=scheduled&kategori={}&provinsi={}`
- Endpoint: `POST /api/planning/{id}`
- Endpoint: `PUT /api/planning/{id}`
- Endpoint: `POST /api/planning/{id}/complete`
- File storage untuk notulen meeting

---

## 7. MODULE: ANGGARAN

### 7.1 Overview
Halaman untuk mengisi anggaran per semester untuk barang-barang yang akan diberikan.

### 7.2 Tab "Per Program"

**Dropdown Filter:**
- Semester (Semester 1, Semester 2)
- Tahun (2024, 2025, 2026, ...)

**Tampilkan:**
- List program yang sudah selesai Planning
- Status = "ANGGARAN"
- Group by semester yang dipilih

**Button "Isi Anggaran":**
- Buka form anggaran

### 7.3 Form Anggaran

**Dynamic Item List:**
Bisa tambah multiple items (barang)

**Per Item Fields:**
```javascript
{
  "namaBarang": "string (required)",
  "kategoriBarang": "enum (required)",
  "jumlah": "number (required)",
  "satuan": "string (required)",
  "hargaSatuan": "number (required)",
  "totalHarga": "auto-calculated",
  "jenisBarang": "enum (Consumable/Asset)",
  "keterangan": "string (optional)"
}
```

**Enum Kategori Barang:**
```
- Pangan
- Sandang
- Papan
- Elektronik
- Alat Tulis
- Peralatan Kesehatan
- Perabotan
- Kendaraan
- Lainnya
```

**Enum Jenis Barang:**
```
- Consumable (barang habis pakai)
- Asset (aset dengan depresiasi)
```

**Button "+ Tambah Item":**
- Add new item row

**Button "Hapus Item":**
- Remove item dari list

**Auto-calculation:**
- totalHarga = jumlah × hargaSatuan
- grandTotal = sum(all totalHarga)

**Button "Simpan Anggaran":**
- Validasi semua items
- Save anggaran data
- Link anggaran ke pengajuan
- Save semester & tahun
- Items dengan jenisBarang = "Consumable" → masuk ke Logistik
- Items dengan jenisBarang = "Asset" → masuk ke Logistik DAN Manajemen Aset
- Update status pengajuan ke "LOGISTIK"
- Backend: `POST /api/anggaran/{pengajuanId}`

### 7.4 Tab "Riwayat Anggaran"

**Tampilkan:**
- Semua anggaran yang sudah dibuat
- Filter: Semester, Tahun, Kategori Program

**Button "Lihat Detail":**
- Expand untuk lihat semua items
- Tampilkan breakdown biaya

**Button "Edit Anggaran":**
- Hanya bisa edit jika status masih "LOGISTIK" (belum dibeli)
- Backend: `PUT /api/anggaran/{id}`

**Button "Download PDF":**
- Generate PDF anggaran
- Backend: PDF generation

**Backend Requirements:**
- Endpoint: `GET /api/anggaran?semester={}&tahun={}&provinsi={}`
- Endpoint: `GET /api/anggaran/{id}`
- Endpoint: `POST /api/anggaran/{pengajuanId}`
- Endpoint: `PUT /api/anggaran/{id}`
- Endpoint: `GET /api/anggaran/summary?semester={}&tahun={}`
- Automatic item distribution (Consumable → Logistik, Asset → Logistik + Aset)
- PDF generation service

---

## 8. MODULE: LOGISTIK

### 8.1 Overview
Mengelola pembelian barang yang sudah dianggarkan, per semester.

### 8.2 Tab "Belum Dibeli"

**Filter:**
- Semester (dropdown)
- Tahun (dropdown)
- Kategori Program (dropdown)

**Tampilkan:**
- Items dari Anggaran dengan statusPembelian = "PENDING"
- Group by program/pengajuan
- Tampilkan: Nama Barang, Jumlah, Satuan, Harga Satuan, Total, Jenis Barang

**Button "Tandai Dibeli":**
- Buka form pembelian

**Form Pembelian:**
```javascript
{
  "tanggalPembelian": "date (required)",
  "namaToko": "string (required)",
  "alamatToko": "string",
  "nomorFaktur": "string (required, unique)",
  "uploadFaktur": "file (PDF/Image, required)",
  "hargaRealPembelian": "number (required)",
  "keterangan": "string (optional)"
}
```

**Button "Simpan Pembelian":**
- Save data pembelian
- statusPembelian = "PURCHASED"
- Jika jenis = "Asset":
  - Auto create entry di Manajemen Aset
  - Generate asset code
- Move item ke tab "Sudah Dibeli"
- Backend: `POST /api/logistik/purchase/{itemId}`

### 8.3 Tab "Sudah Dibeli"

**Filter:**
- Semester, Tahun, Kategori

**Tampilkan:**
- Items dengan statusPembelian = "PURCHASED"
- Tampilkan tanggal pembelian
- Tampilkan nama toko
- Tampilkan selisih harga (budgeted vs actual)

**Button "Lihat Faktur":**
- Download/view faktur file
- Backend: File serving

**Button "Edit Data Pembelian":**
- Update info pembelian (jika ada kesalahan)
- Backend: `PUT /api/logistik/purchase/{id}`

**Button "Selesai Distribusi":**
- Confirmation dialog
- Update status pengajuan terkait
- Jika semua items sudah dibeli → status program = "EKSEKUSI"
- Backend: `POST /api/logistik/complete-distribution/{pengajuanId}`

**Backend Requirements:**
- Endpoint: `GET /api/logistik?status=pending&semester={}&tahun={}`
- Endpoint: `GET /api/logistik?status=purchased&semester={}&tahun={}`
- Endpoint: `POST /api/logistik/purchase/{itemId}`
- Endpoint: `PUT /api/logistik/purchase/{id}`
- Endpoint: `POST /api/logistik/complete-distribution/{pengajuanId}`
- File storage untuk faktur
- Integration dengan Manajemen Aset (auto-create asset entry)

---

## 9. MODULE: MANAJEMEN ASET

### 9.1 Overview
Tracking aset dengan depresiasi, amortisasi, kondisi, dan maintenance tahunan.

**Sumber Data:**
- Auto-generated dari Logistik (item dengan jenisBarang = "Asset")

### 9.2 Tab "Daftar Aset"

**Filter:**
- Kategori Aset
- Status Kondisi
- Tahun Perolehan
- Range Harga

**Tampilkan List Aset:**

**Data Structure per Aset:**
```javascript
{
  "assetCode": "AST-2026-0001 (auto-generated, unique)",
  "namaAset": "string",
  "kategoriAset": "enum (dari kategoriBarang)",
  "hargaPerolehan": "number",
  "tanggalPerolehan": "date",
  "umurEkonomis": "number (tahun, default 5)",
  "nilaiResidu": "number (default 0)",
  "metodeDepresiasi": "enum (Straight Line/Declining Balance)",
  "nilaiDepresiasi": "auto-calculated",
  "nilaiBuku": "auto-calculated",
  "kondisi": "enum (Baik/Rusak Ringan/Rusak Berat/Hilang)",
  "lokasiAset": "string",
  "picAset": "user_id",
  "programAsal": "pengajuan_id",
  "maintenanceSchedule": "array",
  "lastMaintenance": "date",
  "nextMaintenance": "date"
}
```

**Auto-calculation Depresiasi:**

**Straight Line Method:**
```
depresiasiPerTahun = (hargaPerolehan - nilaiResidu) / umurEkonomis
nilaiBuku = hargaPerolehan - (depresiasiPerTahun × jumlahTahun)
```

**Declining Balance Method:**
```
rate = 2 / umurEkonomis
depresiasiPerTahun = nilaiBuku × rate
nilaiBuku = nilaiBuku - depresiasiPerTahun
```

### 9.3 Button "Lihat Detail Aset"

**Tampilkan:**
- Semua informasi aset
- History depresiasi per tahun (tabel)
- History maintenance
- History perubahan kondisi
- Photo aset (jika ada)

### 9.4 Button "Edit Aset"

**Form Edit:**
- Update kondisi
- Update lokasi
- Update PIC
- Upload photo
- Backend: `PUT /api/aset/{assetCode}`

### 9.5 Tab "Maintenance Checking"

**Tampilkan:**
- Aset yang perlu maintenance checking tahunan
- Filter: Overdue, Due Soon, All

**Button "Jadwalkan Maintenance":**
```javascript
{
  "tanggalMaintenance": "date (required)",
  "jenisMaintenance": "enum (Rutin/Perbaikan/Penggantian)",
  "deskripsiPekerjaan": "string (required)",
  "estimasiBiaya": "number",
  "picMaintenance": "user_id"
}
```

**Button "Catat Hasil Maintenance":**
```javascript
{
  "tanggalSelesai": "date",
  "hasilPemeriksaan": "string",
  "tindakanDilakukan": "string",
  "biayaAktual": "number",
  "kondisiSetelah": "enum (Baik/Rusak Ringan/Rusak Berat)",
  "uploadDokumentasi": "file[]",
  "nextMaintenanceDate": "date (auto, +1 year)"
}
```

### 9.6 Tab "Depresiasi & Amortisasi"

**Tampilkan:**
- Tabel summary depresiasi semua aset
- Group by tahun perolehan
- Total nilai perolehan
- Total depresiasi
- Total nilai buku

**Button "Download Laporan":**
- Generate Excel/PDF laporan depresiasi
- Backend: Report generation

**Filter:**
- Tahun
- Kategori Aset
- Metode Depresiasi

### 9.7 Tab "Aset Bermasalah"

**Tampilkan:**
- Kondisi = "Rusak Ringan" / "Rusak Berat" / "Hilang"

**Button "Laporkan Kehilangan":**
```javascript
{
  "tanggalKehilangan": "date",
  "kronologi": "string (required)",
  "laporanPolisi": "file (optional)",
  "tindakLanjut": "string"
}
```
- Update kondisi = "Hilang"
- Write-off aset
- Backend: `POST /api/aset/{assetCode}/report-loss`

**Button "Ajukan Penghapusan":**
- Untuk aset yang rusak berat/hilang
- Approval workflow
- Backend: `POST /api/aset/{assetCode}/request-disposal`

**Backend Requirements:**
- Endpoint: `GET /api/aset?filter={}`
- Endpoint: `GET /api/aset/{assetCode}`
- Endpoint: `PUT /api/aset/{assetCode}`
- Endpoint: `POST /api/aset` (auto dari logistik)
- Endpoint: `GET /api/aset/depreciation-summary?tahun={}`
- Endpoint: `POST /api/aset/{assetCode}/maintenance/schedule`
- Endpoint: `POST /api/aset/{assetCode}/maintenance/complete`
- Endpoint: `POST /api/aset/{assetCode}/report-loss`
- Endpoint: `POST /api/aset/{assetCode}/request-disposal`
- Auto-calculation service untuk depresiasi
- Scheduled job untuk maintenance reminder
- Report generation service

---

## 10. MODULE: REAKSI CEPAT (BENCANA ALAM)

### 10.1 Overview
Jalur khusus untuk bantuan bencana alam yang **melewati tahap verifikasi** dan langsung masuk Planning Meet.

**Workflow Khusus:**
```
Pengajuan → Planning Meet → Anggaran → Logistik → Eksekusi → Dokumentasi
```

### 10.2 Tab "Pengajuan Baru"

**Button "Tambah Pengajuan Bencana":**

**Form Fields:**
```javascript
{
  "jenisBencana": "enum (required)",
  "lokasiKejadian": "string (required)",
  "provinsi": "string (required)",
  "kabupaten": "string (required)",
  "tanggalKejadian": "date (required)",
  "estimasiKorban": "number",
  "estimasiKerugian": "string",
  "deskripsiSituasi": "string (required, textarea)",
  "kontakLokasi": "string (required)",
  "nomorKontak": "string (required)",
  "tingkatUrgensi": "enum (Rendah/Sedang/Tinggi/Kritis)",
  "statusPengajuan": "PLANNING_MEET (auto)"
}
```

**Enum Jenis Bencana:**
```
- Gempa Bumi
- Tsunami
- Banjir
- Tanah Longsor
- Kebakaran
- Gunung Meletus
- Angin Puting Beliung
- Kekeringan
- Lainnya
```

**Enum Tingkat Urgensi:**
```
- Rendah (hijau)
- Sedang (kuning)
- Tinggi (orange)
- Kritis (merah)
```

**Button "Submit Pengajuan Cepat":**
- Save data
- Status langsung = "PLANNING_MEET"
- **Skip verifikasi**
- Notification ke tim Reaksi Cepat
- Backend: `POST /api/reaksi-cepat`

### 10.3 Tab "Planning Meet"

**Tampilkan:**
- Pengajuan bencana dengan status "PLANNING_MEET"
- Sort by tingkatUrgensi (Kritis first)

**Button "Atur Planning Cepat":**
- Form sama seperti planning biasa
- Priority scheduling
- Backend: `POST /api/reaksi-cepat/{id}/planning`

### 10.4 Tab "Dalam Proses"

**Tampilkan:**
- Status: ANGGARAN, LOGISTIK, EKSEKUSI
- Progress indicator
- Timeline tracking

### 10.5 Tab "Selesai"

**Tampilkan:**
- Status: DOKUMENTASI, SELESAI
- Laporan akhir

**Backend Requirements:**
- Endpoint: `POST /api/reaksi-cepat`
- Endpoint: `GET /api/reaksi-cepat?status={}&urgensi={}`
- Endpoint: `PUT /api/reaksi-cepat/{id}`
- Endpoint: `POST /api/reaksi-cepat/{id}/planning`
- Notification service (urgent alerts)
- Integration dengan workflow reguler (setelah planning)

---

## 11. MODULE: DOKUMENTASI

### 11.1 Overview
Upload dokumentasi untuk program yang sudah eksekusi.

### 11.2 Tab "Belum Upload"

**Tampilkan:**
- Program dengan status "EKSEKUSI" yang sudah selesai distribusi
- Filter: Kategori, Provinsi, Tahun

**Button "Upload Dokumentasi":**

**Form Upload:**
```javascript
{
  "tanggalDokumentasi": "date (required)",
  "lokasiDokumentasi": "string (required)",
  "deskripsiKegiatan": "string (required, textarea)",
  "jumlahPenerima": "number (required)",
  "uploadFoto": "file[] (multiple, required, min 3)",
  "uploadVideo": "file[] (optional)",
  "uploadLaporanPDF": "file (optional)",
  "kontakPenerima": "string",
  "feedback": "string (textarea)",
  "picDokumentasi": "user_id"
}
```

**Validasi Upload:**
- Foto: JPG/PNG, max 5MB per file, min 3 foto
- Video: MP4/MOV, max 50MB per file
- PDF: max 10MB

**Button "Simpan Dokumentasi":**
- Upload semua files ke storage
- Save metadata
- Update status program = "DOKUMENTASI"
- Backend: `POST /api/dokumentasi/{pengajuanId}`

### 11.3 Tab "Sudah Upload"

**Tampilkan:**
- Program dengan status "DOKUMENTASI"
- Thumbnail foto pertama
- Tanggal dokumentasi

**Button "Lihat Dokumentasi":**
- Gallery view semua foto
- Video player (jika ada)
- PDF viewer (jika ada)
- Deskripsi kegiatan
- Feedback

**Button "Edit Dokumentasi":**
- Update data
- Re-upload files
- Backend: `PUT /api/dokumentasi/{id}`

**Button "Approve & Selesaikan":**
- Confirmation dialog
- Update status program = "SELESAI"
- Program complete
- Generate completion certificate (optional)
- Backend: `POST /api/dokumentasi/{id}/complete`

**Button "Download Laporan":**
- Generate PDF report dengan foto
- Backend: Report generation

**Backend Requirements:**
- Endpoint: `GET /api/dokumentasi?status=pending`
- Endpoint: `GET /api/dokumentasi?status=completed`
- Endpoint: `POST /api/dokumentasi/{pengajuanId}`
- Endpoint: `PUT /api/dokumentasi/{id}`
- Endpoint: `GET /api/dokumentasi/{id}`
- Endpoint: `POST /api/dokumentasi/{id}/complete`
- Cloud storage untuk files (S3/CloudStorage)
- Image thumbnail generation
- Video streaming service
- Report generation dengan images

---

## 12. MODULE: HUMAN RESOURCE

### 12.1 Overview
Mengelola data anggota organisasi dengan 23 role berbeda.

### 12.2 Tab "Search Nomor Anggota"

**Input Search:**
- Nomor Anggota (text input)

**Button "Cari":**
- Search by memberNumber
- Tampilkan hasil dalam card
- Backend: `GET /api/hr/members/search?q={query}`

**Hasil Search Card:**
- Display: Foto, Nomor Anggota, Email, Gaji, Role, Tanggal Terdaftar
- Button "Edit" → Pindah ke tab Edit dengan data pre-filled

### 12.3 Tab "Input Data Anggota"

**Form Fields:**
```javascript
{
  "memberNumber": "string (required, unique)",
  "email": "string (required, email format, unique)",
  "password": "string (required for new, hashed)",
  "photo": "file (optional, image, max 5MB)",
  "salary": "number (required)",
  "contract": "file (optional, PDF/DOC, max 10MB)",
  "role": "enum (required)",
  "status": "enum (Aktif/Nonaktif)",
  "joinDate": "date (auto)",
  "createdAt": "datetime (auto)"
}
```

**9 Roles Enum:**
```
- BPH PUSAT
- DOKUMENTASI PUSAT
- UNIT REAKSI CEPAT PUSAT
- MANAJEMEN ASET PUSAT
- HRD PUSAT
- HUMAS PUSAT
- HUKUM/PAJAK PUSAT
- LOGISTIK PUSAT
- KARUNGGA PUSAT
```

**Button "Pilih Foto":**
- File picker untuk photo
- Preview image setelah dipilih
- Validation: max 5MB, format JPG/PNG

**Button "X" (Remove Photo):**
- Clear selected photo
- Reset file input

**Button "Pilih File" (Kontrak):**
- File picker untuk contract
- Show file name & size setelah dipilih
- Validation: max 10MB, format PDF/DOC/DOCX

**Button "X" (Remove Contract):**
- Clear selected contract
- Reset file input

**Input Gaji:**
- Auto-format dengan separator ribuan (Rupiah format)
- Input: 5000000 → Display: Rp 5.000.000

**Dropdown "Pilih role":**
- List 9 roles

**Button "Simpan Data Anggota":**
- Validasi semua required fields
- Check memberNumber unique
- Check email unique
- Hash password
- Upload photo (jika ada)
- Upload contract (jika ada)
- Save to database
- Show success notification
- Reset form
- Backend: `POST /api/hr/members`

### 12.4 Tab "Edit Data Anggota"

**Tampilkan List Anggota:**
- Card per member
- Display: Photo, Member Number, Email, Role, Gaji
- Badge untuk role (warna pink)
- Sort by: newest first

**Button "Edit":**
- Load member data ke form edit
- Pre-fill semua fields

**Form Edit:**
- Fields sama seperti input
- memberNumber tidak bisa diubah (read-only)
- Photo & Contract bisa diganti atau keep existing

**Button "Batal":**
- Clear form
- Kembali ke list view

**Button "Update Data":**
- Validasi input
- Jika photo baru → upload & replace old
- Jika contract baru → upload & replace old
- Update database
- Show success notification
- Backend: `PUT /api/hr/members/{memberNumber}`

**Button "X" (Delete Member):**
- Confirmation: "Apakah Anda yakin ingin menghapus data anggota ini?"
- Jika yes:
  - Soft delete (recommended) atau hard delete
  - Remove dari list
  - Show notification
- Backend: `DELETE /api/hr/members/{id}`

**Empty State:**
- Jika belum ada anggota: "Belum ada data anggota"
- Icon User besar
- Text gray

**Backend Requirements:**
- Endpoint: `GET /api/hr/members?page={}&limit={}`
- Endpoint: `GET /api/hr/members/search?q={}`
- Endpoint: `GET /api/hr/members/{memberNumber}`
- Endpoint: `POST /api/hr/members`
- Endpoint: `PUT /api/hr/members/{memberNumber}`
- Endpoint: `DELETE /api/hr/members/{id}`
- File storage untuk photo & contract
- Password hashing
- Unique constraints (memberNumber, email)
- Salary formatting

---

## 13. DATA RELATIONSHIPS

### 13.1 Database Schema Overview

```
Users
├── id (PK)
├── email (unique)
├── password (hashed)
├── role
└── createdAt

HRMembers
├── id (PK)
├── memberNumber (unique)
├── email (unique)
├── passwordHash
├── photoUrl
├── salary
├── contractUrl
├── role (enum 9 roles)
├── status
├── joinDate
└── userId (FK to Users, optional)

Pengajuan (Program 9 ASNAB)
├── id (PK)
├── namaLembaga
├── kontakPerson
├── nomorTelepon
├── alamatLengkap
├── deskripsiKebutuhan
├── kategori (enum 9 kategori)
├── provinsi
├── status (workflow status)
├── kodeVerifikasi1
├── kodeVerifikasi2
├── verifikator1 (FK to Users)
├── verifikator2 (FK to Users)
├── isVerified
├── createdAt
└── updatedAt

ReaksiCepat
├── id (PK)
├── jenisBencana
├── lokasiKejadian
├── provinsi
├── tanggalKejadian
├── tingkatUrgensi
├── status
└── createdAt

Planning
├── id (PK)
├── pengajuanId (FK, nullable)
├── reaksiCepatId (FK, nullable)
├── tanggalMeeting
├── waktuMeeting
├── lokasiMeeting
├── agendaMeeting
├── picMeeting (FK to Users)
├── hasilMeeting
├── notulenUrl
└── completedAt

Anggaran
├── id (PK)
├── pengajuanId (FK)
├── semester
├── tahun
├── grandTotal
└── createdAt

AnggaranItem
├── id (PK)
├── anggaranId (FK)
├── namaBarang
├── kategoriBarang
├── jumlah
├── satuan
├── hargaSatuan
├── totalHarga
├── jenisBarang (Consumable/Asset)
└── keterangan

LogistikPurchase
├── id (PK)
├── anggaranItemId (FK)
├── tanggalPembelian
├── namaToko
├── nomorFaktur (unique)
├── fakturUrl
├── hargaRealPembelian
├── statusPembelian
└── createdAt

Asset
├── id (PK)
├── assetCode (unique)
├── anggaranItemId (FK)
├── logistikPurchaseId (FK)
├── namaAset
├── kategoriAset
├── hargaPerolehan
├── tanggalPerolehan
├── umurEkonomis
├── nilaiResidu
├── metodeDepresiasi
├── kondisi
├── lokasiAset
├── picAset (FK to Users)
└── createdAt

AssetDepreciation (calculated yearly)
├── id (PK)
├── assetId (FK)
├── tahun
├── depresiasiValue
├── nilaiBuku
└── calculatedAt

AssetMaintenance
├── id (PK)
├── assetId (FK)
├── tanggalMaintenance
├── jenisMaintenance
├── deskripsiPekerjaan
├── biayaAktual
├── kondisiSetelah
├── dokumentasiUrls
└── completedAt

Dokumentasi
├── id (PK)
├── pengajuanId (FK, nullable)
├── reaksiCepatId (FK, nullable)
├── tanggalDokumentasi
├── lokasiDokumentasi
├── deskripsiKegiatan
├── jumlahPenerima
├── fotoUrls (array)
├── videoUrls (array)
├── laporanUrl
├── feedback
├── picDokumentasi (FK to Users)
└── completedAt

VerifikasiLog (audit trail)
├── id (PK)
├── pengajuanId (FK)
├── verifikator (FK to Users)
├── tahap (1/2/gabung)
├── kodeVerifikasi
├── status (success/failed)
├── notes
└── timestamp
```

### 13.2 Status Flow

**Program 9 ASNAB Status:**
```
VERIFIKASI_PERTAMA → 
VERIFIKASI_KEDUA → 
PLANNING_MEET → 
ANGGARAN → 
LOGISTIK → 
EKSEKUSI → 
DOKUMENTASI → 
SELESAI
```

**Reaksi Cepat Status:**
```
PLANNING_MEET → 
ANGGARAN → 
LOGISTIK → 
EKSEKUSI → 
DOKUMENTASI → 
SELESAI
```

---

## 14. API ENDPOINTS SUMMARY

### Authentication
```
POST   /api/auth/register
POST   /api/auth/login
POST   /api/auth/logout
GET    /api/auth/verify-token
POST   /api/auth/refresh-token
POST   /api/auth/forgot-password
POST   /api/auth/reset-password
```

### Pengajuan (Program 9 ASNAB)
```
GET    /api/pengajuan?provinsi={}&kategori={}&status={}
GET    /api/pengajuan/{id}
POST   /api/pengajuan
PUT    /api/pengajuan/{id}
DELETE /api/pengajuan/{id}
```

### Verifikasi
```
GET    /api/verifikasi/pertama?kategori={}&provinsi={}
GET    /api/verifikasi/kedua?kategori={}&provinsi={}
GET    /api/verifikasi/ready-to-merge
POST   /api/verifikasi/pertama/{pengajuanId}
POST   /api/verifikasi/kedua/{pengajuanId}
POST   /api/verifikasi/gabung/{pengajuanId}
```

### Planning
```
GET    /api/planning?status={}&kategori={}&provinsi={}
GET    /api/planning/{id}
POST   /api/planning/{pengajuanId}
PUT    /api/planning/{id}
POST   /api/planning/{id}/complete
```

### Anggaran
```
GET    /api/anggaran?semester={}&tahun={}&provinsi={}
GET    /api/anggaran/{id}
POST   /api/anggaran/{pengajuanId}
PUT    /api/anggaran/{id}
GET    /api/anggaran/summary?semester={}&tahun={}
GET    /api/anggaran/{id}/pdf
```

### Logistik
```
GET    /api/logistik?status={}&semester={}&tahun={}
GET    /api/logistik/{id}
POST   /api/logistik/purchase/{itemId}
PUT    /api/logistik/purchase/{id}
POST   /api/logistik/complete-distribution/{pengajuanId}
GET    /api/logistik/faktur/{id}
```

### Manajemen Aset
```
GET    /api/aset?kategori={}&kondisi={}&tahun={}
GET    /api/aset/{assetCode}
POST   /api/aset (auto from logistik)
PUT    /api/aset/{assetCode}
DELETE /api/aset/{assetCode}
GET    /api/aset/depreciation-summary?tahun={}
POST   /api/aset/{assetCode}/maintenance/schedule
POST   /api/aset/{assetCode}/maintenance/complete
GET    /api/aset/{assetCode}/maintenance-history
POST   /api/aset/{assetCode}/report-loss
POST   /api/aset/{assetCode}/request-disposal
GET    /api/aset/depreciation/{assetCode}/calculate
```

### Reaksi Cepat
```
GET    /api/reaksi-cepat?status={}&urgensi={}
GET    /api/reaksi-cepat/{id}
POST   /api/reaksi-cepat
PUT    /api/reaksi-cepat/{id}
POST   /api/reaksi-cepat/{id}/planning
```

### Dokumentasi
```
GET    /api/dokumentasi?status={}&kategori={}&provinsi={}
GET    /api/dokumentasi/{id}
POST   /api/dokumentasi/{pengajuanId}
PUT    /api/dokumentasi/{id}
POST   /api/dokumentasi/{id}/complete
GET    /api/dokumentasi/{id}/files/{fileId}
GET    /api/dokumentasi/{id}/report
```

### Human Resource
```
GET    /api/hr/members?page={}&limit={}
GET    /api/hr/members/search?q={}
GET    /api/hr/members/{memberNumber}
POST   /api/hr/members
PUT    /api/hr/members/{memberNumber}
DELETE /api/hr/members/{id}
```

### File Management
```
POST   /api/files/upload (multipart/form-data)
GET    /api/files/{fileId}
DELETE /api/files/{fileId}
```

---

## 15. SECURITY REQUIREMENTS

### 15.1 Authentication
- JWT token dengan expiry (recommended: 1 hour)
- Refresh token mechanism (expiry: 7 days)
- Secure password hashing (bcrypt/argon2 dengan salt)
- Token blacklist untuk logout

### 15.2 Authorization
- Role-based access control (RBAC)
- Permission matrix per role
- Endpoint protection dengan middleware

**Role Permissions Matrix:**
```
BPH PUSAT: Full access semua module
DOKUMENTASI PUSAT: Read-write Dokumentasi, Read-only lainnya
UNIT REAKSI CEPAT PUSAT: Full Reaksi Cepat, Read-only lainnya
MANAJEMEN ASET PUSAT: Full Manajemen Aset, Read-only lainnya
HRD PUSAT: Full Human Resource, Read-only lainnya
LOGISTIK PUSAT: Full Logistik, Read-only lainnya
HUMAS PUSAT: Read-only all
HUKUM/PAJAK PUSAT: Read all, Write Anggaran & Laporan
KARUNGGA PUSAT: Custom permissions
```

### 15.3 Data Security
- Input sanitization (prevent XSS, SQL injection)
- File upload validation (type, size, scan malware)
- HTTPS only
- CORS configuration
- Rate limiting API calls

### 15.4 Audit Trail
- Log semua perubahan data penting
- Track user actions (who, what, when)
- Khusus untuk Verifikasi: log semua attempts

**Audit Log Structure:**
```javascript
{
  "userId": "user_id",
  "action": "CREATE/UPDATE/DELETE/LOGIN/LOGOUT",
  "module": "PENGAJUAN/VERIFIKASI/etc",
  "entityId": "id of affected entity",
  "changes": "JSON of before/after values",
  "ipAddress": "IP address",
  "userAgent": "browser info",
  "timestamp": "ISO datetime"
}
```

---

## 16. FILE STORAGE

### 16.1 Storage Strategy
**Recommended:** Cloud storage (AWS S3 / Google Cloud Storage / Azure Blob)

### 16.2 File Types & Limits

**Foto:**
- Format: JPG, PNG, WEBP
- Max size: 5MB per file
- Thumbnail generation: Auto-create 200x200px thumbnail

**Kontrak & Faktur:**
- Format: PDF, DOC, DOCX
- Max size: 10MB per file

**Video Dokumentasi:**
- Format: MP4, MOV, AVI
- Max size: 50MB per file
- Consider video compression

**Laporan PDF:**
- Format: PDF only
- Max size: 10MB

### 16.3 File Naming Convention
```
{module}/{year}/{month}/{unique-id}_{original-name}

Example:
hr/2026/01/uuid-123_contract.pdf
dokumentasi/2026/01/uuid-456_photo1.jpg
logistik/2026/01/uuid-789_faktur.pdf
```

### 16.4 Access Control
- Private files (require authentication)
- Generate signed URLs untuk temporary access
- CDN untuk public files (photos in documentation)

---

## 17. NOTIFICATIONS

### 17.1 In-App Notifications

**Trigger Events:**
- Pengajuan baru masuk
- Verifikasi selesai
- Planning dijadwalkan
- Barang dibeli (logistik)
- Maintenance overdue
- Dokumentasi diupload
- Program selesai

### 17.2 Email Notifications

**Send email untuk:**
- Welcome email (register)
- Password reset
- Urgent bencana alam (tingkat Kritis)
- Maintenance reminder (7 days before)
- Monthly summary report

### 17.3 Notification Structure
```javascript
{
  "userId": "recipient",
  "type": "INFO/WARNING/URGENT",
  "module": "module name",
  "title": "notification title",
  "message": "notification body",
  "actionUrl": "deep link",
  "isRead": false,
  "createdAt": "timestamp"
}
```

---

## 18. REPORTS & ANALYTICS

### 18.1 Reports to Generate

**1. Laporan Anggaran Semester**
- Summary per kategori program
- Actual vs Budget
- Download: PDF, Excel

**2. Laporan Logistik**
- Items purchased
- Vendor analysis
- Price variance
- Download: PDF, Excel

**3. Laporan Aset & Depresiasi**
- Asset list dengan nilai buku
- Depreciation schedule
- Maintenance history
- Download: PDF, Excel

**4. Laporan Program (per kategori)**
- Total program completed
- Timeline analysis
- Beneficiary count
- Download: PDF dengan photos

**5. Dashboard Analytics**
- Total program by status (chart)
- Total budget by semester (chart)
- Asset value trend (chart)
- Program completion rate (%)

### 18.2 Export Formats
- PDF: For official reports
- Excel: For data analysis
- CSV: For raw data export

---

## 19. SCHEDULED JOBS

### 19.1 Daily Jobs
- Calculate depreciation untuk assets (run at midnight)
- Check maintenance schedules (send reminders)
- Cleanup expired tokens
- Backup database

### 19.2 Weekly Jobs
- Generate weekly summary report
- Cleanup old temporary files

### 19.3 Monthly Jobs
- Generate monthly analytics
- Archive completed programs (older than 1 year)

### 19.4 Yearly Jobs
- Annual depreciation calculation
- Asset revaluation reminder
- Archive old audit logs

---

## 20. ERROR HANDLING

### 20.1 HTTP Status Codes
```
200 OK: Success
201 Created: Resource created
204 No Content: Deleted successfully
400 Bad Request: Validation error
401 Unauthorized: Not authenticated
403 Forbidden: Not authorized
404 Not Found: Resource not found
409 Conflict: Duplicate entry
422 Unprocessable Entity: Business logic error
500 Internal Server Error: Server error
```

### 20.2 Error Response Format
```javascript
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable message",
    "details": {
      "field": "field_name",
      "issue": "specific issue"
    }
  },
  "timestamp": "ISO datetime"
}
```

### 20.3 Common Error Codes
```
AUTH_INVALID_CREDENTIALS
AUTH_TOKEN_EXPIRED
AUTH_INSUFFICIENT_PERMISSION
VALIDATION_REQUIRED_FIELD
VALIDATION_INVALID_FORMAT
VALIDATION_DUPLICATE_ENTRY
BUSINESS_INVALID_STATUS_TRANSITION
BUSINESS_VERIFIKASI_CODE_MISMATCH
FILE_TOO_LARGE
FILE_INVALID_TYPE
RESOURCE_NOT_FOUND
```

---

## 21. PERFORMANCE REQUIREMENTS

### 21.1 Response Time
- API response: < 200ms (simple queries)
- API response: < 1000ms (complex queries with joins)
- File upload: Progress indicator untuk files > 1MB
- Report generation: Async job untuk reports > 10 pages

### 21.2 Scalability
- Support 1000+ concurrent users
- Database indexing pada foreign keys & search fields
- Query optimization dengan proper indexes
- Caching strategy (Redis recommended)

### 21.3 Caching Strategy
**Cache these:**
- Province list (static data)
- User roles & permissions
- Enum values (kategori, status, etc)
- Dashboard statistics (cache 5 minutes)

### 21.4 Database Optimization
- Index pada: email, memberNumber, assetCode, nomorFaktur
- Soft delete untuk historical data
- Archival strategy untuk old records
- Connection pooling

---

## 22. TESTING REQUIREMENTS

### 22.1 Unit Tests
- Test all business logic functions
- Test calculation functions (depreciation, totals)
- Test validation functions
- Coverage target: > 80%

### 22.2 Integration Tests
- Test all API endpoints
- Test authentication flows
- Test file upload flows
- Test workflow transitions

### 22.3 Load Testing
- Test concurrent users (target: 1000)
- Test file upload under load
- Test report generation under load

### 22.4 Security Testing
- SQL injection testing
- XSS testing
- CSRF testing
- Authentication bypass testing
- Authorization testing

---

## 23. DEPLOYMENT

### 23.1 Environment Setup
```
Development: Local development
Staging: Testing environment (mirror production)
Production: Live environment
```

### 23.2 Environment Variables
```
DATABASE_URL
JWT_SECRET
JWT_REFRESH_SECRET
STORAGE_BUCKET
STORAGE_ACCESS_KEY
STORAGE_SECRET_KEY
EMAIL_SERVICE_API_KEY
FRONTEND_URL
API_URL
NODE_ENV
PORT
```

### 23.3 CI/CD Pipeline
- Auto-run tests on commit
- Auto-deploy to staging on merge to develop
- Manual approval untuk production deployment
- Rollback strategy

### 23.4 Monitoring
- Application logs (Winston/Morgan)
- Error tracking (Sentry recommended)
- Performance monitoring (New Relic/Datadog)
- Uptime monitoring

---

## 24. MIGRATION FROM LOCALSTORAGE

### 24.1 Current Frontend Data Storage

Frontend saat ini menggunakan **localStorage** untuk:
- User authentication (`currentUser`, `users`)
- HR Members (`hrMembers`)
- Program pengajuan (various keys)
- Verifikasi codes
- Planning data
- Anggaran data
- Logistik data
- Asset data
- Dokumentasi data

### 24.2 Migration Strategy

**Phase 1: Backend Setup**
1. Setup database schema
2. Implement all API endpoints
3. Test dengan Postman/Insomnia

**Phase 2: Gradual Migration**
1. Migrate Authentication first
2. Migrate HR module
3. Migrate Program module
4. Migrate supporting modules (Logistik, Aset, dll)

**Phase 3: Data Migration**
1. Create data migration script
2. Export dari localStorage
3. Transform & import ke database
4. Verify data integrity

**Phase 4: Frontend Update**
1. Replace localStorage calls dengan API calls
2. Implement loading states
3. Implement error handling
4. Test all workflows

---

## 25. API INTEGRATION GUIDE FOR FRONTEND

### 25.1 API Client Setup

**Recommended: Axios with interceptors**

```javascript
// api/client.js
import axios from 'axios';

const apiClient = axios.create({
  baseURL: process.env.REACT_APP_API_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request interceptor - add token
apiClient.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('accessToken');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// Response interceptor - handle errors
apiClient.interceptors.response.use(
  (response) => response.data,
  async (error) => {
    if (error.response?.status === 401) {
      // Token expired, try refresh
      const refreshToken = localStorage.getItem('refreshToken');
      if (refreshToken) {
        try {
          const { accessToken } = await refreshTokenAPI(refreshToken);
          localStorage.setItem('accessToken', accessToken);
          // Retry original request
          error.config.headers.Authorization = `Bearer ${accessToken}`;
          return axios(error.config);
        } catch (refreshError) {
          // Refresh failed, logout
          localStorage.clear();
          window.location.href = '/login';
        }
      }
    }
    return Promise.reject(error);
  }
);

export default apiClient;
```

### 25.2 Example API Service

```javascript
// services/pengajuanService.js
import apiClient from '../api/client';

export const pengajuanService = {
  getAll: (params) => 
    apiClient.get('/api/pengajuan', { params }),
  
  getById: (id) => 
    apiClient.get(`/api/pengajuan/${id}`),
  
  create: (data) => 
    apiClient.post('/api/pengajuan', data),
  
  update: (id, data) => 
    apiClient.put(`/api/pengajuan/${id}`, data),
  
  delete: (id) => 
    apiClient.delete(`/api/pengajuan/${id}`),
};
```

### 25.3 File Upload Example

```javascript
// services/fileService.js
export const uploadFile = async (file, onProgress) => {
  const formData = new FormData();
  formData.append('file', file);
  
  return apiClient.post('/api/files/upload', formData, {
    headers: {
      'Content-Type': 'multipart/form-data',
    },
    onUploadProgress: (progressEvent) => {
      const percentCompleted = Math.round(
        (progressEvent.loaded * 100) / progressEvent.total
      );
      onProgress?.(percentCompleted);
    },
  });
};
```

---

## 26. ADDITIONAL FEATURES (FUTURE ENHANCEMENTS)

### 26.1 Phase 2 Features
- WhatsApp notification integration
- Mobile app (React Native)
- Real-time collaboration (Socket.IO)
- Advanced analytics dashboard
- Export to accounting software
- QR code untuk asset tracking
- Geolocation untuk dokumentasi
- Multi-language support

### 26.2 Phase 3 Features
- AI-powered photo recognition untuk dokumentasi
- Predictive analytics untuk budgeting
- Automated report generation
- Integration dengan payment gateway
- Blockchain untuk transparency
- Public portal untuk transparency report

---

## 27. CONTACT & SUPPORT

### 27.1 Technical Questions
Untuk pertanyaan teknis terkait backend implementation, hubungi frontend developer untuk klarifikasi workflow dan UI/UX.

### 27.2 Business Logic Questions
Konsultasi dengan stakeholder yayasan untuk business rules yang belum jelas.

### 27.3 API Documentation
Setelah backend selesai, buat dokumentasi API lengkap dengan:
- Swagger/OpenAPI specification
- Postman collection
- Integration examples
- Error handling guide

---

## 28. GLOSSARY

**Asnab:** Akronim dari program yayasan (detail 9 kategori)  
**BPH:** Badan Pengurus Harian  
**Consumable:** Barang habis pakai  
**Asset:** Aset dengan depresiasi  
**Depresiasi:** Penyusutan nilai aset  
**Amortisasi:** Pengalokasian biaya aset  
**PIC:** Person In Charge  
**SOP:** Standard Operating Procedure  
**Verifikasi Kesamaan:** Penggabungan dua verifikasi independen  

---

## CONCLUSION

Dokumen ini adalah panduan lengkap untuk backend developer dalam membangun sistem backend yang terintegrasi dengan frontend React yang sudah ada. Pastikan untuk:

1. ✅ Implement semua endpoint yang disebutkan
2. ✅ Follow data structure yang didefinisikan
3. ✅ Implement security requirements
4. ✅ Create proper error handling
5. ✅ Setup file storage dengan benar
6. ✅ Implement audit trail
7. ✅ Create API documentation
8. ✅ Write tests (unit & integration)
9. ✅ Setup monitoring & logging
10. ✅ Plan for scalability

**Happy coding! 🚀**

---

**Document Version:** 1.0  
**Last Updated:** 2026-01-09  
**Prepared for:** Backend Development Team  
**Prepared by:** AI Assistant based on Frontend Implementation