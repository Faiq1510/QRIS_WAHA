@'
==================================================
   WhatsApp QRIS Payment Gateway Bot
==================================================

Sistem bot WhatsApp otomatis untuk pemesanan dan verifikasi pembayaran QRIS berbasis AI secara real-time.
Dibuat menggunakan WAHA, n8n, Google Gemini AI, dan Google Sheets.

--------------------------------------------------
1. ARSITEKTUR & GAMBARAN UMUM
--------------------------------------------------
Alur Pemesanan:
Pelanggan Chat WA -> Keyword ("pesan vps") -> Form Data (Nama, Email) 
-> Tampilkan QRIS -> Kirim Bukti Transfer -> Gemini AI Verifikasi 
-> [VALID] -> Simpan Sheets & Kirim Kredensial VPS
-> [TIDAK VALID] -> Kirim Alasan Penolakan

Stack Teknologi:
- Docker & Docker Compose : Containerization service
- WAHA (NOWEB Engine)     : WhatsApp Gateway
- n8n (Self-Hosted)       : Workflow Automation
- Cloudflare Tunnel       : Expose n8n lokal ke HTTPS publik
- Google Gemini AI        : OCR & Verifikasi Bukti Bayar
- Google Sheets           : Database Transaksi

--------------------------------------------------
2. LANGKAH INSTALASI
--------------------------------------------------
1. Salin template .env.example menjadi .env:
   N8N_BASIC_AUTH_ACTIVE=true
   N8N_BASIC_AUTH_USER=admin
   N8N_BASIC_AUTH_PASSWORD=GANTI_PASSWORD_ANDA
   CLOUDFLARE_TUNNEL_TOKEN=TOKEN_CLOUDFLARE_ANDA

2. Jalankan Docker Compose:
   docker compose up -d

--------------------------------------------------
3. KONFIGURASI WEBHOOK WAHA
--------------------------------------------------
Gunakan endpoint API WAHA untuk mengeset webhook session:
PUT http://localhost:3000/api/sessions/default
Header: X-Api-Key: GANTI_API_KEY_WAHA
Body JSON:
{
  "config": {
    "webhooks": [
      {
        "url": "http://n8n:5678/webhook/vps-order",
        "events": ["message"]
      }
    ]
  }
}

--------------------------------------------------
4. CATATAN TROUBLESHOOTING & LESSON LEARNED
--------------------------------------------------
- Deduplikasi Pesan: Filter payload.id di n8n untuk mencegah pesan diproses berulang akibat ACK WhatsApp.
- WAHA Phone Number: Gunakan cleanNumber (tanpa suffix @s.whatsapp.net / @lid) sebagai key tunggal staticData.
- Google Sheets: Gunakan operasi "Append or Update Row" dengan match key chatId agar data ter-update, bukan duplikat.
- Gemini API Key: Kirim via header x-goog-api-key, bukan query parameter ?key=.

--------------------------------------------------
5. CHECKLIST STATUS PROYEK
--------------------------------------------------
[x] Docker Compose (WAHA + n8n + Cloudflare) Aktif
[x] Cloudflare Tunnel Terhubung
[x] WAHA Session WORKING
[x] Workflow n8n Active/Published
[x] Integrasi Gemini AI Validasi Pembayaran
[x] Integrasi Google Sheets
[ ] Migrasi ke Payment Gateway Resmi (SNAP BRIAPI / QRIS Dinamis)
[ ] Deploy ke VPS Production
'@ | Out-File -FilePath README.txt -Encoding utf8
