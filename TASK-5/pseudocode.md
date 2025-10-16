BASLA

// Sistem durumu
SISTEM_AKTIF ← TRUE
GUNLUK_LIMIT ← 1000   // örnek: günlük alarm tetik sayısı limiti
ALARM_SAYACI ← 0

FONKSIYON OKU_SENSORLER()
    HAREKET ← OKU(HAREKET_SENSÖRÜ)
    KAPI ← OKU(KAPI_SENSÖRÜ)
    PENCERE ← OKU(PENCERE_SENSÖRÜ)
    CAM_SPEK ← OKU(KAMERA_HAREKET_ANALIZI) // opsiyonel
    ZAMAN ← SU_AN
    DON -> {HAREKET, KAPI, PENCERE, CAM_SPEK, ZAMAN}
SON

FONKSIYON TEHDIT_DEGERLENDIR(sensörler)
    DERECE ← 0
    EGER sensörler.HAREKET = ALGILANDI ISE
        DERECE ← DERECE + 1
    SON
    EGER sensörler.KAPI = AÇIK VEYA sensörler.PENCERE = AÇIK ISE
        DERECE ← DERECE + 2
    SON
    EGER sensörler.CAM_SPEK = ŞÜPHELİ_HAREKET ISE
        DERECE ← DERECE + 2
    SON
    // sonuç: 0 = yok, 1 = düşük, 2-3 = orta, 4+ = yüksek
    EGER DERECE >= 4 ISE
        DON 3
    DEGILSE EGER DERECE >= 2 ISE
        DON 2
    DEGILSE EGER DERECE >= 1 ISE
        DON 1
    DEGILSE
        DON 0
    SON
SON

FONKSIYON EVDE_MI()
    // Ev sahibi cihaz bağlı mı / geofence / manuel mod kontrolü
    EGER TELEFON_BAGLI = TRUE VEYA KULLANICI_STATUS = EVDE ISE
        DON Evet
    DEGILSE
        DON Hayir
    SON
SON

FONKSIYON GONDER_BILDIRIM(alarm_seviye, detay)
    GONDER_SMS(alarm_seviye, detay)
    GONDER_APP_PUSH(alarm_seviye, detay)
    GONDER_EMAIL(alarm_seviye, detay)
SON

FONKSIYON KAMERA_AKTIF_ET(SURE)
    KAMERA.START_RECORDING(SURE)
SON

// Ana döngü — 7/24 çalışır
DONGU (SISTEM_AKTIF = TRUE) BASLA

    sensörler ← OKU_SENSORLER()
    alarm_seviyesi ← TEHDIT_DEGERLENDIR(sensörler)

    EGER alarm_seviyesi = 0 ISE
        // hiçbir tehdit yok, kısa bekle ve döngüye devam
        YAZ("Durum: Normal. Bekleniyor...")
        BEKLE(3 SANİYE)
        DONA // döngü başına dön
    SON

    // Tehdit algılandı
    YAZ("Tehdit algılandı. Seviye: " + alarm_seviyesi)

    // Yanlış alarma karşı kontrol
    sahibEvde ← EVDE_MI()
    EGER sahibEvde = Evet ISE
        YAZ("Ev sahibi evde → yanlış alarm şüphesi. Alarm sıfırlanıyor.")
        // isteğe bağlı: bildirim gönderme (bilgilendirme)
        GONDER_APP_PUSH("Yanlış alarm", "Sistem tarafından otomatik sıfırlandı.")
        BEKLE(2 SANİYE)
        DONA
    SON

    // Günlük limit kontrolü
    EGER ALARM_SAYACI >= GUNLUK_LIMIT ISE
        YAZ("Günlük alarm limiti aşıldı. Bildirim: destek ekibine.")
        GONDER_EMAIL("Limit aşıldı", "Günlük alarm tetik sayısı limiti aşıldı.")
        BEKLE(60 SANİYE)
        DONA
    SON

    // Alarm tetikle
    ALARM_SAYACI ← ALARM_SAYACI + 1
    YAZ("Alarm tetikleniyor. Seviye: " + alarm_seviyesi)

    // Kamera kaydını başlat (örneğin 60 sn)
    KAMERA_AKTIF_ET(60 SANİYE)

    // Alarm eylemleri seviye bazlı
    EGER alarm_seviyesi = 1 ISE
        ÇAL_SIREN(KISA)
        GONDER_BILDIRIM("Düşük", sensörler)
    SON
    EGER alarm_seviyesi = 2 ISE
        ÇAL_SIREN(ORTA)
        GONDER_BILDIRIM("Orta", sensörler)
        // opsiyonel: dış kapıya ışık yak
        ISIK_YAK('DısKapı')
    SON
    EGER alarm_seviyesi = 3 ISE
        ÇAL_SIREN(UZUN)
        GONDER_BILDIRIM("Yüksek", sensörler)
        // yetkili servisi arama / polis bildirimi opsiyonel
        // OTOMATIK_MUDAHALE()
    SON

    // Bekle & tekrar kontrol et — alarm devam mı?
    SUREC_BASLANGIC ← SU_AN
    TEKRAR_SAYACI ← 0
    DONGU (TEKRAR_SAYACI < 5) BASLA
        BEKLE(10 SANİYE)
        sensörler2 ← OKU_SENSORLER()
        yeni_seviye ← TEHDIT_DEGERLENDIR(sensörler2)

        EGER yeni_seviye = 0 ISE
            YAZ("Tehdit sona erdi. Alarm durumu kontrol ediliyor...")
            // Opsiyonel: kullanıcıdan onay bekle (app üzerinden)
            EGER KULLANICI_ONAYI_ALINDI() ISE
                YAZ("Kullanıcı onayıyla alarm sıfırlandı.")
                DURDUR_SIREN()
                KAMERA.DURDUR()
                GONDER_APP_PUSH("Alarm sonlandı", "Kullanıcınız alarmı sonlandırdı.")
                GIRIS -> DONGU_BITIR
            SON
            // eğer kullanıcı onayı yoksa alarm otomatik sıfırlanabilir veya devam edebilir
            DURDUR_SIREN()
            KAMERA.DURDUR()
            GONDER_BILDIRIM("Alarm sonlandı (otomatik)", sensörler2)
            GIRIS -> DONGU_BITIR
        DEGILSE
            // hala tehdit varsa tekrar sayacı artır
            YAZ("Tehdit devam ediyor. Seviye: " + yeni_seviye)
            TEKRAR_SAYACI ← TEKRAR_SAYACI + 1
            // gerekirse seviye yükseltme
            EGER yeni_seviye > alarm_seviyesi ISE
                alarm_seviyesi ← yeni_seviye
                YAZ("Alarm seviyesi yükseltildi: " + alarm_seviyesi)
                GONDER_BILDIRIM("Seviye yükseldi", sensörler2)
            SON
        SON
    SON DONGU

    // Döngü çıkış etiketi
    DONGU_BITIR:

    // İsteğe bağlı: alarm sıfırlama veya devam ettirme kontrolü (uzaktan veya fiziksel panel)
    EGER KULLANICI_SIFIRLADI() ISE
        YAZ("Kullanıcı tarafından alarm sıfırlandı.")
        DURDUR_SIREN()
        KAMERA.DURDUR()
    SON
    // ana döngü devam eder
SON DONGU

BITIR
