# Doğrulama — 11 Kasım 2026

Testler yalnızca geçici klasörlerde ve sentetik kayıtlarla yürütüldü; kullanıcının canlı JSON/DB dosyası değiştirilmedi.

## JSON deposu

`python -m unittest discover -s kaynak -p test_shared_store.py -v` sonucu: **17 test başarılı**.

- Aynı JSON dosyasına 10 paralel süreç yazdı; hiçbir değişiklik kaybolmadı.
- Eski ETag ile yazma reddedildi; aynı işlem kimliği tekrar sürüm eklemedi.
- Sağlama toplamı bozuk JSON reddedildi.
- Son 100 tam veri görüntüsü saklandı.
- Mevcut günlük dosyası JSON'a geçirildi; eski kaynak dosya korundu.
- Önceki günlük formatının kurtarma, checksum ve eşzamanlılık testleri çalıştırıldı.

## Arayüz ve EXE

- İzole yerel sunucu ve tarayıcıda sayfa hatasız açıldı; formdan konu kaydı JSON'a yazıldı.
- İki başarılı kayıt sonrası `revision` ve JSON'daki `versions` sayısı arttı; tarayıcı `localStorage` boş kaldı.
- Paketlenmiş `IsTakip-Guncel.exe` headless testte yerel API'yi açtı ve başlangıç JSON dosyasını oluşturdu.
- Başlatıcı yaşam döngüsü, tarayıcı heartbeat'i gelmezse yaklaşık 30 dakika sonra kapanacak şekilde güncellendi.

## Sınırlar

Gerçek SMB sunucusunda atomik dosya değiştirme/kilit davranışı ve birden fazla bilgisayarlı ağ kesintisi testi yapılmadı; önce deneme paylaşımında pilot gerekir. Eski `IsTakip.exe` süreçleri otomatik kapatılmadı; geçişten önce tüm eski istemcileri kapatın ve yeni `IsTakip-Guncel.exe` kullanın.
