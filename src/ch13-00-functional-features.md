# Fonksiyonel Dil Özellikleri: Yineleyiciler ve Kapanış İfadeleri

Rust'ın tasarımı birçok mevcut dil ve teknikten ilham almıştır ve önemli bir etkisi de fonksiyonel programlamadır. 
Fonksiyonel bir tarzda programlama, genellikle, fonksiyonları argümanlarda ileterek, diğer fonksiyonlardan geri döndürerek, 
daha sonra yürütülmek üzere değişkenlere atayarak vb. değerler olarak kullanmayı içerir.

Bu bölümde, fonksiyonel programlamanın ne olduğu veya olmadığı konusunu tartışmayacağız, 
bunun yerine Rust'ın birçok dilde genellikle fonksiyonel olarak adlandırılan özelliklere benzeyen bazı 
özelliklerini tartışacağız.

Daha özel olarak, ele alacağız:

* *Kapanış ifadeleri*, bir değişkende saklayabileceğiniz fonksiyon benzeri bir yapı
* *Yineleyiciler*, bir dizi elemanı işlemenin bir yolu
* Bölüm 12'deki I/O projesini geliştirmek için bu iki özelliğin nasıl kullanılacağı
* Bu iki özelliğin performansı (Spoiler uyarısı: düşündüğünüzden daha hızlılar!)


Diğer bölümlerde ele aldığımız model eşleştirme ve numaralandırma gibi diğer Rust özellikleri de fonksiyonel stilden etkilenir. 
Kapanış ifadelerine ve yineleyicilere hakim olmak, deyimsel, hızlı Rust kodu yazmanın önemli bir parçasıdır, bu yüzden bu bölümün tamamını 
onlara ayıracağız.
