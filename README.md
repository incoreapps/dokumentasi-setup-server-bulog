
# Langkah 1: Siapkan VPS Ubuntu

Pastikan VPS telah diinstal dengan Ubuntu dan telah diupdate dengan perintah berikut:
```bash
sudo apt update
```
# Langkah 2: Instal Nginx

```bash
sudo apt install nginx
```
Setelah menginstal Nginx, jalankan servis Nginx dan atur agar dimulai saat boot:
```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```
# Langkah 3: Instal PHP7.4 dan Ekstensi yang Diperlukan

```bash
sudo apt install php7.4 php7.4-cli php7.4-common php7.4-json php7.4-opcache php7.4-mysql php7.4-mbstring php7.4-zip php7.4-fpm php7.4-xml
```
# Langkah 5: Instal MariaDb dan setup database

```bash
sudo apt install mariadb-server
```
Setelah terinstall buat database dan user dengan Masuk ke MySQL dengan perintah:
```bash
sudo mysql -u root -p
```
Setelah masuk, buat database dan pengguna baru. Gantilah nama_database, nama_pengguna, dan kata_sandi sesuai kebutuhan:
```bash
CREATE DATABASE nama_database;
CREATE USER 'nama_pengguna'@'localhost' IDENTIFIED BY 'kata_sandi';
GRANT ALL PRIVILEGES ON nama_database.* TO 'nama_pengguna'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
#  Langkah 6: Deployment API Laravel

### Install Git
```bash
sudo apt install git
```
### install Composer
```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php --check
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer --version=2.0.13
```

### Pindah ke direktori web server
```bash
cd /var/www
```
### Clone repository api sphp_api

```bash
git clone <URL_REPO>
```
ikuti langkah-langkah untuk clone seperti memasukan nama user gitlab dan password gitlab.
setelah clone selesai masuk ke direktor proyek dengan perintah :
```bash
cd /var/www/sphp_api
```
### Setel Hak Akses Nginx
```bash
sudo chown -R www-data:www-data /var/www/sphp_api
sudo chmod -R 775 /var/www/sphp_api/storage

```
### Install dependencies menggunakan Composer
```bash
composer install
```
### Salin file .env.example untuk membuat file .env baru dengan perintah:
```bash
cp .env.example .env
```
### Atur konfigurasi database dan lingkungan lainnya di file .env
```bash
sudo nano .env
```
masukan DB_DATABASE, DB_USERNAME, DB_PASSWORD : dengan database yang telah dibuat di langkah 5

### Lakukan migrasi dan pengaturan awal Laravel
```bash
sudo php artisan migrate
sudo php artisan migrate --seed
sudo php artisan key:generate
php artisan passport:install
php artisan storage:link
```
# Langkah 7: Deployment PWA sphp
[Documentation Build PWA & APK](https://gitlab.com/perumbulog/sphp_pwa/-/blob/main/README.MD?ref_type=heads)

### rename folder www menjadi html dan zip dengan winrar.
### masuk ke vps
```bash
ssh vendor@ip-server
```
### Hapus directory html nginx dengan perintah
```bash
sudo rm -rf /var/www/html
```
### upload html.zip ke server dengan perintah
```bash
scp <direktori_proyek>\html.zip vendor@10.254.250.120:/var/www
```
### unzip html.zip dengan perintah
```bash
sudo unzip /var/www/html.zip
```
### rubah permisions html dengan perintah
```bash
sudo chmod -R 777 /var/www/html
```
# Langkah 8: konfigurasi NGINX server
Buat file konfigurasi Nginx untuk sphp_api dan sphp_pwa
```bash
sudo nano /etc/nginx/sites-available/sphp
```
masukan konfigurasi Nginx berikut:
```bash
server {
    listen 8000 default_server;
    server_name _;

    root /var/www/html;
    index index.php index.html index.htm;

}

server {
  listen 8081 default_server;
  server_name _;
  root /var/www/sphp_api/public;

  index index.php index.html index.htm;

  location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock; # Sesuaikan versi PHP dan socket
    }


    location ~ /\.ht {
        deny all;
    }

}

```
Simpan dan keluar dari editor teks. Kemudian, aktifkan konfigurasi dengan membuat tautan simbolis:
```bash
sudo ln -s /etc/nginx/sites-available/sphp /etc/nginx/sites-enabled
```
uji konfigurasi Nginx dan restart servis Nginx:
```bash
sudo nginx -t
sudo systemctl restart nginx
```

# ----SELESAI----


