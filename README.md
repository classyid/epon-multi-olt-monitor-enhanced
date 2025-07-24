# 🌐 Enhanced EPON Multi-OLT Monitor v2.1

Sistem monitoring jaringan EPON (Ethernet Passive Optical Network) yang canggih dengan dashboard web real-time, notifikasi WhatsApp otomatis, dan analitik komprehensif untuk monitoring multiple OLT secara bersamaan.

## ✨ Fitur Utama

### 🚀 Dashboard Web Real-time
- **Antarmuka Modern**: Dashboard responsif dengan Bootstrap 5 dan Chart.js
- **Real-time Updates**: WebSocket untuk update data langsung tanpa refresh
- **Grafik Interaktif**: Monitoring trend status, suhu, dan kualitas sinyal
- **Filter & Export**: Filter data dan export laporan dalam format JSON

### 📡 Multi-OLT Monitoring
- **Concurrent Processing**: Monitor multiple OLT secara bersamaan
- **Auto-detection**: Deteksi otomatis perubahan status ONU
- **Comprehensive Data**: Temperature, RX Power, Distance, Auth Status
- **Performance Analytics**: Statistik performa dan uptime network

### 📱 WhatsApp Notifications
- **Smart Alerts**: Notifikasi cerdas berdasarkan prioritas masalah
- **Detailed Reports**: Format pesan detail dengan emoji dan status
- **Hourly Reports**: Laporan rutin setiap jam otomatis
- **Recovery Notifications**: Notifikasi saat layanan pulih

### 🗄️ Database & Analytics
- **SQLite Database**: Penyimpanan data komprehensif
- **Historical Data**: Riwayat alert dan statistik monitoring
- **Auto Cleanup**: Pembersihan data lama otomatis
- **Data Export**: Export data untuk analisis lebih lanjut

## 🛠️ Teknologi yang Digunakan

- **Backend**: Python 3.7+, Flask, SQLite
- **Frontend**: Bootstrap 5, Chart.js, Socket.IO
- **Monitoring**: Requests, BeautifulSoup, Concurrent Processing
- **Notifications**: WhatsApp API (kirimwa.classy.id)
- **Database**: SQLite dengan SQLAlchemy

## 📋 Persyaratan Sistem

- Python 3.7 atau lebih baru
- OLT yang mendukung web interface standar
- Akses jaringan ke OLT yang akan dimonitor
- WhatsApp API key (opsional untuk notifikasi)

## 🚀 Instalasi

### 1. Clone Repository
```bash
git clone https://github.com/classyid/epon-multi-olt-monitor-enhanced.git
cd epon-multi-olt-monitor-enhanced
```

### 2. Install Dependencies
```bash
pip install -r requirements.txt
```

### 3. Jalankan Aplikasi
```bash
python epon_monitor_enhanced.py
```

### 4. Akses Dashboard
Buka browser dan kunjungi: `http://localhost:5000`

## ⚙️ Konfigurasi

### Menambah OLT Baru
1. Buka dashboard web di `http://localhost:5000`
2. Pilih menu **Configuration**
3. Klik **Add OLT** dan isi data:
   - **OLT ID**: Identifier unik (contoh: OLT-KEDIRI-01)
   - **OLT Name**: Nama deskriptif
   - **URL**: URL web interface OLT (contoh: http://192.168.1.100:88)
   - **Username/Password**: Kredensial login OLT

### Konfigurasi WhatsApp
1. Buka menu **WhatsApp** di dashboard
2. Masukkan detail API:
   - **API URL**: Endpoint WhatsApp API
   - **API Key**: Kunci API dari provider
   - **Sender**: Nomor pengirim
   - **Recipient**: Nomor penerima alert

## 📊 Cara Penggunaan

### Memulai Monitoring
1. Pastikan OLT sudah dikonfigurasi
2. Klik tombol **Start** di sidebar dashboard
3. Monitor akan berjalan otomatis sesuai interval yang ditentukan

### Melihat Status Real-time
- **Dashboard**: Overview semua OLT dan statistik
- **Grafik Trend**: Monitoring perubahan status dari waktu ke waktu
- **Alert History**: Riwayat semua alert yang terjadi

### Mengatur Interval Monitoring
- Default: 10 detik
- Dapat diubah melalui menu Configuration → Settings
- Rekomendasi: 10-30 detik untuk performa optimal

## 📱 Format Notifikasi WhatsApp

### Alert Status Change
```
🚨 EPON MULTI-OLT STATUS ALERT 🚨

⏰ Time: 2024-12-20 10:30:15
🌐 Total OLT Monitored: 3

🏢 OLT KEDIRI PUSAT (OLT-KEDIRI-01)
──────────────────────────────────────────────────
📊 Status: 2 Total Problems
   🔴 DOWN: 1
   🔌 PWRDOWN: 1

🔴 NEW DOWN DETECTED:
🔴 CLIENT-001
   📍 PON: 1/1/1
   🔗 MAC: 00:11:22:33:44:55
   🚨 Status: Down (Timeout)
   ⚠️ Priority: HIGH
   🌡️ Temp: 45°C
   📏 Jarak: 150m
   📵 RX: -28 dBm
   ❌ Reason: TIMEOUT (Koneksi Terputus)
```

### Hourly Report
```
📋 HOURLY STATUS REPORT 📋

⏰ Report Time: 2024-12-20 11:00:00
🌐 Multi-OLT Network Status

📊 NETWORK OVERVIEW:
🏢 Total OLT: 3
📡 Total ONU: 150
🟢 Online: 145 (96.7%)
❌ Problems: 5

🟢 OLT Kediri Pusat: 48/50 (96.0%) | 🌡️42°C | 📶-22dBm
🟢 OLT Kediri Timur: 49/50 (98.0%) | 🌡️38°C | 📶-20dBm
🟡 OLT Kediri Barat: 48/50 (96.0%) | 🔥66°C | 📵-28dBm

📈 NETWORK HEALTH:
🌡️ Avg Temperature: 48.7°C
📶 Avg RX Power: -23.3 dBm
⚡ Network Uptime: 96.7%
```

## 🔧 API Endpoints

### Dashboard API
- `GET /api/dashboard/enhanced-stats` - Statistik dashboard
- `GET /api/dashboard/enhanced-olt-status` - Status detail OLT
- `GET /api/dashboard/export` - Export data

### Configuration API
- `GET /api/config/olts` - Daftar OLT
- `POST /api/config/olts` - Tambah OLT baru
- `PUT /api/config/olts/{id}` - Update OLT
- `DELETE /api/config/olts/{id}` - Hapus OLT

### Monitoring API
- `POST /api/monitoring/start` - Mulai monitoring
- `POST /api/monitoring/stop` - Stop monitoring
- `GET /api/monitoring/status` - Status monitoring

## 🗂️ Struktur Database

### Tabel Utama
- **olt_configs**: Konfigurasi OLT
- **onu_status**: Status real-time ONU
- **alert_history**: Riwayat alert
- **monitoring_stats**: Statistik monitoring
- **whatsapp_templates**: Template pesan WhatsApp

## 📈 Fitur Monitoring

### Parameter yang Dimonitor
- **Status ONU**: Up, Down, PwrDown, LoopDetected
- **Temperature**: Suhu operasional ONU
- **RX Power**: Kekuatan sinyal terima
- **Distance**: Jarak ONU dari OLT
- **Auth Status**: Status otentikasi
- **CTC Status**: Status protokol CTC

### Deteksi Masalah
- **Critical**: Power down, dying gasp, suhu tinggi
- **High**: Timeout, auth failed, sinyal lemah
- **Medium**: ONU restart, disconnection
- **Normal**: Operasional normal

## 🔍 Troubleshooting

### Monitoring Tidak Berjalan
1. Periksa koneksi ke OLT
2. Verifikasi username/password OLT
3. Pastikan OLT enabled di konfigurasi
4. Cek log error di console

### WhatsApp Tidak Terkirim
1. Verifikasi API key dan URL
2. Periksa format nomor telepon
3. Pastikan saldo API mencukupi
4. Test koneksi ke API WhatsApp

### Performance Issues
1. Kurangi interval monitoring
2. Batasi jumlah data points di chart
3. Enable auto cleanup data lama
4. Monitor resource usage sistem

## 📝 Changelog

### v2.1 (Current)
- ✅ Enhanced web dashboard dengan real-time charts
- ✅ Concurrent multi-OLT monitoring
- ✅ Comprehensive WhatsApp alerts dengan format detail
- ✅ SQLite database dengan analytics
- ✅ Temperature dan signal quality monitoring
- ✅ Recovery notifications
- ✅ Data export dan cleanup features
- ✅ RESTful API dengan enhanced endpoints

### v2.0
- ✅ Multi-OLT support
- ✅ Web dashboard dasar
- ✅ WhatsApp integration
- ✅ Database storage

## 🤝 Kontribusi

1. Fork repository ini
2. Buat branch fitur baru (`git checkout -b feature/AmazingFeature`)
3. Commit perubahan (`git commit -m 'Add some AmazingFeature'`)
4. Push ke branch (`git push origin feature/AmazingFeature`)
5. Buat Pull Request

## 📄 Lisensi

Distributed under the MIT License. See `LICENSE` for more information.

## 📞 Support

Jika mengalami masalah atau memiliki pertanyaan:
- Buat issue di repository GitHub
- Email: kontak@classy.id


## 🙏 Acknowledgments

- [Flask](https://flask.palletsprojects.com/) - Web framework
- [Chart.js](https://www.chartjs.org/) - Charts library  
- [Bootstrap](https://getbootstrap.com/) - CSS framework
- [Socket.IO](https://socket.io/) - Real-time communication

---

**⭐ Jika project ini membantu, jangan lupa berikan star di GitHub!**
