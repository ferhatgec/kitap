## Yığın Üzerindeki Verilere İşaret Etmek için `Box<T>` Kullanma

En basit akıllı işaretçi, türü `Box<T>` olarak yazılan bir çeşit *kutudur*. 
Kutular, verileri yığıt yerine yığın (heap) üzerinde saklamanıza olanak tanır. 
Yığıtta kalan şey, yığın verisinin işaretçisidir. Yığıt ve yığın arasındaki farkı incelemek için Bölüm 4'e bakın.

Kutular, verilerini yığıt yerine yığın üzerinde saklamak dışında performans ek yüküne sahip değildir. 
Ancak çok fazla ekstra yetenekleri de yoktur. Onları en çok bu durumlarda kullanacaksınız:

* Boyutu derleme zamanında bilinemeyen bir türünüz olduğunda ve bu türden bir değeri tam boyut gerektiren 
  bir bağlamda kullanmak istediğinizde
* Büyük miktarda veriye sahip olduğunuzda ve sahipliği devretmek istediğinizde ancak bunu yaparken verilerin 
  kopyalanmayacağından emin olmak istediğinizde
* Bir değere sahip olmak istediğinizde ve belirli bir türde olmasından ziyade yalnızca belirli bir tanımı sürekleyen 
  bir tür olmasını önemsediğinizde

İlk durumu [“Kutularla Özyinelemeli Türleri Etkinleştirme”](#enabling-recursive-types-with-boxes)<!-- ignore -->
bölümünde göstereceğiz. İkinci durumda, büyük miktarda verinin sahipliğinin aktarılması uzun sürebilir
çünkü veriler yığıt üzerinde kopyalanır. Bu durumda performansı artırmak için, büyük miktardaki veriyi 
yığında bir kutu içinde saklayabiliriz. Ardından, yalnızca küçük miktarda işaretçi verisi yığıt 
üzerinde kopyalanırken, referans verdiği veriler yığın üzerinde tek bir yerde kalır. 
Üçüncü durum özellik nesnesi olarak bilinir ve Bölüm 17,
[“Farklı Türlerde Değerlere İzin Veren Tanım Nesnelerini Kullanma”][trait-objects]<!--
ignore --> başlıklı bir bölümün tamamını bu konuya ayırmıştır. Yani burada öğrendiklerinizi 
Bölüm 17'de tekrar uygulayacaksınız!

### Verileri Yığın Üzerinde Saklamak için `Box<T>` Kullanma

`Box<T>` için yığın depolama kullanım durumunu tartışmadan önce, 
söz dizimini ve bir `Box<T>` içinde depolanan değerlerle nasıl etkileşimde bulunacağımızı ele alacağız.

Liste 15-1, bir `i32` değerini yığın üzerinde saklamak için bir kutunun nasıl kullanılacağını gösterir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch15-smart-pointers/listing-15-01/src/main.rs}}
```

<span class="caption">Liste 15-1: Bir kutu kullanarak bir `i32` değerini yığın üzerinde saklama</span>

`b` değişkenini, yığın üzerinde ayrılmış olan `5` değerine işaret eden bir `Box` değerine sahip 
olacak şekilde tanımlarız. Bu program `b = 5` yazdıracaktır; bu durumda, kutudaki verilere, 
bu veriler yığıtta olsaydı yapacağımız gibi erişebiliriz. Tıpkı sahip olunan herhangi bir değer gibi,
`main`'in sonunda `b`'nin yaptığı gibi bir kutu kapsam dışına çıktığında, bellekten silinecektir. 
Bellekten silme işlemi hem kutu (yığıt üzerinde saklanır) hem de işaret ettiği veri 
(yığın üzerinde saklanır) için gerçekleşir.

Yığına tek bir değer koymak çok kullanışlı değildir, bu nedenle kutuları bu şekilde tek başlarına çok sık 
kullanmazsınız. Varsayılan olarak saklandıkları yığıtta tek bir `i32` gibi değerlere sahip olmak, 
çoğu durumda daha uygundur. Kutuların, kutular olmasaydı tanımlamamıza izin verilmeyecek türleri 
tanımlamamıza izin verdiği bir duruma bakalım.

### Kutularla Özyinelemeli Türleri Etkinleştirme

*Özyinelemeli türdeki* bir değer, kendisinin bir parçası olarak aynı türde başka bir değere sahip olabilir. 
Özyinelemeli türler bir sorun teşkil eder çünkü derleme zamanında Rust'ın bir türün ne kadar yer 
kapladığını bilmesi gerekir. Ancak, özyinelemeli türlerin değerlerinin iç içe geçmesi teorik 
olarak sonsuza kadar devam edebilir, bu nedenle Rust değerin ne kadar alana ihtiyaç duyduğunu bilemez. 
Kutular bilinen bir boyuta sahip olduğundan, özyinelemeli tür tanımına bir kutu ekleyerek özyinelemeli 
türleri etkinleştirebiliriz.

Bir özyinelemeli tür örneği olarak, *cons listesini* inceleyelim. 
Bu, fonksiyonel programlama dillerinde yaygın olarak bulunan bir veri türüdür. 
Tanımlayacağımız *cons listesi* türü, özyineleme dışında basittir; bu nedenle, üzerinde çalışacağımız 
örnekteki kavramlar, özyinelemeli türleri içeren daha karmaşık durumlarla karşılaştığınızda yararlı olacaktır.

### *Cons Listesi* Hakkında Daha Fazla Bilgi

Cons listesi, Lisp programlama dili ve lehçelerinden gelen ve iç içe geçmiş çiftlerden oluşan bir 
veri yapısıdır. Adı, Lisp'te iki argümanından yeni bir çift oluşturan `cons` fonksiyonundan (“construct function”'ın kısaltması) 
gelir. Bir değer ve başka bir çiftten oluşan bir çift üzerinde `cons` çağırarak, 
özyinelemeli çiftlerden oluşan *cons listeleri* oluşturabiliriz.

Örneğin, burada 1, 2, 3 listesini içeren ve her bir çifti parantez içinde olan bir *cons listesinin* 
sözde kod gösterimi yer almaktadır:

```text
(1, (2, (3, Nil)))
```

Bir cons listesindeki her öğe iki öğe içerir: geçerli öğenin değeri ve bir sonraki öğe. 
Listedeki son öğe, bir sonraki öğe olmaksızın yalnızca `Nil` adlı bir değer içerir. 
Bir cons listesi, `cons` fonksiyonunun özyinelemeli olarak çağrılmasıyla oluşturulur. 
Özyinelemenin temel durumunu gösteren yaygın kullanılan ad `Nil`'dir. 
Bunun Bölüm 6'daki “null” or “nil” kavramıyla aynı olmadığına dikkat edin; bu kavram geçersiz veya 
olmayan bir değerdir.

Cons listesi Rust'ta yaygın olarak kullanılan bir veri yapısı değildir. 
Rust'ta bir öğe listesine sahip olduğunuzda çoğu zaman `Vec<T>` kullanmak daha iyi bir seçimdir. 
Diğer, daha karmaşık özyinelemeli veri tipleri çeşitli durumlarda kullanışlıdır, 
ancak bu bölümde cons listesi ile başlayarak, kutuların fazla dikkat dağıtmadan özyinelemeli 
bir veri tipi tanımlamamıza nasıl izin verdiğini keşfedebiliriz.

Liste 15-2, cons listesi için bir `enum` tanımını içerir. 
Bu kodun henüz derlenmeyeceğini unutmayın çünkü `List` türünün bilinen bir boyutu yoktur, 
bunu daha sonra göstereceğiz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch15-smart-pointers/listing-15-02/src/main.rs:here}}
```

<span class="caption">Liste 15-2: `i32` değerlerinden oluşan bir cons listesi veri yapısını temsil etmek için bir
`enum` tanımlamaya yönelik ilk girişim</span>

> Not: Bu örneğin amaçları doğrultusunda yalnızca `i32` değerlerini tutan bir cons listesi yapıyoruz.
> Bölüm 10'da tartıştığımız gibi, herhangi bir türden değerleri saklayabilen bir cons liste türü tanımlamak için
> yaygınları kullanabilirdik.

`List` türünü `1, 2, 3` listesini saklamak için kullanırsak, Liste 15-3'teki gibi bir kod yazmamız gerekir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch15-smart-pointers/listing-15-03/src/main.rs:here}}
```

<span class="caption">Listing 15-3: `1, 2, 3` listesini saklamak için `List` `enum`'unu kullanma</span>

İlk `Cons` değeri `1`'i ve başka bir `` değerini tutar. 
Bu `List` değeri, `2` ve başka bir `List` değerini tutan başka bir `Cons` değeridir. 
Bu `List` değeri, `3`'ü ve bir diğer `List` değerini tutan bir `Cons` değeridir ve son 
olarak listenin sonunu işaret eden özyinelemesiz tür olan `Nil`'dir.

Liste 15-3'teki kodu derlemeye çalışırsak, Liste 15-4'te gösterilen hatayı alırız:

```console
{{#include ../listings/ch15-smart-pointers/listing-15-03/output.txt}}
```

<span class="caption">Liste 15-4: Özyinelemeli bir `enum` tanımlamaya çalıştığımızda aldığımız hata</span>

Hata, bu türün “sonsuz boyutu” olduğunu gösterir. Bunun nedeni, `List`'i özyinelemeli bir değişkenle 
tanımlamış olmamızdır: doğrudan kendisinin başka bir değerini tutar. Sonuç olarak, Rust
`List` değerini saklamak için ne kadar alana ihtiyacı olduğunu bulamaz. Bu hatayı neden aldığımızı inceleyelim. 
İlk olarak, Rust'ın özyinelemeli olmayan bir türdeki bir değeri saklamak için ne kadar alana ihtiyaç 
duyduğuna nasıl karar verdiğine bakacağız.

#### Özyinelemeli Olmayan Bir Türün Boyutunu Hesaplama

Bölüm 6'da `enum` tanımlarını tartışırken Liste 6-2'de tanımladığımız `Message` `enum`'unu hatırlayın:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-02/src/main.rs:here}}
```

Bir `Message` değeri için ne kadar alan ayrılacağını belirlemek için Rust, hangi varyantın en fazla alana ihtiyaç 
duyduğunu görmek için varyantların her birini gözden geçirir. Rust, `Message::Quit`'in herhangi bir alana ihtiyaç 
duymadığını, `Message::Move`'un iki `i32` değerini depolamak için yeterli alana ihtiyaç duyduğunu ve benzerlerini görür. 
Yalnızca bir değişken kullanılacağından, bir `Message` değerinin ihtiyaç duyacağı en fazla alan, değişkenlerinin en 
büyüğünü depolamak için gereken alandır.

Rust, Liste 15-2'deki `List` `enum`'u gibi özyinelemeli bir türün ne kadar alana ihtiyaç duyduğunu belirlemeye 
çalıştığında ortaya çıkan durumla bunu karşılaştırın. Derleyici, `i32` türünde bir değer ve `List` türünde bir değer tutan
`Cons` değişkenine bakarak başlar. Bu nedenle `Cons`, `i32`'nin boyutu artı bir `List`'in boyutuna eşit 
miktarda alana ihtiyaç duyar. Derleyici, `List` türünün ne kadar belleğe ihtiyacı olduğunu bulmak için `Cons` 
değişkeninden başlayarak tüm sıralı değişkenlere bakar. `Cons` değişkeni `i32` türünde bir değer ve
`List` türünde bir değer tutar ve bu işlem Şekil 15-1'de gösterildiği gibi *sonsuza kadar* devam eder.

<img alt="Sonsuz bir Cons listesi" src="img/trpl15-01.svg" class="center" style="width: 50%;" />

<span class="caption">Şekil 15-1: Sonsuz `Cons` varyantlarından oluşan sonsuz bir `List`</span>

#### Boyutu Bilinen Özyinelemeli Bir Tür Elde Etmek için `Box<T>` Kullanmak

Rust, özyinelemeli olarak tanımlanan türler için ne kadar alan ayırması gerektiğini bulamadığından, 
derleyici bu yararlı öneriyle birlikte bir hata verir:

<!-- manual-regeneration
after doing automatic regeneration, look at listings/ch15-smart-pointers/listing-15-03/output.txt and copy the relevant line
-->

```text
help: insert some indirection (e.g., a `Box`, `Rc`, or `&`) to make `List` representable
  |
2 |     Cons(i32, Box<List>),
  |               ^^^^    ^
```

Bu öneride “dolayı yönlendirme” (“indirection”), bir değeri doğrudan depolamak yerine, 
veri yapısını değiştirerek değeri dolaylı olarak depolamamız gerektiği ve bunun yerine değere bir işaretçi 
depolamamız gerektiği anlamına gelir.

`Box<T>` bir işaretçi olduğundan, Rust her zaman bir `Box<T>`'nin ne kadar alana ihtiyacı olduğunu bilir: 
bir işaretçinin boyutu, işaret ettiği veri miktarına bağlı olarak değişmez. Bu, doğrudan başka bir
`List` değeri yerine `Cons` değişkeninin içine bir `Box<T>` koyabileceğimiz anlamına gelir.
`Box<T>`, `Cons` değişkeninin içinde olmak yerine yığın üzerinde olacak bir sonraki `List` değerine 
işaret edecektir. Kavramsal olarak, hala diğer listeleri tutan listelerle oluşturulmuş bir listemiz var, 
ancak bu uygulama artık öğeleri birbirinin içine yerleştirmek yerine yan yana yerleştirmeye benziyor.

Liste 15-2'deki `List` `enum`'unun tanımını ve Liste 15-3'teki `List` kullanımını, derlenecek olan 
Liste 15-5'teki kodla değiştirebiliriz:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch15-smart-pointers/listing-15-05/src/main.rs}}
```

<span class="caption">Liste 15-5: Bilinen bir boyuta sahip olmak için `Box<T>` kullanan `List` tanımı</span>

`Cons` varyantı, bir `i32` boyutuna ve kutunun işaretçi verilerini depolamak için alana ihtiyaç duyar.
`Nil` değişkeni hiçbir değer saklamaz, bu nedenle `Cons` değişkeninden daha az alana ihtiyaç duyar. 
Artık herhangi bir `List` değerinin bir `i32` boyutu artı bir kutunun işaretçi verisinin boyutunu kaplayacağını biliyoruz. 
Bir kutu kullanarak sonsuz, özyinelemeli zinciri kırdık, böylece derleyici bir `List` değerini saklamak için gereken 
boyutu bulabilir. Şekil 15-2, `Cons` varyantının şimdi nasıl göründüğünü göstermektedir.

<img alt="Sonlu bir Cons listesi" src="img/trpl15-02.svg" class="center" />

<span class="caption">Şekil 15-2: `Cons` `Box`'ı tuttuğu için sonsuz boyutlu olmayan bir `List`</span>

Kutular yalnızca dolaylama ve heap tahsisi sağlar; diğer akıllı işaretçi türlerinde göreceğimiz gibi başka özel 
yetenekleri yoktur. Ayrıca, bu özel yeteneklerin neden olduğu performans ek yüküne de sahip değildirler, 
bu nedenle dolaylamanın ihtiyaç duyduğumuz tek özellik olduğu cons listesi gibi durumlarda yararlı olabilirler. 
Bölüm 17'de kutular için daha fazla kullanım alanına da bakacağız.

`Box<T>` türü akıllı bir işaretçidir çünkü `Box<T>` değerlerinin referanslar gibi ele alınmasını sağlayan
`Deref` tanımını sürekler. `Box<T>` değeri kapsam dışına çıktığında, `Drop` tanımının süreklenmesi nedeniyle 
kutunun işaret ettiği yığın verileri de temizlenir. Bu iki tanım, bu bölümün geri kalanında tartışacağımız diğer 
akıllı işaretçi türleri tarafından sağlanan işlevsellik için daha da önemli olacaktır. 
Şimdi bu iki tanımı daha ayrıntılı olarak inceleyelim.

[trait-objects]: ch17-02-trait-objects.html#using-trait-objects-that-allow-for-values-of-different-types
