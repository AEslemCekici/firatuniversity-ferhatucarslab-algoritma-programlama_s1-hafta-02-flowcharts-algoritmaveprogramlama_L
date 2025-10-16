BAŞLA

// --- SİSTEM DEĞİŞKENLERİ VE SABİTLER ---
TANIMLA ogrenciNo, sifre, kullaniciSecimi: METİN
TANIMLA girisBasarili: MANTIKSAL = YANLIŞ
TANIMLA KREDI_LIMITI: SABİT TAMSAYI = 35

// --- ÖĞRENCİYE ÖZEL YÜKLENECEK VERİLER ---
TANIMLA secilenDersler: DERS_LİSTESİ
TANIMLA toplamKredi: TAMSAYI
TANIMLA kayitOnayDurumu: METİN // Örnek: "Taslak", "Onay Bekliyor", "Onaylandı"
TANIMLA kayitGecmisi: LİSTE

// --- ADIM 1: KULLANICI GİRİŞİ ---
TANIMLA girisDenemeSayisi: TAMSAYI = 0
DÖNGÜ (girisDenemeSayisi < 3 VE girisBasarili == YANLIŞ)
    YAZ "Öğrenci Numarası:"
    OKU ogrenciNo
    YAZ "Şifre:"
    OKU sifre

    EĞER (SistemdeKullaniciDogruMu(ogrenciNo, sifre)) ISE
        girisBasarili = DOĞRU
        YAZ "Giriş Başarılı!"
    DEĞİLSE
        girisDenemeSayisi = girisDenemeSayisi + 1
        YAZ "Hatalı giriş. Kalan hak: " + (3 - girisDenemeSayisi)
    SON EĞER
SON DÖNGÜ

// --- ADIM 2: ANA İŞLEM DÖNGÜSÜ ---
EĞER (girisBasarili == DOĞRU) ISE
    // Öğrencinin verilerini sistemden yükle
    secilenDersler = VeritabanindanDersleriGetir(ogrenciNo)
    toplamKredi = KrediHesapla(secilenDersler)
    kayitOnayDurumu = OnayDurumuGetir(ogrenciNo)
    kayitGecmisi = GecmisKayitlariGetir(ogrenciNo)

    // Ana menü döngüsü
    DÖNGÜ (kullaniciSecimi != "5")
        YAZ "\n--- DERS KAYIT SİSTEMİ ---"
        YAZ "Onay Durumu: " + kayitOnayDurumu + " | Toplam Kredi: " + toplamKredi
        EĞER (toplamKredi > KREDI_LIMITI) ISE
            YAZ "UYARI: Kredi limiti aşıldı, danışman onayı gerekli."
        SON EĞER
        YAZ "1. Ders Ekle"
        YAZ "2. Ders Çıkar"
        YAZ "3. Kayıt Geçmişini Görüntüle"
        YAZ "4. Kaydı Tamamla ve Onaya Gönder"
        YAZ "5. Çıkış Yap"
        OKU kullaniciSecimi

        SEÇİM DURUMU (kullaniciSecimi)
            DURUM "1": // Ders Ekle
                TumAcilanDersleriGoster()
                YAZ "Eklenecek dersin kodunu girin:"
                OKU eklenecekKod
                hedefDers = DersBilgisiniBul(eklenecekKod)

                // Tüm kontroller tek bir mantıksal sorguda birleştirilir
                kontenjanUygun = (hedefDers.doluKontenjan < hedefDers.kontenjan)
                onKosulTamam = OnKosulSaglandiMi(ogrenciNo, hedefDers.onKosul)
                cakismaYok = CakismiyorMu(hedefDers, secilenDersler)

                EĞER (kontenjanUygun VE onKosulTamam VE cakismaYok) ISE
                    secilenDersler.ekle(hedefDers)
                    toplamKredi = toplamKredi + hedefDers.kredi
                    kayitOnayDurumu = "Taslak" // Değişiklik yapıldı
                    YAZ hedefDers.adi + " eklendi."
                DEĞİLSE
                    YAZ "HATA: Ders eklenemedi (Kontenjan, ön koşul veya ders çakışması)."
                SON EĞER

            DURUM "2": // Ders Çıkar
                SeciliDersleriGoster(secilenDersler)
                YAZ "Çıkarılacak dersin kodunu girin:"
                OKU cikarilacakKod
                EĞER (secilenDersler.iceriyor(cikarilacakKod)) ISE
                    cikarilanDers = secilenDersler.getir(cikarilacakKod)
                    secilenDersler.sil(cikarilacakKod)
                    toplamKredi = toplamKredi - cikarilanDers.kredi
                    kayitOnayDurumu = "Taslak" // Değişiklik yapıldı
                    YAZ cikarilanDers.adi + " çıkarıldı."
                DEĞİLSE
                    YAZ "HATA: Bu ders zaten seçili değil."
                SON EĞER

            DURUM "3": // Kayıt Geçmişi
                YAZ "--- Kayıt Geçmişi ---"
                HER gecmisKayit İÇİN kayitGecmisi:
                    YAZ "Dönem: " + gecmisKayit.donem + " | Durum: " + gecmisKayit.durum
                SON HER

            DURUM "4": // Kaydı Tamamla
                EĞER (toplamKredi > KREDI_LIMITI) ISE
                    kayitOnayDurumu = "Danışman Onayı Bekliyor"
                    YAZ "Kayıt danışman onayına gönderildi."
                DEĞİLSE
                    kayitOnayDurumu = "Onaylandı"
                    YAZ "Kayıt otomatik olarak onaylandı."
                SON EĞER
                KayitlariVeritabaninaIsle(ogrenciNo, secilenDersler, kayitOnayDurumu)
                YAZ "İşlem tamamlandı. Durumunuz: " + kayitOnayDurumu

            DURUM "5": // Çıkış
                YAZ "Çıkış yapılıyor..."

            VARSAYILAN:
                YAZ "Geçersiz seçim."
        SON SEÇİM DURUMU
    SON DÖNGÜ
DEĞİLSE
    YAZ "Giriş hakkınız doldu. Sistem kilitlendi."
SON EĞER

BİTİR
