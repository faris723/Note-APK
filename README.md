# Catatan Pintar — Offline

Aplikasi catatan pintar & manajemen keuangan lokal-pertama (offline-first) untuk Android (APK) dan Web (PWA).

## Isi paket ini
Paket ini sudah dilengkapi `.github/workflows/` yang sebelumnya **tidak ikut** ter-export ke file ZIP deploy, sehingga proses build APK & GitHub Release tidak bisa berjalan otomatis di repo lama. Sudah diperbaiki di sini.

## Langkah membuat repository GitHub baru

1. Buat repository baru di GitHub (kosong, tanpa README/gitignore bawaan GitHub).
2. Upload/push seluruh isi folder ini ke repository tersebut (lewat web upload, GitHub Desktop, atau `git push`).
3. Buka tab **Settings → Pages** di repo, pada "Build and deployment" pilih Source: **GitHub Actions** (supaya workflow `deploy.yml` bisa publish).
4. Buka tab **Actions** → pastikan workflow diizinkan berjalan (kadang GitHub minta konfirmasi pertama kali untuk repo baru).

## Cara build APK (tanpa perlu command line)

1. Buka tab **Actions** di repo Anda.
2. Pilih workflow **"Build Android APK (Aplikasi Mandiri Tanpa Browser)"**.
3. Klik tombol **"Run workflow"**.
4. Untuk build biasa (hanya artifact, tidak jadi Release resmi): biarkan kolom `release_tag` kosong.
5. **Untuk merilis versi resmi yang bisa dideteksi fitur "Cek Update" di dalam aplikasi**: isi kolom `release_tag` dengan nomor versi diawali huruf `v`, misalnya `v1.0.1`. Workflow otomatis akan:
   - Build APK
   - Membuat tag Git `v1.0.1`
   - Membuat GitHub Release resmi + melampirkan file `.apk`

Setelah selesai (±2-3 menit), cek tab **Releases** di repo — APK dan catatan rilis akan muncul di sana.

## ⚠️ PENTING setiap kali merilis versi baru
Fitur cek-update (`src/modules/update.js`) membandingkan versi lewat 3 tempat yang **HARUS selalu disamakan**:

| Lokasi | Contoh nilai untuk rilis v1.0.2 |
|---|---|
| `src/version.js` → `APP_VERSION` | `'1.0.2'` |
| `android/app/build.gradle` → `versionName` | `"1.0.2"` |
| `android/app/build.gradle` → `versionCode` | naikkan angkanya, misal dari `2` jadi `3` |
| Tag/`release_tag` saat menjalankan workflow | `v1.0.2` |

Kalau `versionCode` lupa dinaikkan, Android akan menolak pasang APK baru menimpa yang lama (`INSTALL_FAILED_VERSION_DOWNGRADE`) — baru di titik itu user perlu uninstall manual.

## Kenapa tidak perlu uninstall aplikasi lama saat update APK
- `applicationId` selalu sama: `com.catatanpintar.app`
- Signing key debug selalu sama, dikunci lewat `android/app/debug.keystore` yang di-commit ke repo (lihat komentar di `build.gradle`)

Selama dua hal di atas tidak berubah dan `versionCode` selalu naik tiap rilis, APK baru akan **menimpa (update) instalasi lama** tanpa perlu uninstall.
