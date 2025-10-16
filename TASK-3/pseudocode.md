Başla

// Veri yapıları
HastaListesi = []         // Hasta bilgilerini saklamak için
RandevuListesi = []       // Randevuları saklamak için

Fonksiyon HastaEkle()
    Ekrana "Hasta Adını Girin: "
    Ad = KullanıcıdanAl()
    Ekrana "Hasta Soyadını Girin: "
    Soyad = KullanıcıdanAl()
    Ekrana "Hasta TC Kimlik No Girin: "
    TC = KullanıcıdanAl()
    
    YeniHasta = { "Ad": Ad, "Soyad": Soyad, "TC": TC }
    HastaListesi.Ekle(YeniHasta)
    Ekrana "Hasta başarıyla eklendi."

Fonksiyon RandevuAl()
    Ekrana "Randevu almak için TC girin: "
    TC = KullanıcıdanAl()
    
    Hasta = HastaListesi.IçindenBul(TC)
    Eğer Hasta yoksa ise
        Ekrana "Hasta bulunamadı! Lütfen önce kayıt olun."
        Dön
    Bitir
    
    Ekrana "Randevu Tarihini Girin (gg/aa/yyyy): "
    Tarih = KullanıcıdanAl()
    Ekrana "Randevu Saatini Girin (ss:dd): "
    Saat = KullanıcıdanAl()
    
    YeniRandevu = { "TC": TC, "Tarih": Tarih, "Saat": Saat }
    RandevuListesi.Ekle(YeniRandevu)
    Ekrana "Randevu başarıyla alındı."

Fonksiyon RandevularıGörüntüle()
    Eğer RandevuListesi.BoşMu() ise
        Ekrana "Henüz randevu yok."
        Dön
    Bitir
    
    Ekrana "Randevular:"
    Her Randevu için RandevuListesi
        Hasta = HastaListesi.IçindenBul(Randevu.TC)
        Ekrana "Hasta: " + Hasta.Ad + " " + Hasta.Soyad + ", Tarih: " + Randevu.Tarih + ", Saat: " + Randevu.Saat

// Ana Menü
Sonsuz Döngü
    Ekrana "1. Hasta Ekle"
    Ekrana "2. Randevu Al"
    Ekrana "3. Randevuları Görüntüle"
    Ekrana "4. Çıkış"
    
    Seçim = KullanıcıdanAl()
    
    Eğer Seçim == 1 ise
        HastaEkle()
    Eğer Seçim == 2 ise
        RandevuAl()
    Eğer Seçim == 3 ise
        RandevularıGörüntüle()
    Eğer Seçim == 4 ise
        Çık
    Aksi halde
        Ekrana "Geçersiz seçim, tekrar deneyin."

Bitir
