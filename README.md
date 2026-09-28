# Hukuki Belgeler

Bütçe Takip uygulamasının yayın belgeleri. **İki dosya yüklenir**, ikisi de
**aynı dizine** — aralarındaki bağlantılar göreli.

| Dosya | İçerik |
|---|---|
| `gizlilik-politikasi.html` | Üç belge tek sayfada: gizlilik politikası, aydınlatma metni (`#aydinlatma`), açık rıza beyanları (`#acik-riza`). Play Store'a verilen URL bu olmalı. |
| `kullanim-sartlari.html` | Sorumluluk sınırları, garanti reddi, uyuşmazlık çözümü. Gizlilik belgesi değil, sözleşme olduğu için ayrı tutuldu. |

Uygulama **dört ayrı adres** açar; ikisi aynı sayfanın çapalarına gider.
Eşleşme [`lib/config/legal_links.dart`](../lib/config/legal_links.dart)
dosyasında.

Belgeler koddaki gerçek davranışla satır satır karşılaştırılarak yazıldı.

> Daha önce dört ayrı dosyaydı. Yüklenecek dosya sayısını azaltmak ve
> birini unutup kırık bağlantı bırakma riskini düşürmek için üçü
> birleştirildi; içerik aynen korundu.

---

## ⛔ Yayınlamadan önce kodda tamamlanması gerekenler

Belgeler aşağıdaki dört şeyin var olduğunu söylüyor. **Şu an hiçbiri yok.**
Belgeler bu hâliyle yayınlanırsa, olmayan mekanizmalar vaat edilmiş olur —
düzeltilmek istenen sorunun aynısı tekrar eder.

### ✅ 1. Kişiselleştirilmiş reklam için açık rıza ekranı — TAMAM

İlk açılışta [`consent_dialog.dart`](../lib/screens/consent_dialog.dart)
gösteriliyor. Cevap [`consent_service.dart`](../lib/services/consent_service.dart)
tarafından saklanıyor ve her reklam isteğine uygulanıyor:
`AdRequest(nonPersonalizedAds: !allowsPersonalized)`.

Rıza alınmadan **ve henüz sorulmadıysa** kişiselleştirme kapalı — istek
diyalogdan önce çıkarsa bile güvenli tarafta kalınıyor.

Google'ın UMP aracı bilerek kullanılmadı: formu AdMob konsolundan yönetiliyor,
metni GDPR'a göre yazılmış ve varsayılan olarak yalnızca AB/İngiltere'ye
gösteriliyor. Burada gereken KVKK açık rızası ve `acik-riza-metni.html`'deki
beyan metni.

**AB'ye açılırsan:** Google, EEA trafiği için ayrıca bir CMP şart koşuyor.
O zaman UMP'yi buna ek olarak kurmak gerekir.

### ✅ 2. Ayarlar > Reklam Tercihleri — TAMAM

[`ad_preferences_screen.dart`](../lib/screens/ad_preferences_screen.dart).
Tek dokunuşluk anahtar; onay penceresi veya caydırıcı adım yok — KVKK geri
almanın en az vermek kadar kolay olmasını gerektiriyor.

### ✅ 3. Ayarlar > Lisanslar — TAMAM

Ayarlar > Yasal > Lisanslar. Flutter'ın `showLicensePage()` fonksiyonu.

### ✅ 4. Ayarlar'dan aydınlatma metni ve kullanım şartlarına bağlantı — TAMAM

Ayarlar'a "Yasal" bölümü eklendi: Gizlilik Politikası, Aydınlatma Metni,
Kullanım Şartları, Lisanslar. Adresler tek yerden yönetiliyor:
[`lib/config/legal_links.dart`](../lib/config/legal_links.dart).

---

## ⛔ Kalan tek engel: belgeler henüz yayında değil

Uygulama içindeki bağlantılar aşağıdaki adresleri açıyor ama oraya hâlâ
**15 Haziran tarihli eski metin** yüklü. Dört belgeyi de yüklemeden
yayınlanmamalı.

## Yayınlarken

1. İki dosyayı da **aynı dizine** yükleyin — aralarındaki bağlantılar göreli.
2. Dosya adlarını ve `#aydinlatma` / `#acik-riza` çapa kimliklerini
   **değiştirmeyin**. Uygulama bunlara bağlı:
   [`lib/config/legal_links.dart`](../lib/config/legal_links.dart)
   Adres değişirse yalnızca o dosyadaki `_base` güncellenir.
3. Yükledikten sonra dört adresi de tarayıcıda açıp doğrulayın — özellikle
   çapalı olanların doğru bölüme atladığını.
4. Eski taslakları siteden **silin**. Yayında kalan güncel olmayan bir
   gizlilik metni arama motorlarınca indekslenir ve ileride yanlış belgeye
   bakılmasına yol açar.
4. Play Console'daki **Veri Güvenliği (Data Safety)** formunu bu belgelerle
   tutarlı doldurun. Özellikle bildirim erişimi izni için Play'in ayrı bir
   beyan/gerekçe istediğini kontrol edin.

---

## Belgelerin dayandığı kod gerçekleri

Aşağıdakiler belgelere işlendi. Bu davranışlar değişirse belgeler de
güncellenmelidir.

| Konu | Koddaki durum | Kaynak |
|---|---|---|
| Bütçe verisi | Yalnızca cihazda, `SharedPreferences` | `lib/services/storage_service.dart`, `finance_center.dart` |
| Google Otomatik Yedekleme | **Açık.** Tüm bütçe verisi kullanıcının kendi Google hesabına yedekleniyor | `AndroidManifest.xml` (`allowBackup="true"`), `res/xml/backup_rules.xml`, `res/xml/data_extraction_rules.xml` |
| Bildirim okuma | Opt-in. Tutar + banka adı + metnin ilk 120 karakteri cihazda saklanıyor, yedeğe **dâhil değil** | `BudgetNotificationListener.kt`, `lib/services/spend_detector_service.dart` |
| Sosyal medya filtresi | WhatsApp, Telegram, Instagram, Facebook, X, YouTube ve sistem paketleri hariç tutuluyor | `BudgetNotificationListener.kt` &rarr; `EXCLUDED_PACKAGES` |
| Kur servisi | Parametresiz `GET`; hiçbir veri gönderilmiyor. Ağ yoksa önbellek, o da yoksa sabit kur | `lib/services/currency_service.dart` |
| Reklam | AdMob banner + her 4 harcamada bir geçiş reklamı | `lib/services/ad_service.dart` |
| Analitik / çökme raporu | **Yok.** `pubspec.yaml`'da Firebase, Crashlytics veya analytics paketi bulunmuyor | `pubspec.yaml` |
| Dışa aktarma | Kullanıcı başlatır: Excel ve JSON yedek, sistem paylaşım menüsüyle | `lib/services/excel_export_service.dart`, `backup_service.dart` |
