# Vercel’de Bazı Şeyler Çalışmıyor – Kontrol Listesi

## 1. API’nin ayakta olduğunu kontrol et

Tarayıcıda şunu aç:

```
https://kirmizi-cizgi-world-monitor.vercel.app/api/version
```

- **JSON** (version, tag vb.) görüyorsan → API tarafı çalışıyor.
- **404 / 500 / sayfa açılmıyor** → Build veya deploy sorunu var.

---

## 2. Environment variable’lar (sunucu tarafı – VITE_ YOK)

Bu key’ler **sadece sunucuda** (Edge/API) kullanılır. İsimleri **aynen** böyle olmalı (başında `VITE_` olmamalı):

| Değişken | Ne işe yarar |
|----------|----------------|
| `GROQ_API_KEY` | Yapay zeka özetleme |
| `OPENROUTER_API_KEY` | Yapay zeka yedek |
| `UPSTASH_REDIS_REST_URL` | Cache (opsiyonel) |
| `UPSTASH_REDIS_REST_TOKEN` | Cache (opsiyonel) |
| `FINNHUB_API_KEY` | Piyasa verisi |
| `EIA_API_KEY` | Enerji verisi |
| `FRED_API_KEY` | Ekonomi verisi |
| `ACLED_ACCESS_TOKEN` | Çatışma verisi |
| `UCDP_ACCESS_TOKEN` | Çatışma verisi |
| `CLOUDFLARE_API_TOKEN` | İnternet kesintileri |
| `NASA_FIRMS_API_KEY` | Uydu yangın verisi |
| `WINGBITS_API_KEY` | Uçak takibi (opsiyonel) |

**Yanlış:** `VITE_GROQ_API_KEY`  
**Doğru:** `GROQ_API_KEY`

---

## 3. Env ekledikten / değiştirdikten sonra mutlaka Redeploy

Vercel’de env değişince **mevcut deployment değişmez**. Yeni değerler sadece **yeni bir deploy**’da kullanılır.

1. **Deployments** sekmesi → en üstteki deployment → **⋯** → **Redeploy**  
   veya  
2. **Settings → Environment Variables** sayfasında “Redeploy” uyarısı varsa oradan redeploy et.

---

## 4. Vercel’de Build ayarları

- **Build Command:** `npm run build` (veya `tsc && vite build`)
- **Output Directory:** `dist`
- **Install Command:** `npm install`

Framework Preset: **Vite** seçili olmalı.

---

## 5. Hangi özellik nereden besleniyor?

| Çalışmıyorsa | Kullanılan API / env |
|--------------|------------------------|
| Yapay zeka özeti / Dünya brifingi | `GROQ_API_KEY` veya `OPENROUTER_API_KEY` |
| Stratejik durum kartı (tiyatro postürü) | `/api/military/v1/get-theater-posture` (OpenSky/relay) |
| Piyasa verileri | `FINNHUB_API_KEY` |
| Enerji / petrol | `EIA_API_KEY` |
| Ekonomi (FRED) | `FRED_API_KEY` |
| Çatışma / protesto | `ACLED_ACCESS_TOKEN`, `UCDP_ACCESS_TOKEN` |
| İnternet kesintileri | `CLOUDFLARE_API_TOKEN` |

Env yoksa ilgili özellik devre dışı kalır veya “veri yok” gösterir.

---

## 6. Vercel Function Logs’a bakma

Hata ayıklamak için:

1. Vercel Dashboard → projen → **Logs** (veya **Deployments** → bir deployment → **Functions**).
2. **Runtime Logs** / **Function Logs** kısmında 5xx veya “Error” aramak.
3. Özellikle şu path’ler için hata var mı bak:
   - `/api/news/v1/summarize-article` (yapay zeka)
   - `/api/military/v1/get-theater-posture` (stratejik durum)

Buradaki hata mesajı env eksikliği, timeout veya harici API hatasını gösterebilir.

---

## 7. Kısa özet

1. `/api/version` ile API’nin çalıştığını doğrula.  
2. Sunucu key’lerini **VITE_** olmadan ekle; env’den sonra **Redeploy** yap.  
3. Build/Output ayarlarının doğru olduğundan emin ol.  
4. Hâlâ çalışmıyorsa **Vercel Function Logs**’ta ilgili path’ler için hata mesajına bak.
