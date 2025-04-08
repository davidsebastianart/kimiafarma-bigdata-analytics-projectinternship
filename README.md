# Kimiafarma-projectinternship

## 📊 Project Overview

Proyek ini merupakan bagian dari internship project untuk menganalisis **kinerja bisnis Kimia Farma** selama tahun 2020 hingga 2023, menggunakan **BigQuery** dan divisualisasikan melalui **Google Looker Studio**. Fokus analisis mencakup:
- Pendapatan (nett sales)
- Laba (nett profit)
- Rating cabang
- Performa penjualan produk

## 🏢 About Kimia Farma
Kimia Farma adalah perusahaan farmasi tertua di Asia Tenggara yang berdiri sejak tahun 1817. Perusahaan ini memiliki jaringan luas yang terdiri dari apotek, klinik, dan laboratorium klinik di seluruh Indonesia.

## 🎯 Problem Statement
Bagaimana menganalisis performa operasional Kimia Farma berdasarkan transaksi penjualan, produk, inventaris, dan cabang selama 2020–2023 untuk mendukung pengambilan keputusan bisnis yang lebih baik?

## 🧩 Project Steps

### 1. Importing Dataset to BigQuery
Empat dataset diimpor ke BigQuery dan disimpan dalam dataset `kimia_farma`:
- `kf_final_transaction.csv`
- `kf_product.csv`
- `kf_kantor_cabang.csv`
- `kf_inventory.csv`

Semua tabel menggunakan schema auto-detect untuk mempermudah proses.

### 2. Data Preparation (SQL in BigQuery)

Penggabungan data dilakukan untuk menghasilkan metrik seperti **nett_sales**, **nett_profit**, dan **gross_profit_percentage**.

```sql
# Membuat atau mengganti tabel bernama 'analysis_table' di dataset kimia_farma
CREATE OR REPLACE TABLE `rakamin-kf-analytics-455410.kimia_farma.analysis_table` AS

# Memilih kolom-kolom yang ingin dimasukkan ke dalam tabel baru
SELECT
  t.transaction_id,         # ID transaksi
  t.date,                   # Tanggal transaksi
  c.branch_id,              # ID cabang
  c.branch_name,            # Nama cabang
  c.kota,                   # Kota cabang
  c.provinsi,               # Provinsi cabang
  c.rating AS rating_cabang, # Rating cabang dari data kantor cabang
  t.customer_name,          # Nama pelanggan
  p.product_id,             # ID produk
  p.product_name,           # Nama produk
  p.price AS actual_price,  # Harga asli produk
  t.discount_percentage,    # Persentase diskon dari transaksi

  # Menghitung persentase estimasi gross laba berdasarkan harga produk
  CASE
    WHEN p.price <= 50000 THEN 0.10
    WHEN p.price <= 100000 THEN 0.15
    WHEN p.price <= 300000 THEN 0.20
    WHEN p.price <= 500000 THEN 0.25
    ELSE 0.30
  END AS persentase_gross_laba,

  # Menghitung nett_sales (penjualan bersih setelah diskon)
  p.price * (1 - t.discount_percentage/100) AS nett_sales,

  # Menghitung nett_profit (keuntungan bersih = nett_sales * margin laba)
  p.price * (1 - t.discount_percentage/100) *
    CASE
      WHEN p.price <= 50000 THEN 0.10
      WHEN p.price <= 100000 THEN 0.15
      WHEN p.price <= 300000 THEN 0.20
      WHEN p.price <= 500000 THEN 0.25
      ELSE 0.30
    END AS nett_profit,

  t.rating AS rating_transaksi  # Rating dari transaksi (kemungkinan dari customer)

# Mengambil data dari tabel transaksi akhir
FROM `rakamin-kf-analytics-455410.kimia_farma.kf_final_transaction` AS t

# Menggabungkan data produk berdasarkan product_id
LEFT JOIN `rakamin-kf-analytics-455410.kimia_farma.kf_product` AS p
  ON t.product_id = p.product_id

# Menggabungkan data kantor cabang berdasarkan branch_id
LEFT JOIN `rakamin-kf-analytics-455410.kimia_farma.kf_kantor_cabang` AS c
  ON t.branch_id = c.branch_id;
```

### 3. Sample Output

```sql
-- Menampilkan 10 data pertama dari analysis_table
SELECT *
FROM kimia_farma.analysis_table
LIMIT 10;
```

## 📈 Dashboard

Visualisasi performa dilakukan menggunakan **Google Looker Studio** untuk menampilkan:
- Tren pendapatan dan laba dari tahun ke tahun
- Cabang dengan rating tinggi namun transaksi rendah
- Distribusi keuntungan antar provinsi
- Dampak strategi harga dan diskon terhadap penjualan

## 🧠 Insights & Recommendations

**Insight**:
- Terjadi penurunan laba di beberapa tahun, kemungkinan akibat pandemi.
- Cabang dengan rating tinggi belum tentu memiliki volume transaksi besar.
- Ketimpangan distribusi keuntungan antar wilayah.

**Recommendation**:
- Meningkatkan transaksi di cabang dengan rating tinggi.
- Memperbaiki strategi distribusi dan promosi di wilayah kurang menguntungkan.
- Menerapkan analisis lanjutan untuk segmentasi produk & pelanggan.

## 🔗 Resources

- 📊 [Link Presentation](http://google.com/)
- 💻 [Link GitHub Repository](https://github.com/davidsebastianart/Kimiafarma-projectinternship/)
- 👨‍💻 Author: David Sebastian Aritonang  
  📧 davidsebastianartt@gmail.com  
  🌐 [LinkedIn](https://www.linkedin.com/in/david-sartt/)
