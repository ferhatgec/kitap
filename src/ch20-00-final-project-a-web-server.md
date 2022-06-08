# Bitirme Projesi: Çok İş Parçacıklı Bir Web Sunucusu Oluşturma

Uzun bir yolculuk oldu ama kitabın sonuna geldik. 
Bu bölümde, son bölümlerde ele aldığımız bazı kavramları göstermek ve daha önceki bazı bölümleri
özetlemek için birlikte bir proje daha oluşturacağız.

Bitirme projemiz için, bir web tarayıcısına “hello” yazan ve 
Şekil 20-1'dekine benzeyen bir web sunucusu yapacağız.

![rust'tan bir merhaba](img/trpl20-01.png)

<span class="caption">Şekil 20-1: Nihai bitirme projemiz</span>

İşte web sunucusunu oluşturma planı:

1. TCP ve HTTP hakkında biraz bilgi edinmek.
2. Bir soketteki TCP bağlantılarını dinlemek.
3. Az sayıdaki HTTP isteklerini ayrıştırmak.
4. Uygun bir HTTP yanıtı oluşturmak.
5. Bir iş parçacığı havuzuyla sunucunun verimini iyileştirmek.

Ancak başlamadan önce bir ayrıntıdan bahsetmeliyiz: 
Kullanacağımız yöntem Rust ile bir web sunucusu oluşturmanın en iyi yolu olmayacak. 

[crates.io](https://crates.io/)'da, oluşturacağımızdan daha eksiksiz web sunucusu ve 
iş parçacığı havuzu süreklemelerini sağlayan üretime hazır bir dizi kasa mevcuttur.

Ancak bu bölümdeki amacımız, kolay yolu seçmek değil, 
öğrenmenize yardımcı olmaktır. Rust bir sistem programlama dili olduğu için, 
çalışmak istediğimiz soyutlama seviyesini seçebilir ve diğer dillerde mümkün olan 
veya pratik olandan daha düşük bir seviyeye gidebiliriz. Gelecekte kullanabileceğiniz kasaların 
arkasındaki genel fikirleri ve teknikleri öğrenebilmeniz için temel HTTP sunucusunu ve 
iş parçacığı havuzunu kendimiz yazacağız.
