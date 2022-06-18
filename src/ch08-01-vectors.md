## Vektörlerle Değer Listelerini Saklama

Bakacağımız ilk koleksiyon türü, vektör olarak da bilinen `Vec<T>`'dir. Vektörler, tüm değerleri bellekte 
yan yana koyan tek bir veri yapısında birden fazla değeri saklamanıza izin verir. Vektörler yalnızca aynı türdeki 
değerleri saklayabilir. Bir dosyadaki metin satırları veya bir alış veriş sepetindeki ürünlerin fiyatları gibi bir 
öğe listeniz olduğunda kullanışlıdırlar.

### Yeni bir Vektör Oluşturma

Yeni bir boş vektör oluşturmak için Liste 8-1'de gösterildiği gibi 
`Vec::new` fonksiyonunu çağırıyoruz.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-01/src/main.rs:here}}
```

<span class="caption">Liste 8-1: `i32` türündeki değerleri tutmak için yeni, 
boş bir vektör oluşturma</span>

Buraya bir tür ek açıklaması eklediğimize dikkat edin. Bu vektöre herhangi bir değer eklemediğimiz için, 
Rust ne tür öğeler depolamak istediğimizi bilmiyor. Bu önemli bir noktadır. Vektörler yaygınlar kullanılarak uygulanır; 
Bölüm 10'da yaygınları kendi türlerinizle nasıl kullanacağınızı ele alacağız. Şimdilik, standart kütüphane tarafından sağlanan 
`Vec<T>` türünün herhangi bir türü tutabileceğini bilin. Belirli bir türü tutmak için bir vektör oluşturduğumuzda, 
türü köşeli parantezler içinde belirtebiliriz. Liste 8-1'de, Rust'a `v` içindeki `Vec<T>`'nin `i32` tipinde elemanlar tutacağını söyledik.

Daha sık olarak, ilk değerlerle bir `Vec<T>` oluşturursunuz ve Rust saklamak istediğiniz değerin türünü çıkarır, 
bu nedenle bu tür ek açıklamasını yapmanız nadiren gerekir. Rust, verdiğiniz değerleri tutan yeni bir vektör oluşturacak olan 
`vec!` makrosunu uygun bir şekilde sağlar. Liste 8-2, `1`, `2` ve `3` değerlerini tutan yeni bir `Vec<i32>` oluşturur. Tam sayı 
türü `i32`'dir çünkü bu, Bölüm 3'ün [“Veri Türleri”][data-types]<!-- ignore -->  bölümünde tartıştığımız gibi varsayılan tam sayı türüdür.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-02/src/main.rs:here}}
```

<span class="caption">Liste 8-2: Değerler içeren yeni bir vektör oluşturma</span>

`i32` değerlerini verdiğimiz için, Rust `v` türünün `Vec<i32>` olduğu sonucunu çıkarabilir ve tür ek açıklamasına gerek kalmaz. 
Sonraki başlıkta bir vektörün nasıl değiştirileceğine bakacağız.

### Bir Vektörü Güncelleme

Bir vektör oluşturmak ve daha sonra ona eleman eklemek için `push` metodunu kullanabiliriz,
Liste 8-3'te gösterildiği gibi.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-03/src/main.rs:here}}
```

<span class="caption">Listing 8-3: Using the `push` method to add values to a
vector</span>


Herhangi bir değişkende olduğu gibi, değerini değiştirebilmek istiyorsak, şunu yapmamız gerekir:
Bölüm 3'te tartışıldığı gibi `mut` anahtar sözcüğünü kullanarak değiştirilebilir hale getirmelisiniz. 

Sayılar içine yerleştirdiğimiz tüm öğeler `i32` türündedir ve Rust bunu verilerden çıkarır, yani
`Vec<i32>` ek açıklamasına ihtiyacımız yoktur.

### Vektörlerin Elemanlarını Okuma

Bir vektörde saklanan bir değere başvurmanın iki yolu vardır: indeksleme veya
`get` metodunu kullanma. Aşağıdaki örneklerde, daha fazla netlik için bu fonksiyonlardan 
döndürülen değerlerin türlerini açıkladık.


```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-04/src/main.rs:here}}
```

<span class="caption">Liste 8-4: Bir vektördeki bir öğeye erişmek için indekslemeyi veya 
`get` metodunu kullanma</span>

Burada birkaç ayrıntıya dikkat edin. Üçüncü elemanı elde etmek için `2` indeks değerini kullanırız çünkü vektörler sıfırdan 
başlayarak sayıya göre indekslenir ve `[]` kullanmak bize indeks değerindeki elemana bir referans verir. `get` metodunu argüman 
olarak geçirilen indeksle kullandığımızda, `match` ile kullanabileceğimiz bir `Option<&T>` elde ederiz.

Rust'ın bir öğeye başvurmak için bu iki yolu sağlamasının nedeni, mevcut öğelerin aralığı dışında bir 
indeks değeri kullanmaya çalıştığınızda programın nasıl davranacağını seçebilmenizdir. 
Örnek olarak, beş elemanlı bir vektörümüz olduğunda ve ardından Liste 8-5'te gösterildiği gibi her bir teknikle 
`100` indeksindeki bir elemana erişmeye çalıştığımızda ne olacağını görelim.

```rust,should_panic,panics
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-05/src/main.rs:here}}
```

<span class="caption">Liste 8-5: Beş element içeren bir vektörde indeks 
`100`'deki elemana erişmeye çalışmak</span>

Bu kodu çalıştırdığımızda ilk `[]` metodu var olmayan bir elemente referans verdiği için programın paniğe kapılmasına neden olacaktır. 
Bu yöntem en iyi şekilde, vektörün sonundaki bir öğeye erişme girişimi olduğunda programınızın çökmesini istediğinizde kullanılır.

`get` metodu vektörün dışında bir indeks iletildiğinde panik yapmadan `None` döndürür. 
Normal koşullar altında, vektör aralığının dışındaki bir öğeye erişim ara sıra gerçekleşebiliyorsa, 
bu metodu kullanırsınız. Kodunuz, Bölüm 6'da tartışıldığı gibi, `Some(&element)` veya `None`'a sahip olmayı işlemek için bir mantığa sahip 
olacaktır. Örneğin, dizin bir sayı giren bir kişiden geliyor olabilir. Yanlışlıkla çok büyük bir sayı girerlerse ve program `None` değeri alırsa,
kullanıcıya geçerli vektörde kaç öğe olduğunu söyleyebilir ve onlara geçerli bir değer girmeleri için bir şans daha verebilirsiniz. 
Bu, bir yazım hatası nedeniyle programı çökertmekten daha kullanıcı dostu olurdu!

Programın geçerli bir referansı olduğunda, ödünç alma denetleyicisi, bu referansın ve vektörün içeriğine yönelik diğer referansların geçerli 
kalmasını sağlamak için mülkiyet ve ödünç alma kurallarını (Bölüm 4'te ele alınmıştır) uygular. Aynı kapsamda değiştirilebilir ve değişmez 
referanslara sahip olamayacağınızı belirten kuralı hatırlayın. Bu kural, bir vektördeki ilk öğeye değişmez bir referans tuttuğumuz ve 
sona bir öğe eklemeye çalıştığımız Liste 8-6'da geçerlidir. Fonksiyonda daha sonra bu öğeye başvurmaya çalışırsak, bu program çalışmayacaktır:


```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-06/src/main.rs:here}}
```

<span class="caption">Liste 8-6: Bir öğeye referans tutarken bir vektöre öğe eklemeye çalışmak</span>

Bu kodu derlemek şu hataya neden olur:


```console
{{#include ../listings/ch08-common-collections/listing-08-06/output.txt}}
```

Liste 8-6'daki kod çalışması gerekiyormuş gibi görünebilir: ilk elemana yapılan bir referans vektörün sonundaki değişiklikleri neden önemsesin? 
Bu hata vektörlerin çalışma şeklinden kaynaklanmaktadır: vektörler değerleri bellekte yan yana koyduğu için, vektörün sonuna yeni bir 
eleman eklemek, vektörün şu anda depolandığı yerde tüm elemanları yan yana koymak için yeterli yer yoksa, yeni bellek ayırmayı ve eski 
elemanları yeni alana kopyalamayı gerektirebilir. Bu durumda, ilk elemanın referansı ayrılmış belleğe işaret ediyor olacaktır. 
Ödünç alma kuralları programların bu duruma düşmesini engeller.

> Not: `Vec<T>` türünün sürekleme ayrıntıları hakkında daha fazla bilgi için bkz. [“The Rustonomicon”][nomicon].

### Bir Vektördeki Değerler Üzerinde Yineleme

Bir vektördeki her bir öğeye sırayla erişmek için, her seferinde bir öğeye erişmek üzere indisleri kullanmak yerine 
tüm öğeler arasında yineleme yaparız. Liste 8-7, `i32` değerlerinden oluşan bir vektördeki her bir öğeye 
değişmez referanslar almak ve bunları yazdırmak için bir `for` döngüsünün nasıl kullanılacağını gösterir.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-07/src/main.rs:here}}
```
<span class="caption">Liste 8-7: Bir `for` döngüsü kullanarak öğeler üzerinde yineleme yaparak bir vektördeki her bir öğeyi yazdırma</span>

Ayrıca, tüm öğelerde değişiklik yapmak için değişebilir bir vektördeki her bir öğeye yönelik değişebilir referanslar 
üzerinde yineleme yapabiliriz. Liste 8-8'deki `for` döngüsü her öğeye `50` ekleyecektir.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-08/src/main.rs:here}}
```

<span class="caption">Liste 8-8: Bir vektördeki öğelere yönelik değiştirilebilir referanslar üzerinde yineleme</span>

Değiştirilebilir referansın ifade ettiği değeri değiştirmek için, `+=` operatörünü kullanmadan önce `i` içindeki değere ulaşmak için 
`*` referansı alma operatörünü kullanmamız gerekir. Referansı alma operatörü hakkında daha fazla bilgiyi Bölüm 15'teki  [“Referansı Alma Operatörü ile Değere Bakan İşaretçiyi Takip Etme”][deref]<!-- ignore --> kısmında bulacağız.

İster değişmez ister değişebilir olsun, bir vektör üzerinde yineleme yapmak, ödünç denetleyicisinin kuralları nedeniyle güvenlidir. 
Liste 8-7 ve Liste 8-8'deki `for` döngüsü gövdelerine öğe eklemeye veya çıkarmaya çalışırsak, 
Liste 8-6'daki kodla aldığımıza benzer bir derleyici hatası alırız. `for` döngüsünün tuttuğu vektör referansı tüm vektörün aynı anda 
değiştirilmesini engeller.

### Birden Fazla Türü Saklamak için `enum` Kullanma

Vektörler yalnızca aynı türden değerleri depolayabilir. Bu elverişsiz olabilir; farklı türlerdeki öğelerin bir 
listesini saklamaya ihtiyaç duyan kullanım durumları kesinlikle vardır. Neyse ki, bir `enum`'un varyantları aynı `enum` tipi altında tanımlanır, 
bu nedenle farklı tiplerdeki öğeleri temsil etmek için tek bir tipe ihtiyaç duyduğumuzda, bir `enum` tanımlayabilir ve kullanabiliriz!

Örneğin, satırdaki sütunlardan bazılarının tam sayılar, bazılarının kayan noktalı sayılar ve bazılarının da dizgiler içerdiği bir elektronik 
tablodaki bir satırdan değerler almak istediğimizi varsayalım. Varyantları farklı değer türlerini tutacak bir `enum` tanımlayabiliriz ve 
tüm `enum` varyantları aynı tür olarak kabul edilir. Daha sonra bu `enum`'u tutmak için bir vektör oluşturabiliriz ve 
böylece sonuçta farklı türleri tutabiliriz. Bunu Liste 8-9'da gösterdik.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-09/src/main.rs:here}}
```

<span class="caption">Liste 8-9: Farklı türlerdeki değerleri tek bir vektörde saklamak için `enum` tanımlama</span>

Rust'ın derleme zamanında vektörde hangi türlerin olacağını bilmesi gerekir, böylece her bir öğeyi depolamak için 
yığın üzerinde tam olarak ne kadar bellek gerekeceğini bilir. Ayrıca bu vektörde hangi türlere izin verildiği konusunda 
da açık olmalıyız. Rust bir vektörün herhangi bir türü tutmasına izin verseydi, 
türlerden birinin veya daha fazlasının vektörün elemanları üzerinde gerçekleştirilen işlemlerde hatalara neden olma ihtimali olurdu. 
Bir `enum` ve bir `match` ifadesi kullanmak, Bölüm 6'da tartışıldığı gibi, Rust'ın derleme zamanında olası her durumun 
ele alınmasını sağlayacağı anlamına gelir.

Bir programın çalışma zamanında bir vektörde depolamak için alacağı kapsamlı tür kümesini bilmiyorsanız, 
`enum` işe yaramayacaktır. Bunun yerine, Bölüm 17'de ele alacağımız bir `trait` tanımını kullanabilirsiniz.

Vektörleri kullanmanın en yaygın yollarından bazılarını tartıştığımıza göre, standart kütüphane tarafından `Vec<T>` üzerinde tanımlanan 
birçok yararlı yöntem için [API dokümantasyonunu][vec-api]<!-- ignore --> gözden geçirdiğinizden emin olun. 
Örneğin, `push`'a ek olarak, `pop` yöntemi son elemanı kaldırır 
ve döndürür.

### Bir Vektörü Düşürmek Elemanlarını Düşürür

`struct`'lar gibi, bir vektör de kapsam dışına çıktığında, Liste 8-10'da açıklandığı gibi serbest bırakılır.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-10/src/main.rs:here}}
```

<span class="caption">Liste 8-10: Vektörün ve elemanlarının nereye bırakıldığını gösterme</span>

Vektör bırakıldığında, tüm içeriği de bırakılır, yani tuttuğu tam sayılar temizlenir. Ödünç alma denetleyicisi, 
bir vektörün içeriğine yapılan referansların yalnızca vektörün kendisi geçerli olduğu sürece kullanılmasını sağlar.

Bir sonraki koleksiyon türüne geçelim: `String`!

[data-types]: ch03-02-data-types.html#data-types
[nomicon]: ../nomicon/vec/vec.html
[vec-api]: ../std/vec/struct.Vec.html
[deref]: ch15-02-deref.html#following-the-pointer-to-the-value-with-the-dereference-operator
