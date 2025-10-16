BAŞLA

  // Öğrenci girişi
  YAZ "Öğrenci numarasını girin:"
  OKU ogrenciNo
  YAZ "Şifreyi girin:"
  OKU sifre

  EĞER ogrenciNo ve sifre DOĞRU ise
      YAZ "Giriş başarılı"
  DEĞİLSE
      YAZ "Geçersiz giriş bilgileri!"
      BİTİR
  SON

  // Ders listesi gösterimi
  DERS_LISTESİ ← TÜM_DERSLERİ_GETİR()
  YAZ "Mevcut dersler:"
  HER DERS için DERS_LISTESİ içinde
      YAZ DERS.ad, DERS.kredi, DERS.kontenjan
  SON

  toplamKredi ← 0
  SECİLEN_DERSLER ← boş liste

  // Ders ekleme döngüsü
  DÖNGÜ
      YAZ "Eklemek istediğiniz ders kodunu girin (bitirmek için 0):"
      OKU dersKodu

      EĞER dersKodu = 0 İSE
          ÇIK DÖNGÜDEN
      SON

      DERS ← DERS_BUL(dersKodu)

      // Kontroller
      EĞER DERS.kontenjan > 0 İSE
          EĞER ÖN_KOŞUL_TAMAMLANMIŞ_MI(DERS) = EVET İSE
              EĞER ZAMAN_ÇAKIŞMASI_YOK_MU(DERS, SECİLEN_DERSLER) = EVET İSE
                  EĞER toplamKredi + DERS.kredi ≤ 35 İSE
                      SECİLEN_DERSLER’e DERS EKLE
                      toplamKredi ← toplamKredi + DERS.kredi
                      YAZ "Ders eklendi: ", DERS.ad
                  DEĞİLSE
                      YAZ "Kredi limiti aşıldı! Bu dersi ekleyemezsiniz."
                  SON
              DEĞİLSE
                  YAZ "Zaman çakışması var! Dersi ekleyemezsiniz."
              SON
          DEĞİLSE
              YAZ "Ön koşul dersi alınmamış!"
          SON
      DEĞİLSE
          YAZ "Dersin kontenjanı dolu!"
      SON
  SON_DÖNGÜ

  // Danışman onayı kontrolü
  YAZ "GPA değerini girin:"
  OKU GPA
  EĞER GPA < 2.5 İSE
      YAZ "Danışman onayı gerekli. Lütfen danışmanınızdan onay alın."
  DEĞİLSE
      YAZ "Danışman onayına gerek yok."
  SON

  // Kayıt özeti ve onaylama
  YAZ "Seçilen Dersler:"
  HER DERS için SECİLEN_DERSLER içinde
      YAZ DERS.ad, " (", DERS.kredi, " kredi)"
  SON
  YAZ "Toplam Kredi: ", toplamKredi

  YAZ "Kaydı onaylıyor musunuz? (E/H)"
  OKU cevap
  EĞER cevap = 'E' İSE
      YAZ "Ders kaydı tamamlandı!"
  DEĞİLSE
      YAZ "Kayıt iptal edildi."
  SON

BİTİR
