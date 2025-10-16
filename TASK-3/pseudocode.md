ROGRAM AnaSistem
BAŞLA
  
  // 1. Kimlik Doğrulama Adımı
  GirisYapanKullanici = ÇAĞIR KimlikDogrula()
  
  // Kimlik doğrulama sonucunu kontrol et
  EĞER GirisYapanKullanici != null İSE
    GÖSTER "Giriş başarılı. Hoş geldiniz, " + GirisYapanKullanici.AdSoyad
    ÇAĞIR ModulSecimi(GirisYapanKullanici) // Başarılı ise modül seçimine geç
  DEĞİLSE
    GÖSTER "Hatalı TCKN veya şifre. Erişim reddedildi."
  BİTİR EĞER
  
  GÖSTER "Sistemden çıkış yapıldı."
BİTİR

//----- Kimlik Doğrulama Fonksiyonu -----
FONKSİYON KimlikDogrula()
  GÖSTER "Lütfen TC Kimlik Numaranızı girin:"
  TCKN = KULLANICIDAN_AL()
  GÖSTER "Lütfen şifrenizi (veya e-Devlet şifrenizi) girin:"
  Sifre = KULLANICIDAN_AL()
  
  // Veritabanı kontrolünü simüle et
  EĞER TCKN ve Sifre veritabanında DOĞRU İSE
    KullaniciBilgisi = VeritabanindanKullaniciGetir(TCKN)
    DÖNDÜR KullaniciBilgisi // Kullanıcı nesnesini döndür
  DEĞİLSE
    DÖNDÜR null // Başarısız ise boş değer döndür
  BİTİR EĞER
SON FONKSİYON


//----- Modül Seçimi Fonksiyonu -----
FONKSİYON ModulSecimi(Kullanici)
  DÖNGÜ
    GÖSTER "Lütfen yapmak istediğiniz işlemi seçin:"
    GÖSTER "1: Randevu İşlemleri"
    GÖSTER "2: Tahlil Sonuçları"
    GÖSTER "3: Güvenli Çıkış"
    Secim = KULLANICIDAN_AL()
    
    EĞER Secim == "1" İSE
      ÇAĞIR RandevuModulu(Kullanici)
    DEĞİLSE EĞER Secim == "2" İSE
      ÇAĞIR TahlilModulu(Kullanici)
    DEĞİLSE EĞER Secim == "3" İSE
      DÖNGÜDEN_ÇIK // Döngüyü sonlandır ve programı bitir
    DEĞİLSE
      GÖSTER "Geçersiz bir seçim yaptınız."
    BİTİR EĞER
  SON DÖNGÜ
SON FONKSİYON

//----- Randevu İşlemleri Modülü -----
FONKSİYON RandevuModulu(Kullanici)
  GÖSTER "--- Randevu Modülü ---"
  DoktorListesi = VeritabanindanDoktorlariGetir()
  GÖSTER DoktorListesi
  
  GÖSTER "Lütfen doktor seçiniz:"
  SecilenDoktor = KULLANICIDAN_AL()
  
  UygunSaatler = VeritabanindanUygunSaatleriGetir(SecilenDoktor)
  GÖSTER UygunSaatler
  
  GÖSTER "Lütfen bir saat seçiniz:"
  SecilenSaat = KULLANICIDAN_AL()
  
  GÖSTER "Randevu Bilgileri: " + SecilenDoktor + ", Saat: " + SecilenSaat
  GÖSTER "Bu randevuyu onaylıyor musunuz? (E/H)"
  Onay = KULLANICIDAN_AL()
  
  EĞER Onay == "E" VEYA Onay == "e" İSE
    // Randevu kaydını veritabanına işle
    RandevuKaydet(Kullanici, SecilenDoktor, SecilenSaat)
    GÖSTER "Randevunuz başarıyla oluşturulmuştur."
    
    // SMS Gönderme işlemi
    SMS_Metni = "Sayın " + Kullanici.AdSoyad + ", " + SecilenDoktor + " için " + SecilenSaat + " tarihli randevunuz onaylanmıştır."
    ÇAĞIR SMSGonder(Kullanici.TelefonNo, SMS_Metni)
  DEĞİLSE
    GÖSTER "Randevu işlemi iptal edilmiştir."
  BİTİR EĞER
SON FONKSİYON


//----- SMS Gönderme Simülasyonu -----
FONKSİYON SMSGonder(TelefonNumarasi, Mesaj)
  GÖSTER "[SMS GÖNDERİLİYOR] Numara: " + TelefonNumarasi + " | Mesaj: " + Mesaj
SON FONKSİYON

//----- Tahlil Sonuçları Modülü -----
FONKSİYON TahlilModulu(Kullanici)
  DÖNGÜ // Başka istek olup olmadığını kontrol etmek için ana döngü
    GÖSTER "--- Tahlil Sonuçları Modülü ---"
    KullanicininTahlilleri = VeritabanindanTahlilleriGetir(Kullanici.TCKN)
    GÖSTER KullanicininTahlilleri // Kullanıcının tüm tahlillerini listele
    
    GÖSTER "Sonucunu görmek istediğiniz tahlili seçin (ID girin):"
    SecilenTahlilID = KULLANICIDAN_AL()
    
    // Seçilen tahlilin durumunu kontrol et (Koşul)
    EĞER TahlilSonucuHazirMi(SecilenTahlilID) == doğru İSE
      SonucDetaylari = VeritabanindanTahlilSonucunuGetir(SecilenTahlilID)
      GÖSTER SonucDetaylari // Sonuçları ekranda göster
      
      GÖSTER "1: Sonucu PDF Olarak İndir"
      GÖSTER "2: Ana Menüye Dön"
      TahlilSecim = KULLANICIDAN_AL()
      
      EĞER TahlilSecim == "1" İSE
        ÇAĞIR PDFIndir(SonucDetaylari)
      BİTİR EĞER
      
    DEĞİLSE
      GÖSTER "Seçtiğiniz tahlilin sonucu henüz hazır değil."
    BİTİR EĞER
    
    // Döngünün devam edip etmeyeceğini sor
    GÖSTER "Başka bir tahlil sonucu sorgulamak ister misiniz? (E/H)"
    DevamCevabi = KULLANICIDAN_AL()
    
    EĞER DevamCevabi == "H" VEYA DevamCevabi == "h" İSE
      DÖNGÜDEN_ÇIK // Kullanıcı istemiyorsa döngüyü sonlandır
    BİTİR EĞER
  SON DÖNGÜ
SON FONKSİYON

//----- PDF İndirme Simülasyonu -----
FONKSİYON PDFIndir(Icerik)
  GÖSTER "[PDF İNDİRİLİYOR] " + Icerik.TahlilAdi + ".pdf dosyası oluşturuldu."
SON FONKSİYON








Bütünleşik Hastane Bilgi Sistemi Pseudocode'u
PROGRAM HastaneSistemi
BAŞLA

  //=====================================================
  // 1. ADIM: KİMLİK DOĞRULAMA
  //=====================================================
  GÖSTER "Sisteme Hoş Geldiniz."
  GÖSTER "Lütfen TC Kimlik Numaranızı girin:"
  TCKN = KULLANICIDAN_AL()
  
  GÖSTER "Lütfen şifrenizi girin:"
  Sifre = KULLANICIDAN_AL()
  
  // Kimlik doğrulama kontrolü (Veritabanı kontrolü simüle ediliyor)
  EĞER TCKN ve Sifre veritabanında DOĞRU İSE
    GÖSTER "Giriş başarılı!"
    KullaniciAdi = VeritabanindanKullaniciAdiniGetir(TCKN) // Örnek bir veri çekme
    
    //=====================================================
    // 2. ADIM: ANA MENÜ VE MODÜL SEÇİMİ (ANA DÖNGÜ)
    //=====================================================
    DÖNGÜ (Kullanıcı Çıkış Yapmadığı Sürece)
      GÖSTER "----------------------------------------"
      GÖSTER "Hoş geldiniz, " + KullaniciAdi
      GÖSTER "Lütfen yapmak istediğiniz işlemi seçin:"
      GÖSTER "1: Randevu İşlemleri"
      GÖSTER "2: Tahlil Sonuçları"
      GÖSTER "3: Güvenli Çıkış"
      AnaSecim = KULLANICIDAN_AL()

      //----- Randevu Modülü Akışı -----
      EĞER AnaSecim == "1" İSE
        GÖSTER "--- Randevu Modülü ---"
        DoktorListesi = VeritabanindanDoktorlariGetir()
        GÖSTER DoktorListesi
        GÖSTER "Lütfen bir doktor seçiniz:"
        SecilenDoktor = KULLANICIDAN_AL()
        
        UygunSaatler = VeritabanindanUygunSaatleriGetir(SecilenDoktor)
        GÖSTER UygunSaatler
        GÖSTER "Lütfen bir saat seçiniz:"
        SecilenSaat = KULLANICIDAN_AL()
        
        GÖSTER "Onay: " + SecilenDoktor + " için " + SecilenSaat + " randevusunu onaylıyor musunuz? (E/H)"
        Onay = KULLANICIDAN_AL()
        
        EĞER Onay == "E" İSE
          VeritabaninaRandevuKaydet(TCKN, SecilenDoktor, SecilenSaat)
          GÖSTER "Randevunuz başarıyla oluşturulmuştur."
          GÖSTER "[SMS GÖNDERİLİYOR] Randevu bilgileriniz telefonunuza gönderilmiştir."
        DEĞİLSE
          GÖSTER "Randevu işlemi iptal edilmiştir."
        BİTİR EĞER
        
      //----- Tahlil Modülü Akışı -----
      DEĞİLSE EĞER AnaSecim == "2" İSE
        DÖNGÜ (Kullanıcı Tahlil Modülünden Çıkmadığı Sürece)
          GÖSTER "--- Tahlil Sonuçları Modülü ---"
          KullanicininTahlilleri = VeritabanindanTahlilleriGetir(TCKN)
          GÖSTER KullanicininTahlilleri
          
          GÖSTER "Sonucunu görmek istediğiniz tahlili seçin (veya 'geri' yazın):"
          SecilenTahlilID = KULLANICIDAN_AL()
          
          EĞER SecilenTahlilID == "geri" İSE
             DÖNGÜDEN_ÇIK // Tahlil döngüsünden çık, ana menüye dön
          DEĞİLSE
            // Tahlil sonucunun hazır olup olmadığını kontrol et (KOŞUL)
            EĞER TahlilSonucuHazirMi(SecilenTahlilID) == doğru İSE
              SonucDetaylari = VeritabanindanTahlilSonucunuGetir(SecilenTahlilID)
              GÖSTER "Tahlil Sonuçlarınız: " + SonucDetaylari
              GÖSTER "1: Sonucu PDF Olarak İndir"
              GÖSTER "2: Devam Et"
              TahlilSecim = KULLANICIDAN_AL()
              
              EĞER TahlilSecim == "1" İSE
                GÖSTER "[PDF İNDİRİLİYOR] " + SecilenTahlilID + "_sonuc.pdf dosyası oluşturuldu."
              BİTİR EĞER
            DEĞİLSE
              GÖSTER "Seçtiğiniz tahlilin sonucu henüz hazır değil."
            BİTİR EĞER
          BİTİR EĞER
          
          // Başka istek olup olmadığını sorarak döngüyü yönet (DÖNGÜ KONTROLÜ)
          GÖSTER "Başka bir tahlil sorgulamak ister misiniz? (E/H)"
          DevamCevabi = KULLANICIDAN_AL()
          EĞER DevamCevabi == "H" İSE
            DÖNGÜDEN_ÇIK // Tahlil döngüsünden çık, ana menüye dön
          BİTİR EĞER
        SON DÖNGÜ
        
      //----- Güvenli Çıkış -----
      DEĞİLSE EĞER AnaSecim == "3" İSE
        DÖNGÜDEN_ÇIK // Ana döngüden çık
      DEĞİLSE
        GÖSTER "Geçersiz bir seçim yaptınız. Lütfen tekrar deneyin."
      BİTİR EĞER
      
    SON DÖNGÜ
    
  DEĞİLSE
    // Kimlik doğrulama başarısız olduğunda çalışır
    GÖSTER "Hatalı TCKN veya şifre. Erişim reddedildi."
  BİTİR EĞER

  //=====================================================
  // 3. ADIM: SİSTEMDEN ÇIKIŞ
  //=====================================================
  GÖSTER "Sistemden güvenli bir şekilde çıkış yapıldı. İyi günler dileriz."
  
BİTİR
