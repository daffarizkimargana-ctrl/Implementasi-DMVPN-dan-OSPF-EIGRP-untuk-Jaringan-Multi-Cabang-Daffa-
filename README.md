# Implementasi-DMVPN-dan-OSPF-EIGRP-untuk-Jaringan-Multi-Cabang-Daffa-
<h3>projek akhir semester untuk nilai</h3>


spek buat projek (minimal)
<body>1.processor intel i3 - i5 / amd A9, ryzen</body>

<div align="center">

# 🌐 Implementasi DMVPN & OSPF/EIGRP untuk Jaringan Multi-Cabang

### 🚀 Solusi Konektivitas *Site-to-Site* Dinamis, Aman, dan Skalabel Berbasis Hub-and-Spoke

[![Status Proyek](https://img.shields.io/badge/Status-Aktif%20%26%20Teruji-brightgreen?style=for-the-badge&logo=checkmarx)](https://github.com)
[![Topologi](https://img.shields.io/badge/Topologi-DMVPN%20Phase%202%20%2F%203-blue?style=for-the-badge&logo=cisco)](https://github.com)
[![Routing Protocol](https://img.shields.io/badge/Routing-OSPF%20%7C%20EIGRP-orange?style=for-the-badge&logo=graphql)](https://github.com)
[![Keamanan](https://img.shields.io/badge/Enkripsi-IPsec%20%2B%20GRE-red?style=for-the-badge&logo=shield)](https://github.com)
[![Lisensi](https://img.shields.io/badge/Lisensi-MIT-purple?style=for-the-badge)](LICENSE)

*Dokumentasi desain jaringan, analisis protokol routing dinamis, dan skenario pengujian konektivitas antar-kantor cabang tanpa konfigurasi mentah.*

[Ringkasan](#-ringkasan-proyek) •
[Arsitektur](#-arsitektur-jaringan) •
[Perbandingan Protokol](#-perbandingan-ospf-vs-eigrp-di-atas-dmvpn) •
[Fitur Utama](#-fitur-utama) •
[Skenario Uji](#-skenario-pengujian) •
[Struktur Repo](#-struktur-repositori)

</div>

---

## 📖 Ringkasan Proyek

> [!NOTE]
> 💡 **Tujuan Utama:** Menghubungkan kantor pusat (*Hub*) dan banyak kantor cabang (*Spoke*) secara efisien di atas jaringan publik (*Internet/WAN*) menggunakan **DMVPN** (*Dynamic Multipoint VPN*) yang dikombinasikan dengan protokol *routing* dinamis (**OSPF** atau **EIGRP**).

Seiring bertambahnya jumlah kantor cabang, *tunnel* IPsec statis *point-to-point* menjadi sulit dipelihara dan tidak efisien. Proyek ini merancang dan memvalidasi arsitektur jaringan multi-cabang yang mampu:

* 🔀 Membentuk *tunnel* dinamis antar-lokasi tanpa menambah *interface* statis baru di sisi *Hub* saat cabang baru bergabung.
* ⚡ Mengoptimalkan jalur komunikasi langsung antar-cabang (*Spoke-to-Spoke*) bila dibutuhkan.
* 🛡️ Melindungi seluruh lalu lintas data menggunakan enkripsi **IPsec** di atas *tunnel* **mGRE** (*Multipoint GRE*).
* 🔄 Menjaga konvergensi rute secara otomatis ketika terjadi gangguan jalur utama atau *failover*.

---

## 🏗️ Arsitektur Jaringan

Jaringan dibagi ke dalam beberapa lapisan fungsional agar mudah dikelola dan dikembangkan:

| Komponen / Lapisan | Peran Utama | Karakteristik Kunci |
| :--- | :--- | :--- |
| 🏢 **Router Hub (Kantor Pusat)** | Pusat kontrol *overlay*, *NHRP server*, dan *route reflector* | Memiliki IP publik statis, menangani registrasi *Spoke* |
| 🏬 **Router Spoke (Kantor Cabang)** | *Gateway* jaringan lokal cabang menuju *Hub* dan cabang lain | Dapat menggunakan IP publik dinamis/statis, mendaftar ke *Hub* |
| 🔐 **Lapisan Tunnel (mGRE + IPsec)** | Membentuk jalur virtual terenkripsi di atas jaringan *underlay* | Mengurangi kompleksitas konfigurasi *point-to-point* |
| 🧭 **Lapisan Routing (OSPF / EIGRP)** | Pertukaran informasi prefiks LAN antar-cabang secara otomatis | Mendukung *failover* rute dan pemilihan jalur terbaik |

### Alur Kerja Singkat
1. **Underlay Ready:** Setiap *router* memiliki konektivitas dasar ke jaringan transportasi (*ISP/WAN*).
2. **Registrasi NHRP:** *Spoke* mendaftarkan pemetaan IP *Tunnel* ke IP *Underlay*-nya kepada *Hub* melalui **NHRP** (*Next Hop Resolution Protocol*).
3. **Pertukaran Rute:** **OSPF** atau **EIGRP** mendistribusikan rute subnet internal tiap cabang melewati *tunnel* virtual.
4. **Komunikasi Data:** Lalu lintas antar-lokasi diteruskan sesuai desain fase DMVPN dan kebijakan *routing* yang diterapkan.

---

## ⚖️ Perbandingan OSPF vs EIGRP di Atas DMVPN

Pemilihan protokol *routing* sangat memengaruhi stabilitas *neighbor*, *traffic overhead*, dan perilaku komunikasi *Spoke-to-Spoke*:

| Aspek Evaluasi | 🟦 OSPF | 🟧 EIGRP |
| :--- | :--- | :--- |
| **Jenis Protokol** | *Link-State* (standar terbuka) | *Advanced Distance-Vector* |
| **Penyesuaian di DMVPN** | Memerlukan perhatian pada tipe *network* (*point-to-multipoint* / *broadcast*) & *DR/BDR* | Relatif lebih ringkas; perlu perhatian pada *split horizon* di antarmuka *mGRE* |
| **Skalabilitas Cabang Besar** | Sangat baik dengan pembagian *Area* yang terstruktur | Baik dengan perancangan *summarization* yang tepat |
| **Konvergensi & Failover** | Cepat melalui mekanisme LSA & SPF | Cepat melalui *Feasible Successor* (DUAL) |
| **Kapan Dipilih** | Lingkungan *multi-vendor* atau kebutuhan standar terbuka ketat | Lingkungan homogen yang mengutamakan fleksibilitas metrik & *summarization* |

> [!TIP]
> 🎯 **Rekomendasi Desain:** Gunakan **OSPF** jika mengutamakan interoperabilitas standar terbuka dan segmentasi area bertingkat; pilih **EIGRP** jika ingin memanfaatkan metrik komposit dan *summarization* yang luwes pada topologi *hub-and-spoke*.

---

## ✨ Fitur Utama

* 🟢 **Skalabilitas Multi-Cabang:** Penambahan cabang baru tidak menuntut perubahan besar pada struktur *tunnel* di sisi pusat.
* 🔵 **Dukungan Dua Protokol Routing:** Memungkinkan perbandingan langsung perilaku **OSPF** dan **EIGRP** pada skenario jaringan yang sama.
* 🟣 **Keamanan End-to-End:** Memadukan autentikasi, integritas, dan enkripsi IPsec untuk lalu lintas *control plane* maupun *data plane*.
* 🟡 **Redundansi & Pemulihan:** Mendukung jalur cadangan dan pengujian pemutusan tautan untuk melihat waktu pemulihan rute.
* 🟠 **Dokumentasi Terstruktur:** Fokus pada pemahaman arsitektur, alur paket, dan validasi operasional.

---

## 🧪 Skenario Pengujian

Proyek ini dirancang untuk menguji beberapa kondisi operasional berikut:

1. ✅ **Verifikasi Neighbor & Tunnel:** Memastikan status *tunnel* aktif, pemetaan NHRP terdaftar, dan *adjacency* OSPF/EIGRP terbentuk stabil.
2. ✅ **Konektivitas Hub ↔ Spoke:** Menguji latensi, *packet loss*, dan keterjangkauan subnet LAN antara pusat dan seluruh cabang.
3. ✅ **Konektivitas Spoke ↔ Spoke:** Memvalidasi jalur komunikasi antar-cabang sesuai fase DMVPN yang digunakan.
4. ✅ **Simulasi Gangguan Jalur (*Link Down*):** Memutus salah satu tautan untuk mengamati *failover* rute dan pemulihan otomatis saat tautan kembali aktif.
5. ✅ **Penambahan Cabang Baru:** Menambahkan satu *Spoke* simulasi untuk memastikan integrasi berjalan mulus tanpa mengganggu cabang eksisting.

---

## 📁 Struktur Repositori

```text
📦 dmvpn-ospf-eigrp-multibranch
 ┣ 📂 kongiurasi/                  # Catatan desain, diagram topologi, dan analisis protokol
 ┣ 📂 topologi/              # gambar
 ┗ 📄 README.md              # Dokumentasi utama proyek
