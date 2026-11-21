# İş Takip — Taşınabilir ağ klasörü sürümü

11 Kasım 2026 · Windows 64 bit · Ayrı uygulama sunucusu ve Python kurulumu gerekmez.

## Nasıl açılır?

1. ZIP paketinin tamamını ortak ağ klasörüne çıkarın: örneğin `\\bilgisayar\paylasim\is-takip`.
2. Her kullanıcı **aynı ortak klasördeki `IsTakip-Guncel.exe` dosyasına çift tıklar**. Eski `IsTakip.exe` JSON sürümünü kullanmaz; tüm kullanıcılar yeni EXE'ye geçmeden eski uygulamayla birlikte çalıştırmayın.
3. Tarayıcıdaki çalışma ekranı otomatik açılır. Sekmeyi kapattıktan yaklaşık 30 dakika sonra bu bilgisayardaki yerel yardımcı kendiliğinden kapanır.
4. İlk kullanıcı Excel'i “Excel içe aktar” ile bir kez yükler. Diğer kullanıcılar aynı veriyi görür. Her bilgisayarın Excel'i ayrıca içe aktarması gerekmez.
5. Tablo değişikliklerinden sonra “Tabloyu kaydet” kullanın. Diğer istemciler düzenleme yapmıyorken yaklaşık 3 saniyede güncellemeyi alır.

**`uygulama/index.html` dosyasını doğrudan açmayın.** Tarayıcı güvenlik nedeniyle HTML içinden yerel script başlatamaz veya ağ klasöründeki JSON'a doğrudan yazamaz. EXE, yalnız bu bilgisayarda çalışan yardımcıyı başlatıp HTML'i otomatik açar; sayfa açıkken JSON'u 3 saniyede bir yeniler. `127.0.0.1` bu yerel yardımcıya aittir; ayrıca ortak sunucu, sabit port veya kurulum komutu yoktur.

## Veri nerede?

```
is-takip/
  IsTakip-Guncel.exe
  haftalik-rapor-v2.html
  uygulama/index.html
  assets/xlsx.full.min.js
  kaynak/...
  veri/                         İlk açılışta oluşur
    is-takip.json                Tek canlı veri kaynağı; son 100 tam sürüm
    is-takip.json.lock           Boş eşzamanlı erişim kilidi
```

Canlı veri kullanıcı profilindeki AppData'ya kaydedilmez; **EXE'nin yanındaki `veri/is-takip.json` dosyasındadır**. Dosya geçerli JSON, sürüm geçmişi ve sağlama toplamı içerir. Son 100 başarılı kayıt saklanır; eski sürümler sırayla düşer. JSON'u Excel/Not Defteri ile elle düzenlemeyin.

Mevcut kurulumda eski `is-takip.db` varsa ilk yeni sürüm açılışında JSON'a tek seferlik aktarılır; eski dosya ve JSON yedekleri geçiş doğrulanana kadar silinmez. Ekipteki tüm eski istemcileri kapatıp sonra yeni EXE'yi dağıtın.

Uygulama, Python çalışma dosyalarını çalışırken Windows'un geçici alanına çıkarabilir. Bu geçici alan canlı veri deposu değildir.

## Klasörü taşımak

Tüm kullanıcılarda açık değişiklikleri kaydedin ve İş Takip istemcilerini kapatın. EXE, HTML, assets ve **veri dahil klasörün tamamını** yeni konuma taşıyın. Yeni klasörden EXE'yi açın. Sabit veri yolu yoktur. Eski konuma verilen kısayolları yenileyin.

Klasörü kopyalarsanız iki ayrı veri deposu oluşur; kopyalar otomatik birleşmez. Ekip tek ortak kopyayı kullanmalıdır. OneDrive/Dropbox eşitleme klasörü bu kullanımın yerine geçmez.

## Excel ve v2

- Eski yönetici HTML raporunun tasarımı ve üreticisi `haftalik-rapor-v2.html` dosyasında korunur. Ana ekrandaki **Yönetici HTML raporu indir** düğmesi veriyi doğrudan bu üreticiye aktarır ve raporu otomatik indirir; Excel yükleme ekranında işlem yapmanız gerekmez.
- `--legacy` seçeneği artık ara V2 Excel ekranına yönlendirmez; ortak çalışma ekranını açar.
- Ortak kayda aktarım için ana ekrandaki **Excel içe aktar** düğmesini kullanın.
- Excel sayfa adları boşluk ve Türkçe karakterleriyle aynen korunur. `İş` ile `Is` birleştirilmez.
- Konu, etkilenen servis, konu tipi, kapanış, önem, durum, güncel durum ve haftalık tarih sütunları desteklenir. Başlıktan önce rapor başlığı/boş satır bulunması desteklenir; ilk 30 satır içinde başlık aranır.
- V2'nin kabul ettiği uzun notlar ve “Planlanıyor / Belirlenecek” gibi kapanış metinleri reddedilmez. Tarih olmayan metinler gecikme hesabına alınmaz.
- Excel kütüphanesi pakette bulunur. EXE üzerinden açılan ana ekran ve v2 ekranı internet olmadan Excel okuyabilir.
- Aynı isimli sayfaların verileri, mevcut akıştaki gibi onayınızla yenilenir. Önce JSON yedek alın; bu düğme tekrar tekrar toplu senkronizasyon için tasarlanmamıştır. Boş veya başlığı bulunamayan dosya mevcut kayıtları silmez.
- Sorumlu alanı JSON'da korunur; eski v2 Excel çıktısının kolonları değiştirilmediğinden Excel'e çıkıp tekrar içe alma bu ek alanı taşımayabilir.
- Yeni konu formu konu listesinin üstündedir. Mevcut bir konuya haftalık not eklemek için satırındaki **+** düğmesini kullanın. Not, yalnızca bugünün tarihiyle tanımlanan Pazartesi–Cuma haftasına eklenir; hafta aralığı ve notun tam eklenme tarihi geçmişte ve Excel çıktısında gösterilir.
- Önem seviyeleri 1’den 5’e yüksek kontrastlı yeşil, zeytin, sarı, turuncu ve kırmızı etiketlerle gösterilir. Aktif durum koyu yeşil, kapandı koyu gri, askıda kontrastlı sarı gösterilir. Etkilenen servisler koyu mavi etiketlerle belirtilir. Etkilenen servisleri forma tek tek yazıp Enter’a basarak etiket olarak ekleyin; listede etiket halinde görünür ve servis filtresinde seçilebilir.
- Konu listesi ve haftalık geçmiş tek tabloda birleştirilmiştir. Servis, Proje/Talep ve planlanan kapanış bilgileri hem ayrı sütunlarda hem de satırdaki haftalık geçmiş açıldığında gösterilir. Konu formundaki **Proje / Talep** alanına bu sınıflandırmayı girin. Formda ve tablodaki **Proje kapanış tarihi** hücresinde tarihi takvimden seçebilirsiniz; tarih henüz belli değilse formda tarih yerine planlama notu girebilirsiniz. Eski “Planlanıyor” gibi notlar korunur. Satırdaki **Düzenle** ve **Sil** düğmeleri ilgili konu işlemlerini açar; tablonun kaydedilmemiş değişiklikleri varken bu işlemlerden önce tablo taslağını kaydedin veya geri alın.
- Güncel durum, varsa en son haftalık nottan gösterilir. Yeni haftalık güncelleme eklenince güncel durum da aynı notla güncellenir; yeni konu formunda güncel durum yazıldığında not bugünün tarihiyle haftalık geçmişe eklenir.
- Konu eklemek için ana ekrana ortak klasördeki **IsTakip-Guncel.exe** üzerinden erişin. HTML dosyasını doğrudan tarayıcıda açmak ortak kayda yazmayı güvenlik nedeniyle engeller.

## Bildirimler ne demek?

**“Failed to fetch” / ortak veriye erişilemiyor:** HTML tek başına açılmış olabilir veya yardımcı kapanmıştır. HTML yerine EXE'yi açın. Ağ bağlantısı ve paylaşım izinleri de kontrol edilmelidir. Her kullanıcıya ilgili ortak klasörde okuma/yazma/oluşturma izni gerekir.

**“Aynı alan başka kullanıcı tarafından değiştirildi”:** taslak korunur, başka kişinin verisi ezilmez. JSON taslağını indirin; güncel kaydı açıp farkı uzlaştırın. Tam karşılaştırmalı çözüm ekranı henüz yoktur.

**“İşlem sonucunu denetle”:** yazma gerçekleşmiş ama cevap alınamamış olabilir. Bu düğme işlem kimliğini sorgular; kaydı ve haftalık notu ikinci kez oluşturmaz. Sonuç doğrulanana kadar sekmeyi kapatmayın.

**Veri dosyası eksik/bozuk:** otomatik boş veriye geçilmez. Başarılı JSON yedeğini farklı bir test klasöründe doğrulayıp geri yükleyin. Canlı dosyayı elle silmeyin.

## Sınırlar

JSON dosyası geçici klasörlerde eş zamanlı yazma, sürüm sınırı, checksum, tekrar koruması ve eski depodan geçiş testlerinden geçirilmiştir. Gerçek SMB paylaşımınıza erişilmedi; **20 ayrı bilgisayarda ağ/kilit/kesinti testi henüz yapılmadı**. Önce iki bilgisayarda deneme verisiyle pilot yapın; şirket Excel'i paylaşmanız gerekmez.

Windows dosya kilitlerinin ve yazma/flush davranışının paylaşım sunucusunda doğru uygulanması gerekir. Çevrimdışı Dosyalar/eşitleme üzerinden kullanım desteklenmez. Ağ kesildiğinde çalışma taslağı ekranda kalır; ortak kaydın başarılı olduğu söylenmez. Ağın işletim sistemi düzeyindeki zaman aşımı süresi değişebilir.

9 MB güncel veri, 10.000 konu, 200 sayfa ve 512 MB JSON dosyası sınırı vardır. Sürüm geçmişi en fazla 100 kayıt içerir; sınırda yeni kayıt durdurulur.

Dosya sunucusu kaybına karşı ayrı bir ortamın yedeği gerekir. Kurumsal yedekleme JSON dosyasını kapsamalıdır. Yetki paylaşım/NTFS izinlerine dayanır; uygulama içinde kurumsal SSO veya kayıt bazlı rol kontrolü yoktur. EXE imzasız geliştirme derlemesidir; kurumun yazılım dağıtım süreciyle değerlendirilmelidir.

Şirket verisine erişilmeden, tamamen yapay verilerle hazırlanmış ve test edilmiştir. Paket veri dosyası içermez; ilk açılışta boş depo oluşturulur. Güncelleme yaparken mevcut `veri` klasörünü koruyun.
