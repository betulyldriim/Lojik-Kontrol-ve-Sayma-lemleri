Bu depo, Siemens TIA Portal ile geliştirilmiş, endüstriyel otomasyonun temel yapı taşlarını gösteren çeşitli bağımsız projeleri içerir. Projeler, bir konveyör hattı üzerindeki ürünlerin sayılması ve kontrol edilmesi, motorların SR ve RS blokları ile kalıcı olarak çalıştırılması ve durdurulması, ve bir vana pozisyonunun izlenmesi gibi senaryoları kapsar. Bu, temel PLC programlama mantıklarını ve HMI arayüzlerinin prosesi nasıl görselleştirdiğini öğrenmek için ideal bir kaynaktır.

Çalışma Mantığı
Bu gruptaki projeler, her biri farklı bir amaca hizmet eden birden fazla bağımsız senaryoyu kapsar. Çalışma mantıkları aşağıda her bir senaryo için ayrı ayrı açıklanmıştır.

1. Konveyör Hattı ve Kutulama Prosesi Kontrolü
Bu senaryo, bir konveyör hattındaki ürünlerin sensörler aracılığıyla sayılmasını ve belirli bir sayıda ürün biriktirildiğinde bir kutuya veya kovaya yerleştirilmesini simüle eder.

Sayıcı (S_CU): %I0.3 (Sensor 1) girişi, konveyör üzerindeki ürünleri sayar ve bu sayım değerini %MW0 (SAYAC) hafıza kelimesinde tutar.

Kutu Doluluğu Kontrolü: %MW0 (SAYAC) değeri, bir karşılaştırma bloğu (==) ile 3'e eşitse %Q4.2 (Full) çıkışını aktif eder. Bu, kutunun dolduğunu gösterir.

Resetleme: %I0.2 (Reset) girişi, sayacı sıfırlayarak yeni bir dolum döngüsü başlatır.

HMI Görselleştirme: HMI ekranı, iki konveyör hattını, motorları (Motor 1, Motor 2), sensörleri (S1, S2), ürünleri (kırmızı kutular) ve dolum için bekleyen bir kovayı (mavi kutu) görselleştirir. START, STOP ve RESET butonları, prosesin operatör tarafından manuel olarak kontrol edilmesini sağlar.

2. Tek Butonla Start/Stop Kontrolü
Bu proje, tek bir butonu kullanarak bir motoru başlatma ve durdurma lojiğini gösterir.

Lojik: %I0.0 (START-STOP) girişi, SR (Set-Reset) lojik bloğuyla birleşik olarak kullanılır. Butona basıldığında (%I0.0 aktif olduğunda), S girişi tetiklenir ve %Q0.0 (MOTOR) çıkışı aktif olur.

Durum Hafızası: %M0.0 (Tag_1), SR bloğunun çıkışını hafızada tutar.

Toggle Mantığı: %I0.0'ın negatif kenarı (N_TRIG) veya bir başka buton (%I0.0'ın kendisi) SR bloğunun R1 girişini tetikleyerek motoru durdurur. Bu, tek bir butona her basıldığında motorun durumunun (açık/kapalı) değişmesini sağlar.

3. Vana Kontrolü ve Alarm İzleme
Bu senaryo, bir vana pozisyonunu izlemek ve komut-durum uyuşmazlığında alarm vermek için özel bir fonksiyon bloğu (valve_2Pos [FB1]) kullanır.

Fonksiyon Bloğu: valve_2Pos bloğu, vananın açılma (Valve_Cmd, %I0.0) ve kapanma komutlarını, açık (Opened_Sw, %I0.1) ve kapalı (Closed_Sw, %I0.2) limit anahtarlarını girdi olarak alır.

Alarm Lojiği: İçerideki program, vanaya bir komut verildiğinde (örneğin açılma) ancak belirtilen bir süre (Alarm_Tmr, T#5S) içinde beklenen konuma ulaşmadığında bir TON (On-Delay Timer) zamanlayıcısını tetikler. Zamanlayıcı tamamlandığında, %M10.1 (Valve_Alm_CmdDisc_Alarm) çıkışını aktif ederek bir alarm verir.

Sonuç
