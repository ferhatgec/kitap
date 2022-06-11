## Referanslar ve Ödünç Alma

Liste 4-5'teki tanımlama grubu koduyla ilgili sorun, `String`'i çağıran fonksiyona döndürmemizi gerektirmesidir, 
böylece `String`, `calculate_length` çağrısından sonra hala `String`'i kullanabiliriz, çünkü `String`, içine taşınmıştır. 
Bunun yerine, `String` değerine bir referans sağlayabiliriz. Referans, bir işaretçi gibidir, çünkü o adreste depolanan verilere erişmek için takip edebileceğimiz bir adrestir; bu veriler başka bir değişkene aittir. Bir işaretçiden farklı olarak, bir referansın, o referansın ömrü boyunca belirli bir türün geçerli bir değerine işaret etmesi garanti edilir. Değerin sahipliğini almak yerine parametre olarak bir nesneye referansı olan bir 
`calculate_length` fonksiyonunu nasıl tanımlayacağınız ve kullanacağınız aşağıda açıklanmıştır:

Değerin sahipliğini almak yerine parametre olarak bir nesneye referansı olan bir `calculate_length` fonksiyonunu nasıl tanımlayacağınız ve 
kullanacağınız aşağıda açıklanmıştır:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-07-reference/src/main.rs:all}}
```

İlk olarak, değişken bildirimindeki tüm tanımlama grubu kodunun ve fonksiyonun dönüş değerinin kaybolduğuna dikkat edin. 
İkinci olarak, `&s1`'i `calculate_length`'e ilettiğimizi ve tanımında `String` yerine `&String`'i kullandığımızı unutmayın. 
Bu ve bunun işaretleri, *referansları* temsil eder ve sahipliğini almadan bazı değerlere başvurmanıza izin verir. 
Şekil 4-5 bu konsepti tasvir ediyor.

<img alt="&amp;String s String s1'e işaret ediyor" src="img/trpl04-05.svg" class="center" />

<span class="caption">Şekil 4-5: `String s1`'i gösteren `&String s` diyagramı</span>

> Not: `&` kullanılarak yapılan referansın tersi, referanstan çıkarma operatörü `*` ile 
> gerçekleştirilen *referanstan çıkarma*'dır. Bölüm 8'de referans kaldırma operatörünün 
> bazı kullanımlarını göreceğiz ve 
> Bölüm 15'te referans kaldırmanın ayrıntılarını tartışacağız.

Fonksiyon çağrısına daha yakından bakalım:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-07-reference/src/main.rs:here}}
```

`&s1` söz dizimi, `s1` değerine refere eden ancak kendisine ait olmayan bir referans oluşturmamıza izin verir. 
Kendisine ait olmadığı için, referansın kullanımı durduğunda işaret ettiği değer düşürülmeyecektir. 
Benzer şekilde, fonksiyonun imzası, `s` parametresinin türünün bir referans olduğunu belirtmek için `&` kullanır. 

Bazı açıklayıcı notlar ekleyelim:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-08-reference-with-annotations/src/main.rs:here}}
```

`s` değişkeninin geçerli olduğu kapsam, herhangi bir fonksiyonla aynıdır.
Sahipliği olmadığı için `s` kullanımı durduğunda parametrenin kapsamı bırakılır. 
Ancak referansın gösterdiği değer bırakılmaz.
Sahipliği geri vermek için değerleri döndürmelisiniz, çünkü hiç sahibi olmadınız.
 
Peki ödünç aldığımız bir şeyi değiştirmeye çalışırsak ne olur?
Liste 4-6'daki kodu deneyin. Uyarı: çalışmıyor!

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-06/src/main.rs}}
```

<span class="caption">Liste 4-6: Ödünç alınan bir değeri değiştirmeye çalışmak</span>

İşte hata:

```console
{{#include ../listings/ch04-understanding-ownership/listing-04-06/output.txt}}
```

Değişkenler varsayılan olarak değişmez olduğu gibi, referanslar da öyledir. 
Referansımız olan bir şeyi değiştirmemize izin verilmez.

### Değişebilir Referanslar

Liste 4-6'daki kodu, ödünç alınan bir değeri, bunun yerine *değişken bir referans* kullanan sadece birkaç küçük ince ayar ile 
değiştirmemize izin verecek şekilde düzeltebiliriz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-09-fixes-listing-04-06/src/main.rs}}
```

İlk olarak, `s`'i `mut` olarak değiştiriyoruz. 
Sonra `&mut s` ile değiştirilebilen bir referans yaratır ve burada `change` fonksiyonunu çağırırız ve 
fonksiyon imzasını, `some_string: &mut String` ile değiştirilebilir bir referansı kabul edecek şekilde güncelleriz. 
Bu, `change` fonksiyonunun ödünç aldığı değeri değiştireceğini açıkça ortaya koymaktadır.

Değişken referansların büyük bir kısıtlaması vardır: bir değere değiştirilebilir referansınız varsa, 
o değere başka referansınız olamaz. `s` için iki değişken referans oluşturmaya çalışan bu kod başarısız olur:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-10-multiple-mut-not-allowed/src/main.rs:here}}
```

İşte hata:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-10-multiple-mut-not-allowed/output.txt}}
```

Bu hata, `s`'i bir defada birden fazla değişken olarak ödünç alamayacağımız için bu kodun geçersiz olduğunu söylüyor. 
İlk değişken ödünç alma `r1`'dedir ve `println!`'de kullanılana kadar sürmelidir, ancak bu değişken referansın oluşturulması ve 
kullanımı arasında, `r2`'de `r1` ile aynı verileri ödünç alan başka bir değişken referans oluşturmaya çalıştık.

Aynı anda aynı verilere birden fazla değişken referansı engelleyen kısıtlama, değişkenliğe izin verir, 
ancak çok kontrollü bir şekilde. Bu, yeni Rustseverlerin uğraştığı bir şey çünkü çoğu dil, istediğiniz zaman 
değişkenliğe uğramanıza izin veriyor. Bu kısıtlamaya sahip olmanın yararı, Rust'ın derleme zamanında *veri yarışlarını* önleyebilmesidir. 
Bir veri yarışı, bir yarış durumuna benzer ve şu üç davranış gerçekleştiğinde gerçekleşir:

* İki veya daha fazla işaretçi aynı verilere aynı anda erişir.
* Verilere yazmak için işaretçilerden en az biri kullanılıyor.
* Verilere erişimi senkronize etmek için kullanılan hiçbir mekanizma yoktur.

*Veri yarışları* tanımsız davranışlara neden olur ve bunları çalışma zamanında izlemeye çalışırken 
teşhis edilmesi ve düzeltilmesi zor olabilir; Rust, veri yarışlarıyla dolu kodu derlemeyi reddederek bu sorunu önler!

Her zaman olduğu gibi, yeni bir kapsam oluşturmak için süslü parantezleri kullanabiliriz ve 
birden çok değişken referansa izin verebiliriz:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-11-muts-in-separate-scopes/src/main.rs:here}}
```

Rust, değiştirilebilir ve değişmez referansları birleştirmek için benzer bir kural uygular. Bu kod hata verir:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-12-immutable-and-mutable-not-allowed/src/main.rs:here}}
```

İşte hata:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-12-immutable-and-mutable-not-allowed/output.txt}}
```

Vay be! Aynı değere değişmez bir referansımız *varken* değiştirilebilir bir referansımız *da* olamaz.

Değişmez bir referansın kullanıcıları, değerin aniden altlarında değişmesini beklemezler! 
Bununla birlikte, birden fazla değişmez referansa izin verilir, çünkü yalnızca verileri okuyan hiç kimse, 
başka birinin verileri okumasını etkileme yeteneğine sahip değildir.

Bir referansın kapsamının tanıtıldığı yerden başladığını ve o referansın son kullanıldığı zamana kadar devam ettiğini unutmayın. 
Örneğin, değişmez referansların son kullanımı olan `println!`, değiştirilebilir referans tanıtılmadan önce gerçekleştiği için bu kod derlenecektir:


```rust,edition2021
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-13-reference-scope-ends/src/main.rs:here}}
```

Değişmez `r1` ve `r2` referanslarının kapsamları `println!`'den sonra biter. Bu kapsamlar çakışmaz, bu nedenle bu koda izin verilir. 
Derleyicinin, kapsamın bitiminden önceki bir noktada bir referansın artık kullanılmadığını söyleyebilmesi, *Sözcük Dışı Ömürlükler* (kısaca NLL) 
olarak adlandırılır ve bununla ilgili daha fazla bilgiyi [The Edition Guide][nll]'dan okuyabilirsiniz.

Ödünç alma hataları bazen can sıkıcı olsa da, Rust derleyicisinin olası bir hatayı erkenden (çalışma zamanından ziyade derleme zamanında) 
ve sorunun tam olarak nerede olduğunu size gösterdiğini unutmayın. 
O zaman verilerinizin neden düşündüğünüz gibi olmadığını bulmak zorunda değilsiniz.

### Sarkan Referanslar

İşaretçileri olan dillerde, belleğin bir kısmını boşaltırken, o belleğe bir işaretçiyi koruyarak, 
yanlışlıkla sarkan bir işaretçi (bellekte başka birine verilmiş olabilecek bir konuma başvuran bir işaretçi) oluşturmak kolaydır. 
Buna karşılık Rust'ta derleyici, referansların asla sarkan referanslar olmayacağını garanti eder: 
eğer bazı verilere referansınız varsa, derleyici, veriye yapılan referanstan önce verilerin kapsam dışına çıkmamasını sağlayacaktır.

Rust'ın derleme zamanı hatasıyla bunları nasıl önlediğini görmek için sarkan bir referans oluşturmaya çalışalım:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-14-dangling-reference/src/main.rs}}
```

İşte hata:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-14-dangling-reference/output.txt}}
```

Bu hata mesajı, henüz ele almadığımız bir özelliğe atıfta bulunuyor: ömürler. 
Ömürleri Bölüm 10'da ayrıntılı olarak tartışacağız. 
Ancak, ömürlerle ilgili kısımları göz ardı ederseniz, mesaj bu kodun neden bir sorun olduğunun anahtarını içerir:

```text
this function's return type contains a borrowed value, but there is no value
for it to be borrowed from
```
```text
bu fonksiyonun dönüş türü ödünç alınan bir değer içeriyor, ancak ödünç alınacak bir değer yok
```

*Sarkan* kodumuzun her aşamasında tam olarak neler olduğuna daha yakından bakalım.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-15-dangling-reference-annotated/src/main.rs:here}}
```

`s`, `dangle` içinde oluşturulduğundan, `dangle` kodu bittiğinde, `s` serbest bırakılır. 
Ama ona bir referans döndürmeye çalıştık. Bu, bu referansın geçersiz bir `String`'e işaret edeceği anlamına gelir. 
Bu iyi bir fikir değildir! Rust bunu yapmamıza izin vermez.

Buradaki çözüm, `String`'i doğrudan döndürmektir:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-16-no-dangle/src/main.rs:here}}
```

Bu herhangi bir sorun olmadan çalışır. Sahiplik taşınır ve hiçbir şey serbest bırakılmaz.

### Referans Kuralları

Referanslar hakkında konuştuklarımızı gözden geçirelim:

* Herhangi bir zamanda *ya* bir değişken referansa *ya da* 
  istediğiniz sayıda değişmez referansa sahip olabilirsiniz.

* Referanslar her zaman geçerli olmalıdır.

Bundan sonraki başlığımızda, farklı bir referans türü olan *dilimlere* bakacağız.

[nll]: https://doc.rust-lang.org/edition-guide/rust-2018/ownership-and-lifetimes/non-lexical-lifetimes.html
