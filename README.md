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
- Aplikasi **tidak** memasang pembaruan sendiri dari repo ini: paket diunduh, diperiksa, disimpan, lalu dipasang teknisi (§5.4).

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

## 2. Rilis terbaru — 1.2.1
| | |
|---|---|
| Versi | **1.2.1** |
| Build | **3d6ebf1** |
| Dirilis | **2026-11-20 14:39 UTC** (20 Nov 2026, 22:39 WITA) |
| Ukuran paket | **13.847.165 byte** (13,2 MB) |
| SHA-256 | `2acfc02874d59884a84f98caacbd4a7dc3c1e0e842fbeaec9bd1bc6b31a25f21` |
| `min_from_version` | **1.1.0** |
| Unduhan | [kedaiin-update-1.2.1.zip](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.1/kedaiin-update-1.2.1.zip) |

**Arti `min_from_version` 1.1.0:** instalasi **1.1.0 ke atas** boleh langsung memakai paket ini. Instalasi di bawah 1.1.0 naik lewat installer versi baru, bukan paket zip.

## 3. Riwayat rilis
| Versi | Build | Dirilis (UTC) | Ukuran | SHA-256 | Paket |
|---|---|---|---|---|---|
| **1.2.2** | `fd0c5dc` | 2026-11-26 05:32 | 13.843.666 | `c8c2f5256191db685aca809ddfe7f84dd26f6571dee19daae82bed05680a4de0` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.2/kedaiin-update-1.2.2.zip) |
| **1.2.1** | `3d6ebf1` | 2026-11-20 14:39 | 13.847.165 | `2acfc02874d59884a84f98caacbd4a7dc3c1e0e842fbeaec9bd1bc6b31a25f21` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.1/kedaiin-update-1.2.1.zip) |
| **1.2.0** | `ca21782` | 2026-11-20 00:00 | 13.837.631 | `cc38b5da656d6d3b8eedbd04bb2ce7e7b555726a284d45435c5b5b8c4d5d050d` | [unduh](https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.0/kedaiin-update-1.2.0.zip) |

Rilis 1.2.0 berisi sistem desain baru dan tujuh layar yang didesain ulang. Rilis 1.2.1 berisi perbaikan uang, perbaikan cetak, dan alat pembaruan itu sendiri (§9). Daftar lengkap: [halaman Releases](https://github.com/LIinz27/Kedaiin-release/releases).

## 4. Isi paket pembaruan
Zip berisi dua hal di root: `manifest.json` (versi, build, daftar berkas + ukuran + sha256 tiap berkas) dan folder `app/` (kode server + build web + dependensi, siap salin).

Paket 1.2.1 bila dibuka: **1.973 berkas**, **±27,8 MB** setelah diekstrak (13,2 MB terkompresi).

| Bagian | Saat paket dipasang |
|---|---|
| Kode server (`apps/server`) + build web (`apps/web/dist`) + `node_modules` | diganti |
| `data\` (database SQLite, unggahan, paket pending) | **tidak disentuh** — semua transaksi resto ada di sini |
| `backup\` | **tidak disentuh** — cadangan harian 03:00 |
| `.env` | **tidak disentuh** — kunci acak instalasi |
| `logs\` | **tidak disentuh** — riwayat untuk penelusuran |
| Runtime Node + pm2 | **tidak ikut** — paket update hanya kode aplikasi; runtime urusan installer |
| Berkas dev (`app/.tmp-verify/`, `app/.opencode/`) | ikut terbawa di paket 1.2.1; tidak dipakai saat aplikasi jalan, bersihkan di paket berikutnya |

## 5. Cara memasang pembaruan
Dua jalur, isinya sama: paket `.zip` diverifikasi di dalam aplikasi, disimpan, lalu dipasang oleh teknisi.

### 5.1 Jalur A — dari dalam aplikasi (server punya internet)
1. Unduh `kedaiin-update-1.2.1.zip` dari tautan §2 — boleh di PC mana pun, boleh langsung di PC server.
2. Verifikasi checksum-nya (§6). **Jangan lewati langkah ini.**
3. Di PC server buka `http://<ip-server>:3000` (atau `Kedaiin.exe`), login sebagai **owner** atau **admin**.
4. **Pengaturan** → gulir ke paling bawah → seksi **Pembaruan**.
5. **Pilih paket pembaruan…** → pilih berkas `.zip`. Berkas langsung diperiksa terhadap `manifest.json` di dalamnya: tiap berkas harus ada, ukurannya sama, sha256-nya sama. Bila lolos, layar menulis **Paket 1.2.1 (build 3d6ebf1) lolos verifikasi** beserta jumlah berkas dan ukurannya. Bila gagal, muncul daftar masalah: `hilang: …`, `ekstra: …`, `ukuran beda: …`, `sha256 beda: …`.
6. **Simpan untuk dipasang** — tombol ini hanya hidup untuk **owner**. Paket ditolak kalau masih ada bill terbuka atau shift kasir masih berjalan.
7. Muncul kotak **Pembaruan 1.2.1 siap dipasang** beserta tombol **Batalkan**. Paket tersimpan di `C:\kedaiin\data\updates\pending`.
8. Pemasangan dilakukan teknisi (§5.4). Batas ukuran berkas: **80 MB**; selain `.zip` tidak bisa dipilih.

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

### 5.4 Setelah paket tersimpan — pemasangan tetap oleh teknisi
**Menutup lalu membuka aplikasi belum memasang apa pun.** Layar Pembaruan sendiri menulis **Pemasangan otomatis belum aktif (tahap berikutnya)**. Paket hanya **tersimpan**.

**a. Skrip upgrade.** Ekstrak zip di PC server, lalu:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass `
  -File C:\kedaiin\deploy\windows\upgrade-kedaiin.ps1 `
  -SourceDir C:\kedaiin-baru\app
```

Urutan kerja skrip: backup dulu → salin berkas versi baru → `npm ci` → build web → `pm2 restart kedaiin-pos` → cek `/health`. `data\`, `backup\`, `.env`, `logs\`, `.pm2` tidak pernah ditimpa.

**b. Installer versi baru** (`Kedaiin-Setup-1.2.1.exe`, disimpan lokal oleh penjual, tidak di repo ini). Dipasang **di atas** instalasi lama; folder data, backup, log, dan `.env` dikecualikan. Pakai cara ini kalau runtime Node/WebView2 ikut berubah, atau saat pasang dari nol.

Sesudah salah satu cara di atas, `curl.exe http://localhost:3000/health` harus menjawab `{"ok":true,"build":"3d6ebf1","storage":"ok"}`. Kalau `build` masih `ca21782`, pemasangan belum terjadi.

### 5.5 Kalau versi baru bermasalah (rollback)
```powershell
powershell -NoProfile -ExecutionPolicy Bypass `
  -File C:\kedaiin\deploy\windows\upgrade-kedaiin.ps1 `
  -SourceDir C:\kedaiin-lama\app
```

Simpan folder versi lama sebelum menimpa. Rollback tidak menimpa data; pulihkan database dari backup **hanya** kalau datanya memang rusak.

## 6. Cara memverifikasi berkas
Checksum memastikan berkas yang Anda pegang sama persis dengan yang diterbitkan. Kalau berbeda, berhenti — jangan dipasang.

**Linux / macOS:**

```bash
curl -L -o kedaiin-update-1.2.1.zip \
  https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.1/kedaiin-update-1.2.1.zip
sha256sum kedaiin-update-1.2.1.zip
```

**Windows (PowerShell):**

```powershell
curl.exe -L -o kedaiin-update-1.2.1.zip `
  https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.1/kedaiin-update-1.2.1.zip
Get-FileHash .\kedaiin-update-1.2.1.zip -Algorithm SHA256
```

| Berkas | SHA-256 |
|---|---|
| `kedaiin-update-1.2.1.zip` | `2acfc02874d59884a84f98caacbd4a7dc3c1e0e842fbeaec9bd1bc6b31a25f21` |
| `kedaiin-update-1.2.0.zip` | `cc38b5da656d6d3b8eedbd04bb2ce7e7b555726a284d45435c5b5b8c4d5d050d` |
| `kedaiin-pos.apk` (1.2.1) | `043b649e3611b33e0677a48ac77c016e0e4e28d8ce586bc3abe5e54b8c29dd2e` |

APK juga punya MD5 `64d6fa14c7dd3e5fbcaee0c435757d74` — berguna kalau tablet hanya menyediakan alat MD5 (`Get-FileHash .\kedaiin-pos.apk -Algorithm MD5`).

Ukuran berkas harus sama dengan `size` di `update.json` (13.847.165 byte untuk 1.2.1). Ukuran beda = unduhan terpotong, ulangi.

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
| **Dapur & bar: wajib printer LAN `ip:9100`** | di dapur/bar tidak ada PC yang bisa mencetak; printer USB di sana tidak akan jalan |
| Lisensi **satu per resto** | aktivasi offline: layar **Aktivasi Lisensi** menampilkan **Kode Minta** → penjual mengirim **Kode Aktivasi** |

Satu resto = satu PC server. PC itu menyimpan seluruh data dan melayani PC/tablet lain di jaringan resto.

## 8. Klien
| Klien | Apa itu | Kapan dipakai |
|---|---|---|
| **`Kedaiin.exe`** (PC) | pembungkus Tauri + WebView2; UI tetap diambil dari server | PC kasir kedua, PC pemilik |
| **APK tablet** | aplikasi Android `id.kedaiin.pos` untuk pelayan | tablet pelayan (Wi-Fi jaringan resto) |

**`Kedaiin.exe`** dipasang installer di PC; ikon desktop membuka layar resto tanpa address bar browser. Alamat server diisi saat pemasangan mode **Klien** (mis. `http://192.168.1.10:3000`). Kalau PC hanya dipakai sesekali, cukup buka alamat server di browser — bedanya hanya pembungkus jendela.

**APK tablet** dilayani dari server resto, **bukan** dari repo ini: `http://<ip-server>:3000/apk` — halaman itu menampilkan QR code dan tombol **Unduh APK**. Berkas 1.2.1:

| | |
|---|---|
| Berkas | `kedaiin-pos.apk` |
| Ukuran | **17.484.554 byte** |
| MD5 | `64d6fa14c7dd3e5fbcaee0c435757d74` |
| SHA-256 | `043b649e3611b33e0677a48ac77c016e0e4e28d8ce586bc3abe5e54b8c29dd2e` |
| `versionName` / `versionCode` | **1.2.1** / **2** |
| Package | `id.kedaiin.pos` |

**APK harus di-build ulang setiap rilis web.** Memperbarui server **tidak** memperbarui APK yang sudah terpasang di tablet: berkas APK baru disalin ke server (menggantikan `kedaiin-pos.apk`), lalu tablet memasang ulang lewat `/apk`. Kalau berkas APK di server diganti, checksum di atas **tidak berlaku lagi** — hitung ulang dari berkas yang benar-benar dilayani server.

## 9. Catatan rilis 1.2.1
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

Batas yang masih berlaku dan perlu diketahui pembeli: pemasangan pembaruan **belum otomatis** — paket hanya diperiksa dan disimpan (§5.4).

## 10. Dukungan
| | |
|---|---|
| WhatsApp | **0895-4152-04242** — [wa.me/62895415204242](https://wa.me/62895415204242) |
| Jam kerja | **09.00–17.00 WITA** |

Saat melapor bug, sertakan empat hal ini supaya masalah bisa ditelusuri tanpa bolak-balik — dan jangan kirim database atau `.env` lewat WhatsApp:

1. **Versi & build** — baris pertama seksi **Pengaturan → Pembaruan** (contoh: *Versi terpasang 1.2.1 · build 3d6ebf1*).
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
Paket yang tidak lolos pemeriksaan **tidak disimpan** dan server tetap di versi lama. Kalau gagal saat pemasangan, skrip upgrade menghidupkan lagi versi lama dan menulis **Data TIDAK rusak**; rollback ada di §5.5.

**Apakah update berjalan sendiri setelah aplikasi ditutup?**
Belum. Paket hanya tersimpan; pemasangan dilakukan teknisi (§5.4). Layar Pembaruan menulis sendiri **Pemasangan otomatis belum aktif (tahap berikutnya)**.

## 12. Untuk penjual: menerbitkan rilis berikutnya
Dijalankan di mesin build, di repo kode privat (bukan di repo ini): build paket dari **commit rilis** (bukan `main` terbaru kalau rilis ditag lebih dulu) → verifikasi dengan `verify-update-package.sh` sampai status `OK` → catat ukuran zip dan `sha256sum` → isi `latest` di `update.json` dan geser entri lama ke `history` → unggah aset ke rilis baru (tag `v<versi>`) → periksa dari luar: `curl -s https://raw.githubusercontent.com/LIinz27/Kedaiin-release/main/update.json` dan `curl -sIL <url-aset-rilis-baru> | grep -i '^content-length'`.

`content-length` harus sama dengan `size` di `update.json`; sha256 berkas lokal harus sama dengan `sha256` di JSON.

**Larangan:** jangan menaruh kode sumber, `.env`, atau kredensial di repo ini; jangan menimpa aset rilis yang sudah dipakai klien — terbitkan rilis baru.

Kode sumber tidak ada di repo ini. Lisensi aplikasi diatur di EULA yang disertakan saat pembelian: satu lisensi untuk satu resto, aktivasi offline per mesin.
