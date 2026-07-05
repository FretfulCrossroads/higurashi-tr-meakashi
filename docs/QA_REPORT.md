# Meakashi TR Çeviri — Kalite Değerlendirme Raporu

> **İnceleme**: Claude Opus 4.7 (Anthropic)
> **Tarih**: Temmuz 2026
> **Kapsam**: `json_files/` altındaki tüm JP kaynak ve TR çeviri dosyaları (~88 dosya, ~800.000 karakter Türkçe metin)
> **Yöntem**: Otomatik format taraması (88 dosya, tamamı) + hedefli örnekleme (25+ dosya, satır bazlı JP↔TR karşılaştırma) + chunk-boyu tutarsızlık taraması

Bu belge, ilk sürüm öncesi yapılan bağımsız kalite değerlendirmesinin özetidir. Tespit edilen sorunlar sürüm öncesinde düzeltilmiştir; belge bir "hata listesi" değil, çeviri süreci ve kalitesi hakkında oyuncular için **şeffaflık belgesi** olarak paylaşılmaktadır.

---

## Özet

Meakashi bölümünün Türkçe çevirisi *Gemini 3.1 Flash Lite* ile üretilmiş, özel bir post-process script'iyle temizlenmiş ve bu QA turundan geçtikten sonra tespit edilen sorunlar giderilerek yayımlanmıştır.

**Genel kalite değerlendirmesi**: Fiyat-performans açısından yüksek (~8.5/10 tahmini). Sistemik hatalar sınırlı, çoğu bulgu tekil satır düzeyinde.

**Çevirinin güçlü yanları:**
- Karakter isimleri ve honorifikler (Shion, Mion, Satoshi-kun, Oyashiro-sama, Kasai, Rika-chan, vb.) tutarlı Rōmaji ile korunmuş
- JSON yapısı ve satır numaraları %100 sağlam — oyun motoru için format bozan hata yok
- Higurashi'ye özgü terimler (Watanagashi, Onikakushi, Furude Tapınağı, Üç Büyük Aile, ceza oyunu, boynuz törpüleme) doğru kullanılmış
- Onomatope ve karakter tikleri (Rika'nın "mii~/nipah~", Rena'nın uzatmaları, Satoko'nun kibirli register'ı, Ōishi'nin babacan-mesleki tonu) çoğunlukla yakalanmış
- Kültürel/edebi register uygun — dini terimlerde "gazap/adak dansı/miko", polis dilinde "emniyet müdürlüğü/vekil", modern argoda "sürprizbozan (spoiler)/turnusol kağıdı"

---

## Değerlendirmede tespit edilen ve düzeltilen sorunlar

Aşağıda, bu QA turunda bulunup **ilk sürümden önce giderilen** başlıca sorun kategorileri özetlenmiştir. Oyuncular bu belgeyi okuyarak nelerin kontrol edilip düzeltildiğini ve çevirinin hangi tür hatalara karşı gözden geçirildiğini görebilir.

### 1. Chunk-boyu çeviri tutarsızlıkları

İki dosyada, LLM'in o batch için görevi yanlış yorumladığı yaklaşık 60 satırlık bloklar tespit edildi:

- `_meak_004`: Bir bölüm Türkçe yerine İngilizce üretilmişti.
- `_meak_005`: Bir bölüm Türkçe yerine rōmaji (JP'nin Latin harfli okunuşu) üretilmişti.

Bu bloklar yeniden çevrildi. **Diğer 86 dosyada benzer bir chunk-hatası tespit edilmedi.**

### 2. Format sorunları

- **JP köşe parantezleri** (`「` `」`) — 10 dosyada 56 satırda, dialog etrafındaki JP karakter parantezleri Türkçe eskape formatına dönüştürülmemişti. Post-process script'ine ilgili dönüşüm eklenip toplu düzeltildi.
- **Apostrof kaçış hatası** — Türkçe iyelik apostrofları bazı satırlarda `\"` veya `\'` olarak yanlış eskape edilmişti (örn. `Satoshi-kun\"un`). Regex ile toplu düzeltildi.
- **Fullwidth JP parantez ve noktalama artıkları** — Nadir örneklerde `（）` veya `。` gibi karakterler kalmıştı, temizlendi.

### 3. Terminoloji tutarlılığı

- `シスター` (Katolik yatılı okul bağlamındaki "rahibe") bazı yerlerde "kız kardeş" olarak çevrilmişti. St. Lucia Academy sahnelerinde toplu düzeltildi.
- `Shōwa` dönem adı üç farklı yazımla geçiyordu (`Şowa`/`Showa`/`Shōwa`); projenin diğer terimlerine (`Ōishi`, `Oryō`, `Hōjō`) uyum için `Shōwa` olarak standartlaştırıldı.

### 4. Tek satır doğruluk hataları

Örnekleme sırasında yaklaşık 15–20 belirgin doğruluk hatası tespit edildi. Örnekler:

- Deyim yanlış çevirisi (`石橋を叩いて渡る` — "aşırı temkinli olmak" deyimi kelime kelime çevrilmişti)
- Saat hatası (`20時5分前` = 19:55, "20:55" yazılmıştı)
- Özne kaymaları (JP'de gizli özne yorumlandığında yön yanlışları)
- Bir satırda çevrilmemiş kalan JP kanjisi (`即興`)
- Tek bir satırda tamamen çevrilmemiş bırakılan JP metin

Bunlar elle veya hedefli yeniden geçişle düzeltildi.

### 5. Bad-end sürüm tutarlılığı

`_meak_024a` ve `_meak_024b` aynı sahnenin iki farklı bad-end kolu olduğundan bazı ortak JP satırları farklı Türkçelere sahipti. Tercih edilen sürümler seçilerek eşitlendi.

---

## Kapsam ve sınırlamalar

Bu inceleme:

- **Statik JSON dosyaları üzerinden** yapılmıştır. Oyun içi bağlam (metin balonu boyutu, karakter portresi eşleşmesi, ses-metin senkronizasyonu) değerlendirilmemiştir.
- **Örnekleme tabanlıdır**. `_meak_001` satır satır incelenmiş, diğer dosyalar hedefli örnekleme ile taranmıştır. Otomatik format taraması 88 dosyayı tamamen kapsar.
- **Playtest'in yerine geçmez**. Oyun akışı içindeki tuhaflıklar, sahne kırılmaları ve karakter sesi kaymaları yalnızca oynayarak fark edilebilir.

Değerlendirme sonrası düzeltmelere rağmen çeviri:

- **AI destekli üretilmiştir**. Otomatik üretim + insan/AI gözden geçirme kombinasyonuyla hazırlanmıştır; bir çevirmenin baştan sona yaptığı çeviriyle aynı seviyede kalite garantisi sunmaz.
- **Örnekleme sınırları içinde tespit edilemeyen** tek-satır doğruluk hataları veya ton kaymaları kalmış olabilir. Bu hataların büyük çoğunluğu okuma deneyimini bozmayacak seviyededir; olay örgüsünü veya karakter tanımlamasını etkileyecek hatalar bu QA turunda özellikle aranmış ve düzeltilmiştir.

Genel tahmin: **satır bazında %1'den az kalan hata oranı**.

---

## Katkıda bulunma

Oyunda tuhaf/hatalı gördüğünüz her satır değerlidir. GitHub Issues üzerinden bildirim yaparken şunları eklemeniz düzeltmeyi hızlandırır:

- **Dosya adı ve satır numarası** (`json_files/` altındaki ilgili `_translated.json`, örn. `_meak_017[214]`)
- **Ekran görüntüsü** (varsa)
- **Sorun türü**: yanlış çeviri / cümle akışı / karakter tonu / format bozukluğu / tipografi
- **Öneriniz** (opsiyonel): sizin önerdiğiniz Türkçe karşılık

Küçük yazım hataları veya cümle akışı düzeltmeleri kadar, olay örgüsünü etkileyen doğruluk hataları da (özellikle uzun monolog sahnelerinde) memnuniyetle karşılanır.
