# Kedaiin — feed pembaruan

Repo publik ini **hanya** berisi kabar versi dan paket pembaruan aplikasi **Kedaiin POS**.
Ini **bukan** kode sumber: kode aplikasi ada di repo privat `LIinz27/Kedaiin` dan tidak dibuka untuk pembeli.
Tidak ada `.env`, kredensial, atau data resto di repo ini — yang bisa diunduh di sini hanya paket yang sudah jadi.

| Berkas / lokasi | Isi |
|---|---|
| `update.json` | feed versi: versi terbaru, build, tanggal, ukuran, sha256, URL paket, `min_from_version` |
| `releases/download/<tag>/kedaiin-update-<versi>.zip` | paket pembaruan (aset rilis) |
| `releases/download/<tag>/Kedaiin-<versi>.apk` | APK tablet pelayan (aset rilis, sejak 1.2.5) |
| `releases/download/<tag>/Kedaiin-Desktop-<versi>.exe` | aplikasi PC kasir/pelayan (aset rilis, sejak 1.2.5) |
| `releases/download/<tag>/Kedaiin-Setup-<versi>[-slim].exe` | installer server, penuh & slim (aset rilis, sejak 1.2.5) |

## 1. Apa repo ini

Server Kedaiin di PC resto membaca feed ini untuk menjawab tombol **Pengaturan → Pembaruan → Cek versi terbaru**:

```text
https://raw.githubusercontent.com/LIinz27/Kedaiin-release/main/update.json
```

- **Tanpa token, tanpa login** — berkas publik, hanya dibaca.
- **Internet hanya dipakai saat tombol itu ditekan.** Resto tetap jalan penuh tanpa internet; feed yang tidak bisa dihubungi bukan kerusakan aplikasi, hanya berarti cek versi gagal.
- Sejak 1.2.3 paket yang lolos pemeriksaan bisa **dipasang dari layar** **Pengaturan → Pembaruan** oleh owner (§6.4). Mulai **1.2.5** server juga bisa **mengunduh paketnya sendiri** dari feed ini lewat tombol **Unduh & siapkan**, jadi zip tidak wajib dipindahkan lewat flashdisk (§6.1). Tetap tidak ada unduhan atau pemasangan di latar belakang: unduhan baru mulai setelah tombol ditekan dan bisa dibatalkan, pemasangan tetap butuh konfirmasi **PASANG**.

Isi `update.json` (schema 1):

| Field | Arti |
|---|---|
| `latest.version` / `latest.build` | versi `x.y.z` dan commit pendek yang dibangun jadi paket itu |
| `latest.released_at` | waktu rilis, UTC (`Z`) |
| `latest.size` | ukuran zip dalam byte — harus sama dengan berkas yang diunduh |
| `latest.sha256` | checksum zip — memastikan berkas tidak rusak atau berubah |
| `latest.url` | URL unduhan langsung (aset rilis), bukan halaman HTML |
| `latest.min_from_version` | versi terpasang tertua yang boleh melompat langsung ke versi ini |
| `history[]` | entri rilis sebelumnya, bentuknya sama |
| `apk` | **tambahan sejak 1.2.5** — `version`, `size`, `sha256`, `url` APK tablet terbaru. Opsional: klien lama tidak membacanya |
| `desktop` | **tambahan sejak 1.2.5** — `version`, `size`, `sha256`, `url` aplikasi PC (`.exe`) terbaru. Opsional |

Blok `apk` dan `desktop` hanya menambah kunci baru di tingkat atas. Klien 1.2.0–1.2.4 membaca `latest` dan `history` saja, jadi feed ini tetap aman untuk pembaruan yang sudah beredar — diuji dengan parser 1.2.4 terhadap feed 1.2.5, hasilnya paket 1.2.5 terbaca normal dan kunci baru diabaikan (§10.2).

Server menolak paket yang isinya tidak cocok dengan `manifest.json` di dalam zip. `size` dan `sha256` di feed dipakai memeriksa berkas yang **diunduh**, bukan untuk memasang.

## 2. Aset rilis 1.2.6 — mana yang harus saya unduh?
Semua berkas ada di [halaman rilis v1.2.6](https://github.com/LIinz27/Kedaiin-release/releases/tag/v1.2.6). Cocokkan sha256 sebelum memasang apa pun (§7).

| Berkas | Ukuran (byte) | SHA-256 | Untuk siapa |
|---|---|---|---|
| `kedaiin-update-1.2.6.zip` | 15.024.451 | `7b7619ee6cbd3093e213d06afb4e3814275b33fa831aa71004bc3e799594b545` | **pembaruan** — resto yang sudah jalan 1.1.0 ke atas |
| `Kedaiin-Setup-1.2.6.exe` | 335.676.608 | `185e986905c1f92336ad1608ecb1e41dd55c9968f6a6f7b12472cac4848d784d` | **server** — PC resto baru / pasang dari nol (membawa Node 24 + WebView2) |
| `Kedaiin-Setup-1.2.6-slim.exe` | 43.181.669 | `3f478bf702e4071bcf3fe0ad4571cecd9b623a60c808fbadff430816d593aa46` | **server** — PC yang sudah punya Node 24 + WebView2 |
| `Kedaiin-Desktop-1.2.6.exe` | 4.916.224 | `e00ac18a826f94d8f570bea4e7463f23fc73f64952084e629005ffeb8e4ffb27` | **kasir / pelayan** — PC kedua dan PC pemilik |
| `Kedaiin-1.2.6.apk` | 17.544.226 | `79dacfefd4689a46bb04231da6d7f7a5a35d9aaf7e3349577f43a950061df79f` | **tablet** pelayan (`id.kedaiin.pos`, versionCode 7) |

**Mana yang harus saya unduh?**

- **PC resto baru / belum pernah pasang** → `Kedaiin-Setup-1.2.6.exe`. Pakai `-slim` kalau PC itu sudah punya Node 24 + WebView2.
- **Resto sudah jalan 1.1.0 ke atas** → cukup `kedaiin-update-1.2.6.zip`; dipasang dari layar Pembaruan dengan **3 klik** (§6.1).
- **PC kasir/pelayan tambahan** → `Kedaiin-Desktop-1.2.6.exe`. **Tablet pelayan** → `Kedaiin-1.2.6.apk` (atau dari `http://<ip-server>:3000/apk`, §9).

Tidak yakin? Tiga aturan itu sudah mencakup semua kasus: satu PC (server) pakai installer, resto yang sudah jalan pakai zip, perangkat tambahan pakai `.exe`/`.apk`. Berkas yang salah pasang tidak merusak data, tetapi memboroskan waktu — installer mendapat pilihan mode **Server** dan **Klien** saat dipasang.

## 3. Rilis terbaru — 1.2.6
| | |
|---|---|
| Versi | **1.2.6** |
| Build | **d67234b** |
| Dirilis | **2026-11-27 15:52 UTC** (27 Nov 2026, 23:52 WITA) |
| Ukuran paket | **15.024.451 byte** (14,3 MB) |
| SHA-256 | `7b7619ee6cbd3093e213d06afb4e3814275b33fa831aa71004bc3e799594b545` |
| `min_from_version` | **1.1.0** |
| Unduhan | [kedaiin-update-1.2.6.zip](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.6/kedaiin-update-1.2.6.zip) |
| Catatan rilis | [§10.1](#101-rilis-126-27-november-2026) — Pengaturan dipecah per kategori (10 kategori, sidebar + kotak cari, tautan lama tetap hidup) |

**Arti `min_from_version` 1.1.0:** instalasi **1.1.0 ke atas** boleh langsung memakai paket ini. Instalasi di bawah 1.1.0 naik lewat installer versi baru, bukan paket zip.

## 4. Riwayat rilis
| Versi | Build | Dirilis (UTC) | Ukuran | SHA-256 | Paket |
|---|---|---|---|---|---|
| **1.2.6** | `d67234b` | 2026-11-27 15:52 | 15.024.451 | `7b7619ee6cbd3093e213d06afb4e3814275b33fa831aa71004bc3e799594b545` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.6/kedaiin-update-1.2.6.zip) |
| **1.2.5** | `e518e74` | 2026-11-27 12:51 | 15.006.636 | `8a9a986c6740030b5972dc15a6de4831e276b1ba5d48d7021a57da9ca655924b` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.5/kedaiin-update-1.2.5.zip) |
| **1.2.4** | `de4b184` | 2026-11-26 12:04 | 13.894.558 | `8a7d9e63bc528e0ea05624d64425ed883807a474a23ec06ec8947515f46c33f7` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.4/kedaiin-update-1.2.4.zip) |
| **1.2.3** | `ab81cf5` | 2026-11-26 08:16 | 13.849.080 | `d92143d08bb1a4627b4e0dd0371e8cac64e9f805626182a83aaf33aa75571dde` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.3/kedaiin-update-1.2.3.zip) |
| **1.2.2** | `fd0c5dc` | 2026-11-26 05:32 | 13.843.666 | `c8c2f5256191db685aca809ddfe7f84dd26f6571dee19daae82bed05680a4de0` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.2/kedaiin-update-1.2.2.zip) |
| **1.2.1** | `3d6ebf1` | 2026-11-20 14:39 | 13.847.165 | `2acfc02874d59884a84f98caacbd4a7dc3c1e0e842fbeaec9bd1bc6b31a25f21` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.1/kedaiin-update-1.2.1.zip) |
| **1.2.0** | `ca21782` | 2026-11-20 00:00 | 13.837.631 | `cc38b5da656d6d3b8eedbd04bb2ce7e7b555726a284d45435c5b5b8c4d5d050d` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.0/kedaiin-update-1.2.0.zip) |

Rilis 1.2.0 berisi sistem desain baru dan tujuh layar yang didesain ulang. Rilis 1.2.1 berisi perbaikan uang, perbaikan cetak, dan alat pembaruan itu sendiri (§10.6). Rilis 1.2.2 berisi perbaikan laporan, plafon angka, dan aksesibilitas tab Laporan (§10.5). Rilis 1.2.3 berisi pemasangan pembaruan dari layar Pengaturan plus rollback otomatis ke versi lama (§10.4). Rilis 1.2.4 memindahkan pencetakan tiket ke perangkat stasiun — Bluetooth dapur/bar boleh dipilih, dan APK tablet mencetak BLE tanpa Chrome (§10.3). Rilis 1.2.5 membuat pembaruan bisa diunduh langsung dari layar (3 klik), menambah cari printer & cari server, kunci layar, backup manual yang bisa diunduh, cetak laporan A4, dan autostart yang tahan baterai (§10.2). Rilis 1.2.6 memecah layar Pengaturan menjadi 10 kategori dengan sidebar dan kotak cari, tanpa memutus tautan lama (§10.1). Daftar lengkap: [halaman Releases](https://github.com/LIinz27/Kedaiin-release/releases).

## 5. Isi paket pembaruan
Zip berisi dua hal di root: `manifest.json` (versi, build, daftar berkas + ukuran + sha256 tiap berkas) dan folder `app/` (kode server + build web + dependensi, siap salin).

Paket 1.2.6 bila dibuka: **2.178 berkas** + `manifest.json`, **32.352.226 byte** setelah diekstrak (±30,9 MB; 14,3 MB terkompresi). `manifest.json` sendiri 396.685 byte.

| Bagian | Saat paket dipasang |
|---|---|
| Kode server (`apps/server`) + build web (`apps/web/dist`) + `node_modules` | diganti |
| `data\` (database SQLite, unggahan, paket pending) | **tidak disentuh** — semua transaksi resto ada di sini |
| `backup\` | **tidak disentuh** — cadangan harian 03:00 |
| `.env` | **tidak disentuh** — kunci acak instalasi |
| `logs\` | **tidak disentuh** — riwayat untuk penelusuran |
| Runtime Node + pm2 | **tidak ikut** — paket update hanya kode aplikasi; runtime urusan installer |
| Berkas dev (`app/.opencode/`) | ikut terbawa (5 berkas; `.tmp-verify/` dan `.worktrees/` tidak ada); tidak dipakai saat aplikasi jalan |

## 6. Cara memasang pembaruan
Tiga jalur, hasilnya sama — yang diganti hanya kode aplikasi, `data\` tidak disentuh: **(A) 3 klik dari layar**, mulai 1.2.5 paketnya diunduh sendiri oleh server (§6.1); **(B) pakai berkas zip** dari flashdisk (§6.2); atau **(C) skrip teknisi / installer** kalau layar tidak bisa dipakai (§6.4).

### 6.1 Jalur A — 3 klik dari layar Pembaruan (server punya internet)
1. **Cek** — **Pengaturan → Pembaruan → Cek versi terbaru**. Server membaca feed `update.json` dan menampilkan versi yang tersedia beserta ukurannya.
2. **Unduh & siapkan** — server mengunduh `kedaiin-update-1.2.6.zip` langsung dari URL di feed. Progres persen + MB terlihat di layar, ada tombol **Batal**, dan `sha256` hasil unduhan dicocokkan dengan feed: berkas yang tidak cocok **dibuang**, bukan disimpan.
3. **Pasang sekarang** — hanya untuk **owner**, dan hanya kalau tidak ada bill terbuka atau shift berjalan (§6.3). Ketik **PASANG**; kasir berhenti **±1–3 menit** dan jangan tutup jendela atau matikan PC (§6.4). Sesudahnya `curl.exe http://localhost:3000/health` harus menjawab `"build":"d67234b"`.

Kalau server tidak punya internet, pakai jalur B — hasilnya sama persis.

### 6.2 Jalur B — pakai berkas zip (server tanpa internet / USB)
1. Unduh `kedaiin-update-1.2.6.zip` dari §2 — boleh di PC mana pun yang ada internet.
2. Verifikasi checksum-nya (§7). **Jangan lewati langkah ini.**
3. Salin `.zip` ke flashdisk, pindahkan ke PC server (tidak perlu diekstrak).
4. Di PC server buka `http://<ip-server>:3000` (atau `Kedaiin.exe`), login sebagai **owner** atau **admin**.
5. **Pengaturan → Pembaruan** → **Pilih paket pembaruan…** → pilih berkas `.zip`. Berkas langsung diperiksa terhadap `manifest.json` di dalamnya: tiap berkas harus ada, ukurannya sama, sha256-nya sama. Bila lolos, layar menulis **Paket 1.2.6 (build d67234b) lolos verifikasi** beserta jumlah berkas dan ukurannya. Bila gagal, muncul daftar masalah: `hilang: …`, `ekstra: …`, `ukuran beda: …`, `sha256 beda: …`.
6. **Simpan untuk dipasang** — tombol ini hanya hidup untuk **owner** (§6.3). Paket tersimpan di `C:\kedaiin\data\updates\pending`.
7. **Pasang sekarang** → ketik **PASANG** (§6.4). Batas ukuran berkas: **80 MB**; selain `.zip` tidak bisa dipilih.

Jalur ini dipakai kalau **Cek versi terbaru** menjawab **Feed pembaruan belum tersedia — pakai paket dari USB** atau **Tidak bisa menghubungi server pembaruan**.

### 6.3 Syarat sebelum menyimpan paket
| Syarat | Kalau belum terpenuhi |
|---|---|
| Tidak ada bill terbuka | layar menolak: **Tidak bisa menyimpan paket: N bill masih terbuka.** Selesaikan atau void dulu |
| Shift kasir sudah ditutup | layar menolak: **shift kasir masih berjalan** |
| Login sebagai owner | admin bisa memeriksa dan cek versi, tetapi menyimpan paket hanya owner |
| Berkas `.zip` ≤ 80 MB | berkas lain tidak bisa dipilih |

Syarat ini ada karena pemasangan menutup aplikasi sebentar. Menyimpan paket di tengah jam ramai berarti teknisi akan menutup kasir saat ada tamu.

### 6.4 Memasang paket — mulai 1.2.3 dari layar Pembaruan
**Menutup lalu membuka aplikasi belum memasang apa pun.** Sejak **1.2.3** paket yang sudah tersimpan bisa dipasang langsung dari layar, oleh **owner**. Pemasangan tetap butuh **akses Windows dan PowerShell** di PC server: aplikasi memicu skrip, bukan menyalin berkas sendiri.

**a. Dari layar.** **Pasang sekarang** → **Lanjut** → ketik **PASANG** → **Pasang sekarang**. Kasir berhenti **±1–3 menit**; jangan tutup jendela atau matikan PC server. Enam tahap terlihat di layar: snapshot versi lama → salin berkas → pasang dependensi → build web → nyalakan server → cek `/health`. Status tiap tahap ditulis ke `logs\upgrade-state.json`.

Pemasangan **ditolak** kalau masih ada bill terbuka, shift kasir berjalan, versi terpasang lebih tua dari `min_from_version`, atau ada pemasangan lain yang sedang berjalan. Kalau `/health` tidak cocok dalam **60 detik**, sistem kembali sendiri ke versi lama (§6.5). `data\`, `backup\`, `.env`, `logs\`, dan runtime Node/pm2 tidak pernah disentuh — yang diganti hanya kode aplikasi.

**b. Skrip upgrade (kalau layar tidak bisa dipakai).** Ekstrak zip di PC server, lalu:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass `
  -File C:\kedaiin\deploy\windows\upgrade-kedaiin.ps1 `
  -SourceDir C:\kedaiin-baru\app
```

Urutan kerja skrip: backup dulu → salin berkas versi baru → `npm ci` → build web → `pm2 restart kedaiin-pos` → cek `/health`. `data\`, `backup\`, `.env`, `logs\`, `.pm2` tidak pernah ditimpa.

**c. Installer versi baru** (`Kedaiin-Setup-1.2.6.exe` — berkasnya ada di §2, checksumnya di §7). Dipasang **di atas** instalasi lama; folder data, backup, log, dan `.env` dikecualikan. Pakai cara ini kalau runtime Node/WebView2 ikut berubah, atau saat pasang dari nol.

Sesudah salah satu cara di atas, `curl.exe http://localhost:3000/health` harus menjawab `{"ok":true,"build":"d67234b","storage":"ok"}`. Kalau `build` masih `e518e74`, pemasangan belum terjadi.

### 6.5 Kalau versi baru bermasalah (rollback)
**Otomatis:** sebelum menyalin berkas baru, pemasangan membuat snapshot `app.prev-<versi>`. Kalau `/health` tidak cocok dalam 60 detik, server dikembalikan ke snapshot itu. Panel merah di layar Pembaruan juga menampilkan tombol **Kembalikan ke versi sebelumnya** (aktif kalau snapshot ada).

**Manual** di PC server, dari folder instalasi (mis. `C:\kedaiin`):

```powershell
powershell -NoProfile -ExecutionPolicy Bypass `
  -File .\rollback-kedaiin.ps1 -InstallDir C:\kedaiin
```

Skrip memulihkan kode dari `app.prev-<versi>`, menyalakan server, lalu memeriksa `/health` maksimal 60 detik. Aman dijalankan dua kali; tanpa snapshot tidak ada yang dihapus. Rollback tidak menimpa data; pulihkan database dari backup **hanya** kalau datanya memang rusak.

## 7. Cara memverifikasi berkas
Checksum memastikan berkas yang Anda pegang sama persis dengan yang diterbitkan. Kalau berbeda, berhenti — jangan dipasang.

**Linux / macOS:**

```bash
curl -L -o kedaiin-update-1.2.6.zip \
  https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.6/kedaiin-update-1.2.6.zip
sha256sum kedaiin-update-1.2.6.zip
```

**Windows (PowerShell):**

```powershell
curl.exe -L -o kedaiin-update-1.2.6.zip `
  https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.6/kedaiin-update-1.2.6.zip
Get-FileHash .\kedaiin-update-1.2.6.zip -Algorithm SHA256
```

| Berkas | SHA-256 |
|---|---|
| `kedaiin-update-1.2.6.zip` | `7b7619ee6cbd3093e213d06afb4e3814275b33fa831aa71004bc3e799594b545` |
| `Kedaiin-Setup-1.2.6.exe` (installer full) | `185e986905c1f92336ad1608ecb1e41dd55c9968f6a6f7b12472cac4848d784d` |
| `Kedaiin-Setup-1.2.6-slim.exe` (installer slim) | `3f478bf702e4071bcf3fe0ad4571cecd9b623a60c808fbadff430816d593aa46` |
| `Kedaiin-Desktop-1.2.6.exe` (aplikasi PC) | `e00ac18a826f94d8f570bea4e7463f23fc73f64952084e629005ffeb8e4ffb27` |
| `Kedaiin-1.2.6.apk` (tablet) | `79dacfefd4689a46bb04231da6d7f7a5a35d9aaf7e3349577f43a950061df79f` |
| `kedaiin-update-1.2.5.zip` (versi lama) | `8a9a986c6740030b5972dc15a6de4831e276b1ba5d48d7021a57da9ca655924b` |
| `Kedaiin-Setup-1.2.5.exe` (installer full, versi lama) | `e62370e4ada4988a24b21d584dbe41acae315536e77d8f3b27c155306cda2cf2` |
| `Kedaiin-Setup-1.2.5-slim.exe` (installer slim, versi lama) | `8c7e164e02b91d934b830d9247bf66f180c96924c45b1687a163aa946abccb28` |
| `Kedaiin-Desktop-1.2.5.exe` (aplikasi PC, versi lama) | `4621204cec6b8358f5ee1f8a6cfb8576531e45c2b5b51644e890a63d2b655795` |
| `Kedaiin-1.2.5.apk` (tablet, versi lama) | `826074b3f464fc3d153ae99cd27b5f2951e4c455f5b9775ed6cee8c2cc02d1d4` |
| `kedaiin-update-1.2.4.zip` | `8a7d9e63bc528e0ea05624d64425ed883807a474a23ec06ec8947515f46c33f7` |
| `kedaiin-update-1.2.3.zip` | `d92143d08bb1a4627b4e0dd0371e8cac64e9f805626182a83aaf33aa75571dde` |
| `kedaiin-update-1.2.2.zip` | `c8c2f5256191db685aca809ddfe7f84dd26f6571dee19daae82bed05680a4de0` |
| `kedaiin-update-1.2.1.zip` | `2acfc02874d59884a84f98caacbd4a7dc3c1e0e842fbeaec9bd1bc6b31a25f21` |
| `kedaiin-update-1.2.0.zip` | `cc38b5da656d6d3b8eedbd04bb2ce7e7b555726a284d45435c5b5b8c4d5d050d` |
| `kedaiin-pos.apk` (1.2.4) | `0596fdfc076dfff5e7eaf8f2407aefc97a77d55c45393a53927d123956744d6b` |
| `kedaiin-pos.apk` (1.2.3) | `c4c6d99f0a7c92319492e12cfe960b0cacba714fc7e639b076ded2023e4fbb19` |
| `Kedaiin-Setup-1.2.4.exe` (installer full, dari penjual) | `7f94e1c2091ef9d98df002c89555152af144d84b27c85529d83d317b5a28235c` |
| `Kedaiin-Setup-1.2.4-slim.exe` (installer slim, dari penjual) | `4a1e24a5e01fa9f4aadec5d17b7f2f1c93d18cbbd9547a45db55efcb372f90fa` |
| `Kedaiin-Setup-1.2.3.exe` (installer full, versi lama, dari penjual) | `15a36ae0ae8911458cf747a1269d550bc3e760180d9ae26609b7f143bf2ef30d` |
| `Kedaiin-Setup-1.2.3-slim.exe` (installer slim, versi lama, dari penjual) | `bffe7a79fb9f08c0c3559476a4668a6edea2e50926b357c7b2a03e6f7d3ee2a8` |
| `Kedaiin-Setup-1.2.2.exe` (installer full, versi lama, dari penjual) | `60f4c6b3eee1026a03dfd95c831e36c469f329aa50a1d1caa0945027cbb14d1a` |
| `Kedaiin-Setup-1.2.2-slim.exe` (installer slim, versi lama, dari penjual) | `9c770a5c1c3ad0dffedf3718da19bb9f5f39c5ea8028d3c752582da224e0f7a0` |

APK 1.2.6 juga punya MD5 `e9215f5b54934ba3c8ef6932f74a798a` — berguna kalau tablet hanya menyediakan alat MD5 (`Get-FileHash .\Kedaiin-1.2.6.apk -Algorithm MD5`). MD5 1.2.5: `fae7d854cb8983c9435e2377f8791f06`.

Sejak 1.2.5 **semua berkas** (installer full & slim, aplikasi PC, APK) diunggah di halaman rilis — lihat §2. Installer 1.2.5 (335.673.377 byte full / 43.177.812 byte slim), aplikasi PC 1.2.5, dan APK 1.2.5 tetap ada di [halaman rilis v1.2.5](https://github.com/LIinz27/Kedaiin-release/releases/tag/v1.2.5). Versi 1.2.4 ke bawah masih hanya berupa zip; installernya minta ke penjual (1.2.4: 334.813.849 byte full / 42.319.547 byte slim; 1.2.3: 334.774.515 / 42.279.430; 1.2.2: 334.755.715 / 42.261.460).

Ukuran berkas harus sama dengan `size` di `update.json` (15.024.451 byte untuk 1.2.6). Ukuran beda = unduhan terpotong, ulangi.

## 8. Persyaratan server
| Perlu | Keterangan |
|---|---|
| Windows **10/11 64-bit** | Windows 7/8 tidak didukung |
| RAM 4 GB (8 GB nyaman), disk kosong ±1 GB | PC server biasanya juga dipakai kasir |
| **Node 24** | dibawa installer — tidak perlu memasang Node sendiri. Paket update **tidak** membawa runtime |
| WebView2 | dibawa installer, dipasang otomatis kalau belum ada |
| Port **3000** TCP | dibuka installer di firewall; klien mengakses `http://<ip-server>:3000` |
| IP tetap untuk PC server | mis. `192.168.1.10`; kalau IP berubah, semua klien kehilangan sambungan |
| Printer thermal **58 mm ESC/POS** | kasir: LAN / USB / Bluetooth / **serial (COM)** — tombol **Cari printer** menemukan printer jaringan dan antrean Windows tanpa mengetik IP |
| Printer **A4** (opsional) | untuk **Cetak laporan A4** dan simpan PDF (§10.2); tanpa printer A4 laporan tetap bisa disimpan sebagai PDF |
| **Dapur & bar: printer LAN `ip:9100` (disarankan)** | sejak 1.2.4 **Bluetooth** boleh dipilih untuk dapur/bar, tetapi hanya jalan kalau stasiun itu punya **perangkat terdaftar yang menyala** (tablet/PC dengan halaman aplikasi terbuka); printer USB di dapur/bar tidak akan jalan |
| Lisensi **satu per resto** | aktivasi offline: layar **Aktivasi Lisensi** menampilkan **Kode Minta** → penjual mengirim **Kode Aktivasi** |

Satu resto = satu PC server. PC itu menyimpan seluruh data dan melayani PC/tablet lain di jaringan resto.

## 9. Klien
| Klien | Apa itu | Kapan dipakai |
|---|---|---|
| **`Kedaiin.exe`** (PC) | pembungkus Tauri + WebView2; UI tetap diambil dari server. Berkasnya `Kedaiin-Desktop-1.2.6.exe` (§2) | PC kasir kedua, PC pemilik |
| **APK tablet** | aplikasi Android `id.kedaiin.pos` untuk pelayan | tablet pelayan (Wi-Fi jaringan resto) |

**`Kedaiin.exe`** dipasang installer di PC; ikon desktop membuka layar resto tanpa address bar browser. Alamat server diisi saat pemasangan mode **Klien** (mis. `http://192.168.1.10:3000`). Kalau PC hanya dipakai sesekali, cukup buka alamat server di browser — bedanya hanya pembungkus jendela.

**APK tablet** dilayani dari server resto, **bukan** dari repo ini: `http://<ip-server>:3000/apk` — halaman itu menampilkan QR code dan tombol **Unduh APK**. Berkas 1.2.6 (berkas yang sama juga tersedia langsung di halaman rilis sebagai `Kedaiin-1.2.6.apk`, §2):

| | |
|---|---|
| Berkas | `kedaiin-pos.apk` |
| Ukuran | **17.544.226 byte** |
| MD5 | `e9215f5b54934ba3c8ef6932f74a798a` |
| SHA-256 | `79dacfefd4689a46bb04231da6d7f7a5a35d9aaf7e3349577f43a950061df79f` |
| `versionName` / `versionCode` | **1.2.6** / **7** |
| Package | `id.kedaiin.pos` |

**APK harus di-build ulang setiap rilis web.** Memperbarui server **tidak** memperbarui APK yang sudah terpasang di tablet: berkas APK baru disalin ke server (menggantikan `kedaiin-pos.apk`), lalu tablet memasang ulang lewat `/apk`. Kalau berkas APK di server diganti, checksum di atas **tidak berlaku lagi** — hitung ulang dari berkas yang benar-benar dilayani server.

## 10. Catatan rilis

### 10.1 Rilis 1.2.6 (27 November 2026)
Dirilis 27 November 2026, build `d67234b`. Ringkasan: layar **Pengaturan** dipecah menjadi **10 kategori** dengan sidebar dan kotak cari. Tidak ada fitur baru dan struktur data tidak berubah — ini penataan, dan tautan lama tetap hidup.

**Baru**
- **Pengaturan per kategori** (`dc58e93`) — 10 kategori: **Umum**, **Penjualan**, **Menu & Meja**, **Printer**, **Jaringan**, **Keamanan**, **Data & Backup**, **Pembaruan**, **Lisensi & Aplikasi**, dan **Audit** (khusus owner). Rute per kategori: `/pengaturan/umum`, `/pengaturan/printer`, `/pengaturan/data`, dan seterusnya; membuka `/pengaturan` mendarat di **Umum**.
- **Sidebar + kotak cari** — pencarian mencocokkan nama kategori dan kata kunci (mis. *pajak* → **Penjualan**, *backup* → **Data & Backup**); kategori yang sedang dibuka tetap tampil walaupun tidak cocok dengan kata kunci, dan jumlah hasil terlihat.
- **Tautan lama dialihkan, bukan diputus** — `#s-identitas` → **Umum**; `#s-biaya`, `#s-promo` → **Penjualan**; `#s-stasiun`, `#s-meja` → **Menu & Meja**; `#s-printer`, `#s-com`, `#s-ble`, `#s-device` → **Printer**; `#s-alamat` → **Jaringan**; `#s-keamanan` → **Keamanan**; `#s-cadangan` → **Data & Backup**; `#s-pembaruan` → **Pembaruan**; `#s-lisensi`, `#s-android` → **Lisensi & Aplikasi**; `?tab=audit` → **Audit**.
- **Isi panel tidak hilang** — printer (LAN/serial/Bluetooth, cari printer, perangkat ini), pembaruan (cek, unduh, pasang, rollback), backup (jalankan, unduh, verifikasi), lisensi, dan audit dipindah utuh ke panel kategorinya.

**Teknis & uji**
- `PengaturanPage.tsx` **2.536 → 390 baris**; panel dipisah ke 17 berkas di `apps/web/src/pages/pengaturan/` (terbesar `PrinterPanel.tsx` 508 baris), dengan `categories.ts` sebagai satu sumber daftar kategori, label, kata kunci, dan pemetaan tautan lama.
- Test web **112/112 lulus** (1.2.5: 98), termasuk test baru untuk pemetaan kategori dan pencarian; `tsc` bersih; `lint` 0 error (28 warning lama).
- **Paket**: 2.178 berkas + `manifest.json` = 32.352.226 byte setelah diekstrak (zip 15.024.451 byte); `manifest.json` → version 1.2.6, build `d67234b`, created 2026-11-27T15:49:57Z.
- **Catatan jujur:** installer belum bertanda tangan (SmartScreen muncul) dan jalur Bluetooth belum diuji dengan printer fisik — sama seperti 1.2.5.

**Yang berubah bagi pengguna:** layar Pengaturan kini punya daftar kategori di sisi kiri dan kotak cari, dan alamat lama (`#s-cadangan`, `?tab=audit`, dst.) tetap membuka kategori yang benar — tautan atau bookmark yang sudah disimpan tidak mati. Tidak ada data atau alur kerja yang berubah. Tablet tetap perlu dipasang ulang APK-nya supaya ikut 1.2.6 (§9) — memperbarui server tidak memperbarui APK yang sudah terpasang.

### 10.2 Rilis 1.2.5 (27 November 2026)
Dirilis 27 November 2026, build `e518e74`. Ringkasan: pembaruan bisa diunduh server sendiri dari feed lalu dipasang dengan tiga klik; **Cari printer** dan **Cari server** menghapus kebutuhan mengetik IP; ditambah kunci layar, backup manual yang bisa diunduh, dan cetak laporan A4.

**Baru**
- **Pembaruan satu klik** — `POST /api/update/download` mengunduh paket dari URL di feed, `GET /api/update/download/status` memberi progres (persen + byte) yang dipantau layar, `POST /api/update/download/cancel` membatalkannya. Sha256 hasil unduhan dicocokkan dengan `latest.sha256` sebelum paket disimpan; berkas yang tidak cocok dibuang. Jalur **Pilih paket pembaruan…** (USB) tetap ada.
- **Cari printer** — `printer-discovery.js` memindai printer lewat mDNS, menyapu port **9100** di subnet resto, dan membaca antrean printer Windows (`printer-os.js`); hasilnya bisa dipakai langsung sebagai printer kasir/dapur/bar. Daftar **port COM** tersedia untuk printer serial.
- **Transport serial (COM)** — kolom `printers.kind` menerima nilai `serial`; `serialport` dimuat lazy di `print.js` (hanya saat printer serial dipakai, jadi build Linux/macOS tidak butuh binding native).
- **Cari server** — klien menemukan server lewat mDNS `_kedaiin._tcp` (`server-info.js` + `mdns.js`), dan alamat server ditampilkan sebagai **QR code** supaya tablet/ponsel bisa membuka tanpa mengetik.
- **Kunci layar & logout bersih-bersih** — layar bisa dikunci tanpa keluar akun; **logout** membersihkan cache data resto di perangkat itu.
- **Backup sekarang + unduh** — `POST /api/backup/run` membuat backup segera, `GET /api/backup/download/*` mengunduh berkasnya, `GET /api/backup/verify/*` memeriksa isinya. Backup terjadwal 03:00 tetap jalan.
- **Cetak laporan A4** — halaman cetak laporan memakai `@page { size: A4; margin: 12mm }` (hitam-putih) dan bisa disimpan sebagai PDF lewat dialog cetak.
- **Autostart tahan baterai + watchdog** — installer membuat task `KedaiinPOS` dengan `AllowStartIfOnBatteries`, tanpa `StopIfGoingOnBatteries`, batas waktu tak terbatas, dan 3× pengulangan bila gagal. Setelah task dibuat, installer **membaca ulang** setelannya dan berhenti dengan pesan jelas kalau Windows masih menolak start saat memakai baterai (penyebab klasik "server mati setiap restart di laptop/UPS"). `watchdog-kedaiin.ps1` menyalakan lagi server yang mati; `cek-kedaiin.ps1` melaporkan task, port, log, dan `/health` dalam satu layar.
- **Exclusion Windows Defender** — installer menambahkan folder instalasi ke exclusion Defender **khusus mode server** (tanpa itu pemindaian `node_modules` memperlambat start); **uninstaller mencabutnya lagi**. Kalau exclusion gagal (mesin dikelola kebijakan grup), instalasi tetap lanjut — hanya lebih lambat.

**Perbaikan**
- **Payload installer tidak lagi membawa `.worktrees/`** (186 MB sampah cabang lain) dan ada penjaga supaya tidak terulang; jadi isi installer 1.2.5 tidak lagi membawa 186 MB sampah itu (ukuran berkas installernya sendiri 335.673.377 byte, 1.2.4: 334.813.849 byte).

**Teknis & uji**
- **Paket**: 2.178 berkas + `manifest.json` = 32.296.519 byte setelah diekstrak (zip 15.006.636 byte); `manifest.json` → version 1.2.5, build `e518e74`, created 2026-11-27T12:42:50Z.
- **Migrations**: tabel `printers` dibangun ulang otomatis (`rebuildTableIfNeeded('printers','serial')`) supaya instalasi lama menerima jenis printer baru tanpa kehilangan baris.
- **Feed**: `update.json` 1.2.5 menambah kunci `apk` dan `desktop`. Diuji dengan **parser 1.2.4** terhadap feed baru — `latest` terbaca (versi 1.2.5, ukuran, sha256), kunci baru diabaikan, tidak ada galat. Uji `update.test.js` juga menjalankan cek terhadap feed publik sungguhan.
- **Catatan jujur:** installer belum bertanda tangan (SmartScreen muncul), pemasangan pembaruan menghentikan kasir ±1–3 menit, dan jalur Bluetooth di `Kedaiin-Desktop-1.2.5.exe` belum diuji dengan printer fisik — untuk PC, printer LAN `ip:9100` atau serial/USB tetap yang paling aman.

**Yang berubah bagi pengguna:** pembaruan tidak lagi butuh flashdisk kalau server punya internet (**Cek → Unduh & siapkan → Pasang sekarang**). Printer jaringan dan server kini bisa dicari dari layar. Tablet harus dipasang ulang APK-nya supaya ikut 1.2.5 (§9) — memperbarui server tidak memperbarui APK yang sudah terpasang.

### 10.3 Rilis 1.2.4 (26 November 2026)
Dirilis 26 November 2026, build `de4b184`. Ringkasan: pencetakan tiket pindah ke **perangkat yang ada di stasiunnya** — perangkat mengambil job-nya sendiri, mencetak di tempat, lalu melapor — sehingga dapur/bar bisa memakai printer **Bluetooth**. Struktur data bertambah (tabel `print_devices`, kolom lease di `print_jobs`); migrasi berjalan otomatis saat aplikasi pertama dijalankan.

**Baru**
- **Agen cetak per perangkat** (`595b933` server, `86b8b76` web) — di **Pengaturan → Printer → Perangkat ini** centang stasiun yang dicetak perangkat itu (Kasir / Dapur / Bar), lalu **Simpan perangkat**. Selama halaman aplikasi terbuka, perangkat mengambil sendiri tiket stasiunnya tiap **3 detik** (maksimal 5 job sekali ambil), mencetak lokal, lalu melaporkan hasilnya. Job Bluetooth tidak lagi dikirim server: job ditahan `pending` dengan **lease 60 detik**, jadi kalau perangkat mati di tengah jalan job bisa diambil lagi setelah lease habis — tidak hilang. Indikator di header: *Cetak: aktif (dapur, bar) · 3 job*.
- **Bluetooth dapur/bar boleh dipilih di layar** (`de4b184`) — pilihan **Bluetooth** tidak lagi terkunci untuk stasiun kasir, dan mengganti stasiun tidak memaksa jenis koneksi kembali ke LAN. Server menolak kalau stasiun itu belum punya perangkat terdaftar: *Bluetooth butuh perangkat terdaftar untuk stasiun ini — daftarkan di blok 'Perangkat ini'*; untuk dapur/bar ditambah peringatan *Printer dapur/bar sebaiknya LAN; Bluetooth bergantung pada perangkat yang menyala*.
- **Agen cetak untuk peran pelayan** (`de4b184`) — tablet yang login sebagai pelayan ikut mengambil job cetak. Mendaftarkan atau menghapus perangkat tetap hanya owner/admin.
- **Bluetooth native di APK tablet** (`a19b67f`) — plugin `@capacitor-community/bluetooth-le` 6.1 dengan izin Android 12+ (`BLUETOOTH_SCAN`, `BLUETOOTH_CONNECT`; izin lokasi hanya diminta di Android 11 ke bawah). Tablet bisa mencetak printer thermal Bluetooth **tanpa Chrome/Edge**.
- **Endpoint & tabel baru** — `POST/GET/DELETE /api/print/devices`, `POST /api/print/jobs/claim`, `POST /api/print/jobs/:id/ack`; tabel `print_devices`; `print_jobs` menambah `claimed_by`, `claimed_at`, `lease_until`.

**Perbaikan**
- **Bluetooth tidak lagi masuk antrean printer Windows** (`ea45547`) — printer `bt` tidak pernah dikirim ke antrean OS; tombol tes cetak Bluetooth dijalankan dari browser, bukan dari server. Server tidak bisa mencetak BLE sendiri.
- **Sub-judul seksi Pembaruan** (`d44a619`) — kini menyebut pemasangan dilakukan dari layar itu (kasir berhenti ±1–3 menit), bukan "dipasang saat aplikasi ditutup".

**Teknis & uji**
- **Uji server** — `print-devices.test.js` + `printers.test.js`: **17 test, 17 lulus** (dijalankan 2026-11-26 di mesin build). Termasuk printer `bt` stasiun dapur ditolak 400 tanpa perangkat dan diterima saat perangkat terdaftar; peran `pelayan` boleh claim & ack tetapi ditolak 403 saat membaca daftar perangkat.
- **Klaim job atomik** — satu `UPDATE … RETURNING` dengan `lease_until = now + 60s`, jadi dua perangkat yang memanggil bersamaan tidak bisa mendapat job yang sama.
- **Cetak BLE di APK** menulis payload per potongan ±20 byte dengan jeda 20 ms (MTU default 23 − 3) supaya buffer printer tidak meluap; modul native diimpor dinamis sehingga build web tidak ikut memuatnya.
- **Catatan jujur:** pemasangan 1.2.4 **belum diuji di PC Windows nyata**, dan `manifest.json` di dalam zip masih mengosongkan `min_from_version` (sama seperti 1.2.1–1.2.3) — syarat versi minimum hanya ditegakkan dari feed `update.json`. Aplikasi `.exe` (PC) **belum bisa mencetak Bluetooth** karena WebView2 tidak punya Web Bluetooth; jalur native Tauri masih di cabang `wip/print-agent-exe`, belum dirilis.

**Yang berubah bagi pengguna:** printer Bluetooth di dapur/bar kini bisa dipakai — asal stasiun itu punya perangkat terdaftar yang menyala; printer LAN tetap disarankan. Agen cetak hanya jalan selama halaman aplikasi terbuka, jadi job menunggu kalau tablet mati. Tablet harus dipasang ulang APK-nya supaya ikut 1.2.4 dan bisa mencetak BLE (§9) — memperbarui server tidak memperbarui APK yang sudah terpasang.

### 10.4 Rilis 1.2.3 (26 November 2026)
Dirilis 26 November 2026, build `ab81cf5`. Ringkasan: pembaruan kini bisa dipasang dari layar **Pengaturan**, dan kalau versi baru tidak sehat sistem kembali sendiri ke versi lama. Struktur data tidak berubah — cukup perbarui aplikasi.

**Baru**
- **Pasang pembaruan dari layar Pengaturan** (`cc44c67`) — unggah `.zip` → diperiksa (struktur, sha256 tiap berkas, versi) → **Simpan untuk dipasang** → dialog dua langkah → **Pasang sekarang**. Ditolak kalau masih ada bill terbuka, shift kasir berjalan, versi terpasang lebih tua dari syarat minimum paket, atau ada pemasangan lain yang berjalan.
- **Kembalikan ke versi sebelumnya** (`4a1f81e`, `cc44c67`) — sebelum menyalin berkas baru, dibuat snapshot `app.prev-<versi>`; kalau `/health` tidak cocok dalam 60 detik, rollback dijalankan otomatis. Tombol **Kembalikan ke versi sebelumnya** di layar menjalankan hal yang sama secara manual. Data, backup, log, `.env`, dan runtime Node/pm2 tidak pernah disentuh.
- **Progres pemasangan** (`cc44c67`) — enam tahap terlihat di layar: snapshot versi lama → salin berkas → pasang dependensi → build web → nyalakan server → cek `/health`. Status tiap tahap ditulis ke `logs\upgrade-state.json`.

**Perbaikan**
- **`/health` melaporkan build proses, bukan isi `dist`** (`b7a4e05`) — dulu build dibaca dari berkas `dist` yang bisa diganti tanpa restart, jadi server bisa mengaku versi baru padahal prosesnya masih versi lama. Sekarang `/health` melaporkan build yang benar-benar berjalan; `/api/update/status` menambahkan `process_build`, `dist_build`, dan `mismatch` supaya selisih itu kelihatan dan layar menyarankan restart.

**Teknis & uji**
- **Test peran 403** (`89e5721`) — 8 test, semua lulus: kasir ditolak di 16 endpoint (`update/*`, `PATCH`/`DELETE /api/users/:id`, enam laporan analitik + ekspor CSV), tiap sel punya pembanding owner.
- **Guard APK pakai versi, bukan ukuran** (`1d1a1eb`) — `deploy/windows/check-apk.sh` memeriksa magic `PK` lalu mencocokkan versi APK dari sidecar `<apk>.version` dengan `kedaiin.iss`. Angka ukuran hardcoded yang selalu basi tidak lagi memblokir build rilis.
- **Catatan jujur:** jalur pemasangan-dari-layar 1.2.3 **belum diuji di PC Windows nyata** saat rilis ini diterbitkan. `manifest.json` di dalam zip juga belum mengisi `min_from_version` (sama seperti 1.2.1/1.2.2) — syarat versi minimum hanya ditegakkan dari feed `update.json`.

**Yang berubah bagi pengguna:** pemilik resto bisa memasang paket sendiri dari layar Pembaruan — asal PC server punya akses PowerShell; kalau tidak, jalur skrip/installer di §6.4 tetap ada. Tablet harus dipasang ulang APK-nya supaya ikut 1.2.3 (§9) — memperbarui server tidak memperbarui APK yang sudah terpasang.

### 10.5 Rilis 1.2.2 (26 November 2026)
Dirilis 26 November 2026, build `fd0c5dc`. Ringkasan: laporan penjualan bisa dipakai tanpa mouse, dan daftar menu **Tidak terjual** kini langsung terlihat di tab Ringkasan. Struktur data tidak berubah — cukup perbarui aplikasi.

**Baru**
- **Kartu "Tidak terjual" di tab Ringkasan** — sebelumnya terkubur di dasar tab Laba, kini jadi kartu sendiri di tab pertama supaya menu yang tidak laku cepat ketahuan (`bd4d8a7`). Tidak ada fitur baru lain di 1.2.2; sisanya perbaikan dan pekerjaan rilis.

**Perbaikan**
- **Laporan menyebut alasannya saat CSV kosong** — kalau rentang tanggal tidak ada datanya, keterangan di bawah filter menulis *Tidak ada data pada rentang ini — CSV kosong*, bukan tombol unduh yang mati tanpa penjelasan (`bd4d8a7`).
- **Angka mustahil ditolak dengan pesan jelas** — batas atas dipasang di 7 endpoint: baris order maks 200, qty per baris maks 1.000, kas awal/akhir shift maks Rp1.000.000.000, penyesuaian stok maks 100.000, urutan (`sort`) maks 10.000, `min_select` dan `max_select` maks 100 (`e042b57`). Salah ketik tidak lagi tersimpan sebagai data rusak.

**Aksesibilitas**
- **Tab Laporan bisa dipindah dengan keyboard** — panah kiri/kanan berpindah tab sekaligus memilihnya, Home ke tab pertama, End ke tab terakhir, memutar di ujung; hanya tab aktif yang masuk urutan Tab (`bd4d8a7`).

**Teknis & uji**
- **7 test batas baru** untuk plafon di atas, menutup sel test-plan 0 `[BELUM]` (`e042b57`).
- **Path proyek disamakan** ke `~/projects/kedaiin/app` di dokumen dan skrip (`170c44e`).
- **Versi satu sumber** — `version.json` jadi acuan tunggal; `scripts/set-version.mjs` menulis 4 tempat (AppVersion, VersionInfoVersion, gradle `versionName`/`versionCode`, `package.json`) dan `--check` gagal kalau ada yang tidak cocok (`ac71c87`).
- **Versi 1.2.2** — `version.json` 1.2.2 / code 3 / dirilis 2026-11-26; `set-version --check` 4/4 tempat cocok (`fd0c5dc`).
- **Catatan jujur:** `manifest.json` di dalam zip tidak mengisi `min_from_version` (sama seperti 1.2.1). Penegakan versi minimum hanya lewat `update.json` di feed.

**Yang berubah bagi pengguna:** tab Laporan dipindah dengan panah kiri/kanan, Home/End; kartu **Tidak terjual** kini di Ringkasan, bukan lagi di bawah tab Laba. Tablet harus dipasang ulang APK-nya supaya ikut 1.2.2 (§9) — memperbarui server tidak memperbarui APK yang sudah terpasang.

### 10.6 Rilis 1.2.1 (20 November 2026)
Dirilis 20 November 2026, build `3d6ebf1`. Ringkasan: perbaikan uang (refund, diskon void, tunai tanpa shift), kegagalan cetak tidak lagi disembunyikan, dan alat pembaruan (seksi Pembaruan + feed publik).

**Baru**
- **Seksi Pembaruan di Pengaturan** — endpoint `info` / `verify` / `stage` / `check` plus tombol **Pilih paket pembaruan…**, **Cek versi terbaru**, **Simpan untuk dipasang**, **Batalkan** (`5d8eefc`).
- **Cek versi lewat feed publik** — versi dibandingkan secara semver; laporan verifikasi juga menyebut apakah paket cocok dengan feed (`matches_feed`) (`0e5b83d`).
- **Pembuat paket pembaruan** — `manifest.json` berisi sha256 tiap berkas, plus skrip pemeriksa paket (`8556d14`).

**Perbaikan**
- **Kegagalan cetak saat membuat order tidak lagi ditelan** — order tetap tersimpan, tetapi kasir melihat `print_warning` alih-alih gagal diam-diam (`111e6f3`).
- **Tunai tanpa shift** kini masuk `expected_cash` dan tampil di UI, jadi uang laci tidak lagi tampak kurang (`69fea15`).
- **Refund & diskon void** — dua bug uang yang ditemukan lewat matriks uji ditutup (`44b814b`).
- **Pesan cek versi dibedakan** — belum ada rilis, offline, dan HTTP error tidak lagi tampil sebagai satu pesan yang sama (`2e5a8a7`).
- **Dependensi** — glob/minimatch dinaikkan; 5 peringatan moderate tertutup (`2346c8e`).

**Teknis & uji**
- **Matriks uji endpoint** uang/akses/master data ditambahkan (`2b0f737`).
- **60 sel test-plan ditutup**, 33 test baru; dari situ dua bug uang & meja di atas ketemu (`44b814b`).
- **E2E** skenario h memakai label nyata, bukan nilai yang dikirim langsung ke API (`3949759`).
- **README repo kode** ditulis ulang (`68eb9a7`).
- **Bump versi** 1.2.1 (`3d6ebf1`).

Batas yang berlaku untuk 1.2.1 dan 1.2.2: pemasangan pembaruan **belum otomatis** — paket hanya diperiksa dan disimpan. Pemasangan dari layar baru ada di 1.2.3 (§6.4).

## 11. Dukungan
| | |
|---|---|
| WhatsApp | **0895-4152-04242** — [wa.me/62895415204242](https://wa.me/62895415204242) |
| Jam kerja | **09.00–17.00 WITA** |

Saat melapor bug, sertakan empat hal ini supaya masalah bisa ditelusuri tanpa bolak-balik — dan jangan kirim database atau `.env` lewat WhatsApp:

1. **Versi & build** — baris pertama seksi **Pengaturan → Pembaruan** (contoh: *Versi terpasang 1.2.6 · build d67234b*).
2. **Keluaran `/health`** di PC server — `curl.exe http://localhost:3000/health` (PowerShell 5.1: pakai `curl.exe`, bukan `curl`). Jawabannya memuat `build` (commit yang sedang jalan) dan `storage` (`ok` = folder data bisa ditulis).
3. **Log server**, 30 baris terakhir: `$env:PM2_HOME='C:\kedaiin\.pm2'` lalu `pm2 logs kedaiin-pos --lines 30`.
4. **Apa yang dilakukan sebelum masalah muncul** — tombol yang ditekan, pesan di layar (screenshot), dan jam kejadian.

## 12. FAQ
**Apakah butuh internet?**
Tidak. Kasir, meja, cetak, laporan, dan shift jalan penuh tanpa internet. Internet hanya dipakai saat menekan **Cek versi terbaru** dan saat menekan **Unduh & siapkan**; tanpa internet, paket tetap bisa dipasang dari flashdisk (§6.2).

**Apakah data hilang saat update?**
Tidak. Paket hanya mengganti kode aplikasi; `data\`, `backup\`, `.env`, dan `logs\` tidak disentuh. Skrip upgrade membuat backup dulu sebelum menyalin apa pun.

**Kenapa tampilan di tablet tidak berubah setelah update?**
Karena APK adalah aplikasi terpisah. Update server tidak menyentuh APK yang sudah terpasang — APK baru harus di-build, disalin ke server, lalu dipasang ulang dari `/apk` (§9).

**Bagaimana kalau update gagal?**
Paket yang tidak lolos pemeriksaan **tidak disimpan** dan server tetap di versi lama. Kalau gagal saat pemasangan, skrip upgrade menghidupkan lagi versi lama dan menulis **Data TIDAK rusak**; mulai 1.2.3 rollback juga berjalan sendiri kalau `/health` tidak cocok dalam 60 detik (§6.5).

**Apakah update berjalan sendiri setelah aplikasi ditutup?**
Tidak — dan itu disengaja. Sejak 1.2.5 server bisa mengunduh paketnya sendiri, tetapi unduhan baru mulai setelah **Unduh & siapkan** ditekan (bisa **Batal**), dan pemasangan tetap harus ditekan owner (**Pasang sekarang** → ketik **PASANG**), hanya kalau tidak ada bill terbuka atau shift berjalan (§6.1, §6.4). Tidak ada unduhan atau pemasangan di latar belakang.

## 13. Untuk penjual: menerbitkan rilis berikutnya
Dijalankan di mesin build, di repo kode privat (bukan di repo ini): build paket dari **commit rilis** (bukan `main` terbaru kalau rilis ditag lebih dulu) → verifikasi dengan `verify-update-package.sh` sampai status `OK` → catat ukuran zip dan `sha256sum` → isi `latest` di `update.json` dan geser entri lama ke `history` → unggah **lima aset** ke rilis baru (tag `v<versi>`): zip paket, APK, `.exe` desktop, installer full + slim → periksa dari luar: `curl -s https://raw.githubusercontent.com/LIinz27/Kedaiin-release/main/update.json` dan `curl -sIL <url-aset-rilis-baru> | grep -i '^content-length'`.

`content-length` harus sama dengan `size` di `update.json`; sha256 berkas lokal harus sama dengan `sha256` di JSON.

**Larangan:** jangan menaruh kode sumber, `.env`, atau kredensial di repo ini; jangan menimpa aset rilis yang sudah dipakai klien — terbitkan rilis baru.

Kode sumber tidak ada di repo ini. Lisensi aplikasi diatur di EULA yang disertakan saat pembelian: satu lisensi untuk satu resto, aktivasi offline per mesin.
