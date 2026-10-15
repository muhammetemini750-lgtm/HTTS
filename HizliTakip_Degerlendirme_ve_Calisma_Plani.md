# HızlıTakip Taşıma Sistemi — Değerlendirme ve Çalışma Planı

*Hazırlayan: Claude · 15 Ekim 2026*
*Kaynak: "HızlıTakip Taşıma Sistemi — Ana Çalışma ve Devir Dosyası" (11 Ekim 2026) ve bu sohbet*

---

## 1. Proje özeti

Personel ve öğrenci taşımacılığı yapan herkesin kullanacağı bir program geliştiriliyor. Fabrika ve okul gibi kurumlar kendi personel ve öğrencilerini takip edecek. Uygulama web tabanlı olacak ve iOS/Android'de de kullanılacak.

Mevcut durum (devir dosyasına göre):

- Yayın platformu Vercel, proje adı `tasima-sistemi-v2`; veritabanı Neon.
- Canlı V2'de hatalar var: öğrenci ekleme çalışmıyor, toplu işlemler hata veriyor.
- Bazı kayıtlar hâlâ tarayıcıdaki `localStorage`'da; Neon entegrasyonu tamamlanmamış.
- Gerçek SMS doğrulaması yok.
- Güncel kaynak kodun yeri (Git repo, commit, deployment) doğrulanmamış; elde eski ZIP arşivleri var.

Not: Bağlantı olarak paylaşılan ChatGPT sayfası okunamadı (yalnızca başlık geldi). Değerlendirme, daha sonra yüklenen `.md` devir dosyası üzerinden yapıldı.

---

## 2. Genel değerlendirme

Belge kod değil; iki eski ChatGPT sohbetinden çıkarılmış bir gereksinim ve devir belgesi. Yazılımın iskeleti olarak ciddi bir temel oluşturuyor, ancak henüz çalışan bir program yok. Belgenin kendisi de bunu dürüstçe söylüyor.

### Güçlü yanlar

- **Rol ve yetki ayrımı iyi düşünülmüş.** Okul yalnızca kendi öğrencisini, partner taşıyıcı yalnızca kendisine verilen işi, yolcu yalnızca kendi seferini görüyor.
- **Karma (taşımalı) kart mantığı tutarlı.** Kaynak rozeti ve kilitli kayıt fikri, aynı öğrencinin iki yerden düzenlenip çakışmasını önlüyor.
- **Öğrenci kodunun sistem tarafından üretilmesi doğru karar.** Excel'den veya kullanıcıdan kod alınmaması veri kirliliğini azaltır.
- **İşlem kaydında gerçek aktörün görünmesi** ihtilaf durumunda işe yarar.
- **Dürüst durum tablosu.** "Tamamlandı" denmemiş, test edilmeyen işler açıkça işaretlenmiş.

### Asıl sorunlar

**1. Teknik durum zayıf**

- Canlı sürümde bilinen hatalar var; kalıcı kayıt akışı tamamlanmamış.
- `localStorage` verisi tarayıcıya bağlıdır, cihazlar arasında paylaşılmaz ve silinebilir.
- Güncel kaynak kod bulunamıyor; ZIP'lerle ilerlemek bu büyüklükte bir sistemde sürdürülemez.
- **Öneri:** Yeni özellik eklemeden önce kod bir Git deposuna (GitHub gibi) alınmalı.

**2. KVKK ve güvenlik belgede neredeyse yok**

- Çocukların adresi, telefonu ve canlı konumu işleniyor. Veli aydınlatma metni, açık rıza, saklama süresi ve silme hakkı belgede geçmiyor. Bu, yayından önce çözülmesi gereken bir şart.
- "Sunucuda yetki kontrolü" deniyor ama nasıl yapılacağı tanımlı değil. Neon'da satır bazlı kiracı ayrımı (her sorguda kurum filtresi ya da Row Level Security) tasarlanmalı.
- IBAN ve ödeme bilgileri hassas veridir; erişim kuralları ayrıca yazılmalı.

**3. Kapsam çok geniş**

Yakıt fişi OCR'ı, e-fatura/e-arşiv, KDV/tevkifat, mutabakat, anket, teklif, sesli komut, belge bitiş tarihinin otomatik okunması, beş farklı tema/görünüm modu aynı anda isteniyor. Küçük bir ekiple bu, hiçbir şeyin bitmemesi demek.

**Öneri:** Önce bir çekirdek (MVP) belirlenmeli; finans, fatura, yakıt ve OCR ikinci aşamaya kalmalı.

**4. Mobil strateji belirsiz**

Canlı konum, arka planda çalışma ve bildirim (özellikle iOS'ta) PWA ile sınırlı kalır. Şoför uygulaması için büyük olasılıkla yerel ya da hibrit uygulama (React Native, Flutter, Capacitor) gerekecek. Veli ve yönetim tarafı PWA olabilir.

**5. Mantık tutarsızlıkları ve belirsizlikler**

- **Lisans:** "7 gün sonra askıya alma" kuralının tetikleyicisi belli değil; "1 kullanıcı 1 sefer" ifadesi anlaşılmıyor.
- **Telefon numarasının benzersizliği:** Bir veli birden fazla öğrenciye bağlanabilir, bir kişi hem veli hem personel olabilir. Telefon tek başına kimlik olursa rol çakışması çıkar; giriş sonrası rol seçimi ekranı gerekir.
- **Okul paneli görünürlüğü:** Araç/doluluk gösterilmiyor ama "araç, şoför, güzergâh görülebilir" deniyor. Hangi alanın görüneceği alan alan netleşmeli.
- **Sefer kapanışı:** Canlı konum "sefer süresince" açık ve "25 dakika açık kalan sefer uyarısı" var; seferin otomatik kapanma kuralı yok.
- **Fabrika yönetim modları:** Fabrika Aktif, Ortak Yönetim ve Firma Yönetiyor modlarında kimin neyi onaylayabildiği bir yetki matrisi olarak yazılmalı.
- **Erken ayrıntılar:** Sağ panel ve tema ayrıntıları, veri modeli oturmadan çok erken.

### Dil ve anlatım

- Belge yarı gereksinim, yarı sohbet devir notu karışımı. Asıl çalışma belgesi ile "yeni sohbette kullanılacak talimat" ayrı dosyalar olmalı.
- Kimlikler, ZIP envanteri ve yedek bilgileri ürün gereksiniminden ayrılıp ayrı bir "teknik kayıt" bölümüne taşınmalı.
- Vercel proje ID'si ve hesap adını içeren URL, belge paylaşılırken dikkatle ele alınmalı.
- Yazım büyük ölçüde temiz. "Karma / Taşımalı / Taşımalı-Karma" gibi iki üç isimlendirme tek isme indirilmeli.

---

## 3. Önerilen sıra

1. Kaynak kodu bir Git deposuna taşı, kurtarılabilir hale getir.
2. KVKK/rıza metinlerini ve yetki matrisini yaz.
3. Neon veri modelini ve kiracı ayrımını kesinleştir.
4. Küçük bir MVP kapsamı seç ve yalnızca onu uçtan uca (kalıcılık, telefon doğrulama, yetki testi) bitir.
5. Mobil yaklaşımı (PWA mı, yerel şoför uygulaması mı) karara bağla.
6. Finans, yakıt/OCR ve tema ince ayarlarını sonraya bırak.

---

## 4. Birlikte yapılacak çalışma

Sıradaki adım, kod yazmadan önce kağıt üzerinde dört şeyi çıkarmak:

| # | İş | Durum |
|---|---|---|
| 1 | MVP kapsamı (hangi özellikler ilk sürümde) | Taslak hazır; veli ve personel girişi MVP'de, öğrenci hesabı sonra |
| 2 | Yetki matrisi (rol × veri × işlem) | MVP için doldu, küçük açık noktalar var |
| 3 | Neon veri modeli taslağı (tablolar, ilişkiler, kiracı ayrımı) | Başlanmadı |
| 4 | Kaynak kodun kurtarılması (Vercel'e bağlı Git deposu var mı, yoksa en güncel ZIP'ten depo kur) | Kullanıcı tarafında, paralel |

### 4.1 MVP kapsamı — taslak (onay bekliyor)

İlk sürümde olması önerilenler:

- Telefonla giriş (gerçek SMS doğrulamasıyla) ve rol tespiti
- Firma ve bağlantılar (okul, fabrika)
- Okul/kurum kartı
- Öğrenci kaydı (otomatik öğrenci kodu, Excel ile toplu yükleme)
- Araç ve şoför kayıtları
- Sefer, rota ve yoklama
- Veliye bildirim

İkinci aşamaya bırakılması önerilenler:

- Zarf, aidat ve finans (e-fatura, KDV/tevkifat, mutabakat)
- Yakıt kartı ve OCR
- Fabrika modları ve vardiya/mesai ayrıntıları
- Lisans ve ücretlendirme
- Tema, banner ve panel özelleştirmeleri
- Sesli komut, anket, teklif

> Bu liste öneridir; hangi özelliğin gerçekten ilk sürümde olacağı kullanıcı onayıyla kesinleşecek.

### 4.2 Yetki matrisi — MVP için kesinleşen taslak

**G** görür, **E** ekler, **D** düzenler, **S** siler, **—** erişim yok. "Kendi" ifadesi, kişinin yalnızca kendisiyle ilişkili kaydı gördüğü anlamına gelir.

| Veri | Admin | Firma | Okul | Fabrika | Şoför | Veli | Personel |
|---|---|---|---|---|---|---|---|
| Firma bilgileri | GEDS | GD | G (iletişim) | G (iletişim) | — | G (iletişim) | G (iletişim) |
| Okul/fabrika kartı | GEDS | GEDS | G, D (kendi kartı) | G, D (kendi kartı) | G (atandığı) | — | — |
| Öğrenci kaydı | GEDS | GEDS | G (kendi okulu) | — | G (seferindeki, kısıtlı) | G (kendi çocuğu) | — |
| Personel kaydı | GEDS | GEDS | — | G (kendi personeli) | G (seferindeki, kısıtlı) | — | G (kendi) |
| Araç | GEDS | GEDS | G (yetkili alanlar) | G (yetkili alanlar) | G (atandığı) | G (plaka) | G (plaka) |
| Şoför | GEDS | GEDS | G (ad, telefon) | G (ad, telefon) | G, D (kendi belgeleri) | G (ad, telefon) | G (ad, telefon) |
| Güzergâh/durak | GEDS | GEDS | G | G, onay (gerektiğinde) | G (kendi) | G (kendi) | G (kendi) |
| Sefer | GEDS | GEDS | G | G | G, D (başlat/bitir) | G (kendi) | G (kendi) |
| Yoklama | G | G | G (kendi öğrencileri) | G (kendi personeli) | E, D | G (çocuğu); E ("bugün gelmeyecek") | G (kendi); E ("bugün gelmeyeceğim") |
| Canlı konum | G | G | G (kendi kurumuna gelen araç) | G (kendi kurumuna gelen araç) | paylaşır | G (sefer süresince, çocuğunun aracı) | G (sefer süresince, kendi aracı) |

Bu aşamada alınan kararlar:

- Okul ve fabrika kendi kart bilgilerini (adres, giriş-çıkış saatleri) kendileri değiştirebilir.
- Okul ve fabrika şoförün adını ve telefonunu görebilir.
- Canlı konumu firma, veli/personel ve okul/fabrika görebilir. Okul ve fabrika yalnızca kendi kurumuna gelen araçları görür; canlı konum yalnızca ilgili sefer süresince açıktır.
- İlk sürümde yalnızca veli ve personel girişi olacak. Öğrenci hesabı ikinci aşamaya kaldı, bu yüzden matriste öğrenci sütunu yok.
- Okul ve fabrika hiçbir zaman başka kurumun öğrencisini ya da personelini görmez. Karma öğrenciler okul panelinde görünür ama düzenlenemez.
- Veli ve personel diğer yolcuları hiç görmez.

Hâlâ açık kalan küçük noktalar: partner taşıyıcının matriste ayrı bir sütunu olacak mı ve hangi işleri göreceği; okul ve fabrikanın düzenlediği kart bilgilerinde firma onayının gerekip gerekmediği (şu an gerekmiyor).

### 4.3 Veri modeli — ele alınacak başlıklar

- Kiracı ayrımı: her kayıtta hangi kuruma/firmaya ait olduğu; sorgularda zorunlu filtre ya da Row Level Security
- Kişi ve hesap ayrımı: bir kişinin kaydı olması, hesabı olduğu anlamına gelmez
- Telefon ve rol ilişkisi: aynı telefonun birden fazla rolde ve birden fazla öğrencide kullanılabilmesi
- Otomatik öğrenci kodu: sistem genelinde benzersiz, değiştirilemez
- İşlem geçmişi: kim, ne zaman, neyi değiştirdi
- Kişisel veri: saklama süresi, silme ve rıza kayıtları

---

## 4B. Aşama 1 kararları — Giriş ve roller (kesinleşti)

Çalışma sırası: Önce temel aşama (telefonla giriş ve roller, firma ve bağlantılar) bitirilecek; bu aşama bitmeden diğer aşamaya geçilmeyecek.

**Giriş**

- Telefon numarası yazılır, SMS ile gelen kod girilir.
- Yalnızca firma ya da kurum tarafından önceden kaydedilen numaralar giriş yapabilir. Kayıtlı olmayan numaraya SMS gönderilmez.
- Veli ve personel numaraları kayıt sırasında (ör. Excel şablonundaki "Veli Cep" sütunu) girilir.
- Firma yetkili hesaplarını yalnızca admin açar. Okul ve fabrika yetkili hesaplarını ilgili firma açar; admin onayı gerekmez (sonradan verilen karar).

**SMS ile giriş (netleşti)**

- Kullanıcı telefon numarasını yazar. Numara kayıtlı değilse SMS gönderilmez; kayıtlıysa 6 haneli kod SMS ile gider.
- Kod 5 dakika geçerlidir ve tek kullanımlıktır.
- Aynı numaraya saatte en fazla 3 kod gönderilir; 5 yanlış denemeden sonra numara kısa süreli kilitlenir.
- Kod doğrulaması ve kısıtlar sunucu tarafında yapılır.
- Oturum cihazda 30 gün açık kalır; kullanıcı çıkış yapabilir. Böylece SMS yalnızca oturum açılırken gider.
- Giriş kodu şimdilik SMS ile gider; ileride WhatsApp'a geçmek istenirse değiştirilebilir (kullanıcı kararı).
- SMS sağlayıcısı ve fiyatı henüz seçilmedi; Türkiye'deki toplu SMS sağlayıcılarından teklif alınmalı.

**Roller**

- Tek rolü olan kişi doğrudan kendi ekranına girer.
- Birden fazla rolü olan kişiye girişten sonra "Hangi rolle devam edeceksin?" sorulur; son seçim hatırlanır ve menüden değiştirilebilir.
- Sunucu tarafında yetki kontrolü zorunludur.

**Gerekçeler**

- Çocuk verisi (adres, telefon, canlı konum) herkese açık bir kayıt formunun arkasında durmamalı.
- Herkese açık kayıt, SMS maliyetini kötüye kullanıma açar.
- Kişinin hangi rolde işlem yaptığı ekranda açıkça görünmeli.

**Taşımalı/Karma bağlantısı (netleşti)**

- Aynı bölgeden öğrenci ya da personel toplayan tek bir servis, onları farklı okullara ya da fabrikalara sırayla bırakır. Örnek: 08:05 Koç Lisesi (5 öğrenci), 08:15 Sabancı Lisesi (6 öğrenci), 08:25 Lalegül Koleji (5 öğrenci); okulların giriş saati 08:30.
- Her serviste sıralı bir durak listesi vardır: hangi okula, saat kaçta, kaç kişi bırakılacak.
- Her öğrencinin bir hedef okulu vardır; aynı araçta farklı okulların öğrencileri bulunur.
- Okul yalnızca kendi öğrencilerini görür; aynı araçtaki diğer okulların öğrencilerini görmez.
- Sabah ve dönüş sırası farklı olabilir. Hedef okulun firmayla bağlantılı bir okul kartı olması gerekmez.
- Karma, okul ve fabrikalardan bağımsız tek bir kart olarak tutulur.
- **Bildirim:** Şoför "öğrencileri bıraktı" deyince ilgili okula bildirim gider.

**Firma, okul ve fabrika bağlantısı (kesinleşti)**

- Firma okulu ya da fabrikayı kendisi ekler ve kartı yönetir. Okul ya da fabrika programı kullanmıyorsa bu yeterlidir.
- Okul ya da fabrika programı kullanacaksa firma, yetkilinin telefon numarasını girerek ona doğrudan yetki verir. Admin onayı gerekmez.
- Yetkili o numarayla giriş yapar ve yalnızca kendi okul/fabrika kartını görür ve düzenler.
- Güvence olarak: yetki yalnızca firmanın kendi kartları için verilebilir; yetki verme ve geri alma işlem kaydına yazılır; firma yetkiyi istediği an geri alabilir.

**İş verilen taşımacılar (partner taşıyıcı)**

- İlk sürümde olacak (kullanıcı kararı).
- Devir dosyasına göre: iş veren firma ile işi alan taşıyıcı ayrılır; partner taşıyıcı yalnızca kendisine verilen işleri görür.
- İşi alan taşıyıcı, o işe kendi aracını ve şoförünü kendisi atar (kullanıcı kararı).
- Partner yalnızca kendisine verilen işteki yolcuların adını, adresini ve veli telefonunu görür (kullanıcı kararı).
- Önerilen güvence: iş geri alınır ya da sona ererse partnerin o işe ait yolcu verisine erişimi de kapanır.

**Aşama 1 durumu:** Telefonla giriş, roller, firma-okul/fabrika bağlantısı, Taşımalı/Karma ve iş verilen taşımacılar konuşuldu ve karara bağlandı. Açık not: Aynı okulla iki farklı firma çalışırsa okulun tek kart mı iki kart mı olacağı veri modeli çıkarılırken karara bağlanacak.

---

## 4C. Aşama 2 kararları — Kayıtlar

Aşama 1 tamamlandı sayıldı. Aşama 2: okul/kurum kartı, öğrenci kaydı ve Excel yükleme, araç/şoför/belgeler.

**Öğrenci kaydı**

- Zorunlu alanlar: öğrenci adı soyadı, öğrenci cep telefonu, veli adı soyadı, veli cep telefonu, adres (kullanıcı kararı; öğrenci cep telefonunun zorunlu olması kullanıcının tercihidir).
- Telefon hatalıysa kayıt sessizce geçmez; "Öğrenci cep hatalı" ya da "Veli cep hatalı" gibi açık uyarı çıkar.
- Öğrenci kodunu sistem otomatik üretir; kullanıcı değiştiremez.
- Excel sütunları: Öğrenci Adı Soyadı, Öğrenci Cep, Veli Adı Soyadı, Veli Cep, Adres, Okul Adı, Durum.

**Excel ile toplu yükleme**

- Hatasız satırlar yüklenir; hatalı satırlar nedenleriyle birlikte listelenir (kullanıcı kararı). Tek bir hata yüzünden tüm dosya reddedilmez.

**Araç ve şoför belgeleri**

- İlk sürümde her belge için yalnızca belge türü ve geçerlilik bitiş tarihi tutulur; süresi yaklaşınca uyarı verilir (kullanıcı kararı).
- Belge fotoğrafı/dosyası yükleme sonraya bırakıldı (depolama, güvenlik ve KVKK ayrı bir iş çıkarır).
- Uyarı zamanı: 30 gün ve 7 gün kala; firma yetkilisine ve ilgili şoföre (kullanıcı onayladı).

- Süresi dolmuş belgesi olan şoför ya da araçla sefer başlatılamaz; firma yetkilisi gerektiğinde geçici izin verebilir (kullanıcı kararı).

**Okul/fabrika kartı**

- Alanlar: ad, adres, giriş-çıkış saatleri; ek olarak yetkili adı ve telefonu ile birden fazla vardiya saati (fabrikalar için; okullarda boş kalır).
- Okul ve fabrika kendi kart bilgilerini kendisi değiştirebilir (önceki karar).
- Haritada konum seçme ilk sürümde yok; canlı konum özelliğiyle birlikte eklenecek.

**Araç ve şoför kaydı**

- Araç, zorunlu: plaka (benzersiz; aynı plaka iki kez eklenemez), araç türü, yolcu kapasitesi. İsteğe bağlı: marka ve model.
- Şoför, zorunlu: ad soyad, cep telefonu (şoför bununla giriş yapar). İsteğe bağlı: adres, acil durum iletişim numarası.

**Aşama 2 durumu:** Öğrenci kaydı ve Excel, belgeler, okul/fabrika kartı, araç ve şoför kaydı konuşuldu ve karara bağlandı. Belge uyarıları belgenin bitmesine 30 ve 7 gün kala, firma yetkilisine ve ilgili şoföre gider (kullanıcı onayladı). Aşama 2 tamamlandı sayıldı.

---

## 4D. Aşama 3 kararları — Günlük iş

Kapsam: sefer, rota ve yoklama; veli/personel ekranı ve bildirim.

**Kavramlar:** Rota (güzergâh) servisin hep izlediği yol ve durak sırasıdır. Sefer, o rotanın belirli bir günde ve saatte yapılan hâlidir. Yoklama, seferde kimin bindiği ve indiğidir.

**Sefer oluşturma ve başlatma**

- Firma rotayı kurar; çalışma günlerini, saati, aracı ve şoförü belirler.
- Seferler her gün kendiliğinden oluşur; kimse her sabah elle sefer açmak zorunda kalmaz.
- Şoför sabah "Seferi Başlat" der. Belge kontrolü ve canlı konum paylaşımı bu anda başlar.

**Yoklama**

- Şoför yalnızca gelmeyen öğrencileri işaretler; işaretlenmeyenler araca binmiş sayılır (kullanıcı kararı; Claude'un önerisi her öğrencinin tek dokunuşla "bindi" işaretlenmesiydi).
- Veli önceden "bugün gelmeyecek" bildirebilir; şoförün listesinde o öğrenci gelmeyecek olarak görünür.
- Okulda şoför "öğrencileri bıraktı" der; ilgili okula bildirim gider (önceki karar).

**Bırakma ve sayı teyidi**

- Okulda bırakma sırasında şoförün ekranında "Koç Lisesi: 5 öğrenci bırakılıyor" gibi bir özet çıkar; şoför sayıyı onaylar, sonra okula bildirim gider (kullanıcı seçti).
- Gerekçe: "işaretlenmeyen binmiş sayılır" kuralında, gerçekte binmeyen birinin hatası okulda değil şoförün ekranında yakalanır.

**Bildirimler (veli ve personele)**

İlk sürümde dört bildirim olacak (kullanıcı kararı):

1. Servis yola çıktı (şoför seferi başlatınca).
2. Servis yaklaşıyor (durağa yakınken; canlı konuma bağlı).
3. Öğrenci okula bırakıldı (şoför sayıyı teyit edince).
4. Öğrenci "gelmedi" olarak işaretlendi (velinin haberi olmadan işaretlenirse veli hemen öğrenir).

**Bildirim kanalı**

- Tüm bildirimler yalnızca program (uygulama bildirimi) üzerinden gider; SMS ya da WhatsApp ile bildirim gönderilmez (kullanıcı kararı). Veliler ve personel programı kullanacak.
- Gerekçe: SMS ve WhatsApp mesaj başına ücretlidir ve sefer başı bildirimlerle fatura hızla büyür (örnek: 200 öğrenci × 4 bildirim × 22 gün = 17.600 mesaj/ay). Uygulama bildirimi neredeyse ücretsizdir.
- Dikkat: Bildirim izni kapalı ya da programı kurmamış veliye "gelmedi" bildirimi ulaşmaz. Programda bildirim iznini açtırmak ve izin kapalıysa uyarı göstermek gerekir.
- Giriş için SMS doğrulama kodu yine gerekir (Aşama 1 kararı); bu ayrı ve çok daha az sayıdadır.

**Veli ve personel ekranı**

İlk sürümde ana ekranda dört şey olacak (kullanıcı kararı):

1. Bugünkü sefer ve durumu (yola çıktı mı, nerede, tahmini varış).
2. Canlı konum haritası (yalnızca sefer süresince, yalnızca kendi aracı).
3. "Bugün gelmeyeceğim" butonu: şoförün listesinde o yolcu gelmeyecek olarak görünür.
4. Şoförün adı, telefonu ve araç plakası.

Veli yalnızca kendi çocuğunun, personel yalnızca kendi seferini görür; diğer yolcular görünmez (yetki matrisi).

**Aşama 3 durumu:** Sefer, yoklama, bırakma ve sayı teyidi, bildirimler, bildirim kanalı ve veli/personel ekranı konuşuldu. Aşama 3 tamamlandı sayıldı.

**"Bugün gelmeyeceğim" son saati:** Buton servis çıkmadan 30 dakika öncesine kadar kullanılabilir (kullanıcı seçti). Sonrasında yolcu gelmeyecekse şoföre haber vermesi gerekir; şoför yoklamada "gelmedi" işaretler.

**Sırada:** Aşama 4: fabrika/işletme (personel, vardiya) ve taşımalı/karma kartının ayrıntıları.

---

## 4E. Aşama 4 kararları — Kurum türleri

Kapsam: fabrika/işletme (personel, vardiya) ve taşımalı/karma.

**Fabrika yönetim kuralı**

- Fabrikanın personel listesini firma yönetir. Fabrika programı kullanıyorsa kendi personelini de ekleyip çıkarabilir (kullanıcı seçti).
- Devir dosyasındaki üç mod (Fabrika Aktif, Ortak Yönetim, Firma Yönetiyor) ilk sürümde tek kurala indirildi; yetki matrisi ve test yükü böylece azalır. Üç mod sonraki sürümde yeniden açılabilir.
- Fabrikanın yalnızca kendi personelini gördüğü kuralı değişmez; başka fabrikanın personeli görünmez.

**Vardiya**

- Fabrikada her vardiya için ayrı sefer oluşur (kullanıcı seçti). Örnek: 08:00, 16:00 ve 24:00 vardiyaları için ayrı seferler.
- Her personelin bir vardiyası vardır; o vardiyanın seferinde listelenir.
- Giriş (işe geliş) ve çıkış (eve dönüş) seferleri ayrı oluşur.
- Yoklama, bildirimler ve "bugün gelmeyeceğim" kuralı okul seferiyle aynı çalışır.

**Taşımalı/Karma kartı**

- Karma servisi yöneten firma, yolcuyu kaydederken hedef okulu ya da fabrikayı seçer (kullanıcı seçti).
- Her yolcunun kaydında bir hedef kurum bulunur; güzergâh ve bırakma sırası buna göre kurulur (örn. 08:05 Koç Lisesi, 08:15 Sabancı Lisesi, 08:25 Lalegül Koleji).
- Okul ya da fabrika yalnızca kendi yolcularını görür; aynı araçtaki diğer kurumların yolcularını görmez.
- Karma yolcunun kartı okul panelinde kilitli görünür; düzenleme Karma kartından yapılır.
- Önerilen güvence: aynı yolcu iki farklı serviste aynı anda kayıtlı olamaz.

**Aşama 4 durumu:** Fabrika yönetim kuralı, vardiya ve karma kart karara bağlandı. Açık nokta: aynı okulla iki farklı firma çalışırsa okulun tek kart mı iki kart mı olacağı (veri modeli aşamasında).

**Sırada:** Aşama 5: zarf, aidat ve finans.

---

## 4F. Aşama 5 kararları — Zarf, aidat ve finans

**İlk sürümde olacaklar (kullanıcı seçti)**

- Zarf takibi ve aidat tutarı: öğrenci, veli, dönem, aylık tutar, ödeme şekli (nakit zarf veya IBAN), zarf durumu (Hazırlanmadı, Zarf Hazırlandı, Zarf Dağıtıldı).
- Tahsilat durumu: Ödeme Bekleniyor, Ödendi.
- Ödeme bekleniyor bildirimi: veliye hatırlatma; bildirim kanalı kararı gereği yalnızca program bildirimi.

**Sonraya bırakılanlar:** e-fatura/e-arşiv, KDV/tevkifat, mutabakat.

**Kim günceller**

- Zarf durumunu ve "ödendi" bilgisini firma yetkilisi günceller.
- Şoför yalnızca "zarf dağıtıldı" işaretleyebilir; ödeme bilgisini göremez ve değiştiremez (kullanıcı seçti).
- Her güncelleme kim, ne zaman, neyi değiştirdi olarak işlem kaydına yazılır.

**Görünürlük ve tutar**

- Aidat ve ödeme durumu yalnızca firma ile veliye görünür; okul ve fabrika yetkilisi görmez (kullanıcı seçti). Aidat firma ile veli arasındaki iştir, ayrıca öğrenci ödeme bilgisi okula açılmamış olur.
- Aylık aidat tutarını firma her öğrenci için girer, istenirse toplu uygular; öğrenci kaydında ya da Excel'de aidat sütunu olabilir (kullanıcı seçti).
- Güzergâha göre otomatik fiyat hesabı ilk sürümde yok.

**Aşama 5 durumu:** Zarf takibi, tahsilat, bildirim, güncelleme yetkisi, görünürlük ve tutar belirlendi. Aşama 5 tamamlandı sayıldı.

**Ödeme hatırlatması:** Zarf dağıtılınca veliye bir kez, 7 gün içinde ödenmemişse bir kez daha bildirim gider (kullanıcı seçti). Bildirim yalnızca program üzerinden gider.

**Sırada:** Neon veri modeli (tablolar, ilişkiler, kiracı ayrımı).

---

## 5. Açık sorular

- Projeyi tek başına mı yürütüyorsun, yoksa yanında bir yazılımcı ya da ekip var mı? (Kapsam bu cevaba göre daraltılacak.)
- Canlı V2'nin bağlı olduğu bir Git deposu var mı?
- Şoför uygulaması için yerel/hibrit uygulama düşünülüyor mu, yoksa yalnızca PWA mı?
- Lisans kuralları (45 gün ücretsiz, 7 gün sonra askıya alma, "1 kullanıcı 1 sefer") ne anlama geliyor?

---

## 6. Sonraki adım

Beş aşamanın tamamı kâğıt üzerinde karara bağlandı. Sıradaki iş Neon veri modelini çıkarmak: kiracı ayrımı, kişi/hesap/telefon/rol ilişkisi, otomatik öğrenci kodu, aynı okulla iki firma çalışması ve işlem geçmişi. Kod yazmadan önce kaynak kodun Git deposuna alınması hâlâ ilk teknik iş. Bu dosya ilerleyen sohbetlerde de devam noktası olarak kullanılabilir.
