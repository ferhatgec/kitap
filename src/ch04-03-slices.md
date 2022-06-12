## Dilim Türü

*Dilimler*, koleksiyonun tamamı yerine bir koleksiyondaki bitişik bir öğe dizisine başvurmanıza izin verir. 
Bir dilim bir tür referanstır, dolayısıyla sahipliği yoktur.

İşte küçük bir programlama problemi: boşluklarla ayrılmış bir dizi sözcük alan ve bu dizgide bulduğu 
ilk sözcüğü döndüren bir fonksiyon yazın. Fonksiyon dizgide bir boşluk bulamazsa, dizgi döndürülmelidir.

Dilimlerin çözeceği problemi anlamak için dilimleri kullanmadan bu fonksiyonun imzasını 
nasıl yazacağımız üzerinde çalışalım:

```rust,ignore
fn first_word(s: &String) -> ?
```

`first_word` fonksiyonu, parametre olarak bir `&String`'e sahiptir. 
Sahiplik istemiyoruz, bu yüzden referansını kullanıyoruz. 
Ama neyi iade etmeliyiz? Bir dizginin bir *parçası* hakkında konuşmanın gerçekten bir yolu yok. 
Ancak, bir boşlukla gösterilen kelimenin sonunun indeksini döndürebiliriz. 

Bunu Liste 4-7'de gösterildiği gibi deneyelim.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-07/src/main.rs:here}}
```

<span class="caption">Liste 4-7: `String` parametresine bir bayt indeks değeri döndüren `first_word` fonksiyonu</span>

`String` öğesini eleman bazında gözden geçirmemiz ve bir değerin boşluk olup olmadığını kontrol etmemiz gerektiğinden, 
`as_bytes` metodunu kullanarak `String`'imizi bir bayt dizgisine dönüştüreceğiz:

```rust,ignore
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-07/src/main.rs:as_bytes}}
```

Ardından, `iter` metodunu kullanarak bayt dizgisi üzerinde bir yineleyici oluştururuz.

```rust,ignore
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-07/src/main.rs:iter}}
```

Yineleyicileri [Bölüm 13][ch13]<!-- ignore -->'te daha ayrıntılı olarak tartışacağız. 
Şimdilik, `iter`'ın bir koleksiyondaki her öğeyi döndüren bir metod olduğunu ve numaralandırmanın yinelemenin 
sonucunu sararak bunun yerine her öğeyi bir demetin parçası olarak döndürdüğünü bilin. 

Numaralandırmadan döndürülen grubun ilk öğesi indekstir ve ikinci öğe öğeye bir referanstır. 

Bu, indeksi kendimiz hesaplamaktan biraz daha uygundur.

Numaralandırma metodu bir tanımlama grubu döndürdüğü için, bu tanımlama grubunu yok etmek için modelleri kullanabiliriz. 
Modelleri Bölüm 6'da daha fazla tartışacağız. `for` döngüsünde, tanımlama grubundaki indeks için `i` ve 
tanımlama grubundaki tek bayt için `&item` içeren bir model belirledik. `.iter().enumerate()` öğesinden öğeye bir referans aldığımız için, 
kalıpta `&` kullanırız.

`for` döngüsünün içinde, değişmez bayt söz dizimini kullanarak alanı temsil eden baytı ararız. 
Bir boşluk bulursak, konumu döndürürüz. 
Aksi takdirde, `s.len()` kullanarak dizginin uzunluğunu döndürürüz:

```rust,ignore
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-07/src/main.rs:inside_for}}
```

Artık dizgideki ilk kelimenin sonunun indeksini bulmanın bir yolu var, ancak bir sorun var. 
`usize`'ı kendi başına döndürüyoruz, ancak bu yalnızca `&String` bağlamında anlamlı bir sayıdır. 
Başka bir deyişle, `String`'den ayrı bir değer olduğu için gelecekte hala geçerli olacağının garantisi yoktur. 
Liste 4-7'deki `first_word` fonksiyonunu kullanan Liste 4-8'deki programı düşünün.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-08/src/main.rs:here}}
```

<span class="caption">Liste 4-8: `first_word` fonksiyonunun sonucunu saklama ve 
ardından `String` içeriğini değiştirme</span>

Bu program hatasız derlenir ve `s.clear()`'ı çağırdıktan sonra 
`word` kullansaydık da derlenecekti. `word`, `s` durumuna hiç bağlı olmadığı için, `word` `5` değerini içerir. 
İlk `word`'ü çıkarmaya çalışmak için bu `5` değerini `s` değişkeni ile kullanabiliriz, 
ancak bu bir hata olur çünkü `word`'e `5`'i atadığımızdan beri `s`'in içeriği değişti.

`word`'deki indeksin `s`'deki verilerle senkronizasyondan çıkması konusunda endişelenmek sıkıcı ve hataya açık! 
Bir `second_word` fonksiyonu yazarsak, bu dizginleri yönetmek daha rahat olacak. Yapısı şöyle görünmelidir:

```rust,ignore
fn second_word(s: &String) -> (usize, usize) {
```

Şimdi bir başlangıç ve bitiş indeksini izliyoruz ve belirli bir durumdaki verilerden hesaplanan ancak bu duruma hiç 
bağlı olmayan daha da fazla değere sahibiz. Senkronize tutulması gereken, etrafta dolaşan üç alakasız değişkenimiz var.

Şansımıza ki, Rust'ın bu soruna bir çözümü var: `String` dilimleri.

### Dizgi Dilimleri

*Dizgi dilimi*, bir dizginin parçasına yapılan bir başvurudur ve şöyle görünür:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-17-slice/src/main.rs:here}}
```

`hello`, `String`'in tamamına bir referans yerine, `String`'in fazladan `[0..5]` bitinde belirtilen bir bölümüne referanstır. `[starting_index..end_index]`'i belirterek parantez içinde bir aralık kullanarak dilimler oluştururuz, burada `starting_index` dilimdeki ilk konumdur ve `end_index` dilimdeki son konumdan bir fazlasıdır. Dahili olarak, dilim veri yapısı, son eksi başa karşılık gelen başlangıç konumunu ve dilimin uzunluğunu saklar. Yani `let world = &s[6..11];` durumunda; `world` `s`'in indeks `6`'sındaki bayta işaretçi ve uzunluk değeri `5` olan bir dilim olacaktır.

<img alt="world, s'in indeks 6'daki pointerını ve 5 uzunluğunu tutan işaretçiyi tutar" src="img/trpl04-06.svg" class="center" style="width: 50%;" />

<span class="caption">Şekil 4-6: Bir `String` parçasına atıfta bulunan dizgi dilimi
</span>

Rust'ın `..` aralık söz dizimi ile indeks sıfırdan başlamak istiyorsanız, 
değeri iki noktadan önce bırakabilirsiniz. Başka bir deyişle, bunlar eşittir:

```rust
let s = String::from("hello");

let slice = &s[0..2];
let slice = &s[..2];
```

Aynı şekilde, diliminiz `String`'in son baytını içeriyorsa, sondaki sayıyı bırakabilirsiniz.
Demek ki bunlar eşit:

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[3..len];
let slice = &s[3..];
```

Ayrıca tüm dizgiden bir dilim almak için her iki değeri de bırakabilirsiniz:

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[0..len];
let slice = &s[..];
```


> Not: `String` dilim aralığı indeksleri, geçerli UTF-8 karakter sınırlarında oluşmalıdır. 
> Çok baytlı bir karakterin ortasında bir dizgi dilimi oluşturmaya çalışırsanız, 
> programınız bir hata ile çıkacaktır. Dize dilimlerini tanıtmak amacıyla, sadece bu bölümde ASCII'yi varsayıyoruz; 
> UTF-8 işleme hakkında daha ayrıntılı bir tartışma, 
> Bölüm 8'in [“UTF-8 Kodlu Metni Dizgilerde Depolama”][strings]<!-- ignore --> kısmındadır.

Tüm bu bilgileri göz önünde bulundurarak, bir dilim döndürmek için `first_word`'ü yeniden yazalım. 
“Dizgi dilimi” anlamına gelen tür `&str` olarak yazılır:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-18-first-word-slice/src/main.rs:here}}
```

Bir boşluğun ilk oluşumunu arayarak, Liste 4-7'de yaptığımız gibi, kelimenin sonu için indeksi elde ederiz. 
Bir boşluk bulduğumuzda, başlangıç ve bitiş indeksleri olarak dizgenin başlangıcını ve boşluğun indeksini kullanarak bir dizgi dilimi 
döndürürüz.

Şimdi `first_word`'ü çağırdığımızda, temeldeki verilere bağlı tek bir değeri geri alıyoruz. Değer, dilimin başlangıç noktasına yapılan bir referanstan ve dilimdeki eleman sayısından oluşur.

Bir dilim döndürmek, `second_word` fonksiyonu için de işe yarar:

```rust,ignore
fn second_word(s: &String) -> &str {
```

Derleyici, `String`'e yapılan referansların geçerli kalmasını sağlayacağından, artık karıştırılması çok daha zor olan basit bir API'ye sahibiz. 
Liste 4-8'deki programdaki hatayı hatırlıyor musunuz? İndeksi ilk kelimenin sonuna kadar aldık ama sonra dizgiyi temizledik, 
böylece dizgimiz geçersiz olmuştu. Bu kod mantıksal olarak yanlıştı ancak herhangi bir hata göstermemişti. 
İlk kelime dizgisini boş bir dizgiyle kullanmaya devam edersek, sorunlar daha sonra ortaya çıkacaktı. 
Dilimler bu hatayı imkansız kılar ve kodumuzla ilgili bir sorunumuz olduğunu çok daha erken bildirir. 
`first_word`'ün dilim sürümünü kullanmak derleme zamanı hatası verir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-19-slice-error/src/main.rs:here}}
```

Derleyici hatası:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-19-slice-error/output.txt}}
```

Ödünç alma kurallarından hatırlayın, eğer bir şeye değişmez bir referansımız varsa, 
aynı zamanda değişken bir referans alamayız. `clear`'ın `String`'i temizlemesi gerektiğinden, 
değişken bir referans alması gerekir. `println!` `clear` çağrısı `word`'deki referansı kullandıktan sonra, 
değişmez referansın o noktada hala aktif olması gerekir. Rust, `clear` değiştirilebilir referansın ve `word`'deki değişmez referansın aynı anda var olmasına izin vermez ve derleme başarısız olur. Rust yalnızca API'mizin kullanımını kolaylaştırmakla kalmadı, aynı zamanda derleme zamanında bir dizi hatayı da ortadan kaldırdı!

#### Dizgi Değişmezleri Bir Tür Dilimdir

İkili dosyada saklanan dizgi değişmezlerinden bahsettiğimizi hatırlayın. Artık dilimleri bildiğimize göre, dizgi değişmezlerini doğru bir şekilde anlayabiliriz:

```rust
let s = "Hello, world!";
```

Buradaki `s`'in türü `&str`'dir: ikili dosyanın o belirli noktasına işaret eden bir dilimdir. 
Bu aynı zamanda dizgi değişmezlerinin değişmez olmasının nedenidir; `&str` değişmez bir referanstır.

#### Parametre Olarak Dizgi Dilimleri

Hazır bilgi ve `String` değerlerinin dilimlerini alabileceğinizi bilmek, 
bizi `first_word` üzerinde bir iyileştirmeye daha götürüyor ve bu da onun yapısı:

```rust,ignore
fn first_word(s: &String) -> &str {
```

Daha deneyimli bir Rustsever, bunun yerine Liste 4-9'da gösterilen yapıyı kullanırdı çünkü aynı fonksiyonu hem 
`&String` hem de `&str` değerlerinde kullanmamıza izin verir.

```rust,ignore
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-09/src/main.rs:here}}
```

<span class="caption">Liste 4-9: `s` parametresinin türü için bir dizgi dilimi kullanarak `first_word` 
fonksiyonunu geliştirme</span>

Eğer elimizde bir dizgi dilimi varsa onu direkt argüman olarak verebiliriz. 
Bir `String`'imiz varsa, `String`'in bir dilimini veya `String`'e bir referansı iletebiliriz. 
Bu esneklik, Bölüm 15'in [“Fonksiyonlar ve Metodlarla Örtülü Deref Zorlamaları”][deref-coercions]<!--ignore--> bölümünde ele alacağımız bir özellik olan deref zorlamalarından yararlanır. 
Bir `String` referansı yerine bir dizgi dilimi alacak bir fonksiyon tanımlamak, 
API'mizi daha genel ve herhangi bir işlevsellik kaybetmeden kullanmamızı sağlar:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-09/src/main.rs:usage}}
```

### Diğer Dilimler

Dizgi dilimleri, tahmin edebileceğiniz gibi, dizgilere özgüdür. Ancak daha genel bir dilim türü de var. Bu diziyi düşünün:

```rust
let a = [1, 2, 3, 4, 5];
```

Tıpkı bir dizginin bir kısmına atıfta bulunmak isteyebileceğimiz gibi, 
bir dizinin bir kısmına atıfta bulunmak isteyebiliriz. Biz böyle yapardık:

```rust
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];

assert_eq!(slice, &[2, 3]);
```

Bu dilim `&[i32]` türüne sahip. İlk öğeye ve bir uzunluğa bir referans depolayarak, 
dizgi dilimlerinin yaptığını yapar. Bu tür dilimi diğer tüm koleksiyonlar için kullanacaksınız. 
Bölüm 8'de vektörler hakkında konuşurken bu koleksiyonları ayrıntılı olarak tartışacağız.

## Özet

Sahiplik, ödünç alma ve dilim kavramları, derleme zamanında Rust programlarında bellek güvenliğini sağlar. 
Rust dili, diğer sistem programlama dilleriyle aynı şekilde bellek kullanımınız üzerinde kontrol sağlar, 
ancak veri sahibinin kapsam dışına çıktığında bu verileri otomatik olarak temizlemesi, fazladan kod yazmanız ve 
hata ayıklamanız gerekmediği anlamına gelir. Bu kontrolü elde etmek için.

Sahiplik, Rust'ın diğer birçok bölümünün nasıl çalıştığını etkiler, bu yüzden kitabın geri kalanında bu kavramlar 
hakkında daha fazla konuşacağız. Bölüm 5'e geçelim ve veri parçalarını bir `struct` içinde gruplandırmaya bakalım.

[ch13]: ch13-02-iterators.html
[ch6]: ch06-02-match.html#patterns-that-bind-to-values
[strings]: ch08-02-strings.html#storing-utf-8-encoded-text-with-strings
[deref-coercions]: ch15-02-deref.html#implicit-deref-coercions-with-functions-and-methods
