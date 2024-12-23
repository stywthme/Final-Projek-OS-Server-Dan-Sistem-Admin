# Final-Projek-OS-Server-Dan-Sistem-Admin

# Pengaturan Server Web pada Ubuntu 22.04

Panduan ini memberikan langkah-langkah untuk mengatur server web di Ubuntu 22.04 dengan Apache2, Flask, Gunicorn, menghosting, dan SSH Server.

---

## Prasyarat

- Mesin dengan sistem operasi Ubuntu 22.04.
- Akses root atau pengguna dengan hak `sudo`.
- Koneksi internet yang stabil.

---

## Langkah-Langkah Pengaturan Server Web

### 1. Perbarui Paket Sistem
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Instal Apache2 Web Server

![alt text](https://github.com/Tuanvallen/FINAL-Projek-OS-Server-Sistem-Admiin/blob/main/Foto%20Instalasi/Apache_img.png?raw=true)

```bash
sudo apt install apache2 -y
```
- Aktifkan dan mulai Apache:
```bash
sudo systemctl enable apache2
sudo systemctl start apache2
```

### 3. Instal Python3 dan Pip
```bash
sudo apt install python3 python3-pip -y
```

### 4. Instal Framework Flask

![alt text](https://github.com/Tuanvallen/FINAL-Projek-OS-Server-Sistem-Admiin/blob/main/Foto%20Instalasi/flask1.png?raw=true)

```bash
pip3 install flask
```

### 5. Instal Gunicorn

![alt text](https://github.com/Tuanvallen/FINAL-Projek-OS-Server-Sistem-Admiin/blob/main/Foto%20Instalasi/Gunicorn.jpeg?raw=true)


```bash
pip3 install gunicorn
```

### 6. Instal dan Konfigurasi SSH Server
```bash
sudo apt install openssh-server -y
```
- Aktifkan dan mulai SSH:
```bash
sudo systemctl enable ssh
sudo systemctl start ssh
```

### 7. Konfigurasi Aplikasi Flask
- Buat direktori untuk aplikasi Flask Anda:
```bash
mkdir ~/my_flask_app
cd ~/my_flask_app
```
- Buat file `app.py` :
```python
from flask import Flask, render_template, request, redirect, url_for, flash
import os

app = Flask(__name__)
# Secret key untuk flash messages dan session
app.secret_key = 'fashion_store_secret_key_123'

# Memastikan folder templates ada
if not os.path.exists('templates'):
    os.makedirs('templates')

# Route untuk halaman utama
@app.route('/')
def home():
    # Data produk (bisa diganti dengan database nantinya)
    products = [
        {
            'id': 1,
            'name': 'Kemeja Casual',
            'price': 199000,
            'image': '/api/placeholder/400/300'
        },
        {
            'id': 2,
            'name': 'Dress Modern',
            'price': 299000,
            'image': '/api/placeholder/400/300'
        },
        {
            'id': 3,
            'name': 'Kaos Premium',
            'price': 149000,
            'image': '/api/placeholder/400/300'
        }
    ]
    return render_template('index.html', products=products)

# Route untuk handling form kontak
@app.route('/contact', methods=['POST'])
def contact():
    if request.method == 'POST':
        name = request.form.get('name')
        email = request.form.get('email')
        message = request.form.get('message')
        
        # Di sini Anda bisa menambahkan logika untuk menyimpan pesan
        # ke database atau mengirim email
        
        # Menambahkan flash message
        flash('Pesan Anda telah terkirim! Kami akan segera menghubungi Anda.', 'success')
        
        return redirect(url_for('home', _anchor='contact'))

# Route untuk handling pembelian produk
@app.route('/buy/<int:product_id>', methods=['POST'])
def buy_product(product_id):
    # Di sini Anda bisa menambahkan logika untuk proses pembelian
    flash('Produk telah ditambahkan ke keranjang!', 'success')
    return redirect(url_for('home', _anchor='products'))

# Error handlers
@app.errorhandler(404)
def page_not_found(e):
    return render_template('404.html'), 404

@app.errorhandler(500)
def internal_server_error(e):
    return render_template('500.html'), 500

if __name__ == '__main__':
    # Pastikan file index.html ada di folder templates
    template_path = os.path.join(os.path.dirname(os.path.abspath(__file__)), 'templates')
    if not os.path.exists(os.path.join(template_path, 'index.html')):
        print("Error: index.html tidak ditemukan di folder templates!")
        exit(1)
    
    # Menjalankan aplikasi dalam mode debug
    app.run(debug=True)
```

### 8. Jalankan Flask dengan Gunicorn
```bash
gunicorn --bind 0.0.0.0:8000 app:app
```

### 9. Konfigurasi Apache untuk Meneruskan Permintaan ke Gunicorn
- Aktifkan modul Apache yang diperlukan:
```bash
sudo a2enmod proxy proxy_http
sudo systemctl restart apache2
```
- Buat file konfigurasi Apache untuk aplikasi Flask Anda:
```bash
sudo nano /etc/apache2/sites-available/my_flask_app.conf
```
Tambahkan konten berikut:
```apache
<VirtualHost *:80>
    ServerName yourdomain.com

    ProxyPass / http://127.0.0.1:8000/
    ProxyPassReverse / http://127.0.0.1:8000/

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
- Aktifkan situs dan restart Apache:
```bash
sudo a2ensite my_flask_app
sudo systemctl restart apache2
```

### 10. Uji Pengaturan
- Buka browser dan akses `http://<ip-server-anda>`.
---


### 15. jalankan dengan nama hostingan anada

![alt text](https://github.com/Tuanvallen/FINAL-Projek-OS-Server-Sistem-Admiin/blob/main/Foto%20Instalasi/Screenshot%202024-12-22%20231801.png?raw=true)

---

## Kesimpulan
Anda telah berhasil mengatur server web di Ubuntu 22.04 menggunakan Apache2, Flask, Gunicorn, SSH Server, dan Cloudflare Tunnel. Dengan konfigurasi ini, aplikasi Flask Anda dapat diakses secara aman melalui domain yang terhubung ke Cloudflare.

---
