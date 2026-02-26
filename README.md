#  E-Posta Tabanlı Sosyal Medya Yönetim Asistanı

> n8n + OpenAI GPT-4o + Gmail + Telegram entegrasyonu ile otomatik sosyal medya içerik üretimi ve yayınlama sistemi.

---

## Proje Hakkında

Bu proje, Gmail üzerinden gelen e-posta komutlarını yapay zeka ile işleyerek otomatik sosyal medya içeriği üreten ve Telegram kanalına yayınlayan bir otomasyon sistemidir.

**Örnek kullanım:**

```
Konu: instagram post yaz
İçerik: Yapay zeka hakkında motivasyonel bir post yaz, yarın saat 18:00'de paylaş
```

→ Sistem içerik üretir → Sheets'e kaydeder → 18:00'de Telegram'a yayınlar → Sana onay maili gelir ✅

---

## Kullanılan Teknolojiler

| Araç                  | Amaç                      |
| --------------------- | ------------------------- |
| **n8n**               | Otomasyon akış platformu  |
| **OpenAI GPT-4o**     | Yapay zeka içerik üretimi |
| **Gmail API**         | E-posta okuma ve gönderme |
| **Google Sheets API** | İçerik takvimi yönetimi   |
| **Telegram Bot API**  | İçerik yayınlama kanalı   |

---

## Akış Diyagramı

```
Gmail Trigger
    ↓
Switch (platform tespiti)
    ↓
Code (JavaScript - veri ayrıştırma)
    ↓
OpenAI GPT-4o (içerik üretimi)
    ↓
Edit Fields (veri birleştirme)
    ↓
Google Sheets (Append - kayıt)
    ↓
IF (hemen mi, planlı mı?)
    ├── TRUE → Telegram → Sheets (Update) → Gmail (Onay)
    └── FALSE → Wait → Telegram → Sheets (Update) → Gmail (Onay)
```

---

## Kurulum

### 1. Gereksinimler

- [n8n](https://n8n.io) hesabı (ücretsiz)
- OpenAI API anahtarı
- Gmail hesabı (OAuth2)
- Telegram Bot Token ([BotFather](https://t.me/BotFather))
- Google Sheets dosyası

### 2. Google Sheets Hazırlığı

Yeni bir Google Sheets dosyası oluştur ve ilk satıra şu başlıkları ekle:

```
ID | Tarih | Platform | İçerik | Durum | Yayın Saati
```

### 3. Telegram Bot Kurulumu

1. Telegram'da `@BotFather`'a git
2. `/newbot` komutunu gönder
3. Bot adı ve kullanıcı adı belirle
4. API Token'ı kopyala
5. Yeni bir Telegram kanalı oluştur
6. Botu kanala **admin** olarak ekle

### 4. n8n'e Workflow'u İçe Aktar

1. `workflow.json` dosyasını indir
2. n8n'de **Import Workflow** seçeneğine tıkla
3. Dosyayı yükle
4. Aşağıdaki credential'ları bağla:
   - Gmail OAuth2
   - OpenAI API Key
   - Google Sheets OAuth2
   - Telegram Bot Token

### 5. Düğüm Ayarları

**Switch Düğümü:**

- Value: `{{ $json.Subject }}`
- Operatör: String contains
- "Convert types where required" → Açık

**Code Düğümü:** `workflow.json` içindeki kodu kopyala

**OpenAI Düğümü:**

- Model: GPT-4o
- System: Sosyal medya uzmanı prompt'u
- User: `{{ $('Code in JavaScript').item.json.userRequest }}`

**Telegram Düğümü:**

- Chat ID: Kanalın ID'si (`-100XXXXXXXXXX` formatında)
- Text: `{{ $('Edit Fields1').item.json.generatedContent }}` (Expression modunda)

---

## 📨 Kullanım Kılavuzu

### E-posta Formatı

| Alan       | Açıklama                                                                |
| ---------- | ----------------------------------------------------------------------- |
| **Konu**   | `instagram post yaz` / `twitter post yaz` / `linkedin post yaz`         |
| **İçerik** | Ne hakkında içerik istediğini ve ne zaman yayınlanmasını istediğini yaz |

### Örnekler

**Hemen yayınla:**

```
Konu: instagram post yaz
İçerik: Kahve ve verimlilik hakkında samimi bir post yaz, hemen paylaş
```

**Zamanlı yayın:**

```
Konu: instagram post yaz
İçerik: Motivasyonel bir Pazartesi postu hazırla, yarın saat 09:00'da paylaş
```

---

## Dosya Yapısı

```
sosyal-medya-asistani
 ┣ 📄 README.md
 ┣ 📄 workflow.json          # n8n workflow dosyası
 ┣ 📄 SosyalMedyaAsistani_Rapor.pdf  # Proje raporu
 ┗ 📁 screenshots/           # Ekran görüntüleri
    ┣  workflow_genel.png
    ┣  gmail.png
    ┗  telegram_output.png
```

---

## Sorun Giderme

| Hata                       | Çözüm                                                                |
| -------------------------- | -------------------------------------------------------------------- |
| Switch type error          | "Convert types where required" toggle'ını aç                         |
| Telegram "not found"       | Chat ID başına `-100` ekle                                           |
| generatedContent undefined | `$('Edit Fields1').item.json.generatedContent` kullan                |
| Gmail trigger çalışmıyor   | Önce Gmail'den test e-postası gönder, sonra "Fetch Test Event" tıkla |

---

## Geliştirici

**Beyza Taşğın**

---

## Lisans

Bu proje eğitim amaçlı geliştirilmiştir.
