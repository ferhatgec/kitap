## Fonksiyonlar

Fonksiyonlar Rust kodunda sıklıkla kullanılır. 
Dildeki en önemli fonksiyonlardan birini zaten gördünüz: 
birçok programın giriş noktası olan `main` fonksiyonu. 
Ayrıca, yeni fonksiyonlar tanımlamanızı sağlayan `fn` anahtar sözcüğünü de gördünüz.

Rust kodu, tüm harflerin küçük olduğu ve ayrı sözcüklerin altını çizdiği, 
fonksiyon ve değişken adları için geleneksel stil olan *yılan stilini* kullanır. 

Örnek bir fonksiyon tanımı içeren bir program:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-16-functions/src/main.rs}}
```

Rust'ta `fn` ve ardından bir fonksiyon adı ve bir parantez dizisi girerek bir fonksiyon tanımlarız. 
Parantezler derleyiciye fonksiyon argüman gövdesinin nerede başlayıp nerede bittiğini söyler.

Tanımladığımız herhangi bir fonksiyonu, adını ve ardından bir parantez dizisini girerek çağırabiliriz. 
Programda `another_function` tanımlı olduğundan, `main` fonksiyonu içinden çağrılabilir. 
Kaynak kodda ana fonksiyondan *sonra* `another_function`'u tanımladığımızı unutmayın; 
daha önce de tanımlayabilirdik. Rust, fonksiyonlarınızı nerede tanımladığınızla ilgilenmez, 
yalnızca arayanın görebileceği bir kapsamda bir yerde tanımlanmalarını ister. Kapsam dışı fonksiyonları geleneksel
yöntemle çağıramazsınız.

İşlevleri daha fazla keşfetmek için *functions* adında yeni bir proje başlatalım. 
`other_function` örneğini *src/main.rs* içine atın ve çalıştırın. 
Aşağıdaki çıktıyı görmelisiniz:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-16-functions/output.txt}}
```

Satırlar, ana fonksiyonda göründükleri sırayla yürütülür. 
İlk olarak, “Hello, world!” ekrana yazdırılır ve ardından `another_function` fonksiyonu
çağrılır ve belirtilen parametrelerle birlikte yürütülür.

### Parametreler

Fonksiyonları, bir fonksiyonun yapısının parçası olan özel değişkenler 
olan *parametrelere* sahip olacak şekilde tanımlayabiliriz. 
Bir fonksiyonun parametreleri olduğunda, ona bu parametreler için somut değerler sağlayabilirsiniz. 
Teknik olarak somut değerlere *argümanlar* denir, ancak gündelik konuşmalarda insanlar 
*parametre* ve *argüman* kelimelerini bir fonksiyonun tanımındaki değişkenler veya 
bir fonksiyonu çağırdığınızda iletilen somut değerler için birbirinin yerine kullanma eğilimindedir.

`another_function`'un bu sürümünde bir parametre ekliyoruz:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-17-functions-with-parameters/src/main.rs}}
```

Şimdi bu kodu çalıştırmaya çalışın; şu çıktıyı almalısınız:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-17-functions-with-parameters/output.txt}}
```

`another_function` tanımı, `x` adında bir parametreye sahiptir. 
`x`'in tipi `i32` olarak belirtilmiştir. 
`5`'i `another_function` fonksiyonuna parametre olarak verdiğimizde, 
`println!` makrosu, `x`'i içeren parametre listesinde `x` yerine `5`'i koyar.

Fonksiyon yapılarında, her parametrenin türünü belirtmelisiniz. 
Bu, Rust'ın tasarımında bilinçli olarak verilmiş bir karardır: fonksiyon tanımlarında tip açıklamalarının zorunluluğu, 
derleyicinin, ne türü istediğinizi anlamak için kodun başka bir yerinde bu tarz yaygın tür tanımlarını 
kullanmanıza neredeyse hiç ihtiyaç duymadığı anlamına gelir. Derleyici, fonksiyonun ne tür beklediğini biliyorsa, 
daha yararlı hata mesajları da verebilir.

Birden çok parametre tanımlarken, parametre bildirimlerini aşağıdaki gibi virgülle ayırın:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-18-functions-with-multiple-parameters/src/main.rs}}
```

Bu örnek, iki parametreli `print_labeled_measurement` adlı bir fonksiyon oluşturur. 
İlk parametre `value` olarak adlandırılmıştır ve türü `i32`'dir. 
İkincisi, `unit_label` olarak adlandırılmıştır ve `char` türündendir. 
Fonksiyon hem `value` hem de `unit_label`'in değerini içeren metni ekrana yazdırır.

Bu kodu çalıştırmayı deneyelim. 
*functions* klasörünüzün *src/main.rs* dosyasındaki mevcut programı önceki 
örnekle değiştirin ve `cargo run` komutunu kullanarak çalıştırın:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-18-functions-with-multiple-parameters/output.txt}}
```

Fonksiyonu `value`'nin değeri `5` ve `unit_label`'in değeri `'h'` olacak şekilde 
çağırdığımız için program çıktısı bu değerleri içerecektir.

### İfade Yapıları ve İfadeler

Fonksiyon gövdeleri, 
isteğe bağlı olarak ifade yapılarıyla biten bir dizi ifadeden oluşur. 
Şimdiye kadar ele aldığımız fonksiyonlar bir bitiş ifadesi içermemişti, 
ancak bir ifade yapısının parçası olarak ifadeler görmüştünüz. 
Rust ifade tabanlı bir dil olduğundan, bu anlaşılması gereken önemli bir ayrımdır. 
Diğer diller aynı ayrımlara sahip değildir, o halde şimdi ifade yapılarının ve ifadelerin ne olduğuna ve 
farklılıklarının fonksiyon gövdelerini nasıl etkilediğine bakalım.

*İfade yapıları*, bazı eylemleri gerçekleştiren ve bir değer döndürmeyen talimatlardır. 
*İfadeler* bir sonuç değeri olarak değerlendirilir. Bazı örneklere bakalım.

Aslında zaten ifade yapılarını ve ifadeleri kullandık. 
`let` anahtar sözcüğü ile bir değişken oluşturmak ve ona bir değer atamak 
bir ifade yapısıdır. Liste 3-1'deki `let y = 6;` bir ifade yapısıdır.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-01/src/main.rs}}
```

<span class="caption">Liste 3-1: Bir ifade yapısı içeren `main` fonksiyonu tanımı</span>

Fonksiyon tanımları da ayrıca ifade yapıları olarak değerlendirilir. 
Önceki örneğin tamamı kendi içinde bir ifade yapısıdır.

İfade yapıları herhangi bir değer döndürmez. Statements do not return values. Buna göre, 
`let` ifade yapısıyla başka bir değeri halihazırda tanımlanmış bir değerle eşitleyemezsiniz.
Bunu denerseniz, şu hatayı alacaksınızdır:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-19-statements-vs-expressions/src/main.rs}}
```

Bu programı çalıştırırsanız, şuna benzer bir hata alacaksınızdır:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-19-statements-vs-expressions/output.txt}}
```

`let y = 6` ifade yapısı bir değer döndürmez, 
dolayısıyla `x`'e atanacak bir şey yoktur. 
Bu, atamanın; atanan değeri döndürdüğü C ve Ruby gibi diğer dillerden farklıdır. 
Bu dillerde `x = y = 6` yazabilir ve hem `x` hem de `y`'nin `6` değerine sahip olmasını sağlayabilirsiniz; 
ancak Rust'ta durum böyle değil.

İfadeler bir değer olarak değerlendirilir ve Rust'ta yazacağınız kodun geri kalanının çoğunu oluşturur. 
`11` değerini veren bir ifade olan `5 + 6` gibi bir matematik işlemini düşünün. 
İfadeler ifade yapılarının bir parçası olabilir: 
Liste 3-1'de, `let y = 6` ifade yapısındaki `6`; `6` değerini `y`'ye veren bir ifadedir. 
Bir fonksiyonu çağırmak bir ifadedir. 
Makro çağırmak bir ifadedir. Parantezlerle oluşturulan bir kapsam bloğu bir ifadedir, örneğin:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-20-blocks-are-expressions/src/main.rs}}
```

Bu ifade:

```rust,ignore
{
    let x = 3;
    x + 1
}
```

bu durumda `4` olarak değerlendirilen bir bloktur. 
Bu değer, `let` ifade yapısının bir parçası olarak `y`'ye atanır. 
Şu ana kadar gördüğünüz çoğu satırın aksine `x + 1` satırının sonunda noktalı virgül bulunmadığına dikkat edin. 
İfadeler en sona noktalı virgül eklenmesini gerektirmez. 
Bir ifadenin sonuna noktalı virgül eklerseniz, onu bir ifade yapısına dönüştürürsünüz ve o, bir değer döndürmeyecektir. 
Bir sonraki başlığımız olacak olan fonksiyon dönüş değerlerini 
ve ifadelerini keşfederken bunu aklınızda bulundurun.

### Dönüş Değerleri Olan Fonksiyonlar

Fonksiyonlar, onları çağıran koda değerler döndürebilir. 
Dönüş değerlerini adlandırmıyoruz, ancak türlerini bir oktan (`->`) sonra bildirmeliyiz. 
Rust'ta, fonksiyonun dönüş değeri, bir fonksiyonun gövdesinin bloğundaki son ifadenin değeri ile eş anlamlıdır. 
`return` anahtar sözcüğünü kullanarak ve ona bir değer belirterek bir fonksiyondan erken dönebilirsiniz, 
ancak çoğu fonksiyon son ifadeyi örtük yapıda döndürür. Değer döndüren bir fonksiyon örneği:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-21-function-return-values/src/main.rs}}
```

`five` fonksiyonunda hiçbir fonksiyon çağrısı, 
makro ve hatta `let` ifade yapısı yoktur; 
yalnızca `5` sayısı tek başınadır. Bu, Rust'ta tamamen geçerli bir fonksiyondur. 
Fonksiyon dönüş türünün de `-> i32` olarak belirtildiğine dikkat edin. 

Bu kodu çalıştırmayı deneyin -hatasız çalışmalıdır-, çıktı şöyle görünmelidir:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-21-function-return-values/output.txt}}
```

`five`'ın içindeki `5`, fonksiyonun dönüş değeridir, 
bu nedenle fonksiyonun dönüş türü `i32`'dir. 
Bunu daha detaylı inceleyelim. İki önemli yer vardır: birincisi, `let x = five();` satırı bir değişkeni başlatmak için bir fonksiyonun
dönüş değerini kullandığımızı gösterir. `five` fonksiyonu `5` döndürdüğünden, bu satır aşağıdakiyle tamamen aynıdır:

```rust
let x = 5;
```

Ayrıca, `five` fonksiyonunda parametre yoktur ve dönüş değerinin türünü tanımlayan herhangi bir ifade yoktur, 
ancak işlevin gövdesi, değerini döndürmek istediğimiz bir ifade olduğu için noktalı virgül içermez. Yalnızca 
`5` yazabiliriz.

Başka bir daha örneğe bakalım:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-22-function-parameter-and-return/src/main.rs}}
```

Bu kodu çalıştırmak bize `The value of x is: 6` çıktısını verecektir. 
Ancak `x + 1`'i içeren satırın sonuna noktalı virgül koyarsak, 
onu bir ifadeden bir ifade yapısına çevirmiş oluruz ve bir hata alırız.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-23-statements-dont-return-values/src/main.rs}}
```

Bu kodun derlenmesi aşağıdaki gibi bir hata üretir:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-23-statements-dont-return-values/output.txt}}
```

Hata mesajı, “uyumsuz türler“ bu kodla ilgili temel sorunu ortaya koymaktadır. 
`plus_one` fonksiyonunun tanımı, bir `i32` döndüreceğini söyler, 
ancak ifade yapısı `()` ile ifade edilen bir değer olarak değerlendirilmez. İstenilen şey `i32` türünde döndürmektir,
bir ifade yapısının döndürdüğü gibi `()`'i döndürmek değildir.
Bu nedenle, fonksiyon tanımıyla çelişen ve bir hatayla sonuçlanan hiçbir şey döndürülmemiş olur. 
Bu çıktıda Rust, muhtemelen bu sorunun düzeltilmesine yardımcı olacak bir mesaj gösterir: 
Rust, noktalı virgülün kaldırılmasını önerir, bu da hatayı düzeltir.
