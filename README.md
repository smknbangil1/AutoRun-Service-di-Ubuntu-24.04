# AutoRun-Service-di-Ubuntu-24.04
Berikut adalah langkah-langkah membuat **systemd service** pada Ubuntu Server 24.04 yang secara otomatis akan melakukan **mount** saat node dihidupkan dan melakukan **umount** saat node dimatikan untuk GlusterFS:

### 1. Buat File Service di Systemd
Buat file service baru di direktori `/etc/systemd/system/`.

```bash
sudo nano /etc/systemd/system/mount-glusterfs.service
```

### 2. Konfigurasi Service
Isi file tersebut dengan konfigurasi berikut:

```ini
[Unit]
Description=Mount GlusterFS Volume
After=network.target
Requires=network-online.target

[Service]
Type=oneshot
ExecStart=/bin/mount -t glusterfs moodle1:/gv0 /mnt/glusterfs
ExecStop=/bin/umount /mnt/glusterfs
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

#### Penjelasan:
- **Description**: Deskripsi singkat tentang service ini.
- **After=network.target**: Service ini akan dijalankan setelah jaringan aktif.
- **ExecStart**: Perintah untuk mount GlusterFS (`mount -t glusterfs moodle1:/gv0 /mnt/glusterfs`).
- **ExecStop**: Perintah untuk umount saat service dimatikan (`umount /mnt/glusterfs`).
- **RemainAfterExit=yes**: Membuat systemd tetap menganggap service aktif setelah perintah selesai dijalankan.
- **WantedBy=multi-user.target**: Menentukan bahwa service ini dijalankan pada run level multi-user (run level 3 dan 5).

### 3. Simpan dan Reload Systemd
Simpan file tersebut dan reload systemd untuk mengenali service yang baru dibuat:

```bash
sudo systemctl daemon-reload
```

### 4. Enable Service
Agar service ini berjalan otomatis saat boot, aktifkan service menggunakan perintah berikut:

```bash
sudo systemctl enable mount-glusterfs.service
```

### 5. Start Service
Untuk menjalankan service tanpa perlu reboot, gunakan perintah berikut:

```bash
sudo systemctl start mount-glusterfs.service
```

### 6. Verifikasi Service
Periksa apakah service sudah berjalan dengan benar:

```bash
sudo systemctl status mount-glusterfs.service
```

Jika service berjalan dengan benar, saat boot server akan otomatis melakukan **mount** ke GlusterFS volume dan akan melakukan **umount** saat server dimatikan.

### 7. Testing
- **Reboot Server** untuk memastikan mount GlusterFS dilakukan secara otomatis.
- **Shutdown Server** dan pastikan **umount** dijalankan sebelum server benar-benar mati.

Dengan langkah-langkah di atas, kamu telah berhasil mengkonfigurasi systemd service untuk melakukan `mount` dan `umount` secara otomatis pada saat node dihidupkan dan dimatikan.
