# EKOSISTEM-HADOOP

---

# 🔰 BAGIAN 1 — Persiapan Awal (Sekali Setup)

Sebelum Hadoop bisa jalan, kita perlu “mesin virtual kecil” berbasis container.

## 1️⃣ Install Docker Desktop

Docker dipakai untuk menjalankan Hadoop dalam container.

* Download dari:
  [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
* Install seperti aplikasi biasa.
* Login menggunakan akun Docker Hub.

💡 **Kenapa perlu Docker?**
Karena Hadoop biasanya berjalan di banyak server. Untuk praktikum, kita simulasikan semuanya dalam 1 container agar ringan dan praktis.

---

## 2️⃣ Install WSL + Ubuntu (Linux di Windows)

Buka **PowerShell sebagai Administrator**, lalu jalankan:

```
wsl --install -d Ubuntu-24.04
```

Setelah selesai:

* Restart jika diminta
* Buka aplikasi **Ubuntu**
* Login sesuai instruksi awal

💡 **Kenapa pakai Ubuntu?**
Karena Hadoop berjalan optimal di Linux.

---

# 🔰 BAGIAN 2 — Install dan Jalankan Hadoop

## 1️⃣ Download Project Hadoop

Masuk ke Ubuntu, lalu ketik:

```
cd /mnt/c
git clone https://github.com/sains-data/bigdata-hadoop.git
cd bigdata-hadoop
```

Ini akan mengunduh konfigurasi Hadoop yang sudah disiapkan.

---

## 2️⃣ Download File Hadoop (Manual)

Download file ini lewat browser:

[https://downloads.apache.org/hadoop/common/hadoop-3.4.1/hadoop-3.4.1.tar.gz](https://downloads.apache.org/hadoop/common/hadoop-3.4.1/hadoop-3.4.1.tar.gz)

Pindahkan ke folder:

```
C:\bigdata-hadoop
```

---

## 3️⃣ Build Docker Image

Masih di folder project:

```
bash build.sh
```

Tunggu sampai selesai.

---

## 4️⃣ Jalankan Hadoop

```
bash start.sh
```

Cek apakah container berjalan:

```
docker ps -a
```

Kalau statusnya **Up**, berarti berhasil ✅

---

## 5️⃣ Masuk ke Dalam Hadoop

```
bash login.sh
```

Sekarang kamu sudah berada di dalam sistem Hadoop.

Cek apakah servicenya jalan:

```
jps
```

Harus muncul:

* NameNode
* DataNode
* ResourceManager
* NodeManager

Kalau ada semua → Hadoop siap dipakai 🚀

---

# 🔰 BAGIAN 3 — Eksperimen HDFS (File System Hadoop)

Sekarang kita belajar bagaimana Hadoop menyimpan data.

---

## 1️⃣ Buat Folder di HDFS

```
hdfs dfs -mkdir -p /user/latihan
```

Ini seperti membuat folder di Google Drive, tapi di Hadoop.

---

## 2️⃣ Buat File Percobaan

```
echo "Hadoop adalah framework big data terdistribusi.
HDFS menyimpan data dalam blok.
MapReduce memproses data paralel.
YARN mengelola resource." > latihan.txt
```

---

## 3️⃣ Upload ke HDFS

```
hdfs dfs -put latihan.txt /user/latihan/
```

---

## 4️⃣ Cek File

```
hdfs dfs -ls /user/latihan/
```

---

## 5️⃣ Baca File dari HDFS

```
hdfs dfs -cat /user/latihan/latihan.txt
```

---

## 6️⃣ Lihat Cara Hadoop Menyimpan File

```
hdfs fsck /user/latihan/latihan.txt -files -blocks -locations
```

💡 Di sini kamu akan lihat:

* File dipecah menjadi **blok**
* Ada jumlah **replikasi**

Kenapa dipecah?
Supaya bisa disimpan di banyak node dan diproses paralel.

---

## 7️⃣ Download Kembali

```
hdfs dfs -get /user/latihan/latihan.txt hasil.txt
cat hasil.txt
```

---

# 🔰 BAGIAN 4 — Monitoring lewat Browser

Setelah Hadoop jalan, buka:

### 🖥 NameNode UI

[http://localhost:9870](http://localhost:9870)

Di sini kamu bisa lihat:

* Kapasitas total
* DataNode aktif
* File yang tersimpan

---

### 🖥 ResourceManager UI

[http://localhost:8088](http://localhost:8088)

Di sini kamu bisa lihat:

* Job MapReduce
* Penggunaan memori
* Status node

---

# 🔰 BAGIAN 5 — Jalankan MapReduce (Word Count)

Sekarang kita jalankan program klasik Hadoop: **Word Count**

Tujuannya: menghitung jumlah kata dalam file.

---

## 1️⃣ Buat Dataset

```
cat <<EOF > dataset.txt
hadoop hdfs mapreduce yarn hadoop
namenode datanode hadoop
mapreduce hadoop
EOF
```

---

## 2️⃣ Upload ke HDFS

```
hdfs dfs -mkdir -p /user/latihan/input
hdfs dfs -put dataset.txt /user/latihan/input/
```

---

## 3️⃣ Jalankan WordCount

```
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar \
wordcount /user/latihan/input /user/latihan/output
```

Tunggu sampai selesai.

---

## 4️⃣ Lihat Hasilnya

```
hdfs dfs -cat /user/latihan/output/part-r-00000
```

Contoh hasil:

```
hadoop 4
hdfs 1
mapreduce 2
```

💡 Artinya:

* Mapper memecah kata
* Reducer menjumlahkan

---

# 🔰 BAGIAN 6 — Kelola File di HDFS

### Salin file

```
hdfs dfs -cp /user/latihan/latihan.txt /user/latihan/backup.txt
```

### Pindahkan file

```
hdfs dfs -mv /user/latihan/backup.txt /user/latihan/
```

### Hapus file

```
hdfs dfs -rm -r /user/latihan/output
```

---

# 🔰 Intinya Modul 2 Ini Apa?

Modul ini mengajarkan 3 konsep utama dalam ekosistem Hadoop:

1️⃣ **HDFS** → Cara menyimpan data besar secara terdistribusi
2️⃣ **MapReduce** → Cara memproses data besar secara paralel
3️⃣ **YARN** → Sistem manajemen resource di cluster

Dan semua itu dijalankan dalam 1 container (pseudo-distributed mode) supaya mudah untuk belajar.

---

