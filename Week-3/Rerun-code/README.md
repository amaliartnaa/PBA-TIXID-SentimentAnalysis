## 📊 Dataset
 
- **Sumber**: Google Play Store — Aplikasi TIX ID (`id.tix.android`)
- **Bahasa review**: Indonesia (`lang='id'`, `country='id'`)
- **Metode pengambilan**: Semua ulasan terbaru (`Sort.NEWEST`)
- **Total Review**: **96.275 ulasan**
- **Kolom utama**:
  - `content` — isi teks ulasan
  - `score` — rating bintang (1–5)
  - `thumbsUpCount` — jumlah like pada review
  - `at` — tanggal ulasan ditulis
  - `replyContent` — balasan dari developer
  - `repliedAt` — tanggal balasan developer
  - `reviewCreatedVersion` — versi aplikasi saat ulasan ditulis
 
---
 
## 🔍 Alur Analisis
 
### 1. Pengambilan Data (Scraping)
Review diambil menggunakan `reviews_all()` dari `google-play-scraper`, kemudian disimpan ke file CSV (`tix_reviews_raw.csv`) sebagai backup data mentah.
 
### 2. Eksplorasi Awal Data
Dataset memiliki **96.275 baris** dan **11 kolom**. Beberapa kolom seperti `reviewCreatedVersion` dan `replyContent` memiliki nilai kosong (null), yang wajar karena tidak semua review mendapat balasan dari developer.
 
### 3. Distribusi Rating ⭐
Dari analisis distribusi rating:
 
| Rating | Jumlah Review |
|--------|--------------|
| ⭐⭐⭐⭐⭐ (5) | 73.921 |
| ⭐⭐⭐⭐ (4) | 9.255 |
| ⭐ (1) | 7.591 |
| ⭐⭐⭐ (3) | 3.498 |
| ⭐⭐ (2) | 2.010 |
 
**Insight**: Mayoritas pengguna (sekitar **76,8%**) memberikan rating bintang 5, menandakan tingkat kepuasan yang sangat tinggi secara umum. Namun ada juga sekitar **7,9%** pengguna yang memberikan bintang 1, yang menjadi catatan penting untuk perbaikan.
 
### 4. Labeling Sentimen Berdasarkan Rating
Review diklasifikasikan menjadi tiga kategori sentimen:
- **Positive**: Rating > 3 (bintang 4 dan 5)
- **Neutral**: Rating = 3 (bintang 3)
- **Negative**: Rating < 3 (bintang 1 dan 2)
 
### 5. Analisis Sentimen dengan TextBlob
Setiap review dianalisis menggunakan `TextBlob` untuk mendapatkan:
- **Polarity**: Nilai dari -1 (sangat negatif) hingga +1 (sangat positif)
- **Subjectivity**: Nilai dari 0 (objektif) hingga 1 (subjektif)
 
> ⚠️ **Catatan**: TextBlob didesain untuk Bahasa Inggris, sehingga banyak review Bahasa Indonesia menghasilkan polarity = 0. Ini adalah keterbatasan yang umum dan dapat diatasi dengan menggunakan model NLP khusus Bahasa Indonesia seperti IndoBERT di analisis lanjutan.
 
### 6. Feature Engineering Teks
Beberapa fitur tambahan dihitung dari teks review:
- `wordCount` — jumlah kata per review
- `uppercaseCharCount` — jumlah huruf kapital (indikator emosi tinggi)
- `specialCharCount` — jumlah karakter khusus/tanda baca
 
### 7. Preprocessing Teks
Teks review dibersihkan melalui beberapa tahap:
1. **Lowercasing** — mengubah semua teks menjadi huruf kecil
2. **Stopword Removal (Indonesia)** — menggunakan Sastrawi untuk menghapus kata umum Bahasa Indonesia
3. **Stopword Removal (English)** — menggunakan NLTK untuk menghapus kata umum Bahasa Inggris (198 kata)
 
### 8. Analisis Frekuensi Kata
Kata-kata yang paling sering muncul dianalisis sebelum dan sesudah proses stopword removal, menggunakan `FreqDist` dari NLTK dan divisualisasikan dalam bar chart (top 30 kata).
 
---
 
## 📈 Visualisasi yang Dihasilkan
 
1. **Histogram Distribusi Rating** — Menampilkan sebaran rating 1–5 dari seluruh pengguna
2. **Scatter Plot Sentimen** — Menampilkan hubungan antara polarity dan subjectivity, dibedakan berdasarkan kategori sentimen (Positive/Neutral/Negative)
3. **Bar Chart Frekuensi Kata (sebelum stopword removal)** — Top 30 kata yang paling sering muncul
4. **Bar Chart Frekuensi Kata (setelah stopword removal)** — Top 30 kata bermakna yang paling sering muncul setelah kata umum dihapus
 
---
 
## 💡 Kesimpulan & Insight
 
1. **TIX ID sangat disukai penggunanya** — Lebih dari 76% pengguna memberikan rating tertinggi (5 bintang), menandakan kepuasan tinggi terhadap fitur pembelian tiket, pemilihan kursi, dan kemudahan penggunaan aplikasi.
 
2. **Ada keluhan yang perlu diperhatikan** — Sekitar 9,9% pengguna memberikan rating 1–2. Dari konten review negatif, isu yang sering muncul antara lain masalah login, perubahan kata sandi, dan fitur yang diharapkan belum tersedia (seperti sinopsis film).
 
3. **Developer aktif merespons** — Sekitar 49.893 dari 96.275 ulasan (±52%) mendapat balasan dari developer TIX ID, menunjukkan tim yang responsif terhadap pengguna.
 
4. **Keterbatasan analisis sentimen TextBlob** — Karena TextBlob berbasis Bahasa Inggris, hasil polarity banyak yang bernilai 0 untuk teks Bahasa Indonesia. Untuk analisis yang lebih akurat, disarankan menggunakan model seperti **IndoBERT** atau **VADER**.
 
5. **Kata kunci dominan** — Setelah stopword removal, kata-kata seperti *film*, *tiket*, *beli*, *bayar*, *seat*, *mudah*, *aplikasi*, dan *keren* mendominasi, yang mencerminkan fitur inti aplikasi dan kesan positif pengguna.
 
---