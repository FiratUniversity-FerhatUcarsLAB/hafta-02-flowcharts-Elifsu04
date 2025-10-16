BASLA
  YAZ "HASTANE RANDEVU VE TAHLİL SİSTEMİNE HOŞGELDİNİZ"

ANA_MENU:
  YAZ "Lütfen yapmak istediğiniz işlemi seçiniz:"
  YAZ "1 - Randevu Al"
  YAZ "2 - Tahlil Sonucu Görüntüle"
  YAZ "3 - Çıkış"
  OKU secim

  // ====== 1. RANDEVU ALMA MODÜLÜ ======
  EGER secim = 1 ISE
      YAZ "Lütfen TC Kimlik Numaranızı Giriniz:"
      OKU tcNo
      
      EGER KimlikDogru(tcNo) DEGILSE
          YAZ "Geçersiz veya hatalı TC kimlik numarası!"
          GIT ANA_MENU
      SON

      YAZ "Poliklinik Seçiniz:"
      PoliklinikleriListele()
      OKU poliklinik

      EGER PoliklinikVarMi(poliklinik) DEGILSE
          YAZ "Geçersiz poliklinik seçimi!"
          GIT ANA_MENU
      SON

      DoktorListesiniGöster(poliklinik)
      YAZ "Doktor Seçiniz:"
      OKU doktor

      EGER DoktorUygunMu(doktor) DEGILSE
          YAZ "Seçilen doktor uygun değil!"
          GIT ANA_MENU
      SON

      UygunSaatleriGöster(doktor)
      YAZ "Lütfen uygun bir saat seçiniz:"
      OKU saat

      EGER SaatUygunMu(doktor, saat) DEGILSE
          YAZ "Seçilen saat dolu, lütfen başka bir saat seçiniz."
          GIT ANA_MENU
      SON

      RandevuKaydet(tcNo, poliklinik, doktor, saat)
      YAZ "Randevunuz başarıyla oluşturuldu."
      SMSGönder(tcNo, "Randevunuz onaylandı: " + poliklinik + " - " + doktor + " - " + saat)
      YAZ "Onay SMS'i gönderildi."

      YAZ "Ana menüye dönmek ister misiniz? (E/H)"
      OKU cevap
      EGER cevap = "E" ISE
          GIT ANA_MENU
      DEGILSE
          YAZ "İyi günler dileriz!"
          BITIR
      SON


  // ====== 2. TAHLİL GÖRÜNTÜLEME MODÜLÜ ======
  DEGILSE EGER secim = 2 ISE
      YAZ "Lütfen TC Kimlik Numaranızı Giriniz:"
      OKU tcNo

      EGER KimlikDogru(tcNo) DEGILSE
          YAZ "Geçersiz veya hatalı TC kimlik numarası!"
          GIT ANA_MENU
      SON

      EGER TahlilKaydiVarMi(tcNo) DEGILSE
          YAZ "Tahlil kaydınız bulunamadı!"
          GIT ANA_MENU
      SON

      EGER SonucHazir(tcNo) DEGILSE
          YAZ "Tahlil sonuçlarınız henüz hazır değil, lütfen daha sonra tekrar deneyiniz."
          GIT ANA_MENU
      SON

      YAZ "Tahlil sonuçlarınız görüntüleniyor..."
      SonuclariGoster(tcNo)

      YAZ "Sonuçları PDF olarak indirmek ister misiniz? (E/H)"
      OKU indirme
      EGER indirme = "E" ISE
          PDFindir(tcNo)
          YAZ "PDF başarıyla indirildi."
      SON

      YAZ "Ana menüye dönmek ister misiniz? (E/H)"
      OKU cevap
      EGER cevap = "E" ISE
          GIT ANA_MENU
      DEGILSE
          YAZ "İyi günler dileriz!"
          BITIR
      SON


  // ====== 3. ÇIKIŞ ======
  DEGILSE EGER secim = 3 ISE
      YAZ "Sistemden çıkılıyor..."
      BITIR

  // ====== HATALI SEÇİM ======
  DEGILSE
      YAZ "Geçersiz seçim yaptınız!"
      GIT ANA_MENU
  SON
BİTİR
