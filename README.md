# Web Güvenliği
### Web uygulamalarının güvenliğini artırmak için çeşitli araçlar ve yöntemler içerir. XSS, CSRF, SQL enjeksiyonu gibi yaygın web saldırılarını engellemek ve tespit etmek için kullanılabilir.

## Bu Makale Kimler İçin ?

- Hackerler: Bir web sitesini hacklemeyi öğrenmek isteyenler için.
- Testerlar: Yazılım firmalarında sorunları tespit etme görevinde olanlar için.
- Developerlar: Web sitelerini kurarken güvenlik önlemlerini öğrenmek isteyenler için.


## Gereksinimler
- Sunucu tarafı geliştirme için en az bir programlama diline hakim olmalısın (PHP, ASP.NET, Python, Ruby vb.).
- İstemci tarafı geliştirme için HTML, CSS ve JavaScript gibi temel web teknolojilerine hakim olmalısın.

<br></br>
<div align='center'>
  <h1>Temel Kavramlar</h1>
</div>

# İstemci - Sunucu İletişimi (Client - Server Communication)
Bir istemci uygulaması (Firefox, Google Chrome vb.) kullanılarak herhangi bir web sitesi, belirli bir sunucuya `HTTP Request` gönderilerek açılır. Örneğin, `www.google.com` adresini açmak için, istemci Google adlı sunucuya bir istek gönderir ve bu sunucu isteği işler, ardından `HTML` formatında sonuç gönderir.

Tarayıcılar (browsers), HTML kodunu anlar ve bizim görebileceğimiz bir şekle dönüştürür. Bu tür iletişimlere `HTTP stateless` denir. Çünkü sunucunun istemci hakkında hiçbir bilgisi yoktur, sadece gelen taleplere yanıt verir. `İstemcinin kim olduğunu tespit edemez`.

<div align="center">
    <h3></h3>
    <img src="https://github.com/yasir723/web-guvenligi/assets/111686779/a10ea8cb-2d2e-46ab-a020-a0753f8c4104">
</div>

Kullanıcı, Firefox tarayıcısını kullanarak `http://host/index.php` adresindeki içeriği görmek ister. Bu nedenle istemci tarafından sunucuya bir `istek (request)` gönderilir. Bu istek, index.php sayfasının yerini belirten bir taleptir. Sunucu, bu talebi alır ve eğer aranan sayfa bir PHP dosyasıysa, işleme yapmak üzere `Apache` sunucusuna iletir. Sunucu, kendi harddiskinde (HDD) dosyayı arar ve bulunursa `PHP işlemi (Processing PHP)` gerçekleştirir. Eğer dosyada veritabanı bağlantısı varsa, sunucu veritabanına bağlanır ve gerekli verileri elde eder. Sonrasında, PHP işlemi sonucunda oluşturulan `HTML dosyası` kullanıcıya geri gönderilir. Genelde saldırıların çoğu Client tarafına yapılır.


# POST ile GET Arasındaki Fark

GET ve POST, web sayfalarında form verilerini sunucuya iletmek için kullanılan iki farklı yöntemdir. GET ile gönderilen veriler URL'nin bir parçası olarak görünürken, `POST ile gönderilen veriler gizli kalır`. GET ile gönderilen verilerin güvenliği daha düşüktür çünkü URL'ye eklendikleri için kullanıcı adı, şifre gibi hassas bilgileri içermemelidirler. POST, verileri HTTP isteğinin gövdesinde gönderdiği için daha güvenlidir ve hassas bilgilerin gönderilmesi için tercih edilir. `GET ile gönderilen veri boyutu sınırlıdır`, POST ise daha büyük veri kümelerini göndermek için daha uygundur. `GET, veri almak için` kullanılırken, `POST, veri göndermek için` kullanılır.

<div align="center">
        <img src="https://github.com/yasir723/web-guvenligi/assets/111686779/b64b13b1-d230-41e8-94f8-639287c0283e">
</div>


- ### Get İle Gönderilen Bilgiler 
    Örneğin:
    ```plaintext
     http://example.com/login?username=ali&password=123
    ```
    Bu URL'de, http://example.com/login adresine username=ali ve password=123 parametreleri eklenmiştir. Bu parametreler GET yöntemiyle sunucuya iletilir. Ancak, bu şekilde hassas bilgilerin (örneğin, şifre) açıkça URL'de görünmesi güvenlik riski oluşturabilir.

    __Test aşamasında, URL'yi kontrol etmek yerine, daha kapsamlı bilgileri sağlayan `Sağ Tıklayıp` -> `İncele` -> `Ağ` -> `Başlık` kısmındaki bilgilere odaklanacağız__

    Belirli bir siteye ilk girdiğimizde eğer ağ bilgilerine bakarsak `get` olarak gönderildiğini göreceğiz
    <div align="center" >
        <img src="https://github.com/yasir723/web-guvenligi/assets/111686779/78e1311a-329f-4490-be9f-aa25fe2975f1">
    </div>
    
- ### Post İle Gönderilen Bilgiler
    Post ile gönderilen bilgiler URL'de yer almazlar ki post işlemi daha güvenli bir veri gönderme yaklaşımıdır.
  
    Örneğin, kullanıcı adı olarak 'giriş' ve şifre olarak '123' girerek bir veri gönderdiğimizde, bu bilgilerin ağ kısmında `POST` olarak görünecektir
    <div align="center">
        <img src="https://github.com/yasir723/web-guvenligi/assets/111686779/4ecd3512-ad49-4ecd-b80f-b9aaa0e3e00e">
    </div>
    
   

# Zıt Hedefler (Opposing Goals)
Zaman zaman, faydalı düşüncelerimiz olabilir ancak aynı zamanda zararlı olma potansiyeline sahip olabilirler. Mike Andrews'ın `How To Break Web Software` adlı kitabında bu durum çok açık bir şekilde ele alınmıştır.

## Ortaya Konulan Temel Noktalar
- Güvenilirlik (Reliability): Sorunları çözmek veya hataları düzeltmek için yazılan kod satırları zaman zaman fazla karmaşık hale gelebilir. Bu durumda, bir testçinin sistemin tüm kodları test etmeye zamanı olmayabilir. Bu durum, hacker'lar için bir fırsat olabilir. Sistemlere veri göndererek, `sistemlerden hata mesajları alarak, hacker'lar sistemdeki hataları keşfedebilirler`. Bu durum, hacker'ların sistem hakkında daha fazla bilgi edinmelerine ve potansiyel olarak ciddi zararlara yol açmalarına neden olabilir.
- Performans: Genellikle bir web uygulaması veya başka herhangi bir uygulama geliştirdiğimizde onun çok hızlı olmasına çablıyoruz, Bir web sitesini hızlandıran en temel unsur, kodların sunucu tarafında çalışacak şekilde yazılması değil, istemci tarafında yazılmasıdır. `Bu kullanıcıya ne kadar bir avantaj sağlarsa, bir hacker'a o kadar avantaj sağlayabilir`. Çünkü JavaScript gibi istemci tarafı kodları, hacker'lar tarafından görülebilir. Kodlarımızı istemci tarafında yazdığımızda, hacker'ların sistemimiz hakkında bilgi edinmelerine yardımcı oluyoruz. Bu yüzden istemci tarafında yazacağımız her kodu bir hacker tarafından nasıl değerlendirebileceğini iyi hesaplamamız lazım ve ona göre önlemler almamız gerek.
- Kullanılabilirlik (Usability): Bir web uygulaması tasarlarken, kullanımı mümkün olduğunca kolay hale getirmeye çalışırız. Ancak, basit bir sistem kurmak, genellikle daha fazla detay gerektirir. `Daha fazla detay, hacker'ların daha fazla fırsata sahip olmasını sağlayabilir`. Bu nedenle, hangi bilgilerin kullanıcılara, hangi bilgilerin ise hacker'lara görüneceğini iyi hesaplamamız gerekmektedir.


# Cookies ile Sessions Arasındaki Fark
Cookies ve sessions, web uygulamalarında kullanılan iki farklı yöntemdir.
- __Cookies:__ tarayıcıda saklanan küçük metin dosyalarıdır.
  -  tarayıcıda saklanır.
  -  belirli bir süre boyunca saklanabilir.
  -  her istekte tarayıcıya gönderilen bilgilerle çalışır.
  -  Güvenlik açısından, cookies genellikle daha düşük seviyededir çünkü kullanıcı cihazında saklanır.
  -  Cookies, sessions'dan genellikle daha küçük veri miktarlarını saklayabilir.
- __Sessions:__ sunucuda tutulan geçici depolama alanlarıdır.
  - sunucuda saklanır.
  - oturum sona erene kadar veya belirli bir süre boyunca etkinlik olmadığında sona erer.
  - sunucu tarafında saklanan oturum bilgilerini kullanır.
  - sunucuda saklandığından daha güvenlidir.
  - Veri saklama kapasitesi açısından sessions genellikle daha büyük veri miktarlarını saklayabilir.
 
<div align="center">
    <img src="https://github.com/yasir723/web-guvenligi/assets/111686779/b6cb25cd-4442-414e-9a80-0bbe7c9a4ddd">
</div>

Bir kişi `örneğin: Yasir` tarayıcı açtığında belirli bir sunucuya (servere) `örneğin: http://host.com` talep `örneğin: request-1` gönderir. Eğer sunucuda sessions mekanizması çalışıyorsa. Sunucu bu isteği aldığında adı sess__RANDOM_ID ile başlayan bir oturum dosyası (Session File) oluşturur `örneğin: dosyanın adı: sess__708813902f8ca46`. Sonrasında sunucu isteği işler ve aynı id dosyanın ismi olarak sonucuya geri gönderir (response back) `örneğin: dosyanın adı: PHPSESSID = 708813902f8ca46`. Yani önceden oluşturulan id `708813902f8ca46` kullanır.

Gönderildikten sonra, tarayıcı bu oturum id'sini alır ve aynı isimle yeni bir çerez dosyası (Cookie File) oluşturur `örneğin: host.com` ve alınan oturum id'si (session id) `örneğin: 708813902f8ca46` bu dosyaya kaydeder.

Aynı kullanıcı, kullanıcı adı bilgisini 'yasir723' olarak güncellemek isterse, `örneğin: set username: yasir723` şeklinde bir talep `örneğin: request-2` gönderir. Bu talep, yeni bir istek olarak sunucuya iletilecektir. PHP, bu durumda otomatik olarak sunucuya bağlı tüm çerezleri (cookies) okur. Yani, tarayıcıda saklanan tüm çerezleri tarar ve `örneğin host.com` sunucusuna bağlı `cookie bilgisi gönderir`. Server belirli bir oturum id'yi (session id) `örneğin: PHPSESSID = 708813902f8ca46` arayacak. Sunucu, kendisinde kaydedilen session id'lerde bu id'yi arayıp bulduğunda gönderilen `örneğin: set username: yasir723` güncelleme talebini okur ve gönderilen yeni bilgiyi kaydeder ardından `işlem tamamlandı` mesajını döndürür.

Eğer daha sonra kullanıcı adı bilgisini öğrenmek için `örneğin: get username` şeklinde yeni bir talep `örneğin: request-3` gönderilirse, aynı süreç tekrarlanır. Tarayıcı, ilgili sunucuya bağlı çerez dosyasını bulur ve içeriğini sunucuya gönderir. PHP server, gönderilen id isimli bir dosya arayacak. bulduğunda username bilgisini okur ve onu tarayıcıya gönderir.

Bu arada, sunucuda birden fazla oturum, istemcide ise birden fazla çerez olabilir. `örneğin: key2 = value2` gibi.
    


## ~İçerikler~ Saldırılar
- #### [İstemci - Sunucu İletişimi](https://github.com/yasir723/istemci-sunucu-iletisimi)
- #### [POST ile GET Arasındaki Fark](https://github.com/yasir723/post-get-fark)
- #### [Zıt Hedefler](https://github.com/yasir723/zit-hedefler)
