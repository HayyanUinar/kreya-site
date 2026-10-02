# Website KREYA

Situs statis satu halaman untuk PT Teknologi Kreatif Berkarya. Tidak memerlukan Node.js, framework, atau build command. Upload seluruh isi folder ini ke document root hosting, atau gunakan folder ini sebagai root project pada static hosting.

## Berkas deploy

- `index.html`: halaman, CSS, JavaScript, dan metadata sosial.
- `robots.txt` dan `sitemap.xml`: instruksi crawler dan sitemap untuk `www.kreyasolution.com`.
- `.htaccess`: header keamanan, caching, kompresi, dan directory index untuk Apache/cPanel.
- `_headers`: header keamanan dan caching untuk Netlify atau Cloudflare Pages.
- `favicon.ico`, `favicon.png`, dan `assets/`: ikon, preview sosial, logo, serta gambar portofolio.

Gambar portofolio tersedia dalam WebP dan JPG. Pertahankan keduanya: elemen `<picture>` memilih WebP dan menyediakan JPG sebagai fallback.

## Sebelum go-live

1. Pastikan domain yang akan dipakai adalah `www.kreyasolution.com`. Jika berbeda, ubah URL di `canonical`, `og:url`, `og:image`, `robots.txt`, dan `sitemap.xml` agar semuanya memakai domain HTTPS final.
2. Verifikasi email, nomor telepon, alamat kantor, dan klaim kemitraan di halaman.
3. Halaman kontak memakai tautan `mailto:` dan `tel:`; belum ada formulir yang mengirim data ke server. Pastikan alamat email berfungsi dan siapkan kotak surat untuk domain bila diperlukan.
4. Pastikan hak penggunaan gambar portofolio, logo, dan gambar preview sudah disetujui.
5. Upload semua isi folder, termasuk berkas tersembunyi `.htaccess` jika menggunakan cPanel. Jangan upload folder induknya sebagai satu tingkat tambahan kecuali itu memang document root yang diinginkan.

## Pilihan hosting

### cPanel atau Apache

Upload seluruh isi folder ke `public_html/` melalui File Manager atau SFTP. Aktifkan tampilan hidden files agar `.htaccess` ikut terunggah. Jika hosting menolak directive di `.htaccess` dan menampilkan HTTP 500, hapus directive yang ditolak atau minta hosting mengaktifkan modul yang diperlukan (`mod_headers` dan `mod_deflate`); konten halaman tidak bergantung pada kedua modul itu.

### Netlify atau Cloudflare Pages

Hubungkan repository atau unggah folder situs sebagai static site. Gunakan root folder sebagai publish/output directory dan biarkan build command kosong. Berkas `_headers` akan mengatur header keamanan dan cache. Pastikan dashboard provider tidak mengganti aturan tersebut dengan header yang bertentangan.

### Vercel

Impor folder/repository sebagai situs statis tanpa framework dan tanpa build command. Gunakan root project sebagai lokasi file. Berkas `.htaccess` dan `_headers` tidak diterapkan oleh Vercel; jika perlu header yang sama di Vercel, tambahkan aturan melalui konfigurasi project Vercel.

### GitHub Pages

Push file situs ke repository, lalu buka **Settings > Pages** dan pilih branch serta folder root yang berisi `index.html`. GitHub Pages tidak membaca `.htaccess` atau `_headers`, sehingga header keamanan dan caching perlu diatur melalui layanan di depannya bila diperlukan.

### VPS dengan Nginx

Salin isi folder ke document root yang sudah dikonfigurasi, misalnya `/var/www/kreyasolution.com/`. `.htaccess` tidak berlaku di Nginx. Berikut contoh blok server untuk HTTP; pasang sertifikat TLS dan konfigurasi redirect HTTPS menggunakan panduan provider/administrator server sebelum mengalihkan trafik publik:

```nginx
server {
    listen 80;
    server_name www.kreyasolution.com;
    root /var/www/kreya-site;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location = /index.html {
        expires -1;
    }

    location ~* \.(webp|jpg|jpeg|png|ico)$ {
        expires 7d;
    }

    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header X-Frame-Options "DENY" always;
    add_header Permissions-Policy "camera=(), microphone=(), geolocation=()" always;
}
```

Contoh unggah dari PowerShell dengan OpenSSH `scp` (jalankan dari folder induk situs; ganti user dan server). Salinan direktori ikut membawa berkas tersembunyi seperti `.htaccess`:

```powershell
scp -r .\kreya-site user@server:/var/www/
```

Perintah itu tidak menghapus file lain di server. Jangan gunakan sinkronisasi dengan opsi penghapusan (`--delete`) kecuali document root memang khusus untuk situs ini dan file di dalamnya sudah dicadangkan.

## Domain dan HTTPS

1. Di panel DNS, arahkan `www` ke target yang diberikan provider hosting (umumnya CNAME). Arahkan domain akar `kreyasolution.com` sesuai instruksi provider, lalu atur redirect permanen ke `https://www.kreyasolution.com/` agar sesuai dengan canonical.
2. Tambahkan domain di dashboard hosting dan terbitkan/aktifkan sertifikat TLS untuk domain yang dipakai. Tunggu propagasi DNS dan status sertifikat aktif.
3. Aktifkan redirect HTTP ke HTTPS dan domain akar ke `www` pada provider. Hindari memasang beberapa aturan redirect yang saling bertentangan.
4. Jangan aktifkan HSTS sampai HTTPS berfungsi konsisten untuk semua host yang akan digunakan.

Alamat record DNS berbeda antar-provider; gunakan nilai persis dari panel hosting, bukan IP contoh dari panduan umum.

## Pemeriksaan setelah deploy

- Buka `https://www.kreyasolution.com/` dan pastikan HTTPS valid tanpa mixed content.
- Uji `https://www.kreyasolution.com/robots.txt` dan `https://www.kreyasolution.com/sitemap.xml`.
- Periksa gambar hero, gambar carousel, favicon, tampilan ponsel, navigasi, dan tombol carousel.
- Uji tautan email/telepon pada perangkat yang memiliki aplikasi terkait.
- Periksa preview sosial menggunakan URL gambar `https://www.kreyasolution.com/assets/social-preview.jpg`.
- Verifikasi redirect domain akar dan HTTP menuju satu URL kanonis HTTPS.

## Mengedit konten

Semua teks ada di `index.html` dan ditandai dengan `id` per section: `#tentang`, `#layanan`, `#portofolio`, `#kontak`. Tidak ada CMS, jadi edit langsung di HTML.

Menambah proyek baru ke carousel: salin satu blok `<article class="slide">`, ganti isinya, taruh gambar baru di `assets/`. Titik navigasi di bawah carousel dibuat otomatis oleh JavaScript sesuai jumlah slide, tidak perlu diubah manual. Perbarui juga atribut `aria-label` tiap slide supaya nomornya benar.

Mengganti warna: semua warna didefinisikan sebagai CSS variable di blok `:root` paling atas. Ada dua blok tambahan untuk mode gelap. Ubah `--accent` dan `--accent-2` kalau warna brand berubah.

## Font

Satu-satunya permintaan ke luar server adalah Google Fonts (Inter). Di perangkat Apple font sistem SF Pro dipakai lebih dulu, jadi Inter hanya termuat di Windows dan Android. Halaman tetap tampil normal kalau Google Fonts diblokir, hanya bentuk hurufnya berbeda.

Kalau Anda ingin nol dependensi eksternal, unduh Inter dari rsms.me/inter, taruh file woff2 di `assets/fonts/`, hapus dua tag `<link>` ke fonts.googleapis.com, lalu tambahkan `@font-face` sendiri di CSS.

## Catatan teknis

Interaksi geser pada carousel memakai Pointer Events dengan animasi spring, proyeksi momentum, dan resistensi bertahap di ujung. Semuanya JavaScript biasa tanpa library. Setelan "kurangi gerak" di sistem operasi pembaca dihormati secara otomatis: pantulan dimatikan dan animasi hero dilewati.

Halaman mengikuti mode terang atau gelap perangkat pembaca. Belum ada tombol manual untuk berpindah tema.

Diuji di Chromium pada lebar 390px dan 1440px.
