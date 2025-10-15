BAŞLA

    // Kullanıcı Girişi
    YAZ "Kullanıcı girişi yapınız (E-posta ve şifre girin): "
    OKU eposta, sifre

    EĞER (eposta ve sifre doğrulanır) İSE
        YAZ "Giriş başarılı, hoş geldiniz!"
    DEĞİLSE
        YAZ "Hatalı giriş bilgisi!"
        BİTİR
    SON

    // Sepet oluşturma
    sepet ← boş_liste
    toplam_tutar ← 0
    indirim ← 0
    kargo_ucreti ← 0

    DÖNGÜ (sonsuz)
        YAZ "Ürün eklemek istiyor musunuz? (E/H): "
        OKU cevap

        EĞER cevap = "H" VEYA cevap = "h" İSE
            ÇIK DÖNGÜDEN
        SON

        YAZ "Ürün ID giriniz: "
        OKU urun_id
        YAZ "Adet giriniz: "
        OKU adet

        // Stok kontrolü
        EĞER stok[urun_id] >= adet İSE
            YAZ "Ürün sepete eklendi."
            sepet ← sepet + (urun_id, adet)
            toplam_tutar ← toplam_tutar + (fiyat[urun_id] * adet)
        DEĞİLSE
            YAZ "Yetersiz stok!"
        SON
    SON DÖNGÜ

    // Sepet boşsa kontrol
    EĞER sepet = boş_liste İSE
        YAZ "Sepetiniz boş, işlem sonlandırıldı."
        BİTİR
    SON

    // İndirim kodu kontrolü
    YAZ "İndirim kodunuz var mı? (E/H): "
    OKU cevap

    EĞER cevap = "E" VEYA cevap = "e" İSE
        YAZ "İndirim kodunu giriniz: "
        OKU kod

        EĞER kod = "YENI10" İSE
            indirim ← toplam_tutar * 0.10
            YAZ "%10 indirim uygulandı."
        DEĞİLSE EĞER kod = "KARGO BEDAVA" İSE
            kargo_ucreti ← 0
            YAZ "Kargo ücreti kaldırıldı."
        DEĞİLSE
            YAZ "Geçersiz indirim kodu!"
        SON
    SON

    // Kargo hesaplama
    EĞER toplam_tutar >= 1000 İSE
        kargo_ucreti ← 0
        YAZ "Kargo ücretsiz."
    DEĞİLSE
        kargo_ucreti ← 49
        YAZ "Kargo ücreti 49 TL olarak eklendi."
    SON

    // Toplam tutar hesaplama
    odenecek_tutar ← toplam_tutar - indirim + kargo_ucreti

    YAZ "Toplam ürün tutarı: ", toplam_tutar
    YAZ "İndirim: ", indirim
    YAZ "Kargo ücreti: ", kargo_ucreti
    YAZ "Ödenecek toplam: ", odenecek_tutar

    // Ödeme aşaması
    YAZ "Ödeme yöntemi seçiniz:"
    YAZ "1 - Kredi Kartı"
    YAZ "2 - Havale/EFT"
    YAZ "3 - Kapıda ödeme"
    OKU secim

    EĞER secim = 1 İSE
        YAZ "Kart bilgilerinizi giriniz (numara, tarih, CVV): "
        OKU kart_bilgi
        YAZ "Ödeme onaylanıyor..."
        EĞER ödeme_başarılı İSE
            YAZ "Ödeme başarılı!"
        DEĞİLSE
            YAZ "Ödeme başarısız! Lütfen tekrar deneyiniz."
            BİTİR
        SON

    DEĞİLSE EĞER secim = 2 İSE
        YAZ "Havale/EFT talimatı gönderildi. Lütfen ödeme yapınız."
    DEĞİLSE EĞER secim = 3 İSE
        YAZ "Kapıda ödeme seçildi."
    DEĞİLSE
        YAZ "Geçersiz seçim!"
        BİTİR
    SON

    // Sipariş oluşturma
    YAZ "Siparişiniz hazırlanıyor..."
    siparis_no ← rastgele_numara_olustur()
    YAZ "Sipariş numaranız: ", siparis_no

    // Stok güncelleme
    DÖNGÜ (her urun sepette)
        stok[urun.id] ← stok[urun.id] - urun.adet
    SON DÖNGÜ

    YAZ "Sipariş başarıyla oluşturuldu!"
    YAZ "Teşekkür ederiz, iyi alışverişler."

BİTİR
