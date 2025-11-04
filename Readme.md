# Cara Menjalankan

# sesuaikan kredensial di .env


# run

`docker compose up -d`


# Migrasi dari Cpanel / Hpanel

 Backup file dan database dari server lama
 Upload ke Server Baru
 Restore manual file dan database ke container docker


 jika menggunakan zero trust tunnel tambahkan pada wp-config.php sebelum text 
 /* That's all, stop editing! Happy publishing. */


`if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
    $_SERVER['HTTPS'] = 'on';
}`



# Migrasi dari server lain

1. Jalankan perintah berikut di server lama:

# Backup WordPress files
`docker run --rm \
  -v wordpress_data:/data \
  -v $(pwd):/backup \
  alpine tar czf /backup/wordpress_data.tar.gz -C /data .`

# Backup Database
`docker run --rm \
  -v db_data:/data \
  -v $(pwd):/backup \
  alpine tar czf /backup/db_data.tar.gz -C /data .`


2. Setelah selesai, kamu akan punya dua file di direktori kerja:

wordpress_data.tar.gz
db_data.tar.gz


🔹 Opsi B — Gunakan phpMyAdmin untuk Backup Database
Kalau kamu lebih nyaman GUI:
Buka phpMyAdmin (http://serverlama:8081)
Pilih database wordpress
Klik tab Export → Format: SQL → Download

Simpan file .sql hasil ekspor.

# Restore ke server baru dari backup docker

Copy file backup ke server baru:

`scp wordpress_data.tar.gz db_data.tar.gz user@serverbaru:/home/user/``

Jalankan container di server baru (gunakan docker-compose.yml yang sama).

Setelah container dibuat (pertama kali jalankan docker compose up -d lalu hentikan):

docker compose down


Restore data:

# Restore WordPress files
`docker run --rm \
  -v wordpress_data:/data \
  -v $(pwd):/backup \
  alpine sh -c "cd /data && tar xzf /backup/wordpress_data.tar.gz"`

# Restore Database
`docker run --rm \
  -v db_data:/data \
  -v $(pwd):/backup \
  alpine sh -c "cd /data && tar xzf /backup/db_data.tar.gz"`


Jalankan kembali:

`docker compose up -d`

