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

---

## Mikroservis Mimarisinin Dezavantajı

- Mikroservis mimarisi, geleneksel monolitik mimariye göre daha fazla karmaşıklık sergilemektedir. Servisler çoğaldıkça iletişim ve koordinasyon yönetimi zorlaşacaktır. Monolitik mimaride ise tüm uygulama tek bir yapıda bütünlük arz edeceğinden dolayı geliştirilmesi ve yönetilmesi daha basittir.
- Mikroservis'ler bir takım operasyonel zorluklar getirebilir. Halbuki monolitik mimari tüm kodun tek bir yerde bulunması sayesinde geliştirme ve bakım süreçlerinde oldukça kolaylık sağlar.
- Mikroservisler'de servisler arası iletişim süreçleri ister istemez performansı etkileyecektir, monolotikte ise bütünselliğin getirdiği atomik durumun iletişim hızı kat be kat daha fazla olacaktır.
- Mikroservislerde her bir servisin kendine ait veritabanı olması veri tutarlılığı açısından kritik arz etmektedir, veri senkronizasyonu konuusnda servisler arası tutarsızlık söz konusu olabilirken monolitik bir mimaride tek bir veritabanı üzerinden çalışıldığında veri tutarsızlığı yaşanmayacaktır.

---

## Hangi Senaryolarda Mikroservis Mimarisi Tercih Edilmelidir

- Mikroservis mimarisi, büyük ve karmaşık uygulama süreçlerinde ihtiyacını daha çok hissettirecektir. Misal olarak, e-ticaret sisteminde ödeme modülünün kesintisiz ve ölçeklenebilir olma ihtiyacı ancak mikroservis yaklaşımıyla çözüm bulacaktır. Başka bir örnekte, kullanıcı verilerini listelerken kullanıcıyı bekletmemek adına istek geldikten sonra ilgili servise yönlenip sürecin aksamaması sağlanacaktır.
- Teknoloji çeşitliliği ihtiyacı olan durumlarda, uygulamanın ihtiyacı olduğu operasyonlarda farklı teknolojiler yahut diller kullanarak çözüm getirilmesi gerekiyorsa eğer mikroservis mimarisi gerekir. Örnek olarak yüz taraması işlemini yapan teknoloji mevcut kullanılan teknolojiden ayrı olabilir.

---

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


---

## Bir Mikroservis Mimarisinin Tasarımı Nasıl Gerçekleştirilir?

- **Gereksinimleri Anlamak:** Her şeyden önce ilk adım olarak projenin gereksinimlerinin net bir şekilde anlaşılması gerekmektedir. Gereksinimler, projenin özelliklerinin fonksiyonlarının ve performans hedeflerinin belirlenmesini içermektedir. Ekip üyeleri, proje sahipleri ve kullanıcılarla etkileşimde bulunmak ve gereksinimlerin doğru bir şekilde anlaşıldığından emin olmak önemlidir.
- **Mirkoservislerin Belirlenmesi:** Gereksinimler belirlendikten sonra uygulamanın işlevlerinin birbirlerinden ayırıp özerk servislere bölmek gerekmektedir.
- **İletişim Protokollerinin Belirlenmesi:** Mikroservis mimarisinde servislerin sınırlarının belirlenmesinden sonra en önemli konulardan birisi de servisler arası iletişim ve veri paylaşım protokolleridir. Servisler arası veri alışverişi için, o anki senaryonun yapısın, yani iletişim modeline uygun olan, senkron ya da asenkron davranış sergilenebilecek protokoler belirlenmelidir.
- **Veritabanı Stratejisi:** İletişim protokollerinin yanında ayrıca her bir servisin veritabanı stratejisi dikkate alınmalı ve senaryosuna göre en doğru model tasarlanmalıdır.
- **Güvenlik Tasarımı:** Mikroservis mimarisinin en önemli kanatlarından birisi de güvenlik yapılanmasıdır. Sistemin geneli için authentication, authorization, veri güvenliği ve ağ güvenliği gibi konular göz önünde bulundurulmalı ve özellikle her bir servisin yetkisiz erişmden korunması için gerekli kritikler yapılarak teknoloji altyapı kurgulanmalıdır.
- **Hata Yönetimi ve İzleme:** Bir mikroservis mimarisi birbirinden bağımsız birçok bileşen ve servisin meydana gelmesiyle bütünlük arz ettiğinden bu bütünün her bir noktasından haberdar olmalı ve olası hatalara yahut kesintilere karşın dinamik bir izlenebilirlik sağlanmalıdır. Böylece sistemin parçalarından meydana gelen hataları son kullanıcılara karşı yönetilebilir kılmalı ve biryandan da onarılabilirlik açısından hız kazandıracak özellikler tasarlanmalıdır. Bunun için gerekli araçlar ve loglama stratejileri düşünülmeldiri.
- **Ölçeklenebilirlik ve Yedekleme:** Tasarlanan mimarinin nihahi olarak son kullanıcıya sunulması sürecinde olası talep artışları hesaba  katılmalı ve gerekli ölçeklendirme ve yedekleme senaryoları kurgulanmalıdır.
- **Test ve Sürüm Yönetimi:** Uygulamanın bütünü açısından kümülatif olmakla beraber her bir servisinde tek tek test edilebileceği senaryolar hazırlanmalıdır.
- **Dokümantasyon**: Son olarak tasarlanan mikroservis mimarisi ve bileşenleriyle ilgili, hem mevcut ekibin hem de gelecekteki ekip üyelerinin sitemi anlamalarına ve geliştirmelerine yardımcı olacak kapsamlı ve güncel dokümantasyon sağlanmaya özen gösterilmelidir.

---

## Mikroservis Mimarisinde Bir Servisin Sınırları Nasıl Belirlenir?

- **Tek Sorumluluk Prensibi:** Her servis yalnızca belirli bir işlevselliği veya sorumluluğu yerine getirmelidir. Böylece servislerin sınırlarını sorumlulukları üzerinden ayırt edebiliriz.
- **Dış Dünya Etkileşimi:** Servisin sınırlarını belirlerken, servisin dış dünya ile nasıl etkileşime geçeceğinin belirlenmesi işimizi kolaylaştıracaktır. Misal olarak; gelen veriyi asenkron işleyecekse bir servis o verinin üretiminden sorumlu olmayacak ve böylece sınırına dair ipucu vermiş olacaktır.
- **Ekip ve Organizasyon Yapısı:** Organizasyon yapısı servisin sınırlarına dair ipuçları verebilir. Bir ekibin niteliği ve almış oldukları sorumluluk geliştirecekleri servisin sınırlılıklarıyla paralellik arz edebilir.
- **Bağımlılıklar ve Sınırlar:** Yapılacak bir işin farklı bir işe olan bağımlılığı servisler arasındaki sınırları çizmenizde yardımcı olabilir.
- **Veri Yapısı ve Sınırı:** İşlenecek olan verinin yapısı ve sınırı, o veriyi işleyecek olan servisin sınırları ile doğru orantılı olabilir.

--- 

 ## Bir Servisin İş Gereksinimleri Analizi Nasıl Gerçekleştirilir?

- **Gereksinimleri Anlama:** İlk adım olarak, müşteri proje sahibi veya ilgili paydaşlarla etkileşime geçilmeli servis hakkındaki beklentileri,işlevselliği, performansı ve diğer gereksinimleri anlamak için açık sorular sorulmalı ve cevaplar dinlenmelidir.
- **Hedef Kitleyi Belirleme:** Servisin hangi kullanıcılar veya paydaşlar için tasarlandığı ve ne tür bir değer sağlaması gerektiği belirlenmedilir.
- **Kapsam Belirleme:** Servisin kapsamı belirlenmelidir. Yani servisin neleri yapacağı ve neleri yapmayacağı belirlenmelidir.
- **Temel İşlevselliği Tanımlama:** Servisin hangi işlemleri gerçekleştireceği veri ve bilgileri nasıl işleyeceği ve sonuçlarını nasıl sunacağı belirlenmelidir.
- **Performans Gereksinimleri:** Servisin performans hedefleri belirlenmeli, hız yanıt süresi işlem kapasitesi vs. gibi performans ölçüleri tanımlanmaldır.
- **Güvenlik Gereksinimleri:** Servisin kimlik doğrulama yetkilendirme veri güvenliği vs gibi güvenlik önlemleri gereksinimleri belirlenmeli.
- **Entegrasyon Gereksinimleri:** Servisin diğer servislerle nasıl entegre olacağı belirlenmelidir.
- **Veri ve Veritabanı Gereksinimleri:** Servis için kullanıalcak veri türleri ve bu veri türlerine uygun veritabanı sistemi seçilmeli ve veri erişimi,depolama vs gibi işlemlerin nasıl kurgulanacağı düşünülmelidir.
- **Hata Yönetimi ve İzleme:** Serviste olası hatalar meydana geldiği takdirde bunların nasıl yönetileceği ve tracebility için gerekli önlemler kurgulanmalı.
- **Belgelendirme:** Servisin tüm gelişim sürecinde ekip üyeleri ve paydaşlar arasında net bir anlayış oluşturmak ve servisin geliştirilme açısından sürdürülebilirliğini sağlamak için kapsamlı belgelendirme hazırlanmalıdır.

---

## Mikroservislerde Veritabanı Stratejileri Nelerdir?

- **Her Mikroservis Kendi Veritabanına Sahip Olabilir:** Her servisin kendine ait veritabanı olmasııdr. Bu strateji, mikroservislerin bağımsızlığını en üst düzeye çıkarmaktadır ve bir mikroservis veritabanındaki değişikliklerin diğerini etkilemesini önlemektedir. Bu stratejinin dezavantajları, kimi verilerin farklı veritabanlarında tekrarlanması yahut veri tutarsızlıklarının meydana gelmesi gibi sorunlar olabilmektedir.
- **Ortak Veritabanı Kullanımı:** Tüm mikroservisler, ortak bir veritabanını kullanmakta verileri paylaşabilirler. Bu strateji veri bütünlüğünü sağlamak ve veri tekrarı sorunlarını önlemek için avantajlı olabilir. Ancak bu stratejide servisler arasında sıkı bir bağlantıya neden olabilmekte ve değişiklik süreçlerinde diğer servislere direkt etkide bulunabilmektedir.
- **Veri Servisi:** Mikroservisler, verileri paylaşmak ve yönetmek için ayrı bir veri servisi kullanabilirler. Bu stratejide, verilerin merkezi olarak yönetilmesini sağlar ve mikroservislerin kendi veritabanlarını tutmalarını önleyerek veri tekrarı sorununu azaltabilirler. Ancak bu yaklaşımda da ekstra karmaşıklık ve performans sorunları söz konusu olabilir.
- **Event Sourcing:** EventSourcing verilerin meydana geldiği olaylar aracılığıyla kaydedildiği bir veri modelidir ve mikroservisler'de veri bütünlüğünün sağlanması ve veri tekrarı sorunlarının azaltılması için kullanılabilir.
- **CQRS:** CQRS veritabanı işlemlerini query ve command olarak ikiye ayıran bir tasarım desenidir. Mikroservisler, veri değişiklikleri için command tarafını kullanabilir ve verileri okuma ve sorgulamak için ise query tarafını kullanabilirler. Böylece veri yazma ve okuma işlemlerini ayırarak performans ve ölçeklenebilirlik avantajları sağlayabilirler.

---

## Mikroservisler Kendi Aralarında Nasıl Veri Paylaşabilirler?

- **API Request:** İki servis HTTP protokolü veya RPC gibi protokoller üzerinden API çağrıları yaparak veri paylaşabilirler. Bir servis, diğer bir servisin sunduğu API'ları çağırarak belirli verilere erişebilir.
- **Event Driven Architecture:** Olay tabanlı mimari davranışı eşliğinde birbirleriyle veri paylaşımı yapabilirler. Message Broker aracılığı ile.
- **Ortak Veri Depolama:** Bazı durumlarda farklı mikroservislerin aynı veriye ihtiyacı olabilir ve bu durumda veriyi ortak bir veritabanında depolamak mantıklı olabilir.
- **API Gateway:** Tüm servislerin API'lerine merkezi bir erişim noktası sağlayan bir bileşendir. Bu sayede, istemciler sadece API Gateway'e istek gönderir ve gateway'de ilgili servislere bu istekleri yönlendirir.


---

## Mikroservisler Kendi Aralarında Senkronizasyonu Nasıl Sağlarlar?

- Saga Pattern, Outbox Pattern, Inbox Pattern, 2PC Two-Phase commit


--- 

## Servisler Arası Senkron ve Asenkron İletişim Modelleri Nelerdir?

- Mikroservis mimarisinde, servisler arası iletişim modelleri teknik olarak senkron ve asenkron olarak değerlendirilmektedir. Bu modeller, servisler arasındaki iletişim senaryosu ve iş kuralı gereği mantıken tercih edilerek, uygulanmaktadırlar.

- **Senkron İletişim:** Bir servisin, diğer bir servisle doğrudan etkileşimde bulunduğu bir iletişim modelidir. Request atan servis attığı servisten response bekler. Request'i gönderen servis, response gelinceye kadar bekler ve geldiği taktirde de işlemini tamamlar. Bu modelde senkron denmesinin sebebi de budur. Bir servis, diğer bir servisten yanıt almadığı sürece bir sonraki adıma geçmeyecek bekleyecektir. Yani bir senkronizasyon süreci söz konusudur. Avantajları: Senkron iletişim modeli kolay anlaşılabilir, haliyle iki servis arasındaki iletişim sürecini rahatlıkla takip edebilir ve bütünsel davranış olarak genel topolojiyi rahatlıkla çıkarabilirsiniz. Hataların izlenmesi ve hata ayıklama süreci de oldukça kolaydır. Dezavantajları: Senkron iletişimin en büyük dezavantajı servislerin birbirlerine olan bağımlılığını arttırmasıdır. Bu bağımlılıktan dolayı uygulamanın ölçeklenebilirliği olumsuz etkilenebilir. Ayrıca gelen istekleri karşılayan servisin cevap verme süresine bağlı olarak tüm işlemlerin yavaşlamasına yol açabilir.

- **Asenkron İletişim:** Bir servis diğerine beklemeksizin mesaj gönderebilir ve işlemi tamamlamasını beklemeden diğer işlemlerine geçebilir. Böylece işlemler paralel olarak gerçekleştirilebilir ve bir servis, diğer servis(ler)in cevaplarını almaksızın çalışmasına devam edebilir. Avantajları: Servisler arası bağımlılığı azaltır ve böylece ölçeklenebilirliği arttırır. Servislerin, diğer servislerden yanıt beklemeksizin diğer işlemlerine devam etmesi söz konusu olacağı için her bir servis açısından işlem süreleri daha hızlı olacaktır. Message broker'a atılan mesajların işlenmesi sürecinde olası hatalara karşı ilgili mesajların daha sonra da işlenebilmesi özelliğinden dolayı daha yüksek hata toleransı sunmaktadır. Dezavantajları: Asenkron iletişim modelinde message broker yönetimi vs. gerekeceğinden dolayı karmaşıklığı artabilir. Mesajların sırası ve zamanlamasıyla ilgili sorunlar ortaya çıkabilir ve uygulama tasarımında bu durumları göz önünde bulundurup gerekli önlemlerin alınması gerekmektedir.

- **Özetle;** Uygulamanın ihtiyaçlarına ve gereksinimlerine bağlı olarak doğru iletişim modeli seçilmelidir.
 
- **Senkron İletişim Örneği:** Bir eticaret uygulamasında sepet ve stok işlemlerinin ayrı mikroservislerde işlendiğini varsayalım. Eğer ki müşteri sepette bir ürünü eklendiğinde sepet servisi doğrudan stok servisi çağırarak ilgili ürüne dair stoğun güncellenmesini talep ediyor ve bu talep neticesini bekliyorsa, ve stok servisi de bu talebe karşılık işlemi tamamlayarak cevap döndürüyorsa bu senkron bir iletişim senaryosudur.
- **Asenkron İletişim Örneği:** Bir bülten uygulamasında, e-posrta süreçlernin farklı bir servisle yürütüldüğünü düşünelim. Eğer ki gönderilecek e-posta isteği bir kuyruğa eklenip ardından da e-posta işleminden sorumlu olan servisin bu kuyrutkan mesajları alarak gönderme işlemi gerçekleştiriliyorsa bu bir asenkron iletişim senaryosudur. Böylece onlarca kullancıya bülten uygulamasını yormaksızın ve son kullanıcıları bekletmeksizin rahatça(asenkron bir şekilde) e-posta gönderilebilir.
- **Karma İletişim Örneği:** Bir e-ticaret uygulamasında sepet, sipariş, stok ve teslimat işlemlernin ayrı servislerde yürütüldüğünü düşünelim. Müşteri bir sipariş verdiğinde, sepet servisi siparişi doğrulayarak sto kservisini direkt senkron bir şekilde çağırabilir ve stok güncellemesi sağlandıktan sonra sipariş tamamlanabilir. Ancak siparişin teslimatı ise asenkron bir şekilde yürütülebilir ve bu siparişe dair bilgi bir message broker'a atılarak teslimattan sorumlu servis tarafından asenkron bir şekilde asenrkon işlenerek sonucun kargo şirketinin yanıtına bağlı olarak işlenmesi sağlanabilir.
- **Senkron İletişim Örneği:** Banka sistemlerinde müşterilerin kendi aralarındaki transfer süreci senkron iletiim modeline örnek bir senaryodur. X müşteri tarafından Y müşterisine dair yapılan para transfer talebi, transfer işleminden sorumlu mikroservise gönderilir ve gerekli hesap ve bakiye kontrolleri neticesinde transfer ya gerçekleştirilir ya da tutarsızlıktan dolayı iptal edilir. Her iki durumda da işlem sonucu response olarak kullanıcıya döndürülür.
- **Asenkron İletişim Örneği:** İstatistik yahut analiz gibi uygulamardaki rapor oluşturma süreçleri de asenkron iletişim modeline örnek senaryo teşkil edebilmektedir. Kullanıcı rapor talebinde bulunduğu zaman bu istek bir mesaj kuyruğuna gönderilir ve rapor işlemlerinden sorumlu serviste bu kuyruktan mesajı alarak raporu işleme başlar ve tamamlandığı zaman mail ya da arayüz üzerinden kullanıcıya bilgi verebilir.
- **Asenkron İletişim Örneği:** Uygulamalardaki notification sistemlerinde asenkron iletişim modeline bir örnektir. Kuıllanıcı arayüz üzerinden bildirim talebinde bulunarak bu bildirimi mesaj kuyruğuna ekler. Notification'dan sorumlu bir servis ise kuyruktan mesajı alır ve mesajda bildirilen alıcılara gerekli bildirim çalışmasını gerçekleştirir.
- **Asenkron İletişim Örneği:** Dosya işlemleri de genellikle asenkron iletişim modelinde yürütülürler. Özellikle büyük dosyalar yüklenmek istendiğinde mesaj kuyruğuna gerekli bilgilere sahip olan mesaj atılır, bu işten sorumlu olan serviste mesajı işleyerek dosyanın işlenmesini asenkron bir şekilde gerçekleştirir.

---

## Senkron ve Asenkron İletişim Süreçlerinde Hangi Yöntemler Kullanılır?

- Http Restful API, SOAP, Http gRPC gibi yöntemler ile senkron iletişim sağlanır.
- Message broker ile de asenkron iletişim sağlanır.




 
