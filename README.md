# Hukuki Belgeler

Bütçe Takip uygulamasının yayın belgeleri. **Dört dosya yüklenir** (her
dilde iki), hepsi **aynı dizine** — aralarındaki bağlantılar göreli.

| Dosya | İçerik |
|---|---|
| `gizlilik-politikasi.html` | **Esas metin.** Gizlilik politikası, aydınlatma metni (`#aydinlatma`), açık rıza beyanları (`#acik-riza`), GDPR (`#gdpr`), veri silme (`#veri-silme`). Play Store'a verilen URL bu. |
| `kullanim-sartlari.html` | Sorumluluk sınırları, garanti reddi, uyuşmazlık çözümü. |
| `privacy-policy.html` | Gizlilik politikasının İngilizce çevirisi (`#disclosure`, `#consent`, `#gdpr`, `#data-deletion`). "Çelişkide Türkçe esas" der. |
| `terms-of-use.html` | Kullanım şartlarının İngilizce çevirisi. Aynı not. |

Uygulama **dört ayrı adres** açar ve hangi dildeki sayfayı açacağını
uygulama dilinden seçer. Eşleşme
[`lib/config/legal_links.dart`](../lib/config/legal_links.dart) dosyasında;
çapaların ve göreli bağlantıların var olduğunu `hukuki_metin_test.dart`
denetliyor. **Türkçe metin değişirse İngilizce de aynı gün değişir** — test
başlık, madde ve tablo satırı sayılarını karşılaştırıyor ama cümlenin
anlamını göremez.

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

## Yayınlarken

1. Dört dosyayı da **aynı dizine** yükleyin ve **AAB'den ÖNCE** yükleyin
   (uygulama yayına çıktığı an bu adresleri açar).
2. Dosya adlarını ve çapa kimliklerini **değiştirmeyin**; uygulama bunlara
   bağlı. Adres değişirse yalnızca `legal_links.dart` içindeki `_base`.
3. Yükledikten sonra sekiz adresi (iki dil × dört) tarayıcıda açıp
   doğrulayın — özellikle çapalı olanların doğru bölüme atladığını.
4. Play Console'daki **Veri Güvenliği** formunu bu belgelerle tutarlı
   tutun.

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
