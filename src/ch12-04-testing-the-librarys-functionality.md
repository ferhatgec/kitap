## Test Odaklı Geliştirme ile Kütüphanenin İşlevselliğini Geliştirme

Artık kök mantığı *src/lib.rs*'ye çıkardığımıza ve argüman toplama ve hata işlemeyi *src/main.rs*'de bıraktığımıza göre, 
kodumuzun temel işlevselliği için test yazmak çok daha kolay. Fonksiyonları çeşitli argümanlarla doğrudan çağırabilir ve 
ikili dosyamızı komut satırından çağırmak zorunda kalmadan dönüş değerlerini kontrol edebiliriz.

Bu bölümde, aşağıdaki adımlarla test odaklı geliştirme (TDD) sürecini kullanarak `minigrep` programına arama mantığını ekleyeceğiz:

1. Başarısız olan bir test yazın ve beklediğiniz nedenden dolayı başarısız olduğundan emin olmak için çalıştırın.
2. Yeni testin geçmesi için yeterli kodu yazın veya değiştirin.
3. Yeni eklediğiniz veya değiştirdiğiniz kodu yeniden düzenleyin ve testlerin geçmeye devam ettiğinden emin olun.
4. Adım 1'den itibaren tekrarlayın!

TDD, yazılım yazmanın birçok yolundan yalnızca biri olsa da kod tasarımını yönlendirmeye yardımcı olabilir. 
Testin geçmesini sağlayan kodu yazmadan önce testi yazmak, süreç boyunca yüksek test kapsamının korunmasına yardımcı olur.

Dosya içeriğindeki sorgu dizgisini gerçekten arayacak ve sorguyla eşleşen satırların bir listesini üretecek işlevselliğin uygulanmasını test edeceğiz. 
Bu işlevselliği `search` adlı bir fonksiyona ekleyeceğiz.

### Başarısız Bir Test Yazma

Artık onlara ihtiyacımız olmadığından, programın davranışını kontrol etmek için kullandığımız `println!` ifade yapılarını 
*src/lib.rs* ve *src/main.rs* dosyalarından kaldıralım. Daha sonra, *src/lib.rs* dosyasına, Bölüm 11'de yaptığımız gibi bir test 
fonksiyonu içeren bir `tests` modülü ekleyelim. Test fonksiyonu, `search` fonksiyonunun sahip olmasını istediğimiz davranışı belirtir: 
bir sorgu ve aranacak metni alır ve metinden yalnızca sorguyu içeren satırları döndürür. Liste 12-15, henüz derlenmeyecek olan bu 
testi göstermektedir.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-15/src/lib.rs:here}}
```

<span class="caption">Liste 12-15: Keşke yapsaydık dediğimiz `search` fonksiyonu için başarısız bir test oluşturma</span>

Bu test `"duct"` dizgisini arar. Aradığımız metin, yalnızca biri `"duct"` içeren üç satırdır (Açılıştaki çift tırnak işaretinden sonraki 
ters eğik çizginin Rust'a bu dize değişmezinin içeriğinin başına yeni satır karakteri koymamasını söylediğine dikkat edin). 
Arama fonksiyonundan dönen değerin sadece beklediğimiz satırı içerdiğini iddia ediyoruz.

Henüz bu testi çalıştırıp başarısız olmasını izleyemiyoruz çünkü test derlenmiyor bile: arama fonksiyonu henüz mevcut değil! 
TDD ilkelerine uygun olarak, Liste 12-16'da gösterildiği gibi her zaman boş bir vektör döndüren bir arama fonksiyonu tanımı ekleyerek 
testin derlenmesini ve çalışmasını sağlayacak kadar kod ekleyeceğiz. Ardından test derlenmeli ve başarısız olmalıdır çünkü boş bir vektör 
`"safe, fast, productive"` satırını içeren bir vektörle eşleşmez.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-16/src/lib.rs:here}}
```

<span class="caption">Liste 12-16: Testimizin derlenebilmesi için `search` fonksiyonunun değiştirilmesi</span>

`search`'ın imzasında açık bir `'a` yaşam süresi tanımlamamız ve bu yaşam süresini `contents` argümanı ve dönüş değeri ile kullanmamız 
gerektiğine dikkat edin. [Bölüm 10][ch10-lifetimes]<!-- ignore -->'da yaşam süresi parametrelerinin hangi argüman yaşam süresinin 
geri dönüş değerinin yaşam süresine bağlı olduğunu belirttiğini hatırlayın. Bu durumda, döndürülen vektörün (argüman sorgusu yerine) 
argüman `contents`'in dilimlerine referans veren dizgi dilimleri içermesi gerektiğini belirtiriz.

Başka bir deyişle, Rust'a `search` fonksiyonu tarafından döndürülen verilerin, `contents` argümanında `search` fonksiyonuna aktarılan 
veriler kadar uzun yaşayacağını söylüyoruz. Bu çok önemlidir! Referansın geçerli olabilmesi için bir dilim tarafından referans verilen 
verinin geçerli olması gerekir; derleyici içerik yerine sorgunun dize dilimlerini oluşturduğumuzu varsayarsa, 
güvenlik kontrolünü yanlış yapacaktır.

Eğer yaşam süresi ek açıklamalarını unutur ve bu fonksiyonu derlemeye çalışırsak, bu hatayı alırız:

```console
{{#include ../listings/ch12-an-io-project/output-only-02-missing-lifetimes/output.txt}}
```

Rust bu iki argümandan hangisine ihtiyacımız olduğunu bilemez, bu yüzden bunu ona açıkça söylememiz gerekir. 
`contents` tüm metnimizi içeren argüman olduğundan ve bu metnin eşleşen kısımlarını döndürmek istediğimizden, 
`contents`'in `lifetime` söz dizimini kullanarak dönüş değerine bağlanması gereken argüman olduğunu biliyoruz.

Diğer programlama dilleri, imzada argümanları dönüş değerlerine bağlamanızı gerektirmez, ancak bu uygulama zamanla daha kolay hale gelecektir. 
Bu örneği Bölüm 10'daki [“Referansları Yaşam Süreleri ile Doğrulama”][validating-references-with-lifetimes]<!-- ignore --> bölümü ile 
karşılaştırmak isteyebilirsiniz.

Şimdi testi çalıştıralım:

```console
{{#include ../listings/ch12-an-io-project/listing-12-16/output.txt}}
```

Harika, test tam da beklediğimiz gibi başarısız oldu. Hadi testi geçelim!

### Testi Geçirmek İçin Kod Yazma

Şu anda, her zaman boş bir vektör döndürdüğümüz için testimiz başarısız oluyor. Bunu düzeltmek ve `search`'ü uygulamak için 
programımızın aşağıdaki adımları izlemesi gerekir:

* İçeriğin her satırını yineleyin.
* Satırın sorgu dizemizi içerip içermediğini kontrol edin.
* Eğer içeriyorsa, döndürdüğümüz değerler listesine ekleyin.
* Eğer içermiyorsa, hiçbir şey yapmayın.
* Eşleşen sonuçların listesini döndürün.

Satırlar arasında yineleme ile başlayarak her adımda çalışalım.

#### `lines` Metodu ile Satırlar Arasında Yineleme

Rust, dizgilerin satır satır yinelenmesini işlemek için uygun bir şekilde satır olarak adlandırılan ve Liste 12-17'de 
gösterildiği gibi çalışan yararlı bir metoda sahiptir. Bunun henüz derlenmeyeceğini unutmayın.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-17/src/lib.rs:here}}
```

<span class="caption">Liste 12-17: `contents`'teki her satırı yineleme</span>

`lines` metodu bir yineleyici döndürür. Yineleyiciler hakkında [Bölüm 13][ch13-iterators]<!-- ignore -->'te derinlemesine 
konuşacağız, ancak bir yineleyici kullanmanın bu yolunu, bir koleksiyondaki her bir öğe üzerinde bazı kodlar çalıştırmak 
için bir yineleyici ile bir `for` döngüsü kullandığımız [Liste 3-5][ch3-iter]<!-- ignore -->'te gördüğünüzü hatırlayın.

#### Sorgu için Her Satırı Arama

Ardından, geçerli satırın sorgu dizemizi içerip içermediğini kontrol edeceğiz. Neyse ki, `String` bunu bizim için yapan `contains` adında 
yararlı bir metoda sahiptir! Liste 12-18'de gösterildiği gibi, `search` fonksiyonuna `contains` metoduna bir çağrı ekleyin. 
Bunun henüz derlenmeyeceğini unutmayın.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-18/src/lib.rs:here}}
```

<span class="caption">Liste 12-18: Satırın `query`'deki dizgiyi içerip içermediğini görmek için yeni bir işlevsellik ekleme</span>

Şu anda işlevsellik geliştiriyoruz. Derlemek için, fonksiyon imzasında belirttiğimiz gibi gövdeden bir 
değer döndürmemiz gerekiyor.

#### Eşleşen Satırları Depolama

Bu fonksiyonu tamamlamak için, döndürmek istediğimiz eşleşen satırları saklamanın bir yoluna ihtiyacımız var. 
Bunun için, `for` döngüsünden önce değiştirilebilir bir vektör oluşturabilir ve bir satırı vektörde saklamak için `push` metodunu çağırabiliriz. 
`for` döngüsünden sonra, Liste 12-19'da gösterildiği gibi vektörü döndürürüz.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-19/src/lib.rs:here}}
```

<span class="caption">Liste 12-19: Eşleşen satırları döndürebilmek için depolamak</span>

Şimdi `search` fonksiyonu yalnızca `query`'i içeren satırları döndürmeli ve testimiz geçmelidir. Testi çalıştıralım:

```console
{{#include ../listings/ch12-an-io-project/listing-12-19/output.txt}}
```

Testimiz geçti, bu yüzden işe yaradığını biliyoruz!

Bu noktada, aynı işlevselliği korumak için testlerin geçmesini sağlarken `search` fonksiyonunun süreklemesini yeniden 
düzenleme fırsatlarını değerlendirebiliriz. `search` fonksiyonundaki kod çok da *kötü* **değil**, ancak yineleyicilerin bazı yararlı 
özelliklerinden yararlanmıyor *o kadar*. Yineleyicileri ayrıntılı olarak inceleyeceğimiz [Bölüm 13][ch13-iterators]<!-- ignore -->'te bu örneğe 
geri döneceğiz ve nasıl geliştirebileceğimize bakacağız.

#### `search` Fonksiyonunu `run` Fonksiyonunda Kullanma

Artık `search` fonksiyonu çalıştığına ve test edildiğine göre, `run` fonksiyonumuzdan `search`'ü çağırmamız gerekiyor. 
`config.query` değerini ve `run`'ın dosyadan okuduğu içeriği `search` fonksiyonuna aktarmamız gerekiyor. 
Ardından `run`, `search` çağrısından dönen her satırı yazdıracaktır:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch12-an-io-project/no-listing-02-using-search-in-run/src/lib.rs:here}}
```

Her satırı `search`'ten döndürmek ve yazdırmak için hala bir `for` döngüsü kullanıyoruz.

Artık programımız çalışmalıdır! İlk olarak Emily Dickinson şiirinden tam olarak bir dizgi döndürmesi gereken bir sözcük ile deneyelim, “frog”:

```console
{{#include ../listings/ch12-an-io-project/no-listing-02-using-search-in-run/output.txt}}
```

Harika! Şimdi “body” gibi birden fazla satırla eşleşecek bir sözcüğü deneyelim:

```console
{{#include ../listings/ch12-an-io-project/output-only-03-multiple-matches/output.txt}}
```

Ve son olarak, “monomorphization” gibi şiirin hiçbir yerinde olmayan bir sözcüğü aradığımızda herhangi bir satır 
almadığımızdan emin olalım:

```console
{{#include ../listings/ch12-an-io-project/output-only-04-no-matches/output.txt}}
```

Mükemmel! Klasik aracın kendi mini versiyonumuzu oluşturduk ve uygulamaların nasıl yapılandırılacağı hakkında çok şey öğrendik. 
Ayrıca dosya girişi ve çıkışı, yaşam süreleri, test etme ve komut satırı ayrıştırma hakkında da biraz bilgi edindik.

Bu projeyi tamamlamak için, her ikisi de komut satırı programları yazarken yararlı olan ortam değişkenleriyle nasıl çalışılacağını ve 
standart hataya nasıl yazdırılacağını kısaca göstereceğiz.

[validating-references-with-lifetimes]:
ch10-03-lifetime-syntax.html#validating-references-with-lifetimes
[ch11-anatomy]: ch11-01-writing-tests.html#the-anatomy-of-a-test-function
[ch10-lifetimes]: ch10-03-lifetime-syntax.html
[ch3-iter]: ch03-05-control-flow.html#looping-through-a-collection-with-for
[ch13-iterators]: ch13-02-iterators.html
