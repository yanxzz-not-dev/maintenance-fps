# ZUAX FPS — GitHub Maintenance

File ini menjadi dokumentasi untuk sistem **maintenance ZUAX FPS v1.0.0** yang menggunakan `maintenance.json` di repository GitHub sebagai sumber konfigurasi.

## 1. Struktur Repository

Letakkan file maintenance di repository GitHub yang digunakan panel.

Contoh:

```text
repository/
├── maintenance.json
└── README.md
```

Nama file yang direkomendasikan:

```text
maintenance.json
```

## 2. Sumber Data

Panel/bot membaca konfigurasi maintenance melalui **HTTPS GitHub Raw**.

Contoh pola URL:

```text
https://raw.githubusercontent.com/USERNAME/REPOSITORY/BRANCH/maintenance.json
```

Ganti:

- `USERNAME` dengan username/organisasi GitHub.
- `REPOSITORY` dengan nama repository.
- `BRANCH` dengan branch yang digunakan, biasanya `main`.

Contoh:

```text
https://raw.githubusercontent.com/zuax/zuax-fps/main/maintenance.json
```

URL sebenarnya harus disesuaikan dengan repository milik Anda.

## 3. Local maintenance.json

File `maintenance.json` lokal pada server/panel **tidak wajib** apabila source utama sudah menggunakan GitHub Raw.

Arsitektur yang direkomendasikan:

```text
ZUAX PANEL
   ↓
HTTPS
   ↓
GitHub Raw
   ↓
maintenance.json
   ↓
Validasi Schema v1
   ↓
Cache/Fallback
   ↓
Status Maintenance
```

GitHub menjadi **source of truth**.

## 4. Schema

Dokumentasi ini hanya menggunakan:

```json
"schema_version": 1
```

Jangan menggunakan schema `2` untuk konfigurasi v1.0.0.

Contoh awal:

```json
{
  "schema_version": 1,
  "app": {
    "id": "zuax-fps",
    "name": "ZUAX FPS",
    "version": "1.0.0",
    "environment": "production"
  }
}
```

## 5. Fungsi Utama

Maintenance dapat digunakan untuk:

- Mengaktifkan maintenance.
- Menonaktifkan maintenance.
- Maintenance semua akun.
- Maintenance semua akun kecuali owner.
- Maintenance satu akun.
- Maintenance beberapa akun.
- Maintenance semua akun kecuali akun tertentu.
- Mengubah judul maintenance.
- Mengubah pesan maintenance.
- Mengubah alasan maintenance.
- Mengubah detail/informasi maintenance.
- Mengatur progress.
- Mengatur estimasi selesai.
- Mengaktifkan countdown.
- Mengatur jadwal.
- Mengaktifkan emergency mode.
- Memblokir login saat maintenance.
- Memblokir fitur tertentu.
- Mengatur akun/role yang tetap diperbolehkan.
- Mengatur daftar Telegram ID dan username target.
- Mengatur daftar pengecualian.
- Mengatur tampilan maintenance pada aplikasi.
- Mengatur kontak informasi maintenance.
- Mengatur kebijakan versi dan offline.

## 6. Target Maintenance

Target maintenance dapat menggunakan beberapa mode.

### Semua

Semua pengguna yang memenuhi aturan maintenance terkena maintenance.

### Semua kecuali owner

Semua pengguna terkena maintenance, tetapi owner selalu dikecualikan.

**Owner lock harus tetap aktif**, sehingga owner tidak ikut terkena maintenance meskipun mode target berubah menjadi semua.

### Satu akun

Hanya satu akun yang dipilih.

Jika memilih akun lain setelah sebelumnya ada pilihan, pilihan lama diganti.

Contoh:

```text
Pilih A
↓
Pilih B
↓
Target akhir = B
```

Tidak boleh menjadi:

```text
A + B
```

### Beberapa akun

Dapat memilih satu atau banyak akun.

Contoh:

```text
A
B
C
D
```

Jumlah minimum bukan 2. Memilih tepat 1 akun tetap valid.

### Kecuali akun tertentu

Semua akun dapat terkena maintenance kecuali akun yang dimasukkan ke daftar pengecualian.

## 7. Pagination Pemilihan Akun

Untuk daftar akun yang panjang, akun dapat dibagi menjadi beberapa halaman.

Rekomendasi:

```text
6 akun / halaman
```

Alur:

```text
Maintenance
   ↓
Pilih Target
   ↓
Pilih 1 Akun
atau
Pilih Beberapa Akun
   ↓
Daftar akun
   ↓
⬅️ Kembali | ➡️ Lanjut
✅ KONFIRMASI
```

Pada mode satu akun:

- Hanya satu pilihan aktif.
- Memilih akun baru mengganti pilihan sebelumnya.

Pada mode beberapa akun:

- Pilihan dapat ditambah/dikurangi.
- Pilihan tetap tersimpan saat berpindah halaman.

`⬅️ Kembali` pada halaman berikutnya kembali ke halaman sebelumnya.

`⬅️ Kembali` pada halaman pertama kembali ke menu pemilihan mode.

`✅ KONFIRMASI` membuka halaman review.

## 8. Review Sebelum Simpan

Perubahan target tidak langsung ditulis ke GitHub.

Bot harus menampilkan review terlebih dahulu.

Contoh informasi:

```text
Mode       : Beberapa akun
Jumlah     : 3
Target     :
- akun1
- akun2
- akun3

Owner      : Dikecualikan
Status     : Siap disimpan
```

Tombol:

```text
✅ YA, SIMPAN
✏️ PILIH LAGI
❌ BATAL
```

Hanya `✅ YA, SIMPAN` yang boleh mengubah `maintenance.json`.

## 9. Perubahan Pesan Maintenance

Perubahan teks maintenance menggunakan sistem draft.

Alur:

```text
Maintenance
   ↓
Edit Pesan
   ↓
Bot meminta pesan baru
   ↓
Owner mengirim pesan
   ↓
Validasi
   ↓
Draft
   ↓
Review
   ↓
Simpan
```

Pesan yang baru dikirim **jangan langsung ditulis** tanpa konfirmasi.

Contoh review:

```text
Judul:
ZUAX FPS

Pesan baru:
APK SEDANG MAINTENANCE

Alasan:
Perbaikan sistem

Status:
Draft
```

Lalu:

```text
✅ SIMPAN
✏️ EDIT LAGI
❌ BATAL
```

## 10. Validasi

Sebelum perubahan dikirim ke GitHub, konfigurasi harus divalidasi.

Minimal:

- JSON valid.
- `schema_version` valid.
- Tipe data sesuai schema.
- Array tetap berupa array.
- Boolean tetap boolean.
- Nilai target valid.
- Telegram ID valid.
- Username tidak kosong.
- Jadwal mempunyai format waktu yang valid.
- Tidak ada struktur wajib yang hilang.
- Owner tetap dikecualikan.

Jika validasi gagal, konfigurasi lama tidak boleh ditimpa.

## 11. Safe Save

Sistem penyimpanan menggunakan pola:

```text
Config lama
   ↓
Buat draft perubahan
   ↓
Validasi
   ↓
Review
   ↓
Simpan ke GitHub
   ↓
Ambil ulang file
   ↓
Validasi ulang
   ↓
Reload config
```

Jika proses GitHub gagal:

```text
Config lama tetap digunakan
```

Jangan menganggap perubahan berhasil apabila GitHub belum menerima perubahan.

## 12. Cache dan Fallback

Panel dapat menggunakan cache konfigurasi terakhir yang valid.

Alur saat GitHub dapat diakses:

```text
GitHub
 ↓
maintenance.json valid
 ↓
Cache diperbarui
 ↓
Config digunakan
```

Jika GitHub gagal sementara:

```text
GitHub gagal
 ↓
Gunakan cache terakhir yang valid
```

Jika GitHub mengembalikan JSON invalid, konfigurasi invalid tersebut tidak boleh menggantikan cache valid.

## 13. Auto Reload

Setelah `maintenance.json` berubah:

```text
GitHub Save
   ↓
Fetch ulang
   ↓
Validate
   ↓
Reload
   ↓
Maintenance aktif menggunakan config baru
```

Panel tidak perlu restart hanya untuk membaca perubahan maintenance apabila sistem reload sudah aktif.

## 14. Owner Lock

Owner harus selalu dikecualikan dari maintenance.

Aturan:

```text
owner
→ allow
→ tidak terkena maintenance
```

Aturan ini tetap berlaku pada:

```text
ALL
ALL EXCEPT OWNER
SELECTED
EXCEPT SELECTED
```

Owner lock tidak boleh bergantung hanya pada daftar akun yang ditampilkan pada halaman pemilihan.

## 15. Status Detail

Dashboard maintenance sebaiknya menampilkan informasi seperti:

```text
Status      : ON/OFF
Target      : ALL / SELECTED / EXCEPT SELECTED
Jumlah      : jumlah target
Owner       : EXCLUDED
Message     : pesan aktif
Source      : GitHub
Schema      : v1
Config      : VALID/INVALID
```

## 16. Schedule

Jika jadwal digunakan, gunakan format waktu yang jelas dan konsisten.

Contoh:

```json
"schedule": {
  "enabled": true,
  "start": "2026-09-23T22:00:00+07:00",
  "end": "2026-09-24T01:00:00+07:00"
}
```

Gunakan timezone yang eksplisit agar panel dan aplikasi tidak salah membaca waktu.

## 17. Countdown

Countdown dapat digunakan apabila:

```json
"show_countdown": true
```

Countdown sebaiknya hanya ditampilkan apabila `estimated_end` tersedia dan valid.

Contoh:

```json
"estimated_end": "2026-09-24T01:00:00+07:00"
```

## 18. Emergency Mode

Emergency mode dapat digunakan untuk kondisi yang membutuhkan maintenance segera.

Contoh:

```json
"emergency": true
```

Gunakan nilai boolean:

```text
true
false
```

## 19. Block Login

Apabila login akun harus dihentikan selama maintenance:

```json
"block_login": true
```

Jika login tetap diperbolehkan:

```json
"block_login": false
```

## 20. Block Features

Fitur tertentu dapat diblokir melalui daftar feature.

Contoh:

```json
"block_features": [
  "create_account",
  "server",
  "tools"
]
```

Nama feature harus mengikuti nama feature yang benar-benar dikenali oleh panel/APK.

Jangan menambahkan nama feature sembarangan karena panel mungkin tidak mengenalinya.

## 21. Scope

Scope dapat menyimpan target dan pengecualian.

Contoh struktur:

```json
"scope": {
  "telegram_ids": [],
  "account_usernames": [],
  "exclude_telegram_ids": [],
  "exclude_usernames": [],
  "exclude_roles": [
    "owner"
  ]
}
```

Gunakan identifier yang stabil.

Telegram ID sebaiknya menjadi identifier utama, sedangkan username dapat digunakan sebagai identifier tambahan.

## 22. Contact

Informasi kontak maintenance dapat disimpan di konfigurasi.

Contoh:

```json
"contact": {
  "owner": "@AnosVlocticoal"
}
```

Gunakan data kontak yang benar-benar dipakai oleh panel.

## 23. Display

Bagian display dapat digunakan untuk pengaturan tampilan pada APK/panel.

Contoh:

```json
"display": {
  "show_title": true,
  "show_message": true,
  "show_reason": true,
  "show_progress": true,
  "show_contact": true,
  "show_countdown": true
}
```

Nama field harus mengikuti schema `maintenance.json` yang digunakan oleh versi v1.0.0.

## 24. Version Policy

Jika maintenance berhubungan dengan versi aplikasi, gunakan bagian kebijakan versi.

Contoh:

```json
"version_policy": {
  "enabled": false
}
```

Jangan memblokir versi aplikasi secara tidak sengaja.

## 25. Offline Policy

Jika GitHub tidak dapat diakses, gunakan kebijakan offline yang sudah ditentukan.

Konfigurasi offline harus tetap konsisten dengan mekanisme cache.

Tujuannya agar aplikasi tidak salah menampilkan status maintenance hanya karena koneksi internet GitHub gagal sesaat.

## 26. Hak Akses

Perubahan maintenance melalui panel hanya boleh dilakukan oleh owner/admin yang memiliki izin.

Pengguna biasa tidak boleh:

- Mengubah status maintenance.
- Mengubah target maintenance.
- Mengubah pesan maintenance.
- Menyimpan konfigurasi ke GitHub.
- Mengubah daftar pengecualian.
- Mengubah kebijakan aplikasi.

## 27. UI Maintenance

Maintenance sebaiknya diakses dari menu panel.

Contoh alur:

```text
Owner Panel
   ↓
Maintenance
   ↓
Dashboard Maintenance
```

Perintah manual seperti:

```text
/maintenance set ...
```

tidak diperlukan untuk penggunaan normal.

Semua perubahan dapat dilakukan melalui tombol dan input pesan dari owner/admin.

## 28. Tombol Maintenance

Gunakan warna/status tombol secara logis.

Contoh:

```text
🟢 AKTIFKAN
🔴 MATIKAN
🎯 TARGET
✏️ EDIT PESAN
👁️ PREVIEW
📊 STATUS
🔄 REFRESH
⬅️ KEMBALI
```

Gunakan custom emoji Telegram apabila tersedia.

Apabila Telegram menolak custom emoji, sistem harus mempunyai fallback ke emoji biasa agar menu tetap berjalan.

## 29. Preview Target

Sebelum menyimpan target, bot sebaiknya menampilkan hasil target setelah pengecualian owner diterapkan.

Contoh:

```text
Target konfigurasi:
ALL

Owner:
Dikecualikan

Hasil:
1. user01
2. user02
3. user03
```

Preview hanya untuk verifikasi dan tidak mengubah konfigurasi.

## 30. GitHub Update

Saat konfigurasi disimpan:

```text
1. Ambil maintenance.json lama.
2. Ambil SHA terbaru.
3. Buat konfigurasi baru.
4. Validasi.
5. Commit ke GitHub.
6. Fetch ulang.
7. Validasi ulang.
8. Update cache.
9. Reload panel.
10. Tampilkan hasil.
```

Jangan menggunakan SHA yang sudah kedaluwarsa apabila repository menerima perubahan dari tempat lain.

## 31. Keamanan Token

GitHub Personal Access Token **jangan ditaruh di `maintenance.json`**.

Jangan menaruh token di:

```text
README.md
maintenance.json
log
pesan Telegram
source code publik
```

Token harus disimpan melalui konfigurasi rahasia/environment/secret mechanism yang memang digunakan panel.

## 32. Backup

Sebelum perubahan besar, simpan salinan konfigurasi lama.

Contoh nama:

```text
maintenance.backup.json
```

Backup tidak wajib menjadi file yang dibaca panel. Fungsinya hanya sebagai cadangan.

## 33. Kesalahan yang Harus Dihindari

Jangan:

```text
schema_version = 2
```

untuk panel v1.0.0.

Jangan:

- Menghapus `maintenance.json` dari GitHub jika GitHub adalah source utama.
- Menulis JSON invalid.
- Menimpa konfigurasi valid dengan konfigurasi invalid.
- Menghapus owner dari pengecualian.
- Menganggap multi-account harus minimal 2 akun.
- Membuat pilihan satu akun menjadi lebih dari satu.
- Menyimpan perubahan sebelum review/konfirmasi.
- Menaruh GitHub token di file publik.

## 34. Checklist

Sebelum memakai maintenance:

```text
[ ] maintenance.json ada di GitHub
[ ] schema_version = 1
[ ] Raw URL dapat diakses
[ ] JSON valid
[ ] App ID benar
[ ] App version benar
[ ] Target maintenance benar
[ ] Owner dikecualikan
[ ] Pesan maintenance benar
[ ] Schedule valid jika digunakan
[ ] estimated_end valid jika digunakan
[ ] GitHub write permission benar
[ ] Token GitHub tidak berada di file publik
```

## 35. Prinsip Sistem

Sistem maintenance ZUAX FPS v1.0.0 menggunakan prinsip:

```text
GitHub = Source of Truth
        ↓
Validation
        ↓
Cache
        ↓
Safe Save
        ↓
Reload
        ↓
Maintenance
```

Semua perubahan penting sebaiknya dilakukan melalui alur:

```text
EDIT
 ↓
DRAFT
 ↓
PREVIEW
 ↓
CONFIRM
 ↓
VALIDATE
 ↓
SAVE
 ↓
RELOAD
```

Dengan alur tersebut, kesalahan input tidak langsung mengganti konfigurasi maintenance yang sedang aktif.
