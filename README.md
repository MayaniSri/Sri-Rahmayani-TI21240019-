# Sistem Manajemen Rental Kendaraan

REST API dan GraphQL untuk relasi `Pelanggan`, `Kendaraan`, dan `Penyewaan`.
Database lokal menggunakan SQLite dan Prisma.

## Menjalankan proyek

```powershell
Copy-Item .env.example .env
```

Ubah `NIM_SUFFIX` di `.env` menjadi 3 digit terakhir NIM Anda. Kemudian:

```powershell
npm install
npm run db:setup
npm run dev
```

`db:setup` membuat database SQLite dan data contoh. Seed saat ini menggunakan
nama `Maya 019`, email `maya.019@example.test`, plat `DR 019 SR`, serta biaya
sewa `500019` dan `750019`. Server berjalan di port `3000` (atau nilai `PORT`
pada `.env`).

## Endpoint REST

| Method | URI | Fungsi |
|---|---|---|
| GET | `/api/penyewaan` | Mengambil daftar penyewaan beserta pelanggan dan kendaraan |
| GET | `/api/penyewaan/:id` | Mengambil detail satu transaksi beserta relasinya |
| POST | `/api/penyewaan` | Membuat transaksi penyewaan |
| DELETE | `/api/penyewaan/:id` | Menghapus transaksi penyewaan |

Contoh body `POST /api/penyewaan`:

```json
{
  "pelangganId": 1,
  "kendaraanId": 1,
  "totalBiaya": 500019,
  "tanggalSewa": "2026-10-12T00:00:00.000Z"
}
```

Body POST harus berisi `pelangganId` dan `kendaraanId` berupa angka yang sudah
ada di database. Jangan kirim objek `pelanggan` atau `kendaraan` pada body;
keduanya hanya muncul sebagai relasi di respons. Ambil ID dari
`GET /api/penyewaan`. DTO memvalidasi ID sebagai bilangan bulat positif,
`totalBiaya` sebagai bilangan bulat minimal 0, dan `tanggalSewa` sebagai
tanggal ISO 8601. Properti tambahan ditolak.

## GraphQL Apollo Sandbox

Buka `http://localhost:3000/graphql` di browser. Query seluruh transaksi:

```graphql
query {
  penyewaan {
    tanggalSewa
    totalBiaya
    pelanggan {
      nama
    }
    kendaraan {
      merk
      platNomor
    }
  }
}
```

Query detail transaksi:

```graphql
query {
  detailPenyewaan(id: 1) {
    tanggalSewa
    totalBiaya
    pelanggan {
      nama
    }
    kendaraan {
      merk
      model
      platNomor
    }
  }
}
```

## Bukti pengujian

Screenshot Postman/browser dan Apollo Sandbox perlu diambil setelah server
dijalankan. Sebelum mengambil screenshot, pastikan `NIM_SUFFIX` sudah diubah
ke 3 digit NIM Anda agar suffix tampak pada JSON yang ditampilkan.

Untuk konfigurasi Postman siap pakai, impor file
`Postman Rental API.postman_collection.json` melalui **Import**. Jalankan
request **List penyewaan (GET)** terlebih dahulu agar ID pelanggan dan kendaraan
terisi otomatis, lalu jalankan request lain. Pastikan server berjalan dengan
`npm run dev`. URL variabel `baseURL` adalah `http://localhost:3000` (tanpa
akhiran `/graphql`); endpoint REST dan GraphQL masing-masing ditambahkan pada
URL request.
