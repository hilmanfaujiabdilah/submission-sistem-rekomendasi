# Laporan Proyek Machine Learning - Hilman Fauji

## Project Overview

Di era digital saat ini, jumlah konten yang tersedia bagi pengguna telah meledak. Platform streaming seperti Netflix, Amazon Prime, dan Disney+ memiliki puluhan ribu judul film dan serial TV. Kondisi ini menciptakan sebuah tantangan yang dikenal sebagai information overload atau "beban informasi", di mana pengguna kesulitan untuk menemukan konten yang relevan dan menarik di antara lautan pilihan yang ada. Tanpa bantuan, pengguna dapat menghabiskan waktu lebih banyak untuk mencari tontonan daripada menikmatinya, yang pada akhirnya dapat menyebabkan frustrasi dan penurunan minat terhadap platform tersebut.

Proyek ini penting untuk diselesaikan karena sistem rekomendasi yang efektif secara langsung mengatasi masalah beban informasi tersebut. Dengan menganalisis preferensi pengguna dan karakteristik konten, sistem ini dapat menyajikan daftar film yang dipersonalisasi dan kemungkinan besar akan disukai oleh pengguna. Hal ini tidak hanya meningkatkan pengalaman dan kepuasan pengguna, tetapi juga memberikan nilai bisnis yang signifikan bagi penyedia layanan. Menurut riset dari McKinsey, 75% dari apa yang ditonton pengguna di Netflix berasal dari rekomendasi produk [1]. Selain itu, diperkirakan sistem personalisasi ini menghemat lebih dari $1 miliar per tahun karena mampu meningkatkan retensi pelanggan [2].

Oleh karena itu, membangun sistem rekomendasi film yang andal adalah solusi strategis untuk meningkatkan engagement, loyalitas, dan retensi pengguna pada sebuah platform digital.

## Business Understanding

### Problem Statements

Berdasarkan latar belakang yang telah diuraikan, masalah utama yang ingin diselesaikan adalah sebagai berikut:
- Pengguna kesulitan menemukan film yang sesuai dengan selera mereka di tengah katalog yang sangat besar, yang menyebabkan pengalaman pengguna yang kurang memuaskan.
- Penyedia platform berisiko kehilangan pengguna (churn) jika pengguna secara konsisten gagal menemukan konten yang menarik, sehingga mengurangi pendapatan dan pertumbuhan bisnis.

### Goals

Untuk menjawab permasalahan tersebut, proyek ini memiliki beberapa tujuan utama:
- Mengembangkan model yang dapat menghasilkan daftar rekomendasi film yang relevan dan dipersonalisasi untuk setiap pengguna.
- Membangun dua jenis sistem rekomendasi dengan pendekatan berbeda untuk memberikan alternatif solusi dan memahami keunggulan masing-masing.

### Solution Approach
Untuk mencapai tujuan tersebut, dua pendekatan utama dalam sistem rekomendasi akan diimplementasikan:
- Content-Based Filtering
  Pendekatan ini merekomendasikan film berdasarkan kemiripan atribut atau konten dari film itu sendiri. Misalnya, jika seorang pengguna menyukai film A yang bergenre Sci-Fi dan Adventure, sistem akan merekomendasikan film B dan C yang juga memiliki genre serupa. Analisis akan dilakukan pada fitur-fitur film seperti genre untuk menghitung kemiripan antar film.

- Collaborative Filtering
  Pendekatan ini merekomendasikan film berdasarkan kemiripan pola perilaku antar pengguna. Logikanya adalah "pengguna yang menyukai hal yang sama di masa lalu, kemungkinan akan menyukai hal yang sama di masa depan". Sistem akan mencari pengguna dengan riwayat rating yang mirip dengan pengguna target, lalu merekomendasikan film yang disukai oleh "tetangga" tersebut tetapi belum ditonton oleh si pengguna target.

## Data Understanding

Data yang digunakan dalam proyek ini adalah MovieLens Latest Datasets (Small), yang merupakan kumpulan data populer untuk penelitian sistem rekomendasi.

### Sumber Data

**Tautan sumber data** : [MovieLens Latest Datasets](https://grouplens.org/datasets/movielens/latest/)

**Detail Dataset (Small)** : Dataset ini berisi 100.836 rating yang diberikan oleh 610 pengguna untuk 9.742 film.

### Deskripsi Data
Dataset ini terdiri dari dua file utama uaitu `movies.csv` dan `ratings.csv`.

**Variabel pada Data**
1. `movies.csv`: Berisi informasi mengenai setiap film.
   - `movieId`: ID unik untuk setiap film. (Tipe Data: Integer)
   - `title`: Judul film, beserta tahun rilis. (Tipe Data: String)
   - `genres`: Genre film, dipisahkan oleh karakter |. (Tipe Data: String)
2. `ratings.csv`: Berisi informasi rating yang diberikan oleh pengguna.
   - `userId`: ID unik untuk setiap pengguna. (Tipe Data: Integer)
   - `movieId`: ID film yang diberi rating. (Tipe Data: Integer)
   - `rating`: Rating yang diberikan, dalam skala 0.5 hingga 5.0. (Tipe Data: Float)
   - `timestamp`: Waktu pemberian rating dalam format epoch time. (Tipe Data: Integer)

**Kondisi Awal Data (Missing Values & Duplikat)**

Sebelum dilakukan proses preprocessing, dilakukan pengecekan terhadap kondisi awal dataset:

- Data Duplikat
  Berdasarkan pengecekan, tidak ditemukan data duplikat pada file `ratings.csv`. Namun, pada `movies.csv`, ditemukan beberapa film dengan judul yang sama meskipun movieId-nya berbeda. Hal ini diidentifikasi sebagai duplikasi judul yang perlu ditangani.
- Missing Values
  Pada pengecekan awal, tidak ditemukan missing values pada kedua file `movies.csv` dan `ratings.csv`. Namun, nilai `NaN` muncul setelah proses penggabungan kedua data, ketika rating untuk movieId yang tidak terdapat di file `movies.csv`, tepatnya terdapat 1868 baris yang missing pada fitur `title` dan `genres`.

### Exploratory Data Analysis (EDA)
Analisis data eksplorasi dilakukan untuk memahami karakteristik data lebih dalam. Beberapa temuan utama antara lain:

- Distribusi Rating
  Sebagian besar film mendapatkan rating antara 3.0 hingga 4.0, yang menunjukkan bahwa pengguna cenderung memberikan rating positif atau netral.

- Genre Paling Populer
  Genre yang paling sering muncul dalam dataset adalah Drama dan Comedy, diikuti oleh Thriller dan Action. Ini memberikan gambaran mengenai komposisi genre dalam katalog film.

- Jumlah Data
  Data terdiri dari 610 pengguna unik, 9.742 film unik, dan lebih dari 100.000 rating. Data ini cukup untuk melatih model collaborative filtering dan content-based filtering.
   
## Data Preparation
Tahapan persiapan data sangat penting untuk memastikan model dapat dilatih dengan baik. Berikut adalah langkah-langkah yang dilakukan:

1. Penggabungan Data
   - Menggabungkan dataframe ratings dan movies menjadi satu dataframe tunggal menggunakan movieId sebagai kunci.
   - Hal ini dilakukan agar setiap rating memiliki informasi lengkap tentang film yang dirating (judul dan genre), yang memudahkan analisis dan pemodelan selanjutnya.

2. Pembersihan Judul Film
   - Menghapus informasi tahun rilis (contoh: `(1995)`) dari setiap judul film menggunakan ekspresi reguler (regex).
   - Hal ini dilakukan untuk mendapatkan judul film yang lebih bersih dan ringkas saat ditampilkan sebagai hasil rekomendasi.

3. Penanganan Duplikasi Judul Film
   - Mengidentifikasi dan menghapus film dengan judul yang duplikat, dengan hanya mempertahankan entri pertama yang muncul.
   - memastikan setiap judul film unik dalam dataset, sehingga tidak ada ambiguitas dalam rekomendasi.

4. Penghapusan Kolom Tidak Relevan
   - Kolom `timestamp` dari data rating dihapus.
   - Hal ini dilakukan karena informasi waktu pemberian rating tidak digunakan dalam model Content-Based maupun Collaborative Filtering yang dikembangkan, sehingga dapat dihilangkan untuk menyederhanakan data.

5. Penanganan Missing Values
   - Setelah penggabungan dataset `movies` dan `ratings`, dilakukan pengecekan ulang terhadap missing values. Baris data yang mengandung nilai NaN (jika ada) akan dihapus dari dataframe.
   - Hal ini dilakuakn karena Model machine learning tidak dapat memproses nilai NaN. Menghapus baris yang tidak lengkap adalah cara paling langsung untuk memastikan integritas data.

6. Persiapan untuk Content-Based Filtering
   - Untuk model content-based, fitur utama yang digunakan adalah genres. Teks genre (contoh: "Adventure|Animation|Children") diubah menjadi representasi numerik menggunakan TF-IDF (Term Frequency-Inverse Document Frequency). TF-IDF akan membuat sebuah matriks di mana setiap baris mewakili sebuah film dan setiap kolom mewakili sebuah genre, dengan nilai yang menunjukkan seberapa penting genre tersebut bagi film tersebut.
   - Mesin tidak bisa memproses data teks secara langsung. TF-IDF adalah teknik yang efektif untuk mengubah data teks kategorikal seperti genre menjadi vektor fitur numerik yang dapat diukur kemiripannya.

7. Persiapan untuk Collaborative Filtering
   - Data pengguna dan film perlu diubah menjadi format yang dapat dipahami model. ID pengguna (userId) dan ID film (movieId) diubah menjadi indeks integer yang berurutan (misalnya, dari 0 hingga jumlah pengguna-1). Data kemudian dibagi menjadi data latih (training) dan data validasi (validation) untuk mengevaluasi model.
   - Model machine learning untuk collaborative filtering, seperti yang menggunakan Keras, memerlukan input dalam bentuk indeks numerik yang konsisten. Pembagian data latih dan validasi adalah praktik standar untuk mengukur kinerja model pada data yang belum pernah dilihat sebelumnya dan mencegah overfitting.
  
## Modeling and Result
Dua model sistem rekomendasi dikembangkan untuk menyelesaikan masalah ini.

**Content-Based Filtering**

Pendekatan ini merekomendasikan film yang mirip dengan film yang sudah disukai pengguna. Kemiripan dihitung berdasarkan genre film.
   1. Cara Kerja
      - Genre dari setiap film diubah menjadi vektor numerik menggunakan matriks TF-IDF yang telah dibuat pada tahap persiapan data.
      - Cosine Similarity dihitung antara vektor TF-IDF dari semua film. Cosine Similarity mengukur kosinus sudut antara dua vektor, menghasilkan skor antara -1 (sangat tidak mirip) hingga 1 (sangat mirip). Skor yang lebih tinggi menandakan kemiripan genre yang lebih besar.
      - Saat pengguna memilih satu film, sistem akan mencari film lain dengan skor kemiripan kosinus tertinggi.

   2. Hasil Top-N Recommendation
      Berikut adalah contoh output rekomendasi untuk film 'Toy Story'

      ![image](https://github.com/user-attachments/assets/89f4af84-e178-4712-a867-85ec9fd59bda)

      Rekomendasi yang dihasilkan sangat relevan, karena didominasi oleh film animasi lain dari Pixar dan studio besar lainnya.

   4. Kelebihan
      - Tidak ada cold start untuk item baru: Selama item baru memiliki deskripsi fitur (genre), item tersebut bisa langsung direkomendasikan.
      - Interpretasi Mudah: Rekomendasi mudah dijelaskan ("Kami merekomendasikan ini karena mirip dengan yang Anda suka").

   6. Kekurangan
      - Terlalu Spesifik (Overspecialization): Cenderung merekomendasikan item yang sangat mirip dan jarang memberikan rekomendasi yang mengejutkan atau di luar zona nyaman pengguna (serendipity).
      - Membutuhkan Fitur Item: Sangat bergantung pada ketersediaan dan kualitas data fitur (misal, genre, sutradara, aktor).

**Collaborative Filtering**

Pendekatan ini merekomendasikan film berdasarkan preferensi dari pengguna lain yang memiliki selera serupa.

   1. Cara Kerja
      - Model dibangun menggunakan deep learning dengan Embedding Layers dari library Keras.
      - Model ini mempelajari representasi laten (vektor embedding) untuk setiap pengguna dan setiap film. Vektor ini menangkap "selera" pengguna dan "karakteristik" film dalam ruang dimensi rendah.
      - Prediksi rating dilakukan dengan menghitung dot product (perkalian titik) antara vektor embedding pengguna dan vektor embedding film.
      - Model dilatih untuk meminimalkan error antara rating yang diprediksi dan rating yang sebenarnya.

   2. Hasil Top-N Recommendation
      Berikut adalah contoh output rekomendasi untuk UserId 482 

      ![image](https://github.com/user-attachments/assets/0501dae1-5115-49f4-adb9-78114cefebf5)


      Rekomendasi ini berisi film-film yang mendapatkan rating tinggi secara universal dan sering dianggap sebagai film drama atau komedi, yang menunjukkan bahwa model berhasil menangkap pola preferensi umum.

   4. Kelebihan
      - Menghasilkan Serendipity: Dapat merekomendasikan item yang tidak terduga namun disukai pengguna, karena tidak bergantung pada fitur item.
      - Tidak Memerlukan Data Item: Model bekerja hanya berdasarkan interaksi pengguna-item (rating).

   6. Kekurangan
      - Cold Start untuk Pengguna/Item Baru: Sulit memberikan rekomendasi untuk pengguna baru (tanpa riwayat rating) atau item baru (belum pernah dirating).
      - Data Sparsity: Kinerjanya menurun jika matriks interaksi pengguna-item sangat renggang (banyak rating yang kosong).

## Evaluation

Untuk mengukur kinerja model, berikut metrik evaluasi yang relevan dengan tugas sistem rekomendasi digunakan.

- **Metrik: Precision@10**
   Precision@k mengukur seberapa relevan rekomendasi yang diberikan dalam daftar top-k. Dalam kasus ini, kita menggunakan k=10. Metrik ini menjawab pertanyaan: "Dari 10 film yang direkomendasikan, berapa persen yang benar-benar relevan?". Sebuah film dianggap relevan jika genre utamanya sama dengan genre utama film yang menjadi dasar rekomendasi.

  ![image](https://github.com/user-attachments/assets/37023cbc-de9b-4d30-b2ed-4c6c701200c8)
  
- **Root Mean Squared Error (RMSE):**
  Metrik ini digunakan untuk mengevaluasi akurasi prediksi rating pada model Collaborative Filtering. RMSE mengukur rata-rata magnitudo dari selisih (error) antara rating yang diprediksi oleh model dan rating yang sebenarnya diberikan oleh pengguna. Semakin kecil nilai RMSE, semakin baik kinerja model dalam memprediksi rating.

  ![image](https://github.com/user-attachments/assets/3f75c39b-7576-41df-8c9c-45817ef13cf8)

### Hasil evaluasi

- Berdasarkan implementasi, model Content-Based Filtering mencapai nilai Precision@10 sekitar 0.0670. dan untuk model Collaborative Filtering pada proses pelatihanm, mencapai nilai RMSE pada data validasi sekitar 0.199. Nilai ini menunjukkan bahwa secara rata-rata, prediksi rating yang dihasilkan model memiliki selisih sekitar 0.19 poin dari rating sebenarnya pada skala 5 poin, yang merupakan hasil yang cukup baik.

**Referensi:**

[1] A. T. Core, "The Netflix Recommender System: A Case Study," Medium, 2022. [Online]. Available: https://medium.com/@its_TusharG/the-netflix-recommender-system-a-case-study-f495b5978197.

[2] C. A. Gomez-Uribe and N. Hunt, "The Netflix Recommender System: Algorithms, Business Value, and Innovation," ACM Transactions on Management Information Systems (TMIS), vol. 6, no. 4, pp. 1-19, 2016.
