## Veri Türleri

Rust'taki her değer, 
Rust'a bu verilerle nasıl çalışacağını bilmesi için ne tür verilerin belirtildiğini söyleyen belirli bir veri türündendir. İki veri türü alt kümesine bakacağız: skaler ve bileşik. 

Rust'ın *statik yazılmış* bir dil olduğunu unutmayın; bu, derleme zamanında tüm değişkenlerin türlerini bilmesi gerektiği anlamına gelir. Derleyici genellikle değere ve onu nasıl kullandığımıza bağlı olarak ne tür kullanmak istediğimizi çıkarabilir. 
Birçok türün mümkün olduğu durumlarda, örneğin Bölüm 2'deki [“Tahminle Gizli Numarayı Karşılaştırma”][comparing-the-guess-to-the-secret-number]<!-- ignore --> bölümünde `parse`'ı kullanarak bir `String`'i sayısal bir türe dönüştürdüğümüzde, aşağıdaki gibi bir tür ek açıklaması eklemeliyiz:

```rust
let guess: u32 = "42".parse().expect("Not a number!");
```

Yukarıdaki gibi `: u32` tipini eklemezsek, Rust aşağıdaki hatayı döndürür, 
bu da derleyicinin hangi türü kullanmak istediğimizi bilmesi için bizden daha fazla bilgiye ihtiyacı olduğu anlamına gelir:

```console
{{#include ../listings/ch03-common-programming-concepts/output-only-01-no-type-annotations/output.txt}}
```

Diğer veri türleri için farklı tür ek açıklamaları göreceksiniz.

### Skaler Tipler

Bir *skaler* tip, tek bir değeri temsil eder. 
Rust'ın dört birincil skaler türü vardır: 
tamsayılar, kayan noktalı sayılar, Boole'ler ve karakterler. 
Bunları diğer programlama dillerinden tanıyabilirsiniz. Hadi Rust'ta nasıl çalıştıklarına geçelim.

#### Tam Sayı Türleri

Tam sayı, kesirli bileşeni olmayan bir sayıdır. 
Bölüm 2'de bir tam sayı türü olan `u32` türü kullandık. Bu tür bildirimi, ilişkilendirildiği değerin 32 bit yer kaplayan işaretsiz bir tam sayı (işaretli tamsayı türleri `u` yerine `i` ile başlar) olması gerektiğini belirtir. 
Tablo 3-1, Rust'taki yerleşik tam sayı türlerini gösterir. Bir tam sayı değerinin türünü bildirmek için bu değişkenlerden herhangi birini kullanabiliriz. 


<span class="caption">Tablo 3-1: Rust'ta Tam Sayı Türleri</span>

| Büyüklüğü  | İşaretli  | İşaretsiz |
|---------|---------|----------|
| 8-bit   | `i8`    | `u8`     |
| 16-bit  | `i16`   | `u16`    |
| 32-bit  | `i32`   | `u32`    |
| 64-bit  | `i64`   | `u64`    |
| 128-bit | `i128`  | `u128`   |
| (mimariye bağlı) | `isize` | `usize`  |

Her varyant işaretli veya işaretsiz olabilir ve açık bir boyutu vardır. 
*İşaretli* ve *işaretsiz*, sayının negatif olmasının mümkün olup olmadığına - başka bir deyişle, sayının onunla bir işareti olması gerekip gerekmediğine (işaretli) veya yalnızca pozitif olup olmayacağına ve bu nedenle işaretsiz temsil edilip edilemeyeceğine (işaretsiz) atıfta bulunur. Kağıda sayılar yazmak gibidir: işaret önemli olduğunda, bir sayı artı veya eksi işaretiyle gösterilir; ancak, sayının pozitif olduğunu varsaymak güvenli olduğunda, işaretsiz olarak gösterilir. İşaretli sayılar, [ikinin tümleyen gösterimi](https://en.wikipedia.org/wiki/Two%27s_complement)<!-- ignore --> 
kullanılarak saklanır. 

Her işaretli varyant -(2<sup>n - 1</sup>) ile 2<sup>n - 1</sup> - 1 arasındaki sayıları saklayabilir; 
burada n, varyantın kullandığı bit sayısıdır.

Böylece bir `i8`, -(2<sup>7</sup>) ile 2<sup>7</sup> - 1 arasındaki sayıları saklayabilir, bu da -128 ile 127'ye eşittir. 
İşaretsiz değişkenler 0 ile 2<sup>n</sup> - 1 arasındaki sayıları saklayabilir, dolayısıyla bir `u8` 0 ile 2<sup>8</sup> - 1 arasındaki sayıları saklayabilir, bu da 0 ila 255'e eşittir. 

Ek olarak, `isize` ve `usize`, programınızın üzerinde çalıştığı bilgisayarın mimarisine bağlıdır; bu, tabloda “arch” olarak gösterilir: 
64 bit mimarideyseniz 64 bit; 32 bit eğer 32 bit mimarideyseniz. Tam sayı değişmezlerini Tablo 3-2'de gösterilen herhangi bir biçimde yazabilirsiniz. 
Birden çok sayısal tür olabilen sayı değişmezlerinin, `57u8` gibi bir tür son ekinin türü belirlemesine izin verdiğini unutmayın. 
Sayı değişmezleri, `1000` şeklinde belirttiğiniz gibi aynı değere sahip olacak `1_000` gibi sayının okunmasını kolaylaştırmak için görsel ayırıcı olarak `_`'yi de kullanabilir.

<span class="caption">Tablo 3-2: Rust'ta Tamsayı Değişmezlerit</span>

| Sayı değişmezleri  | Örnek       |
|------------------|---------------|
| Ondalıklı          | `98_222`      |
| On altılık tabanda              | `0xff`        |
| Sekizlik tabanda            | `0o77`        |
| İkilik tabanda         | `0b1111_0000` |
| Bit (sadece `u8` türü için) | `b'A'`        |

Peki hangi tamsayı türünü kullanacağınızı nasıl bileceksiniz? 
Emin değilseniz, Rust'ın varsayılanları genellikle başlamak için iyi yerlerdir: 
tam sayı türleri varsayılan olarak `i32`'dir. `isize` veya `usize` kullandığınız birincil durum, bir tür koleksiyonu dizine eklerken olur.

> ##### Tam sayı taşması
>  Diyelim ki 0 ile 255 arasında değerler tutabilen `u8` tipinde bir değişkeniniz var. 
>  Değişkeni bu aralığın dışında, örneğin 256 gibi bir değerle değiştirmeye çalışırsanız, *tam sayı taşması* meydana gelir ve 
>  bu iki davranıştan biriyle sonuçlanabilir. Hata ayıklama modunda derleme yaparken; Rust, 
>  bu davranış ortaya çıkarsa programınızın çalışma zamanında panik yapmasına neden olan *tam sayı taşması* için bazı kontroller içerir. 
>  Rust, bir program bir hatayla çıktığında panikleme terimini kullanır; [“`panic!`'le Kurtarılamayan Hatalar”][unrecoverable-errors-with-panic]<!-- ignore -->  bölümünde panikleri daha derinlemesine tartışacağız. 
> `--release` bayrağıyla yayın modunda derlediğinizde, Rust paniklere neden olan *tam sayı taşması* denetimlerini içermez. 
> Bunun yerine, taşma meydana gelirse, Rust iki tamamlayıcı sarmayı gerçekleştirir. Kısacası, türün tutabileceği maksimum değerden daha büyük 
> değerler, türün tutabileceği değerlerin minimumuna "sarılır". Bir `u8` durumunda, 256 değeri 0 olur, 257 değeri 1 olur ve bu böyle devam eder. 
> Program paniğe kapılmaz, ancak değişken muhtemelen beklediğiniz değerde olmayan bir değere sahip olacaktır. *Tam sayı taşmasının* sarma davranışına 
> güvenmek bir hata olarak kabul edilir. 
> 
> Taşma olasılığını açıkça ele almak için, ilkel sayı türleri için standart kütüphane tarafından sağlanan bu metodları kullanabilirsiniz:
> 
> - `wrapping_add` gibi `wrapping_*` metodlarını kullanabilirsiniz
> - `checked_*` yöntemlerinde taşma varsa `None` değerini döndürebilirsiniz
> - `overflowing_*` yöntemleriyle taşma olup olmadığını gösteren değeri Boole olarak döndürebilirsiniz
> - `saturating_*` yöntemleri ile değerin minimum veya maksimum değerlerinde 'doyurun' 

#### Kayan Nokta Türleri

Rust ayrıca ondalık basamaklı sayılar olan *kayan noktalı sayılar* için iki temel türe sahiptir. 
Rust'ın kayan nokta türleri, sırasıyla 32 bit ve 64 bit boyutunda olan `f32` ve `f64`'tür. 
Varsayılan tür `f64`'tür çünkü modern CPU'larda kabaca `f32` ile aynı hızdadır ancak daha fazla hassasiyete sahiptir. 
Tüm kayan nokta türleri işaretlidir. 

İşte kayan noktalı sayılarını çalışırken gösteren bir örnek:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-06-floating-point/src/main.rs}}
```

Kayan nokta sayıları, *IEEE-754 standardına* göre tanımlanmıştır. `f32` türü tek duyarlıklı bir kayan noktadır 
ve `f64` çift duyarlıklıdır.

#### Sayısal İşlemler

Rust, tüm sayı türleri için beklediğiniz temel matematiksel işlemleri destekler: 
toplama, çıkarma, çarpma, bölme ve kalan. 

Tam sayı bölümü en yakın tam sayıya yuvarlar. 

Aşağıdaki kod, bir `let` ifadesinde her bir sayısal işlemi nasıl kullanacağınızı gösterir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-07-numeric-operations/src/main.rs}}
```

Bu ifadelerdeki her ifade bir matematiksel operatör kullanır ve daha sonra bir değişkene bağlanan tek bir değer olarak değerlendirilir. 
[Ekleme B][appendix_b]<!-- ignore -->, Rust'ın sağladığı tüm operatörlerin bir listesini içerir.

#### Boole Türü

Diğer programlama dillerinin çoğunda olduğu gibi, Rust'ta da bir Boole türünün iki olası değeri vardır: 
`true` ve `false`. Boole'ların boyutu bir bayttır. Rust'taki Boole türü `bool` kullanılarak belirtilir. 

Örneğin:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-08-boolean/src/main.rs}}
```

Boole değerlerini kullanmanın ana yolu, `if` ifadesi gibi koşullu ifadelerdir. 
Rust'ta ifadelerin nasıl çalışacağını [“Kontrol Akışı”][control-flow]<!-- ignore --> bölümünde ele alacağız.

#### Karakter Türü

Rust'ın `char` türü, dilin en temel alfabetik türüdür. 

Aşağıda, `char` değerlerinin bildirilmesine ilişkin bazı örnekler verilmiştir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-09-char/src/main.rs}}
```

Çift tırnak kullanan dize değişmezlerinin aksine, 
tek tırnaklı `char` değişmezlerini belirttiğimize dikkat edin. 
Rust'ın `char` türü, dört bayt boyutundadır ve bir Unicode Skaler Değerini temsil eder; 
bu, yalnızca ASCII'den çok daha fazlasını temsil edebileceği anlamına gelir. 

Aksanlı harfler; Çince, Japonca ve Korece karakterler; emoji; ve sıfır genişlikli boşlukların tümü Rust'taki geçerli karakter değerleridir. 

Unicode Skaler Değerleri, `U+0000` ile `U+D7FF` ve `U+E000` ile `U+10FFFF` dahil arasında değişir. 
Bununla birlikte, bir `char`, Unicode'da gerçekten bir kavram değildir, bu nedenle, bir “karakterin” ne olduğuna ilişkin insan sezginiz, 
Rust'ta bir karakterin ne olduğu ile eşleşmeyebilir. Bu konuyu Bölüm 8'deki [“UTF-8 Kodlu Metinleri Dizgilerde Depolama”][strings]<!-- ignore --> bölümünde ayrıntılı olarak tartışacağız.

### Bileşik Türler

*Bileşik türler* birden çok değeri tek bir türde gruplayabilir. 
Rust'ın iki temel bileşik türü vardır: demetler ve diziler.

#### Demet Türü

Demet, çeşitli türlere sahip bir dizi değeri tek bir bileşik türde gruplandırmanın genel bir yoludur. 
Demetlerin sabit bir uzunluğu vardır: bir kez bildirildiğinde, boyut olarak büyüyemez veya küçülemezler.

Parantez içinde virgülle ayrılmış bir değerler listesi yazarak bir demet oluşturuyoruz. 
Tanımlama grubundaki her konumun bir türü vardır ve tanımlama grubundaki farklı değerlerin türlerinin aynı olması gerekmez.
Bu örnekte isteğe bağlı tür ek açıklamaları ekledik:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-10-tuples/src/main.rs}}
```

Değişken `tup`'a bütün bir demet atanır, çünkü bir demet tek bir bileşik eleman olarak kabul edilir. 
Bir tanımlama grubundan tek tek değerleri elde etmek için, bir tanımlama grubunu yok etmek için *model eşleştirmeyi* kullanabiliriz, 
bunun gibi:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-11-destructuring-tuples/src/main.rs}}
```

Bu program önce bir tanımlama grubu oluşturur ve onu `tup` değişkenine atar. Daha sonra `let` `tup`'u alıp onu `x`, `y` ve `z` 
olmak üzere üç ayrı değişkene dönüştüren bir model kullanır. Buna *yıkım* denir, çünkü tek demeti üç parçaya böler. 
Son olarak, program `y`'nin `6.4` değerini yazdırır.

Ayrıca, bir nokta (`.`) ve ardından erişmek istediğimiz değerin sırasını (indeksini) kullanarak bir 
tanımlama grubu öğesine doğrudan erişebiliriz. 

Örneğin:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-12-tuple-indexing/src/main.rs}}
```

Bu program, `x` demetini oluşturur ve ardından ilgili dizinleri kullanarak grubun her bir öğesine erişir. 
Çoğu programlama dilinde olduğu gibi, bir demet içindeki ilk sıra 0'dır.

Herhangi bir değeri olmayan demetin özel bir adı vardır, birim. Bu değer ve buna karşılık gelen tür yazılır `()` 
ve boş bir değeri veya boş bir dönüş türünü temsil eder. 
İfadeler, başka bir değer döndürmezlerse örtük olarak birim değerini döndürür.

#### Dizi Türü

Birden çok değerden oluşan bir koleksiyona sahip olmanın başka bir yolu da dizi kullanmaktır. 
Demetlerden farklı olarak, bir dizinin her elemanı aynı tipte olmalıdır. 
Diğer bazı dillerdeki dizilerin aksine, Rust'taki dizilerin sabit bir uzunluğu vardır.

Bir dizideki değerleri köşeli parantezler içinde virgülle ayrılmış bir liste olarak yazıyoruz:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-13-arrays/src/main.rs}}
```

Diziler, verilerinizin yığın yerine yığına atanmasını istediğinizde (yığın (stack) ve yığıtı (heap) [Bölüm 4][stack-and-heap]<!-- ignore -->'te daha fazla tartışacağız) veya her zaman sabit sayıda öğeye sahip olduğunuzdan emin olmak istediğinizde yararlıdır. 
Yine de bir dizi, vektör türü kadar esnek değildir. 
Vektör, standart kütüphane tarafından sağlanan ve boyutunun büyümesine veya küçülmesine izin verilen benzer bir koleksiyon türüdür. 
Dizi mi yoksa vektör mü kullanacağınızdan emin değilseniz, büyük olasılıkla bir vektör kullanmalısınız. [Bölüm 8][vectors]<!-- ignore -->, vektörleri daha ayrıntılı olarak tartışır.

Ancak diziler, eleman sayısının değişmesi gerekmediğini bildiğiniz zaman daha kullanışlıdır.
Örneğin, bir programda ayın adlarını kullanıyor olsaydınız, her zaman 12 öğe içereceğini bildiğiniz için vektör yerine muhtemelen bir dizi kullanırdınız:


```rust
let months = ["January", "February", "March", "April", "May", "June", "July",
              "August", "September", "October", "November", "December"];
```

Her öğenin türü, noktalı virgül ve ardından dizideki öğelerin sayısıyla birlikte köşeli
parantezler kullanarak bir dizinin türünü yazarsınız, şöyle:

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

Burada `i32`, her bir elemanın tipidir. 
Noktalı virgülden sonraki `5` sayısı, dizinin beş eleman içerdiğini gösterir.

Ayrıca, burada gösterildiği gibi ilk değeri, ardından noktalı virgül ve
ardından dizinin uzunluğunu köşeli parantez içinde belirterek her öğe için aynı değeri içerecek bir dizi tanımlayabilirsiniz:

```rust
let a = [3; 5];
```

`a` adlı dizi, tümü başlangıçta `3` değerine ayarlanacak `5` öğe içerecektir. Bu, `let a = [3, 3, 3, 3, 3];` ile aynıdır
fakat daha yalındır.

##### Dizi Öğelerine Erişim

Dizi, yığına ayrılabilen, bilinen, sabit boyuttaki tek bir bellek yığınıdır. 
Dizinin öğelerine aşağıdaki gibi dizin oluşturmayı kullanarak erişebilirsiniz:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-14-array-indexing/src/main.rs}}
```

Bu örnekte, `first` olarak adlandırılan değişken `1` değerini alacaktır, çünkü bu, dizideki `[0]` dizinindeki değerdir. 
`second` adlı değişken, dizideki `[1]` dizininden `2` değerini alacaktır.

##### Geçersiz Dizi Öğesine Erişmek

Dizinin sonunu aşan bir dizinin bir öğesine erişmeye çalışırsanız ne olacağını birlikte görelim. 
Kullanıcıdan bir dizi sırası almak için Bölüm 2'deki tahmin oyununa benzer şekilde 
bu kodu çalıştırdığınızı varsayalım:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,panics
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-15-invalid-array-access/src/main.rs}}
```

Bu kod başarıyla derlenir. Bu kodu `cargo run` komutunu kullanarak çalıştırırsanız ve girdi olarak
0, 1, 2, 3 veya 4 girerseniz, program dizideki o sırada karşılık gelen değeri yazdıracaktır.
Bunun yerine, 10 gibi; dizinin sonunu aşan bir sayı girerseniz, şöyle bir çıktı görürsünüz:

<!-- manual-regeneration
cd listings/ch03-common-programming-concepts/no-listing-15-invalid-array-access
cargo run
10
-->

```console
thread 'main' panicked at 'index out of bounds: the len is 5 but the index is 10', src/main.rs:19:19
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

Program, *sırasına koyma* (indeksleme) işleminde geçersiz bir değer kullanma noktasında 
bir *çalışma zamanı hatasıyla* sonuçlandı. Program bir hata mesajıyla çıktı ve son eklenen `println`'i çalıştırmadı! 
Sırasına koyma işlemini kullanarak bir elemana erişmeye çalıştığınızda Rust, 
belirttiğiniz sıranın dizi uzunluğundan küçük olup olmadığını kontrol eder. Sıra, uzunluktan büyük veya ona eşitse, 
Rust panikleyecektir. Bu kontrol, özellikle bu durumda ve bu kodda, çalışma zamanında yapılmalıdır, çünkü derleyici, 
bir kullanıcının kodu daha sonra çalıştırdığında hangi değeri gireceğini muhtemelen bilemez.

Bu, Rust'ın bellek güvenliği ilkelerinin uygulamadaki bir örneğidir. 
Birçok düşük seviyeli dilde bu tür bir kontrol yapılmaz ve yanlış bir sıra verdiğinizde geçersiz hafızaya erişilebilir. 
Rust, belleğe erişime izin vermek ve devam etmek yerine hemen çıkarak sizi bu tür hatalara karşı korur. 

Bölüm 9, Rust'ın hata işlemesini ve panik yaratmayan veya geçersiz bellek erişimine izin vermeyen okunabilir, güvenli kodu nasıl yazabileceğinizi daha fazla tartışıyor olacak.

[comparing-the-guess-to-the-secret-number]:
ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number
[control-flow]: ch03-05-control-flow.html#control-flow
[strings]: ch08-02-strings.html#storing-utf-8-encoded-text-with-strings
[stack-and-heap]: ch04-01-what-is-ownership.html#the-stack-and-the-heap
[vectors]: ch08-01-vectors.html
[unrecoverable-errors-with-panic]: ch09-01-unrecoverable-errors-with-panic.html
[wrapping]: ../std/num/struct.Wrapping.html
[appendix_b]: appendix-02-operators.md
