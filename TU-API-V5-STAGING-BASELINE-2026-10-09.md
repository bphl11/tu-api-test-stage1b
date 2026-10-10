# TU API — FINAL PRODUCTION STABILITY BASELINE

Tanggal: 2026-10-10
Status: **PRODUKSI STABIL — TARGET V5 AKTIF**

## 1. Konfigurasi final

- Worker produksi: `tu-p3hpl-proxy`
- Worker produksi aktif: **versi/hash 4e82247a** (sesuai deployment yang diuji)
- Endpoint produksi: `https://tu-p3hpl-proxy.asterales-niza.workers.dev/tu`
- Target Apps Script aktif: deployment **TU API V5**
- Worker staging V5: `tu-p3hpl-proxy-v5-staging`
- Worker produksi telah dialihkan dari TARGET lama ke TARGET Apps Script V5.
- Tidak ada perubahan pada endpoint `/tu`, CORS, format request, atau data TU selain pergantian TARGET.

## 2. Hasil utama produksi

### tu_monitoring FULL 2026
- HTTP: **200**
- Waktu: **4,94 detik**
- Jumlah detail: **192**
- `ok=true`
- Struktur response tetap tersedia.

### tu_monitoring FILTER — TU-2026-000356
- HTTP: **200**
- Data sesuai baseline:
  - Pagu Revisi: **600**
  - Realisasi Final: **0**
  - Total RPD: **100**
  - Sisa RPD: **500**

## 3. Read-only endpoint produksi

### tu_realisasi_list
- HTTP: **200**
- Waktu: **2,12 detik**
- Status: **LULUS**
- Record acuan: `RTU-2026-000001`
- Nominal: **100**
- Bulan: **10**
- Status data: **NONAKTIF**

### tu_rpd_list
- HTTP: **200**
- Waktu: **2,66 detik**
- Status: **LULUS**
- Pagu Revisi: **600**
- Realisasi Final: **0**
- Dana Tersedia: **600**
- Total RPD: **100**
- Sisa RPD: **500**

### tu_revisi_list
- HTTP: **200**
- Waktu: **2,66 detik**
- Status: **LULUS**
- Record acuan: `REV-TU-2026-000001`
- Pagu Lama: **500**
- Pagu Baru: **600**
- Selisih: **100**
- Status: **AKTIF**

## 4. Security response

Pemeriksaan langsung pada Worker produksi:
- `tu_bootstrap`: HTTP **200**, sensitive fields **TIDAK TERDETEKSI**
- `tu_monitoring`: HTTP **200**, sensitive fields **TIDAK TERDETEKSI**
- Tidak ditemukan field `id_token`, `access_token`, `refresh_token`, atau `token` pada response yang diperiksa.
- Nilai token tidak disimpan dalam baseline.

## 5. Perbandingan sebelum dan sesudah

### Sebelum
Worker produksi + TARGET lama:
- `tu_monitoring FULL`: sekitar **101,57–120,45 detik**
- Pada pengujian sebelumnya dapat mengalami HTTP 524.

### Sesudah
Worker produksi + TARGET V5:
- `tu_monitoring FULL`: **4,94 detik**
- HTTP **200**
- **192 detail**

Perubahan menunjukkan bottleneck utama berhasil diatasi oleh backend V5 dan jalur produksi sekarang kembali berada pada kisaran beberapa detik.

## 6. Status akhir

- Backend V5: **LULUS**
- Worker V5 staging: **LULUS**
- Worker produksi → Apps Script V5: **LULUS**
- Monitoring FULL 192 detail: **LULUS**
- Monitoring FILTER: **LULUS**
- Realisasi list: **LULUS**
- RPD list: **LULUS**
- Revisi list: **LULUS**
- Security response: **LULUS**

## 7. Kebijakan operasional setelah go-live

1. **Jangan mengubah kode Worker produksi** kecuali ada kebutuhan yang terdokumentasi.
2. **Jangan menjalankan SAVE/UPDATE/DELETE untuk pengujian rutin.**
3. Pengujian rutin gunakan endpoint **read-only**.
4. Jangan mencatat, menyimpan, atau menampilkan `id_token`.
5. Simpan Worker produksi saat ini sebagai **versi stabil/reference version**.
6. Setiap perubahan berikutnya lakukan melalui **staging terlebih dahulu**, lalu uji, dokumentasikan, dan baru pertimbangkan produksi.
7. Jangan menghapus baseline ini; gunakan sebagai pembanding jika performa kembali menurun.

## 8. Catatan

Baseline ini menggantikan status sebelumnya yang masih menyatakan produksi belum diubah. Dokumen ini menjadi acuan operasional setelah migrasi TARGET ke Apps Script V5 selesai.

