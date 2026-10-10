# TU API — Production Final Baseline

Tanggal: 2026-10-10
Status: PRODUKSI STABIL — TARGET V5 AKTIF

## 1. Konfigurasi Produksi
- Worker produksi: `tu-p3hpl-proxy`
- Worker production active/latest version yang terlihat saat finalisasi: `4e82247a`
- Endpoint produksi: `https://tu-p3hpl-proxy.asterales-niza.workers.dev/tu`
- TARGET produksi diarahkan ke deployment Apps Script V5.
- Tidak ada perubahan pada data TU selama tahap finalisasi.

## 2. Hasil Uji Produksi Read-Only

### tu_monitoring FULL 2026
- HTTP: 200
- Waktu: 4,94 detik
- Detail: 192
- Status: LULUS

### tu_monitoring FILTER — TU-2026-000356
- HTTP: 200
- Data acuan:
  - Pagu Revisi: 600
  - Realisasi Final: 0
  - Total RPD: 100
  - Sisa RPD: 500
- Status: LULUS

### tu_realisasi_list
- HTTP: 200
- Waktu: 2,12 detik
- Status: LULUS
- Record acuan: RTU-2026-000001
- Nominal: 100
- Bulan: 10
- Status record: NONAKTIF

### tu_rpd_list
- HTTP: 200
- Waktu: 2,66 detik
- Status: LULUS
- Pagu Revisi: 600
- Realisasi Final: 0
- Dana Tersedia: 600
- Total RPD: 100
- Sisa RPD: 500

### tu_revisi_list
- HTTP: 200
- Waktu: 2,66 detik
- Status: LULUS
- Record acuan: REV-TU-2026-000001
- Pagu Lama: 500
- Pagu Baru: 600
- Selisih: 100
- Status: AKTIF

## 3. Security Response Check Produksi
- `tu_bootstrap`: HTTP 200, sekitar 3,00 detik, sensitive fields tidak terdeteksi.
- `tu_monitoring` FILTER: HTTP 200, sekitar 12,48 detik, sensitive fields tidak terdeteksi.
- Pemeriksaan mencari field `id_token`, `access_token`, `refresh_token`, dan `token`.
- Nilai token tidak dicatat di dokumen ini.

## 4. Perbandingan Performa
Sebelum migrasi:
- Worker produksi + TARGET lama untuk `tu_monitoring FULL`: sekitar 101–120 detik.

Sesudah migrasi:
- Worker produksi + TARGET V5 untuk `tu_monitoring FULL`: 4,94 detik.
- Detail tetap 192.

## 5. Status Final
- Apps Script V5: LULUS
- Worker V5 staging: LULUS
- Worker produksi → Apps Script V5: LULUS
- Monitoring FULL: LULUS
- Monitoring FILTER: LULUS
- Read-only endpoints: LULUS
- Security response check: LULUS

## 6. Aturan Operasional Setelah Finalisasi
1. Jangan melakukan SAVE, UPDATE, DELETE, RPD SAVE, atau REVISI SAVE untuk pengujian rutin.
2. Jangan mengubah kode Worker produksi tanpa baseline dan rencana rollback.
3. Jangan mencatat atau menyimpan `id_token` atau token autentikasi.
4. Gunakan endpoint read-only untuk pemeriksaan kesehatan.
5. Pertahankan versi Worker produksi saat ini sebagai baseline stabil.
6. Jika ada perubahan berikutnya, buat Worker staging/versi baru dan uji sebelum mengganti produksi.

## 7. Rollback
Rollback dilakukan dengan mengaktifkan kembali versi Worker produksi sebelumnya melalui mekanisme deployment Cloudflare. Tidak diperlukan perubahan pada data TU untuk rollback konfigurasi proxy.

## 8. Catatan
Dokumen ini adalah baseline operasional setelah migrasi TARGET ke Apps Script V5. Angka waktu dapat berfluktuasi antar-request; kriteria utama adalah HTTP 200, integritas data, dan tidak adanya field token sensitif pada response.
