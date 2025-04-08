# Kimiafarma-projectinternship

Proyek ini bertujuan untuk menganalisis kinerja bisnis Kimia Farma selama tahun 2020 hingga 2023. Data yang tersedia mencakup transaksi penjualan, informasi produk, inventarisasi, serta data cabang.

## SQL Query

```SQL
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
