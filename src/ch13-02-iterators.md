## Yineleyicilerle Bir Öğe Serisini İşleme
Yineleyici deseni, sırayla bir dizi öğe üzerinde bazı görevleri gerçekleştirmenize olanak tanır. 
Bir yineleyici, her bir öğe üzerinde yineleme mantığından ve dizinin ne zaman bittiğini belirlemekten sorumludur. 
Yineleyicileri kullandığınızda, bu mantığı kendiniz yeniden uygulamak zorunda kalmazsınız.

Rust'ta yineleyiciler *tembeldir*, yani siz onu kullanmak için yineleyiciyi tüketen metodları 
çağırana kadar hiçbir etkileri yoktur. Örneğin, Liste 13-10'daki kod, `Vec<T>` üzerinde tanımlanan
`iter` metodunu çağırarak `v1` vektöründeki öğeler üzerinde bir yineleyici oluşturur. 
Bu kod kendi başına yararlı bir şey yapmaz.

```rust
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-10/src/main.rs:here}}
```

<span class="caption">Liste 13-10: Yineleyici oluşturmak</span>

Bir yineleyici oluşturduktan sonra, onu çeşitli şekillerde kullanabiliriz. 
Bölüm 3'teki Liste 3-5'te, öğelerinin her birinde bazı kodlar çalıştırmak için `for` döngüsü kullanarak bir dizi 
üzerinde yineleme yaptık. Bu dolaylı olarak bir yineleyici oluşturdu ve sonra üzerinden geçti, 
ancak şimdiye kadar bunun tam olarak nasıl çalıştığını geçtik.

Liste 13-11'deki örnek, yineleyicinin oluşturulmasını `for` döngüsündeki yineleyici kullanımından ayırır. 
Yineleyici `v1_iter` değişkeninde saklanır ve o sırada herhangi bir yineleme gerçekleşmez.
`v1_iter` içindeki yineleyici kullanılarak `for` döngüsü çağrıldığında, yineleyicideki her öğe döngünün bir 
yinelemesinde kullanılır ve her değer yazdırılır.


```rust
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-11/src/main.rs:here}}
```

<span class="caption">Liste 13-11: Bir `for` döngüsünde yineleyici kullanma</span>

Standart kütüphaneleri tarafından sağlanan yineleyicilere sahip olmayan dillerde,
muhtemelen aynı fonksiyonu `0` indeksinde bir değişken başlatarak, bir değer elde etmek için vektörü indekslemek
üzere bu değişkeni kullanarak ve vektördeki toplam öğe sayısına ulaşana kadar değişken değerini bir döngü içinde 
artırarak yazarsınız.

Yineleyiciler tüm bu mantığı sizin için halleder ve potansiyel olarak karıştırabileceğiniz tekrarlayan kodu azaltır. 
Yineleyiciler, aynı mantığı yalnızca vektörler gibi indeksleyebileceğiniz veri yapılarıyla değil, birçok farklı türde 
diziyle kullanmanız için size daha fazla esneklik sağlar. Yineleyicilerin bunu nasıl yaptığını inceleyelim.

### `Iterator` Tanımı ve `next` Metodu

Tüm yineleyiciler, standart kütüphanede tanımlanan `Iterator` adlı tanımı sürekler. 
Tanımın *tanımı* şu şekildedir:

```rust
pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;

    // methods with default implementations elided
}
```

Bu tanımın bazı yeni söz dizimleri kullandığına dikkat edin:
`Item` ve `Self::Item` türleri bu özellik ile *ilişkili bir tür* tanımlamaktadır. 
İlişkili türler hakkında Bölüm 19'da derinlemesine konuşacağız. Şimdilik bilmeniz gereken tek şey, 
bu kodun `Iterator` tanımını süreklemenin `Item` türünü de tanımlamanızı gerektirdiğini ve bu
`Item` türünün bir sonraki metodun dönüş türünde kullanıldığını söylediğidir. Başka bir deyişle,
`Item` türü yineleyiciden döndürülen tür olacaktır.

`Iterator` tanımı, sürekleyicilerin yalnızca bir metod tanımlamasını gerektirir:
`Some` içine sarılmış olarak her seferinde yineleyicinin bir öğesini döndüren ve yineleme sona erdiğinde
`None` döndüren `next` metodu.

Yineleyicilerde `next` metodunu doğrudan çağırabiliriz; Liste 13-12, vektörden oluşturulan yineleyicide
`next` metodunun tekrarlanan çağrılarından hangi değerlerin döndürüldüğünü gösterir.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-12/src/lib.rs:here}}
```

<span class="caption">Liste 13-12: Yineleyici üzerinde `next` metodunun çağrılması</span>

`v1_iter`'ı değiştirilebilir yapmamız gerektiğine dikkat edin: bir yineleyici üzerinde `next` 
metodunu çağırmak, yineleyicinin sıralamada nerede olduğunu takip etmek için kullandığı dahili durumu değiştirir. 
Başka bir deyişle, bu kod yineleyiciyi tüketir ya da kullanır. Her `next` çağrısı yineleyiciden bir öğe tüketir. 
Bir `for` döngüsü kullandığımızda `v1_iter`'ı değiştirilebilir yapmamıza gerek yoktu çünkü döngü `v1_iter`'ın sahipliğini 
aldı ve onu *perde arkasında* değiştirilebilir yaptı.

Ayrıca `next` çağrısından elde ettiğimiz değerlerin vektördeki değerlere değişmez referanslar olduğuna dikkat edin.
`iter` metodu, değişmez referanslar üzerinde bir yineleyici üretir. Eğer `v1`'in sahipliğini alan ve 
sahip olunan değerleri döndüren bir yineleyici oluşturmak istiyorsak, `iter` yerine `into_iter`'ı çağırabiliriz. 
Benzer şekilde, değiştirilebilir referanslar üzerinde yineleme yapmak istiyorsak, `iter` yerine `iter_mut` metodunu 
çağırabiliriz.

### Yineleyici Kullanan Metodlar

`Iterator` tanımı, standart kütüphane tarafından sağlanan varsayılan süreklemelere sahip bir dizi farklı 
metoda sahiptir; `Iterator` tanımı için standart kütüphane API dokümantasyonuna bakarak bu metodlar hakkında bilgi 
edinebilirsiniz. Bu metodlardan bazıları tanımlarında `next` metodunu çağırır, bu nedenle `Iterator` tanımını 
süreklerken `next` metodunu da süreklememiz gerekir.

`next` metodunu çağıran metodlara *tüketim uyarlayıcıları* denir, çünkü bunları çağırmak yineleyiciyi kullanır. 
Bunun bir örneği, yineleyicinin sahipliğini alan ve `next` metodunu tekrar tekrar çağırarak öğeler arasında yineleme 
yapan ve böylece yineleyiciyi tüketen `sum` metodudur. Yineleme sırasında, her öğeyi çalışan bir toplama ekler 
ve yineleme tamamlandığında toplamı döndürür. Liste 13-13, `sum` metodunun kullanımını gösteren bir test içerir:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-13/src/lib.rs:here}}
```

<span class="caption">Liste 13-13: Yineleyicideki tüm öğelerin toplamını almak için `sum` metodunu çağırma</span>

`sum` çağrısından sonra `v1_iter` kullanmamıza izin verilmez çünkü `sum`, çağırdığımız yineleyicinin sahipliğini alır.

### Diğer Yineleyicileri Üreten Metodlar

`Iterator` tanımı üzerinde tanımlanan ve *yineleyici adaptorü* olarak bilinen diğer metodlar, 
yineleyiciyi farklı yineleyici türlerine dönüştürmenize olanak tanır. Karmaşık eylemleri okunabilir bir 
şekilde gerçekleştirmek için yineleyici uyarlayıcılarına birden fazla çağrıyı zincirleyebilirsiniz. 
Ancak tüm yineleyiciler tembel olduğundan, yineleyici uyarlayıcılarına yapılan çağrılardan sonuç almak için 
tüketen uyarlayıcı metodlardan birini çağırmanız gerekir.

Liste 13-14, yeni bir yineleyici üretmek için her öğede çağrılacak bir kapanış alan yineleyici uyarlayıcı 
yöntemi olan `map`'i çağırmanın bir örneğini göstermektedir. Buradaki kapanış, 
vektördeki her öğenin `1` artırıldığı yeni bir yineleyici oluşturur. Ancak, bu kod bir uyarı üretir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,not_desired_behavior
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-14/src/main.rs:here}}
```

<span class="caption">Liste 13-14: Yeni bir yineleyici oluşturmak için `map` yineleyici uyarlayıcısını çağırma</span>

Aldığımız uyarı şudur:

```console
{{#include ../listings/ch13-functional-features/listing-13-14/output.txt}}
```

Liste 13-14'teki kod hiçbir şey yapmaz; belirttiğimiz kapanış hiçbir zaman çağrılmaz. 
Uyarı bize nedenini gösteriyor: yineleyici uyarlayıcıları *tembeldir* ve burada yineleyiciyi tüketmemiz gerekir.

Bunu düzeltmek ve yineleyiciyi tüketmek için, Bölüm 12'de Liste 12-1'de `env::args` ile kullandığımız `collect` 
metodunu kullanacağız. Bu metod yineleyiciyi tüketir ve elde edilen değerleri bir koleksiyon veri türünde toplar.

Liste 13-15'te, `map` çağrısından döndürülen yineleyici üzerinde yineleme sonuçlarını bir vektörde topluyoruz. 
Bu vektör, orijinal vektördeki her bir öğenin `1` ile artırılmış halini içerecektir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-15/src/main.rs:here}}
```

<span class="caption">Liste 13-15: Yeni bir yineleyici oluşturmak için `map` metodunu çağırmak ve ardından yeni 
yineleyiciyi tüketmek ve bir vektör oluşturmak için `collect` metodunu çağırmak</span>

`map` bir kapanış aldığı için, her bir öğe üzerinde gerçekleştirmek istediğimiz herhangi bir işlemi belirtebiliriz. 
Bu, kapanışların `Iterator` tanımını sağladığı yineleme davranışını yeniden kullanırken bazı davranışları özelleştirmenize 
nasıl izin verdiğinin harika bir örneğidir.

### Ortamlarını Yakalayan Kapanışları Kullanma

Yineleyicileri tanıttığımıza göre, `filter` *yineleyici adaptörünü* kullanarak çevrelerini yakalayan kapanışların 
yaygın bir kullanımını gösterebiliriz. Bir yineleyici üzerindeki `filter` metodu, yineleyicideki her bir 
öğeyi alan ve bir Boole döndüren bir kapanış alır. Eğer kapanış `true` döndürürse, değer
`filter` tarafından üretilen yineleyiciye dahil edilecektir. Kapanış `false` döndürürse, değer 
elde edilen yineleyiciye dahil edilmez.

Liste 13-16'da, `Shoe` `struct` örneklerinden oluşan bir koleksiyon üzerinde yineleme yapmak için ortamından
`shoe_size` değişkenini yakalayan bir kapanış ile `filter`'ı kullanırız. Yalnızca belirtilen boyutta olan 
ayakkabıları döndürür.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-16/src/lib.rs}}
```

<span class="caption">Liste 13-16: `shoe_size` öğesini yakalayan bir kapanış ile `filter` metodunu kullanma</span>

`shoes_in_size` fonksiyonu, parametre olarak bir ayakkabı vektörüne ve bir `shoe_size`'a yani
ayakkabı boyutuna sahip olur. 
Yalnızca belirtilen boyuttaki ayakkabıları içeren bir vektör döndürür.

`shoes_in_size`'ın gövdesinde, vektörün sahipliğini alan bir yineleyici oluşturmak için `into_iter`'ı çağırıyoruz. 
Daha sonra bu yineleyiciyi yalnızca kapanışın `true` döndürdüğü öğeleri içeren yeni bir yineleyiciye uyarlamak için
`filter`'ı çağırıyoruz.

Kapanış, `shoe_size` parametresini ortamdan alır ve değeri her `shoe_size`'ı karşılaştırarak 
yalnızca belirtilendekileri tutar. Son olarak, `collect` çağrısı, uyarlanmış yineleyici tarafından döndürülen değerleri,
fonksiyon tarafından döndürülen bir vektörde toplar.

Test, `shoes_in_size` öğesini çağırdığımızda, yalnızca belirttiğimiz değerle aynı boyuta sahip 
ayakkabıları geri aldığımızı gösterir.
