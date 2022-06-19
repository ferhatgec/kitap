## `panic!`'lemek mi, `panic!`'lememek mi?

Peki ne zaman panik yapacağınıza ve ne zaman `Result`'a geri döneceğinize nasıl karar vereceksiniz? 
Kod panik yaptığında, kurtarmanın bir yolu yoktur. Kurtarmanın olası bir yolu olsun ya da olmasın, herhangi bir hata durumu için 
`panic!` çağrısı yapabilirsiniz, ancak o zaman çağıran kod adına bir durumun kurtarılamaz olduğuna karar vermiş olursunuz. 
Bir `Result` değeri döndürmeyi seçtiğinizde, çağıran koda seçenekler sunarsınız. Çağıran kod, kendi durumuna uygun bir şekilde kurtarma 
girişiminde bulunmayı seçebilir veya bu durumda bir `Err` değerinin kurtarılamaz olduğuna karar verebilir, böylece `panic!` çağrısı yapabilir 
ve kurtarılabilir hatanızı kurtarılamaz bir hataya dönüştürebilir. Bu nedenle, başarısız olabilecek bir fonksiyon tanımlarken `Result` döndürmek 
iyi bir varsayılan seçimdir.

Örnekler, prototip kodu ve testler gibi durumlarda, `Result` döndürmek yerine panikleyen kod yazmak daha uygundur. 
Nedenini inceleyelim, ardından derleyicinin başarısızlığın imkansız olduğunu söyleyemediği, ancak insan olarak sizin söyleyebildiğiniz 
durumları tartışalım. Bölüm, kütüphane kodunda panik yapıp yapmamaya nasıl karar verileceğine ilişkin bazı genel yönergelerle sona erecektir.

### Örnekler, Prototip Kod ve Testler

Bir kavramı açıklamak için bir örnek yazarken, sağlam hata işleme kodu da eklemek örneği daha az anlaşılır hale getirebilir. 
Örneklerde, `unwrap` gibi panik yaratabilecek bir metoda yapılan çağrının, uygulamanızın hataları nasıl ele almasını istediğinize yönelik 
bir yer tutucu olduğu anlaşılır; bu da kodunuzun geri kalanının ne yaptığına bağlı olarak farklılık gösterebilir.

Benzer şekilde, `unwrap` ve `expect` metodları, hataları nasıl ele alacağınıza karar vermeye hazır olmadan önce prototip 
oluştururken çok kullanışlıdır. Programınızı daha sağlam hale getirmeye hazır olduğunuzda kodunuzda net işaretler bırakırlar.

Bir testte bir metod çağrısı başarısız olursa, bu yöntem test edilen fonksiyon olmasa bile tüm testin başarısız olmasını istersiniz. 
`panic!` bir testin başarısız olarak işaretlenme şekli olduğundan, `unwrap` veya `expect` çağrısı tam olarak olması gereken şeydir.

### Derleyiciden Daha Fazla Bilgiye Sahip Olduğunuz Durumlar

Ayrıca, `Result`'un `Ok` değerine sahip olmasını sağlayan başka bir mantığınız olduğunda `unwrap` veya `expect` çağrısı 
yapmak da uygun olacaktır, ancak bu mantık derleyicinin anlayabileceği bir şey değildir. Hala işlemeniz gereken bir `Result` değeriniz olacaktır:
çağırdığınız işlem, sizin özel durumunuzda mantıksal olarak imkansız olsa bile, genel olarak başarısız olma olasılığına sahiptir. 
Kodu manuel olarak inceleyerek hiçbir zaman bir `Err` varyantına sahip olmayacağınızdan emin olabiliyorsanız, 
`unwrap`'i çağırmak tamamen kabul edilebilir ve hatta `expect` metninde hiçbir zaman bir `Err` varyantına sahip olmayacağınızı düşünmenizin 
nedenini belgelemek daha iyidir. İşte bir örnek:

```rust
{{#rustdoc_include ../listings/ch09-error-handling/no-listing-08-unwrap-that-cant-fail/src/main.rs:here}}
```

Kodlanmış bir dizeyi ayrıştırarak bir `IpAddr` örneği oluşturuyoruz. `127.0.0.1`'in geçerli bir IP adresi olduğunu görebiliyoruz, 
bu nedenle burada `expect` kullanmak kabul edilebilir. Ancak, sabit kodlu, geçerli bir dizeye sahip olmak, `parse` metodunun dönüş türünü 
değiştirmez: hala bir `Result` değeri alırız ve derleyici, bu dizginin her zaman geçerli bir IP adresi olduğunu görecek kadar akıllı olmadığından, 
`Err` varyantı bir olasılıkmış gibi `Result`'u işlememizi sağlar. IP adresi dizgisi programa kodlanmak yerine bir kullanıcıdan gelseydi ve 
bu nedenle hata olasılığı olsaydı, bunun yerine `Result`'u kesinlikle daha sağlam bir şekilde ele almak isterdik. 
Bu IP adresinin sabit kodlu olduğu varsayımından bahsetmek, gelecekte IP adresini başka bir kaynaktan almamız gerekirse, 
daha iyi hata işleme kodu beklentisini değiştirmemizi sağlayacaktır.

### Hata İşleme Yönergeleri

Kodunuzun kötü bir duruma düşme olasılığı olduğunda kodunuzun paniğe kapılması tavsiye edilir. Bu bağlamda kötü durum, geçersiz değerler, 
çelişkili değerler veya eksik değerlerin kodunuza aktarılması gibi bazı varsayımların, garantilerin, sözleşmelerin veya değişmezlerin 
ihlal edilmesi ve ayrıca aşağıdakilerden bir veya daha fazlasının gerçekleşmesi durumudur:

* Kötü durum, kullanıcının yanlış formatta veri girmesi gibi ara sıra meydana gelebilecek bir durumun aksine beklenmedik bir durumdur.
  Bu noktadan sonra kodunuzun her adımda sorunu kontrol etmek yerine bu kötü durumda olmamaya güvenmesi gerekir.
  Kullandığınız türlerde bu bilgiyi kodlamanın iyi bir yolu yoktur. Bölüm 17'deki 
  [“Durumları ve Davranışları Türler Olarak Kodlama”][encoding]<!-- ignore --> kısmında ne demek istediğimizi bir örnekle açıklayacağız.

* Birisi kodunuzu çağırır ve mantıklı olmayan değerler girerse, yapabiliyorsanız bir hata döndürmek en iyisidir, 
 böylece kütüphane kullanıcısı bu durumda ne yapmak istediğine karar verebilir. Ancak, devam etmenin güvensiz veya zararlı olabileceği durumlarda, 
 en iyi seçim `panic!` çağrısı yapmak ve kütüphanenizi kullanan kişiyi kodlarındaki hata konusunda uyarmak olabilir, böylece geliştirme 
 sırasında düzeltebilirler. Benzer şekilde, kontrolünüz dışında olan harici bir kodu çağırıyorsanız ve bu kod düzeltme imkanınızın olmadığı geçersiz 
 bir durum döndürüyorsa `panic!` çağrısı yapılmalıdır.
 
* Başarısızlık beklendiğinde, `panic!` çağrısı yapmaktansa bir `Result` döndürmek daha uygundur. Örnekler arasında, hatalı biçimlendirilmiş
 verilerin verildiği bir ayrıştırıcı veya bir hız sınırına ulaştığınızı gösteren bir durum döndüren bir HTTP isteği yer alır. 
 Bu durumlarda, `Result` döndürmek, başarısızlığın, çağıran kodun nasıl ele alacağına karar vermesi gereken beklenen bir olasılık olduğunu gösterir.

Kodunuz, geçersiz değerler kullanılarak çağrıldığında kullanıcıyı riske atabilecek bir işlem gerçekleştirdiğinde, 
kodunuz önce değerlerin geçerli olduğunu doğrulamalı ve değerler geçerli değilse paniklemelidir. 
Bu çoğunlukla güvenlik nedenleriyle yapılır: geçersiz veriler üzerinde işlem yapmaya çalışmak kodunuzu güvenlik açıklarına maruz bırakabilir. 
Sınır dışı bir bellek erişimi denediğinizde standart kütüphanenin `panic!` çağrısı yapmasının ana nedeni budur: mevcut veri yapısına ait 
olmayan belleğe erişmeye çalışmak yaygın bir güvenlik sorunudur. Fonksiyonların genellikle sözleşmeleri vardır: 
davranışları yalnızca girdilerin belirli gereksinimleri karşılaması durumunda garanti edilir. Sözleşme ihlal edildiğinde paniklemek 
mantıklıdır çünkü bir sözleşme ihlali her zaman çağıran tarafında bir hata olduğunu gösterir ve çağıran kodun açıkça ele almasını istediğiniz bir 
hata türü değildir. Aslında, çağıran kodun hatayı telafi etmesinin makul bir yolu yoktur; çağıran programcıların kodu düzeltmesi gerekir. 
Bir fonksiyon için sözleşmeler, özellikle de bir ihlal paniğe neden olacaksa, işlevin API belgelerinde açıklanmalıdır.

Ancak, tüm fonksiyonlarınızda çok sayıda hata kontrolü olması ayrıntılı ve can sıkıcı olacaktır. Neyse ki, Rust'ın tür sistemini 
(ve dolayısıyla derleyici tarafından yapılan tür kontrolünü) kontrollerin çoğunu sizin için yapmak için kullanabilirsiniz. Fonksiyonunuz parametre 
olarak belirli bir türe sahipse, derleyicinin zaten geçerli bir değere sahip olduğunuzdan emin olduğunu bilerek kodunuzun mantığına devam 
edebilirsiniz. Örneğin, bir `Option` yerine bir türünüz varsa, programınız hiçbir şey yerine bir şey olmasını bekler. 
Bu durumda kodunuz `Some` ve `None` varyantları için iki durumla uğraşmak zorunda kalmaz: kesinlikle bir değere sahip olmak için 
yalnızca bir durum olacaktır. Fonksiyonunuza hiçbir şey iletmemeye çalışan kod derlenmez bile, bu nedenle fonksiyonunuzun çalışma zamanında bu 
durumu kontrol etmesi gerekmez. Başka bir örnek de `u32` gibi işaretsiz bir tam sayı türü kullanmaktır, bu da parametrenin asla negatif 
olmamasını sağlar.

### Doğrulama için Özel Tipler Oluşturma

Geçerli bir değere sahip olduğumuzdan emin olmak için Rust'ın tür sistemini kullanma fikrini bir adım daha ileri götürelim 
ve doğrulama için özel bir tür oluşturmaya bakalım. Bölüm 2'de kodumuzun kullanıcıdan 1 ile 100 arasında bir sayı tahmin etmesini 
istediği tahmin oyununu hatırlayın. Gizli sayımızla karşılaştırmadan önce kullanıcının tahmininin bu sayılar arasında olduğunu 
doğrulamadık; yalnızca tahminin pozitif olduğunu doğruladık. Bu durumda, sonuçlar çok vahim değildi: “Too high” veya "Too low" 
çıktılarımız yine de doğru olacaktı. Ancak, kullanıcıyı geçerli tahminlere yönlendirmek ve bir kullanıcı aralık dışında bir sayı tahmin 
ettiğinde, bunun yerine örneğin harfler yazdığında farklı bir davranışa sahip olmak yararlı bir geliştirme olacaktır.

Bunu yapmanın bir yolu, potansiyel olarak negatif sayılara izin vermek için tahmini yalnızca bir `u32` yerine bir `i32` olarak ayrıştırmak ve 
ardından sayının aralıkta olup olmadığına dair bir kontrol eklemek olabilir:

```rust,ignore
{{#rustdoc_include ../listings/ch09-error-handling/no-listing-09-guess-out-of-range/src/main.rs:here}}
```

`if` ifadesi, değerimizin aralık dışında olup olmadığını kontrol eder, kullanıcıya sorun hakkında bilgi verir ve döngünün 
bir sonraki yinelemesini başlatmak ve başka bir tahmin istemek için `continue` çağrısı yapar. `if` ifadesinden sonra, 
tahminin `1` ile `100` arasında olduğunu bilerek tahmin ile gizli sayı arasındaki karşılaştırmalara devam edebiliriz.

Ancak, bu ideal bir çözüm değildir: programın yalnızca `1` ile `100` arasındaki değerler üzerinde çalışması kesinlikle kritikse ve 
bu gereksinime sahip birçok fonksiyonu varsa, her işlevde bunun gibi bir kontrol yapmak sıkıcı olacaktır (ve performansı etkileyebilir).

Bunun yerine, yeni bir tür oluşturabilir ve doğrulamaları her yerde tekrarlamak yerine türün bir örneğini oluşturmak için doğrulamaları 
bir fonksiyona koyabiliriz. Bu şekilde, fonksiyonların imzalarında yeni türü kullanmaları ve aldıkları değerleri güvenle kullanmaları 
güvenli olur. Liste 9-13, yalnızca yeni fonksiyon `1` ile `100` arasında bir değer alırsa `Guess`'in bir örneğini oluşturacak bir 
`Guess` türü tanımlamanın bir yolunu göstermektedir.

<!-- Deliberately not using rustdoc_include here; the `main` function in the
file requires the `rand` crate. We do want to include it for reader
experimentation purposes, but don't want to include it for rustdoc testing
purposes. -->

```rust
{{#include ../listings/ch09-error-handling/listing-09-13/src/main.rs:here}}
```

<span class="caption">Liste 9-13: Yalnızca `1` ile `100` arasındaki değerlerle devam edecek bir `Guess` 
türü</span>

İlk olarak, bir `i32` tutan `value` adlı bir üyeye sahip `Guess` adlı bir yapı tanımlarız. Burası sayının saklanacağı yerdir.

Ardından, `Guess` değerlerinin örneklerini oluşturan `Guess` üzerinde new adında ilişkili bir fonksiyon uyguluyoruz. 
`new` fonksiyonu, `i32` türünde `value` adında bir parametreye sahip olacak ve bir `Guess` değeri döndürecek şekilde tanımlanır. 
`new` fonksiyonunun gövdesindeki kod, `1` ile `100` arasında olduğundan emin olmak için değeri test eder. Eğer `value` bu testi geçemezse, 
çağıran kodu yazan programcıyı düzeltmesi gereken bir hata olduğu konusunda uyaracak bir `panic!` çağrısı yaparız, 
çünkü bu aralığın dışında bir değere sahip bir `Guess` oluşturmak `Guess::new`'in dayandığı sözleşmeyi ihlal edecektir. 
`Guess::new`'in paniğe kapılabileceği koşullar kamuya açık API dokümantasyonunda tartışılmalıdır; 
Bölüm 14'te oluşturacağınız API dokümantasyonunda `panic` olasılığını belirten dokümantasyon kurallarını ele alacağız. 
Eğer `value` testi geçerse, value alanı value parametresine ayarlanmış yeni bir `Guess` yaratırız ve `Guess`'i döndürürüz.

Ardından, `self` öğesini ödünç alan, başka parametresi olmayan ve bir `i32` döndüren `value` adlı bir metod yazarız. 
Bu tür yöntemlere bazen *getter* adı verilir, çünkü amacı alanlarından bazı verileri almak ve döndürmektir. 
`Guess` yapısının `value` üyesi gizli olduğu için burada yaygın metod gereklidir. Değer üyesinin gizli olması önemlidir, 
böylece `Guess` yapısını kullanan kodun değeri doğrudan ayarlamasına izin verilmez: modül dışındaki kod, bir `Guess` tanımı oluşturmak 
için `Guess::new` fonksiyonunu kullanmalıdır, böylece `Guess`'in `Guess::new` fonksiyonundaki koşullar tarafından kontrol edilmemiş 
bir değere sahip olmasının hiçbir yolu yoktur.

Parametresi olan veya yalnızca `1` ile `100` arasındaki sayıları döndüren bir fonksiyon, imzasında bir `i32` yerine bir `Guess` aldığını 
veya döndürdüğünü bildirebilir ve gövdesinde herhangi bir ek kontrol yapması gerekmez.

## Özet

Rust'ın hata işleme özellikleri, daha sağlam kod yazmanıza yardımcı olmak için tasarlanmıştır. 
`panic!` makrosu, programınızın üstesinden gelemeyeceği bir durumda olduğunu bildirir ve geçersiz veya yanlış değerlerle devam 
etmeye çalışmak yerine sürece durmasını söylemenizi sağlar. `Result` `enum`'u, işlemlerin kodunuzun kurtarabileceği bir şekilde başarısız 
olabileceğini belirtmek için Rust'ın tür sistemini kullanır. `Result`'u, kodunuzu çağıran koda olası başarı veya başarısızlığı da 
ele alması gerektiğini söylemek için kullanabilirsiniz. `panic!` ve `Result`'u uygun durumlarda kullanmak, kaçınılmaz sorunlar karşısında 
kodunuzu daha güvenilir hale getirecektir.

Standart kütüphanenin `Option` ve `Result` `enum`'ları ile yaygınları nasıl kullandığını gördüğünüze göre, 
yaygınların nasıl çalıştığından ve bunları kodunuzda nasıl kullanabileceğinizden bahsedeceğiz.

[encoding]: ch17-03-oo-design-patterns.html#encoding-states-and-behavior-as-types
