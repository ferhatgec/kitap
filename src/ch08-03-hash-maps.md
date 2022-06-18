## Anahtar-Kilit Koleksiyonlarında İlişkili Değerlerle Anahtarları Saklama

Ortak koleksiyonlarımızın sonuncusu *anahtar-kilit koleksiyonudur*. `HashMap<K, V>` türü, 
bu anahtarları ve değerleri belleğe nasıl yerleştireceğini belirleyen bir karma fonksiyonu kullanarak K türündeki anahtarların 
V türündeki değerlerle eşlenmesini depolar. Birçok programlama dili bu tür bir veri yapısını destekler, 
ancak genellikle hash, map, object, hash table, dictionary veya associative array gibi farklı isimler kullanırlar.

Anahtar-kilit koleksiyonları, vektörlerde olduğu gibi bir indeks kullanarak değil, herhangi bir türde olabilen bir anahtar 
kullanarak verileri aramak istediğinizde kullanışlıdır. Örneğin, bir oyunda, her anahtarın bir takımın adı ve 
değerlerin her takımın puanı olduğu bir anahtar-kilit koleksiyonunda her takımın puanını takip edebilirsiniz. 
Bir takım adı verildiğinde, skorunu alabilirsiniz.

Bu bölümün devamında a-k.k diyerek bahsedeceğimiz şey anahtar-kilit koleksiyonu olacaktır.

Bu bölümde a-k.k'nin temel API'sinin üzerinden geçeceğiz, ancak standart kütüphane tarafından `HashMap<K, V>` üzerinde tanımlanan 
fonksiyonlarda çok daha fazla güzellik gizlidir. Her zaman olduğu gibi, daha fazla bilgi için standart kütüphane dokümantasyonunu 
kontrol edin.

## Yeni Bir A-K.K Oluşturma

Boş bir a-k.k oluşturmanın bir yolu `new` kullanmak ve `insert` ile eleman eklemektir. Liste 8-20'de, isimleri 
*Blue* ve *Yellow* olan iki takımın skorlarını takip ediyoruz. *Blue* takım 10 puanla başlar ve *Yellow* takım 50 puanla başlar.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-20/src/main.rs:here}}
```

<span class="caption">Liste 8-20: Yeni bir a-k.k oluşturma ve bazı anahtar ve değerleri ekleme</span>

Öncelikle standart kütüphanenin koleksiyonlar bölümündeki `HashMap`'i kullanmamız gerektiğini unutmayın. 
Üç yaygın koleksiyonumuz arasında bu en az kullanılanıdır, bu nedenle başlangıçta otomatik olarak kapsama alınan 
özelliklere dahil edilmemiştir. `HashMap` standart kütüphaneden de daha az destek alır; örneğin bunları oluşturmak için 
yerleşik bir makro yoktur.

Tıpkı vektörler gibi, a-k.k da verilerini yığın üzerinde saklar. Bu `HashMap`'in `String` türünde anahtarları ve `i32` türünde değerleri 
vardır. Vektörler gibi, a-k.k da homojendir: tüm anahtarlar birbiriyle aynı türde olmalıdır ve tüm değerler aynı türde olmalıdır.

### A.K-K'da Değerlere Erişme

Liste 8-21'de gösterildiği gibi, `get` metoduna anahtarı sağlayarak a.k-k'dan dönüş değerini alabiliriz.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-21/src/main.rs:here}}
```

<span class="caption">Liste 8-21: A-k.k'da saklanan *Blue* takımının 
skoruna erişim</span>

Burada, skor *Blue* takımla ilişkilendirilen değere sahip olacak ve sonuç `10` olacaktır. `get` metodu `Option<&V>` döndürür; 
a-k.k'da o anahtar için değer yoksa `get`, `None` döndürür. Bu program, skorlarda anahtar için bir giriş yoksa skoru sıfıra ayarlamak 
için `unwrap_or` öğesini çağırarak `Option`'ı işler.

Bir a-k.k'daki her bir anahtar/değer çifti üzerinde, vektörlerde yaptığımıza benzer şekilde, bir `for` döngüsü kullanarak yineleme yapabiliriz:

```rust
{{#rustdoc_include ../listings/ch08-common-collections/no-listing-03-iterate-over-hashmap/src/main.rs:here}}
```

Bu kod, her bir çifti rastgele bir sırayla yazdıracaktır:

```text
Yellow: 50
Blue: 10
```

### A-K.K'lar ve Sahiplik

`Copy` tanımını uygulayan `i32` gibi türler için değerler a-k.k'a kopyalanır. `String` gibi sahip olunan değerler için, 
değerler taşınır ve a-k.k, Liste 8-22'de gösterildiği gibi bu değerlerin sahibi olur.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-22/src/main.rs:here}}
```

<span class="caption">Liste 8-22: Eklendikten sonra anahtarların ve değerlerin a-k.k'a 
ait olduğunu gösterme</span>

`field_name` ve `field_value` değişkenlerini, `insert` çağrısı ile a-k.k'a taşındıktan sonra kullanamıyoruz.

Değerlere yapılan referansları a-k.k'a eklersek, değerler hash haritasına taşınmaz. 
Referansların işaret ettiği değerler en azından a-k.k geçerli olduğu sürece geçerli olmalıdır. 
Bölüm 10'daki [“Referansları Yaşam Süreleriyle Doğrulama”][validating-references-with-lifetimes]<!-- ignore --> bölümünde 
bu konular hakkında daha fazla konuşacağız.

### A-K.K'unu Güncelleme

Anahtar ve değer çiftlerinin sayısı artırılabilir olsa da, her benzersiz anahtar aynı anda kendisiyle 
ilişkilendirilmiş yalnızca bir değere sahip olabilir (ancak bunun tersi geçerli değildir: örneğin, hem *Blue* takım hem de *Yellow* 
takım skorlar a-k.k'da depolanan 10 değerine sahip olabilir).

Bir anahtar-kilit eşlemedeki verileri değiştirmek istediğinizde, bir anahtarın zaten atanmış bir değere sahip olduğu 
durumu nasıl ele alacağınıza karar vermeniz gerekir. Eski değeri tamamen göz ardı ederek eski değeri yeni değerle değiştirebilirsiniz. 
Eski değeri tutup yeni değeri *yok sayabilir*, yalnızca anahtarın zaten bir değeri yoksa yeni değeri ekleyebilirsiniz. 
Ya da eski değer ile yeni değeri birleştirebilirsiniz. Şimdi bunların her birinin nasıl yapılacağına bakalım!

#### Bir Değerin Üzerine Yazma

Bir a-k.k'a bir anahtar ve bir değer eklersek ve daha sonra aynı anahtarı farklı bir değerle eklersek, 
bu anahtarla ilişkili değer değiştirilecektir. Liste 8-23'teki kod iki kez `insert` çağrısı yapsa da, 
*Blue* takımın anahtarının değerini iki kez eklediğimiz için a-k.k yalnızca bir anahtar/değer çifti içerecektir.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-23/src/main.rs:here}}
```

<span class="caption">Liste 8-23: Belirli bir anahtarla saklanan bir değeri değiştirme</span>

Bu kod `{"Blue": 25}` yazdıracaktır. `10`'un orijinal değerinin üzerine yazılmıştır.

<!-- Old headings. Do not remove or links may break. -->
<a id="only-inserting-a-value-if-the-key-has-no-value"></a>

#### Yalnızca Bir Anahtar Mevcut Değilse Anahtar ve Değer Ekleme

Belirli bir anahtarın a-k.k'da bir değerle zaten var olup olmadığını kontrol etmek ve ardından aşağıdaki eylemleri 
gerçekleştirmek yaygındır: anahtar a-k.k'da varsa, mevcut değer olduğu gibi kalmalıdır. 
Anahtar mevcut değilse, onu ve değerini eklersiniz.

A-k.k'ları bunun için kontrol etmek istediğiniz anahtarı parametre olarak alan `entry` adında özel bir API'ye sahiptir. 
`entry` metodunun geri dönüş değeri, var olabilecek veya olmayabilecek bir değeri temsil eden `Entry` adlı bir `enum`'dur. 
Diyelim ki *Yellow* takımın anahtarının kendisiyle ilişkili bir değeri olup olmadığını kontrol etmek istiyoruz. 
Eğer yoksa, `50` değerini eklemek istiyoruz ve aynı şeyi *Blue* takım için de yapmak istiyoruz. 
`entry` API'sini kullanarak yazacağımız kod Liste 8-24'e benzeyecektir:

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-24/src/main.rs:here}}
```

<span class="caption">Liste 8-24: Yalnızca anahtarın halihazırda bir değeri yoksa eklemek 
için `entry` metodunu kullanma</span>

`Entry` üzerindeki `or_insert` metodu, ilgili `Entry` anahtarı mevcutsa bu anahtarın değerine 
değiştirilebilir bir referans döndürmek için tanımlanmıştır; mevcut değilse, parametreyi bu anahtarın yeni 
değeri olarak ekler ve yeni değere değiştirilebilir bir referans döndürür. 
Bu teknik, mantığı kendimiz yazmaktan çok daha temizdir ve ayrıca ödünç denetleyicisi ile daha iyi çalışır.

Liste 8-24'teki kod çalıştırıldığında `{"Yellow": 50, "Blue": 10}` çıktısını verecektir. 
`entry`'e yapılan ilk çağrı *Yellow* takımın anahtarına `50` değerini ekleyecektir çünkü *Yellow* takımın zaten bir değeri yoktur. 
İkinci `entry` çağrısı a-k.k'unu değiştirmeyecektir çünkü *Blue* takım zaten `10` değerine sahiptir.

#### Eski Değere Dayalı Olarak Bir Değeri Güncelleme

A-K.K'lar için bir başka yaygın kullanım durumu da bir anahtarın değerini aramak ve ardından eski değere göre güncellemektir. 
Örneğin, Liste 8-25, bir metinde her bir kelimenin kaç kez geçtiğini sayan kodu göstermektedir. 
Kelimeleri anahtar olarak içeren bir a-k.k kullanırız ve o kelimeyi kaç kez gördüğümüzü takip etmek için değeri artırırız. 
Eğer bir kelimeyi ilk kez görüyorsak, önce 0 değerini ekleriz.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-25/src/main.rs:here}}
```

<span class="caption">Liste 8-25: Kelimeleri ve sayıları saklayan bir a-k.k kullanarak 
kelimelerin oluşumlarını sayma</span>

This code will print `{"world": 2, "hello": 1, "wonderful": 1}`. You might see
the same key/value pairs printed in a different order: recall from the
 section that
iterating over a hash map happens in an arbitrary order.

Bu kod `{"world": 2, "hello": 1, "wonderful": 1}` yazdıracaktır. Aynı anahtar/değer çiftlerinin farklı bir sırada yazdırıldığını 
görebilirsiniz: [“A-K.K'daki Değerlere Erişim”][access]<!-- ignore --> bölümünden bir a-k.k üzerinde yinelemenin 
rastgele bir sırada gerçekleştiğini hatırlayın.

`split_whitespace` metodu, metin içindeki değerin boşluklarla ayrılmış alt dilimleri üzerinde bir yineleyici döndürür. 
`or_insert` metodu, belirtilen anahtar için değere değiştirilebilir bir referans (`&mut V`) döndürür. 
Burada bu değişebilir referansı `count` değişkeninde saklarız, bu nedenle bu değere atama yapmak için önce yıldız işaretini (`*`) 
kullanarak `count` referansını kaldırmamız gerekir. Değiştirilebilir referans `for` döngüsünün sonunda kapsam dışına çıkar, 
bu nedenle tüm bu değişiklikler güvenlidir ve ödünç alma kuralları tarafından izin verilir.

### Şifreleme Fonksiyonları

Varsayılan olarak `HashMap`, anahtar-kilit tablolarını içeren Hizmet Reddi (DoS) saldırılarına karşı direnç 
sağlayabilen *SipHash* adlı bir şifreleme fonksiyonu kullanır[^siphash]<!-- ignore -->. Bu, mevcut en hızlı şifreleme algoritması değildir, 
ancak performanstaki düşüşle birlikte gelen daha iyi güvenlik için yapılan takas buna değecektir. 
Kodunuzun profilini çıkarırsanız ve varsayılan şifreleme fonksiyonunun amaçlarınız için çok yavaş olduğunu fark ederseniz, 
farklı bir şifreleyici belirterek başka bir fonksiyona geçebilirsiniz. Bir şifreleyici, `BuildHasher` tanımını uygulayan bir türdür. 
Özellikler ve bunların nasıl uygulanacağı hakkında Bölüm 10'da konuşacağız. Kendi şifreleyicinizi sıfırdan yazmak zorunda değilsiniz; 
[crates.io](https://crates.io/)<!-- ignore -->, birçok yaygın hashing algoritmasını uygulayan şifreleyiciler sağlayan, diğer Rust kullanıcıları 
tarafından paylaşılan kütüphanelere sahiptir.

### Özet

Vektörler, dizgiler ve anahtar-kilit koleksiyonları, verileri depolamanız, erişmeniz ve değiştirmeniz gerektiğinde programlarda 
gerekli olan büyük miktarda işlevsellik sağlayacaktır. 

İşte şimdi çözmeniz gereken bazı alıştırmalar:

* Bir tam sayı listesi verildiğinde, bir vektör kullanın ve listenin medyanını (sıralandığında orta konumdaki değer) ve modunu 
  (en sık ortaya çıkan değer; bir a-k.k burada yardımcı olacaktır) döndürün.
* Dizgileri Domuz Latincesine dönüştürün. Her kelimenin ilk ünsüzü kelimenin sonuna taşınır ve “ay” eklenir, 
  böylece “first” “irst-fay olur. Sesli harfle başlayan kelimelerin sonuna “hay” eklenir (“apple”, “apple-hay” olur). 
  UTF-8 kodlamasıyla ilgili ayrıntıları aklınızda bulundurun!
* Bir a-k.k ve vektör kullanarak, bir kullanıcının bir şirketteki bir departmana çalışan isimleri eklemesine olanak tanıyan bir metin 
  arayüzü oluşturun. Örneğin, “Sally'i Mühendisliğe Ekle” veya “Amir'i Satış Danışmanlığına Ekle”. 
  Ardından kullanıcının bir departmandaki tüm kişilerin veya şirketteki tüm kişilerin alfabetik olarak 
  sıralanmış bir listesini almasına izin verin.

Standart kütüphane API dokümantasyonları vektörlerin, dizgilerin ve a-k.k'ların bu alıştırmalar için yararlı olacak 
metodlarını açıklamaktadır!

İşlemlerin başarısız olabileceği daha karmaşık programlara giriyoruz, bu nedenle hata işlemeyi tartışmak için mükemmel bir zaman. 
Bunu daha sonra yapacağız!

[^siphash]: [https://en.wikipedia.org/wiki/SipHash](https://en.wikipedia.org/wiki/SipHash)

[validating-references-with-lifetimes]:
ch10-03-lifetime-syntax.html#validating-references-with-lifetimes
[access]: #accessing-values-in-a-hash-map
