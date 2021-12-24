## 1. GİRİŞ 

<p> Bilişim teknolojileri sistemlerinden çeşitli sebeplerle log toplama ve arama ihtiyacımız doğar. Örneğin bir alışveriş sitesinde kullanıcı davranışlarını loglamak (müşteriler hangi tarayıcılarla, hangi ülkelerden geliyorlar, hangi ürünleri geziyorlar vb.) bu amaçlardan birisi olabileceği gibi, Windows ve Linux hibrit bir ortamdaki gibi sistemlerden de güvenlik amaçlı log toplamak isteyebiliriz.</p>
<p>Performans sorunları, kaynak tüketim oranları, sistemlerin erişebilirlik durumlarını izleme, ağda yaşanan problem ve tehditlerin çözümü sistemde yapılan değişikliklerin takibi gibi dahası iş yüklerini otomatize ederek sorun ile karşılaşmadan önce bu sistem ile durumu çözerek sorun oluşmadan önce giderilmesi çok önemlidir.</p>
<p>Bu proje kapsamında kişisel bilgisayarımıza açık kaynaklı ELK Stack ve yardımcı araçlarını kurup bağlantılarını sağlayarak esnek log toplama işini otomatikleştireceğiz. Bunun üzerinde incelemeler yapılarak güvenliğini sağlama yolunda adımlar atılacaktır.</p>

## 2. LOG KAVRAMI
### 2.1 Log Nedir? 

<p> Log, bilişim sistemlerinde gerçekleşen her bir olayın kaydı anlamına gelir. Sistemlerinizde bilgi ve isteğiniz dışında çalışan bir program veya daha da kötüsü davetsiz bir misafir olabileceğinden, logların kayıt altında tutuluyor olması sitemleriniz açısından kritik önem arz etmektedir. Eğer log kaydı tutulmazsa sistemlerinize zarar verebilecek bu tür durumlardan yalnızca zarar gördükten sonra haberiniz olur. Bu da kurumunuza maddi ve manevi zararlar verebilir.</p>
<p> Örnek vermek gerekirse; bir internet sayfasına giriş yapan kullanıcının internet sayfasının sunucusunda; kullanıcının IP adresi, konum bilgisi ve yaptığı işlemler gibi bilgilerin logları tutulmalıdır. Çünkü bu kullanıcı sayfa üzerinde normal bir kullanıcının yaptıkları dışında sisteme zarar verecek işlemler yapabilir. Örneğin bir form doldurma yerinde istenilen dışında meta-karakterler girerek sistemi manipüle etmeye çalışabilir ve eğer başarırsa kritik verileri ele geçirebilir. Bu gibi durumlarda internet sayfanızda gerçekleşecek olayların loglarını kayıt altında tutmanız, saldırgan form ekranına istenilen dışında bir giriş yaptığın da, veya kötü niyetli başka bir eylem gerçekleştirdiğin bunun farkına önceden vararak saldırganın erişimini kısıtlayabilmenizi sağlayacaktır.</p>

### 2.2 Log Toplama  

<p> Log toplama, çeşitli yerlerden gelen kayıtların bir arada tutulmasındır. Ama log toplama, toplanan logların analizinin yapılması ve doğru cihazdan doğru formatta log gelmesi gibi birçok zorluğu vardır. Bundan dolayı gelen logların analizi ve logu doğru formata dönüştüren birçok yazılım bulunmaktadır. Çünkü asıl mesele gelen kayıtların analizinin doğru yapılmasıdır. Biz bu çalışmamızda ELK Stack open source yazılımlarını kullanacağız.</p>
<p>Loglar kayıt altına alınırken yaşanılan en büyük zorluk gereksiz, fazla log alınmasıdır. Önemli olan fazla log toplamaktansa ihtiyaçlara, olaylara göre belirlenerek çözüm için fayda verecek logların toplanması gerekir. İşte burada log yönetimi bu işleri yapmamıza yardım edecek ve olaylar arasında log bağlantısı kurarak log analizini rahat yapma imkânı sunar.</p>

### 2.3 Log Yönetimi ve Analizi  

<p> Log izleme, tüm kritik ağlar ve cihazları kapsayan bilişim sistemlerinin ürettiği olay kayıtlarının(loglarının) belirlenen kurallara göre analiz edilmesi olarak tanımlanmaktadır.  Logların kapsamlı bir şekilde toplanması, birleştirilmesi, orijinal haliyle saklanması, metin olarak analizi ve sunumu gibi adımlardan oluşan log yönetimi ise saldırının göstergelerini ve delillerini elde etmeye olanak sağlamaktadır.</p> 
<p> Logların kayıt altına alınıyor olması etkili bir çözüm olabilir fakat tek başına yeterli değildir. Loglar, güvenlik denetimi sağlamak amacıyla merkezi olarak kaydedilmeli ve arşivlenmelidir. Sistemde hangi logların tutulacağı ihtiyaca göre belirlenmeli ve log yönetimi ve analizi yapılmalıdır. Kuracağımız sistemde kayıt altına alacağımız olaylardan bazıları aşağıda verilmektedir.</p>

•	Uygulamalara ait Loglar</br>
•	Web aktiviteleri</br>
•	Jboss kullanıcı komut geçmişi</br>
•	Sistem logları</br>
•	Ağ üzerindeki hareketler</br>
•	Kullanıcı hareketlerin takibi</br>
•	Dizin / Dosya erişim takibi</br>
•	Tüm giriş denemeleri</br>
•	Port tarama logları</br>
•	Hatalar</br>

## 3. SIEM (Security Information and Event Management)

<p> SIEM belirlenen politika ve kuralların yardımıyla bağımsız gibi görünen olaylar arasında anlamlı bağlantılar kurarak muhtemel saldırıları tespit etmeye yardımcı olan korelasyon tekniğidir. SIEM ürünleri çevre birimlerden uç kullanıcılara kadar sistemlerin ürettiği logları merkezi olarak toplayan, saklayan ve analiz eden sistemlerdir. SIEM ’in çeşitli sistemlerden loglanan farklı formatlardaki olay kayıtlarını ortak bir veri modeline dönüştürmesi işlemine normalleştirme denir.  Korelasyon aşaması önceden belirlenmiş kuralların yardımıyla farklı farklı sistemlerden veya uygulamalardan gelen olayları bağlantılandırarak güvenlik tehditlerinin tespitine ve harekete geçilmesine yardımcı olur. Birleştirme ise olayların birden fazla sayıda kaydı tutulmuşsa bunları bir kayıta indirerek analiz edilecek verinin hacmini düşürmekte ve işlemleri hızlandırmaya yardımcı olmaktadır.</p> 

### 3.1 SIEM’ in Uygulama Adımları

- Toplanan logların global bir formata dönüştürülmesi ve olayların saldırı tipine göre sınıflandırılması yöntemlerini kullanarak normalleştirme ve kategorilendirme adımlarını uygulamak</br>
- Bağımsız gibi görünen olayları birbiriyle bağlantılandırmak ya da olayları datayla ilişkilendirmek</br>
- Yöneticilere mail, SMS veya SNMP mesajları ile bildirim veya alarm sağlamak</br>
- Toplanan veri ve korelasyon sonuçlarını gerçek zamana yakın bir ölçüde güvenlik uzmanlarına sunan izleme paneli sağlamak</br>
- SIEM ürünü tarafından toplanan verinin analiz aşamalarını kapsayan rapor üretmek</br>

### 3.2 SIEM’ in Önemi

<p> SIEM ürünleri gerçek zamanlı raporlama ve güvenlik olayları analizi sağlayarak ağlara yönelik en son tehditleri tespit edebilme imkânını sunarlar. Ağ güvenliğine yönelik tehditler hızla yayılmakta ve her geçen gün yenileri ortaya çıkmaktadır.  Uzaktan erişim noktalarının ve ağlara bağlanan cihazların sayısındaki artış ağlara sızma noktalarının da çoğalmasına neden olmaktadır. Bilişimciler ağın karşı karşıya kaldığı tehditleri algılayabilmek için birden fazla kaynaktan toplanan veriyi analiz etmek ve bunların sonucunda atılacak adımları kararlaştırmak durumundadırlar.</p>  
<p> Saldırıların tespit edilmesi, dijital delillerin kaybedilmesine olanak vermeyecek şekilde daha fazla zararın oluşmasının önlenmesi, bütünsel bir güvenlik analizi raporlaması ve güvenlik tehditlerinin gerçek zamanlı olarak izlemeye alınması gibi önemli hizmetleri sunan SIEM ürünleri ağ geçidi, sunucular, güvenlik duvarları ve diğer kritik BT bileşenlerinin nasıl bir saldırı ile karşı karşıya kaldığı konusunda ayrıntılı rapor üretirler.</p> 

## 4. ELK STACK NEDİR?

<p> Elk Stack, Modern uygulamaları ve BT altyapısını izlemek, mühendislerin yüksek düzeyde dağıtılmış, dinamik ve gürültülü ortamları izleme zorluğunun üstesinden gelmelerini sağlayan bir log yönetimi ve analiz çözümü gerektirir. Kullanıcılara birden fazla veri kaynağından veri toplayan ve işleyen, bu verileri veri büyüdükçe ölçeklenebilen tek bir merkezi veri deposunda depolayan ve verileri analiz etmek için bir dizi araç sağlayan güçlü bir platform sağlayarak yardımcı olur. </p> 
<p> Elk Stack, açık kaynak kodlu log toplama, arama ve analiz bileşenlerinin tamamına verilen genel addır. Kabaca aşağıdaki bileşenlerden oluşur;</p> 
•	Elasticsearch</br>
•	Kibana</br>
•	Logstash</br>
•	Beats ailesi</br>
o	Winlogbeat</br>
o	Filebeat</br>
o	Packetbeat</br>

### 4.1 Elasticsearch 

<p> Elasticsearch esasında bir arama ve veri indeksleme motorudur. Geri planında, Apache Lucene projesini kullanır. Bunu bir veri tabanı olarak düşünebilirsiniz. Verileri Logstash ya da doğrudan Beats Agent ’ları üzerinden alarak onları aranabilir halde indeksler. Ve REST ve JSON teknolojilerini kullanarak HTTP protokolü üzerinden veri üzerinde arama, ekleme, silme vb. operasyonları yapmanızı sağlar. Dinamik ve gürültülü ortamları izleme zorluğunun üstesinden gelmelerini sağlayan bir log yönetimi ve analiz çözümü gerektirir.</p>  
<p> Kullanıcılara birden fazla veri kaynağından veri toplayan ve işleyen, bu verileri veri büyüdükçe ölçeklenebilen tek bir merkezi veri deposunda depolayan ve verileri analiz etmek için bir dizi araç sağlayan güçlü bir platform sağlayarak yardımcı olur. En büyük avantajlarından birisi esnekliği ve bu sebeple sağlanan kolay ölçeklenebilme avantajıdır.</p> 

### 4.1.1 Temel Elasticsearch Kavramları

1. Index: Elasticsearch üzerinde veri indekslenerek JSON formatında tutulur. Yani dökümanlarınızın içindeki her bir kelime(terim) için hangi döküman ya da dökümanlarda o kelimenin olduğu bilgisini tutan indexler ayrıca JSON belgeler topluluğudur. Indexi Database kavramı gibi düşünebiliriz.</br> 
2. Type: Veri tabanı yönetim sistemlerinde kullandığımız tablolar gibi düşünebiliriz. Index içerisindeki verileri mantıksal olarak bölümlememizi sağlar. Örneğin e-ticaret sistemleri için ürünler, kategoriler, log bilgileri gibi verilerin her birini bir type olarak düşünebiliriz. Bir index içerisinde birden fazla type barındırabiliriz.</br> 
3. Mapping: Amacı, ilgili dökümanın arama motoruna nasıl aktarılacağının tanımlanmasıdır. Kısaca veri tabanında isimlendirdiğimiz bir schema diyebiliriz.</br> 
4. Document: Elasticsearch’ deki her bir kayda, yani row’ a document denir.</br> 
5. Field: Her bir döküman içindeki alana, field denir. Yani DB’deki bildiğiniz column.</br> 
6. Cluster: Bir veya daha fazla düğümün bir koleksiyonudur. Cluster, tüm veriler için toplu dizin oluşturma ve arama yetenekleri sağlar.</br> 
7. Node: Elasticsearch’ in tek bir çalışan örneğini ifade eder. Tek fiziksel ve sanal Sunucu; RAM, depolama ve işleme gücü gibi fiziksel kaynaklarının yeteneklerine bağlı olarak birden çok düğüm barındırır.</br> 
8. Shard: Indexler yatay olarak parçalara ayrılır. Bu, her bir parçanın, belgenin tüm özelliklerini içerdiği, ancak dizinden daha az sayıda JSON nesnesi içerdiği anlamına gelir.</br>
9. Replica: Tamamen güvenlik amaçlı çalışan, her verinin bir kopyasının bulunduğu başka makinalardır. Böylece bir makina çöktüğünde replica veya replicalarından biri devreye girebilecektir. Örneğin aşağıda 3 Node 3 Shard’ lı bir yapı söz konusudur. Ve her bir Shard’ın 1 yedeği, yani 1 Replicası bulunmaktadır. Toplamda 6 sunucu bulunmaktadır. Her bir Shard’ın Replicasının başka bir Node ’da bulunması gerekir.</br>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147297633-cc874529-fedf-4bcb-9d02-5439a7ea87b3.png"></br> Şekil 1 - Shard ve Replica Kavramı
</p>

### 4.2 Logstash 

<p> Açık kaynak kodlu bir veri toplama motoru diyebiliriz. Gönderilen logları toplar ve conf dosyasında belirtilen filtreleme işlemlerine göre çıktı üreten Elasticsearch’e bu logları indekslenmek üzere gönderen sistemdir. Bu işlemlere örnek olarak gelen log üzerinde ismini değiştirmek istediğiniz sütunlar olabilir, host adına göre yeni eklemek istediğiniz sütunlar olabilir veya Timestamp formatını değiştirmek için filtreler yazılabilir. Son olarak formatı ayarlar ve Elasticsearch’e iletir.</p>

### 4.2.1 Logstash Nasıl Çalışır?

<p> Logstash Pipeline ’ı temel olarak 3 aşamadan oluşur. Input, Filter ve Output,</p>
Input: Eventlerin gelmesi, oluşturulmasıdır.</br>
Filter: Gelen eventlerin üzerinde aksiyonlar, transformasyonlar gerçekleştirir.</br>
Output: Eventleri başka bir yere aktarır.</br>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147297699-5c218813-92ea-4365-a860-4da146dbc003.png"></br> Şekil 2 - Logstash Pipeline
</p>

### 4.3 Kibana 

<p> Toplanıp anlamlı hale gelen verinin, analizini yaptıktan sonra ki görselleştirme işlemini yapar. ELK, DevOps yaşam döngüsünde Monitoring kısmına hizmet ederler. İstenen verilerin toplanıp, anlamlı hale getirilmesi ve sonrasında bunların merkezi bir birimde toplanıp analiz süreçlerine sokularak görseller ile bize sunar.</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147297761-41a74312-6872-4fa4-8ce5-e828ec291a03.png"></br> Şekil 3 - Kibana Arayüzü
</p>

### 4.4 Beats

<p> Beats ürün ailesinin üyelerini sistemlerden Logstash ‘a gönderen ajanlar olarak düşünebiliriz. Biz çoğunluk olarak Filebeat kullanacağız.</p> 
Winlogbeat: Windows olay günlükleri için hafif göndericimizdir. Bir Windows hizmeti olarak yüklenir ve çalışır ve olay günlüğü verilerini Elasticsearch veya Logstash 'a gönderir.</br>
Packetbeat: Kurduğunuz sisteme dair network verisi getiren bileşendir. Bu sayede örneğin kritik bir sunucunuzdaki network anormalliklerini izleyebilirsiniz.</br>
Filebeat: Bir dosyayı izleyerek bu dosya içeriğindeki değişiklikleri gönderen Beats ailesi ürünüdür. Örneğin /var/log/httpd.log dosyanızı bu ajana göstererek bu dosyayı loglayabilirsiniz.</br>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147297858-b44cd996-c839-4a29-aec6-aa622b78e01c.png"></br> Şekil 4 - ELK Stack Çalışma Mantığı
</p>

## 5. ELK STACK KURULUMU

<p> Beats ve Logstash veri toplama ve işleme ile ilgilenir, Elasticsearch verileri endeksler ve saklar ve Kibana verileri sorgulamak ve görselleştirmek için bir kullanıcı arayüzü sağlar. Şimdi bu sistemi ayağa kaldırmak için gerekli paket ve bağlılıkları kurduktan sonra kurulumlara başlıyoruz.</p>
1)	Java ve tüm bağlılıkların yüklenmesi: Elasticsearch, makinemizde bulunan OpenJDK gerektirir. Java’yı aşağıdaki komutu kullanarak HTTPS desteği ve APT için wget paketleriyle birlikte yüklüyoruz. </br>

`apt install -y openjdk-11-jdk wget apt-transport-https curl`
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147298249-7cea5389-81f6-4b2e-9183-b7f6b2b2e98b.png"></br> Şekil 5 - Java ve bağımlılıklarının kurulumu
</p>

2)	GPG Anahtarı Aktarımı: Elasticsearch genel anahtarını APT ’ye aktarıyoruz. ’OK’ çıktısını aldığımızda işlem tamamlanmıştır.</br>
`wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add –`
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147298301-3a576fa6-bd0a-4e0a-8f62-51a046a70244.png"></br> Şekil 6 - GPG anahtarın aktarımı
</p>

3)	sources.list.d dizinine Elastic deposunu ekliyoruz.</br>
`echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list`
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147298374-b45abe1d-ced9-45a9-904e-113f47686bf9.png"></br> Şekil 7 - Elastic deposunu ekleme
</p>

### 5.1 Elasticsearch Kurulumu

<p> Elasticsearch ve Kibanayı kurduktan sonra ilerleyen aşamalarda nginx’in arkasına yerleştirerek parolalı erişim ile güvenliğini artıracağız. Elasticsearch, 9200 numaralı bağlantı noktasındaki trafiği dinliyor. Dış tarafların REST API aracılığıyla verilere erişememesi veya elastik kümeyi kapatmaması için Elasticsearch örneğimize dışarıdan erişimi kısıtlayacağız.</p>

1)	Sistem havuzunu güncelliyoruz.</br>
`sudo apt update` 
2)	Elasticsearch ’ü kurun:</br>
`sudo apt install elasticsearch`
3)	Elasticsearch.yml yapılandırma dosyasını düzenliyoruz.</br>
`sudo nano /etc/elasticsearch/elasticsearch.yml`
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147298449-b1e8cb58-d03f-4d7e-9ef3-596243283048.png"></br> Şekil 8 - elasticsearch.yml yapılandırması
</p>

4)	Elasticsearch hizmetlerini başlatın ve etkinleştirin.</br>
`systemctl start elasticsearch` </br>
`systemctl enable elasticsearch`

5)	Elasticsearch durum kontrolü;</br>
Elasticsearch, 9200 numaralı bağlantı noktasını dinliyor. Bunu localhost:9200 adresinden kontrolünü sağlıyoruz.
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147298555-ed33db97-3ba8-46cd-b38a-f1ad9fc53451.png"></br> Şekil 9 - Elasticsearch durum kontrolü
</p>

### 5.2 Logstash Kurulumu

<p> Logstash JSON formatında yazılmış config dosyaları ile yönetilir. Filebeat kullanarak farklı dosyalardan günlükleri toplayarak ve bu günlükleri merkezleştirmek için Logstash’e göndereceğiz.</p>
1)	Logstash’i kurun;</br>
`sudo apt install logstash`
<p> Pipeline Config dosyaları ise /etc/logstash/conf.d/ dizinindedir. Burada input, filter ve output olmak üzere 3 farklı formatta logstash yapılandırma dosyası oluşturulur. Pipeline ile ilgili esnek davranabilirsiniz. Örneğin tüm inputları tek bir input dosya da toplayabilir ya da ayrı ayrı inputlar oluşturabilirsiniz. Ya da Filter dosyasını gelen log tiplerine göre ayırabilir, ya da iç içe yazabilir tek bir output oluşturabilirsiniz. Yani esneksiniz. Logstash’i indirdik fakat input, filter ve output yapılandırmalarını ilerleyen bölümlerde yapılandıracağız.</p>

### 5.3 Kibana Kurulumu

<p> Kibana 5601 portundan iletişim kurmaktadır ve localhost:5601 adresinden tarayıcı üzerinden arayüze erişebiliriz.</p>

1)	Kibanayı kurun;</br>
`sudo apt install kibana`

2)	Kibana yapılandırma dosyasında port ve host ayarlarını düzenliyoruz;</br>
`sudo nano /etc/kibana/kibana.yml`
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147298966-3e423402-2515-4b6e-aa84-6bd7d65acf4b.png"></br> Şekil 10 - kibana.yml yapılandırması
</p>

3)	Kibana hizmetini başlatın ve etkinleştirin;</br>
`systemctl enable kibana`</br>
`systemctl start kibana`

4)	Kibana arayüzüne erişim;</br>
`localhost:5601`
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299035-2c430f12-fc14-4a32-bbb5-b8191c84c561.png"></br> Şekil 11 - Kibana arayüzüne erişim
</p>

### 5.4 Filebeat Kurulumu

<p> Data & Log transferi için farklı farklı Beat ajanları mevcut. Örneğin, Filebeat log dosyaları için, Packetbeat network paketleri için, Winlogbeat ise Windows logları için kullanılıyor. Şimdilik test amaçlı syslog’ları görmek için Filebeat’i kurup logları  Logstash’e göndermeden direk Elasticsearch’e göndererek Kibana dan bu logları göreceğiz.</p>

1)	Filebeat’i kurun;</br>
`sudo apt install filebeat`
2)	filebeat.yml yapılandırma dosyasını düzenliyoruz.</br>
•	Yapılandırdığımız alanı etkinleştirmek için;</br>
`enable: true` </br>
•	`/var/log/*.log` Taranması ve getirilmesi gereken yoldaki tüm dosyalar olarak belirledik.
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299140-d8628f1e-8e11-493b-8b63-e4664a5d9c2b.png"></br> Şekil 12 - filebeat.yml yapılandırması-1
</p>
•	Outputs kısmında logstash çıktısı disable olarak gelmektedir. Şimdilik aktif etmiyoruz ve elasticsearch kısmı Şekil.13 ‘deki gibi ayarlıyoruz.</br>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299188-86c5a0e8-5d51-4047-a762-99b2a594e33e.png"></br> Şekil 13 - filebeat.yml yapılandırması-2
</p>
3)	Filebeat hizmetini başlatın ve etkinleştirin;</br>
`systemctl enable filebeat`</br>
`systemctl start filebeat`</br>

### 5.5 Kibana Günlük Panosu Oluşturma

<p> Tarayıcımızı açıp `https://localhost:5601` ‘e gidiyoruz. Stack Management > Index Pattern yoluna ulaştığımızda aşağıdaki gibi ekranı göreceğiz.</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299252-557c1d21-f5c9-496a-9a62-379378afc493.png"></br> Şekil 14 - Index Pattern
</p>
<p> Indeks modelimiz olarak filebeat-* tanımladık.  Şimdi oluşturmadan önce bazı ayarları belirleyebiliriz. Zaman filtresi alan adı alanında @timestamp'ı seçip modeli oluşturuyoruz.</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299272-6a9ee79f-4e88-416d-8ba7-97b6b6658b5c.png"></br> Şekil 15 - Index Pattern timestamp
</p>
<p> Discover ekranına geldiğimizde oluşturduğumuz index şablonunu seçerek logların kibana arayüzüne aktarıldığını görüyoruz. Bu kurulumda örnek olarak /var/log dizinin altındaki tüm Syslog’ların logların merkezi bir alanda toplanılmasını sağladık. İlerleyen konularda bu örnekler filebeat’ den direk olarak değil de Logstash üzerinden gönderimlerini sağlayacağız.</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299292-d39f7ca2-7816-486d-bf7d-89d7064c9289.png"></br> Şekil 16 - Discover arayüzü log takibi
</p>

## 6. NGİNX İLE KORUMA ALTINA ALMA

<p> Elasticsearch http API arayüzü üzerinden çalıştığı için bir sunucu üzerinden çalıştırdığınızda dış dünya tarafından ulaşılabilir halde çalışıyor olacaktır. Burada Elasticsearch erişimini dış dünyaya yani sizin uygulamanız dışındaki kişilere erişimi kapatmak için birkaç yol bulunmaktadır. Bunlardan birisi de kullanıcı adı ve şifre ile koruma yöntemidir. Bu yöntem sizin sunucularınızı tabi ki tamamen koruyamayabilir. Ancak kısıtlama iç ağdan birilerinde elasticsearch içerisinde rahatça işlem yapmalarını engelleyemez. Bunun için kullanıcı adı ve şifre ile koruyarak buradaki erişimi iç ağ üzerinde de kısıtlamaya çalışacağız. Bunu nginx yazılımı ile yapacağız.</p>

### 6.1 Nginx Nedir?

<p> Yüksek eş zamanlı çalışma kabiliyeti, yüksek performans ve düşük hafıza kullanımına odaklanılarak tasarlanmış bir Web sunucusudur. Aynı zamanda ters vekil sunucusu, yük dengeleyicisi ve http ön belleği olarak da kullanılabilir. Biz burada ters vekil sunucu özelliğini kullanarak Elasticsearch ve Kibanayı nginx’ in arkasına koyarak kullanıcı ve şifre ile koruma altına alacağız.</p>

### 6.2 Nginx Kurulumu ve Yapılandırılması

1) Nginx ve ‘Apache2-utils’i yüklüyoruz;</br>
`sudo apt install nginx apache2-utils -y`</br>

2)	elastic ve kibana adında serverlar oluşturuyoruz ve yapılandırıyoruz;</br>
`nano /etc/nginx/sites-available/kibana`</br>
`nano /etc/nginx/sites-available/elastic`</br>

<p> Localhost:9200 adresi üzerinde çalışan Elasticsearch ile localhost:5601 üzerinde çalışan kibana için elastic.local ve kibana.local domain’ inde ve 80.porttan çalışacak şekilde serverlar oluşturduk. Oluşturulan serverlara gelen bütün istekleri proxy_pass ile yönlendiriyoruz. Böylelikle uygulama içerisinde Elasticsearch ve Kibana ya erişmek için yeni server adreslerimizi kullanacağız.</p>
<p>Bu ayarlardan sonra firewall üzerinden 9200 ve 5601 portuna erişimi engelliyoruz ki bu portların üzerinden API arayüzü kullanılmaya devam etmesin. Sadece nginx’ in olduğu sunucu üzerinden erişilebilir olsun. Bu uygulamamda bu işlemi kendi bilgisayarımda yaptığım için bu erişişim konusunu göz ardı ettim.</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299383-e11f40a6-3a7b-4f22-92af-1bb5a51ab895.png"></br> Şekil 17 - Elastic server yapılandırması
</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299410-df1f2457-e30d-4699-8b6e-7ed62f1a8eb8.png"></br> Şekil 18 - Kibana server yapılandırması
</p>

3)	Şimdi buradaki asıl koruma aut_basic_user_file /etc/nginx/htpasswd.users yapılandırmasındadır. Bu dosyadaki bilgilere göre bu alana gelen istekleri koruma altına alıyoruz. Aşağıdaki kod ile kullanıcı adı ve şifre oluşturuyoruz ve etkinleştiriyoruz;</br>

•	sudo htpasswd -c /etc/nginx/.kibana-user elkadmin</br>
ln -s /etc/nginx/sites-available/kibana /etc/nginx/sites-enabled/
dosyanın içeriği aşağıdaki gibidir;
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299712-3f5a5b15-0f31-4b7a-96e4-9c0aa956af09.png"></br> Şekil 19 - htpasswd.user içeriği
</p>

4)	Oluşturduğumuz serverlar için DNS kayıtlarını giriyoruz.</br>
`sudo nano /etc/hosts`
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299736-fee41ab1-c19a-4ea6-98a0-29367d210069.png"></br> Şekil 20 - /etc/hosts kayıtları
</p>

5)	Nginx hizmetini etkinleştirip başlatıyoruz;</br>
`nginx -t`</br>
`systemctl enable nginx`</br>
`systemctl restart nginx`</br>

6)	Artık elastic.local ve kibana.local adresi üzerinden erişim sağlayabiliriz. Örnek olarak kibana.local üzerinden deniyoruz.</br>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299783-52b4f7d2-a9f8-433a-bba4-9b051170f8f5.png"></br> Şekil 21 - kibana.local erişimi
</p>

### 6.3 Nginx Loglarını İzlemeye Alma

<p> Bu işlemde Filebeat’in nginx modülünü kullanarak logları Logstash’e yönlendireceğiz. Logstash de Grok dili ile filtreleme şablonu oluşturarak gelen logları Elasticsearch’e aktaracağız. Bu işlemin amacı Elasticsearch ve Kibana’ya hem doğru hem de yanlış erişim denemelerin takibe alınmasıdır.</p>

1)	Filebeat in çalıştığını kontrol edip nginx modulünu etkinleştiriyoruz.</br>
`systemctl status filebeat`</br>
`sudo filebeat modules enable nginx`</br>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299829-f57b380a-8fbc-4b13-a09a-10f6b4c8405c.png"></br> Şekil 22 - Filebeat modül listesi
</p>

2)	filebeat.yml dosyasını yapılandırıyoruz;</br>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299857-d966ef36-d6fb-4166-8400-9e89cc4825f9.png"></br> Şekil 23 - filebeat.yml input yapılandırması
</p>

<p> Bu sefer Elasticsearch çıktısını pasif duruma getirip Logstash yapılandırılmasını aktifleştiriyoruz. Filebeat’den gelen loglar direk Elasticsearch’e gitmeyecek Logstash’e gönderilecektir. Bu işlem sonucunda filebeat hizmetini yeniden başlatıp çalıştırıyoruz.</p> 
`systemctl restart filebeat`</br>
`filebeat setup`</br>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299912-8cddc768-9d81-425b-b21f-7913b14dac95.png"></br> Şekil 24 - filebeat.yml output yapılandırılması
</p>

3)	Dosya izinlerini güncelliyoruz;</br>
`chown -R www-data:adm /var/log/nginx`</br>
`system restart nginx`</br>
`usermod -aG adm logstash`</br>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147299977-eb03200b-03bc-4655-b6e3-c520524dcf14.png"></br> Şekil 25 - Nginx dosya izinleri
</p>

4)	Grok komutları için desen dosyası oluşturuyoruz. Oluşan dosyayı yetkilendirerek Grok desen kodunu kaydediyoruz.</br>
`mkdir /etc/logstash/pattern`</br>
`chmod 755 -R /etc/logstash/Pattern`</br>
`sudo nano /etc/logstash/Pattern/nginx`</br>
`NGUSERNAME [a-zA-Z\.\@\-\+_%]+`</br>
`NGUSER %{NGUSERNAME}`</br>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147300261-a54890bc-b7eb-41f3-ab96-20d67ac95361.png"></br> Şekil 26 - Pattern desen dosyası
</p>


5)	Logstash ‘in Pipeline (Boru Hattı) sisteminin üzerinde çalıştığı conf.d klasörünün altında nginx.conf yapılandırma dosyasını oluşturuyoruz. Bu dosya içerisinde yazdığımız kodlar ile Logstash ’in input, filter ve output ayarlamasını sağlıyoruz.</br>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147300100-17678c2c-f54a-45b4-af50-e7ee11913641.png"></br> Şekil 27 - Logstash yapılandırma dosyası
</p>

6)	Logstash hizmetini yeniden başlatıyoruz.</br>
`systemctl restart logstash`</br>

7)	Son olarak kibana günlük panosu oluşturacağız. kibana.local adresine gidip Indeks modelimiz olarak nginx-* tanımladık. Zaman filtresi alan adı alanında @timestamp'ı seçip modeli oluşturuyoruz. Discover ekranına geldiğimizde nginx üzerindeki Access.log ve error.log dosyalarından logları çektiğini görüyoruz. Örnek olarak bir logu açtığımızda “elkadmin” kullanıcı adımız sisteme düşmüştür.
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147300145-b9b6f932-ea2d-400d-8da8-3c1345ff9083.png"></br> Şekil 28 - Nginx log kayıtları
</p>

