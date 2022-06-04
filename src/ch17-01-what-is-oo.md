## Nesne Yönelimli Dillerin Karakteristik Özellikleri

Programlama topluluğunda, bir dilin nesne yönelimli olarak kabul 
edilmesi için hangi özelliklerin olması gerektiği konusunda bir fikir birliği yoktur. Rust, NYP (OOP) dahil olmak üzere birçok programlama paradigmasından etkilenir; 
örneğin, Bölüm 13'te işlevsel programlamadan gelen özellikleri araştırdık. 
Muhtemelen, NYP dilleri nesneler, kapsülleme ve kalıtım gibi belirli ortak özellikleri paylaşır. 
Bu özelliklerin her birinin ne anlama geldiğine ve Rust'ın bunu destekleyip desteklemediğine bakalım.

### Nesneler Veri ve Davranış İçeriyor

Erich Gamma, Richard Helm, Ralph Johnson ve John Vlissides (Addison-Wesley Professional, 1994) 
tarafından yazılan *Design Patterns: Elements of Reusable Object-Oriented Software* kitabı (Addison-Wesley Professional, 1994), 
halk arasında *The Gang of Four* kitabı olarak anılır, bir nesne kataloğudur. Bu kitap, NYP'ı şu şekilde tanımlar: 

> Nesne yönelimli programlar nesnelerden oluşur.
> Bir nesne hem verileri hem de bu veriler üzerinde çalışan prosedürleri paketler.
> Prosedürlere tipik olarak *yöntemler* veya *işlemler* denir. 

Bu tanımı kullanırsak, Rust nesne yönelimlidir: yapılar ve numaralandırılmışlar verilere sahiptir ve `impl` blokları, 
yapılar ve numaralandırılmışlar üzerinde metodlar sağlar. Metodları olan yapılar ve numaralandırılmışlar nesne olarak adlandırılmasa da, 
The Gang of Four'un nesne tanımına göre aynı işlevselliği sağlarlar.

### Sürekleme Ayrıntılarını Gizleyen Kapsülleme

NYP ile yaygın olarak ilişkilendirilen başka bir yön, *kapsülleme* fikridir; 
bu, bir nesnenin uygulama ayrıntılarına o nesneyi kullanarak erişemeyeceği anlamına gelir. 
Bu nedenle, bir nesneyle etkileşim kurmanın tek yolu, onun genel API'sidir; nesneyi kullanan kod, nesnenin iç kısımlarına erişememeli ve verileri veya davranışı doğrudan değiştirememelidir. Bu, programcının, nesneyi kullanan kodu değiştirmeye gerek kalmadan 
bir nesnenin içindekileri değiştirmesini ve yeniden düzenlemesini sağlar.

Kapsüllemenin nasıl kontrol edileceğini Bölüm 7'de tartıştık: 
kodumuzdaki hangi modüllerin, türlerin, fonksiyonların ve yöntemlerin genel olacağına karar vermek için `pub` anahtar sözcüğünü kullanabiliriz ve varsayılan olarak diğer her şey `private` olur. Örneğin, bir `i32` vektörü bir `AveragedCollection` yapısı tanımlayabiliriz. 
Yapı, vektördeki değerlerin ortalamasını içeren bir alana da sahip olabilir; bu da, herhangi birinin ihtiyaç duyduğunda ortalamanın talep üzerine hesaplanması gerekmediği anlamına gelir. Başka bir deyişle, `AveragedCollection` hesaplanan ortalamayı bizim için önbelleğe alacaktır. Liste 17-1, `AveragedCollection` yapısını tanımlar:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-01/src/lib.rs}}
```

<span class="caption">Liste 17-1: `AveragedCollection` yapısı koleksiyondaki öğelerin tamsayılarının ve ortalamalarının bir listesini tutar</span>

Yapı, diğer kodların kullanabilmesi için `pub` olarak işaretlenir, ancak yapı içindeki alanlar özel kalır. 
Bu, bu durumda önemlidir, çünkü listeye bir değer eklendiğinde veya listeden çıkarıldığında, ortalamanın da güncellenmesini sağlamak istiyoruz. 
Bunu, Liste 17-2'de gösterildiği gibi, yapıya ekleme, kaldırma ve ortalama fonksiyonlarını uygulayarak yapıyoruz:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-02/src/lib.rs:here}}
```

<span class="caption">Liste 17-2: Ortalama toplama, kaldırma ve ortalamaya ilişkin genel fonksiyonların süreklenmesi</span>

Genel fonksiyonlar olan ekleme (`add`), kaldırma (`remove`) ve ortalama (`average`), 
bir `AveragedCollection` örneğindeki verilere erişmenin veya verileri değiştirmenin tek yoludur. 
Bir öğe, ekleme yöntemi kullanılarak listeye eklendiğinde veya kaldır yöntemi kullanılarak kaldırıldığında, 
her birinin süreklemeleri, ortalama alanı güncellemeyi de işleyen özel `update_average` metodunu çağırır. 

`list`'i ve `average` alanlarını özel olarak tutuyoruz, bu nedenle harici kodun liste alanına doğrudan öğe eklemesi veya liste alanından öğe kaldırması mümkün değildir; aksi takdirde, liste değiştiğinde `average` alanı senkronize olmayabilir. `average` metodu, `avetage` alanındaki değeri döndürür ve harici kodun ortalamayı okumasına ancak değiştirmemesine izin verir. `AveragedCollection` yapısının sürekleme detaylarını kapsüllediğimiz için, 
gelecekte veri yapısı gibi yönlerini kolayca değiştirebiliriz. 

Örneğin, `list` alanı için `Vec<i32>` yerine `HashSet<i32>` kullanabiliriz. 
Ekleme, kaldırma ve ortalama genel metodlarının imzaları aynı kaldığı sürece, `AveragedCollection` kullanan kodun değişmesi gerekmez. 
Bunun yerine `list`'i `public` olarak işleseydik, durum böyle olmazdı: `HashSet<i32>` ve `Vec<i32>` öğeleri eklemek ve kaldırmak için farklı yöntemlere sahipti, bu nedenle, listeyi doğrudan değiştiriyorsa harici kodun büyük olasılıkla değişmesi gerekirdi. 
Bir dilin nesne yönelimli olarak kabul edilmesi için kapsülleme gerekli bir özellikse, Rust bu gereksinimi karşılar. 
Kodun farklı bölümleri için `pub` kullanma veya kullanmama seçeneği, uygulama ayrıntılarının kapsüllenmesini sağlar.

### Tür Sistemi ve Kod Paylaşımı Olarak Kalıtım

*Kalıtım*, bir nesnenin başka bir nesnenin tanımından öğeleri devralabileceği, böylece onları yeniden tanımlamanıza gerek kalmadan üst nesnenin verilerini ve davranışını kazanabileceği bir mekanizmadır.

Bir dilin nesne yönelimli bir dil olması için kalıtıma sahip olması gerekiyorsa, 
Rust bunlardan birisi değildir. Üst yapının alanlarını ve fonksiyon süreklemelerini devralan bir yapı tanımlamanın bir yolu yoktur. 
Bununla birlikte, programlama araç kutunuzdan kalıtım almaya alıştıysanız, ilk etapta kalıtım için ulaşma nedeninize bağlı olarak Rust'taki diğer çözümleri kullanabilirsiniz.

İki ana nedenden dolayı kalıtımı seçersiniz. Biri kodun yeniden kullanımı içindir: bir tür için belirli davranışı uygulayabilirsiniz ve kalıtım, bu uygulamayı farklı bir tür için yeniden kullanmanızı sağlar. Rust kodunu, `Summary` tanımındaki `summarize` metodunun varsayılan bir uygulamasını eklediğimizde Liste 10-14'te gördüğünüz gibi, varsayılan tanım fonksiyon süreklemelerini kullanarak paylaşabilirsiniz. 

`Summary` tanımını sürekleyen herhangi bir tür, üzerinde başka bir kod olmadan `summarize` metoduna da sahip olacaktır. 
Bu, bir metodun süreklemesine sahip bir üst sınıfa ve aynı zamanda yöntemin uygulanmasına sahip olan miras alan bir alt sınıfa benzer. 
Ayrıca, bir üst sınıftan miras alınan bir metodun uygulanmasını geçersiz kılan bir alt sınıfa benzer olan `Summary` tanımını 
süreklediğimizde, `summarize` metodunun varsayılan uygulamasını da geçersiz kılabiliriz.

Kalıtımı kullanmanın diğer nedeni, tür sistemiyle ilgilidir: bir alt türün üst türle aynı yerlerde kullanılmasını sağlamaktır. 
Buna *polimorfizm*, çok biçimlilik de denir; bu, belirli karakteristik özellikleri paylaşıyorlarsa çalışma zamanında birden 
çok nesneyi birbirinin yerine koyabileceğiniz anlamına gelir.

> ### Çok Biçimlilik
> Birçok insan için çok biçimlilik kalıtımla eş anlamlıdır. 
> Aslında, birden çok türdeki verilerle çalışabilen kodu ifade eden daha genel bir kavramdır. 
> Kalıtım için bu türler genellikle alt sınıflardır.
> Bunun yerine Rust, farklı olası türler üzerinde soyutlamak için yaygın türleri ve bu türlerin sağlaması gerekenlere 
> kısıtlamalar getirmek için özellik sınırlarını kullanır. Buna bazen sınırlı *parametrik çok biçimlilik* de denir.


Kalıtım, son zamanlarda birçok programlama dilinde bir programlama tasarım çözümü olarak gözden düştü çünkü genellikle gereğinden 
fazla kod paylaşma riskini taşıyor. Alt sınıflar her zaman üst sınıflarının tüm özelliklerini paylaşmamalıdır, ancak bunu 
kalıtımı kullanırsalar yapacaklardır. Bu, bir programın tasarımını daha az esnek hale getirebilir. Ayrıca, mantıklı olmayan 
veya metodların alt sınıfa süreklenmediği durumlar için hatalara neden olan alt sınıflardaki metodları çağırma olasılığını da sunar. 
Ek olarak, bazı diller bir alt sınıfın yalnızca bir sınıftan miras almasına izin vererek program tasarımının esnekliğini daha da kısıtlar.

Bu nedenlerle Rust, kalıtım yerine tanım nesnelerini kullanma konusunda farklı bir yaklaşım benimser. 
Tanım nesnelerinin Rust'ta çok biçimliliği nasıl sağladığına bakalım.
