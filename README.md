# .NET Microservices Architecture – Eğitim Notları

Bu repoda, .NET ile mikroservis tabanlı sistemlerin nasıl inşa edildiğini adım adım öğrenirken çıkardığım notlar, mimari diyagramlar ve örnek projeler yer almaktadır.  
Hedefim hem kendi öğrenim sürecimi belgelemek hem de benzer yolda ilerleyen yazılımcılara rehber olmaktır.

---

## Mikroservis Mimarisi Nedir

- Mikroservis mimarisi, bir uygulamanın bağımsız ve ölçeklendirilebilir tek bir işe odaklanmış, küçük/atomik olan hizmetlere/servislere ayrılarak geliştirilmesini ve dağıtılmasını sağlayan bir yazılım geliştirme yaklaşımıdır.  
- Bu yaklaşımda her bir servis, uygulamanın bir işlevinin sorumluluğunu üstlenmekte ve ihtiyaç doğrultusunda birbirleriyle iletişim kurarak daha büyük bir sistem oluşturmaktadır.

---

## Dağıtılabilirlik Nedir

- Bir yazılımın dağıtık olması ya da dağıtık olarak tasarlanması; farklı bileşenlerinin veya modüllerinin farklı bilgisayarlarda yahut sunucularda dağıtılması, deploy edilmesidir.  
- Haliyle mikroservis mimarisi çalışmalarında uygulamanın tüm servisleri tek bir sunucuda ayağa kaldırmaktansa, her bir servisin farklı bir sunucuda ayağa kaldırılabilir olması dağıtık olması demektir.  
- Dağıtılabilir sistemlerde, uygulamanın her bir servisi farklı bir konumda host ediliyor olsa da işlevsel açıdan bu servislerin bir bütün olarak çalışması esastır. *(Stok yönetimi ve sipariş servisleri gibi)*  
- DevOps süreci içinde, dağıtık bir mimaride her bir servis farklı ekipler tarafından yürütüleceği için daha kolay olacaktır.

---

## Ölçeklenebilirlik Nedir

- Bir yazılım sisteminde artan işlem yoğunluğunu, uygulama performansını koruyarak karşılayabilmesi için uygulanan davranışlardır.  
- Bu davranışlar sayesinde sistemden beklenen taleplere karşın uyum sağlanabilmekte ve uygulamanın sürdürülebilirliği korunaklı hale getirilmektedir.  
- **Örnek:**  
  Markette bir kasa saatte 10 müşteri alıyor ise, 40 müşterinin aynı anda kasaya yöneldiğinde diğer kasaların açılıp gelen müşterileri karşılama işlemi ölçeklendirilebilirliğe örnek verilebilir. Bu, darboğaza sebep olmasını engeller.

---

## Ölçekleme Nasıl Gerçekleştirilir

- **Yatay Ölçeklenebilirlik (Scale-out / Horizontal Scalability):**  
  İş yükünün birden fazla aynı özelliklerdeki sunucuya dağıtıldığı yöntemdir. Her bir sunucuda uygulamanın ya da ilgili servisin bir replikasyonu bulunarak hizmet sağlanmaktadır.

- **Dikey Ölçeklenebilirlik (Scale-up / Vertical Scalability):**  
  Sistemdeki donanımsal kaynakların (RAM, CPU gibi) artırımı yöntemidir.  
  Yani mevcut sistem kaynaklarının kapasitesi artırılmaktadır.  
  Bu yöntemde sistemin daha fazla yoğunluğa karşılık verebilmesi için tek bir sunucunun özellikleri genişletilir ya da iyileştirilir.

- **Bölümlendirme (Partitioning):**  
  Bellek yahut veri depolama alanı gibi yerleşim birimlerini bölerek ölçeklendirilebilirliğin sağlanmasıdır.  
  Böylece büyük verilerin yahut depolama gerektiren işlemlerin farklı sunucular arasında paylaştırılarak yük dağıtımı sağlanır.

- **Önbellekleme (Caching):**  
  Sık kullanılan verilerin Redis gibi caching yapılarında depolanarak sistemin yükünün hafifletilmesiyle ölçeklendirme sağlanır.


**Özetle:**
Ölçeklendirme, bir yazılımın darboğaza düşmeksizin ve performansından ödün vermeksizin hizmetine devam edebilmesidir.  
Genelde ölçeklendirme yöntemi olarak **scale-out** kullanılır. Uygulama farklı sunucularda ayağa kaldırılır ve yoğunluğa göre **load balancer** sistemleriyle uygun olan sunucuya trafik yönlendirilerek ölçeklendirme gerçekleştirilir.Mikroservis yaklaşımı uygulanan projelerde sistemin tümünden ziyade, yoğun bir şekilde işlevsellik gösteren servisler ölçeklendirilerek **az maliyetle oldukça etkili** bir ölçeklendirme yaklaşımı sergilenmiş olur. Bu da mikroservis yaklaşımının büyük bir avantajı olarak öne çıkar.

---

## Mikroservislerin Temel Prensipleri Nelerdir?

- **Bağımsızlık:**  
  Her bir mikroservis birbirinden bağımsız olarak geliştirilir ve dağıtılır.  
  Ve her bir servis kendi işlevselliğini ve veritabanını yönetir.  
  Böylece bağımsızlığın verdiği konforla her bir servis ayrı ekipler yahut organizasyonlar tarafından geliştirilebilir ve hızlı bir şekilde yeniden dağıtılabilir.

- **Teknoloji çeşitliliği:**  
  Mikroservis mimarisinde her servis birbirinden farklı olacağından dolayı farklı teknolojiler ve diller kullanarak geliştirilebilir bir yapı sunmaktadırlar.  
  Bu sayede her bir servisin ihtiyaçlarını en uygun teknoloji ile karşılamak mümkün olur ve geliştiricilerin uzmanlık alanlarına odaklanmaları sağlanabilir.

- **İşlevsel bölünme:**  
  Uygulama işlevsel açıdan farklı servislere ayrılacağından dolayı, her bir servis işine odaklı yönetilebilir bir ortam sağlanmaktadır.

- **Hafif İletişim:**  
  Mikroservisler birbirleriyle genellikle asenkron iletişim modeline sahip esnek kanallar üzerinden iletişim kurmaktadırlar.  
  Bu message broker olabileceği gibi, yer yer birbirlerine HTTP tabanlı API isteklerinde de bulunabilirler.  
  Tüm bunların sayesinde servisler arasında **loose coupling (gevşek bağlılık)** söz konusu olacağından dolayı, servisler arası geliştirme süreçlerinde bir direnç durumu söz konusu olmayacaktır.

- **Ölçeklendirme:**  
  Her bir servis ihtiyacına ve yoğunluğuna göre diğerlerinden bağımsız olarak ölçeklendirilebilir.

- **Yüksek Kusur Toleransı:**  
  Bir servisin hata yapması yahut çökmesi durumunda diğer servislerin etkilenmemesi ve sistem bütünlüğünün korunması oldukça önemlidir.  
  Mikroservis yaklaşımında, servislerin birbirlerinden izole olması, olası hata durumlarının komple sisteme yansıtılmasını engellemekte ve böylece yüksek kusur toleransı sunmaktadır.

- **Hızlı Dağıtım:**  
  Mikroservis'lerin bağımsızlığı, hızlı ve sürekli dağıtımı kolaylaştırmaktadır.  
  Her sistemin kendine ait süreçleri ve altyapısı olduğundan, bu servislerin güncellenmesi, yeniden dağıtılması veya ölçeklendirilmesi daha hızlı ve sıkıntısız olur.

- **İzlenebilirlik:**  
  Sistemin bütünsel açıdan sağlığını kontrol edebilmek için her servisin izlenebilir olması gerekmektedir.  
  Haliyle gerekli log ve traceability yaklaşımlarıyla servislerin sağlığı ve performans durumları sürekli olarak izlenebilir.



