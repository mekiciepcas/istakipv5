# JSON tabanlı ortak ağ klasörü tasarımı

## Veri ve sürümleme

`veri/is-takip.json` canlı verinin tek kaynağıdır. Dosya geçerli JSON biçimindedir ve şu alanları içerir:

- `current`: arayüzün kullandığı son `sheets` ve `topics` verisi
- `revision` ve `storeId`: istemcilerin eski verinin üzerine yazmasını engelleyen sürüm kimliği
- `versions`: en fazla 100 başarılı kayıt; her biri tam veri görüntüsü, zaman, Windows kullanıcı adı ve işlem kimliğini içerir
- `sha256`: dosyanın bütünlüğünü doğrular

Her yazma aynı klasörde geçici JSON dosyasına yazılır, flush edilir, atomik değiştirme uygulanır ve geri okunarak doğrulanır. İstemciler `is-takip.json.lock` dosyasının tüm aralığını Windows `LockFileEx` ile kilitler; bu dosyada veri tutulmaz. Aynı revizyondan yazma girişimleri 412 ile reddedilir, arayüz bağımsız alanları birleştirip tekrar dener ve aynı alandaki çakışmayı kullanıcıya bildirir. İşlem kimliği son 100 sürüm içinde tekrarlı yazmayı önler.

Güncel veri 9 MB, toplam JSON dosyası 512 MB ile sınırlıdır. Yeni sürüm eklemek dosyayı bu sınırın üzerine çıkarırsa kayıt reddedilir; sürümler sessizce silinmez. En fazla 100 sürüm tutulur.

## Açılış ve yaşam döngüsü

Her bilgisayardaki `IsTakip-Guncel.exe` yalnız `127.0.0.1` üzerinde rastgele portta yerel arayüz yardımcısını başlatır ve tarayıcıyı otomatik açar. HTML, veriyi `no-store` HTTP istekleriyle okur ve yaklaşık 3 saniyede yeniler. Sayfa ayrıca 3 saniyede bir heartbeat yollar. 30 dakika heartbeat gelmezse helper sunucuyu kapatır. Uygulama yalnız yerel yardımcı üzerinden yazabilir; düz HTML tarayıcı güvenlik modeli nedeniyle yerel script başlatamaz veya ağ dosyasına doğrudan yazamaz.

Ortak bir uygulama sunucusu yoktur. Her istemci ağ paylaşımındaki aynı JSON ve kilit dosyasını Windows kullanıcı izinleriyle kullanır. SMB kilit ve atomik değiştirme davranışı gerçek paylaşımda ayrıca doğrulanmalıdır.

## Eski depodan geçiş

İlk yeni sürüm açılışında JSON yoksa mevcut `is-takip.db` doğrulanarak bir başlangıç görüntüsü oluşturulur. Eski dosya ve tarihli JSON yedekleri otomatik silinmez. Dağıtımdan önce tüm eski istemciler kapatılmalıdır; eski istemci JSON deposunu kullanmadığından iki sürümün aynı anda çalışması veri ayrışmasına yol açabilir.

JSON bozuksa, checksum tutmazsa veya eski depo eksikse uygulama boş veriye geçmez ve kayıt yapmaz. Elle geri yükleme öncesi canlı klasörün kopyası alınmalıdır.

## Pilot ve sınırlar

JSON depo testleri geçici klasörlerde checksum hatası, son 100 sürüm, eşzamanlı yazma, eski revizyon, tekrar koruması ve eski depodan geçişi kapsar. Gerçek ağ paylaşımındaki kilit/atomik değiştirme, bağlantı kesintisi ve çok bilgisayarlı yük ayrıca pilot edilmelidir. Yedekleme kurumsal dosya sunucusu yedekleme çözümüne bağlıdır; sürüm geçmişi aynı dosya içinde olduğundan tek başına felaket kurtarma yedeği değildir.
