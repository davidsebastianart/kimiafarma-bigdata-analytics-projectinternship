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
-- Membuat tabel analisis performa penjualan dan cabang
CREATE OR REPLACE TABLE kimia_farma.analysis_table AS
SELECT
  t.transaction_id,
  p.product_name,
  c.branch_name,
  t.actual_price,
  t.discount_percentage,
  t.rating_cabang,
  (t.actual_price - (t.actual_price * t.discount_percentage / 100)) AS nett_sales,
  (t.actual_price - (t.actual_price * t.discount_percentage / 100) - p.product_cost) AS nett_profit,
  ROUND(((t.actual_price - (t.actual_price * t.discount_percentage / 100) - p.product_cost) / 
         (t.actual_price - (t.actual_price * t.discount_percentage / 100))) * 100, 2) AS gross_profit_percentage
FROM
  kimia_farma.kf_final_transaction t
JOIN
  kimia_farma.kf_product p ON t.product_id = p.product_id
JOIN
  kimia_farma.kf_kantor_cabang c ON t.branch_id = c.branch_id;
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
- 💻 [Link GitHub Repository](http://google.com/)
- 👨‍💻 Author: David Sebastian Aritonang  
  📧 davidsebastianartt@gmail.com  
  🌐 [LinkedIn](https://www.linkedin.com/in/david-sartt/)
