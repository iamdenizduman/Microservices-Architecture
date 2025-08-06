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

---

## Mİkroservis'lerin Mantığı Nedir

- Büyük ve karmaşık uygulamaların daha küçük ve yönetilebilir parçalara a yrılmasını sağlayarak, yazılım geliştirme süreçlerini ve uygulamanın ölçeklendirilmesini kolaylaştırmaktır. 

---

## Mikroservis Mimarsinin Avantajı

- Mikroservis mimarisi, bir uygulamayı bağımsız, küçük ve özelleştirilmiş servislere böldüğü için modüler bir yapı sunmaktadır. Bu modülerlik, farklı servislerin ayrı ekipler tarafından geliştirilebilmesini ve sadece ihtiyacı olan hizmetlerin güncellenebilmesini sağlar. Lakin bu durum monolitik mimaride öyle değildir! Tek bir ekip tarafından komple bir uygulama geliştirilir ve ekipteki bir kişiden uygulamanın tüm noktasına hakimiyet beklenir.
- Mikroservisler yapacakları işe göre farklı teknolojiler ve diller kullanarak geliştirilebilirler. Dolayısıyla geliştiricilere uzmanlık alanına göre odaklanma şansı verir. Monolitik mimaride ise bu uygulama tek bir teknolojiye odaklıdır.
- Mikroservis yaklaşımıyla geliştirilen uygulama gevşek bağlılıkla bütünlük arz eder. Böylece uygulama sürecinde olası bir etki sistemin genelini etkilemeyecektir. Lakin monolitik uygulamada ise sistemin modülleri birbirlerine sıkı bağımlı olacağı için sistemin herhangi bir noktasındaki hata yahut kesinti komple uygulamayı etkileyecek ve bir kriz ortamı oluşturacaktır.
- Her bir mikroservis diğerinden bağımsız olarak ölçeklenebilirken, monolitik yaklaşımda ise uygulama bir bütün olarak ölçeklendirilmek zorundadır. Bu durumda kaynakların kullanımı açısından büyük bir fark yaratmaktadır.
- Mikroservis yaklaşımında, servislerin kendi süreçlerine ve altyapısına göre hususi dağıtım planlaması gerçekleştirilebilir. Monolitik yaklaşımda ise sistem her açıdan bütünlük arz edeceği için en ufak bir değişiklikte bile topyekün deploy durumu söz konusudur.

## Mikroservis Mimarisinin Dezavantajı

- Mikroservis mimarisi, geleneksel monolitik mimariye göre daha fazla karmaşıklık sergilemektedir. Servisler çoğaldıkça iletişim ve koordinasyon yönetimi zorlaşacaktır. Monolitik mimaride ise tüm uygulama tek bir yapıda bütünlük arz edeceğinden dolayı geliştirilmesi ve yönetilmesi daha basittir.
- Mikroservis'ler bir takım operasyonel zorluklar getirebilir. Halbuki monolitik mimari tüm kodun tek bir yerde bulunması sayesinde geliştirme ve bakım süreçlerinde oldukça kolaylık sağlar.
- Mikroservisler'de servisler arası iletişim süreçleri ister istemez performansı etkileyecektir, monolotikte ise bütünselliğin getirdiği atomik durumun iletişim hızı kat be kat daha fazla olacaktır.
- Mikroservislerde her bir servisin kendine ait veritabanı olması veri tutarlılığı açısından kritik arz etmektedir, veri senkronizasyonu konuusnda servisler arası tutarsızlık söz konusu olabilirken monolitik bir mimaride tek bir veritabanı üzerinden çalışıldığında veri tutarsızlığı yaşanmayacaktır.

---

## Hangi Senaryolarda Mikroservis Mimarisi Tercih Edilmelidir

- Mikroservis mimarisi, büyük ve karmaşık uygulama süreçlerinde ihtiyacını daha çok hissettirecektir. Misal olarak, e-ticaret sisteminde ödeme modülünün kesintisiz ve ölçeklenebilir olma ihtiyacı ancak mikroservis yaklaşımıyla çözüm bulacaktır. Başka bir örnekte, kullanıcı verilerini listelerken kullanıcıyı bekletmemek adına istek geldikten sonra ilgili servise yönlenip sürecin aksamaması sağlanacaktır.
- Teknoloji çeşitliliği ihtiyacı olan durumlarda, uygulamanın ihtiyacı olduğu operasyonlarda farklı teknolojiler yahut diller kullanarak çözüm getirilmesi gerekiyorsa eğer mikroservis mimarisi gerekir. Örnek olarak yüz taraması işlemini yapan teknoloji mevcut kullanılan teknolojiden ayrı olabilir.

## Hangi Senaryolarda Mikroservis Mimarisi Tercih Edilmemelidir

- Küçük ve basit uygulamalarda, gereksiz karmaşık ve maliyetli olabilir. Bu tür uygulamalarda, monolitik mimari daha basit ve uygun olacaktır.
- Teknoloji ve altyapı yetersizliği durumlarda, yeterli kaynak altyapı yoksa mikroservis mimarisi uygun olmayabilir.
- Hızlı geliştirme ve prototipleme durumlarında, hızlı bir şekilde geliştirme gerekiyorsa mikroservis mimarisi yavaşlatacaktır.
- Farklı sorumluluk bağlamları barındırmayan uygulamalarda, mikroservis mimarisi uygun değildir. 
- Özetle, monolitik mimaride yeterliliğini gözlemledikten sonra ihtiyaç durumunda mikroservis mimariye geçilmelidir.

---

## Mikroservis Mimarisi Temel Kavramlar

- **Servis:** Mikroservis mimarisinin temel yapıtaşıdır. Bağımsız işlevselliğe sahip, küçük ve sadece belirli bir işe odaklanmış uygulamanın bileşenleridir. Her servis, kendine özgü bir veritabanına, iş mantığına ve endpoint'e sahiptir.
- **İşlev:** Her servisin, spesifik bir şekilde meydana getirdiği operasyonun işin ta kendisidir. Misal olarak; kullanıcı yönteminden sorumlu olan bir servisin kullanıcı yetkilendirmeyle ilgili desteği o servisin bir işlevidir.
- **Bağımsızlık:** Her bir servis, birbirinden bağımsız ve kendi içersinide tutarlılık sağlayan bileşenlerdir. Bir servisin çökmesi veya güncellenmesi, diğer servisleri etkilemez.Bu şekilde uygulama parçalara bölündüğü için geliştirme, dağıtım ve bakım süreçleri oldukça kolay ve yönetilebilir olacaktır.
- **API:** Servis'lerin birbirleriyle yahut dış dünyayla iletişim kurmasını sağlayan arayüzlerdir. Genellikle senkron iletişim modellemesinde tercih edilir.
- **Message Broker:** Servis'lerin birbirleriyle iletişim kurmasını sağlayan kuyruk sistemidir. Genellikle asenkron iletişim modellemesinde tercih edilir.
- **Ölçeklenebilirlik:** Her bir servis, ihtiyaç ve işlem hacmine göre diğerlerinden ayrı olarak ölçeklendirilebilir.
- **Yeniden Kullanılabilirlik:** Mikroservis'ler işlevsel açıdan bağımsız ve tek bir işe odaklanmış yapılar olduğu için ihtiyaç doğrultusunda diğer projelerde yahut farklı platformlar da yeniden kullanılabilirler.

--- 

## Mikroservis Organizasyon Modelleri

- **Teknoloji Odaklı Model:** Bu modelde, her bir servis farklı bir teknoloji yığını ve dilinde geliştirilir. Örneğin bir servis Java ile yazılırken diğeri Go ile yazılabilir. Bu yaklaşım, geliştiricilere teknoloji seçiminde özgürlük sağlar ve her servisin belirli bir teknoloji yığınından en iyi şekilde yararlanmasına izin verir. Ancak, farklı teknolojilerin desteklenmesi ekiplerin çoklu teknolojilere hakim olması gereği gibi zorlukları da beraberinde getirmektedir.
- **İş Odaklı Model:** Bu modelde, her servis belirli bir iş fonksiyonunu temsil eder. Örneğin, kullancıı yönetimi ödeme işlemleri gibi farklı işlevler için ayrı servsler oluşturulabilir. Bu yaklaşımda ekiplerin sorumlulukarının net bir şekilde tanımlanmasını sağlar. Ancak iş odaklı modelde, farklı işlevlere sahip olan servisler arasındaki veri paylaşımının tasarlanması ve yönetimi biraz zahmetli olabilir.
- **Veri Odaklı Model:** Bu modelde ise her bir servsini belirli bir veri yahut veri grubu üzerinde yoğunlaştığı görülmektedir. Örneğin, müşteri verilerini yöneten bir servis, ürün kataloğunu yöneten bir servis gibi. Bu yaklaşım, servisler arasındaki veri bütünlüğünü ve veri işleme performasını artırabilir. Ancak veri odaklı modelde, her veri tüm servislerin ilgileneceği yapı olabileceğinden dolayı bu yaklaşımda servisler arası sınırlar net çizilemeyebilir ve doğal olarak servisler arasındaki bağımlılığı arttırabilir ve veri güncellemelerinin koordinasyonu zorlaştırabilir.
- **Karışık Model:** Bu modelde, farklı yaklaşımların bir kombinsyonu kullanılmaktadır. Örneğin, iş odaklı bir ana yapı üzerine, farklı teknolojilere sahip alt servisler eklenerek projelerin tasarımı genişletilebilir. Böylece organizasyon ve uygulama ihtiyaçlarıyla birlikte uygulanabilirliğe bağlı olarak çeşitli mikroservis yaklaşımlarını bir araya getiren esnek bir yaklaşım sağlanmış olur.

---

## Mikroservis'lerde Servisler Arası İletişim (Veri İletişim) Nasıl Gerçekleştirilir?

- **HTTP Tabanlı API'lar:** En yaygın olarak kullanılan yöntemlerden birisidir. HTTP protokolü üzerinden REST API'ları kullanarak yapılan iletişim modelidir. Bu yöntem, servislerin HTTP talepleri aracılığıyla birbirleriyle etkileşime girmesine olanak tanımaktadır. Client, bir servise HTTP talebi gönderir ve servis de HTTP yanıtı ile cevap verir. Bu cevap client tarafından bekleneceği için genellikle senkron iletişim modellerinde tercih edilen yöntemdir. Veri paylaşımını JSON veya XML gibi veri formatlarıyla sağlar.
- **Message Broker:** Servisler message broker yapıları aracılığıyla asenkron bir şekilde birbirleriyle iletişim kurabilirler. Bu modelde, herhangi bir servis message broker'a bir mesaj bırakır ve hedef servis bu mesajı uygun zamanda alır ve işler. Kuyrukyaki mesajların sorumlu servis tarafından opsiyonel bir şekilde işlenmesi  ve bu işlemin client tarafından beklenmemesi asenkron iletişim modellerinde tercih edilmesine sebep olur. Böylece servisler arasında gevşek bağlılık sağlanmış olur ve arttırılmış bağımsızlık ile birlikte yüksek ölçeklenebilirlik sağlanabilir.
- **RPC(Remote Procedure Call):** Bu yöntemde, bir servis farklo bir sunucudaki serviste bulunan herhangi bir metodu veya metotları sanki kendi ortamının birer parçasıymış gibi çağırabilmektedir. Haliyle servisler arası doğrudan bir bağlantı gerektiren bu yöntem, bağımlılığı arttırabilir. Bu nedenle dikkatli kullanılmaldır.

---

## Mikroservis'lerde Servisler Arası Veri İletişiminde API Gateway'in Görevi Nedir?

- Servisler ile client arasında köprü görevi görür.
- Mikroservis mimarisinde geliştirme süresini kısaltacak ve uygulamaları merkezi bir nokta üzerinden haberleştirecek şekilde odaklayacak mühim bir rol üstlenen bileşendir.
- Client'in yapacağı istek süreçlerinde hedef servisin yahut servislerin konumunu/ipsini/adresini bilmesine gerek kalmaksızın, tek bir merkezi nokta üzerinden isteklerin doğru servise yönlendirilmesini sağlamaktadır.
- Ayrıca sistemde kurguların merkezi API yapılanması sayesinde gereklik kimlik doğrulama ve yetkilendirme sorumululuklarını da üstlenerek servislerin de güvenliği sağlayabilmektedir. Bu sayede gelen tüm trafiği merkezi bir şekilde denetleyerek uygulama bütünü için gerekli güvenlik protokollerinin yönetilmesini merkezileştirebilir.
- Tüm bunların dışında, ölçeklendirilmiş servisler üzerinde gelen trafiği dengeli bir şekilde yönlendirerek yük dağılımını (load balancing) optimize edebilir.
- API sürüm yönetimi ve caching gibi süreçlerde de desteklerde bulunabilir.
- Özetle, mikroservis mimarisinde elzem olmasa da oldukça önemli bir bileşendir. Uygulama karmaşıklığı arttıkça ve servis client sayıları arttıkça önemi hissedilmektedir.




