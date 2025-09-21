# Google Messages Web Client

## Bu Nedir?
Bu kütüphane, [Google Messages](https://messages.android.com) web arayüzünü kullanarak SMS gönderme işlevselliği sağlayan bir Node.js kütüphanesidir. Kendi telefon numaranızı kullanarak bir SMS gateway API'si gibi çalışabilir ve başkalarına mesaj gönderebilirsiniz. Bu modül özellikle OTP mesajları göndermek için backend uygulamalarında (Express.js gibi) kullanılabilir.

## Özellikler
- ✅ QR kod ile kolay kimlik doğrulama
- ✅ Oturum yönetimi ve kimlik bilgileri kaydetme
- ✅ SMS gönderme işlevselliği
- ✅ TypeScript desteği
- ✅ Puppeteer tabanlı güvenilir otomasyon

## Kurulum

NPM'den paketi yükleyin:

```bash
npm install messages-web
```

## Kullanım

### 1. Kimlik Bilgileri Olmadan Kullanım

```javascript
const { default: MessagesClient } = require('messages-web')
const fs = require('fs')

const client = new MessagesClient()

client.on('qr-code', (base64Image) => {
    // QR kodunu kaydetme örneği
    fs.writeFileSync('qr.jpg', base64Image.replace(/^data:image\/png;base64,/, ""), { encoding: 'base64' })
    console.log('QR kod kaydedildi! Google Messages uygulamanızla tarayın.')
})

client.on('authenticated', async (service) => {
    const inbox = service.getInbox()
    const credentials = await client.getCredentials()
    fs.writeFileSync('credentials.json', JSON.stringify(credentials, null, '\t'))
    console.log('Kimlik bilgileri kaydedildi!')
    await client.quit()
})
```

### 2. Kaydedilmiş Kimlik Bilgileri ile Kullanım

```javascript
const { default: MessagesClient } = require('messages-web')

const credentials = MessagesClient.loadCredentialFile('credentials.json')
const client = new MessagesClient({ credentials })

client.on('authenticated', async (service) => {
    const inbox = await service.getInbox()
    console.log('Gelen Kutusu:', inbox)
    await client.quit()
})
```

### 3. Mesaj Gönderme

```javascript
const { default: MessagesClient } = require('messages-web')

const credentials = MessagesClient.loadCredentialFile('credentials.json')
const client = new MessagesClient({ credentials })

client.on('authenticated', async (service) => {
    console.log('Mesaj gönderiliyor...')
    const result = await service.sendMessage('+905551234567', 'Merhaba! Bu bir test mesajıdır.')
    console.log('Mesaj gönderildi!', result.statusMessage)
    await client.quit()
})
```

## API Referansı

### MessagesClient

#### Constructor
```javascript
new MessagesClient(options)
```

**Seçenekler:**
- `headless`: boolean | "new" - Tarayıcının görünür olup olmayacağı (varsayılan: "new")

#### Events

- `qr-code`: QR kod değiştiğinde tetiklenir
- `authenticated`: Kimlik doğrulama tamamlandığında tetiklenir
- `browser-launched`: Tarayıcı başlatıldığında tetiklenir

#### Metodlar

- `getCredentials()`: Mevcut oturum kimlik bilgilerini döndürür
- `loadCredentialFile(path)`: Dosyadan kimlik bilgilerini yükler
- `quit()`: Tarayıcıyı kapatır

### MessageService

#### Metodlar

- `sendMessage(to, message)`: Belirtilen numaraya mesaj gönderir
  - `to`: string - Ülke kodu ile birlikte telefon numarası (örn: "+905551234567")
  - `message`: string - Gönderilecek mesaj metni
  - **Döndürür:** `{ statusMessage: string }` - Mesaj durumu

- `getInbox()`: Gelen kutusundaki mesajları döndürür

## Örnekler

Proje içinde daha fazla örnek için [examples](https://github.com/SwapnilSoni1999/messages-web/tree/main/examples) klasörüne bakın.

## Gereksinimler

- Node.js 14+
- Google hesabı
- Google Messages uygulaması (telefon için)

## Sorun Giderme

### QR Kod Tarama Sorunu
- Google Messages uygulamasının güncel olduğundan emin olun
- İnternet bağlantınızı kontrol edin
- QR kodun net ve okunabilir olduğundan emin olun

### Mesaj Gönderme Sorunu
- Telefon numarasının doğru formatta olduğundan emin olun (+ülke kodu + numara)
- İnternet bağlantınızı kontrol edin
- Google Messages web arayüzünün erişilebilir olduğundan emin olun

## Geliştirme Durumu

Bu proje aktif olarak geliştirilmektedir. Mevcut özellikler:
- ✅ Temel SMS gönderme
- ✅ QR kod kimlik doğrulama
- ✅ Oturum yönetimi
- 🔄 Gelen mesajları okuma (geliştiriliyor)
- 🔄 Grup mesajları (planlanıyor)
- 🔄 Medya gönderme (planlanıyor)

## Lisans
ISC - Swapnil Soni ©

## Katkıda Bulunma

Katkılarınızı bekliyoruz! Lütfen önce bir issue açın veya mevcut issue'ları kontrol edin.

## Destek

Sorunlarınız için GitHub Issues kullanın veya proje sahibi ile iletişime geçin.
