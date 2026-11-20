# Kedaiin — feed pembaruan

Repo publik ini **hanya berisi metadata dan paket update** aplikasi Kedaiin.
Tidak ada kode sumber di sini. Kode aplikasi ada di repo privat terpisah.

## Isi

| Berkas | Fungsi |
|---|---|
| `update.json` | Feed yang dibaca klien Kedaiin untuk cek versi terbaru |
| `releases/download/<tag>/kedaiin-update-<versi>.zip` | Paket update (aset rilis) |

Klien membaca:

```
https://raw.githubusercontent.com/LIinz27/Kedaiin-release/main/update.json
```

Struktur `update.json`:

```json
{
  "schema": 1,
  "latest": {
    "version": "1.2.0",
    "build": "ca21782",
    "released_at": "2026-11-20T00:00:00Z",
    "size": 13837631,
    "sha256": "cc38b5da656d6d3b8eedbd04bb2ce7e7b555726a284d45435c5b5b8c4d5d050d",
    "url": "https://github.com/LIinz27/Kedaiin-release/releases/download/v1.2.0/kedaiin-update-1.2.0.zip",
    "notes": "Sistem desain baru, 7 layar redesain",
    "min_from_version": "1.1.0"
  },
  "history": []
}
```

- `size` = ukuran zip dalam byte, `sha256` = checksum zip. Klien menolak paket yang
  tidak cocok dengan keduanya.
- `min_from_version` = versi terpasang tertua yang boleh melompat langsung ke versi ini.
- `url` harus URL unduhan langsung (release asset publik), bukan halaman HTML.

## Paket update

Zip berisi `manifest.json` (versi, build, sha256 tiap berkas) di root + folder `app/`.
Runtime (Node portable, WebView2) tidak ikut — hanya kode aplikasi.

## Cara membuat rilis berikutnya

Dijalankan di mesin build (VM103), di repo privat `Poskafe`.

1. **Build paket** dari commit rilis (bukan `main` terbaru kalau rilis di-tag sebelumnya):

   ```bash
   git worktree add --detach /tmp/wt <commit>   # atau: git checkout <commit> di repo bersih
   cd /tmp/wt
   # payload/app harus hasil build commit itu: jalankan deploy/windows/make-payload.sh
   deploy/windows/make-update-package.sh --version <versi> --out /tmp/up
   ```

2. **Verifikasi** — wajib `status OK`:

   ```bash
   deploy/windows/verify-update-package.sh /tmp/up/kedaiin-update-<versi>.zip
   ```

   Catat `stat -c %s` (ukuran zip) dan `sha256sum` (checksum zip).

3. **Bump `update.json`**: isi `latest` dengan versi/build/ukuran/sha256/url baru,
   pindahkan entri lama ke elemen pertama array `history`. Commit + push ke `main`.

4. **Upload aset** ke release baru di repo ini:

   ```bash
   gh release create v<versi> /tmp/up/kedaiin-update-<versi>.zip \
     -R LIinz27/Kedaiin-release --title "Kedaiin <versi>" --notes "<ringkasan>"
   ```

5. **Verifikasi publik** (jangan lewatkan):

   ```bash
   curl -s https://raw.githubusercontent.com/LIinz27/Kedaiin-release/main/update.json
   curl -sIL https://github.com/LIinz27/Kedaiin-release/releases/download/v<versi>/kedaiin-update-<versi>.zip | grep -i '^content-length'
   ```

   `content-length` harus sama dengan `size` di `update.json`; `sha256sum` file lokal
   harus sama dengan `sha256` di JSON.

## Larangan

- Jangan menaruh kode sumber, `.env`, atau kredensial di repo ini.
- Jangan menimpa aset rilis yang sudah dipakai klien — buat rilis baru.
