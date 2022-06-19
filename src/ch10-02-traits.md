## Tanımlar: Paylaşılan Davranışı Tanımlama

Bir *tanım*, belirli bir türün sahip olduğu ve diğer türlerle paylaşabileceği işlevselliği tanımlar. 
Paylaşılan davranışı soyut bir şekilde tanımlamak için tanımları kullanabiliriz. Genel bir türün belirli davranışlara sahip herhangi 
bir tür olabileceğini belirtmek için tanım sınırlarını kullanabiliriz.

> Not: Tanımlar, bazı farklılıklara rağmen diğer dillerde genellikle *arayüz* olarak adlandırılan 
> bir özelliğe benzer.

### *Tanım* Tanımlama

Bir türün davranışı, o tür üzerinde çağırabileceğimiz metodlardan oluşur. 
Tüm türler üzerinde aynı metodları çağırabiliyorsak, farklı tipler aynı davranışı paylaşır. 
Tanım tanımları, bir amacı gerçekleştirmek için gerekli olan bir dizi davranışı tanımlamak üzere metod imzalarını 
bir araya getirmenin bir yoludur.

Örneğin, çeşitli tür ve miktarlarda metin tutan birden fazla yapımız olduğunu varsayalım: 
belirli bir konumda dosyalanmış bir haberi tutan bir NewsArticle yapısı ve yeni bir tweet mi, retweet mi yoksa başka bir tweet'e yanıt mı olduğunu gösteren meta verilerle birlikte en fazla 280 karaktere sahip olabilen bir Tweet.

Bir `NewsArticle` veya `Tweet` örneğinde saklanabilecek verilerin özetlerini görüntüleyebilen `aggregator` adlı bir 
medya toplayıcı kütüphane kasası yapmak istiyoruz. Bunu yapmak için, her türden bir özete ihtiyacımız var ve bir 
örnek üzerinde bir summarize yöntemi çağırarak bu özeti talep edeceğiz. Liste 10-12, bu davranışı ifade eden bir genel
`Summary` tanımının tanımlanmasını göstermektedir.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-12/src/lib.rs}}
```

<span class="caption">Liste 10-12: `summarize` metoduyla sağlanan davranıştan oluşan bir `Summary` tanımı</span>

Burada, `trait` anahtar sözcüğünü ve ardından bu durumda `Summary` olan `trait`'in adını kullanarak bir `trait` bildiriyoruz. 
Ayrıca, birkaç örnekte göreceğimiz gibi, bu kasaya bağlı kasaların da bu tanımı kullanabilmesi için tanımı `pub` olarak bildirdik. 
Süslü parantezlerin içinde, bu tanımı uygulayan türlerin davranışlarını tanımlayan metod imzalarını bildiriyoruz; 
bu durumda `fn summarize(&self) -> String` olacaktır.

Metod imzasından sonra, süslü parantezler içinde bir sürekleme sağlamak yerine noktalı virgül kullanırız. 
Bu tanımı uygulayan her tür, metodun gövdesi için kendi özel davranışını sağlamalıdır. Derleyici, `Summary` tanımına sahip 
herhangi bir türün `summarize` metodunun tam olarak bu imza ile tanımlanmasını zorunlu kılacaktır.

Bir tanımın gövdesinde birden fazla metod olabilir: metod imzaları her satırda bir tane listelenir ve her satır noktalı virgülle biter.

### Tür Üzerinde Tanım Uygulama

`Summary` tanımının metodlarının istenen imzalarını tanımladığımıza göre, bunu medya toplayıcımızdaki türlere uygulayabiliriz. 
Liste 10-13, `Summary` tanımının `NewsArticle` yapısı üzerinde, `summarize`'ın dönüş değerini oluşturmak için başlığı, 
yazarı ve konumu kullanan bir süreklemesini göstermektedir. Tweet yapısı için, tweet içeriğinin zaten 280 karakterle sınırlı olduğunu 
varsayarak, `summarize` özelliğini kullanıcı adı ve ardından tweet metninin tamamı olarak tanımlarız.


<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-13/src/lib.rs:here}}
```

<span class="caption">Liste 10-13: `NewsArticle` ve `Tweet` türlerine `Summary` tanımının süreklenmesi</span>

Bir tür üzerinde bir tanım süreklemek, normal metodları süreklemeye benzer. Aradaki fark, `impl`'den sonra süreklemek istediğimiz 
özellik adını koymamız, ardından `for` anahtar sözcüğünü kullanmamız ve ardından tanımı uygulamak istediğimiz türün adını belirtmemizdir. 
`impl` bloğunun içine, *tanım* tanımının **tanımladığı** metod imzalarını koyarız. Her imzadan sonra noktalı virgül eklemek yerine, 
süslü parantezler kullanırız ve metod gövdesini, tanımın metodlarının belirli bir tür için sahip olmasını istediğimiz belirli 
davranışla doldururuz.

Artık kütüphane `NewsArticle` ve `Tweet` üzerinde `Summary` tanımını süreklediğine göre, kasa kullanıcıları `NewsArticle` ve 
`Tweet` örnekleri üzerindeki tanım metodlarını normal metodları çağırdığımız şekilde çağırabilir. Tek fark, kullanıcının türlerin 
yanı sıra tanımı da kapsam içine alması gerektiğidir. İşte ikili bir kasanın `aggregator` kütüphane kasamızı nasıl kullanabileceğine dair 
bir örnek:

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-01-calling-trait-method/src/main.rs}}
```

Bu kod `1 new tweet: horse_ebooks: of course, as you probably already
know, people` yazdırır.

`aggregator` kasamıza bağımlı olan diğer kasalar da `Summary` tanımını kendi türlerinde süreklemek için kapsam içine alabilir. 
Unutulmaması gereken bir kısıtlama, bir özelliği bir tür üzerinde yalnızca tanım veya türden en az birinin kasamız için yerel olması 
durumunda uygulayabileceğimizdir. Örneğin, `Tweet` türü `aggregator` kasamız için yerel olduğundan, `Display` gibi standart kütüphane 
özelliklerini `Tweet` gibi gizli bir tür üzerinde kasa işlevselliğimizin bir parçası olarak sürekleyebiliriz. 
Ayrıca `Summary` tanımını `Vec<T>` üzerinde de sürekleyebiliriz, çünkü `Summary` tanımı kasamız için yereldir.

Ancak harici tanımları harici türler üzerinde uygulayamayız. Örneğin, `Display` tanımını `Vec<T>` üzerinde; kasamızda sürekleyemeyiz, 
çünkü `Display` ve `Vec<T>` standart kütüphanede tanımlanmıştır ve kasamız için yerel değildir. Bu kısıtlama, *tutarlılık* adı verilen bir 
özelliğin ve daha spesifik olarak, ana tür mevcut olmadığı için bu şekilde adlandırılan *yetim kuralının* bir parçasıdır. 
Bu kural, başkalarının kodunun sizin kodunuzu bozamamasını ve bunun tersinin de geçerli olmamasını sağlar. 
Bu kural olmasaydı, iki kasa aynı tür için aynı tanımı sürekleyebilirdi ve Rust hangi süreklemeyi kullanacağını bilemezdi.

### Varsayılan Süreklemeler

Bazen, her türdeki tüm metodlar için sürekleme gerektirmek yerine, bir tanımdaki metodlardan bazıları veya 
tümü için varsayılan davranışa sahip olmak yararlıdır. Daha sonra, tanımı belirli bir tür üzerinde süreklerken, 
her bir metodun varsayılan davranışını koruyabilir veya geçersiz kılabiliriz.

Liste 10-14'te, Liste 10-12'de yaptığımız gibi yalnızca metod imzasını tanımlamak yerine `Summary` tanımının `summarize` metodu için 
varsayılan bir dizgi belirtiyoruz.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-14/src/lib.rs:here}}
```

<span class="caption">Liste 10-14: `Summarize` metodunun varsayılan süreklemesiyle bir `Summary` tanımının yazılması</span>

`NewsArticle` örneklerini özetlemek üzere varsayılan bir sürekleme kullanmak istediğimiz için, 
`impl Summary for NewsArticle {}` ile boş bir `impl` bloğu belirtiriz.

Artık `NewsArticle` üzerinde `summarize` metodunu doğrudan tanımlamıyor olsak da, 
varsayılan bir sürekleme sağladık ve `NewsArticle`'ın `Summary` tanımını süreklediğini belirttik. 
Sonuç olarak, bir `NewsArticle` örneği üzerinde `summarize` metodunu aşağıdaki gibi çağırabiliriz:

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-02-calling-default-impl/src/main.rs:here}}
```

Bu kod `New article available! (Read more...)` çıktısını verir.

Varsayılan bir sürekleme oluşturmak, Liste 10-13'teki `Tweet`'deki `Summary` süreklemesinde herhangi bir değişiklik yapmamızı 
gerektirmez. Bunun nedeni, varsayılan bir süreklemeyi geçersiz kılma söz diziminin, varsayılan bir süreklemeye sahip olmayan 
bir tanım metodunu sürekleme söz dizimiyle aynı olmasıdır.

Varsayılan süreklemeler, diğer metodların varsayılan bir süreklemesi olmasa bile aynı tanımdaki diğer metodları çağırabilir. 
Bu şekilde, bir özellik çok sayıda yararlı işlevsellik sağlayabilir ve uygulayıcıların bunun yalnızca küçük bir bölümünü 
belirtmesini gerektirebilir. Örneğin, `Summary` tanımını, süreklenmesi gerekli olan bir `summarize_author` metoduna sahip olacak 
şekilde tanımlayabilir ve ardından `summarize_author` metodunu çağıran varsayılan bir süreklemeye sahip bir `summarize` metodu tanımlayabiliriz:

```rust,noplayground
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-03-default-impl-calls-other-methods/src/lib.rs:here}}
```

`Summary`'in bu sürümünü kullanmak için, özelliği bir türe süreklediğimizde yalnızca `summary_author`'u tanımlamamız gerekir:

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-03-default-impl-calls-other-methods/src/lib.rs:impl}}
```

`summarize_author`'ı tanımladıktan sonra, `Tweet` yapısının örnekleri üzerinde `summarize`'ı çağırabiliriz ve 
`summarize`'ın varsayılan süreklemesi, sağladığımız `summarize_author` tanımını çağıracaktır. 
`summarize_author` tanımını süreklediğimiz için, `Summary` tanımı bize daha fazla kod yazmamızı gerektirmeden 
`summarize` metodunun davranışını vermiştir.

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-03-default-impl-calls-other-methods/src/main.rs:here}}
```

Bu kod `1 new tweet: (Read more from @horse_ebooks...)` çıktısını verecektir.

Aynı metodun geçersiz kılınan bir süreklemesinden varsayılan süreklemeyi çağırmanın mümkün olmadığını unutmayın.

### Parametre olarak Tanımlar

Artık tanımları nasıl tanımlayacağınızı ve sürekleyeceğinizi bildiğinize göre, 
birçok farklı türü kabul eden fonksiyonları tanımlamak için tanımları nasıl kullanacağımızı keşfedebiliriz. 
Liste 10-13'te `NewsArticle` ve `Tweet` türleri üzerinde süreklediğimiz `Summary` tanımını, `Summary` tanımını sürekleyen bir 
türden olan `item` parametresi üzerinde `summarize` metodunu çağıran `notify` fonksiyonunu tanımlamak için kullanacağız. 
Bunu yapmak için, aşağıdaki gibi `impl Trait` söz dizimini kullanırız:

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-04-traits-as-parameters/src/lib.rs:here}}
```

Öğe parametresi için somut bir tür yerine, 
`impl` anahtar sözcüğünü ve tanım adını belirtiriz. Bu parametre, belirtilen tanımı uygulayan herhangi bir 
türü kabul eder. `notify`'ın gövdesinde, `item` üzerinde `Summary` tanımından gelen `summarize` gibi herhangi bir metodu çağırabiliriz. 
`notify`'ı çağırabilir ve `NewsArticle` veya `Tweet`'in herhangi bir örneğini aktarabiliriz. Fonksiyonu `String` veya 
`i32` gibi başka bir türle çağıran kod derlenmez çünkü bu türler `Summary` tanımını uygulamaz.

#### Tanıma Bağlılık Söz Dizimi

`impl Trait` söz dizimi basit durumlar için çalışır, ancak aslında *tanıma bağlılık* olarak bilinen daha uzun bir form için 
*söz dizimi tatlılığıdır*; şöyle görünür:

```rust,ignore
pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}
```

Bu uzun form önceki bölümdeki örneğe eş değerdir ancak daha ayrıntılıdır. Tanıma bağlılık, iki nokta üst üste 
işaretinden sonra ve köşeli parantezler içinde yaygın tür parametresinin bildirimiyle birlikte yerleştiririz.

`impl Trait` söz dizimi kullanışlıdır ve basit durumlarda daha özlü bir kod sağlarken, tanıma bağlılık söz dizimi 
karmaşık durumlarda daha farklı bir şekilde kendini ifade edebilir. Örneğin, `Summary` öğesini uygulayan iki parametreye sahip olabiliriz. 
Bunu `impl Trait` sö zdizimi ile yapmak şu şekilde görünür:

```rust,ignore
pub fn notify(item1: &impl Summary, item2: &impl Summary) {
```

Bu fonksiyonun `item1` ve `item2`'nin farklı türlere sahip olmasına izin vermesini istiyorsak 
(her iki tür de `Summary`'yi süreklediği sürece) `impl Trait` kullanmak uygundur. 
Ancak her iki parametrenin de aynı türde olmasını istiyorsak, aşağıdaki gibi, tanıma bağlılığı kullanmalıyız:


```rust,ignore
pub fn notify<T: Summary>(item1: &T, item2: &T) {
```

`item1` ve `item2` parametrelerinin türü olarak belirtilen `T` yaygın türü; fonksiyonu, `item1` ve `item2` için argüman olarak 
aktarılan değerin somut türünün aynı olması gerektiği şekilde kısıtlar.

#### `+` Söz Dizimi ile Birden Fazla Tanıma Bağlılığın Belirtilmesi

Birden fazla tanıma bağlılığı da belirleyebiliriz. Diyelim ki `notify`'ın öğe üzerinde özetlemenin yanı sıra görüntüleme 
biçimlendirmesini de kullanmasını istedik: `notify` tanımında öğenin hem `Display` hem de `Summary`'i süreklemesi gerektiğini belirtiriz. 
Bunu `+` söz dizimini kullanarak da yapabiliriz:

```rust,ignore
pub fn notify(item: &(impl Summary + Display)) {
```

`+` söz dizimi, yaygın türlerdeki tanıma bağlılıkta da geçerlidir:

```rust,ignore
pub fn notify<T: Summary + Display>(item: &T) {
```

Belirtilen iki tanıma bağlılık ile, `notify` öğesinin gövdesi `summarize`'ı çağırabilir ve `item`'ı 
biçimlendirmek için `{}` kullanabilir.

#### `where` ile Daha Net Tanıma Bağlılık

Çok fazla tanıma bağlılık kullanmanın dezavantajları vardır. Her yaygın kendine özgü tanıma bağlılığa sahiptir, 
bu nedenle birden fazla yaygın tür parametresi olan fonksiyonlar, fonksiyonun adı ve parametre listesi arasında 
çok sayıda tanıma bağlılık bilgisi içerebilir ve bu da fonksiyon imzasının okunmasını zorlaştırır. 
Bu nedenle Rust, fonksiyon imzasından sonra `where` içinde tanıma bağlılığı belirtmek için alternatif bir söz dizime sahiptir. 

Yani bunu yazmak yerine:


```rust,ignore
fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 {
```

`where` kullanabiliriz:

```rust,ignore
fn some_function<T, U>(t: &T, u: &U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
{
```

Bu fonksiyonun imzası daha az karmaşıktır: fonksiyon adı, parametre listesi ve dönüş türü birbirine yakındır, 
çok sayıda tamıma bağlılığı olmayan bir fonksiyona benzer.

### Tanımları Sürekleyen Dönüş Türleri

Burada gösterildiği gibi, bir tanımı sürekleyen bir türden bir değer döndürmek için `return` konumunda
`impl Trait` söz dizimini de kullanabiliriz:

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-05-returning-impl-trait/src/lib.rs:here}}
```

Dönüş türü için `impl Summary` kullanarak, `returns_summarizable` fonksiyonunun somut türü belirtmeden `Summary` 
tanımını sürekleyen bir türü döndürdüğünü belirtiyoruz. Bu durumda, `returns_summarizable` `Tweet` döndürür, 
ancak bu fonksiyonu çağıran kodun bunu bilmesine gerek yoktur.

Bir geri dönüş türünü yalnızca uyguladığı özelliğe göre belirtme yeteneği, özellikle Bölüm 13'te ele aldığımız kapanış ifadeleri ve 
yineleyiciler bağlamında kullanışlıdır. Kapanış ifadeleri ve yineleyiciler yalnızca derleyicinin bildiği türler veya 
belirtilmesi çok uzun olan türler oluşturur. `impl Trait` söz dizimi, çok uzun bir tür yazmanıza gerek kalmadan bir fonksiyonun 
`Iterator` tanımını sürekleyen bir tür döndürdüğünü kısaca belirtmenizi sağlar.

Ancak, `impl Trait`'i yalnızca tek bir tür döndürüyorsanız kullanabilirsiniz. Örneğin, dönüş türü `impl Summary` olarak belirtilen 
bir `NewsArticle` veya `Tweet` döndüren bu kod çalışmaz:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-06-impl-trait-returns-one-type/src/lib.rs:here}}
```

Derleyicide `impl Trait` söz diziminin nasıl süreklendiğine ilişkin kısıtlamalar nedeniyle `NewsArticle` veya `Tweet` döndürülmesine 
izin verilmez. Bu davranışa sahip bir fonksiyonun nasıl yazılacağını Bölüm 17'deki 
[“Farklı Türlerde Değerlere İzin Veren Tanım Nesnelerini Kullanma”][using-trait-objects-that-allow-for-values-of-different-types]<!-- ignore --> 
kısmında ele alacağız.

### Tanıma Bağlılık ile `largest` Fonksiyonunu Düzeltme

Artık yaygın tür parametresinin bağlılıklarını kullanarak istediğiniz davranışı nasıl belirleyeceğinizi bildiğinize göre, 
yaygın tür parametresi kullanan `largest` fonksiyonunun tanımını düzeltmek için Liste 10-5'e dönelim! Bu kodu en son çalıştırmayı 
denediğimizde bu hatayı almıştık:

```console
{{#include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-05/output.txt}}
```

`largest`'in gövdesinde, daha büyük (`>`) operatörünü kullanarak `T` türündeki iki değeri karşılaştırmak istedik. 
Bu operatör standart kütüphane özelliği `std::cmp::PartialOrd` üzerinde varsayılan bir metod olarak tanımlandığından, 
`T`'ye tanıma bağlılıktan dolayı `PartialOrd`'u süreklememiz gerekir, böylece `largest` fonksiyonu karşılaştırabileceğimiz 
herhangi bir türden dilimler üzerinde çalışabilir. `PartialOrd`'u kapsam içine almamıza gerek yok çünkü o zaten kapsama otomatik
olarak dahildir... Yani, `largest`'in imzasını aşağıdaki gibi değiştirin:

```rust,ignore
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-07-fixing-listing-10-05/src/main.rs:here}}
```

Bu sefer kodu derlediğimizde farklı hatalar alıyoruz:

```console
{{#include ../listings/ch10-generic-types-traits-and-lifetimes/no-listing-07-fixing-listing-10-05/output.txt}}
```

Buranın en önemli noktası, `kopyalanmamış bir dilim olan [T] türünün dışına çıkamaz` (`cannot move out of type [T], a non-copy slice`) hatasıdır. 
`largest` fonksiyonunun yaygın olmayan versiyonlarında, yalnızca en büyük `i32` veya `char`'ı bulmaya çalışıyorduk. 
Bkz. [“Sadece Yığıt Kullanan Veriler: Kopyalama”][stack-only-data-copy]<!-- ignore --> konusuna bakın, `i32` ve `char` gibi bilinen bir 
boyuta sahip türler yığıtta saklanabilir, böylece `Copy` tanımı süreklenebilir. Ancak, `largest` fonksiyonunu genelleştirdiğimizde, 
`list` parametresinin içinde `Copy` tanımını süreklemeyen türler olması mümkün hale geldi. Sonuç olarak, 
değeri `list[0]`'ten `largest` değişkenine taşıyamayız, bu da bu hataya neden olur.

Bu kodu yalnızca `Copy` tanımını sürekleyen türlerle birlikte çağırmak için, `T`'nin tanıma bağlılık listesine `Copy`'i ekleyebiliriz! 
Liste 10-15, fonksiyona aktardığımız dilimdeki değerlerin türleri `i32` ve `char` gibi `PartialOrd` *ve* `Copy` tanımlarını süreklediği sürece 
derlenecek yaygın `largest` fonksiyonunun tam kodunu gösterir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-15/src/main.rs}}
```

<span class="caption">Liste 10-15: `PartialOrd` ve `Copy` tanımlarını sürekleyen herhangi bir yaygın tür 
üzerinde çalışan `largest` fonksiyonunun düzgün çalışan versiyonu</span>

`largest` fonksiyonunu `Copy` tanımını sürekleyen türlerle sınırlamak istemiyorsak, 
`T`'nin `Copy` yerine `Clone` tanımına sahip olduğunu belirtebiliriz. Böylece, `largest` fonksiyonunun sahibi olmasını istediğimizde 
dilimdeki her değeri klonlayabiliriz. `Clone` fonksiyonunu kullanmak, `String` gibi yığın verisine sahip türler söz konusu olduğunda 
potansiyel olarak daha fazla yığın tahsisi yapacağımız anlamına gelir ve büyük miktarda veriyle çalışıyorsak yığın tahsisleri yavaş olabilir.

Ayrıca, fonksiyonun dilimdeki bir `T` değerine bir referans döndürmesini sağlayarak `largest`'ı sürekleyebiliriz. 
Dönüş türünü `T` yerine `&T` olarak değiştirirsek, böylece fonksiyonun gövdesini bir referans döndürecek şekilde değiştirirsek, 
`Clone` veya `Copy` tanıma bağlılıklarına ihtiyacımız olmaz ve yığın tahsisatlarından kaçınabiliriz. 
Bu alternatif çözümleri kendi başınıza yazmayı deneyin! Yaşam süreleri ile ilgili hatalara takılırsanız, 
okumaya devam edin: “Yaşam Süreleri ile Referansları Doğrulama” bölümü durumu açıklayacaktır, 
ancak bu meydan okumaları çözmek için yaşam süreleri gerekli değildir.

### Metodları Koşullu Olarak Süreklemek için Tanıma Bağlılığı Kullanma

Yaygın tür parametreleri kullanan bir `impl bloğu` ile bağlı bir tanım kullanarak, 
belirtilen tanımları sürekleyen türler için metodları koşullu olarak sürekleyebiliriz. Örneğin, Liste 10-16'daki `Pair<T>` türü her 
zaman yeni bir `Pair<T>` örneği döndürmek için `new` fonksiyonunu çağırır (Bölüm 5'teki [“Metodları Tanımlama”][methods]<!-- ignore --> 
bölümünden `Self`'in `impl` bloğunun türü için bir tür takma ad olduğunu hatırlayın, bu durumda `Pair<T>`'dir). 
Ancak bir sonraki `impl` bloğunda, `Pair<T>` yalnızca iç tipi `T`, karşılaştırmayı sağlayan `PartialOrd` tanımını ve 
yazdırmayı sağlayan `Display` tanımını süreklerse `cmp_display` metodunu sürekleyebilir.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-16/src/lib.rs}}
```

<span class="caption">Liste 10-16: *Tanıma bağlılığa* bağlı olarak metodları yaygın bir tür üzerinde koşullu ,
olarak süreklemek</span>

Ayrıca, başka bir tanımı sürekleyen herhangi bir tür için bir tanımı koşullu olarak sürekleyebiliriz. 
Bir tanımın, tanıma bağlılığı karşılayan herhangi bir tür üzerindeki süreklemelerine 
*kapsamlı sürekleme* denir ve Rust standart kütüphanesinde yaygın olarak kullanılır. 
Örneğin, standart kütüphane `Display` tanımını sürekleyen herhangi bir tür üzerinde `ToString` tanımını sürekler. 
Standart kütüphanedeki `impl` bloğu bu koda benzer:

```rust,ignore
impl<T: Display> ToString for T {
    // --snip--
}
```

Standart kütüphane bu *kapsamlı süreklemeye* sahip olduğundan, `Display` tanımını sürekleyen herhangi bir tür üzerinde 
`ToString` tanımı tarafından tanımlanan `to_string` metodunu çağırabiliriz. Örneğin, tam sayılar `Display` tanımını 
süreklediği için tam sayıları karşılık gelen `String` değerlerine dönüştürebiliriz:

```rust
let s = 3.to_string();
```

*Kapsamlı süreklemeler*, tanımın dokümantasyonunun “Implementors” bölümünde görünür.

Tanımlar ve tanıma bağlılık, yinelemeyi azaltmak için yaygın tür parametrelerini kullanan kod yazmamıza ve 
aynı zamanda derleyiciye yaygın türün belirli bir davranışa sahip olmasını istediğimizi belirtmemize olanak tanır. 
Derleyici daha sonra kodumuzla birlikte kullanılan tüm somut tiplerin doğru davranışı sağlayıp sağlamadığını kontrol etmek için 
tanıma bağlılık bilgisini kullanabilir. Dinamik olarak yazılan dillerde, metodu tanımlamayan bir tür üzerinde bir metod çağırırsak 
çalışma zamanında bir hata alırız. Ancak Rust bu hataları derleme zamanına taşır, böylece kodumuz daha çalışmadan önce sorunları 
düzeltmek zorunda kalırız. Ayrıca, derleme zamanında zaten kontrol ettiğimiz için çalışma zamanında davranışı kontrol eden kod yazmak 
zorunda kalmayız. Bunu yapmak, yaygınların esnekliğinden vazgeçmek zorunda kalmadan performansı artırır.

[stack-only-data-copy]: ch04-01-what-is-ownership.html#stack-only-data-copy
[using-trait-objects-that-allow-for-values-of-different-types]: ch17-02-trait-objects.html#using-trait-objects-that-allow-for-values-of-different-types
[methods]: ch05-03-method-syntax.html#defining-methods
