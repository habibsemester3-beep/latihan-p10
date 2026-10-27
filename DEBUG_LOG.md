# LOG DEBUGGING - LATIHAN P10

## BUG G1: Logo di Navbar Tidak Tampil
*   **Gejala (Reproduksi):** Saat halaman dibuka, logo di kiri atas (sebelah tulisan "Katalog Karya") tidak muncul, hanya terlihat ikon gambar rusak.
*   **Hipotesis:** Jalur (path) ke file gambar salah di kode HTML.
*   **Bukti Alat (DevTools):** Tab *Network* menunjukkan status `404 Not Found` untuk pencarian file `img/logo.svg`.
*   **Penyebab & Perbaikan (index.html, Baris 11):** Atribut `src` pada tag `<img>` menunjuk ke folder `img/`, sedangkan folder aslinya bernama `assets/`. Diperbaiki dengan mengubah `src="img/logo.svg"` menjadi `src="assets/logo.svg"`.
*   **Uji Ulang:** Logo berhasil tampil dan tidak ada lagi pesan 404 di tab *Network*.

## BUG G2: Kartu Tersusun Satu Kolom Memanjang
*   **Gejala (Reproduksi):** Saat dibuka di layar lebar (laptop), kartu-karya tidak berjejer menyamping, melainkan menumpuk satu kolom ke bawah.
*   **Hipotesis:** Sistem grid (Grid System) Bootstrap tidak diimplementasikan dengan benar pada elemen pembungkus (container) galeri.
*   **Bukti Alat (DevTools):** Melalui tab *Elements*, terlihat elemen pembungkus `<div id="galeri" class="g-3">` menampung elemen dengan class `col-*`, tetapi pembungkusnya sendiri kehilangan class `row`.
*   **Penyebab & Perbaikan (index.html, Baris 34):** Elemen `#galeri` kekurangan class `row` yang diwajibkan oleh Bootstrap. Diperbaiki dengan mengubah `class="g-3"` menjadi `class="row g-3"`.
*   **Uji Ulang:** Kartu sekarang berjejer sejajar (3 kolom di layar lebar) dengan rapi.

## BUG G3: Filter Kategori Tidak Berfungsi (TypeError)
*   **Gejala (Reproduksi):** Memilih opsi (misal: "Video") pada dropdown filter kategori tidak memfilter daftar karya di layar.
*   **Hipotesis:** Ada kesalahan logika JavaScript (kesalahan pemilihan elemen DOM) sehingga aksi perubahan tidak terdeteksi.
*   **Bukti Alat (DevTools):** Melalui tab *Console*, terdapat pesan error berwarna merah `Uncaught TypeError: Cannot read properties of null (reading 'addEventListener')` yang menunjuk pada file `main.js`.
*   **Penyebab & Perbaikan (main.js, Baris 47):** JavaScript mencoba mengambil elemen dengan `querySelector('#filter-kategori')` (menggunakan tanda hubung), padahal ID asli di HTML adalah `filterKategori` (camelCase). Diperbaiki dengan langsung menggunakan variabel `filterKategori` yang sudah dideklarasikan di baris atas.
*   **Uji Ulang:** Daftar karya berhasil tersaring sesuai pilihan kategori, dan tidak ada lagi error di tab Console.

## BUG G4: Teks Jumlah Karya Tidak Dinamis Saat Digeledah (Search)
*   **Gejala (Reproduksi):** Saat menggunakan kotak pencarian (misal mencari "Video"), daftar karya tersaring, tetapi teks penghitung di atasnya tetap menampilkan "Menampilkan 6 dari 6 karya".
*   **Hipotesis:** Variabel JavaScript yang bertugas merender teks jumlah mengambil panjang dari data master, bukan panjang array hasil filter.
*   **Bukti Alat (DevTools):** Melalui tab *Elements*, terlihat bahwa inner text pada `<p id="jumlah">` memang tidak pernah ter-update secara dinamis (selalu mengembalikan angka statis 6).
*   **Penyebab & Perbaikan (main.js, Baris 32):** Kode menggunakan referensi `KARYA.length` (data mentah statis) untuk jumlah yang ditampilkan. Diperbaiki dengan mengubah referensi pertama menjadi panjang dari array hasil saringan: diubah menjadi `hasil.length`.
*   **Uji Ulang:** Saat mencari atau memfilter, teks jumlah sekarang berubah dinamis mengikuti sisa kartu yang tampil.

## BUG G5: Tombol Detail Mati Setelah Fungsi Filter/Search Dijalankan
*   **Gejala (Reproduksi):** Tombol "Detail" berfungsi saat pertama kali muat, tetapi menjadi tidak responsif (mati) setelah kita mencari atau memfilter kategori.
*   **Hipotesis:** Elemen DOM hasil filter dibuat ulang (re-render) sehingga kehilangan binding event listener "click" lamanya.
*   **Bukti Alat (DevTools):** Melalui tab *Elements* > *Event Listeners*, tombol "Detail" yang muncul setelah pencarian tidak memiliki event "click", berbeda dengan tombol saat inisialisasi awal.
*   **Penyebab & Perbaikan (main.js, fungsi tampil):** Fungsi `pasangDetail()` hanya dipanggil secara statis di luar, sehingga tombol yang baru dirender oleh fungsi `tampil()` tidak kebagian event listener. Diperbaiki dengan memanggil `pasangDetail();` di dalam akhir baris fungsi `tampil()`.
*   **Uji Ulang:** Tombol Detail tetap bisa diklik secara normal meskipun daftar karya sudah difilter berkali-kali.

## BUG G6: Pencarian Case-Sensitive (Tidak Merespons Huruf Kecil)
*   **Gejala (Reproduksi):** Memasukkan kata kunci dengan huruf kecil (contoh: "animasi") tidak memunculkan judul yang berhuruf kapital ("Animasi").
*   **Hipotesis:** Metode validasi teks mencari kecocokan persis (case-sensitive) tanpa normalisasi karakter.
*   **Bukti Alat (DevTools):** Melalui *Sources* (menambahkan breakpoint pada fungsi `saring`), terlihat bahwa string "Animasi".includes("animasi") me-return nilai false.
*   **Penyebab & Perbaikan (main.js, Baris fungsi saring):** Perbandingan string tidak dinormalisasi. Diperbaiki dengan merantai string menggunakan method `.toLowerCase()` pada kata kunci (`kata.toLowerCase()`) dan pada judul asli (`k.judul.toLowerCase()`) sebelum `includes`.
*   **Uji Ulang:** Pencarian berhasil menangkap judul secara dinamis tanpa peduli penulisan huruf besar atau kecil (case-insensitive).