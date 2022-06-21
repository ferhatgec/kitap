## Kapanış İfadeleri: Çevrelerini Yakalayabilen Anonim Fonksiyonlar

Rust'ın kapanışları, bir değişkene kaydedebileceğiniz veya diğer fonksiyonlara argüman olarak aktarabileceğiniz 
anonim fonksiyonlardır. Kapanışları bir yerde oluşturabilir ve daha sonra farklı bir bağlamda
değerlendirmek için kapanışları çağırabilirsiniz. Fonksiyonların aksine, kapanışlar tanımlandıkları kapsamdaki değerleri yakalayabilir. 
Bu kapanış özelliklerinin kodun yeniden kullanımına ve davranış özelleştirmesine nasıl izin verdiğini göstereceğiz.

<!-- Old headings. Do not remove or links may break. -->
<a id="creating-an-abstraction-of-behavior-with-closures"></a>
<a id="refactoring-using-functions"></a>
<a id="refactoring-with-closures-to-store-code"></a>

### Kapanışlar ile Ortamı Yakalama

Kapanışların inceleyeceğimiz ilk yönü, kapanışların tanımlandıkları ortamdaki değerleri daha sonra kullanmak üzere yakalayabilmeleridir. 
İşte senaryo: Bir tişört şirketi, e-posta listesindeki bir kişiye sık sık ücretsiz bir tişört hediye ediyor. 
E-posta listesindeki kişiler isteğe bağlı olarak profillerine favori renklerini ekleyebilirler. 
Ücretsiz tişörtü almak için seçilen kişinin profilinde en sevdiği renk varsa, 
o renk tişörtü alır. Kişi favori rengini belirtmemişse, şirketin şu anda en çok sahip olduğu rengi alır.

Bunu yapmanın birçok yolu vardır. Bu örnek için, `Red` ve `Blue` değişkenlerine sahip `ShirtColor` adlı bir `enum` kullanacağız. 
Şirketin envanteri, şu anda stokta bulunan tişörtleri temsil eden bir `Vec<ShirtColor>` içeren `shirts` adlı bir alana sahip bir
`Inventory` `struct`'ı ile temsil edilir. `Inventory` üzerinde tanımlanan `shirt_giveaway` metodu, ücretsiz gömlek alacak kişinin 
isteğe bağlı gömlek rengi tercihini alır ve kişinin alacağı gömlek rengini döndürür. Bu, Liste 13-1'de gösterilmektedir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-01/src/main.rs}}
```

<span class="caption">Liste 13-1: Tişört şirketi çekilişi</span>

`main`'de tanımlı `store`'da iki mavi ve bir kırmızı gömlek bulunmaktadır. 
Ardından, kırmızı gömlek tercihi olan bir kullanıcı ve herhangi bir tercihi olmayan bir kullanıcı için `giveaway` metodunu 
çağırmış olsun. Bu kodu çalıştırmak şunları yazdırırır:

```console
{{#include ../listings/ch13-functional-features/listing-13-01/output.txt}}
```

Yine, bu kod birçok şekilde uygulanabilir, ancak bu yol, bir kapanışları kullanan `giveaway` metodunun gövdesi dışında, 
daha önce öğrendiğiniz kavramları kullanır. `giveaway` metodu kullanıcı tercihi `Option<ShirtColor>`'ı alır ve üzerinde
`unwrap_or_else` çağrısı yapar. [`Option<T>` üzerindeKİ `unwrap_or_else` metodu][unwrap-or-else]<!-- ignore -->
metodu standart kütüphane tarafından tanımlanmıştır. Bir argüman alır: `T` (`Option<T>`'nin `Some` varyantında saklanan aynı tür, 
bu durumda bir `ShirtColor`) döndüren herhangi bir 
argümanı olmayan bir kapanıştır. `Option<T>`, `Some` varyantı ise `unwrap_or_else`, `Some` içindeki değeri döndürür.
`Option<T>` `None` varyantıysa, `unwrap_or_else` kapanışı çağırır ve ka tarafından döndürülen değeri döndürür.

Bu ilginçtir çünkü mevcut `Inventory` örneğinde `self.most_stocked()` fonksiyonunu çağıran bir kapanış geçirdik. 
Standart kütüphanenin, tanımladığımız `Inventory` veya `ShirtColor` türleri ya da bu senaryoda kullanmak istediğimiz mantık hakkında 
hiçbir şey bilmesine gerek yoktu. Kapanış, `self Inventory` örneğine değişmez bir referans yakaladı ve `unwrap_or_else` metoduna belirttiğimiz 
kodla birlikte aktardı. Fonksiyonlar kendi ortamlarını bu şekilde yakalayamazlar.

### Kapanış Tür Çıkarsaması ve Ek Açıklama

Fonksiyonlar ve kapanışlar arasında daha fazla fark vardır. Kapanışlar genellikle `fn` fonksiyonlarında olduğu gibi parametrelerin 
veya dönüş değerinin türlerini açıklamanızı gerektirmez. Kullanıcılarınıza açık bir arayüzün parçası oldukları için fonksiyonlarda tür ek 
açıklamaları gereklidir. Bu arayüzü katı bir şekilde tanımlamak, bir fonksiyonun kullandığı ve döndürdüğü değer türleri konusunda herkesin 
hemfikir olmasını sağlamak açısından önemlidir. Ancak kapanışlar bu şekilde açık bir arayüzde kullanılmazlar: 
değişkenlerde saklanırlar ve isimlendirilmeden ve kütüphanemizin kullanıcılarına gösterilmeden kullanılırlar.

Kapanışlar tipik olarak kısadır ve herhangi bir rastgele senaryodan ziyade yalnızca dar bir bağlamda önemlidir. 
Bu sınırlı bağlamlarda derleyici, çoğu değişkenin türünü çıkarabildiği gibi parametrelerin türlerini ve dönüş türünü de çıkarabilir 
(derleyicinin kapanış türü ek açıklamalarına ihtiyaç duyduğu nadir durumlar da vardır).

Değişkenlerde olduğu gibi, kesinlikle gerekli olandan daha ayrıntılı olma pahasına açıklığı ve netliği artırmak istiyorsak tür 
ek açıklamaları ekleyebiliriz. Bir kapanış için tür açıklamaları Liste 13-2'de gösterilen tanıma benzeyecektir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-02/src/main.rs:here}}
```

<span class="caption">Liste 13-2: Kapanıştaki parametre ve dönüş değeri türlerinin isteğe bağlı tür ek açıklamalarının eklenmesi</span>

Tür ek açıklamaları eklendiğinde, kapanışların söz dizimi fonksiyonların söz dizimine daha yakın görünür. 
Aşağıda, parametresine `1` ekleyen bir fonksiyon ile aynı davranışa sahip bir kapanış tanımının söz diziminin dikey bir karşılaştırması 
yer almaktadır. İlgili kısımları hizalamak için bazı boşluklar ekledik. Bu, kapanış söz diziminin, boruların kullanımı ve isteğe bağlı 
söz dizimi miktarı dışında fonksiyon söz dizimine nasıl benzediğini göstermektedir:

```rust,ignore
fn  add_one_v1   (x: u32) -> u32 { x + 1 }
let add_one_v2 = |x: u32| -> u32 { x + 1 };
let add_one_v3 = |x|             { x + 1 };
let add_one_v4 = |x|               x + 1  ;
```

İlk satır bir fonksiyon tanımını, ikinci satır ise tam açıklamalı bir kapanış tanımını göstermektedir. Üçüncü satırda kapanış tanımından 
tür ek açıklamaları kaldırılır ve dördüncü satırda kapanış gövdesinde yalnızca bir ifade olduğu için isteğe bağlı olan parantezler kaldırılır. 
Bunların hepsi, çağrıldıklarında aynı davranışı üretecek geçerli tanımlardır. Kapanışların çağrılması `add_one_v3` ve `add_one_v4`'ün derlenebilmesi 
için gereklidir çünkü türler kullanımlarından çıkarılacaktır.

Kapanış tanımları, parametrelerinin her biri ve dönüş değerleri için çıkarılan bir somut türe sahip olacaktır.
Örneğin, Liste 13-3 sadece parametre olarak aldığı değeri döndüren bir kısa kapanış tanımını göstermektedir. 
Bu kapanış, bu örneğin amaçları dışında çok kullanışlı değildir. Tanıma herhangi bir tür ek açıklaması eklemediğimize dikkat edin: 
ilk seferinde argüman olarak bir `String` ve ikinci seferinde bir `u32` kullanarak kapanışı iki kez çağırmaya çalışırsak, bir hata alırız.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-03/src/main.rs:here}}
```

<span class="caption">Liste 13-3: Türleri iki farklı türle çıkarılan bir kapanış çağrılmaya çalışılıyor</span>

Derleyici bu hatayı verir:

```console
{{#include ../listings/ch13-functional-features/listing-13-03/output.txt}}
```

`String` değeriyle `example_closure` öğesini ilk kez çağırdığımızda, derleyici `x`'in türünü ve kapanışın dönüş türünü
`String` olarak çıkarır. Bu türler daha sonra `example_closure`'daki kapanışa kilitlenir ve aynı kapanış ile farklı bir tür kullanmaya 
çalışırsak bir tür hatası alırız.

### Referansları Yakalama veya Sahipliği Taşıma

Kapanışlar çevrelerinden üç şekilde değer alabilir, bu da doğrudan bir fonksiyonun parametre alabileceği üç yolla eşleşir: 
değişmez olarak ödünç alma, değişebilir olarak ödünç alma ve sahiplik alma. Kapanış, fonksiyonun gövdesinin yakalanan değerlerle ne 
yaptığına bağlı olarak bunlardan hangisini kullanacağına karar verecektir.

Liste 13-4, `list` adlı vektöre değişmez bir ödünç alan bir kapanış tanımlar çünkü değeri yazdırmak için yalnızca değişmez 
bir ödünç almaya ihtiyaç duyar. Bu örnek aynı zamanda bir değişkenin bir kapanış tanımına bağlanabileceğini ve 
kapanışın daha sonra değişken adı ve parantezler kullanılarak sanki değişken adı bir fonksiyon adıymış gibi çağrılabileceğini göstermektedir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-04/src/main.rs}}
```

<span class="caption">Liste 13-4: Değişmez bir ödünç almayı yakalayan bir kapanış tanımlama ve çağırma</span>

`list`, kapanış tanımından önce, kapanış tanımından sonra ancak kapanış çağrılmadan önce ve kapanış çağrıldıktan sonra kod 
tarafından hala erişilebilir çünkü aynı anda birden fazla değişmez `list`'i ödünç alabiliriz. 
Bu kod derlenir, çalıştırılır ve yazdırılır:

```console
{{#include ../listings/ch13-functional-features/listing-13-04/output.txt}}
```

Ardından, Liste 13-5, kapanış gövdesi liste vektörüne bir eleman eklediği için kapanış tanımını değiştirilebilir bir ödünç 
almaya ihtiyaç duyacak şekilde değiştirir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-05/src/main.rs}}
```

<span class="caption">Liste 13-5: Değiştirilebilir bir ödünç almayı yakalayan bir kapanış tanımlama ve çağırma</span>

Bu kod derlenir, çalışır ve bunu yazdırır:

```console
{{#include ../listings/ch13-functional-features/listing-13-05/output.txt}}
```

Tanım ile `borrows_mutably` kapanışının çağrısı arasında artık bir `println!` olmadığına dikkat edin:
`borrows_mutably` tanımlandığında, `list`'e değiştirilebilir bir referans yakalar. Kapanış çağrıldıktan sonra, 
o noktadan sonra kapanışı tekrar kullanmadığımız için, `mutable` ödünç alma işlemi sona erer. Kapanış tanımı ve kapanış çağrısı arasında,
`print`'e değişmez bir ödünç almaya izin verilmez çünkü değişebilir bir ödünç alma olduğunda başka ödünç almaya izin verilmez. 
Nasıl bir hata mesajı alacağınızı görmek için oraya bir `println!` eklemeyi deneyin!

Kapanışın gövdesi kesinlikle sahipliğe ihtiyaç duymasa bile kapanışı ortamda kullandığı değerlerin sahipliğini almaya zorlamak istiyorsanız, 
parametre listesinden önce `move` anahtar sözcüğünü kullanabilirsiniz. Bu teknik çoğunlukla bir kapanışı yeni bir iş parçacığına aktarırken, 
verileri yeni iş parçacığına ait olacak şekilde taşımak için kullanışlıdır. Eşzamanlılık hakkında konuştuğumuz 16. Bölümde
`move` kapanışları ile ilgili daha fazla örneğimiz olacak.

<!-- Old headings. Do not remove or links may break. -->
<a id="storing-closures-using-generic-parameters-and-the-fn-traits"></a>
<a id="limitations-of-the-cacher-implementation"></a>

### Yakalanan Değerleri Kapanış ve `Fn` Tanımlarının Dışına Taşıma

Bir kapanış bir referansı yakaladığında veya bir değeri kapanışa taşıdığında, fonksiyonun gövdesindeki kod da 
fonksiyonun çağrılmasının bir sonucu olarak referanslara veya değerlere ne olacağını etkiler. 
Bir kapanış gövdesi, yakalanan bir değeri kapanış dışına taşıyabilir, yakalanan değeri değiştirebilir, 
yakalanan değeri ne taşıyabilir ne de değiştirebilir veya ortamdan hiçbir şey yakalayamaz. 
Bir kapanışın ortamdan değerleri yakalama ve işleme şekli, kapanışın hangi özellikleri uyguladığını etkiler. 
Tanımlar, fonksiyonların ve yapıların ne tür kapanışları kullanabileceklerini belirtme şeklidir.

Kapanışlar otomatik olarak bu `Fn` tanımlarından birini, ikisini ya da üçünü de eklenebilir bir şekilde sürekleyecektir:

1. `FnOnce` en az bir kez çağrılabilen kapanışlar için geçerlidir. Tüm kapanışlar bu tanımı sürekler, çünkü tüm kapanışlar çağrılabilir. 
  Bir kapanış yakalanan değerleri gövdesinin dışına taşırsa, bu kapanış yalnızca `FnOnce` tanımını sürekler ve diğer
  `Fn` tanımlarından hiçbirini süreklemez, çünkü yalnızca bir kez çağrılabilir.
2. `FnMut`, yakalanan değerleri gövdelerinin dışına taşımayan ancak yakalanan değerleri mutasyona uğratabilen kapanışlar için geçerlidir. 
  Bu kapanışlar birden fazla kez çağrılabilir.
3. `Fn`, yakalanan değerleri gövdelerinin dışına taşımayan ve yakalanan değerleri mutasyona uğratmayan kapamalar için geçerlidir. Bu kapanışlar, 
  ortamlarını değiştirmeden birden fazla kez çağrılabilir; bu da bir closure'ın aynı anda birden fazla kez çağrılması gibi durumlarda önemlidir.
  Çevrelerinden hiçbir şey yakalamayan kapanışlar `Fn`'i sürekler.

Liste 13-6'da kullandığımız `Option<T>` üzerindeki `unwrap_or_else` metodunun tanımına bakalım:

```rust,ignore
impl<T> Option<T> {
    pub fn unwrap_or_else<F>(self, f: F) -> T
    where
        F: FnOnce() -> T
    {
        match self {
            Some(x) => x,
            None => f(),
        }
    }
}
```

`T`'nin, `Option`'ın `Some` varyantındaki değerin türünü temsil eden yaygın tür olduğunu hatırlayın. 
Bu `T` türü aynı zamanda `unwrap_or_else` fonksiyonunun dönüş türüdür: örneğin bir `Option<String>` üzerinde `unwrap_or_else` çağrısı yapan 
kod bir `String` elde edecektir.

Daha sonra, `unwrap_or_else` fonksiyonunun ek bir yaygın tür parametresi olduğuna dikkat edin: `F`. `F` türü,
`unwrap_or_else` fonksiyonunu çağırırken sağladığımız kapanış olan `f` adlı parametrenin tipidir.

`F` yaygın türünde belirtilen tanım bağlılığı `FnOnce() -> T`'dir, yani `F` en az bir kez çağrılabilmeli, hiçbir argüman almamalı ve 
bir `T` döndürmelidir. Tanım bağlılığında `FnOnce` kullanılması, `unwrap_or_else` fonksiyonunun `f`'yi yalnızca en fazla bir kez 
çağıracağı kısıtlamasını ifade eder. `unwrap_or_else`'in gövdesinde; `Option` `Some` ise `f`'nin çağrılmayacağını görebiliriz.
`Option` `None` ise, `f` bir kez çağrılacaktır. Tüm kapanışlar `FnOnce`'ı uyguladığından, `unwrap_or_else` en farklı kapanış türlerini 
kabul eder ve olabildiğince esnektir.

> Not: Fonksiyonlar da `Fn` tanımının üçünü de sürekleyebilir. Yapmak istediğimiz şey ortamdan bir değer yakalamayı gerektirmiyorsa, 
> `Fn` tanımlarından birini sürekleyen bir şeye ihtiyaç duyduğumuz yerde kapanış yerine fonksiyonun adını kullanabiliriz. 
> Örneğin, `Option<Vec<T>>` değeri üzerinde, değer `None` ise yeni ve boş bir vektör elde etmek için 
> `unwrap_or_else(Vec::new)` çağrısı yapabiliriz.

Şimdi bunun nasıl farklılaştığını görmek için dilimler üzerinde tanımlanan standart kütüphane metodu `sort_by_key`'e bakalım.
`FnMut`'i sürekleyen bir kapanış alır. Kapanış, dikkate alınan dilimdeki geçerli öğeye bir referans olmak üzere bir argüman alır ve 
sıralanabilen `K` türünde bir değer döndürür. Bu fonksiyon, bir dilimi her bir öğenin belirli bir özelliğine göre sıralamak 
istediğinizde kullanışlıdır. Liste 13-7'de, `Rectangle` örneklerinden oluşan bir listemiz var ve bunları `width` niteliklerine göre 
düşükten yükseğe doğru sıralamak için `sort_by_key`'i kullanıyoruz:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-07/src/main.rs}}
```

<span class="caption">Liste 13-7: `Rectangle` örneklerinden oluşan bir listeyi `width` değerlerine göre sıralamak için `sort_by_key`'i ve
kapanışları kullanma</span>

Bu kod şunları yazdırır:

```console
{{#include ../listings/ch13-functional-features/listing-13-07/output.txt}}
```

`sort_by_key`'in `FnMut` kapanışı alacak şekilde tanımlanmasının nedeni, kapanışı birden çok kez çağırmasıdır: 
dilimdeki her öğe için bir kez. `|r| r.width` kapanışı, çevresinden herhangi bir şeyi yakalamaz, mutasyona uğratmaz veya dışarı taşımaz, 
bu nedenle tanım bağlılığı gereksinimlerini karşılar.

Buna karşılık, Liste 13-8, ortamdan bir değer taşıdığı için yalnızca `FnOnce` uygulayan bir kapanış örneğini gösterir. 
Derleyici bu kapanışı `sort_by_key` ile kullanmamıza izin vermez:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-08/src/main.rs}}
```

<span class="caption">Liste 13-8: `sort_by_key` ile `FnOnce` kapanışını kullanma girişimi</span>

Bu, `list`'i sıralarken `sort_by_key`'in kaç kez çağrıldığını saymaya çalışmak için uydurulmuş, karmaşık bir yoldur (işe yaramaz). 
Bu kod bu sayımı, kapanış ortamından bir `String` olan değeri `sort_operations` vektörüne iterek yapmaya çalışır. 
Kapanış değeri yakalar ve ardından değerin sahipliğini `sort_operations` vektörüne aktararak değeri kapanış dışına taşır. 
Bu kapanış bir kez çağrılabilir; ikinci kez çağırmaya çalışmak işe yaramaz çünkü değer artık `sort_operations`'a tekrar itilecek ortamda 
olmayacaktır! Bu nedenle, bu kapanış yalnızca `FnOnce`'ı sürekler. Bu kodu derlemeye çalıştığımızda, değerin kapanış dışına taşınamayacağını 
çünkü kapanıın `FnMut`'u süreklemesi gerektiğini belirten bir hata alırız:

```console
{{#include ../listings/ch13-functional-features/listing-13-08/output.txt}}
```

Hata, değeri ortamın dışına taşıyan kapatma gövdesindeki satıra işaret eder. Bunu düzeltmek için, kapanış gövdesini 
değerleri ortamın dışına taşımayacak şekilde değiştirmemiz gerekir. `sort_by_key`'in kaç kez çağrıldığıyla ilgileniyorsak, 
ortamda bir sayaç tutmak ve kapanış gövdesinde değerini artırmak bunu hesaplamanın daha kolay bir yoludur. 
Liste 13-9'daki kapanış `sort_by_key` ile çalışır çünkü sadece `num_sort_operations` sayacına değiştirilebilir bir referans yakalar 
ve bu nedenle birden fazla kez çağrılabilir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-09/src/main.rs}}
```

<span class="caption">Liste 13-9: `sort_by_key` ile bir `FnMut` kapanışının kullanılmasına izin verilir</span>

`Fn` tanımları, kapanışlardan yararlanan fonksiyonları veya türleri tanımlarken veya kullanırken önemlidir. 
Bir sonraki bölümde yineleyiciler ele alınmaktadır ve birçok yineleyici yöntemi kapanış argümanları alır.
Yineleyicileri keşfederken kapanışlarla ilgili bu ayrıntıları aklınızda tutun!

[unwrap-or-else]: ../std/option/enum.Option.html#method.unwrap_or_else
