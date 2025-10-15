BAŞLA

    bakiye ← 5000
    gunluk_limit ← 2000
    deneme_hakki ← 3
    dogru_pin ← 1234

    DÖNGÜ (deneme_hakki > 0) YAP
        YAZ "Lütfen PIN kodunuzu giriniz: "
        OKU pin

        EĞER pin = dogru_pin İSE
            YAZ "PIN doğru. İşleme devam ediliyor..."
            ÇIK DÖNGÜDEN
        DEĞİLSE
            deneme_hakki ← deneme_hakki - 1
            YAZ "Hatalı PIN! Kalan deneme hakkınız: ", deneme_hakki
        SON
    SON DÖNGÜ

    EĞER deneme_hakki = 0 İSE
        YAZ "Kartınız bloke olmuştur. Lütfen banka ile iletişime geçiniz."
        BİTİR
    SON

    TEKRARLA

        YAZ "Çekmek istediğiniz tutarı giriniz (20 TL katları olmalı): "
        OKU tutar

        EĞER tutar MOD 20 ≠ 0 İSE
            YAZ "Hatalı giriş! Tutar 20 TL’nin katı olmalıdır."
        DEĞİLSE EĞER tutar > bakiye İSE
            YAZ "Yetersiz bakiye! Mevcut bakiye: ", bakiye
        DEĞİLSE EĞER tutar > gunluk_limit İSE
            YAZ "Tutar günlük limit olan ", gunluk_limit, " TL’yi aşıyor!"
        DEĞİLSE
            bakiye ← bakiye - tutar
            gunluk_limit ← gunluk_limit - tutar
            YAZ "İşlem başarılı! Yeni bakiyeniz: ", bakiye
            YAZ "Kalan günlük limitiniz: ", gunluk_limit
        SON

        YAZ "Başka bir işlem yapmak ister misiniz? (E/H): "
        OKU cevap

    TEKRARLA (cevap = "E" VEYA cevap = "e") OLDUĞU SÜRECE

    YAZ "Teşekkür ederiz. Kartınızı almayı unutmayınız."

BİTİR
