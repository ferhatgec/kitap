## `Result` İle Kurtarılabilir Hatalar

Çoğu hata, programın tamamen durdurulmasını gerektirecek kadar ciddi değildir. Bazen, bir fonksiyon başarısız olduğunda, 
kolayca yorumlayabileceğiniz ve yanıt verebileceğiniz bir nedenden dolayı başarısız olur. Örneğin, bir dosyayı açmaya çalışırsanız 
ve dosya mevcut olmadığı için bu işlem başarısız olursa, işlemi sonlandırmak yerine dosyayı oluşturmak isteyebilirsiniz.

Bölüm 2'deki [“`Result` Türü ile Potansiyel Başarısızlığı Ele Alma”][handle_failure]<!-- ignore --> kısmından, 
`Result` `enum`'unun aşağıdaki gibi `Ok` ve `Err` olmak üzere iki değişken olarak tanımlandığını hatırlayın:

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

`T` ve `E` yaygın tür parametreleridir: yaygın türleri Bölüm 10'da daha ayrıntılı olarak tartışacağız. 
Şu anda bilmeniz gereken şey, `T`'nin `Ok` değişkeni içinde bir başarı durumunda döndürülecek değerin türünü temsil ettiği ve `E`'nin Err 
değişkeni içinde bir başarısızlık durumunda döndürülecek hatanın türünü temsil ettiğidir. `Result` bu yaygın tür parametrelerine 
sahip olduğu için, döndürmek istediğimiz başarılı değer ile hata değerinin farklı olabileceği birçok farklı durumda `Result` türünü ve 
üzerinde tanımlı fonksiyonları kullanabiliriz.

Fonksiyon başarısız olabileceği için `Result` değeri döndüren bir fonksiyon çağıralım. Liste 9-3'te bir dosya açmaya çalışıyoruz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-03/src/main.rs}}
```

<span class="caption">Liste 9-3: Dosya açmak</span>

`File::open` öğesinin dönüş türü `Result<T, E>`'dir. Yaygın parametre `T`, `File::open` tarafından başarı değerinin türü olan 
`std::fs::File` ile tanımlanmıştır, bu da bir dosya tanıtıcısıdır. Hata değerinde kullanılan `E`'nin türü `std::io::Error`'dır. 
Bu dönüş türü, `File::open` çağrısının başarılı olabileceği ve okuyabileceğimiz veya yazabileceğimiz bir dosya tanıtıcısı döndürebileceği 
anlamına gelir. Fonksiyon çağrısı başarısız da olabilir: örneğin, dosya mevcut olmayabilir veya dosyaya erişim iznimiz olmayabilir. 
`File::open` fonksiyonunun bize başarılı ya da başarısız olduğunu söyleyecek ve aynı zamanda bize dosya tanıtıcısı ya da hata 
bilgisi verecek bir yolu olmalıdır. Bu bilgi tam olarak `Result` `enum`'unun anlattığı şeydir.

`File::open` fonksiyonunun başarılı olduğu durumda, `greeting_file_result` değişkenindeki değer, 
bir dosya tanıtıcısı içeren `Ok` tanımı olacaktır. Başarısız olduğu durumda, `greeting_file_result` değişkenindeki değer, 
meydana gelen hata türü hakkında daha fazla bilgi içeren `Err` tanımı olacaktır.

`File::open`'ın döndürdüğü değere bağlı olarak farklı eylemler gerçekleştirmek için Liste 9-3'teki koda ekleme yapmamız gerekir. 
Liste 9-4, Bölüm 6'da tartıştığımız temel bir araç olan `match` ifadesini kullanarak `Result`'u ele almanın bir yolunu göstermektedir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,should_panic
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-04/src/main.rs}}
```

<span class="caption">Liste 9-4: Döndürülebilecek `Result` değişkenlerini işlemek için `match` 
ifadesi kullanma</span>

`Option` `enum`'u gibi `Result` `enum`'u ve varyantlarının da `prelude` tarafından kapsama alındığına dikkat edin, 
bu nedenle `match` kollarında `Ok` ve `Err` varyantlarından önce `Result::` belirtmemize gerek yoktur.

`Result`, `Ok` olduğunda, bu kod `Ok` değişkeninden iç dosya değerini döndürür ve daha sonra bu dosya tanıtıcısı 
değerini `greeting_file` değişkenine atar. `match`'ten sonra, dosya tanıtıcısını okuma veya yazma için kullanabiliriz.

`match`'in diğer kolu, `File::open`'dan `Err` değeri aldığımız durumu ele alır. Bu örnekte, `panic!` makrosunu çağırmayı seçtik. 
Geçerli dizinimizde `hello.txt` adında bir dosya yoksa ve bu kodu çalıştırırsak, `panic!` makrosundan aşağıdaki çıktıyı görürüz:

```console
{{#include ../listings/ch09-error-handling/listing-09-04/output.txt}}
```

Her zamanki gibi, bu çıktı bize tam olarak neyin yanlış gittiğini söyler.

### Farklı Hatalarda Eşleştirme

Liste 9-4'teki kod, `File::open`'ın neden başarısız olduğuna bakmadan `panic!` yapacaktır. 
Ancak, farklı başarısızlık nedenleri için farklı eylemler gerçekleştirmek istiyoruz: `File::open` dosya mevcut 
olmadığı için başarısız olduysa, dosyayı oluşturmak ve yeni dosyanın tanıtıcısını döndürmek istiyoruz. 
`File::open` başka bir nedenle başarısız olduysa - örneğin, dosyayı açma iznimiz olmadığı için - kodun yine de Liste 9-4'te 
olduğu gibi `panic!` yapmasını istiyoruz. Bunun için Liste 9-5'te gösterilen bir iç `match` ifadesi ekleriz.

<span class="filename">Dosya adı: src/main.rs</span>

<!-- ignore this test because otherwise it creates hello.txt which causes other
tests to fail lol -->

```rust,ignore
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-05/src/main.rs}}
```

<span class="caption">Liste 9-5: Farklı türdeki hataları farklı şekillerde ele alma</span>

`File::open`'ın `Err` değişkeni içinde döndürdüğü değerin türü, standart kütüphane tarafından sağlanan bir yapı olan 
`io::Error`'dır. Bu `struct`, bir `io::ErrorKind` değeri elde etmek için çağırabileceğimiz bir `kind` metoduna sahiptir. 
`io::ErrorKind` `enum`'u standart kütüphane tarafından sağlanır ve bir `io` işleminden kaynaklanabilecek farklı hata türlerini temsil 
eden varyantlara sahiptir. Kullanmak istediğimiz değişken, açmaya çalıştığımız dosyanın henüz mevcut olmadığını gösteren `ErrorKind::NotFound`'dur. 
Bu yüzden `greeting_file_result` ile eşleşiyoruz, ancak `error.kind()` ile de bir iç `match`'imiz var.

İç `match`'te kontrol etmek istediğimiz koşul, `error.kind()` tarafından döndürülen değerin `ErrorKind` `enum`'unun `NotFound` varyantı 
olup olmadığıdır. Eğer varsa, dosyayı `File::create` ile oluşturmaya çalışırız. Ancak, `File::create` de başarısız olabileceğinden, 
iç `match` ifadesinde ikinci bir kola ihtiyacımız var. Dosya oluşturulamadığında, farklı bir hata mesajı yazdırılır. 
Dış `match`'in ikinci kolu aynı kalır, böylece program eksik dosya hatası dışındaki herhangi bir hatada panik yapar.

> ### `Result<T, E>` ile `match` Kullanmanın Alternatifleri
> 
> Çok fazla `match` var! `match` ifadesi çok kullanışlıdır ancak aynı zamanda çok ilkeldir. 
> Bölüm 13'te, `Result<T, E>` üzerinde tanımlanan birçok metodla birlikte kullanılan kapanış ifadeleri hakkında bilgi 
> edineceksiniz. Bu metodlar, kodunuzdaki `Result<T, E>` değerlerini işlerken `match` kullanmaktan daha özlü olabilir.
> 
> Örneğin, Liste 9-5'te gösterilen aynı mantığı bu kez kapanış ifadelerini ve `unwrap_or_else` metodunu kullanarak 
> yazmanın başka bir yolunu aşağıda bulabilirsiniz:
>
> <!-- CAN'T EXTRACT SEE https://github.com/rust-lang/mdBook/issues/1127 -->
>
> ```rust,ignore
> use std::fs::File;
> use std::io::ErrorKind;
>
> fn main() {
>     let greeting_file = File::open("hello.txt").unwrap_or_else(|error| {
>         if error.kind() == ErrorKind::NotFound {
>             File::create("hello.txt").unwrap_or_else(|error| {
>                 panic!("Problem creating the file: {:?}", error);
>             })
>         } else {
>             panic!("Problem opening the file: {:?}", error);
>         }
>     });
> }
> ```
> 
> Bu kod Liste 9-5 ile aynı davranışa sahip olsa da, herhangi bir `match` ifadesi içermez ve okunması daha temizdir. 
> Bölüm 13'ü okuduktan sonra bu örneğe geri dönün ve standart kütüphane dokümantasyonundan `unwrap_or_else` metoduna bakın. 
> Bu metodlardan çok daha fazlası, hatalarla uğraşırken iç içe geçmiş büyük `match` ifadelerini temizleyebilir.

### Panik Hatası Kısayolları: `unwrap` ve `expect`

`match` kullanmak yeterince işe yarar, ancak biraz ayrıntılı olabilir ve amacı her zaman iyi iletmez. 
`Result<T, E>` türü, çeşitli ve daha spesifik görevleri yerine getirmek için üzerinde tanımlanmış birçok yardımcı metoda sahiptir. 
`unwrap` metodu, tıpkı Liste 9-4'te yazdığımız `match` ifadesi gibi uygulanan bir kısayol yöntemidir. 
`Result` değeri `Ok` varyantı ise unwrap, `Ok` içindeki değeri döndürür. `Result` değeri `Err` değişkeniyse, 
`unwrap` bizim için `panic!` makrosunu çağırır. İşte `unwrap`'in iş başında olduğu bir örnek:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,should_panic
{{#rustdoc_include ../listings/ch09-error-handling/no-listing-04-unwrap/src/main.rs}}
```

Bu kodu *hello.txt* dosyası olmadan çalıştırırsak, `unwrap` yönteminin yaptığı `panic!` çağrısından
bir hata mesajı görürüz:

<!-- manual-regeneration
cd listings/ch09-error-handling/no-listing-04-unwrap
cargo run
copy and paste relevant text
-->

```text
thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: Error {
repr: Os { code: 2, message: "No such file or directory" } }',
src/libcore/result.rs:906:4
```


Benzer şekilde, `expect` metodu da `panic!` hata mesajını seçmemizi sağlar. `unwrap` yerine `expect` kullanmak ve 
iyi hata mesajları sağlamak, amacınızı iletebilir ve paniğin kaynağını bulmayı kolaylaştırabilir. 
`expect`'in söz dizimi şu şekildedir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,should_panic
{{#rustdoc_include ../listings/ch09-error-handling/no-listing-05-expect/src/main.rs}}
```

`expect`'i `unwrap` ile aynı şekilde kullanırız: dosya tanıtıcısını döndürmek veya `panic!` makrosunu çağırmak için. 
`expect` tarafından `panic!` çağrısında kullanılan hata mesajı, `unwrap`'ın kullandığı varsayılan `panic!` mesajı yerine 
`expect`'e aktardığımız parametre olacaktır. İşte böyle görünüyor:

<!-- manual-regeneration
cd listings/ch09-error-handling/no-listing-05-expect
cargo run
copy and paste relevant text
-->

```text
thread 'main' panicked at 'hello.txt should be included in this project: Error
{ repr: Os { code: 2, message: "No such file or directory" } }',
src/libcore/result.rs:906:4
```

Üretim kalitesindeki kodlarda, çoğu Rustsever `unwrap` yerine `expect`'i seçer ve işlemin neden her zaman 
başarılı olmasının beklendiği hakkında daha fazla bağlam verir. Bu şekilde, varsayımlarınızın yanlış olduğu kanıtlanırsa, 
hata ayıklamada kullanabileceğiniz daha fazla bilgiye sahip olursunuz.

### Hataların Yayılması

Bir fonksiyonun süreklemesi başarısız olabilecek bir şeyi çağırdığında, hatayı fonksiyonun kendi içinde ele almak yerine, 
hatayı çağıran koda döndürebilirsiniz, böylece işlev ne yapacağına karar verebilir. 
Bu, hatanın yayılması olarak bilinir ve hatanın nasıl ele alınması gerektiğini belirleyen daha fazla bilgi veya 
mantığın kodunuzun bağlamında mevcut olandan daha fazla olabileceği çağıran koda daha fazla kontrol sağlar.

Örneğin, Liste 9-6'da bir dosyadan kullanıcı adı okuyan bir fonksiyon gösterilmektedir. 
Dosya mevcut değilse veya okunamıyorsa, bu fonksiyon; hataları, fonksiyonu çağıran koda döndürür.

<span class="filename">Dosya adı: src/main.rs</span>

<!-- Deliberately not using rustdoc_include here; the `main` function in the
file panics. We do want to include it for reader experimentation purposes, but
don't want to include it for rustdoc testing purposes. -->

```rust
{{#include ../listings/ch09-error-handling/listing-09-06/src/main.rs:here}}
```

<span class="caption">Liste 9-6: `match` kullanarak arama koduna hata 
döndüren bir fonksiyon</span>

Bu fonksiyon çok daha kısa bir şekilde yazılabilir, ancak hata işlemeyi keşfetmek için çoğunu manuel olarak yaparak başlayacağız; 
sonunda daha kısa yolu göstereceğiz. Önce fonksiyonun dönüş tipine bakalım: `Result<String, io::Error>`. 
Bu, fonksiyonun `Result<T, E>` türünde bir değer döndürdüğü anlamına gelir; burada yaygın parametre `T`, somut `String` türüyle 
ve yaygın tür `E`, somut `io::Error` tipiyle doldurulmuştur.

Bu fonksiyon sorunsuz bir şekilde başarılı olursa, bu fonksiyonu çağıran kod, bu fonksiyonun dosyadan okuduğu kullanıcı 
adı olan bir `String` içeren bir `Ok` değeri alacaktır. Bu fonksiyon herhangi bir sorunla karşılaşırsa, çağıran kod, 
sorunların ne olduğu hakkında daha fazla bilgi içeren `io::Error` tanımını tutan bir `Err` değeri alır. 
Bu fonksiyonun geri dönüş türü olarak `io::Error`'ı seçtik çünkü bu fonksiyonun gövdesinde çağırdığımız ve başarısız olabilecek 
her iki işlemden (`File::open` fonksiyonu ve `read_to_string` metodu) dönen hata değerinin türü budur.

Fonksiyonun gövdesi `File::open` fonksiyonunu çağırarak başlar. Ardından `Result` değerini Liste 9-4'teki `match`'e benzer 
bir `match` ile ele alıyoruz. `File::open` başarılı olursa, `file` kalıp değişkenindeki dosya tanıtıcısı `username_file` değişkenindeki 
değer olur ve fonksiyon devam eder. `Err` durumunda, `panic!` çağrısı yapmak yerine, `return` anahtar sözcüğünü kullanarak 
fonksiyondan erken döneriz ve `File::open`'dan gelen hata değerini, şimdi `e` kalıp değişkeninde, bu fonksiyonun hata değeri olarak
çağıran koda geri aktarırız.

Dolayısıyla, `username_file` içinde bir dosya tanıtıcımız varsa, fonksiyon `username` değişkeninde yeni bir `String` oluşturur ve 
dosyanın içeriğini `username` içine okumak için `username_file` içindeki dosya tanıtıcısında `read_to_string` metodunu çağırır. 
`read_to_string` metodu da `Result` döndürür, çünkü `File::open` başarılı olsa bile başarısız olabilir. 
Bu nedenle, `Result`'u işlemek için başka bir `match`'e ihtiyacımız var: `read_to_string` başarılı olursa, fonksiyonumuz başarılı olmuştur ve 
şimdi bir `Ok`'a sarılmış `username`'de bulunan dosyadan kullanıcı adını döndürürüz. `read_to_string` başarısız olursa, 
`File::open`'ın dönüş değerini işleyen `match`'te hata değerini döndürdüğümüz şekilde hata değerini döndürürüz. Ancak, 
bu fonksiyondaki son ifade olduğu için açıkça `return` dememize gerek yoktur.

Bu kodu çağıran kod daha sonra kullanıcı adı içeren bir `Ok` değeri ya da `io::Error` içeren bir `Err` değeri almayı işleyecektir. 
Bu değerlerle ne yapılacağına çağıran kod karar verir. Çağıran kod bir `Err` değeri alırsa, `panic!` çağrısı yapabilir ve programı 
çökertebilir, varsayılan bir kullanıcı adı kullanabilir veya örneğin kullanıcı adını bir dosyadan başka bir yerden arayabilir. 
Çağıran kodun gerçekte ne yapmaya çalıştığı hakkında yeterli bilgiye sahip değiliz, bu nedenle uygun şekilde işlemesi için 
tüm başarı veya hata bilgilerini yukarı doğru yayıyoruz.

Bu hata yayma eğilimi Rust'ta o kadar yaygındır ki, Rust bunu kolaylaştırmak için `?` soru işareti operatörünü sağlar.

#### Hataları Yaymak İçin Bir Kısayol: `?` Operatörü

Liste 9-7, Liste 9-6'dakiyle aynı fonksiyona sahip bir `read_username_from_file` süreklemesini göstermektedir, 
ancak bu sürekleme `?` işlecini kullanmaktadır.

<span class="filename">Dosya adı: src/main.rs</span>

<!-- Deliberately not using rustdoc_include here; the `main` function in the
file panics. We do want to include it for reader experimentation purposes, but
don't want to include it for rustdoc testing purposes. -->

```rust
{{#include ../listings/ch09-error-handling/listing-09-07/src/main.rs:here}}
```

<span class="caption">Liste 9-7: `?` operatörünü kullanarak arama koduna hata döndüren bir
fonksiyon</span>

Bir `Result` değerinden sonra yerleştirilen `?` işareti, Liste 9-6'da `Result` değerlerini işlemek için tanımladığımız 
`match` ifadeleriyle hemen hemen aynı şekilde çalışacak şekilde tanımlanmıştır. `Result`'un değeri bir `Ok` ise, `Ok`'un içindeki 
değer bu ifadeden döndürülür ve program devam eder. Değer `Err` ise, `return` anahtar sözcüğünü kullanmışız gibi tüm fonksiyondan `Err` 
döndürülür, böylece hata değeri çağıran koda yayılır.

Liste 9-6'daki `match` ifadesinin yaptığı ile `?` operatörünün yaptığı arasında bir fark vardır: `?` operatörünün çağrıldığı 
hata değerleri, standart kütüphanedeki `From` tanımında tanımlanan ve değerleri bir türden diğerine dönüştürmek için kullanılan `from` 
fonksiyonundan geçer. `?` işleci `from` fonksiyonunu çağırdığında, alınan hata türü geçerli fonksiyonun dönüş türünde tanımlanan 
hata türüne dönüştürülür. Bu, bir fonksiyonun birçok farklı nedenden dolayı başarısız olsa bile, bir fonksiyonun başarısız 
olabileceği tüm yolları temsil etmek için tek bir hata türü döndürdüğünde kullanışlıdır.

Örneğin, Liste 9-7'deki `read_username_from_file` fonksiyonunu `OurError` adında tanımladığımız özel bir hata türünü döndürecek şekilde
değiştirebiliriz. Bir `io::Error`'dan bir `OurError` tanımı oluşturmak için `impl From<io::Error> for OurError` olarak tanımlarsak,
`read_username_from_file`'ın gövdesindeki `?` operatör çağrıları, fonksiyona daha fazla kod eklemeye gerek kalmadan `from`'u çağıracak ve 
hata türlerini dönüştürecektir.

Liste 9-7 bağlamında, `File::open` çağrısının sonundaki `?`, `Ok` içindeki değeri `username_file` değişkenine döndürecektir. 
Bir hata oluşursa, `?` işleci tüm fonksiyonlardan erken dönecek ve çağıran koda herhangi bir `Err` değeri verecektir. 
Aynı şey `read_to_string` çağrısının sonundaki `?` için de geçerlidir.

`?` işleci, çok sayıda kopyala-yapıştır kodu ortadan kaldırır ve bu fonksiyonun süreklenmesini de daha basit hale getirir. 
Hatta Liste 9-8'de gösterildiği gibi, `?`'den hemen sonra metod çağrılarını zincirleyerek bu kodu daha da kısaltabiliriz.

<span class="filename">Dosya adı: src/main.rs</span>

<!-- Deliberately not using rustdoc_include here; the `main` function in the
file panics. We do want to include it for reader experimentation purposes, but
don't want to include it for rustdoc testing purposes. -->

```rust
{{#include ../listings/ch09-error-handling/listing-09-08/src/main.rs:here}}
```

<span class="caption">Liste 9-8: `?` operatöründen sonra zincirleme yöntemi çağrıları</span>

`username`'de yeni `String`'in oluşturulmasını fonksiyonun başına taşıdık; sıkıntısız çalışacaktır. 
Bir `username_file` değişkeni oluşturmak yerine `read_to_string` çağrısını doğrudan `File::open("hello.txt")?` sonucunun üzerine zincirledik. 
Hala bir `?` `read_to_string` çağrısının sonunda ve hata döndürmek yerine hem `File::open` hem de `read_to_string` başarılı olduğunda 
`username`'i içeren `Ok` değerini döndürürüz. İşlevsellik yine Liste 9-6 ve Liste 9-7'deki ile aynıdır; bu sadece yazmanın farklı, 
daha ergonomik bir yolu.

Liste 9-9, `fs::read_to_string` kullanarak bunu daha da kısaltmanın bir yolunu gösterir.

<span class="filename">Dosya adı: src/main.rs</span>

<!-- Deliberately not using rustdoc_include here; the `main` function in the
file panics. We do want to include it for reader experimentation purposes, but
don't want to include it for rustdoc testing purposes. -->

```rust
{{#include ../listings/ch09-error-handling/listing-09-09/src/main.rs:here}}
```

<span class="caption">Liste 9-9: Dosyayı açıp okumak yerine `fs::read_to_string` kullanma</span>

Bir dosyayı bir `String`'e okumak oldukça yaygın bir işlemdir, bu nedenle standart kütüphane dosyayı açan, 
yeni bir `String` oluşturan, dosyanın içeriğini okuyan, içeriği bu `String`'e atayan ve geri döndüren kullanışlı `fs::read_to_string` 
fonksiyonunu sağlar. Elbette, `fs::read_to_string` kullanmak bize tüm hata işlemlerini açıklama fırsatı vermez, bu yüzden önce uzun yoldan yaptık.

#### `?` Operatörünün Kullanılabileceği Yerler

`?` işleci yalnızca dönüş türü `?` işlecinin kullanıldığı değerle uyumlu olan fonksiyonlarda kullanılabilir. 
Bunun nedeni, `?` işlecinin, Liste 9-6'da tanımladığımız `match` ifadesiyle aynı şekilde, bir değerin fonksiyondan erken dönüşünü 
gerçekleştirmek üzere tanımlanmış olmasıdır. Liste 9-6'da, `match` `Result` değeri kullanıyordu ve erken dönüş kolu `Err(e)` 
değeri döndürüyordu. Bu dönüşle uyumlu olması için fonksiyonun dönüş tipi bir `Result` olmalıdır.

Liste 9-10'da, `?` operatörünü `?` kullandığımız değerin türüyle uyumsuz bir geri dönüş türüne sahip `main` 
fonksiyonunda kullanırsak alacağımız hataya bakalım:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-10/src/main.rs}}
```

<span class="caption">Liste 9-10: `()` döndüren `main` fonksiyonunda `?` kullanmaya çalışırsak derlenmeyecektir</span>

Bu kod, başarısız olabilecek bir dosya açar. `?` işleci `File::open` tarafından döndürülen `Result` değerini takip eder, 
ancak bu `main` fonksiyonunun dönüş türü `Result` değil `()`'dir. Bu kodu derlediğimizde aşağıdaki hata mesajını alırız:

```console
{{#include ../listings/ch09-error-handling/listing-09-10/output.txt}}
```

Bu hata, `?` operatörünü yalnızca `Result`, `Option` veya `FromResidual`'ı sürekleyen başka bir tür döndüren bir 
fonksiyonda kullanabileceğimize işaret eder.

Hatayı düzeltmek için iki seçeneğiniz vardır. Seçeneklerden biri, bunu engelleyen herhangi bir kısıtlama olmadığı sürece fonksiyonunuzun dönüş 
türünü `?` operatörünü kullandığınız değerle uyumlu olacak şekilde değiştirmektir. Diğer teknik ise, `Result<T, E>`'yi uygun olan şekilde 
işlemek için `match` veya `Result<T, E>` yöntemlerinden birini kullanmaktır.

Hata mesajında ayrıca `?` operatörünün `Option<T>` değerleriyle de kullanılabileceği belirtilmiştir. `Result` üzerinde `?` kullanımında olduğu 
gibi, `Option` üzerinde `?` kullanımını da yalnızca `Option` döndüren bir fonksiyonda kullanabilirsiniz. 
Bir `Option<T>` üzerinde çağrıldığında `?` operatörünün davranışı, bir `Result<T, E>` üzerinde çağrıldığında gösterdiği davranışa benzer: 
değer `None` ise, `None` o noktada fonksiyondan erken döndürülür. Değer `Some` ise, `Some` içindeki değer ifadenin sonuç değeridir ve 
fonksiyon devam eder. Liste 9-11'de, verilen metindeki ilk satırın son karakterini bulan bir fonksiyon örneği vardır:

```rust
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-11/src/main.rs:here}}
```

<span class="caption">Liste 9-11: Bir `Option<T>` değerinde `?` 
operatörünü kullanma</span>

Bu fonksiyon `Option<char>` döndürür, çünkü orada bir karakter olması mümkündür, ancak olmaması da mümkündür. 
Bu kod, metin dizgisi dilim argümanını alır ve dizedeki satırlar üzerinde bir yineleyici döndüren `lines` metodunu çağırır. 
Bu fonksiyon ilk satırı incelemek istediğinden, yineleyiciden ilk değeri almak için yineleyicide `next` öğesini çağırır. 
Eğer `text` boş dizgiyse, `next`'e yapılan bu çağrı `None` değerini döndürür, bu durumda durdurmak için `?` kullanırız ve 
`last_char_of_first_line`'dan `None` değerini döndürürüz. `text` boş değilse, `next` çağrısı metindeki ilk satırın dizgi dilimini içeren 
`Some` değerini döndürür.

`?` dizgi dilimini çıkarır ve karakterlerinin bir yineleyicisini almak için bu dizgi dilimi üzerinde `chars`'ı çağırabiliriz. 
Bu ilk satırdaki son karakterle ilgilendiğimizden, yineleyicideki son öğeyi döndürmek için `last` öğesini çağırırız. 
Bu bir `Option`'dur çünkü ilk satırın boş bir dizgi olması mümkündür, örneğin metin boş bir satırla başlıyorsa ancak `"\nhi"` gibi diğer 
satırlarda karakterler varsa. Ancak, ilk satırda bir son karakter varsa, `Some` değişkeninde döndürülür. Ortadaki `?` operatörü bize 
bu mantığı ifade etmek için kısa bir yol sunar ve fonksiyonu tek bir satırda yazmamıza olanak tanır. 
`Option` üzerinde `?` operatörünü kullanamasaydık, bu mantığı daha fazla metod çağrısı veya bir `match` ifadesi kullanarak uygulamamız gerekirdi.
Kısaca Rust bu ameleliği sizin üzerinizden alır.

`Result` döndüren bir fonksiyonda bir `Result` üzerinde `?` operatörünü kullanabileceğinizi ve `Option` döndüren bir 
fonksiyonda bir `Option` üzerinde `?` operatörünü kullanabileceğinizi, ancak karıştırıp eşleştiremeyeceğinizi unutmayın. 
`?` işleci bir `Result`'u otomatik olarak `Option`'a dönüştürmez veya tam tersini yapmaz; bu gibi durumlarda, dönüştürmeyi 
açıkça yapmak için `Result` üzerinde `Ok` metodu veya `Option` üzerinde `ok_or` metodu gibi metodları kullanabilirsiniz.

Şimdiye kadar kullandığımız tüm `main` fonksiyonlar `()` döndürüyordu. `main` fonksiyonu özeldir, çünkü çalıştırılabilir 
programların giriş ve çıkış noktasıdır ve programların beklendiği gibi davranması için dönüş türünün ne olabileceği 
konusunda kısıtlamalar vardır.

Neyse ki `main` aynı zamanda bir `Result<(), E>` de döndürebilir. Liste 9-12, Liste 9-10'daki kodu içerir, 
ancak `main`'in dönüş türünü `Result<(), Box<dyn Error>>` olarak değiştirdik ve sonuna bir dönüş değeri 
`Ok(())` ekledik. Bu kod şimdi derlenecektir:

```rust,ignore
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-12/src/main.rs}}
```

<span class="caption">Liste 9-12: `main`'i `Result<(), E>` döndürecek şekilde değiştirmek, `Result` değerlerinde 
`?` operatörünün kullanılmasına izin verir</span>

`Box<dyn Error>` türü bir *tanım nesnesidir* ve 17. Bölümdeki 
[“Farklı Türlerde Değerlere İzin Veren Tanım Nesnelerini Kullanma”][trait-objects]<!-- ignore --> kısmında bundan bahsedeceğiz. 
Şimdilik, `Box<dyn Error>` türünü “hatanın her türlüsü” olarak okuyabilirsiniz. Hata türü `Box<dyn Error>` olan bir `main`'de, 
`Result` değeri üzerinde `?` kullanılmasına izin verilir, çünkü herhangi bir `Err` değerinin erken döndürülmesine izin verir. 
Bu `main`'in gövdesi yalnızca `std::io::Error` türünde hatalar döndürecek olsa da, `Box<dyn Error>` belirtilerek, 
`main` gövdesine başka hatalar döndüren başka kodlar eklense bile bu imza doğru olmaya devam edecektir.

`main`, `Result<(), E>` döndürdüğünde, `main` `Ok(())` döndürürse yürütülebilir dosya `0` değeriyle çıkar ve `main` `Err` değeri döndürürse 
*sıfır olmayan* bir değerle çıkar. C'de yazılmış çalıştırılabilir dosyalar çıktıklarında tam sayı döndürür: 
başarıyla çıkan programlar `0` döndürür ve hata veren programlar 0 dışında bir tam sayı döndürür. 
Rust da bu kuralla uyumlu olmak için çalıştırılabilir dosyadan tam sayı döndürür.

`main` fonksiyonu, `ExitCode` döndüren bir fonksiyon raporu içeren `std::process::Termination` tanımını sürekleyen herhangi bir türü 
döndürebilir. Kendi türleriniz için `Termination` tanımını sürekleme hakkında daha fazla bilgi için standart kütüphane 
dokümantasyonuna bakın.

`panic!` çağrısı yapmanın veya `Result` döndürmenin ayrıntılarını tartıştığımıza göre, 
hangi durumlarda hangisinin kullanılmasının uygun olacağına nasıl karar verileceği konusuna geri dönelim.

[handle_failure]: ch02-00-guessing-game-tutorial.html#handling-potential-failure-with-the-result-type
[trait-objects]: ch17-02-trait-objects.html#using-trait-objects-that-allow-for-values-of-different-types
[termination]: ../std/process/trait.Termination.html
