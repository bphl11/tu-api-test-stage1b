# TU API — Baseline Final V5 Staging

Tanggal: 2026-10-09
Status: STAGING LULUS — PRODUKSI BELUM DIUBAH

## Scope
Baseline ini mencatat hasil pengujian read-only sebelum perubahan TARGET pada Worker produksi.

## Deployment
- Worker produksi: `tu-p3hpl-proxy`
- Worker staging V5: `tu-p3hpl-proxy-v5-staging`
- Apps Script V5 staging: deployment TU API — STAGING OPTIMIZED V5 — monitoring only
- Worker produksi saat ini tetap menggunakan TARGET lama.
- Worker V5 staging menggunakan TARGET Apps Script V5.

## TARGET
TARGET lama (Worker produksi):
`https://script.google.com/macros/s/AKfycbF1gDWXYubrCZEaJ1bccaJpTLJSv4DULNmqk-N3IfsxS1UfnTC9T73qhXqNvBJkmSrdw/exec`

TARGET V5 (Worker staging):
`https://script.google.com/macros/s/AKfycbLfDwll2EOs6fbUTZ3a7asENik4cjes_WCRqdCwN5gVTH5B5wM5l60NOIZOIbr7JjnjQ/exec`

## Backend V5 direct
- tu_bootstrap: ~5.34 detik, HTTP 200
- tu_monitoring FILTER TU-2026-000356: ~4.70–5.96 detik, HTTP 200
- tu_monitoring FULL 2026: ~4.01 detik, HTTP 200, 192 detail
- Data acuan filter:
  - Pagu Revisi: 600
  - Realisasi Final: 0
  - Total RPD: 100
  - Sisa RPD: 500

## Worker V5 staging
- tu_monitoring FULL 2026: 3.89 detik
- HTTP: 200
- Detail: 192
- Response: ok=true

## Security response check
- tu_bootstrap: HTTP 200, ~4.02 detik, sensitive fields: none
- tu_monitoring FILTER: HTTP 200, ~4.65 detik, sensitive fields: none
- Tidak ditemukan field id_token/access_token/refresh_token/token pada response yang diperiksa.
- Nilai token tidak dicatat dalam baseline.

## Read-only endpoints via Worker V5 staging
Target: Tahun 2026, ID TU TU-2026-000356

### tu_realisasi_list
- HTTP 200
- 2.90 detik
- LULUS
- RTU-2026-000001
- Nominal 100
- Bulan 10
- Status NONAKTIF

### tu_rpd_list
- HTTP 200
- 4.41 detik
- LULUS
- Pagu Revisi 600
- Realisasi Final 0
- Dana Tersedia 600
- Total RPD 100
- Sisa RPD 500

### tu_revisi_list
- HTTP 200
- 2.37 detik
- LULUS
- REV-TU-2026-000001
- Pagu Lama 500
- Pagu Baru 600
- Selisih 100
- Status AKTIF

## Performance diagnosis
- Worker produksi sebelumnya: tu_monitoring FULL sekitar 101.57–120.45 detik, HTTP 200.
- Worker produksi / jalur lama berhasil mengembalikan 192 detail tetapi lambat.
- Worker V5 staging + Apps Script V5: FULL 3.89 detik dan 192 detail.
- Kesimpulan staging: V5 mempertahankan hasil dan memangkas bottleneck monitoring FULL.

## Production change gate
JANGAN ubah Worker produksi sampai baseline ini tersimpan.

Perubahan produksi yang direncanakan:
- Tidak mengubah Worker logic.
- Tidak mengubah CORS.
- Tidak mengubah endpoint /tu.
- Tidak mengubah data TU.
- Hanya mengganti konstanta TARGET pada Worker produksi dari TARGET lama ke TARGET V5.

Setelah perubahan produksi, wajib uji:
1. tu_monitoring FILTER TU-2026-000356
2. tu_monitoring FULL 2026
3. tu_realisasi_list
4. tu_rpd_list
5. tu_revisi_list

Kriteria minimum:
- HTTP 200
- FULL = 192 detail
- Filter = 600 / 0 / 100 / 500
- Read-only endpoints tetap LULUS
- Tidak ada sensitive token field pada response.

## Safety
- Jangan mencatat atau menyimpan id_token.
- Worker produksi tetap dianggap belum berubah sampai deployment produksi benar-benar diperbarui.
