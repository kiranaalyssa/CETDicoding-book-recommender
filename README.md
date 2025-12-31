# Laporan Proyek Sistem Rekomendasi Buku – Kirana Alyssa Putri

## Domain Proyek

Proyek ini berada dalam domain **Sistem Rekomendasi**, dengan fokus pada pemberian rekomendasi buku yang relevan bagi pembaca. Sistem ini dirancang untuk membantu pengguna menemukan buku yang sesuai dengan preferensi mereka, khususnya berdasarkan genre buku. Tujuan utamanya adalah membangun model rekomendasi berbasis konten (*content-based filtering*) yang mampu menilai kemiripan antar buku menggunakan data genre sebagai acuan.

## Latar Belakang

Di era digital saat ini, jumlah buku yang tersedia di *platform* seperti Goodreads sangat banyak. Hal ini membuat pembaca sering kesulitan memilih buku yang sesuai minat mereka. Sistem rekomendasi berbasis konten hadir sebagai solusi untuk mempermudah proses pencarian, meningkatkan pengalaman pengguna, dan menghemat waktu. Dengan menilai kesamaan antar buku berdasarkan genre, sistem dapat memberikan rekomendasi yang logis dan sesuai dengan preferensi pembaca. Selain itu, pendekatan ini dapat dimanfaatkan oleh platform buku online untuk meningkatkan keterlibatan pengguna dan membantu pembaca menemukan karya yang relevan dengan cepat.

## Business Understanding

Masalah utama yang ingin diselesaikan dalam proyek ini adalah bagaimana memberikan rekomendasi buku yang relevan berdasarkan genre, dan bagaimana sistem dapat menilai kesamaan antar buku dengan efektif. Tujuan dari proyek ini adalah membuat sistem rekomendasi yang dapat menghasilkan daftar buku mirip secara otomatis, sehingga pengguna dapat menemukan buku baru yang sesuai minat mereka tanpa perlu mencari secara manual. Pendekatan content-based filtering dipilih karena fokus pada karakteristik buku itu sendiri, yaitu genre, sehingga dapat memberikan rekomendasi yang spesifik dan personal tanpa bergantung pada data pengguna lain.

## Data Understanding

### Pengantar Dataset
Dataset yang digunakan dalam proyek ini diperoleh dari platform Kaggle dengan sumber **Ishika Johari** – Best Books 10K Multi-Genre Data. Dataset ini merupakan hasil pengumpulan data dari Goodreads, khususnya dari daftar **Books That Everyone Should Read At Least Once**. Data ini dirancang untuk merepresentasikan informasi penting terkait buku lintas genre yang banyak direkomendasikan oleh pengguna.

### Sumber Data
Dataset dapat diakses melalui Kaggle pada tautan berikut:
https://www.kaggle.com/datasets/ishikajohari/best-books-10k-multi-genre-data

### Jumlah Baris dan Kolom
Berdasarkan hasil eksplorasi awal, dataset terdiri dari:

* 10.000 Baris Data
* 8 Kolom Fitur

Setiap baris merepresentasikan satu buku dengan atribut informasi yang relevan untuk analisis dan pemodelan sistem rekomendasi.

### Kondisi Data
Hasil pengecekan kualitas data menunjukkan beberapa kondisi berikut:
* **Missing Value:**
Berdasarkan hasil pemeriksaan kualitas data, ditemukan beberapa kondisi pada dataset. Terdapat missing value pada kolom Description sebanyak 77 baris. Jumlah ini relatif kecil dibandingkan total data sehingga tidak memberikan dampak signifikan terhadap keseluruhan analisis.

* **Tipe Data:**
Dari sisi tipe data, kolom Num_Ratings awalnya tersimpan sebagai string karena adanya tanda koma sebagai pemisah ribuan, sehingga perlu diperhatikan sebelum dilakukan analisis numerik.

* **Duplikasi Data:**
Selain itu, pemeriksaan duplikasi data menunjukkan bahwa tidak terdapat data duplikat dalam dataset.

### Uraian Fitur Dataset

Berikut adalah penjelasan masing-masing fitur yang terdapat dalam dataset:

| Fitur        | Penjelasan                                                                      |
| ------------ | ------------------------------------------------------------------------------- |
| Book         | Nama buku                                                                       |
| Author       | Nama penulis buku.                                                              |
| Description  | Deskripsi buku sesuai yang tercantum di Goodreads.                              |
| Genres       | Kategori genre buku. Satu buku bisa memiliki lebih dari satu genre.             |
| Avg\_Rating  | Rating rata-rata buku dari pengguna Goodreads, skala 1–5.                       |
| Num\_Ratings | Jumlah pengguna yang memberikan rating. Penting untuk membedakan dengan review. |
| URL          | URL halaman Goodreads buku.                                                     |

## Data Preparation

### 1. Pengecekan Missing Value
Fungsi isnull().sum() digunakan untuk mengidentifikasi jumlah nilai kosong (missing value) pada setiap kolom dalam DataFrame df_clean.

Hasil pengecekan menunjukkan bahwa kolom Description memiliki 77 missing value dari total 10.000 baris data. Jumlah tersebut relatif kecil, sehingga baris dengan nilai kosong pada kolom ini dihapus tanpa memberikan dampak signifikan terhadap kualitas dan hasil analisis.

### 2. Pengecekan Data Duplikat
Fungsi duplicated().sum() digunakan untuk mendeteksi keberadaan baris duplikat dalam dataset.

Berdasarkan hasil pengecekan, tidak ditemukan data duplikat, yang berarti setiap baris merepresentasikan entri buku yang unik dan dataset siap digunakan untuk tahap analisis selanjutnya tanpa perlu pembersihan tambahan terkait duplikasi.

### 3. Transformasi Tipe Data Num_Ratings
Kolom Num_Ratings awalnya bertipe string karena menggunakan tanda koma sebagai pemisah ribuan, misalnya "9,278,135".

Untuk memungkinkan analisis numerik, tanda koma pada kolom ini dihapus, kemudian nilainya dikonversi menjadi tipe integer. Kolom ini merepresentasikan jumlah pengguna yang memberikan rating pada suatu buku, sehingga penting untuk dipastikan memiliki tipe data numerik yang sesuai.

### 4. Penghapusan Kolom Tidak Relevan
Kolom Unnamed: 0 dan URL dihapus dari dataset karena tidak relevan untuk analisis lanjutan.

* Unnamed: 0 merupakan kolom indeks tambahan yang tidak mengandung informasi baru.
* URL hanya berisi tautan ke halaman Goodreads dan tidak digunakan dalam proses perhitungan maupun visualisasi data.

Penghapusan kedua kolom ini bertujuan untuk menyederhanakan struktur dataset dan memastikan fokus analisis hanya pada fitur-fitur yang memiliki kontribusi terhadap pemodelan.

## Model Development & Results

Sistem rekomendasi yang dikembangkan pada proyek ini adalah sistem rekomendasi berbasis konten (content-based filtering). Sistem ini bekerja dengan merekomendasikan buku yang memiliki karakteristik serupa dengan buku yang dipilih pengguna, berdasarkan informasi konten yang dimiliki buku tersebut, khususnya genre.

Pendekatan ini tidak bergantung pada interaksi pengguna lain, melainkan fokus pada kemiripan antar item, sehingga cocok digunakan ketika data preferensi pengguna terbatas.

### Skema Sistem Rekomendasi
Skema rekomendasi yang digunakan adalah sebagai berikut:
1. Sistem menerima judul buku input dari pengguna.
2. Sistem mengambil representasi vektor TF-IDF dari buku input berdasarkan genre.
3. Sistem menghitung nilai Cosine Similarity antara buku input dan seluruh buku lain dalam dataset.
4. Buku-buku dengan nilai kemiripan tertinggi dipilih sebagai rekomendasi.
5. Hasil rekomendasi ditampilkan dalam bentuk daftar buku yang paling relevan.

### Metode yang Digunakan
Untuk mengukur tingkat kemiripan antar buku, digunakan metode Cosine Similarity. Sebelum proses perhitungan kemiripan dilakukan, fitur teks berupa genre buku terlebih dahulu diubah menjadi representasi numerik menggunakan TF-IDF (Term Frequency–Inverse Document Frequency). TF-IDF berfungsi untuk merepresentasikan setiap buku dalam bentuk vektor berdasarkan pentingnya kata atau genre yang dimiliki.

Selanjutnya, Cosine Similarity digunakan untuk mengukur kesamaan antara dua vektor TF-IDF dengan menghitung sudut kosinus di antara keduanya. Nilai cosine similarity berada pada rentang 0 hingga 1, di mana nilai yang semakin mendekati 1 menunjukkan tingkat kemiripan yang semakin tinggi. Dalam konteks sistem rekomendasi ini, buku dengan nilai kemiripan tertinggi akan direkomendasikan karena memiliki genre yang paling mirip.

### Cara Kerja Sistem Rekomendasi
Ketika pengguna memilih sebuah buku, sistem akan:
* Mengambil representasi vektor TF-IDF dari buku tersebut berdasarkan genre.
* Menghitung nilai Cosine Similarity antara vektor buku input dan seluruh vektor buku lainnya.
* Mengurutkan buku berdasarkan nilai kemiripan tertinggi.
* Menampilkan beberapa buku teratas sebagai rekomendasi.

### Hasil Rekomendasi

Sebagai contoh hasil rekomendasi, ketika pengguna memilih buku Pride and Prejudice, sistem menampilkan daftar buku rekomendasi yang memiliki genre paling mirip.

`book_recommendations('Pride and Prejudice')`

Hasil rekomendasi menunjukkan bahwa buku-buku seperti Emma, Persuasion, dan Sense and Sensibility muncul sebagai rekomendasi utama. Hal ini terjadi karena buku-buku tersebut memiliki kesamaan genre, yaitu Classics, Fiction, Romance, dan Historical, sehingga dinilai sangat relevan untuk pembaca Pride and Prejudice.

| No | Book                                   | Genres                                 |
|----|----------------------------------------|--------------------------------------- |
| 1  | Emma                                   | Classics, Fiction, Romance, Historical |
| 2  | Persuasion                             | Classics, Fiction, Romance, Historical |
| 3  | Sense and Sensibility                  | Classics, Fiction, Romance, Historical |
| 4  | The Duke and I (Bridgertons, #1)        | Romance, Historical Romance, Historical |
| 5  | Gone with the Wind                     | Classics, Historical Fiction, Fiction |

Hasil ini menunjukkan bahwa sistem rekomendasi mampu menangkap kesamaan karakteristik buku dengan baik dan memberikan rekomendasi yang sesuai berdasarkan konten.

### Kesesuaian Model dengan Business Understanding

Berdasarkan hasil pengembangan dan contoh rekomendasi yang dihasilkan, sistem rekomendasi buku yang dibangun telah berhasil menjawab permasalahan utama pada tahap Business Understanding. Sistem mampu memberikan rekomendasi buku yang relevan berdasarkan kesamaan genre, serta menilai kemiripan antar buku secara efektif melalui perhitungan similarity.

Tujuan proyek untuk menghasilkan daftar buku yang mirip secara otomatis juga tercapai, ditunjukkan dengan munculnya buku-buku yang memiliki karakteristik genre serupa dengan buku input, seperti pada rekomendasi untuk Pride and Prejudice. Dengan pendekatan content-based filtering, sistem dapat memberikan rekomendasi yang spesifik dan sesuai dengan minat pengguna tanpa bergantung pada data pengguna lain.

Dengan demikian, proyek ini dinilai berhasil dalam menyelesaikan permasalahan bisnis yang didefinisikan, yaitu membantu pengguna menemukan buku baru yang relevan secara efisien dan terarah berdasarkan preferensi genre.

## Evaluation

### Hasil Evaluasi Model
Evaluasi dilakukan pada sistem rekomendasi dengan skema Top-10 Recommendation, yaitu sistem hanya menampilkan 10 buku teratas dengan tingkat kemiripan tertinggi terhadap buku input. Hasil evaluasi yang diperoleh adalah sebagai berikut:

| Metric        | Value  |
|---------------|--------|
| Precision@10  | 0.9080 |
| Recall@10     | 0.0031 |
| F1-Score@10   | 0.0062 |

### 1. Interpretasi Precision
Nilai Precision@10 sebesar 0.9080 menunjukkan bahwa sebagian besar buku yang direkomendasikan oleh sistem merupakan buku yang relevan. Artinya, dari 10 buku yang ditampilkan, sekitar 90% di antaranya memiliki kesamaan genre yang sesuai dengan buku acuan. Hal ini mengindikasikan bahwa model mampu memberikan rekomendasi yang tepat sasaran dan konsisten berdasarkan kemiripan konten.

### 2. Interpretasi Recall
Nilai Recall@10 yang rendah (0.0031) menunjukkan bahwa sistem hanya mencakup sebagian kecil dari seluruh buku relevan yang tersedia di dataset. Hal ini wajar terjadi karena:
* Dataset memiliki jumlah item yang sangat besar (ribuan buku).
* Sistem hanya menampilkan 10 rekomendasi teratas, sehingga tidak mungkin mencakup seluruh buku yang relevan.
* Pendekatan content-based filtering lebih menekankan pada kualitas rekomendasi teratas dibandingkan cakupan keseluruhan item.

Dengan kata lain, recall menjadi kecil karena ruang pencarian relevan sangat luas, sementara jumlah rekomendasi dibatasi.

### 3. Interpretasi F1-Score
Nilai F1-Score sebesar 0.0062 dipengaruhi langsung oleh nilai recall yang rendah. Meskipun precision tinggi, nilai F1 menjadi kecil karena F1 merupakan harmonisasi antara precision dan recall. Namun, dalam konteks sistem rekomendasi Top-K, F1-score bukan satu-satunya indikator utama kualitas rekomendasi.

### Kesimpulan Evaluasi
Berdasarkan hasil evaluasi, dapat disimpulkan bahwa model:
1. Cukup akurat dalam memberikan rekomendasi yang relevan, ditunjukkan oleh nilai precision yang tinggi.
2. Lebih fokus pada kualitas rekomendasi dibandingkan jumlah item relevan yang tercakup.
3. Layak untuk diterapkan dalam skenario rekomendasi buku, di mana pengguna lebih membutuhkan beberapa rekomendasi yang benar-benar sesuai minat dibandingkan daftar rekomendasi yang sangat banyak.

Dengan demikian, sistem rekomendasi yang dikembangkan telah memenuhi tujuan utama proyek, yaitu membantu pengguna menemukan buku yang relevan secara efektif berdasarkan kesamaan genre.
