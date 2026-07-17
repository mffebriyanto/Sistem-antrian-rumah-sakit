# Sistem Antrian Rumah Sakit 🏥
Proyek Akhir — Algoritma dan Pemrograman

## Identitas
- Kelompok: Sistem Antrian Rumah Sakit — Kelompok 5
- Nama Anggota:
  1. Ivanka Widyaningrum (0112525005)
  2. Lolita Disry Mustika (0112525006)
  3. Muhammad Fajri Febriyanto (0112525008)
  4. Sahid Haqqi An Najil (0112525013)
- Mata Kuliah: Algoritma dan Pemrograman (3 SKS)
- Program Studi: Informatika, Universitas Al Azhar Indonesia
- Dosen: Tri Aji Nugroho, S.T., M.T.
- Semester: Genap 2025/2026

## Deskripsi
Aplikasi konsol Python untuk mengelola antrian pasien rumah sakit/puskesmas
secara digital, dengan pembedaan prioritas antara pasien darurat, lansia,
dan umum. Aplikasi ini mencatat riwayat kunjungan dan riwayat penyakit tiap
pasien, mendukung kunjungan ulang tanpa perlu mendaftar ulang biodata, serta
memberikan estimasi waktu tunggu berdasarkan data historis.

Dikembangkan sebagai proyek akhir mata kuliah **Algoritma dan Pemrograman** —
Universitas Al Azhar Indonesia (UAI).

## Fitur
1. **Pendaftaran pasien** — mendukung pasien baru maupun kunjungan ulang.
   Pasien lama bisa dicari lewat Nomor RM, NIK, atau nama (tanpa perlu
   mengulang input nama/umur/NIK), lengkap dengan validasi berlapis (nama,
   umur, NIK 16 digit, keluhan) dan konfirmasi akhir sebelum data disimpan.
2. **Panggil pasien berikutnya** — antrian bertingkat berdasarkan prioritas
   medis: **Darurat → Lansia → Umum**, dengan disiplin FIFO di dalam
   masing-masing kategori.
3. **Lihat antrian saat ini** — menampilkan seluruh pasien yang sedang
   menunggu, dikelompokkan per kategori prioritas.
4. **Cari pasien** — pencarian pasien yang sedang aktif berdasarkan Nomor
   RM atau nama.
5. **Riwayat kunjungan pasien** — dicari lewat Nomor RM, NIK, atau nama
   (dengan penanganan otomatis jika nama yang dicari cocok untuk lebih dari
   satu pasien). Riwayat penyakit sebelumnya juga ditampilkan otomatis saat
   pasien lama mendaftar kunjungan ulang, membantu petugas melakukan
   penanganan awal.
6. **Statistik harian** — jumlah pasien per kategori dan rata-rata waktu
   tunggu pada hari berjalan.
7. **Laporan kunjungan** — bisa diurutkan berdasarkan waktu daftar,
   kategori, atau jumlah kunjungan.
8. **Estimasi waktu tunggu** — dihitung dari posisi pasien dalam antrian
   (termasuk memperhitungkan antrian prioritas lebih tinggi yang belum
   dilayani) dikalikan rata-rata durasi layanan historis.
9. **Data demo otomatis** — 18 pasien contoh dimuat otomatis setiap program
   dijalankan, supaya fitur 2–8 bisa langsung diuji tanpa registrasi manual.

## Cara Menjalankan

### Prasyarat
- Python 3.8 atau lebih baru
- Tidak ada dependency eksternal — hanya memakai modul bawaan (`datetime`)

### Menjalankan di Google Colab
1. Buka [Google Colab](https://colab.research.google.com)
2. Buat notebook baru, lalu buat satu cell baru
3. Salin seluruh isi `sistem_antrian_rs.py` ke cell tersebut
4. Jalankan cell (Shift + Enter)

### Menjalankan Secara Lokal
```bash
python3 sistem_antrian_rs.py
```

## Struktur Proyek
```
.
├── sistem_antrian_rs.ipynb           # Kode program utama
├── AI_Usage_Log_Sistem_Antrian_RS.md # Log transparansi penggunaan AI
└── README.md                         # Dokumentasi proyek (file ini)
```

## Struktur Data

| Sub-masalah | Struktur | Alasan Pemilihan |
|---|---|---|
| Data pasien | `dict` (key: Nomor RM) | Akses O(1) — begitu Nomor RM dipanggil, biodata langsung tersedia tanpa harus menelusuri satu per satu |
| Antrian FIFO + prioritas | `dict` berisi 3 `list` (`{"darurat": [], "lansia": [], "umum": []}`) | Memisahkan pasien per level prioritas sekaligus menjaga urutan FIFO di dalam tiap level, tanpa perlu sorting ulang setiap ada pasien baru |
| Riwayat kunjungan | `dict` (key: Nomor RM) berisi `list` of `dict` | Riwayat dikelompokkan per pasien (akses cepat via key) sekaligus menyimpan banyak kunjungan berurutan waktu |
| Riwayat durasi layanan | `list` | Hanya perlu ditambah di akhir dan dibaca semua untuk dihitung rata-ratanya |

Variabel global utama:
```python
data_pasien = {}                                       # key: no_rm -> dict biodata
antrian = {"darurat": [], "lansia": [], "umum": []}     # key: kategori -> list no_rm
riwayat_kunjungan = {}                                  # key: no_rm -> list of dict kunjungan
riwayat_durasi_layanan = []                             # list durasi (menit) pasien yang sudah dilayani
nomor_counter = 0                                       # int, penghasil nomor RM berurutan
URUTAN_PRIORITAS = ["darurat", "lansia", "umum"]        # list tetap, urutan pengecekan prioritas
WAKTU_DEFAULT = 5                                       # int, asumsi menit sebelum ada riwayat durasi
```

## Daftar Fungsi Lengkap
Total terdapat **27 fungsi** dalam program ini (termasuk fungsi pendukung/utilitas kecil), disusun mengikuti dekomposisi top-down per modul.

| No | Fungsi | Modul | Struktur Data yang Dipakai | Deskripsi Singkat |
|---|---|---|---|---|
| 1 | `konfirmasi_yes_no(pertanyaan)` | Utilitas | `str` (perbandingan) | Loop input sampai jawaban persis "yes"/"no" |
| 2 | `label_kategori(kategori)` | Utilitas | `dict` (lookup label) | Mengubah kode kategori jadi label tampilan `[DARURAT]` dst. |
| 3 | `pilih_kategori()` | Utilitas | — (if/elif) | Loop menu pilihan kategori manual (1-3) |
| 4 | `validasi_nama(nama)` | Utilitas | `set` (karakter diizinkan), `tuple` (return) | Mengecek nama kosong/mengandung angka/karakter tak umum |
| 5 | `input_nama()` | Utilitas | — | Loop pemanggil `validasi_nama()` + konfirmasi jika perlu |
| 6 | `input_umur()` | Utilitas | — | Loop validasi umur numerik 1-120 |
| 7 | `input_nik()` | Utilitas | `str` (panjang & digit) | Loop validasi NIK 16 digit angka |
| 8 | `input_keluhan()` | Utilitas | — | Loop validasi keluhan tidak kosong |
| 9 | `buat_no_rm()` | Utilitas | `int` (counter global) | Menghasilkan nomor RM baru berurutan (RM001, RM002, ...) |
| 10 | `muat_data_demo()` | Data Demo | `list` of `tuple`, `dict` | Memuat 18 pasien contoh + riwayat + durasi historis |
| 11 | `daftar_kunjungan_ulang(no_rm)` | Pendaftaran | `dict` (update in-place) | Mendaftarkan kunjungan baru pasien lama tanpa ulang biodata |
| 12 | `daftar_pasien()` | Pendaftaran | `dict`, `dict`-of-`list` | Orkestrator: cabang pasien baru vs pasien lama |
| 13 | `tambah_ke_antrian(no_rm, kategori)` | Antrian | `list.append()` | Menambah pasien ke antrian sesuai kategori — O(1) |
| 14 | `panggil_pasien_berikutnya()` | Antrian | `list.pop(0)` | Memanggil pasien sesuai prioritas darurat→lansia→umum |
| 15 | `tampilkan_riwayat_singkat(no_rm)` | Antrian/Pendaftaran | `dict.get()`, `list` | Menampilkan ringkasan diagnosa sebelumnya |
| 16 | `catat_layanan_selesai(no_rm)` | Antrian | `list.append()` | Mencatat durasi layanan + memicu simpan riwayat |
| 17 | `estimasi_waktu_tunggu(no_rm)` | Antrian | `list.index()` | Menghitung estimasi menit dari posisi × rata-rata durasi |
| 18 | `tampilkan_antrian()` | Antrian | `dict`-of-`list`, `enumerate()` | Menampilkan seluruh antrian per kategori |
| 19 | `tambah_riwayat_kunjungan(no_rm, poli, diagnosa)` | Riwayat | `dict`-of-`list`-of-`dict` | Menambah satu entri riwayat kunjungan |
| 20 | `cari_no_rm_pasien_lama()` | Riwayat/Pendaftaran | `list comprehension` (linear search) | Cari pasien lama via RM/NIK/Nama, dengan disambiguasi nama ganda |
| 21 | `tampilkan_riwayat()` | Riwayat | `dict.get()`, `enumerate()` | Menampilkan seluruh riwayat kunjungan satu pasien |
| 22 | `tampilkan_detail_pasien(no_rm, info)` | Riwayat | `dict` (akses field) | Mencetak kartu detail satu pasien |
| 23 | `cari_pasien()` | Riwayat | `list comprehension` (linear search) | Cari pasien aktif via Nomor RM atau Nama |
| 24 | `tampilkan_statistik_harian()` | Laporan | `dict` (counter) | Menghitung jumlah pasien per kategori hari ini |
| 25 | `urutkan_laporan()` | Laporan | `list` of `dict`, `sorted(key=...)` | Mengurutkan laporan berdasarkan 3 kriteria |
| 26 | `tampilkan_menu()` | Menu | — | Mencetak kotak menu utama |
| 27 | `main_menu()` | Menu | `dict` (dispatch aksi) | Loop utama program, memanggil fungsi sesuai pilihan |

## Algoritma & Kompleksitas

| Operasi | Fungsi | Kompleksitas | Catatan |
|---|---|---|---|
| Tambah ke antrian | `tambah_ke_antrian()` | O(1) | `list.append()` |
| Panggil pasien berikutnya | `panggil_pasien_berikutnya()` | O(n) | `list.pop(0)` menggeser elemen list; trade-off yang disadari karena `deque` di luar cakupan silabus |
| Cari pasien by Nomor RM | `cari_pasien()`, `cari_no_rm_pasien_lama()` | O(1) | Akses langsung karena Nomor RM adalah key `dict` |
| Cari pasien by NIK/nama | `cari_pasien()`, `cari_no_rm_pasien_lama()` | O(n) | Linear search karena NIK dan nama bukan key dari `data_pasien` |
| Pengurutan laporan | `urutkan_laporan()` | O(n log n) | Memakai `sorted()` bawaan Python dengan parameter `key` custom |
| Estimasi waktu tunggu | `estimasi_waktu_tunggu()` | O(1) | Dihitung dari posisi (indeks list) × rata-rata durasi layanan historis |

## Konsep Pemrograman yang Diterapkan (Bab 1–13)

| Bab | Konsep | Penerapan di Proyek |
|---|---|---|
| 1 | Algoritma & Computational Thinking | Seluruh fungsi dirancang lewat pseudocode terlebih dahulu sebelum diimplementasikan (lihat proses desain di dokumentasi proyek) |
| 2 | Variabel, Tipe Data, Operator | `str` (nama, NIK), `int` (umur, nomor_counter), `float` (durasi layanan), `bool` (hasil validasi); operator perbandingan (`==`, `<=`, `in`), aritmatika (`posisi * rata_rata`), dan `or` untuk nilai default (`poli = input(...) or "Umum"`) |
| 3 | Seleksi (if/elif/else) | Hampir seluruh fungsi validasi, `pilih_kategori()`, cabang pasien baru vs lama di `daftar_pasien()`, cabang pencarian RM/NIK/Nama di `cari_no_rm_pasien_lama()` |
| 4 | Perulangan (for/while) | `while True` pada semua fungsi `input_*()` dan `konfirmasi_yes_no()` (loop sampai valid); `for` pada `URUTAN_PRIORITAS`, `enumerate()` pada tampilan antrian/riwayat/laporan |
| 5 | Fungsi & Dekomposisi | 27 fungsi modular dengan tanggung jawab tunggal, tersusun dalam 6 modul (Utilitas, Pendaftaran, Antrian, Riwayat, Laporan, Menu) |
| 6 | String & Pengolahan Teks | `.strip()`, `.lower()`, `.upper()`, `.isdigit()` pada seluruh validasi input; f-string formatting dengan alignment (`{:<20}`) untuk tabel |
| 7 | List, Tuple, Operasi Koleksi | `list.append()`/`pop(0)`/`index()` pada antrian; `tuple` unpacking pada `contoh_pasien` di `muat_data_demo()`; list comprehension pada `cari_pasien()` dan `urutkan_laporan()` |
| 8 | Dictionary, Set, Pemilihan Struktur Data | `dict` untuk `data_pasien`/`antrian`/`riwayat_kunjungan`; `set` untuk karakter yang diizinkan pada `validasi_nama()` |
| 9 | Algoritma Pencarian (Searching) | Akses O(1) via key `dict` (Nomor RM) vs. linear search O(n) untuk pencarian by NIK/Nama pada `cari_pasien()` dan `cari_no_rm_pasien_lama()` |
| 10 | Algoritma Pengurutan (Sorting) | `sorted()` dengan parameter `key=lambda` pada `urutkan_laporan()`, mendukung 3 kriteria pengurutan berbeda |
| 11 | Rekursi & Pemecahan Masalah | **Tidak digunakan** — seluruh masalah proyek ini terselesaikan secara iteratif (for/while) dan lewat dekomposisi fungsi (Bab 5); tidak ada sub-masalah yang secara alami membutuhkan rekursi |
| 12 | Kompleksitas Algoritma (Big-O) | Dibahas eksplisit pada bagian [Algoritma & Kompleksitas](#algoritma--kompleksitas) di atas, termasuk trade-off `list.pop(0)` O(n) yang disadari sejak desain |
| 13 | AI-Augmented Programming & Code Quality | Didokumentasikan penuh pada [`AI_Usage_Log_Sistem_Antrian_RS.md`](./AI_Usage_Log_Sistem_Antrian_RS.md), termasuk momen menolak/mengoreksi saran AI |

## Alur Penggunaan Singkat
```
╔══════════════════════════════════════════╗
║          SISTEM ANTRIAN RUMAH SAKIT      ║
╠══════════════════════════════════════════╣
║  1. Daftar Pasien Baru                   ║
║  2. Panggil Pasien Berikutnya            ║
║  3. Lihat Antrian Saat Ini               ║
║  4. Cari Pasien                          ║
║  5. Riwayat Kunjungan Pasien             ║
║  6. Statistik Harian                     ║
║  7. Laporan (Urutkan Data)               ║
║  8. Estimasi Waktu Tunggu                ║
║  0. Keluar                               ║
╚══════════════════════════════════════════╝
```
Contoh skenario: pasien lama mendaftar kunjungan ulang lewat menu 1 dengan
mencari via NIK → sistem menampilkan riwayat penyakit sebelumnya (mis.
"Hipertensi terkontrol") → petugas loket bisa langsung memberi informasi
awal ke pasien sebelum masuk ke antrian.

## Data Demo
Program otomatis memuat 18 data pasien contoh (kombinasi kategori darurat,
lansia, dan umum) beserta beberapa riwayat kunjungan dan riwayat durasi
layanan historis setiap dijalankan, supaya fitur 2–8 bisa langsung
didemokan tanpa perlu registrasi manual satu per satu. Nomor RM pasien baru
akan tetap lanjut dari RM019 dan seterusnya.

## Test Case

Berikut skenario pengujian yang telah diverifikasi berjalan sesuai desain (mengacu pada dataset demo 18 pasien).

| No | Fungsi yang Diuji | Input | Output yang Dihasilkan |
|---|---|---|---|
| 1 | `input_umur()` | `"abc"` | `[!] Umur harus berupa angka.` — loop meminta input ulang |
| 2 | `input_umur()` | `"150"` | `[!] Umur tidak valid (harus 1-120).` — loop meminta input ulang |
| 3 | `input_nik()` | `"12345"` (5 digit) | `[!] NIK harus terdiri dari 16 digit angka.` — loop meminta input ulang |
| 4 | `validasi_nama()` via `input_nama()` | `"Siti123"` lalu konfirmasi `"no"` | `[!] Nama mengandung angka.` — diminta input ulang karena dijawab "no" |
| 5 | `konfirmasi_yes_no()` | `"y"` (bukan "yes") | `[!] Input tidak valid. Ketik 'yes' atau 'no' saja.` — loop bertahan sampai input persis "yes"/"no" |
| 6 | `panggil_pasien_berikutnya()` | Menu `2` saat antrian darurat berisi RM001 & RM007, meski RM008 (umum) terdaftar lebih dulu | RM001 (darurat) dipanggil lebih dulu — membuktikan prioritas mengalahkan urutan waktu daftar |
| 7 | `cari_pasien()` | Menu `4` → pilih `1` (Nomor RM) → `"RM002"` | Menampilkan detail Budi Santoso (kartu info lengkap) |
| 8 | `cari_pasien()` | Menu `4` → pilih `3` (Nama) → `"sari"` | Ditemukan 2 hasil: Ratna Sari (RM004) dan Wulan Sari (RM008) |
| 9 | `estimasi_waktu_tunggu()` | Menu `8` → `"RM008"` (posisi ke-8 di antrian gabungan) | `Estimasi: ± 82 menit` (posisi 8 × rata-rata ±10,25 menit) |
| 10 | `estimasi_waktu_tunggu()` | Menu `8` → Nomor RM pasien yang sudah dipanggil (tidak ada di antrian) | `[!] Pasien tidak sedang dalam antrian (mungkin sudah dipanggil).` |
| 11 | `urutkan_laporan()` | Menu `7` → pilih `2` (Kategori) | Laporan terurut: seluruh pasien **Darurat** tampil dulu, lalu **Lansia**, lalu **Umum** |
| 12 | `cari_no_rm_pasien_lama()` via `daftar_pasien()` | Menu `1` → `"yes"` → cari via NIK `"9999999999999999"` (tidak terdaftar) | `[!] Nomor RM tidak ditemukan.` lalu ditawarkan `Lanjutkan sebagai pendaftaran pasien BARU? (yes/no):` |
| 13 | `daftar_kunjungan_ulang()` | Menu `1` → `"yes"` → NIK `"3201234567890002"` (Budi Santoso) | `[Ditemukan] Budi Santoso, 67 tahun (NIK: 3201234567890002).` + riwayat penyakit sebelumnya tampil otomatis, tanpa perlu input ulang nama/umur/NIK |

## Keterbatasan & Pengembangan Selanjutnya
- **Tidak ada penyimpanan permanen (persistence)** — seluruh data hanya
  tersimpan di memori (RAM) selama program berjalan dan akan hilang saat
  program ditutup/di-restart. Sempat diimplementasikan menggunakan file
  JSON, namun dihapus kembali karena dianggap di luar cakupan materi
  (Bab 1–13) dan berisiko sulit dijelaskan saat presentasi.
- **`list.pop(0)` pada antrian** bersifat O(n), bukan O(1). Struktur
  `collections.deque` yang lebih efisien tidak dipakai karena belum
  diajarkan dalam silabus.
- **Kategori pasien ditentukan manual** oleh petugas (bukan otomatis dari
  teks keluhan), karena keputusan medis tidak seharusnya ditebak oleh
  sistem berbasis pencocokan kata kunci semata.

## Dokumentasi Tambahan
Riwayat lengkap proses pengembangan dan transparansi penggunaan AI selama
proyek ini tersedia di [`AI_Usage_Log_Sistem_Antrian_RS.md`](./AI_Usage_Log_Sistem_Antrian_RS.md).
