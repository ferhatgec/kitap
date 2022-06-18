## UTF-8 Kodlu Metni Dizgilerde Saklama

Dizgilerden Bölüm 4'te bahsetmiştik, ancak şimdi onlara daha derinlemesine bakacağız. 
Yeni Rustseverler genellikle üç nedenden dolayı dizgilere takılırlar: Rust'ın olası hataları açığa çıkarma eğilimi, 
dizgilerin birçok programcının düşündüğünden daha karmaşık bir veri yapısı olması ve UTF-8. 
Bu faktörler, diğer programlama dillerinden geldiğinizde zor görünebilecek bir şekilde birleşir.

Dizgileri koleksiyonlar bağlamında ele alıyoruz çünkü dizgiler bir bayt koleksiyonu ve bu baytlar metin olarak yorumlandığında yararlı 
işlevler sağlayan bazı metodlar olarak uygulanmaktadır. Bu bölümde, `String` üzerinde her koleksiyon türünün sahip olduğu oluşturma, 
güncelleme ve okuma gibi işlemlerden bahsedeceğiz. Ayrıca, `String`'in diğer koleksiyonlardan farklı olduğu yönleri, yani bir 
`String`'de indekslemenin, insanların ve bilgisayarların `String` verilerini yorumlama biçimleri arasındaki farklar nedeniyle nasıl karmaşıklaştığını tartışacağız.

### Dizgi Nedir?

İlk olarak *dizgi* terimi ile ne kastettiğimizi tanımlayacağız. Rust'ın çekirdek dilinde yalnızca bir dizgi tipi vardır, 
bu da genellikle ödünç alınmış `&str` biçiminde görülen dizgi dilimi `str`'dir. Bölüm 4'te, başka bir yerde saklanan bazı UTF-8 kodlu dizgi 
verilerine referans olan dizgi dilimlerinden bahsetmiştik. Örneğin dizgi değişmezleri, programın ikili dosyasında saklanır ve bu 
nedenle dizgi dilimleridir.

Çekirdek dile kodlanmak yerine Rust'ın standart kütüphanesi tarafından sağlanan `String` türü, büyütülebilir, değiştirilebilir, 
sahipli, UTF-8 kodlu bir dize türüdür. Rustseverler Rust'ta “dizgilerden” bahsettiklerinde, `String` ya da dizgi dilimi `&str` tiplerinden 
birine atıfta bulunuyor olabilirler, sadece bu tiplerden birine değil. Bu bölüm büyük ölçüde `String` hakkında olsa da, 
her iki tür de Rust'ın standart kütüphanesinde yoğun olarak kullanılır ve hem `String` hem de string dilimleri UTF-8 kodludur.

### Yeni Bir `String` Oluşturma

`Vec<T>` ile kullanılabilen işlemlerin çoğu `String` ile de kullanılabilir, çünkü `String` aslında bazı ekstra garantilere, 
kısıtlamalara ve yeteneklere sahip bir bayt vektörü etrafında bir sarmalayıcı olarak uygulanmaktadır. `Vec<T>` ve `String` ile 
aynı şekilde çalışan bir fonksiyon örneği, Liste 8-11'de gösterilen bir örnek oluşturmak için `new` fonksiyonudur.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-11/src/main.rs:here}}
```

<span class="caption">Liste 8-11: Yeni, boş bir `String` oluşturma</span>


Bu satır, daha sonra içine veri yükleyebileceğimiz `s` adında yeni bir boş dizgi oluşturur. 
Genellikle, dizgiyi başlatmak istediğimiz bazı başlangıç verilerimiz olacaktır. Bunun için, dizgi değişmezlerinin yaptığı 
gibi `Display` tanımını sürekleyen herhangi bir türde kullanılabilen `to_string` metodunu kullanırız.

Liste 8-12'de iki örnek gösterilmektedir.


```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-12/src/main.rs:here}}
```

<span class="caption">Liste 8-12: Bir dize değişmezinden bir `String` oluşturmak için `to_string` metodunu 
kullanma</span>

`String::from` fonksiyonunu bir dizgi değişmezinden `String` oluşturmak için de kullanabiliriz. 
Liste 8-13'teki kod, Liste 8-12'deki `to_string` kullanan koda eş değerdir.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-13/src/main.rs:here}}
```

<span class="caption">Liste 8-13: Bir dizgi değişmezinden `String` oluşturmak için `String::from` fonksiyonunu 
kullanma</span>

Dizgiler pek çok şey için kullanıldığından, dizgiler için pek çok farklı genel API kullanabiliriz ve bu da bize pek çok seçenek sunar. 
Bazıları gereksiz görünebilir, ancak hepsinin önemli amacı vardır! Bu durumda, `String::from` ve `to_string` aynı şeyi yapar, 
bu nedenle hangisini seçeceğiniz bir stil ve okunabilirlik meselesidir.

Dizgilerin UTF-8 kodlu olduğunu unutmayın, bu nedenle Liste 8-14'te gösterildiği gibi uygun şekilde kodlanmış herhangi bir 
veriyi bunlara dahil edebiliriz.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-14/src/main.rs:here}}
```

<span class="caption">Liste 8-14: Farklı dillerdeki selamlamaları dizgilerde saklama</span>

Bunların tamamı geçerli `String` değerleridir. 

### `String`'i Güncelleme

Bir `String`'in boyutu büyüyebilir ve içine daha fazla veri koyarsanız, tıpkı bir `Vec<T>`'nin içeriği gibi içeriği değişebilir. 
Ayrıca, `String` değerlerini birleştirmek için `+` operatörünü veya `format!` makrosunu rahatlıkla kullanabilirsiniz.

#### `push_str` ve `push` ile `String`'e ekleme yapmak

Liste 8-15'te gösterildiği gibi, bir dizgi dilimi eklemek için `push_str` 
metodunu kullanarak bir `String`'i büyütebiliriz.
   

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-15/src/main.rs:here}}
```

<span class="caption">Liste 8-15: `push_str` metodunu kullanarak bir `String`'e bir dizgi 
dilimi ekleme</span>


Bu iki satırdan sonra, `s` `foobar`'ı içerecektir. `push_str` metodu bir dizgi dilimi alır çünkü parametrenin sahipliğini 
almak zorunda değilizdir. Örneğin, Liste 8-16'daki kodda, içeriğini `s1`'e ekledikten sonra `s2`'yi kullanabilmek istiyoruz.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-16/src/main.rs:here}}
```

<span class="caption">Liste 8-16: İçeriğini bir `String`'e ekledikten sonra dizgi dilimini 
kullanma</span>

Eğer `push_str` metodu `s2`'nin sahipliğini alsaydı, değerini son satıra yazdıramazdık. 
Ancak, bu kod beklediğimiz gibi çalışıyor!

`push` metodu parametre olarak tek bir karakter alır ve onu `String`'e ekler. Liste 8-17, `push` metodunu kullanarak bir 
`String`'e “l” harfini ekler.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-17/src/main.rs:here}}
```

<span class="caption">Liste 8-17: `push` kullanarak `String`'e 
karakter ekleme</span>

Sonuç olarak, `s`, *`lol`* içerecektir.

#### `+` Operatörü veya `format!` Makrosu ile birleştirme

Çoğu zaman, mevcut iki dizgiyi birleştirmek isteyeceksiniz. Bunu yapmanın bir yolu, Liste 8-18'de gösterildiği gibi 
`+` operatörünü kullanmaktır.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-18/src/main.rs:here}}
```

<span class="caption">Liste 8-18: İki `String` değerini yeni bir `String` değeriyle birleştirmek 
için `+` operatörünü kullanmak</span>

`s3` dizgisi `Hello, world!` içerecektir. Ekleme işleminden sonra `s1`'in artık geçerli olmamasının ve 
`s2`'ye bir referans kullanmamızın nedeni, `+` operatörünü kullandığımızda çağrılan metodun imzasıyla ilgilidir. 
Imzası aşağıdaki gibi görünen `add` metodunu kullanır:

```rust,ignore
fn add(self, s: &str) -> String {
```

Standart kütüphanede, yaygınlar ve ilişkili türler kullanılarak tanımlanmış eklentiler görürsünüz. 
Burada, bu metodu `String` değerleriyle çağırdığımızda olan şey olan somut türlerle değiştirdik. 
Yaygınları Bölüm 10'da tartışacağız. Bu imza bize `+` operatörünün zor kısımlarını anlamamız için gereken ipuçlarını verir.

İlk olarak, `s2` bir `&` içerir, yani ilk dizgiye ikinci dizginin bir referansını ekliyoruz. Bunun nedeni `add` fonksiyonundaki 
`s` parametresidir: bir `String`'e yalnızca bir `&str` ekleyebiliriz; iki `String` değerini birbirine ekleyemeyiz. 
Ama bekleyin - `&s2`'nin türü, `add` fonksiyonunun ikinci parametresinde belirtildiği gibi `&str` değil, `&String`'dir. 

Öyleyse Liste 8-18 neden derleniyor?

`add` çağrısında `&s2`'yi kullanabilmemizin nedeni, derleyicinin `&String` argümanını `&str`'e zorlayabilmesidir. 
`add` metodunu çağırdığımızda, Rust burada `&s2`'yi `&s2[..]`'ye dönüştüren bir *`deref` zorlaması* kullanır. 
*`deref` zorlamasını* Bölüm 15'te daha derinlemesine tartışacağız. `add`, `s` parametresinin sahipliğini almadığından, 
`s2` bu işlemden sonra hala geçerli bir `String` olacaktır.

İkinci olarak, imzada `add`'in `self`'in sahipliğini aldığını görebiliriz, çünkü `self`'in `&'`si yoktur. 
Bu, Liste 8-18'deki `s1`'in `add` çağrısına taşınacağı ve bundan sonra artık geçerli olmayacağı anlamına gelir. 
Dolayısıyla, `let s3 = s1 + &s2;` ifade yapısı her iki dizgiyi de kopyalayıp yeni bir tane oluşturacak gibi görünse de, 
bu ifade yapısı aslında `s1`'in sahipliğini alır, `s2`'nin içeriğinin bir kopyasını ekler ve ardından sonucun sahipliğini döndürür. 
Başka bir deyişle, çok sayıda kopya oluşturuyormuş gibi görünür ancak oluşturmaz; uygulama kopyalamadan daha verimlidir.

Birden fazla dizgiyi birleştirmemiz gerekirse, `+` operatörünün davranışı hantal hale gelir:

```rust
{{#rustdoc_include ../listings/ch08-common-collections/no-listing-01-concat-multiple-strings/src/main.rs:here}}
```

Bu noktada, `s` `tic-tac-toe` olacak. Tüm `+` ve `"` karakterleri ile neler olup bittiğini görmek zordur. 
Daha karmaşık dizgi birleştirmeleri için `format!` makrosunu kullanabiliriz:

```rust
{{#rustdoc_include ../listings/ch08-common-collections/no-listing-02-format/src/main.rs:here}}
```

Bu kod da aynı şekilde `s`'yi `tic-tac-toe` olarak ayarlar. `format!` makrosu `println!` gibi çalışır, 
ancak çıktıyı ekrana yazdırmak yerine, içeriği içeren bir `String` döndürür. Kodun `format!` kullanan versiyonunun okunması 
çok daha kolaydır ve `format!` makrosu tarafından oluşturulan kod referanslar kullanır, böylece bu çağrı parametrelerinden 
herhangi birinin sahipliğini almaz.

### Dizgilerde İndeksleme

Diğer birçok programlama dilinde, bir dizedeki karakterlere indeksle referans vererek tek tek erişmek geçerli ve yaygın bir işlemdir. 
Ancak, Rust'ta indeksleme söz dizimini kullanarak bir `String`'in parçalarına erişmeye çalışırsanız, 
bir hata alırsınız. Liste 8-19'daki geçersiz kodu düşünün.

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-19/src/main.rs:here}}
```

<span class="caption">Liste 8-19: İndeksleme söz dizimini `String` ile kullanmaya çalışmak</span>

Bu kod aşağıdaki hataya sebebiyet verecektir:

```console
{{#include ../listings/ch08-common-collections/listing-08-19/output.txt}}
```

Hata ve not hikayeyi anlatıyor: Rust dizgileri indekslemeyi desteklemez. Ama neden desteklemiyor? 
Bu soruyu yanıtlamak için, Rust'ın dizgileri bellekte nasıl sakladığını tartışmamız gerekir.

#### Dahili Temsil

`String`, `Vec<u8>` kullanan bir sarmalayıcıdır. Liste 8-14'teki düzgün kodlanmış UTF-8 örnek dizgilerimizden bazılarına bakalım. 

İlk olarak, buna bakalım:

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-14/src/main.rs:spanish}}
```

Bu durumda, `len` `4` olacaktır, bu da “Hola” dizgisini depolayan vektörün 4 bayt uzunluğunda olduğu anlamına gelir. 
UTF-8'de kodlandığında bu harflerin her biri 1 bayt alır. Ancak aşağıdaki satır sizi şaşırtabilir. 
(Bu dizenin Arapça 3 rakamı ile değil, büyük Kiril harfi Ze ile başladığına dikkat edin).

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-14/src/main.rs:russian}}
```

Dizginin ne kadar uzunlukta olduğu sorulduğunda 12 diyebilirsiniz. 
Aslında Rust'ın cevabı 24'tür: UTF-8'de “Здравствуйте” yi kodlamak için gereken bayt sayısı budur, 
çünkü bu dizgideki her Unicode skaler değeri 2 bayt depolama alanı alır. Bu nedenle, dizginin baytlarındaki bir 
indeks her zaman geçerli bir Unicode skaler değeriyle ilişkili olmayacaktır. 

Kanıt için bu geçersiz Rust kodunu düşünün:

```rust,ignore,does_not_compile
let hello = "Здравствуйте";
let answer = &hello[0];
```

`answer`'ın ilk harf olan `З` olmayacağını zaten biliyorsunuz. UTF-8'de kodlandığında, `З`'nin ilk baytı `208` ve ikincisi `151`'dir, 
bu nedenle cevabın aslında `208` olması gerekir, ancak `208` tek başına geçerli bir karakter değildir. 
Bu dizginin ilk harfini soran bir kullanıcı muhtemelen `208` sonucunu almak istemeyecektir; 
ancak Rust'ın `0` bayt indeksinde sahip olduğu tek veri budur. Kullanıcılar, dizgi yalnızca Latin harfleri içerse bile 
genellikle bayt değerinin döndürülmesini istemezler: `&"hello"[0]` bayt değerini döndüren geçerli bir kod olsaydı, `h` değil `104` döndürürdü.

O halde cevap, beklenmedik bir değer döndürmekten ve hemen keşfedilemeyecek hatalara neden olmaktan kaçınmak için Rust'ın 
bu kodu hiç derlememesi ve geliştirme sürecinin başlarında yanlış anlamaları önlemesidir.

### Baytlar ve Skaler Değerler ve Grapheme Kümeleri! Amanın!

UTF-8 ile ilgili bir başka nokta da, Rust'ın bakış açısından dizgilere bakmanın aslında üç ilgili yolu olduğudur: 
baytlar, skaler değerler ve grapheme kümeleri (*harf* olarak adlandırdığımız şeye en yakın şey).

Devanagari alfabesiyle yazılmış Hintçe “नमस्ते” kelimesine bakarsak, bu kelime aşağıdaki gibi görünen 
`u8` değerlerinden oluşan bir vektör olarak saklanır:

```text
[224, 164, 168, 224, 164, 174, 224, 164, 184, 224, 165, 141, 224, 164, 164,
224, 165, 135]
```

Bu 18 bayttır ve bilgisayarlar bu verileri nihai olarak bu şekilde depolar. 
Bunlara Unicode skaler değerleri olarak bakarsak, ki Rust'ın `char` türü budur, bu baytlar şöyle görünür:

```text
['न', 'म', 'स', '्', 'त', 'े']
```

Burada altı `char` değeri vardır, ancak dördüncü ve altıncı harfler harf değildir: 
bunlar kendi başlarına bir anlam ifade etmeyen aksan işaretleridir. 
Son olarak, bunlara grapheme kümeleri olarak bakarsak, bir kişinin Hintçe kelimeyi oluşturan 
dört harf olarak adlandıracağı şeyi elde ederiz:

```text
["न", "म", "स्", "ते"]
```

Rust, bilgisayarların depoladığı ham dizgi verilerini yorumlamak için farklı yollar sağlar, 
böylece veriler hangi insan dilinde olursa olsun her program ihtiyaç duyduğu yorumu seçebilir.

Rust'ın bir karakteri elde etmek için bir String içinde indeksleme yapmamıza izin vermemesinin son bir nedeni, 
indeksleme işlemlerinin her zaman sabit zaman (O(1)) almasının beklenmesidir. Ancak bir `String` ile bu performansı garanti 
etmek mümkün değildir, çünkü Rust'ın kaç tane geçerli karakter olduğunu belirlemek için başlangıçtan indekse kadar içerik boyunca 
yürümesi gerekir.

### `String`'i Dilimleme

Bir dizeye indeksleme yapmak genellikle kötü bir fikirdir çünkü dizeye indeksleme işleminin dönüş türünün ne olması gerektiği 
açık değildir: bayt değeri, karakter, grapheme kümesi veya dizgi dilimi. Bu nedenle, dizgi dilimleri oluşturmak için 
gerçekten indis kullanmanız gerekiyorsa, Rust sizden daha spesifik olmanızı bekler.

Tek bir sayı ile `[]` kullanarak indeksleme yapmak yerine, belirli baytları içeren bir dizgi dilimi oluşturmak 
için `[]` ile bir aralığı kullanabilirsiniz:

```rust
let hello = "Здравствуйте";

let s = &hello[0..4];
```

Burada `s`, dizginin ilk 4 baytını içeren bir `&str` olacaktır. Daha önce, bu karakterlerin her birinin 
2 bayt olduğundan bahsetmiştik, bu da `s`'nin `Зд` olacağı anlamına gelir.

Eğer bir karakterin baytlarının sadece bir kısmını `&hello[0..1]` gibi bir şeyle dilimlemeye çalışsaydık, 
Rust çalışma zamanında bir vektörde geçersiz bir indekse erişildiğinde olduğu gibi paniğe kapılırdı:

```console
{{#include ../listings/ch08-common-collections/output-only-01-not-char-boundary/output.txt}}
```

Dizgi dilimleri oluşturmak için aralıkları dikkatli kullanmalısınız, çünkü yanlış kullanım programınızı çökertebilir.

### `String` Üzerinde Yineleme Yöntemleri

Dizgi parçaları üzerinde işlem yapmanın en iyi yolu, karakter mi yoksa bayt mı istediğinizi açıkça belirtmektir. 
Tek tek Unicode skaler değerleri için `chars` metodunu kullanabilirsiniz. “Зд” üzerinde `chars` metodu çağrıldığında `char` türünde 
iki değer ayrılır ve döndürülür; her bir öğeye erişmek için sonuç üzerinde yineleme yapabilirsiniz:


```rust
for c in "Зд".chars() {
    println!("{}", c);
}
```

Bu kod aşağıdakileri yazdıracaktır:

```text
З
д
```

Alternatif olarak, `bytes` yöntemi, kullanımınız için uygun olabilecek her ham baytı döndürür:

```rust
for b in "Зд".bytes() {
    println!("{}", b);
}
```

Bu kod, bu dizgiyi oluşturan dört baytı yazdıracaktır:

```text
208
151
208
180
```

Ancak geçerli Unicode skaler değerlerinin 1 bayttan fazla olabileceğini unutmayın.

Devanagari alfabesinde olduğu gibi dizelerden grapheme kümeleri elde etmek karmaşıktır, 
bu nedenle bu fonksiyon direkt standart kütüphane tarafından sağlanmamaktadır. 

İhtiyacınız olan işlevsellik buysa [crates.io](https://crates.io/)<!-- ignore -->'da işe yarayabilecek 
kasalar mevcuttur.

### `String`'ler O Kadar da Basit Değildir

Özetlemek gerekirse, dizgiler karmaşıktır. Farklı programlama dilleri, bu karmaşıklığın programcıya nasıl sunulacağı 
konusunda farklı seçimler yapar. Rust, `String` verilerinin doğru işlenmesini tüm Rust programları için varsayılan 
davranış haline getirmeyi seçmiştir, bu da programcıların UTF-8 verilerini önceden ele almak için daha fazla düşünmesi gerektiği 
anlamına gelir. Bu değiş tokuş, dizelerin karmaşıklığını diğer programlama dillerinde göründüğünden daha fazla ortaya çıkarır, 
ancak geliştirme yaşam döngünüzün ilerleyen aşamalarında *ASCII* olmayan karakterleri içeren hataları ele almak zorunda kalmanızı önler.

İyi haber şu ki, standart kütüphane bu karmaşık durumların doğru şekilde ele alınmasına yardımcı olmak için `String` ve 
`&str` türlerinden oluşturulmuş çok sayıda işlevsellik sunar. Bir dizgi içinde arama yapmak için `contains` ve bir dizginin 
parçalarını başka bir dizgiyle değiştirmek için `replace` gibi yararlı metodlara ulaşmak için ilgili dokümantasyonlara
göz attığınızdan emin olun.

Daha az karışık bir şeye geçelim: *anahtar-kilit koleksiyonları*!
