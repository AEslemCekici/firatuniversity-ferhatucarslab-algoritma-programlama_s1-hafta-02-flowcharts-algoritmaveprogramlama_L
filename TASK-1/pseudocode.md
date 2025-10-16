BAŞLA

  // --- Değişkenleri ve Sabitleri Tanımla ---
  TANIMLA hatali_giris_sayisi = 0
  TANIMLA MAX_HATALI_GIRIS = 3
  TANIMLA KULLANICI_HESAP_BAKIYESI = 5000  // Örnek bakiye
  TANIMLA GUNLUK_CEKIM_LIMITI = 7500      // Örnek limit
  TANIMLA KART_BLOKE_DURUMU = false
  TANIMLA DOGRU_SIFRE = "1234"            // Örnek doğru şifre

  // --- Şifre Giriş ve Kontrol Döngüsü ---
  ÇIKIŞ "Lütfen kartınızı takınız."
  
  DÖNGÜ (hatali_giris_sayisi < MAX_HATALI_GIRIS)

    GİRİŞ kullanici_sifre: "Lütfen 4 haneli şifrenizi giriniz:"

    EĞER (kullanici_sifre == DOGRU_SIFRE)
      hatali_giris_sayisi = 0 // Başarılı girişte sayacı sıfırla
      DÖNGÜDEN ÇIK // Şifre doğru, döngüyü sonlandır
    DEĞİLSE
      hatali_giris_sayisi = hatali_giris_sayisi + 1
      ÇIKIŞ "Hatalı şifre girdiniz. Kalan deneme hakkınız: " + (MAX_HATALI_GIRIS - hatali_giris_sayisi)
    SON EĞER

  SON DÖNGÜ

  // --- Kart Bloke Kontrolü ---
  EĞER (hatali_giris_sayisi == MAX_HATALI_GIRIS)
    KART_BLOKE_DURUMU = true
    ÇIKIŞ "3 kez hatalı giriş yaptınız. Kartınız güvenlik nedeniyle bloke edilmiştir."
    ÇIKIŞ "Lütfen şubeniz ile iletişime geçiniz."
  DEĞİLSE // İşlemlere devam et

    // --- Bakiye Görüntüleme ve Tutar Girişi ---
    ÇIKIŞ "Hoş geldiniz."
    ÇIKIŞ "Hesap Bakiyeniz: " + KULLANICI_HESAP_BAKIYESI + " TL"

    GİRİŞ cekilecek_tutar: "Lütfen çekmek istediğiniz tutarı giriniz:"

    // --- Tutar Kontrolleri ---
    EĞER (cekilecek_tutar > KULLANICI_HESAP_BAKIYESI)
      ÇIKIŞ "Yetersiz bakiye. İşleminiz gerçekleştirilemiyor."
    DEĞİLSE EĞER (cekilecek_tutar > GUNLUK_CEKIM_LIMITI)
      ÇIKIŞ "Günlük çekim limitini aştınız. İşleminiz gerçekleştirilemiyor."
    DEĞİLSE // Tüm kontroller başarılı
      
      // --- Para Çekme İşlemi ---
      KULLANICI_HESAP_BAKIYESI = KULLANICI_HESAP_BAKIYESI - cekilecek_tutar
      ÇIKIŞ "Lütfen paranızı hazneden alınız."
      ÇIKIŞ "İşleminiz başarıyla tamamlanmıştır."
      ÇIKIŞ "Kalan Bakiyeniz: " + KULLANICI_HESAP_BAKIYESI + " TL"

      // --- Fiş Talebi ---
      GİRİŞ fis_isteği: "İşlem fişi istiyor musunuz? (E/H)"

      EĞER (fis_isteği == "E" veya fis_isteği == "e")
        ÇIKIŞ "Fişiniz yazdırılıyor. Lütfen bekleyiniz."
      SON EĞER

    SON EĞER

  SON EĞER

  // --- Son Mesaj ---
  ÇIKIŞ "İyi günler dileriz. Lütfen kartınızı almayı unutmayınız."

BİTİR
