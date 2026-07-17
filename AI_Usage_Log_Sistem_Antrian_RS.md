# AI Usage Log — Proyek Akhir

## Identitas
- Kelompok: Sistem Antrian Rumah Sakit
- Nama Anggota: Kelompok 5
  1. Ivanka Widyaningrum (0112525005)
  2. Lolita Disry Mustika (0112525006)
  3. Muhammad Fajri Febriyanto (0112525008)
  4. Sahid Haqqi An Najil (0112525013)
- Proyek: Sistem Antrian Rumah Sakit
- Tanggal mulai: 13 Juni 2026
- Tanggal selesai: 17 Juli 2026

## Ringkasan
- AI yang digunakan: Claude
- Persentase kode AI: ~45%
- Persentase kode sendiri: ~55%
- Jumlah interaksi: 10 prompt

## Detail Interaksi

### Interaksi 1 (Minggu 12)
- **Prompt:** "Bantu saya brainstorm 10 fitur yang sangat penting untuk sistem antrian rumah sakit, lalu rancang struktur data untuk 5 sub-masalah yang sudah saya identifikasi (pendaftaran, antrian FIFO, prioritas darurat/lansia, estimasi waktu tunggu, riwayat kunjungan)."
- **Respons AI:** Memberi 10 opsi fitur dan menyarankan struktur `dict` untuk data pasien, `deque` untuk antrian FIFO, dan kombinasi `dict` + list untuk prioritas.
- **Yang digunakan:** Struktur `dict` untuk data pasien (akses by nomor RM), dan pendekatan multi-level queue per kategori prioritas.
- **Modifikasi:** Mengganti `deque` menjadi `list` biasa karena `deque` belum diajarkan di silabus (Bab 1-13); memilih hanya memakai struktur data yang sudah dipelajari.
- **Pelajaran:** Struktur data yang "lebih efisien secara teori" belum tentu boleh dipakai kalau di luar cakupan materi — harus disesuaikan dengan apa yang benar-benar sudah dikuasai kelompok.

### Interaksi 2 (Minggu 13)
- **Prompt:** "Lanjut ke requirements analysis (FR/NFR) dan desain top-down."
- **Respons AI:** Membuat 10 Functional Requirements, 5 Non-Functional Requirements, dan diagram dekomposisi 4 modul (Pendaftaran, Antrian, Riwayat, Laporan).
- **Yang digunakan:** Seluruh daftar FR/NFR dan struktur modul dipakai sebagai dasar pembagian fungsi.
- **Modifikasi:** Tidak ada — kelompok memvalidasi bahwa setiap FR bisa ditelusuri ke fitur yang sudah disepakati.
- **Pelajaran:** Requirement yang jelas (FR-2, FR-4 soal prioritas) menentukan test case yang wajib diuji nanti — requirement dan pengujian saling terhubung sejak awal desain.

### Interaksi 3 (Minggu 14)
- **Prompt:** "Tentukan pemilihan struktur data dari fungsi per modul yang sudah dibuat."
- **Respons AI:** Memetakan struktur data (dict/list) untuk setiap fungsi, lengkap dengan alasan kompleksitas (mis. akses by-RM O(1) vs pencarian by-nama O(n)).
- **Yang digunakan:** Seluruh pemetaan struktur data per fungsi dipakai sebagai acuan implementasi.
- **Modifikasi:** Tidak ada perubahan struktur, hanya dikonfirmasi ulang konsistensinya dengan keputusan di Interaksi 1 (tanpa `deque`).
- **Pelajaran:** Struktur data yang sama (`dict`/`list`) bisa dipakai dengan cara berbeda (dict of list, list of dict) tergantung kebutuhan akses datanya.

### Interaksi 4 (Minggu 14)
- **Prompt:** "Tulis sketsa algoritma/pseudocode untuk fungsi-fungsi inti."
- **Respons AI:** Pseudocode untuk 9 fungsi inti, termasuk logika prioritas antrian dan estimasi waktu tunggu.
- **Yang digunakan:** Sebagian besar alur logika, terutama urutan pengecekan kategori prioritas (darurat → lansia → umum).
- **Modifikasi:** Fungsi `tentukan_kategori` (awalnya keyword-matching otomatis dari teks keluhan) diubah total menjadi pilihan manual dari menu, karena keputusan medis tidak boleh ditebak dari teks bebas oleh sistem.
- **Pelajaran:** AI cenderung mengotomatisasi keputusan yang sebenarnya harus tetap di tangan manusia (petugas) — perlu dikoreksi secara sadar, bukan diterima mentah-mentah.

### Interaksi 5 (Minggu 15)
- **Prompt:** "Tambahkan pengecualian validasi nama (anti-angka/karakter aneh) dan konfirmasi 'apakah data sudah sesuai? (yes/no)' yang hanya menerima jawaban yes/no."
- **Respons AI:** Menambahkan fungsi `validasi_nama()` (3 status: gagal/peringatan/valid) dan `konfirmasi_yes_no()` dengan loop input ketat.
- **Yang digunakan:** Seluruh logika loop validasi dipakai.
- **Modifikasi:** Tidak ada modifikasi besar pada logika ini — sesuai kebutuhan sejak awal.
- **Pelajaran:** AI awalnya membuat validasi biner (benar/salah) untuk nama, padahal nama dengan angka/karakter tak umum butuh jalur ketiga (konfirmasi), bukan langsung ditolak.

### Interaksi 6 (Minggu 15)
- **Prompt:** "Sekarang kita bisa lanjut ke implementasi kode Python, sebelumnya saya pernah membuat proyek Puskesmas — apakah kita akan memakainya?"
- **Respons AI:** Menganalisis kode lama (struktur `list` + `insert()` berbasis prioritas) vs rancangan baru (`dict` of `list`), merekomendasikan membangun ulang struktur data tapi mengadopsi gaya penulisan kode lama.
- **Yang digunakan:** Gaya docstring, format tabel f-string, dan pola dispatch menu dari kode lama; seluruh isi 26 fungsi hasil implementasi AI.
- **Modifikasi:** Struktur antrian tetap dirombak total (bukan pakai `insert()` berbasis prioritas dari kode lama).
- **Pelajaran:** Kode lama yang "sudah jalan" belum tentu strukturnya paling tepat untuk kebutuhan baru yang lebih kompleks (3 kategori prioritas, bukan 2).

### Interaksi 7 (Minggu 16)
- **Prompt:** "Tambahkan data demo 15-20 pasien untuk menguji fitur 2-8, dan bagaimana agar data pasien bisa tersimpan permanen antar sesi?"
- **Respons AI:** Membuat 18 data pasien contoh, serta mengimplementasikan fitur penyimpanan ke file JSON (`simpan_data()`/`muat_data()` dengan try-except).
- **Yang digunakan:** Data demo 18 pasien dipakai penuh untuk pengujian fitur 2-8.
- **Modifikasi:** Fitur JSON persistence **dihapus kembali** pada interaksi berikutnya (lihat Interaksi 8) atas keputusan kelompok sendiri.
- **Pelajaran:** Menambahkan fitur teknis yang canggih tidak selalu berarti lebih baik untuk dikumpulkan — kalau tidak bisa dijelaskan dengan percaya diri saat presentasi, risikonya lebih besar daripada manfaat nilai tambahnya.

### Interaksi 8 (Minggu 16)
- **Prompt:** "Saya akan menghapus fitur JSON karena khawatir tidak bisa menjelaskan saat demo, bagaimana menurutmu?"
- **Respons AI:** Menyetujui alasan tersebut (fitur ini opsional/bonus, di luar cakupan Bab 1-13) dan membantu menghapus seluruh kode terkait `json`/`os`, `simpan_data()`, dan `muat_data()` secara bersih.
- **Yang digunakan:** Proses penghapusan kode dilakukan oleh AI atas instruksi eksplisit kelompok.
- **Modifikasi:** Keputusan untuk menghapus murni datang dari kelompok, bukan saran AI — AI hanya mengeksekusi dan memverifikasi tidak ada sisa kode yang rusak.
- **Pelajaran:** Keputusan **tidak** memakai suatu fitur AI juga merupakan bagian penting dari tanggung jawab akademik — kelompok harus berani mengevaluasi kembali saran AI berdasarkan kesiapan sendiri untuk mempertanggungjawabkannya.

### Interaksi 9 (Minggu 16)
- **Prompt:** "Bukankah mencari riwayat pasien lewat No. RM saja menyulitkan karena sulit diingat? Bagaimana jika ditambah opsi cari lewat NIK atau nama juga?"
- **Respons AI:** Menambahkan fungsi pencarian dengan 3 opsi (RM/NIK/Nama) untuk fitur riwayat kunjungan, termasuk penanganan kasus nama yang cocok lebih dari satu pasien (disambiguasi).
- **Yang digunakan:** Seluruh logika pencarian 3 opsi dan penanganan hasil ganda.
- **Modifikasi:** Tidak ada modifikasi besar — langsung sesuai kebutuhan yang diajukan.
- **Pelajaran:** Nama tidak unik (dua pasien berbeda bisa punya nama sama) sehingga pencarian by-nama wajib punya mekanisme konfirmasi lanjutan, tidak bisa langsung dianggap satu hasil.

### Interaksi 10 (Minggu 16)
- **Prompt:** "Bagaimana untuk pasien yang sudah pernah berobat agar tidak perlu mendaftar ulang? Tambahkan juga pencarian dengan NIK dan tampilkan riwayat penyakit sebelumnya saat pendaftaran ulang, supaya memudahkan diagnosa awal."
- **Respons AI:** Menambahkan fungsi `cari_no_rm_pasien_lama()` (pencarian via RM/NIK/Nama, dengan disambiguasi jika nama sama), `daftar_kunjungan_ulang()`, dan `tampilkan_riwayat_singkat()`.
- **Yang digunakan:** Seluruh logika pencarian dan alur kunjungan ulang.
- **Modifikasi:** Titik pemanggilan `tampilkan_riwayat_singkat()` awalnya diletakkan AI di fitur "panggil pasien" (fitur 2), lalu dipindahkan ke fitur "pendaftaran" (fitur 1) atas koreksi kelompok, karena riwayat penyakit lebih berguna saat skrining awal di loket, bukan saat pasien sudah dipanggil masuk.
- **Pelajaran:** AI bisa menaruh fitur di tempat yang secara teknis benar tetapi kurang tepat secara alur kerja nyata (real user flow) — pemahaman terhadap skenario penggunaan sesungguhnya (bukan hanya requirement teknis) tetap harus datang dari kelompok.

## Refleksi
AI (Claude) banyak membantu mempercepat proses desain formal (problem statement, FR/NFR, diagram top-down, pseudocode) dan penulisan kode boilerplate seperti validasi input, format tabel, dan struktur menu. Namun, keputusan-keputusan penting tetap dipegang kelompok: menolak struktur data di luar silabus (`deque`), mengoreksi otomatisasi keputusan medis (`tentukan_kategori`), menghapus fitur bonus yang berisiko tidak bisa dijelaskan (JSON persistence), serta memperbaiki penempatan fitur riwayat penyakit agar sesuai alur kerja nyata petugas loket. Kelompok memahami dan mampu menjelaskan seluruh 26 fungsi yang dikumpulkan, termasuk alasan setiap pemilihan struktur data dan kompleksitas algoritmanya.
