BAŞLA

   // --- 1. SİSTEMİN BAŞLANGIÇ AYARLARI VE DEĞİŞKENLERİ ---
   TANIMLA sistemAcikMi, hareketVarMi, kapiPencereAcikMi: MANTIKSAL
   TANIMLA sistemModu: METİN // Olası değerler: "Evde", "Dışarıda"
   TANIMLA TEKRAR_KONTROL_SURESI: SABİT TAMSAYI = 15 // saniye

   // --- 2. SİSTEMİN GÜÇ KONTROLÜ VE MOD YÜKLENMESİ ---
   YAZ "Sistem başlatılıyor..."
   sistemAcikMi = SisteminGucDurumunuOku()
   
   EĞER (sistemAcikMi == DOĞRU) ISE
      sistemModu = KayitliSistemModunuOku()
      YAZ "Sistem durumu: AKTİF. Mod: " + sistemModu
   DEĞİLSE
      YAZ "Sistem durumu: KAPALI. İşlem yapılmayacak."
      BİTİR
   SON EĞER

   // --- 3. ANA SENSÖR DÖNGÜSÜ (Sistem açıksa başlar) ---
   SÜREKLİ DÖNGÜ
      
      // 3.1. VERİ TOPLAMA
      hareketVarMi = HareketSensorunuOku()
      
      // 3.2. KARAR VERME
      SEÇİM DURUMU (sistemModu)

         // --- DURUM A: "DIŞARIDA" MODU (YÜKSEK GÜVENLİK) ---
         DURUM "Dışarıda":
            EĞER (hareketVarMi == DOĞRU) ISE
               // AŞAMA 1: İLK TESPİT
               YAZ "İLK TESPİT: 'Dışarıda' modunda hareket algılandı!"
               KamerayiAktifEt()
               KayitBaslat()
               FotografCek()
               KullaniciyaBildirimGonder("UYARI! Evde hareket algılandı. Durum " + TEKRAR_KONTROL_SURESI + " sn içinde tekrar kontrol edilecek.", "YÜKSEK")

               // AŞAMA 2: BEKLEME VE TEKRAR KONTROL
               YAZ "Tekrar kontrol için bekleniyor..."
               BEKLE(TEKRAR_KONTROL_SURESI)
               
               hareketVarMi = HareketSensorunuOku()
               
               EĞER (hareketVarMi == DOĞRU) ISE
                  // AŞAMA 3: TEHLİKE DOĞRULANDI, ALARM!
                  YAZ "ALARM: Hareket devam ediyor! Tehlike doğrulandı!"
                  
                  kapiPencereAcikMi = KapiPencereSensorleriniOku()
                  EĞER (kapiPencereAcikMi == DOĞRU) ISE
                     KullaniciyaBildirimGonder("ACİL DURUM! Hareket devam ediyor ve KAPI/PENCERE AÇIK!", "KRİTİK")
                  DEĞİLSE
                     KullaniciyaBildirimGonder("ACİL DURUM! Evdeki hareket devam ediyor!", "KRİTİK")
                  SON EĞER
                  
                  SireniCal()
               DEĞİLSE
                  // Tehlike durumu ortadan kalktı
                  YAZ "Durum normale döndü. Algılanan hareket durdu."
                  KullaniciyaBildirimGonder("Bilgi: Hareket durdu, yanlış alarm olabilir. Kayıt alındı.", "DÜŞÜK")
                  KaydiDurdur()
                  KamerayiPasifEt()
               SON EĞER
            SON EĞER

         // --- DURUM B: "EVDE" MODU (BİLGİLENDİRME AMAÇLI) ---
         DURUM "Evde":
            EĞER (hareketVarMi == DOĞRU) ISE
               YAZ "BİLGİ: 'Evde' modunda hareket algılandı."
               KamerayiAktifEt()
               KullaniciyaBildirimGonder("Bilgi: Siz evdeyken bir hareket algılandı.", "DÜŞÜK")
               BEKLE(5 saniye)
               KamerayiPasifEt()
            SON EĞER

         // --- DURUM C: GEÇERSİZ MOD ---
         VARSAYILAN:
            YAZ "HATA: Tanımsız sistem modu! Lütfen kontrol edin."
            BEKLE(10 saniye)

      SON SEÇİM DURUMU

      BEKLE(1 saniye) 
      
   SON DÖNGÜ

BİTİR
