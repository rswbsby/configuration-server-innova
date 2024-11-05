---

# Dokumentasi Sistem

## Gambaran Server

- **Server 225**: Menjalankan database utama.
- **Server 222**: Menjalankan aplikasi utama dan database simulasi.
- **Server 223**: Menjalankan aplikasi staging.

## Proses Pemeliharaan

### Proses Dump/Backup dan Pengecekan Cronjob

1. **Cek Cronjob**  
   Jalankan perintah berikut untuk melihat cron job yang aktif:
   ```bash
   sudo crontab -e
   ```

2. **Analisis Query**  
   Gunakan `pgbadger` untuk menganalisis query pada database.

3. **Perbarui Sertifikat**  
   Untuk memperbarui sertifikat subdomain, gunakan:
   ```bash
   sudo /usr/bin/certbot renew --quiet
   ```

4. **Script Backup**  
   - Lokasi script backup: `/home/backup/backup.sh`
   - Edit script backup jika diperlukan:
     ```bash
     sudo nano /home/backup/backup.sh
     ```
   - Salin baris pertama dari file `backup.sh` untuk digunakan dalam konfigurasi lain jika diperlukan.

### Proses Restore

1. **Ganti Nama Database**  
   Tambahkan format waktu pada nama database untuk menandai versi lama.

2. **Pembuatan Database Baru**  
   Ikuti langkah-langkah berikut untuk membuat database baru.

3. **Perintah SQL untuk Pengaturan Database**
   - Mengakhiri koneksi yang aktif:
     ```sql
     SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE pid <> pg_backend_pid() AND datname = 'db_rscms_running';
     ```
   - Ganti nama database lama:
     ```sql
     ALTER DATABASE "db_rscms_running" RENAME TO "db_rscms_running_old";
     ```
   - Buat database baru:
     ```sql
     CREATE DATABASE "db_rscms_running";
     ```
   - Tambahkan komentar untuk dokumentasi:
     ```sql
     COMMENT ON DATABASE db_rscms_running IS '20241103';
     ```

4. **Perintah Restore Database**  
   Lakukan restore database menggunakan:
   ```bash
   sudo pg_restore -h localhost -U postgres -p 5121 -d db_rswb_simulasi -v "filepath_backup"
   ```

> **Catatan**: Untuk backup satu paket tertentu, Anda dapat menyalin baris yang diperlukan dari file backup.

## Monitoring Query dengan pgbadger

Akses `pgbadger` di:
```
http://192.168.214.225:8181/pgbadger/
```

---
