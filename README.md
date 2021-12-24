<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147292359-46690a5b-7f0f-4095-a7d0-10bda82b7af7.png">
</p>

<p align="center"> 
SELÇUK ÜNİVERSİTESİ</br>
TEKNOLOJİ FAKÜLTESİ</br>
BİLGİSAYAR MÜHENDİSLİĞİ BÖLÜMÜ</br>
MÜHENDİSLİK TASARIMI PROJESİ</br>
</p>
<p align="center"> 
LİNUX İŞLETİM SİSTEMİNDE </br>
ELK STACK İLE LOG İZLEME</br></br>
Mustafa ERDOĞAN</br>
21/11/2021
</p>

## İÇİNDEKİLER

### 1. GİRİŞ	</br>
### 2. LOG KAVRAMI	</br>
2.1 Log Nedir?	</br>
2.2 Log Toplama	</br>
2.3 Log Yönetimi ve Analizi	</br>
### 3. SIEM (Security Information and Event Management)	</br>
3.1 SIEM’ in Uygulama Adımları	</br>
3.2 SIEM’ in Önemi	</br>
### 4. ELK STACK NEDİR?	</br>
4.1 Elasticsearch	</br>
4.1.1 Temel Elasticsearch Kavramları	</br>
4.2 Logstash	</br>
4.2.1 Logstash Nasıl Çalışır?	</br>
4.3 Kibana	</br>
4.4 Beats	</br>
### 5. ELK STACK KURULUMU	</br>
5.1 Elasticsearch Kurulumu	</br>
5.2 Logstash Kurulumu	</br>
5.3 Kibana Kurulumu	</br>
5.4 Filebeat Kurulumu	</br>
5.5 Kibana Günlük Panosu Oluşturma	</br>
### 6. NGİNX İLE KORUMA ALTINA ALMA	</br>
6.1 Nginx Nedir?	</br>
6.2 Nginx Kurulumu ve Yapılandırılması	</br>
6.3 Nginx Loglarını İzlemeye Alma	</br>
### 7. LOG DOSYALARINI İZLEME	</br>
7.1 Neden Log Dosyalarını İzliyoruz?	</br>
7.2 Çoklu Giriş ve Çıkış Ayarlı Logstash	</br>
7.3 İzlenmesi Gereken Bazı Önemli Log Dosyaları	</br>
7.3.1 Syslog	</br>
7.3.2 Messages.log	</br>
7.3.3 Auth.log	</br>
7.3.4 Secure Log	</br>
7.3.5 Apt Logları	</br>
7.3.6 Dmesg Logları	</br>
7.3.7 Kern.log	</br>
7.3.8 Faillog	</br>
### 8. PACKETBEAT İLE AĞ TRAFİĞİNİ	</br>
### 9. SURİCATE İLE AĞ TRAFİĞİNİ	</br>

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

## 7. LOG DOSYALARINI İZLEME

<p> Log dosyaları, Linux’un yöneticiler için depoladığı, sunucu, çekirdek, hizmetler ve üzerinde çalışan uygulamalar hakkındaki önemli olayları izlemesi için kaydedilen kayıtlardır. Bu yazıda, sunucu yöneticilerinin izlemesi gereken Linux Log dosyalarını gözden geçireceğiz.</p>
<p> Log dosyaları, Linux yöneticilerinin önemli olayları izlemesini sağladığı bir dizi kayıttır. Çekirdek, hizmetler ve üzerinde çalışan uygulamalar dahil olmak üzere sunucu hakkında mesajlar içerirler. Linux, / var / log dizini altında bulunabilen merkezi bir Log dosyaları deposu sağlar.</p>
<p> Bir Linux ortamında oluşturulan log dosyaları genellikle dört farklı kategoride sınıflandırılabilir:</p>
1.	Application (Uygulama) Logları</br>
2.	Event (Olay) Logları</br>
3.	Service (Servis) Logları</br>
4.	System (Sistem) Logları</br>

### 7.1 Neden Log Dosyalarını İzliyoruz?

<p> Log yönetimi, bir sistem yöneticisinin en önemli sorumluluklarından biridir. Sistem üzerinde gerçekleşen olaylardan haberdar olmak, sistemin durumunu öğrenmek, alınan hata mesajlarının sorunun sebebinin ne olduğunu bilmek ve bunlara göre çözüm bulmak sistem yöneticisinin görevidir. Tabi ki günümüzde artık makine öğrenmesi sayesinde bu log analizi işlemleri daha hızlı ve daha aktif şekilde yapılmaktadır. Bu yüzden de bu log kayıtları hayati önem taşımaktadır.</p>
<p>Kısacası, Log dosyaları, ortaya çıkmadan önce ortaya çıkacak sorunları önceden tahmin edebilmenizi sağlar.</p>

### 7.2 Çoklu Giriş ve Çıkış Ayarlı Logstash

<p>Logstash, bir giriş ve çıkış verileri üzerinden gönderim yapmanın yanışına bunu ihtiyacımız olanı kadar esnetebilmemiz için bize imkan sağlamaktadır. Yani her bir dosya için tek tek .conf dosyası oluşturmak yerine bu işlemi bir dosya içerisinde Şekil – 29 da ki yapıyı kullanarak halledebiliyoruz. Her input girişi için bir etiket(tags) değeri atıyoruz. Bu etiket değeri üzerinden koşul işlemleri ile yönlendirmeler yapıyoruz. Bu aşamadan sonra biz bu yapı üzerinden gideceğiz.</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147311997-d7789fc8-8913-4b9f-afec-f62ebce2e764.png"></br> Şekil 29 - Multiple inputs and Outputs Logstash
</p>

### 7.3 İzlenmesi Gereken Bazı Önemli Log Dosyaları
## 7.3.1 SysLog 

<p> Syslog, Linux işletim sistemimiz üzerinde neler olup bittiğini öğrenmek ve geriye yönelik sisteme giriş, uyarı, durum, hata ve rapor gibi kayıtların tutulduğu sistemlerdir. Bu sayede sistem yöneticileri için büyük önem taşır. Syslog sistem hatalarının, saldırıları veya işletim sisteminde oluşan problemler gibi durumları kayıt altında tutar. Bu gibi durumları tespit edebilmemiz için SysLog kayıtlarını geçmişe yönelik olarak incelememiz yeterli olacaktır. Bu kayıtlar sayesinde sistem yöneticileri sistem üzerindeki anormallikleri tespit ederek sistemin verimli veya güvenli çalışmasını sağlar, olası hata durumlarında çözüm üretebilirler.</p>
<p>Syslog sistemlerinin bir diğer özelliği de başka bir sisteme bu kayıtların aktarılabilmesidir. Bu sayede uzaktan log kayıtlarınıza erişebilir ve yönetim sağlayabilirsiniz. Klasik Linux sistemlerde bu kayıtlar /etc/syslog.conf şeklinde ayarlanabilir. Ancak modern Linux sistemlerde SysLog’a yeni özellikler eklenerek rsyslogd servisi geliştirilmiştir.</p>
`nano /etc/rsyslogd.conf`
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147312121-75e1692e-a6e6-4f44-a357-5bb89163fd08.png"></br> Şekil 30 - rsyslog.conf
</p>
<p> Klasik Linux sistemlerde log kayıtları /var/log dizini altında tutulmaktadır. Bu dizin SysLog aracılığı ile ortak bir şekilde log kayıtların tutulduğu önemli bir dizindir. Biz bu örnekte Syslog.conf dosyasından yönlendirme ayarlarına dokunmadan filebeat ile bu dizindeki logları alacağız.</p>
<p>Örnek: Sistem günlüğüne bir mesaj yazalım ardından yazdığımız mesajı SysLog dosyasından görelim. Bu işi yapan logger programı hızlı bir şekilde basit bir komutla sistem günlüğüne mesaj yazmanızı sağlar. Aşağıdaki komutu kullanarak sistem günlüğüne “Mühendislik Tasarımı” yazalım, hemen ardından tail komutunu kullanarak yazdığımız mesajı SysLog dosyasında görelim.</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147312175-023d560a-313b-48d8-8188-764c9adb49fd.png"></br> Şekil 31 - Logger SysLog Örneği-1
</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147312212-6464c59b-cace-4cc2-a3d5-a83752866ad0.png"></br> Şekil 32 - Logger SysLog Örneği-2
</p>

#### SysLog Dosyasını İzleme Alalım;

1)	Filebeat yapılandırma dosyasında tüm SysLog dosyalarını okuması için tanımlamasını yapıyoruz.</br>
`sudo nano /etc/filebeat/filebeat.yml`
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147312621-3a474ad6-ccdc-4232-9bb5-72e98dd588d9.png"></br> Şekil 33 - filebeat.yml Yapılandırma Dosyası
</p>

2)	Logstash conf.d dizinine giderek daha önce oluşturduğumuz tek giriş ve çıkış yönlendirmeli yapılandırma dosyasını Şekil 33 de görmekteyiz.</br>
`cat /etc/logstash/conf.d/nginx.conf`
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147312719-b574874d-c730-4bf8-9051-913a13061dd6.png"></br> Şekil 34 - Logstash Yapılandırma Dosyası
</p>

3)	Bu adımda logstash/conf.d dizininde oluşturduğumuz yapılandırma dosyasını Şekil 29 da ki yapıya uyarlayarak daha önceden oluşturduğumuz nginx erişim yönlendirmesinin üzerine SysLog dosyasını ekleyeceğiz.</br>
`sudo nano /etc/logstash/conf.d/nginx.conf` dosyasını açarak ilk olarak input kısmını ekliyoruz.
<p> Burada nginx ve SysLog girişlerine bir etiket(tags) belirliyoruz. Bu etiket üzerinden filter ve output adımlarında koşullu işlemlere dahil edeceğiz.</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147313836-c0cda3da-d1f4-4ce0-bde7-e7c27fdcf08b.png"></br> Şekil 35 - Yapılandırma Dosyası İnput Alanı
</p>
•	Oluşturduğumuz etiketler üzerinden burada input kısmındaki log dosyalarını koşullara dahil ettik. Belirlediğimiz etiket değerine karşılık gelen Grok kodlarını yazarak hangi yönlendirme trafiğini düzenlemiş oluyoruz.</br>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147313870-a5dde5a5-b982-44bf-a314-b73dfe487836.png"></br> Şekil 36 - Yapılandırma Dosyası Filter Alanı
</p>

•	Output alanında ise etiket değerlerine göre elasticsearch de index oluşturma işlemini gerçekleştiriyoruz.</br>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147313918-e436f36b-e853-466e-8d45-3697cb2c55e4.png"></br> Şekil 37 - Yapılandırma Dosyası Output Alanı
</p>

4)	Logstash ve filebeat hizmetlerini yeniden başlatıyoruz.</br>
`systemctl restart filebeat` </br>
`systemctl restart logstash` </br>
5)	Son olarak kibana günlük panosu da syslog-* Indeks modelimizi tanımladık.  Discover ekranına geldiğimizde SysLog üzerinden okunan logları görebiliyoruz. “Logger” komutu ile oluşturduğumuz “Mühendislik Tasarımı” bilgisini de görebiliyoruz.
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147314092-237787af-19a2-4d98-a7d9-f648e44b9b6e.png"></br> Şekil 38 - Syslog Detayı
</p>

### 7.3.2 Messages.log

<p> Bu log dosyasında genel sistem etkinlik logları tutulur. Bilgi amaçlı ve kritik olmayan sistem mesajlarını saklamak için kullanılır. Debian tabanlı Linux dağıtımlarında “/var/log/syslog” dizini ile benzerlik gösterir. Bir sorun olup olmadığının kontrolü için bakılması gereken ilk log dosyası burası olmalıdır. Bu log dosyası sayesinde aşağıdaki şeyleri öğrenebiliriz.</p>

1.	Çekirdek dışı önyükleme hataları</br>
2.	Uygulamalar ile ilgili servis hataları

<p>Bu örneklerin üzerinden gittiğimiz Linux dağıtımı Ubuntu da /var/log/messages log dosyasının tuttuğu loglar SysLog kayıtlarında bulunmaktadır. Bu nedenle bu logları biz bir önceki Syslog başlığı altında izlemeye almış olduk.</p>

### 7.3.3 Auth.log 

<p> Kimlik doğrulama işlemleri ile ilgili tüm olaylar buraya kaydedilir. Debian ve Ubuntu dağıtımlarında bu log dosyasını görebilirsiniz. Aslında önemli bir log dosyasıdır. Güvenlik açıkları ile ilgili tespit yapmaya çalışıyorsanız buraya bakmanız gerekir. Sisteme kim giriş yapmış, kim kaç kere yanlış şifre girmiş veya kaba kuvvet saldırı deneyen birini buradaki log kayıtları ile tespit edebilirsiniz. Buradaki kayıtları ile aşağıdaki olayları tespit edebilirsiniz:</p>
1.	Sisteme giriş yapan kişileri</br>
2.	Yanlış şifre deneyen kişileri</br>
3.	Başarılı veya başarısız oturum açan kişileri</br>
4.	Kaba kuvvet saldırısı deneyen kişileri</br>

 #### Auth.log Dosyasını İzleme Alalım

1)	Filebeat yapılandırma dosyasında tüm auth.log dosyalarını okuması için tanımlamasını yapıyoruz.</br>
`sudo nano /etc/filebeat/filebeat.yml`
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147314225-0747e588-40cc-403d-bda3-d78b6435fcb7.png"></br> Şekil 39 - filebeat.yml
</p>

2)	logstash/conf.d dizininde ki yapılandırma dosyamıza auth.log dosyalarını input  alanında tanımlıyoruz. Elasticsearch’e gerekli index’i oluşturması için yönlendirme tanımlamasını ise output kısmında gerçekleştiriyoruz.</br>

`sudo nano /etc/logstash/conf.d/nginx.conf`
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147314300-c892c8a9-b53e-4022-b822-6d28c814045a.png"></br> Şekil 40 - logstash/conf.d/nginx.conf -> input
</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147314328-42e74bfe-e0ff-4915-af2b-a234757e86b7.png"></br> Şekil 41 - logstash/conf.d/nginx.conf -> output
</p>

3)	Burada önceki yapılandırma dosyalarındaki yaptığımız değişikliklerden bir fark var. O da auth.log dosyası için bir filter yapılandırması tanımlamadık ve ona uygun Grok şablonu kullanmadık. Bu şekilde de logstash yapılandırılması mevcuttur. Fakat okunabilirliği artırmak için, ihtiyaca göre değişiklik gösterdiği için tercih size kalmıştır.
4)	Kibana üzerinden “Stack Management-> Index Pattern” üzerinden auth.log dosyaları için modelimizi oluşturuyoruz. Discover sekmesinden ise bu logları detaylı bir şekilde görebilirsiniz.
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147314462-f9168f13-9bf7-4ec9-b604-113b28031444.png"></br> Şekil 42 - Discover auth.log detay
</p>

<p> NOT: Eğer güncel logları görmekte problem yaşıyorsanız Şekil 42’de ki saat ayarlarını güncelleyiniz.</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147314549-a0809373-d4c3-428b-b34e-98137647e575.png"></br> Şekil 43 - Discover Zaman Bilgisi
</p>

### 7.3.4 Secure.log
<p> RedHat ve CentOS tabanlı sistemler /var/log/auth.log yerine bu log dosyasını kullanır. Temel olarak ise authentication sistemlerinin kullanımını izlemek için kullanılır.</p>
<p> Kimlik doğrulama hataları dahil, güvenlikle ilgili tüm mesajları saklar.</p>
Ayrıca sudo girişlerini, SSH girişlerini ve sistem güvenlik hizmetleri arka planında oturum açan diğer hataları izler.
<p> Bur örneğimizde Ubuntu üzerinde auth.log dosyasını bir önceki adımda izlemeye almıştık.</p>

### 7.3.5 Apt / Yum Logları

<p> Sisteme yeni bir paket yüklendiğinde bu işlemin bilgileri buraya kaydedilir. Linux dağıtımlarına göre paket türleri değişmektedir. Biz Ubuntu üzerinden ilerlediğimiz için /var/log/apt dizini altındaki history.log ve term.log dosyasını izlemeye alacağız. Farklı bir dağıtım için /var/log/ dizinin altında uygun paketin log dosyasını izlemelisiniz.</p>
Bu log dosyası sayesinde şunları öğrenebiliriz:</br>
1.	Bir paketin doğru kurulup kurulmadığını</br>
2.	Yazılım yüklemeleriyle ilgili sorunları</br>
3.	Yakın zamanda kurulmuş paketleri bulmak</br>

1)	Filebeat ile /var/log/apt dizinin altındaki log dosyalarını okumasını söylüyoruz.
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147314681-d93f0442-67f0-4dd8-bdcc-dfc37bbd7d57.png"></br> Şekil 44 - filebeat.yml
</p>

2)	Logstash yapılandırma dosyasında giriş ve çıkış yönlendirme ayarlarını yapıyoruz.
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147314703-e79771e8-f8fe-4bf7-b878-050f2585aa01.png"></br> Şekil 45 - logstash/conf.d/nginx.conf -> input
</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147314741-3c41298a-e250-43a5-a388-c1c0ed40318f.png"></br> Şekil 46 - logstash/conf.d/nginx.conf -> output
</p>

3)	Logstash ve filebeat hizmetlerini yeniden başlatıyoruz.</br>
`systemctl restart filebeat` </br>
`systemctl restart logstash` </br>

4)	Kibana üzerinden index modeli oluşturarak Discover alanında apt Loglarını görebiliyoruz.
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147314803-3da297a3-b632-4a8d-b9cc-08d415ca0141.png"></br> Şekil 47 - Apt Log Kayıtları
</p>

<p> NOT: Kalan diğer log dosyalarını bu şekilde elasticsearch havuzunda topluyoruz. İhtiyacınıza bağlı olarak farklı dosyaları da bu şekilde izleyebilirsiniz. Dmesg.log, kern.log, Faillog dosyalarını bu şekilde izlemeye alabiliriz.</p>

### 7.3.6 Dmesg Logları

Linux çekirdeği tarafından kaydedilen bilgileri bu log dosyası depolar.</br>
Bu log dosyası sayesinde şunları öğrenebiliriz:</br>
1.	Çekirdek ile ilgili hataları ve uyarıları görmek için.</br>
2.	Çekirdeğin sorunlarını gidermek için.</br>
3.	Ayrıca donanım ve bağlantı sorunlarında hata ayıklamak için</br>


### 7.3.7 Kern.log

<p> Donanım ve sürücüler ile ilgili bilgiler buraya kaydedilir. Çekirdek, önyükleme işlemi sırasında sunucuyla ilişkili fiziksel donanım aygıtlarını algıladığından, aygıt durumunu, donanım hatalarını ve diğer genel mesajları yakalar. Bir donanım düzgün çalışmıyorsa veya algılanmıyorsa, sorunu bu log dosyasında arayabilirsiniz.</p>

### 7.3.8 Faillog
<p> Bu dosya başarısız giriş denemeleri hakkında bilgi içerir. Username/Password Hacking ve Brute-Force Attack içeren herhangi bir güvenlik ihlali girişimini bulmak için yararlı bir log dosyası olabilir.</p>

## 8. PACKETBEAT İLE AĞ TRAFİĞİNİ DİNLEME

<p> Packetbeat, Elasticsearch tarafından geliştirilen ve ağ trafiği yakalama için libpcap kitaplığını kullanan bir ağ izleme aracıdır. Bu aracı kullanarak http, TLS veya DNS'yi diğer birçok ağ protokolünü izleyebiliriz. Ancak, aracın güvenliğe değil, uygulama izlemeye odaklandığını belirtmek gerekir. Bu, güvenlik açısından yararlı olmayacağı anlamına gelmez, ancak birkaç sınırlamanın farkında olmamız gerekir.</p>

1)	`cd /etc/packetbeat/packetbeat.yml` yapılandırma dosyasına giderek aşağıdaki ayarlamaları yapıyoruz.</br>

•	Cihaz arayüzünü “herhangi biri” olarak ayarlıyoruz.
 <p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147315044-6dfca46f-a1e6-45b3-91b3-e32b1faec55e.png"></br> Şekil 48 - packetbeat.yml -1
</p>
•	Dinlenecek protokolleri belirliyoruz.
 <p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147371544-8478754a-0ba5-4d72-9813-cd378b9bb277.png"></br> Şekil 49 - packetbeat.yml -2
</p>
•	Dashboard özelliğini aktif ediyoruz.
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147371553-31b88e96-90f7-45ff-b8dd-1538b9cf8a0b.png"></br> Şekil 50 - packetbeat.yml -3
</p>
•	Dinlenen paketlerin çıkış yönlendirmesini yapıyoruz.
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147371585-4eed460e-f886-46ce-a944-20dad94e63d9.png"></br> Şekil 51 - packetbeat.yml -4
</p>
•	Kibana panolarını kullanmak için gerekli yönlendirmeyi yapıyoruz.
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147371598-0c8eb646-a501-40a3-ade5-9a0be3917d0a.png"></br> Şekil 52 - packetbeat.yml -5
</p>
•	Son olarak packetbeat servisini yeniden başlatıyoruz.</br>

`systemctl restart packetbeat`</br>
2)	Discover alanına baktığımızda Network loğlarının düştüğünü görmüş oluyoruz.
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147371618-381879e7-a22f-46ea-b482-a1a931c0ca61.png"></br> Şekil 53 - Packetbeat log kayıtları
</p>
3)	Packetbeat ile networkü dinlememizin sonucunda ulaştığımız bilgileri bir Dashboard oluşturarak istatistiksel olarak göreceğiz.</br>
•	Dashboard alanına giderek “Overview ECS” seçtik den sonra “Create Dashboard” ile oluşturuyoruz.
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147371632-a402a13a-9828-4c96-aa9f-ce80cf00da0e.png"></br> Şekil 54 - Packetbeat Dashboard
</p>
4)	Bu panelde, networkü dinlememiz sonucunda ulaştığımız bilgiler ile aşağıdaki sonuçlara bir görsel düzen içerisinde ayrıntılı bir şekilde ulaşabiliyoruz;</br>
•	DNS işlemleri</br>
•	HTTP işlemleri</br>
•	TLS oturumları</br>
•	İşlem türleri</br>
•	Yanıt sürelerinin yüzdeleri</br>
•	Zaman içinde hata sayısı  </br>
•	Networkü oluşturan cihazlar</br>

<p> gibi bir çok bilgilere istatistiksel bir şekilde ayrıntılı olarak ulaşabiliriz. Bu kısım detay ve geniş kapsamlı olabileceği için üzerinde fazladan zaman harcayarak pekiştirmek daha doğru olacaktır.</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147371664-0c2bc534-8ab2-47e5-86a1-5872af4d29dd.png"></br> Şekil 55 - Overview ECS
</p>
<p align="center"> 
<img src="https://user-images.githubusercontent.com/82549640/147371702-7da6cb08-5b59-4990-bcf7-84d5f5d024a1.png"></br> Şekil 56 - Top Hosts Creating Traffic
</p>


## 9. SURİCATE İLE AĞ TRAFİĞİNİ DİNLEME

<p> Suricata, Packetbeat’in aksine hem algılama hem de koruma özellikleri sunar ve ELK yığınıyla entegre edilebilir. Packetbeat'in aksine, Suricata ağ trafiğini izler ve önceden tanımlanmış güvenlik kurallarından bazılarıyla eşleşmeye çalışır. Bu trafik belirli bir güvenlik kuralıyla eşleşiyorsa, Suricata bir uyarı oluşturabilir veya bu trafiği engelleyebilir. Ayrıca, kötü amaçlı etkinlikleri algılayan birçok açık kural vardır, ancak kendi kurallarımızı da oluşturabiliriz.</p>

