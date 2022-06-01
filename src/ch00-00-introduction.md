# Başlangıç

> Not: Kitabın bu sürümü İngilizce olarak [Rust Programlama
> Dili][nsprust]'da ve basılı ve e-kitap formatında [No Starch
> Press][nsp]'dan temin edilebilir.

[nsprust]: https://nostarch.com/rust
[nsp]: https://nostarch.com/

*Rust Programlama Diline* hoş geldiniz. Bu kitap size Rust'ı tanıtacaktır.
Rust programlama dili size daha hızlı, daha güvenli program yazmanız için yardım eder.
Yüksek seviyeli ergonomi ve düşük seviyeli kontrol, programlama dili tasarımında genellikle çelişkili olarak lanse edilir; 
Rust, işte bu çatışmaya meydan okur. Güçlü teknik kapasiteyi ve harika bir geliştirici deneyimini dengeleyen Rust, size düşük seviyeli ayrıntıları (bellek kullanımı gibi) bu tür kontrollerle geleneksel olarak ilişkilendirilen tüm güçlükler olmadan kontrol etme seçeneği sunar.

## Rust Kimin İçin

Rust çeşitli nedenlerden dolayı çoğu kişi için idealdir. Hadi bazı çok önemli gruplara göz atalım.

### Geliştirici Ekipleri

Rust, farklı düzeylerde sistem programlama bilgisi olan büyük geliştirici ekipleri arasında iş birliği yapmak için 
üretken bir araç olduğunu kanıtlıyor. Düşük seviyeli kod, diğer birçok dilde yalnızca deneyimli geliştiriciler tarafından
kapsamlı testler ve dikkatli kod incelemesi yoluyla yakalanabilen çeşitli ince hatalara yönelimlidir. Rust'ta derleyici, 
eşzamanlılık hataları da dahil olmak üzere bu anlaşılması zor hatalarla kod derlemeyi reddederek bir kapı bekçisi rolü oynar. 
Derleyici ile birlikte çalışarak ekip, zamanlarını hataları aramak yerine programın mantığına odaklanarak geçirebilir.

Rust ayrıca çağdaş geliştirici araçlarını sistem programlama dünyasına getiriyor:

* Cargo, dahili bağımlılık yöneticisi ve inşa aracı, eklemeler yapar,
  derler, sorunsuzca bağımlılıkları yönetir ve Rust ekosisteminin en önemli parçalarından birisidir.
* Rustfmt, geliştiriciler arasında tutarlı bir kodlama stili sağlar.
* Rust Dil Sunucusu, Entegre Geliştirme Ortamına (IDE) kod tamamlama ve hata mesajları için güç verir.

Geliştiriciler, Rust ekosisteminde bunları ve farklı araçları kullanarak sistem programlama seviyesinde daha üretken olabilirler.

### Öğrenciler

Rust, öğrenciler ve sistem kavramlarını öğrenmek isteyenler içindir. 
Rust'ı kullanarak birçok kişi, işletim sistemi geliştirme gibi konuları öğrendi. 
Topluluk çok sıcakkanlı ve öğrencilerin sorularını yanıtlamaktan mutluluk duyuyor. 
Rust ekipleri, bu kitap gibi çaba göstererek sistem kavramlarını, özellikle programlamada 
yeni olanlar için daha fazla insan için daha erişilebilir hale getirmek istiyor.

### Şirketler

Büyük ya da küçük yüzlerce şirket, çeşitli görevler için üretimde Rust kullanıyor. 
Bu görevler arasında komut satırı araçları, web hizmetleri, DevOps araçları, gömülü cihazlar, 
ses ve video analizi ve kod dönüştürme, kripto para birimleri, biyoinformatik, arama motorları, Nesnelerin İnterneti uygulamaları, 
makine öğrenimi ve hatta Firefox web tarayıcısının büyük bölümleri de yer alıyor.

### Açık Kaynak Geliştiricileri

Rust, Rust programlama dili, topluluk, geliştirici araçları ve kitaplıklar oluşturmak isteyenler içindir.
Rust diline katkıda bulunmanızı çok isteriz.

### Hıza ve Kararlılığa Önem Verenler

Rust, bir dilde hız ve istikrar isteyen insanlar içindir. 
Hız derken, Rust ile oluşturabileceğiniz programların hızını ve Rust'ın bunları yazmanıza izin verdiği hızı kastediyoruz. 
Rust derleyicisinin denetimleri, yeni özellik eklemeleri ve yeniden düzenleme yoluyla kararlılık sağlar. 
Bu, geliştiricilerin genellikle değiştirmekten korktukları, bu kontrollerin olmadığı dillerdeki kırılgan eski kodun aksine. 
Rust, sıfır maliyetli soyutlamalar, elle yazılan kod kadar hızlı bir şekilde daha düşük seviyeli koda derlenen daha yüksek seviyeli özellikler için çabalayarak her güvenli kodun da hızlı kod olmasını sağlamaya çalışır.

Rust dili, diğer birçok kullanıcıyı da desteklemeyi umuyor; burada bahsedilenler sadece en büyük paydaşlardan bazılarıdır. 
Genel olarak, Rust'ın en büyük amacı, güvenlik ve üretkenlik olmak üzere artı olarak hız ve ergonomi sağlayarak 
geliştiriclerin on yıllardır kabul ettiği ödünleri ortadan kaldırmaktır. 
Rust'ı deneyin ve seçeneklerinin sizin için işe yarayıp yaramadığını görün.

## Bu Kitap Kimler İçin

Bu kitap, başka bir programlama dilinde kod yazdığınızı varsayar. Kitabı çok çeşitli programlama geçmişlerinden gelenler için geniş çapta 
erişilebilir hale getirmeye çalıştık. Programlamanın ne olduğu veya onun hakkında nasıl düşünüleceği hakkında konuşmak için çok zaman harcamıyoruz. Programlama konusunda tamamen yeniyseniz, özellikle programlamaya giriş sağlayan bir kitap okuyarak daha fazla bilgi alarak bu serüvene atılabilirsiniz.

## Kitabı Nasıl Kullanmalı

Genel olarak, bu kitap önden arkaya sırayla okuduğunuzu varsayarak anlatır. Sonraki bölümler, önceki bölümlerdeki kavramların üzerine inşa edilmiştir ve önceki bölümler bir konunun ayrıntılarına girmeyebilir; Konuyu genellikle daha sonraki bir bölümde tekrar ele alırız.

Bu kitapta iki tür bölüm bulacaksınız: kavram bölümleri ve proje bölümleri. Konsept bölümlerinde Rust'ın farklı bir yönü hakkında bilgi edineceksiniz. Proje bölümlerinde, şimdiye kadar öğrendiklerinizi uygulayarak birlikte küçük programlar oluşturacağız. Bölüm 2, 12 ve 20 proje bölümleridir; geri kalanı kavram bölümleridir.

Bölüm 1, Rust'ın nasıl kurulacağını, “Hello, World!”'ün nasıl yazılacağını, Rust'ın paket yöneticisi ve oluşturma aracı olan Cargo'nun nasıl kullanılacağını açıklar. 2. Bölüm, Rust diline uygulamalı bir giriştir. Burada kavramları yüksek düzeyde ele alıyoruz. Eğer kavramlarla kafanız karışmışsa sonraki bölümlerdeki ek ayrıntılar size ek bilgiler verecektir. Ellerinizi hemen kirletmek istiyorsanız, bunun yeri Bölüm 2'dir. İlk başta, diğer programlama dillerine benzer Rust özelliklerini kapsayan Bölüm 3'ü atlayabilir ve Rust'ın sahiplik sistemi hakkında bilgi edinmek için doğrudan Bölüm 4'e gidebilirsiniz. Ancak, bir sonrakine geçmeden önce her ayrıntıyı öğrenmeyi tercih eden özellikle titiz bir öğrenciyseniz, Bölüm 2'yi atlayıp doğrudan Bölüm 3'e geçebilir, bir konu üzerinde çalışmak istediğinizde Bölüm 2'ye dönebilirsiniz. öğrendiğiniz detayları uygulayarak projelendirmeyi unutmayın.

Bölüm 5 yapıları ve yöntemleri tartışır ve Bölüm 6 numaralandırmaları, eşleşme ifadelerini ve `if let` kontrol akışı yapısını kapsar. Rust'ta özel türler oluşturmak için yapıları ve numaralandırmaları kullanacaksınız.

Bölüm 7'de, Rust'ın modül sistemi ve kodunuzu ve onun genel Uygulama Programlama Arayüzü'nü (API) düzenlemek için gereken gizlilik kuralları hakkında bilgi edineceksiniz. Bölüm 8, vektörler, diziler ve karma haritalar (hash map) gibi standart kitaplığın sağladığı bazı ortak veri toplama yapılarını tartışır. 9. Bölüm, Rust'ın hata işleme felsefesini ve tekniklerini anlatır.

Bölüm 10, size birden çok tür için geçerli olan kodu tanımlama gücü veren yaygın türler, özellikler ve ömürlükleri inceler. Bölüm 11, programınızın mantığının doğru olduğundan emin olmak için Rust'ın güvenlik garantileriyle bile gerekli olan testlerle ilgilidir. 12. Bölümde, dosyalar içinde metin arayan "grep" komut satırı aracının yaptığıyla benzer olarak kendi uygulamamızı oluşturacağız. Bunun için önceki bölümlerde tartıştığımız kavramların birçoğunu kullanacağız.

Bölüm 13, kapanış ifadelerini ve yineleyicileri anlatıyor: işlevsel programlama dillerinden gelen Rust özellikleri. 14. Bölümde, Cargo'yu daha derinlemesine inceleyeceğiz ve kitaplıklarınızı başkalarıyla paylaşmak için kullanılabilecek en iyi tekniklerden bahsedeceğiz. Bölüm 15'te, standart kütüphanenin sağladığı akıllı işaretçileri ve bunların işlevselliğini sağlayan özellikleri tartışacağız.

Bölüm 16'da, farklı eşzamanlı programlama modellerini inceleyeceğiz ve Rust'ın birden çok iş parçacığında korkusuzca programlamanıza nasıl yardımcı olduğu hakkında konuşacağız. Bölüm 17, Rust deyimlerinin aşina olabileceğiniz nesne yönelimli programlama ilkeleriyle nasıl karşılaştırıldığını inceler.

Bölüm 18, Rust programları boyunca fikirleri ifade etmenin güçlü yolları olan kalıplar ve kalıp eşleştirme hakkında bir referanstır. Bölüm 19, güvenli olmayan Rust, makrolar ve ömürlükler, tanımlar, türler, fonksiyonlar ve kapanış türleri hakkında daha fazlası dahil olmak üzere ileri düzey ilgi çekici konulardan oluşan bir *İsveç masasını* içerir.

Bölüm 20'de, düşük seviyeli çok iş parçacıklı bir web sunucusu uygulayacağımız bir projeyi tamamlayacağız!

Son olarak, Ekleme A, Rust'ın anahtar sözcüklerini, Ekleme B, Rust'ın operatörlerini ve sembollerini kapsar, Ekleme C, standart kütüphane tarafından sağlanan türevlenebilir özellikleri kapsar, Ekleme D, bazı yararlı geliştirme araçlarını kapsar ve Ekleme E, Rust sürümlerini açıklar.

Bu kitabı okumanın yanlış bir yolu yok: Nasıl ilerlemek istiyorsanız, devam edin! Herhangi bir karışıklık yaşarsanız, önceki bölümlere geri dönmeniz gerekebilir. Ama bu kitabı istediğin gibi kullanabilirsin, yapabileceğinin en iyisini yap!

<span id="ferris"></span>


Rust öğrenme sürecinin önemli bir parçası, derleyicinin görüntülediği hata mesajlarının nasıl okunacağını öğrenmektir: bunlar sizi çalışma koduna yönlendirecektir. Bu nedenle, derleyicinin her durumda size göstereceği hata mesajıyla birlikte derlenmeyen birçok örnek sunacağız. Rastgele bir örnek girip çalıştırırsanız derlenmeyebileceğini bilin! Çalıştırmaya çalıştığınız örneğin hata amaçlı olup olmadığını görmek için çevreleyen metni okuduğunuzdan emin olun. Ferris, çalışması mümkün olmayan ya da düzgün çalışmayan kodları ayırt etmenize de yardımcı olacaktır:

| Ferris                                                                                                           | Anlamı                                          |
|------------------------------------------------------------------------------------------------------------------|--------------------------------------------------|
| <img src="img/ferris/does_not_compile.svg" class="ferris-explain" alt="Ferris with a question mark"/>            | Bu kod derlenmiyor!                      |
| <img src="img/ferris/panics.svg" class="ferris-explain" alt="Ferris throwing up their hands"/>                   | Bu kod paniğe sahip!                                |
| <img src="img/ferris/not_desired_behavior.svg" class="ferris-explain" alt="Ferris with one claw up, shrugging"/> | Bu kod belirtilen davranışı sergilemiyor. |

Çoğu durumda, sizi derlenmeyen herhangi bir kodun derlenen doğru sürümüne yönlendireceğiz.

## Kaynak Kod

Bu kitabın oluşturulduğu kaynak dosyalar şu adreste bulunabilir: [GitHub, Rust deposu][book].
Türkçeye çevrilmiş kaynak dosyaları şu adreste bulunabilir: [GitHub, ferhatgec deposu][kitap].

[book]: https://github.com/rust-lang/book/tree/main/src
[kitap]: https://github.com/ferhatgec/kitap/tree/patch-1/src
