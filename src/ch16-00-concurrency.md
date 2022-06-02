# Korkusuz Eşzamanlılık

*Eşzamanlı programlamayı* güvenli ve verimli bir şekilde kullanmak, Rust'ın ana hedeflerinden birisidir. 
Bir programın farklı bölümlerinin bağımsız olarak yürütüldüğü *eşzamanlı programlama* ve bir programın farklı bölümlerinin aynı anda yürütüldüğü *paralel programlama* giderek daha önemli hale geliyor.

Tarihsel olarak, bu tarz bir programlama zor ve hataya açıktı: Rust, bunu değiştirmeyi umuyor. 
Başlangıçta Rust ekibi, bellek güvenliğini sağlamanın ve eşzamanlılık sorunlarını önlemenin farklı yöntemlerle çözülmesi gereken iki ayrı zorluk olduğunu düşündü. Zamanla ekip, sahiplik *ve* tür sistemlerinin bellek güvenliği ve eşzamanlılık sorunlarını yönetmeye yardımcı olacak güçlü bir araç seti olduğunu keşfetti! 
Rust, bir çalışma zamanı eşzamanlılık hatasının oluştuğu kesin koşulları yeniden oluşturmaya çalışmak için fazladan zaman harcamak yerine, yanlış kod derlemeyi reddedecek ve sorunu açıklayan bir hata sunacaktır. Sonuç olarak, kodunuz üretime gönderildikten sonra değil, siz üzerinde çalışırken kodunuzu düzeltebilirsiniz. Rust'ın *korkusuz* eşzamanlılığının bu yönüne bir takma ad verdik. *Korkusuz* eşzamanlılık, ince hatalardan arınmış ve yeni hatalar eklemeden yeniden düzenlenebilmesi yönüyle kolay kod yazmanıza olanak tanır. 

> Not: Basitlik olması babından, eşzamanlı ve/veya paralel diyerek daha kesin olmak yerine birçok soruna *eşzamanlı* olarak değineceğiz. 
> Bu kitap eşzamanlılık ve/veya paralellik hakkında olsaydı, daha spesifik açıklardık. Bu bölüm için, *eşzamanlı* kullandığımızda lütfen zihinsel olarak 
> eşzamanlı ve/veya paralel olarak değiştirin.

Birçok dil, eşzamanlı sorunları ele almak için sundukları çözümler konusunda dogmatiktir. 
Örneğin, Erlang, mesaj iletme eşzamanlılığı için zarif bir işlevselliğe sahiptir, ancak iş parçacıkları arasında durumu paylaşmak için yalnızca belirsiz yollara sahiptir. Olası çözümlerin yalnızca bir alt kümesini desteklemek, üst düzey diller için makul bir stratejidir, çünkü daha yüksek düzeyli bir dil, soyutlamalar elde etmek için bazı kontrollerden vazgeçmenin yararlarını vaat eder. 
Bununla birlikte, daha düşük seviyeli dillerin, herhangi bir durumda en iyi performansla çözümü sağlaması ve donanım üzerinde daha az soyutlamaya sahip olması beklenir. 
Bu nedenle Rust, durumunuza ve gereksinimlerinize uygun olan herhangi bir şekilde problemleri modellemek için çeşitli araçlar sunar.

İşte bu bölümde ele alacağımız konulardan bazıları:

* Aynı anda birden çok kod parçasını çalıştırmak için iş parçacıklarının nasıl oluşturulacağı
* *Mesaj geçişi* türünde eşzamanlılık, iş parçacıklarının ileti dizileri arasında mesaj gönderdiği yer
* *Paylaşılan durum* türünde eşzamanlılık, birden çok iş parçacığının bir veri parçasına erişimi olduğu yer
* Rust'ın eşzamanlılık garantilerini kullanıcı tanımlı türlere ve standart kütüphane tarafından sağlanan türlere 
  genişleten `Sync` ve `Send` tanımları

