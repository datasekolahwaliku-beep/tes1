# Sistem Pelaporan Kinerja Terpadu

Aplikasi web satu-halaman (single-file) untuk laporan kinerja sekolah, memakai
**repositori GitHub sebagai "server"** — data disimpan dan diperbarui langsung
di berkas `data.json` melalui GitHub Contents API.

## Isi paket

| Berkas       | Fungsi                                                             |
|--------------|---------------------------------------------------------------------|
| `index.html` | Seluruh aplikasi (tampilan + logika). Cukup 1 berkas ini yang dibuka. |
| `data.json`  | Data awal (dummy): akun pengguna & contoh laporan.                  |

## Alur pelaporan

```
Guru BK          ─┐
Organisasi/OSIS  ─┴─► Wakasek Kesiswaan ─┐
                                          │
Wakasek Kesiswaan ─┐                     │
Kurikulum         ─┤                     │
Humas             ─┼──► Kepala Sekolah ──┼──► Pengawas
Sarana Prasarana  ─┤                     │
Tata Usaha        ─┘                     │
                                          │
Kepala Sekolah ───────────────────────────┴──► Pengawas
```

- Laporan hanya bisa **diubah/dihapus oleh pemiliknya** (yang membuat laporan).
- Pihak yang menerima laporan **hanya bisa melihat** (tidak bisa mengedit).

## Cara memasang ke GitHub (langkah demi langkah)

1. **Buat repositori baru** di GitHub (boleh publik atau privat), misalnya
   bernama `laporan-sekolah`.
2. **Unggah** kedua berkas `index.html` dan `data.json` ke repositori tersebut
   (lewat menu *Add file → Upload files* di GitHub, atau `git push`).
3. **Aktifkan GitHub Pages** (opsional, agar bisa diakses lewat tautan web):
   - Buka *Settings → Pages*
   - Pilih *Deploy from branch* → branch `main` → folder `/root`
   - Simpan. Setelah beberapa menit, aplikasi bisa diakses di
     `https://<nama-pengguna>.github.io/<nama-repo>/`
4. **Buat Personal Access Token (PAT)** agar aplikasi bisa **menyimpan**
   perubahan (menambah/mengubah/menghapus laporan) ke repositori:
   - Buka https://github.com/settings/tokens → *Generate new token
     (classic)*
   - Beri centang scope **`repo`** (untuk repo privat) atau minimal
     **`public_repo`** (untuk repo publik)
   - Salin token yang dihasilkan (hanya tampil sekali)
5. **Buka aplikasi** (`index.html`), klik ikon ⚙️ **Pengaturan Server** di
   halaman login, lalu isi:
   - **Pemilik/Organisasi**: nama pengguna GitHub Anda
   - **Nama Repositori**: `laporan-sekolah` (sesuai nama repo Anda)
   - **Branch**: `main`
   - **Path Berkas Data**: `data.json`
   - **Personal Access Token**: tempel token dari langkah 4
   - Klik **Simpan & Hubungkan**

Setelah terhubung, semua tambah/ubah/hapus laporan akan langsung membuat
*commit* baru pada `data.json` di repositori Anda — data benar-benar
tersimpan di GitHub, bukan hanya di peramban.

## Mode Lokal (tanpa GitHub)

Jika Pengaturan Server tidak diisi, aplikasi tetap bisa dicoba langsung
(`index.html` dibuka apa adanya di peramban) menggunakan data contoh
bawaan yang disimpan di `localStorage` peramban. Cocok untuk uji coba
cepat, tapi data **tidak tersinkronisasi** antar perangkat/pengguna lain.

## Akun demo (kata sandi semua akun: `sekolah123`)

| Nama pengguna       | Peran                |
|---------------------|----------------------|
| budi.bk             | Guru BK              |
| siti.osis           | Organisasi (OSIS)    |
| andi.wakasek        | Wakasek Kesiswaan    |
| rina.kurikulum      | Wakasek Kurikulum    |
| dedi.humas          | Wakasek Humas        |
| wati.sarpras        | Sarana Prasarana     |
| joko.tu             | Tata Usaha           |
| hendra.kepsek       | Kepala Sekolah       |
| sri.pengawas        | Pengawas Sekolah     |

**Catatan keamanan:** ini adalah aplikasi statis tanpa server backend
sungguhan — kata sandi disimpan apa adanya di `data.json` dan token GitHub
tersimpan di `localStorage` peramban pengguna. Cukup aman untuk penggunaan
internal/terbatas, tapi untuk penggunaan produksi yang lebih luas sebaiknya
diganti dengan backend + autentikasi yang semestinya.

## Tentang "realtime"

GitHub tidak menyediakan push notification/WebSocket, sehingga "realtime" di
sini diwujudkan dengan:
- Memuat ulang data setiap kali login dan setiap menekan tombol **Muat Ulang
  Data**.
- Pembaruan otomatis di latar belakang setiap ± 25 detik saat tidak ada
  modal yang sedang terbuka.
