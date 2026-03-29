# MeshGrid-Node-Firmware

**MeshGrid-Node** için resmi firmware dağıtım ve kurulum kılavuzu.  
Bu depo, MeshGrid ekosisteminde kullanılan desteklenen ESP32 tabanlı kartlar **ve gerekli LoRa radyo donanımı** için resmi firmware sürümlerini ve yükleme talimatlarını sağlar.

MeshGrid-Node; **şifreli sub-GHz LoRa mesh haberleşmesini** etkinleştirmek ve küçük ekipler ile saha odaklı kullanım senaryoları için **çevrimdışı koordinasyon ve taktik haritalama iş akışlarını** desteklemek üzere tasarlanmıştır.

> **Önemli:** Mevcut firmware derlemesi, desteklenen ESP32 kartları ile birlikte **EBYTE E22-900T22D LoRa modülü ve uyumlu bir anten** ile çalışacak şekilde tasarlanmıştır. LoRa radyo modülü ve anten, MeshGrid-Node’un çalışması için gerekli donanım bileşenleridir; isteğe bağlı değildir.

---

## İçindekiler

- [Genel Bakış](#genel-bakış)
- [Desteklenen Donanım](#desteklenen-donanım)
  - [Desteklenen Kartlar](#desteklenen-kartlar)
  - [Gerekli LoRa Modülü](#gerekli-lora-modülü)
  - [Gerekli Anten](#gerekli-anten)
- [Radyo Uyumluluğu ve Kullanıcı Sorumluluğu](#radyo-uyumluluğu-ve-kullanıcı-sorumluluğu)
- [Sürüm Kapsamı](#sürüm-kapsamı)
- [Firmware Kurulumu](#firmware-kurulumu)
  - [Gereksinimler](#gereksinimler)
  - [Önerilen Yükleme Yöntemi](#önerilen-yükleme-yöntemi)
  - [Kartın Bağlanması](#kartın-bağlanması)
  - [Seri Portun Bulunması](#seri-portun-bulunması)
  - [Firmware’in Yüklenmesi](#firmwarein-yüklenmesi)
  - [Manuel Bootloader Modu](#manuel-bootloader-modu)
  - [İsteğe Bağlı: Kurulum Öncesi Flash Belleği Silme](#isteğe-bağlı-kurulum-öncesi-flash-belleği-silme)
  - [Kurulumu Doğrulama](#kurulumu-doğrulama)
- [Sorun Giderme](#sorun-giderme)
- [Önemli Notlar](#önemli-notlar)
- [Sürüm Kullanım Bildirimi](#sürüm-kullanım-bildirimi)
- [Beklenen Bağlantı Yapısı](#beklenen-bağlantı-yapısı)
- [AT Modu Yapılandırma Uyarısı](#at-modu-yapılandırma-uyarısı)
- [Garanti Yok / Uyumluluk Feragati](#garanti-yok--uyumluluk-feragati)
- [Lisans](#lisans)

---

## Genel Bakış

MeshGrid-Node-Firmware, MeshGrid uyumlu düğümler için resmi firmware dağıtım deposudur.

Bu firmware; kısıtlı ortamlarda çevrimdışı kullanım için hafif ve şifreli bir LoRa mesh haberleşme katmanı sağlamak amacıyla tasarlanmıştır. Ana odak, GitHub Releases üzerinden dağıtılan önceden derlenmiş firmware ikilileri kullanılarak desteklenen ESP32 donanımları üzerinde pratik saha kullanımıdır.

Bu depo şu amaçlarla kullanılmak üzere tasarlanmıştır:

- Resmi firmware dağıtımı
- Firmware kurulum talimatları
- Sürüme özel ikili dosya bilgileri
- Desteklenen donanımlar için son kullanıcı yükleme rehberi

> MeshGrid-Node, **yalnızca ESP32 hedefli bir firmware değildir**. Bu firmware; desteklenen bir ESP32 kartı, **EBYTE E22-900T22D** LoRa radyo modülü ve **uyumlu bir anten** içeren bir düğüm yapılandırması için hazırlanmıştır.

---

## Desteklenen Donanım

### Desteklenen Kartlar

Mevcut firmware sürümü, aşağıdaki ESP32 tabanlı kartlar için tasarlanmıştır:

- **ESP32-S**
- **ESP32 DevKit V1**

### Gerekli LoRa Modülü

Mevcut MeshGrid-Node firmware derlemesi, hedef donanımda aşağıdaki LoRa radyo modülünü bekler:

- **EBYTE E22-900T22D**

EBYTE E22-900T22D, bu firmware sürümü için desteklenen MeshGrid-Node donanım yapılandırmasının zorunlu bir parçasıdır.

Önemli uyumluluk notları:

- Yalnızca desteklenen bir ESP32 kartı **yeterli değildir**
- Firmware, düğümün **EBYTE E22-900T22D** modülünü içermesini bekler
- Önceden derlenmiş ikili dosya, beklenen MeshGrid-Node bağlantı düzeni ve donanım yerleşimi için hazırlanmıştır
- Farklı bir LoRa modülü, farklı pin eşlemesi veya farklı bir radyo bağlantı düzeni kullanılırsa firmware başarıyla yüklenebilir; ancak LoRa haberleşmesi çalışmayabilir veya öngörülemez davranabilir
- Seçilen firmware ikilisinin hedef karta, radyo modülüne, donanım revizyonuna ve beklenen MeshGrid-Node bağlantı yapılandırmasına uygun olduğunu her zaman doğrulayın

### Gerekli Anten

**EBYTE E22-900T22D** ile çalışmak için ayrıca **uyumlu bir anten** gereklidir.

Önemli anten notları:

- Radyo modülü tek başına eksiksiz bir RF çözümü olarak değerlendirilmemelidir
- Modülü uygun bir anten olmadan çalıştırmak düşük performansa, kararsız çalışmaya veya donanım riski oluşmasına neden olabilir
- Kullanılan anten, seçilen çalışma bandına ve hedef bölgesel kullanıma uygun olmalıdır
- Yapılandırılan frekans bandı, kurulum koşulları ve geçerli yasal gereklilikler açısından elektriksel ve mevzuatsal olarak uygun bir anten seçmek kullanıcının sorumluluğundadır

---

## Radyo Uyumluluğu ve Kullanıcı Sorumluluğu

Sub-GHz bantlarında çalışan radyo cihazları; **bölgesel, ulusal ve yerel RF düzenlemelerine** tabidir. Bu kurallar ülkeye ve yargı alanına göre değişir ve radyo yapılandırması ile çalıştırılması üzerinde gereklilikler veya kısıtlamalar getirebilir.

Bu gereklilikler şunları içerebilir, ancak bunlarla sınırlı değildir:

- izin verilen frekans bantları
- kanal veya merkez frekans seçimi
- iletim gücü sınırları
- bant genişliği ve işgal edilen spektrum sınırları
- duty cycle veya kanal erişim kısıtlamaları
- modülasyonla ilgili ayarlar
- antenle ilgili sınırlamalar
- uygun olan yerlerde sertifikasyon, kayıt veya diğer yasal yükümlülükler

> **Yasal Uyarı:** **EBYTE E22-900T22D**, yapılandırılabilir bir radyo modülüdür. Modül **AT yapılandırma modunda** veya başka bir yapılandırılabilir arayüz üzerinden çalıştırılıyorsa, bölgeye özgü RF parametrelerinin seçimi ve uygulanması **tamamen kullanıcının sorumluluğundadır**.

Bu, uygun olduğu durumlarda şunları kapsar:

- cihazın yerel olarak izin verilen **868 MHz**, **900 MHz** veya diğer izinli bölgesel çalışma aralıkları için yapılandırılıp yapılandırılmayacağını seçmek
- yapılandırılan radyo parametrelerinin cihazın kullanıldığı ülke veya bölgenin yasa ve düzenlemelerine uygun olmasını sağlamak
- seçilen antenin ve ortaya çıkan RF davranışının yerel yasal gerekliliklerle uyumlu olmasını sağlamak

Firmware dağıtıcısı, depo yöneticisi ve sürüm yayımlayıcısı; varsayılan veya kullanıcı tarafından seçilen radyo parametrelerinin her ülke, bölge veya kullanım ortamında yasal olduğunu **garanti etmez**.

Kullanıcılar, cihazı çalıştırmadan önce geçerli tüm yasaları, düzenlemeleri, lisans kurallarını ve teknik sınırları bağımsız olarak doğrulamak ve bunlara uymak zorundadır.

Belirli bir yapılandırmanın bulunduğunuz yerde yasal olup olmadığından emin değilseniz, ilgili yargı alanındaki kurallara göre incelenene kadar **iletim yapmayın**.

---

## Sürüm Kapsamı

Bu depo, `.bin` formatında **önceden derlenmiş firmware ikili dosyaları** dağıtır.

Sürüme bağlı olarak firmware aşağıdaki biçimlerden biriyle sağlanabilir:

1. **Tek birleşik imaj**
   - Tüm flash imajını içeren tek bir `.bin` dosyası

2. **Çok dosyalı firmware paketi**
   - Aşağıdakiler gibi ayrı dosyalar:
     - `bootloader.bin`
     - `partitions.bin`
     - `boot_app0.bin`
     - `firmware.bin`

Bu sürümler; beklenen MeshGrid-Node donanım yapılandırmasında, **EBYTE E22-900T22D** LoRa modülü ve **uyumlu bir anten** ile kullanılan desteklenen ESP32 kartları için tasarlanmıştır.

Kesin ikili dosya düzeni, uyumluluk notları, ofsetler ve sürüme özel talimatlar için her zaman ilgili GitHub Release sayfasını kontrol edin.

---

## Firmware Kurulumu

Bu bölüm, yayımlanmış `.bin` firmware dosyasının desteklenen bir ESP32 karta nasıl yükleneceğini açıklar.

### Gereksinimler

#### Donanım

- 1 × **ESP32-S** veya **ESP32 DevKit V1**
- 1 × **EBYTE E22-900T22D** LoRa modülü
- 1 × seçilen çalışma bandı için **uyumlu anten**
- 1 × USB veri kablosu
- Windows, macOS veya Linux çalıştıran bir bilgisayar

#### Yazılım

- Python 3
- `esptool`

#### USB Sürücüleri

ESP32 kartınızda kullanılan USB-seri dönüştürücü çipe bağlı olarak aşağıdaki sürücülerden birine ihtiyaç duyabilirsiniz:

- **CP210x**
- **CH340**

Kart bağlandıktan sonra seri aygıt olarak görünmüyorsa, devam etmeden önce doğru sürücüyü yükleyin.

> Firmware’i yüklemek, yazılımı ESP32 üzerine kurar. Düğümün açıldıktan sonra LoRa işlevi sağlayabilmesi için **EBYTE E22-900T22D** ve **uyumlu bir antenin** de mevcut olması ve hedeflenen MeshGrid-Node donanım tasarımına uygun şekilde doğru bağlanmış olması gerekir.

---

## Önerilen Yükleme Yöntemi

MeshGrid firmware’ini kurmak için önerilen ve en güvenilir yöntem **esptool** kullanmaktır.

### 1. Python’u Kurun

Python 3’ün zaten kurulu olup olmadığını kontrol edin:

```bash
python --version
```

veya:

```bash
python3 --version
```

### 2. esptool Kurulumu

`esptool` aracını pip ile kurun:

```bash
pip install esptool
```

Sisteminiz `pip3` kullanıyorsa şunu çalıştırın:

```bash
pip3 install esptool
```

Kurulumu şu komutla doğrulayabilirsiniz:

```bash
esptool.py version
```

---

## Kartın Bağlanması

ESP32 kartını bilgisayarınıza bir **USB veri kablosu** ile bağlayın.

> Bazı USB kablolar yalnızca güç sağlar ve veri aktarımını desteklemez. Kart algılanmazsa sorun gidermeye geçmeden önce başka bir kablo deneyin.

> **Önemli:** USB kablosu ESP32’yi flashlamak için kullanılır. Tam MeshGrid-Node donanımının, hedef derlemenin gerektirdiği doğru bağlantılarla ESP32’ye bağlanmış **EBYTE E22-900T22D** modülünü ve **uyumlu bir anteni** içermesi beklenir.

---

## Seri Portun Bulunması

### Windows

**Aygıt Yöneticisi**ni açın ve şu bölümün altında kontrol edin:

- **Bağlantı Noktaları (COM ve LPT)**

Tipik örnekler:

- `COM3`
- `COM5`

### macOS

Şunu çalıştırın:

```bash
ls /dev/cu.*
```

Tipik örnekler:

- `/dev/cu.usbserial-0001`
- `/dev/cu.SLAB_USBtoUART`

### Linux

Şunu çalıştırın:

```bash
ls /dev/ttyUSB* /dev/ttyACM*
```

Tipik örnekler:

- `/dev/ttyUSB0`
- `/dev/ttyACM0`

---

## Firmware’in Yüklenmesi

İki yaygın firmware sürüm biçimi vardır.

### Seçenek A — Tek Birleşik Firmware `.bin`

Sürümde **tek bir birleşik firmware dosyası** varsa, bunu `0x0000` adresine yükleyin:

```bash
esptool.py --chip esp32 --port <PORT> --baud 460800 write_flash -z 0x0000 MeshGrid-Node-firmware.bin
```

#### Örnek (Windows)

```bash
esptool.py --chip esp32 --port COM5 --baud 460800 write_flash -z 0x0000 MeshGrid-Node-firmware.bin
```

#### Örnek (macOS / Linux)

```bash
esptool.py --chip esp32 --port /dev/cu.SLAB_USBtoUART --baud 460800 write_flash -z 0x0000 MeshGrid-Node-firmware.bin
```

---

### Seçenek B — Birden Fazla `.bin` Dosyası

Sürüm aşağıdaki gibi ayrı dosyalar içeriyorsa:

- `bootloader.bin`
- `partitions.bin`
- `boot_app0.bin`
- `firmware.bin`

bunlar doğru ofsetlerle flashlanmalıdır.

Tipik ESP32 flash düzeni:

```bash
esptool.py --chip esp32 --port <PORT> --baud 460800 write_flash -z \
0x1000 bootloader.bin \
0x8000 partitions.bin \
0xe000 boot_app0.bin \
0x10000 firmware.bin
```

> Örnektekinden farklıysa, sürüm notlarında verilen tam ofsetleri kullanın. Bu derleme için asıl referans sürüm sayfasıdır.

---

## Manuel Bootloader Modu

Bazı ESP32 kartları otomatik olarak flashlama moduna geçmez.

Flashlama başarısız olursa manuel bootloader modunu kullanın:

1. **BOOT** düğmesine basılı tutun
2. **EN** düğmesine basıp bırakın
3. **BOOT** düğmesini 2–3 saniye daha basılı tutun
4. Flashlama komutunu başlatın
5. Yükleme başladığında **BOOT** düğmesini bırakın

Bu yöntem, `esptool` kartla bağlantı kuramadığında sıklıkla gereklidir.

---

## İsteğe Bağlı: Kurulum Öncesi Flash Belleği Silme

Eski bir derlemeden yükseltme yapıyorsanız ve açılış sorunları, bozulmuş ayarlar veya beklenmeyen davranışlarla karşılaşıyorsanız, önce flash belleği silin:

```bash
esptool.py --chip esp32 --port <PORT> erase_flash
```

Ardından flashlama komutunu yeniden çalıştırın.

> Flash belleği silmek; kaydedilmiş yapılandırmaları, eşleştirme bilgilerini, kayıtlı ayarları ve önceki firmware verilerini kaldırır.

---

## Kurulumu Doğrulama

Flashlama başarıyla tamamlandıktan sonra:

1. Kartı yeniden başlatmak için **EN** düğmesine bir kez basın
2. Firmware’in beklediği baud rate ile bir seri monitör açın
3. Kartın normal şekilde açıldığını ve başlangıç loglarını verdiğini doğrulayın

### Doğrulama kontrol listesi

- Kart düzgün şekilde açılıyor
- Firmware boot loop’a girmeden başlıyor
- Anında çökme veya reset yaşanmıyor
- Derleme tarafından etkinleştirildiyse mesh veya Bluetooth servisleri kullanılabilir hale geliyor
- Kart yeniden başlatmadan sonra tutarlı davranıyor

> Başarılı bir flash işlemi yalnızca ESP32 firmware’inin doğru yazıldığını doğrular. MeshGrid-Node’un tam çalışması ayrıca **EBYTE E22-900T22D** ve **uyumlu bir antenin** doğru kurulmuş ve doğru bağlanmış olmasına da bağlıdır.

---

## Sorun Giderme

### Kart algılanmıyor

Olası nedenler:

- Arızalı veya yalnızca güç taşıyan USB kablosu
- Eksik CP210x veya CH340 sürücüsü
- Bozuk USB portu
- Kararsız USB bağlantısı
- Kartın doğru şekilde beslenmemesi

Önerilen işlemler:

- Farklı bir USB kablosu deneyin
- Başka bir USB portu deneyin
- Doğru USB sürücüsünü kurun
- Kartı yeniden bağlayın
- Seri arayüz hâlâ görünmüyorsa bilgisayarı yeniden başlatın

---

### macOS veya Linux’ta erişim izni hatası

Mevcut kullanıcının seri cihaza erişim izni olmayabilir.

Şunları deneyin:

- Komutu yükseltilmiş yetkilerle çalıştırmak
- Kullanıcınızı doğru seri erişim grubuna eklemek
- İzin değişikliklerinden sonra kartı yeniden bağlamak

---

### esptool kartla bağlantı kuramıyor

Şu tür hatalar görüyorsanız:

- `Failed to connect`
- `Timed out waiting for packet header`

kart büyük olasılıkla bootloader moduna girmemiştir.

[Manuel Bootloader Modu](#manuel-bootloader-modu) bölümündeki adımları kullanın ve tekrar deneyin.

---

### Flashlama başarılı ama kart açılmıyor

Olası nedenler:

- Yanlış firmware dosyası
- Yanlış flash ofsetleri
- Uyumsuz kart hedefi
- Eksik veya yanlış sürüm paketi
- Eski flash içeriğinin yeni derlemeyle çakışması

Önerilen çözüm:

1. Flash belleği silin
2. Doğru komutla yeniden flashlayın
3. Seçilen ikili dosyanın hedef karta uyduğunu doğrulayın
4. Doğru paket düzeni ve ofsetler için sürüm sayfasını yeniden kontrol edin

---

### Firmware açılıyor ama LoRa haberleşmesi çalışmıyor

Olası nedenler:

- **EBYTE E22-900T22D** modülü eksik
- Uyumlu anten eksik
- LoRa modülü doğru beslenmiyor
- ESP32 ile E22-900T22D arasındaki bağlantılar yanlış veya eksik
- Kart ve radyo modülü beklenen MeshGrid-Node donanım düzeniyle eşleşmiyor
- Derleme desteklenmeyen donanıma yüklenmiş
- Firmware farklı bir pin eşlemesi veya bağlantı düzeni bekliyor
- Kullanım bölgesi için yapılandırılmış radyo parametreleri yanlış veya mevzuata aykırı

Önerilen işlemler:

1. Düğümün **EBYTE E22-900T22D** içerdiğini doğrulayın
2. **Uyumlu bir antenin** takılı olduğunu doğrulayın
3. Beklenen MeshGrid-Node bağlantı düzenini yeniden kontrol edin
4. Seçilen firmware sürümünün tam kartınız ve donanım revizyonunuz için destek verdiğini doğrulayın
5. Radyo modülünün hedef derlemenin beklediği şekilde bağlandığını doğrulayın
6. Yapılandırılmış radyo parametrelerinin kullanım bölgesi için uygun olduğunu doğrulayın
7. Donanım uyumluluğu ve bilinen sınırlamalar için sürüm notlarını yeniden okuyun

---

### Flashlama sonrası seri çıktı yok

Olası nedenler:

- Yanlış seri monitör baud rate değeri
- Arızalı USB kablosu veya kararsız port
- Firmware doğru şekilde açılmadı
- Yanlış firmware imajı flashlandı

Önerilen işlemler:

- Kartı yeniden bağlayın
- Resetlemek için **EN** düğmesine basın
- Doğru seri portun seçili olduğunu doğrulayın
- Gerekirse çipi sildikten sonra yeniden flashlayın

---

## Önemli Notlar

- Mevcut MeshGrid-Node firmware derlemesi; desteklenen bir ESP32 kartı, **EBYTE E22-900T22D** LoRa modülü ve **uyumlu bir anten** gerektirir
- Yalnızca bu depoda veya resmi MeshGrid sürüm kanallarında yayımlanan resmi firmware ikili dosyalarını kullanın
- Resmi olmayan veya değiştirilmiş ikili dosyaların flashlanması kararsızlığa, uyumsuzluğa veya beklenen işlevlerin kaybına neden olabilir
- Kurulumdan önce firmware sürümünün donanım revizyonunuzla eşleştiğini her zaman doğrulayın
- Tüm sürümlerin aynı ikili düzeni veya aynı flash ofsetlerini kullandığını varsaymayın
- Sürüm notlarında açıkça belirtilmedikçe firmware’in farklı LoRa modüllerini desteklediğini varsaymayın
- Varsayılan radyo yapılandırmasının her ülke veya bölgede yasal olduğunu varsaymayın
- Modül **AT modu** veya başka bir yapılandırılabilir arayüz üzerinden yapılandırılıyorsa, yasal bölgesel RF ayarları **kullanıcının sorumluluğundadır**
- Yeni bir sürümü flashlamadan önce ilgili GitHub Release notlarını okuyun

---

## Sürüm Kullanım Bildirimi

Bu depo yalnızca resmi MeshGrid-Node firmware derlemelerini ve kurulum talimatlarını dağıtmak için tasarlanmıştır.

Son kullanıcılar, yayımlanan sürüm belgelerini dikkatlice izlemeli ve desteklenmeyen donanım yapılandırmalarına firmware yüklemekten kaçınmalıdır.

Aşağıdaki sürüme özel ayrıntılar için:

- ikili dosya türü
- flash ofsetleri
- desteklenen kartlar
- gerekli radyo donanımı
- gerekli anten hususları
- özellik kullanılabilirliği
- bilinen sınırlamalar
- uyumluluk notları

her zaman ilgili GitHub Release sayfasına başvurun.

---

## Beklenen Bağlantı Yapısı

Mevcut firmware derlemesi, aşağıdakileri kullanan belirli bir MeshGrid-Node donanım düzeni için tasarlanmıştır:

- desteklenen bir **ESP32** kartı
- bir **EBYTE E22-900T22D** LoRa modülü
- **uyumlu bir anten**

> **Önemli:** Yalnızca firmware’i flashlamak, radyo işlevinin çalışacağını garanti etmez. ESP32, EBYTE E22-900T22D ve gerekli anten arasındaki doğru bağlantı zorunludur.

Kullanıcılar şunlardan emin olmalıdır:

- seçilen ESP32 kartı hedeflenen derlemeyle eşleşmelidir
- **EBYTE E22-900T22D** düğüm üzerinde fiziksel olarak bulunmalıdır
- modül, ilgili donanım revizyonu için beklenen bağlantı ve pin eşlemesi ile bağlanmalıdır
- radyo modülü uygun ve kararlı bir güç kaynağıyla beslenmelidir
- RF çalıştırmadan önce uyumlu bir anten bağlanmış olmalıdır
- donanım tasarımının gerektirdiği yardımcı pinler, mod seçim pinleri, UART veya kontrol pinleri doğru şekilde bağlanmış olmalıdır

Donanım yerleşimi, pin eşlemesi veya radyo bağlantısı hedeflenen MeshGrid-Node tasarımından farklıysa firmware:

- başarıyla açılabilir ancak LoRa üzerinden haberleşemeyebilir
- öngörülemez davranabilir
- kararsız veya düşük RF performansı verebilir
- amaçlandığı şekilde çalışmayabilir

Gerçek donanım montajının, çalıştırmadan önce hedeflenen MeshGrid-Node bağlantı düzeniyle eşleştiğini doğrulamak kullanıcının sorumluluğundadır.

---

## AT Modu Yapılandırma Uyarısı

**EBYTE E22-900T22D**, yapılandırılabilir bir radyo modülüdür. Donanım kurulumuna ve kullanım gereksinimlerine bağlı olarak modül, **AT modu** veya başka bir yapılandırma arayüzü üzerinden parametre değişikliklerini destekleyebilir.

Bu yapılandırılabilir parametreler şunları içerebilir, ancak bunlarla sınırlı değildir:

- çalışma frekansı veya kanal ayarları
- hava veri hızı
- UART baud rate
- iletim gücü
- paketle ilgili radyo parametreleri
- bölgeye duyarlı diğer RF ayarları

> **Uyarı:** **EBYTE E22-900T22D** üzerinde **AT modu** veya başka herhangi bir yapılandırma yöntemiyle yapılan her türlü ayar, tamamen kullanıcının kendi sorumluluğundadır.

Kullanıcı aşağıdakilerden tek başına sorumludur:

- çalışılan ülke veya bölgede yasal olan ayarları seçmek
- yapılandırılmış frekans aralıklarının, kanal planlarının, air rate değerlerinin, güç seviyesinin ve ilgili RF parametrelerinin geçerli düzenlemelere uygun olmasını sağlamak
- seçilen antenin ve ortaya çıkan iletim karakteristiklerinin yerel yasal sınırlarla uyumlu olmasını sağlamak
- yasadışı veya mevzuata aykırı radyo kullanımından kaçınmak

Firmware dağıtıcısı, depo yöneticisi ve sürüm yayımlayıcısı; kullanıcı tarafından seçilen herhangi bir modül yapılandırmasının belirli bir yargı alanı için yasal, uyumlu veya uygun olduğunu **onaylamaz, garanti etmez veya beyan etmez**.

Yasal radyo ayarları konusunda herhangi bir belirsizlik varsa, yapılandırma kullanıcı tarafından ilgili bölgesel gerekliliklere göre doğrulanana kadar cihaz **iletim hizmetine alınmamalıdır**.

---

## Garanti Yok / Uyumluluk Feragati

Bu depo ve içindeki firmware sürümleri, açık veya zımni hiçbir garanti olmaksızın **“olduğu gibi”** sağlanmaktadır.

Geçerli hukukun izin verdiği azami ölçüde:

- belirli bir amaca uygunluk konusunda hiçbir garanti verilmez
- kesintisiz veya hatasız çalışma konusunda hiçbir garanti verilmez
- her donanım varyantı veya her bölgesel RF ortamıyla uyumluluk konusunda hiçbir garanti verilmez
- belirli bir radyo yapılandırmasının, anten seçiminin veya kullanım senaryosunun yasal uygunluğu konusunda hiçbir garanti verilmez

Firmware dağıtıcısı, depo yöneticisi ve sürüm yayımlayıcısı aşağıdakiler için sorumluluk kabul etmez:

- hatalı donanım montajı
- hatalı bağlantı
- uyumsuz antenler
- desteklenmeyen kart veya modül kombinasyonları
- yasaya aykırı RF yapılandırması
- mevzuat ihlalleri
- kötü kullanım, yanlış yapılandırma veya mevzuata aykırı çalıştırmadan kaynaklanan hasar, girişim, arıza veya kayıplar

Bu firmware’in, **EBYTE E22-900T22D** modülünün ve ilişkili tüm RF donanımının kullanımı tamamen kullanıcının kendi riskindedir.

Kullanıcılar tek başına şunlardan sorumludur:

- donanım uyumluluğunu doğrulamak
- doğru bağlantıyı ve güvenli montajı teyit etmek
- yasal bölgesel RF parametrelerini seçmek
- anten uygunluğunu sağlamak
- geçerli tüm yerel, ulusal ve bölgesel yasa, düzenleme, sertifikasyon ve işletim kısıtlamalarına uymak

## Lisans

Bu firmware yalnızca **kişisel ve ticari olmayan kullanım** için dağıtılmaktadır.  
[CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/) lisansı ile lisanslanmıştır.

### Kısıtlamalar

- ❌ Ticari yeniden dağıtım yasaktır
- ❌ Bu firmware yüklü donanımın satılması yasaktır
- ❌ Tersine mühendislik kesin olarak yasaktır

Bu firmware’i indirerek, flashlayarak, yapılandırarak veya kullanarak lisans şartlarına, dağıtım kısıtlamalarına ve geçerli tüm RF yasa ve düzenlemelerine uymayı kabul etmiş olursunuz.

---
