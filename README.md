# Kedaiin — feed pembaruan

Repo publik ini **hanya** berisi kabar versi dan paket pembaruan aplikasi **Kedaiin POS**.
Ini **bukan** kode sumber: kode aplikasi ada di repo privat `LIinz27/Kedaiin` dan tidak dibuka untuk pembeli.
Tidak ada `.env`, kredensial, atau data resto di repo ini — yang bisa diunduh di sini hanya paket yang sudah jadi.

| Berkas / lokasi | Isi |
|---|---|
| `update.json` | feed versi: versi terbaru, build, tanggal, ukuran, sha256, URL paket, `min_from_version` |
| `releases/download/<tag>/kedaiin-update-<versi>.zip` | paket pembaruan (aset rilis) |

## 1. Apa repo ini

Server Kedaiin di PC resto membaca feed ini untuk menjawab tombol **Pengaturan → Pembaruan → Cek versi terbaru**:

```text
https://raw.githubusercontent.com/LIinz27/Kedaiin-release/main/update.json
```

- **Tanpa token, tanpa login** — berkas publik, hanya dibaca.
- **Internet hanya dipakai saat tombol itu ditekan.** Resto tetap jalan penuh tanpa internet; feed yang tidak bisa dihubungi bukan kerusakan aplikasi, hanya berarti cek versi gagal.
- Sejak 1.2.3 paket yang lolos pemeriksaan bisa **dipasang dari layar** **Pengaturan → Pembaruan** oleh owner (§5.4). Tidak ada unduhan latar belakang dan tidak ada pemasangan tanpa perintah: paket tetap harus diunggah, disimpan, lalu dipasang dengan konfirmasi **PASANG**.

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

Server menolak paket yang isinya tidak cocok dengan `manifest.json` di dalam zip. `size` dan `sha256` di feed dipakai memeriksa berkas yang **diunduh**, bukan untuk memasang.

## 2. Rilis terbaru — 1.2.4
| | |
|---|---|
| Versi | **1.2.4** |
| Build | **de4b184** |
| Dirilis | **2026-11-26 12:04 UTC** (26 Nov 2026, 20:04 WITA) |
| Ukuran paket | **13.894.558 byte** (13,2 MB) |
| SHA-256 | `8a7d9e63bc528e0ea05624d64425ed883807a474a23ec06ec8947515f46c33f7` |
| `min_from_version` | **1.1.0** |
| Unduhan | [kedaiin-update-1.2.4.zip](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.4/kedaiin-update-1.2.4.zip) |
| Catatan rilis | [§9.1](#91-rilis-124-26-november-2026) — cetak tiket dari perangkat stasiun, Bluetooth dapur/bar boleh dipilih di layar, APK tablet mencetak BLE tanpa Chrome |

**Arti `min_from_version` 1.1.0:** instalasi **1.1.0 ke atas** boleh langsung memakai paket ini. Instalasi di bawah 1.1.0 naik lewat installer versi baru, bukan paket zip.

## 3. Riwayat rilis
| Versi | Build | Dirilis (UTC) | Ukuran | SHA-256 | Paket |
|---|---|---|---|---|---|
| **1.2.4** | `de4b184` | 2026-11-26 12:04 | 13.894.558 | `8a7d9e63bc528e0ea05624d64425ed883807a474a23ec06ec8947515f46c33f7` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.4/kedaiin-update-1.2.4.zip) |
| **1.2.3** | `ab81cf5` | 2026-11-26 08:16 | 13.849.080 | `d92143d08bb1a4627b4e0dd0371e8cac64e9f805626182a83aaf33aa75571dde` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.3/kedaiin-update-1.2.3.zip) |
| **1.2.2** | `fd0c5dc` | 2026-11-26 05:32 | 13.843.666 | `c8c2f5256191db685aca809ddfe7f84dd26f6571dee19daae82bed05680a4de0` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.2/kedaiin-update-1.2.2.zip) |
| **1.2.1** | `3d6ebf1` | 2026-11-20 14:39 | 13.847.165 | `2acfc02874d59884a84f98caacbd4a7dc3c1e0e842fbeaec9bd1bc6b31a25f21` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.1/kedaiin-update-1.2.1.zip) |
| **1.2.0** | `ca21782` | 2026-11-20 00:00 | 13.837.631 | `cc38b5da656d6d3b8eedbd04bb2ce7e7b555726a284d45435c5b5b8c4d5d050d` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.0/kedaiin-update-1.2.0.zip) |

Rilis 1.2.0 berisi sistem desain baru dan tujuh layar yang didesain ulang. Rilis 1.2.1 berisi perbaikan uang, perbaikan cetak, dan alat pembaruan itu sendiri (§9.4). Rilis 1.2.2 berisi perbaikan laporan, plafon angka, dan aksesibilitas tab Laporan (§9.3). Rilis 1.2.3 berisi pemasangan pembaruan dari layar Pengaturan plus rollback otomatis ke versi lama (§9.2). Rilis 1.2.4 memindahkan pencetakan tiket ke perangkat stasiun — Bluetooth dapur/bar boleh dipilih, dan APK tablet mencetak BLE tanpa Chrome (§9.1). Daftar lengkap: [halaman Releases](https://github.com/LIinz27/Kedaiin-release/releases).

## 4. Isi paket pembaruan
Zip berisi dua hal di root: `manifest.json` (versi, build, daftar berkas + ukuran + sha256 tiap berkas) dan folder `app/` (kode server + build web + dependensi, siap salin).

Paket 1.2.4 bila dibuka: **1.953 berkas** + `manifest.json`, **29.254.064 byte** setelah diekstrak (±27,9 MB; 13,2 MB terkompresi). `manifest.json` sendiri 353.428 byte.

| Bagian | Saat paket dipasang |
|---|---|
| Kode server (`apps/server`) + build web (`apps/web/dist`) + `node_modules` | diganti |
| `data\` (database SQLite, unggahan, paket pending) | **tidak disentuh** — semua transaksi resto ada di sini |
| `backup\` | **tidak disentuh** — cadangan harian 03:00 |
| `.env` | **tidak disentuh** — kunci acak instalasi |
| `logs\` | **tidak disentuh** — riwayat untuk penelusuran |
| Runtime Node + pm2 | **tidak ikut** — paket update hanya kode aplikasi; runtime urusan installer |
| Berkas dev (`app/.opencode/`) | ikut terbawa di paket 1.2.4 (5 berkas; `.tmp-verify/` tidak ada); tidak dipakai saat aplikasi jalan, bersihkan di paket berikutnya |

## 5. Cara memasang pembaruan
Dua jalur, isinya sama: paket `.zip` diverifikasi di dalam aplikasi, disimpan, lalu dipasang — **mulai 1.2.3 bisa dari layar Pembaruan sendiri** (§5.4), atau lewat skrip teknisi.

### 5.1 Jalur A — dari dalam aplikasi (server punya internet)
1. Unduh `kedaiin-update-1.2.4.zip` dari tautan §2 — boleh di PC mana pun, boleh langsung di PC server.
2. Verifikasi checksum-nya (§6). **Jangan lewati langkah ini.**
3. Di PC server buka `http://<ip-server>:3000` (atau `Kedaiin.exe`), login sebagai **owner** atau **admin**.
4. **Pengaturan** → gulir ke paling bawah → seksi **Pembaruan**.
5. **Pilih paket pembaruan…** → pilih berkas `.zip`. Berkas langsung diperiksa terhadap `manifest.json` di dalamnya: tiap berkas harus ada, ukurannya sama, sha256-nya sama. Bila lolos, layar menulis **Paket 1.2.4 (build de4b184) lolos verifikasi** beserta jumlah berkas dan ukurannya. Bila gagal, muncul daftar masalah: `hilang: …`, `ekstra: …`, `ukuran beda: …`, `sha256 beda: …`.
6. **Simpan untuk dipasang** — tombol ini hanya hidup untuk **owner**. Paket ditolak kalau masih ada bill terbuka atau shift kasir masih berjalan.
7. Muncul kotak **Pembaruan 1.2.4 siap dipasang** beserta tombol **Batalkan**. Paket tersimpan di `C:\kedaiin\data\updates\pending`.
8. **Pasang sekarang** → **Lanjut** → ketik **PASANG** → **Pasang sekarang** (§5.4). Batas ukuran berkas: **80 MB**; selain `.zip` tidak bisa dipilih.

### 5.2 Jalur B — offline / USB (server tanpa internet)
1. Unduh paket di komputer lain yang ada internet, verifikasi checksum (§6).
2. Salin `.zip` ke flashdisk, pindahkan ke PC server.
3. Langkahnya sama dengan jalur A poin 3–7: **Pengaturan → Pembaruan → Pilih paket pembaruan…**
4. Teknisi memasang seperti §5.4.

Jalur ini dipakai kalau **Cek versi terbaru** menjawab **Feed pembaruan belum tersedia — pakai paket dari USB** atau **Tidak bisa menghubungi server pembaruan**.

### 5.3 Syarat sebelum menyimpan paket
| Syarat | Kalau belum terpenuhi |
|---|---|
| Tidak ada bill terbuka | layar menolak: **Tidak bisa menyimpan paket: N bill masih terbuka.** Selesaikan atau void dulu |
| Shift kasir sudah ditutup | layar menolak: **shift kasir masih berjalan** |
| Login sebagai owner | admin bisa memeriksa dan cek versi, tetapi menyimpan paket hanya owner |
| Berkas `.zip` ≤ 80 MB | berkas lain tidak bisa dipilih |

Syarat ini ada karena pemasangan menutup aplikasi sebentar. Menyimpan paket di tengah jam ramai berarti teknisi akan menutup kasir saat ada tamu.

### 5.4 Memasang paket — mulai 1.2.3 dari layar Pembaruan
**Menutup lalu membuka aplikasi belum memasang apa pun.** Sejak **1.2.3** paket yang sudah tersimpan bisa dipasang langsung dari layar, oleh **owner**. Pemasangan tetap butuh **akses Windows dan PowerShell** di PC server: aplikasi memicu skrip, bukan menyalin berkas sendiri.

**a. Dari layar.** **Pasang sekarang** → **Lanjut** → ketik **PASANG** → **Pasang sekarang**. Kasir berhenti **±1–3 menit**; jangan tutup jendela atau matikan PC server. Enam tahap terlihat di layar: snapshot versi lama → salin berkas → pasang dependensi → build web → nyalakan server → cek `/health`. Status tiap tahap ditulis ke `logs\upgrade-state.json`.

Pemasangan **ditolak** kalau masih ada bill terbuka, shift kasir berjalan, versi terpasang lebih tua dari `min_from_version`, atau ada pemasangan lain yang sedang berjalan. Kalau `/health` tidak cocok dalam **60 detik**, sistem kembali sendiri ke versi lama (§5.5). `data\`, `backup\`, `.env`, `logs\`, dan runtime Node/pm2 tidak pernah disentuh — yang diganti hanya kode aplikasi.

**b. Skrip upgrade (kalau layar tidak bisa dipakai).** Ekstrak zip di PC server, lalu:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass `
  -File C:\kedaiin\deploy\windows\upgrade-kedaiin.ps1 `
  -SourceDir C:\kedaiin-baru\app
```

Urutan kerja skrip: backup dulu → salin berkas versi baru → `npm ci` → build web → `pm2 restart kedaiin-pos` → cek `/health`. `data\`, `backup\`, `.env`, `logs\`, `.pm2` tidak pernah ditimpa.

**c. Installer versi baru** (`Kedaiin-Setup-1.2.4.exe`, disimpan lokal oleh penjual, tidak di repo ini; checksumnya di §6). Dipasang **di atas** instalasi lama; folder data, backup, log, dan `.env` dikecualikan. Pakai cara ini kalau runtime Node/WebView2 ikut berubah, atau saat pasang dari nol.

Sesudah salah satu cara di atas, `curl.exe http://localhost:3000/health` harus menjawab `{"ok":true,"build":"de4b184","storage":"ok"}`. Kalau `build` masih `ab81cf5`, pemasangan belum terjadi.

### 5.5 Kalau versi baru bermasalah (rollback)
**Otomatis:** sebelum menyalin berkas baru, pemasangan membuat snapshot `app.prev-<versi>`. Kalau `/health` tidak cocok dalam 60 detik, server dikembalikan ke snapshot itu. Panel merah di layar Pembaruan juga menampilkan tombol **Kembalikan ke versi sebelumnya** (aktif kalau snapshot ada).

**Manual** di PC server, dari folder instalasi (mis. `C:\kedaiin`):

```powershell
powershell -NoProfile -ExecutionPolicy Bypass `
  -File .\rollback-kedaiin.ps1 -InstallDir C:\kedaiin
```

Skrip memulihkan kode dari `app.prev-<versi>`, menyalakan server, lalu memeriksa `/health` maksimal 60 detik. Aman dijalankan dua kali; tanpa snapshot tidak ada yang dihapus. Rollback tidak menimpa data; pulihkan database dari backup **hanya** kalau datanya memang rusak.

## 6. Cara memverifikasi berkas
Checksum memastikan berkas yang Anda pegang sama persis dengan yang diterbitkan. Kalau berbeda, berhenti — jangan dipasang.

**Linux / macOS:**

```bash
curl -L -o kedaiin-update-1.2.4.zip \
  https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.4/kedaiin-update-1.2.4.zip
sha256sum kedaiin-update-1.2.4.zip
```

**Windows (PowerShell):**

```powershell
curl.exe -L -o kedaiin-update-1.2.4.zip `
  https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.4/kedaiin-update-1.2.4.zip
Get-FileHash .\kedaiin-update-1.2.4.zip -Algorithm SHA256
```

| Berkas | SHA-256 |
|---|---|
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

APK juga punya MD5 `a2d12439954c1ae215e5d2710332fc3e` — berguna kalau tablet hanya menyediakan alat MD5 (`Get-FileHash .\kedaiin-pos.apk -Algorithm MD5`).

Installer **tidak** diunggah ke repo ini — minta ke penjual; versi 1.2.4 ukurannya 334.813.849 byte (full) dan 42.319.547 byte (slim); versi 1.2.3: 334.774.515 byte dan 42.279.430 byte; versi 1.2.2: 334.755.715 byte dan 42.261.460 byte.

Ukuran berkas harus sama dengan `size` di `update.json` (13.894.558 byte untuk 1.2.4). Ukuran beda = unduhan terpotong, ulangi.

## 7. Persyaratan server
| Perlu | Keterangan |
|---|---|
| Windows **10/11 64-bit** | Windows 7/8 tidak didukung |
| RAM 4 GB (8 GB nyaman), disk kosong ±1 GB | PC server biasanya juga dipakai kasir |
| **Node 24** | dibawa installer — tidak perlu memasang Node sendiri. Paket update **tidak** membawa runtime |
| WebView2 | dibawa installer, dipasang otomatis kalau belum ada |
| Port **3000** TCP | dibuka installer di firewall; klien mengakses `http://<ip-server>:3000` |
| IP tetap untuk PC server | mis. `192.168.1.10`; kalau IP berubah, semua klien kehilangan sambungan |
| Printer thermal **58 mm ESC/POS** | kasir: LAN / USB / Bluetooth |
| **Dapur & bar: printer LAN `ip:9100` (disarankan)** | sejak 1.2.4 **Bluetooth** boleh dipilih untuk dapur/bar, tetapi hanya jalan kalau stasiun itu punya **perangkat terdaftar yang menyala** (tablet/PC dengan halaman aplikasi terbuka); printer USB di dapur/bar tidak akan jalan |
| Lisensi **satu per resto** | aktivasi offline: layar **Aktivasi Lisensi** menampilkan **Kode Minta** → penjual mengirim **Kode Aktivasi** |

Satu resto = satu PC server. PC itu menyimpan seluruh data dan melayani PC/tablet lain di jaringan resto.

## 8. Klien
| Klien | Apa itu | Kapan dipakai |
|---|---|---|
| **`Kedaiin.exe`** (PC) | pembungkus Tauri + WebView2; UI tetap diambil dari server | PC kasir kedua, PC pemilik |
| **APK tablet** | aplikasi Android `id.kedaiin.pos` untuk pelayan | tablet pelayan (Wi-Fi jaringan resto) |

**`Kedaiin.exe`** dipasang installer di PC; ikon desktop membuka layar resto tanpa address bar browser. Alamat server diisi saat pemasangan mode **Klien** (mis. `http://192.168.1.10:3000`). Kalau PC hanya dipakai sesekali, cukup buka alamat server di browser — bedanya hanya pembungkus jendela.

**APK tablet** dilayani dari server resto, **bukan** dari repo ini: `http://<ip-server>:3000/apk` — halaman itu menampilkan QR code dan tombol **Unduh APK**. Berkas 1.2.4:

| | |
|---|---|
| Berkas | `kedaiin-pos.apk` |
| Ukuran | **17.522.866 byte** |
| MD5 | `a2d12439954c1ae215e5d2710332fc3e` |
| SHA-256 | `0596fdfc076dfff5e7eaf8f2407aefc97a77d55c45393a53927d123956744d6b` |
| `versionName` / `versionCode` | **1.2.4** / **5** |
| Package | `id.kedaiin.pos` |

**APK harus di-build ulang setiap rilis web.** Memperbarui server **tidak** memperbarui APK yang sudah terpasang di tablet: berkas APK baru disalin ke server (menggantikan `kedaiin-pos.apk`), lalu tablet memasang ulang lewat `/apk`. Kalau berkas APK di server diganti, checksum di atas **tidak berlaku lagi** — hitung ulang dari berkas yang benar-benar dilayani server.

## 9. Catatan rilis

### 9.1 Rilis 1.2.4 (26 November 2026)
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

**Yang berubah bagi pengguna:** printer Bluetooth di dapur/bar kini bisa dipakai — asal stasiun itu punya perangkat terdaftar yang menyala; printer LAN tetap disarankan. Agen cetak hanya jalan selama halaman aplikasi terbuka, jadi job menunggu kalau tablet mati. Tablet harus dipasang ulang APK-nya supaya ikut 1.2.4 dan bisa mencetak BLE (§8) — memperbarui server tidak memperbarui APK yang sudah terpasang.

### 9.2 Rilis 1.2.3 (26 November 2026)
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

**Yang berubah bagi pengguna:** pemilik resto bisa memasang paket sendiri dari layar Pembaruan — asal PC server punya akses PowerShell; kalau tidak, jalur skrip/installer di §5.4 tetap ada. Tablet harus dipasang ulang APK-nya supaya ikut 1.2.3 (§8) — memperbarui server tidak memperbarui APK yang sudah terpasang.

### 9.3 Rilis 1.2.2 (26 November 2026)
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

**Yang berubah bagi pengguna:** tab Laporan dipindah dengan panah kiri/kanan, Home/End; kartu **Tidak terjual** kini di Ringkasan, bukan lagi di bawah tab Laba. Tablet harus dipasang ulang APK-nya supaya ikut 1.2.2 (§8) — memperbarui server tidak memperbarui APK yang sudah terpasang.

### 9.4 Rilis 1.2.1 (20 November 2026)
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

Batas yang berlaku untuk 1.2.1 dan 1.2.2: pemasangan pembaruan **belum otomatis** — paket hanya diperiksa dan disimpan. Pemasangan dari layar baru ada di 1.2.3 (§5.4).

## 10. Dukungan
| | |
|---|---|
| WhatsApp | **0895-4152-04242** — [wa.me/62895415204242](https://wa.me/62895415204242) |
| Jam kerja | **09.00–17.00 WITA** |

Saat melapor bug, sertakan empat hal ini supaya masalah bisa ditelusuri tanpa bolak-balik — dan jangan kirim database atau `.env` lewat WhatsApp:

1. **Versi & build** — baris pertama seksi **Pengaturan → Pembaruan** (contoh: *Versi terpasang 1.2.4 · build de4b184*).
2. **Keluaran `/health`** di PC server — `curl.exe http://localhost:3000/health` (PowerShell 5.1: pakai `curl.exe`, bukan `curl`). Jawabannya memuat `build` (commit yang sedang jalan) dan `storage` (`ok` = folder data bisa ditulis).
3. **Log server**, 30 baris terakhir: `$env:PM2_HOME='C:\kedaiin\.pm2'` lalu `pm2 logs kedaiin-pos --lines 30`.
4. **Apa yang dilakukan sebelum masalah muncul** — tombol yang ditekan, pesan di layar (screenshot), dan jam kejadian.

## 11. FAQ
**Apakah butuh internet?**
Tidak. Kasir, meja, cetak, laporan, dan shift jalan penuh tanpa internet. Internet hanya dipakai saat menekan **Cek versi terbaru** dan saat mengunduh paket.

**Apakah data hilang saat update?**
Tidak. Paket hanya mengganti kode aplikasi; `data\`, `backup\`, `.env`, dan `logs\` tidak disentuh. Skrip upgrade membuat backup dulu sebelum menyalin apa pun.

**Kenapa tampilan di tablet tidak berubah setelah update?**
Karena APK adalah aplikasi terpisah. Update server tidak menyentuh APK yang sudah terpasang — APK baru harus di-build, disalin ke server, lalu dipasang ulang dari `/apk` (§8).

**Bagaimana kalau update gagal?**
Paket yang tidak lolos pemeriksaan **tidak disimpan** dan server tetap di versi lama. Kalau gagal saat pemasangan, skrip upgrade menghidupkan lagi versi lama dan menulis **Data TIDAK rusak**; mulai 1.2.3 rollback juga berjalan sendiri kalau `/health` tidak cocok dalam 60 detik (§5.5).

**Apakah update berjalan sendiri setelah aplikasi ditutup?**
Tidak — dan itu disengaja. Mulai 1.2.3 paket bisa dipasang dari layar Pembaruan, tetapi tetap harus ditekan owner (**Pasang sekarang** → ketik **PASANG**), dan hanya kalau tidak ada bill terbuka atau shift berjalan (§5.4). Tidak ada unduhan atau pemasangan di latar belakang.

## 12. Untuk penjual: menerbitkan rilis berikutnya
Dijalankan di mesin build, di repo kode privat (bukan di repo ini): build paket dari **commit rilis** (bukan `main` terbaru kalau rilis ditag lebih dulu) → verifikasi dengan `verify-update-package.sh` sampai status `OK` → catat ukuran zip dan `sha256sum` → isi `latest` di `update.json` dan geser entri lama ke `history` → unggah aset ke rilis baru (tag `v<versi>`) → periksa dari luar: `curl -s https://raw.githubusercontent.com/LIinz27/Kedaiin-release/main/update.json` dan `curl -sIL <url-aset-rilis-baru> | grep -i '^content-length'`.

`content-length` harus sama dengan `size` di `update.json`; sha256 berkas lokal harus sama dengan `sha256` di JSON.

**Larangan:** jangan menaruh kode sumber, `.env`, atau kredensial di repo ini; jangan menimpa aset rilis yang sudah dipakai klien — terbitkan rilis baru.

Kode sumber tidak ada di repo ini. Lisensi aplikasi diatur di EULA yang disertakan saat pembelian: satu lisensi untuk satu resto, aktivasi offline per mesin.
