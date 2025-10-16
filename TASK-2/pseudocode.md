1. Değişkenlerin ve Yapıların Tanımlanması
BAŞLA

  // Veri yapılarını tanımla
  TANIMLA Ürün(ürün_id, ad, kategori, fiyat, stok_miktarı)
  TANIMLA Sepet(ürünler_listesi, toplam_tutar)
  TANIMLA İndirimKodu(kod, indirim_oranı, minimum_tutar)

  // Gerekli değişkenleri oluştur
  MevcutKullanıcıSepeti = YENİ Sepet()
  MinimumSiparişTutarı = 50.00
  İndirimUygulandı = yanlış

SON

2. Ana Akış ve Kullanıcı Etkileşimleri
// Kullanıcı siteye girdiğinde ana döngü başlar
DÖNGÜ (Kullanıcı sitede aktif OLDUĞU SÜRECE)

  // Seçenekleri göster
  GÖSTER "1: Kategorileri Gezin"
  GÖSTER "2: Sepeti Görüntüle"
  GÖSTER "3: Çıkış"

  KullanıcıSeçimi = KULLANICIDAN_AL()

  EĞER KullanıcıSeçimi == "1" İSE
    ÇAĞIR KategorileriGezin()
  DEĞİLSE EĞER KullanıcıSeçimi == "2" İSE
    ÇAĞIR SepetiYönet()
  DEĞİLSE EĞER KullanıcıSeçimi == "3" İSE
    DÖNGÜDEN_ÇIK
  DEĞİLSE
    GÖSTER "Geçersiz seçim."
  BİTİR EĞER

SON DÖNGÜ

Elbette, istediğiniz tüm özellikleri içeren kapsamlı bir online alışveriş sepeti sistemi için pseudocode (sözde kod) aşağıda adım adım açıklanmıştır. Bu pseudocode, programlama dilinden bağımsız olarak bir alışveriş sitesinin temel mantığını ve akışını ana hatlarıyla belirtir.

🛒 Online Alışveriş Sistemi Pseudocode'u
Bu sistem, kullanıcının ürünleri gezip sepete eklemesinden ödemeyi tamamlamasına kadar olan tüm süreci kapsar.

1. Değişkenlerin ve Yapıların Tanımlanması
BAŞLA

  // Veri yapılarını tanımla
  TANIMLA Ürün(ürün_id, ad, kategori, fiyat, stok_miktarı)
  TANIMLA Sepet(ürünler_listesi, toplam_tutar)
  TANIMLA İndirimKodu(kod, indirim_oranı, minimum_tutar)

  // Gerekli değişkenleri oluştur
  MevcutKullanıcıSepeti = YENİ Sepet()
  MinimumSiparişTutarı = 50.00
  İndirimUygulandı = yanlış

SON
2. Ana Akış ve Kullanıcı Etkileşimleri
// Kullanıcı siteye girdiğinde ana döngü başlar
DÖNGÜ (Kullanıcı sitede aktif OLDUĞU SÜRECE)

  // Seçenekleri göster
  GÖSTER "1: Kategorileri Gezin"
  GÖSTER "2: Sepeti Görüntüle"
  GÖSTER "3: Çıkış"

  KullanıcıSeçimi = KULLANICIDAN_AL()

  EĞER KullanıcıSeçimi == "1" İSE
    ÇAĞIR KategorileriGezin()
  DEĞİLSE EĞER KullanıcıSeçimi == "2" İSE
    ÇAĞIR SepetiYönet()
  DEĞİLSE EĞER KullanıcıSeçimi == "3" İSE
    DÖNGÜDEN_ÇIK
  DEĞİLSE
    GÖSTER "Geçersiz seçim."
  BİTİR EĞER

SON DÖNGÜ
3. Kategorileri Gezinme ve Ürün Ekleme
FONKSİYON KategorileriGezin()

  GÖSTER "Kategoriler: Giyim, Elektronik, Kitap"
  SeçilenKategori = KULLANICIDAN_AL()

  ÜrünleriListele(SeçilenKategori)

  GÖSTER "Eklemek istediğiniz ürünün ID'sini girin (Geri dönmek için 'geri' yazın):"
  SeçilenÜrünID = KULLANICIDAN_AL()

  EĞER SeçilenÜrünID != "geri" İSE
    GÖSTER "Adet girin:"
    Adet = KULLANICIDAN_AL()
    ÇAĞIR SepeteEkle(SeçilenÜrünID, Adet)
  BİTİR EĞER

SON FONKSİYON

//-----

FONKSİYON SepeteEkle(ürün_id, adet)

  HedefÜrün = VeritabanındanÜrünüBul(ürün_id)

  // Stok Kontrolü
  EĞER HedefÜrün != null VE HedefÜrün.stok_miktarı >= adet İSE
    MevcutKullanıcıSepeti.ürünler_listesi.EKLE({ürün: HedefÜrün, adet: adet})
    GÖSTER adet + " adet " + HedefÜrün.ad + " sepete eklendi."
  DEĞİLSE
    GÖSTER "Stok yetersiz veya ürün bulunamadı."
  BİTİR EĞER

SON FONKSİYON

4. Sepeti Görüntüleme ve Düzenleme
FONKSİYON SepetiYönet()

  ÇAĞIR SepetiGörüntüle(MevcutKullanıcıSepeti)

  GÖSTER "1: Ürün Adedi Güncelle"
  GÖSTER "2: Ürün Sil"
  GÖSTER "3: İndirim Kodu Kullan"
  GÖSTER "4: Ödemeye Geç"
  GÖSTER "5: Alışverişe Devam Et"
  SepetSeçimi = KULLANICIDAN_AL()

  // Kullanıcı seçimine göre işlem yap
  EĞER SepetSeçimi == "1" İSE
    // Adet güncelleme işlemleri...
  DEĞİLSE EĞER SepetSeçimi == "2" İSE
    // Ürün silme işlemleri...
  DEĞİLSE EĞER SepetSeçimi == "3" İSE
    ÇAĞIR İndirimKoduUygula()
  DEĞİLSE EĞER SepetSeçimi == "4" İSE
    ÇAĞIR ÖdemeyeGeç()
  DEĞİLSE EĞER SepetSeçimi == "5" İSE
    // Hiçbir şey yapma, ana döngüye dön
  BİTİR EĞER

SON FONKSİYON

//-----

FONKSİYON SepetiGörüntüle(Sepet)

  GÖSTER "--- SEPETİNİZ ---"
  EĞER Sepet.ürünler_listesi BOŞ İSE
    GÖSTER "Sepetiniz boş."
  DEĞİLSE
    ToplamTutar = 0
    HER ÜrünBilgisi İÇİN Sepet.ürünler_listesi
      AraToplam = ÜrünBilgisi.ürün.fiyat * ÜrünBilgisi.adet
      GÖSTER ÜrünBilgisi.ürün.ad + " - " + ÜrünBilgisi.adet + " adet - " + AraToplam + " TL"
      ToplamTutar = ToplamTutar + AraToplam
    SON HER

    Sepet.toplam_tutar = ToplamTutar
    GÖSTER "------------------"
    GÖSTER "Toplam Tutar: " + Sepet.toplam_tutar + " TL"
  BİTİR EĞER

SON FONKSİYON

5. İndirim Kodu ve Minimum Tutar Kontrolü
FONKSİYON İndirimKoduUygula()

  EĞER İndirimUygulandı == doğru İSE
      GÖSTER "Zaten bir indirim kodu kullandınız."
      DÖN
  BİTİR EĞER
  
  GÖSTER "İndirim kodunu girin:"
  GirilenKod = KULLANICIDAN_AL()

  KodBilgisi = VeritabanındanKoduBul(GirilenKod)

  EĞER KodBilgisi != null VE MevcutKullanıcıSepeti.toplam_tutar >= KodBilgisi.minimum_tutar İSE
    İndirimMiktarı = MevcutKullanıcıSepeti.toplam_tutar * KodBilgisi.indirim_oranı
    MevcutKullanıcıSepeti.toplam_tutar = MevcutKullanıcıSepeti.toplam_tutar - İndirimMiktarı
    İndirimUygulandı = doğru
    GÖSTER "İndirim başarıyla uygulandı. Yeni Tutar: " + MevcutKullanıcıSepeti.toplam_tutar + " TL"
  DEĞİLSE
    GÖSTER "Geçersiz kod veya minimum sepet tutarı sağlanmadı."
  BİTİR EĞER

SON FONKSİYON

6. Ödeme İşlemleri
FONKSİYON ÖdemeyeGeç()

  // Minimum Sipariş Tutarı Kontrolü
  EĞER MevcutKullanıcıSepeti.toplam_tutar < MinimumSiparişTutarı İSE
    GÖSTER "Sipariş verebilmek için sepet tutarınız en az " + MinimumSiparişTutarı + " TL olmalıdır."
    DÖN // Sepet yönetimine geri dön
  BİTİR EĞER

  GÖSTER "Ödenecek Tutar: " + MevcutKullanıcıSepeti.toplam_tutar + " TL"
  GÖSTER "Lütfen bir ödeme yöntemi seçin:"
  GÖSTER "1: Kredi Kartı"
  GÖSTER "2: Banka Havalesi"
  ÖdemeSeçimi = KULLANICIDAN_AL()

  EĞER ÖdemeSeçimi == "1" İSE
    // Kredi kartı bilgilerini al ve işlemi yap
    GÖSTER "Kredi kartı ile ödeme tamamlandı."
    ÇAĞIR SiparişiTamamla()
  DEĞİLSE EĞER ÖdemeSeçimi == "2" İSE
    // Havale bilgilerini göster
    GÖSTER "Banka havalesi ile ödeme bekleniyor."
    ÇAĞIR SiparişiTamamla()
  DEĞİLSE
    GÖSTER "Geçersiz ödeme yöntemi."
  BİTİR EĞER

SON FONKSİYON

//-----

FONKSİYON SiparişiTamamla()
  
  // Stokları güncelle
  HER ÜrünBilgisi İÇİN MevcutKullanıcıSepeti.ürünler_listesi
      ÜrünBilgisi.ürün.stok_miktarı = ÜrünBilgisi.ürün.stok_miktarı - ÜrünBilgisi.adet
      VeritabanındaÜrünüGüncelle(ÜrünBilgisi.ürün)
  SON HER

  GÖSTER "Siparişiniz başarıyla oluşturuldu. Teşekkür ederiz!"
  MevcutKullanıcıSepeti = YENİ Sepet() // Sepeti sıfırla
  İndirimUygulandı = yanlış

SON FONKSİYON
