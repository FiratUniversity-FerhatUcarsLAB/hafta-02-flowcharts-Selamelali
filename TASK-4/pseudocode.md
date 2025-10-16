Başla

Öğrenci giriş yap
Eğer giriş başarılı ise
    Öğrenci bilgilerini göster
    Mevcut ders listesini getir
    Maksimum kredi limitini belirle
    Toplam kayıtlı kredi = 0

    Döngü: Ders seçmek isteyene kadar
        Ders seç
        Eğer ders kontenjanı dolu ise
            "Ders dolu, başka ders seçiniz" mesajı göster
        Yoksa eğer ders zaman çakışması varsa
            "Zaman çakışması, başka ders seçiniz" mesajı göster
        Yoksa eğer dersin ön koşulu alınmamış ise
            "Ön koşul dersler alınmamış, başka ders seçiniz" mesajı göster
        Yoksa eğer ders eklendiğinde maksimum kredi aşılacaksa
            "Kredi limiti aşılacak, başka ders seçiniz" mesajı göster
        Yoksa
            Dersi kaydet
            Toplam kayıtlı kredi += ders kredisi
            "Ders başarıyla eklendi" mesajı göster
        Bitir
        
    Kullanıcı kayıt işlemini bitirmek isterse
        Ders kayıtlarını onayla
        Dersleri ve toplam krediyi göster
        "Kayıt işlemi tamamlandı" mesajı göster

Aksi takdirde
    "Giriş başarısız" mesajı göster

Bitir
