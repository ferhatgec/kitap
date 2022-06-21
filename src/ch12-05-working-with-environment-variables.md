## Ortam Değişkenleriyle Çalışmak

Ekstra bir özellik ekleyerek `minigrep`'i geliştireceğiz: kullanıcının bir ortam değişkeni aracılığıyla açabileceği
büyük/küçük harfe duyarlı olmayan arama seçeneği. Bu özelliği bir komut satırı seçeneği haline getirebilir
ve kullanıcıların her uygulamak istediklerinde girmelerini isteyebilirdik, ancak bunun yerine bir ortam değişkeni yaparak,
kullanıcılarımızın ortam değişkenini bir kez ayarlamalarına ve o terminal oturumunda tüm aramalarının büyük/küçük harfe duyarsız
olmasına izin veriyoruz.

### Büyük/Küçük Harfe Duyarsız `search` Fonksiyonu için Başarısız Testi Yazma

İlk olarak, ortam değişkeni bir değere sahip olduğunda çağrılacak yeni bir `search_case_insensitive` fonksiyonu ekliyoruz.
TDD sürecini takip etmeye devam edeceğiz, bu nedenle ilk adım yine başarısız bir test yazmaktır. Yeni `search_case_insensitive` fonksiyonu
için yeni bir test ekleyeceğiz ve Liste 12-20'de gösterildiği gibi iki test arasındaki farkları netleştirmek için eski testimizin adını
`one_result`'tan `case_sensitive`'e değiştireceğiz.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-20/src/lib.rs:here}}
```

<span class="caption">Liste 12-20: Eklemek üzere olduğumuz 
*büyük/küçük harfe duyarlı olmayan* fonksiyon için yeni bir başarısız test ekleme</span>

Eski testin `contents`'ini de düzenlediğimizi unutmayın. 
Büyük/küçük harfe duyarlı bir şekilde arama yaparken `"duct"` sorgusuyla 
eşleşmemesi gereken büyük D harfini kullanarak `"Duct tape."` metnini içeren yeni bir satır 
ekledik. Eski testi bu şekilde değiştirmek, halihazırda uyguladığımız büyük/küçük harfe 
duyarlı arama işlevini yanlışlıkla bozmamamızı sağlamaya yardımcı olur. Bu test şimdi geçmeli 
ve biz büyük/küçük harfe duyarsız arama üzerinde çalışırken sorunsuzca geçmeye devam 
etmelidir.

Büyük/küçük harfe duyarlı olmayan `search` için yeni test, sorgu olarak
`"rUsT"` kullanır. Eklemek üzere olduğumuz `search_case_insensitive` fonksiyonunda,
`"rUsT"` sorgusu, büyük R ile `"Rust:"` içeren satırla eşleşmeli ve her ikisi de sorgudan 
farklı harflere sahip olsa bile `"Trust me."` satırıyla eşleşmelidir. Bu bizim başarısız 
testimizdir ve henüz `search_case_insensitive` fonksiyonunu tanımlamadığımız için 
derlenemeyecektir. Testin derlenip başarısız olduğunu görmek için Liste 12-16'daki
`search` fonksiyonu için yaptığımıza benzer şekilde, her zaman boş bir vektör döndüren bir 
sürekleme eklemekten çekinmeyin.

### `search_case_insensitive` Fonksiyonunun Süreklenmesi

Liste 12-21'de gösterilen `search_case_insensitive` fonksiyonu, `search` fonksiyonuyla 
neredeyse aynı olacaktır. Tek fark, sorguyu ve her satırı küçük harfle yazacağız, 
böylece girdi argümanlarının büyük/küçük harf durumu ne olursa olsun, 
satırın sorguyu içerip içermediğini kontrol ettiğimizde aynı büyük/küçük harf 
durumunda olacaklar.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-21/src/lib.rs:here}}
```

<span class="caption">Liste 12-21: Sorguyu ve satırı karşılaştırmadan önce küçük harfle yazmak için `search_case_insensitive` 
fonksiyonunu tanımlama</span>

İlk olarak, sorgu dizesini küçük harfle yazarız ve aynı ada sahip gölgeli bir değişkende saklarız. 
Sorgu üzerinde `to_lowercase` çağrısı yapmak gereklidir, böylece kullanıcının sorgusu `"rust"`, `"RUST"`, `"Rust"` 
veya `"rUsT"` olsun, sorguyu `"rust"` olarak ele alacağız ve fonksiyona büyük/küçük harfe duyarsız şekilde
yönlendireceğiz. `to_lowercase` temel Unicode'u işleyecek olsa da, tüm durumlarda %100 doğru çalışmayacaktır. 
Gerçek bir uygulama yazıyor olsaydık, burada biraz daha fazla durumu işlemek isterdik, ancak bu bölüm Unicode değil, 
ortam değişkenleri ile ilgilidir, bu yüzden burada bırakacağız.

Sorgunun artık bir dizgi dilimi yerine bir `String` olduğuna dikkat edin, çünkü `to_lowercase` çağrısı mevcut verilere 
başvurmak yerine yeni veriler oluşturur. Örnek olarak, sorgunun `"rUsT"` olduğunu varsayalım: bu dizgi diliminde 
kullanabileceğimiz küçük harfli bir *u* veya *t* bulunmadığından, `"rust"` içeren yeni bir `String` tahsis etmemiz gerekir. 
Şimdi sorguyu `contains` metoduna argüman olarak ilettiğimizde, `contains`'ın imzası bir dizgi dilimi alacak şekilde 
tanımlandığı için bir ve işareti eklememiz gerekir.

Ardından, tüm karakterleri küçük harfle yazmak için her satıra `to_lowercase` çağrısı ekliyoruz. 
Artık satırı ve sorguyu küçük harfe dönüştürdüğümüze göre, sorgunun büyük/küçük harf durumu ne olursa olsun
`match` yapısını kullanarak bulacağız.

Bakalım bu sürekleme testleri geçebilecek mi?

```console
{{#include ../listings/ch12-an-io-project/listing-12-21/output.txt}}
```

Harika! Geçti. Şimdi, `run` fonksiyonundan yeni `search_case_insensitive` fonksiyonunu çağıralım. 
İlk olarak, büyük/küçük harfe duyarlı ve duyarsız arama arasında geçiş yapmak için `Config` yapısına bir 
yapılandırma seçeneği ekleyeceğiz. Bu alanı eklemek derleyici hatalarına neden olacaktır çünkü bu alanı henüz hiçbir 
yerde tanımlamıyoruz:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-22/src/lib.rs:here}}
```

Bir Boole tutan `ignore_case` alanını ekledik. Daha sonra, `ignore_case` alanının değerini kontrol etmek ve 
bunu Liste 12-22'de gösterildiği gibi `search` fonksiyonunu mu yoksa `search_case_insensitive` fonksiyonunu mu çağıracağımıza 
karar vermek için kullanacak `run` fonksiyonuna ihtiyacımız olacak. Bu hala derlenmeyecektir.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-22/src/lib.rs:there}}
```

<span class="caption">Liste 12-22: `config.ignore_case` içindeki değere bağlı olarak `search` ya da 
`search_case_insensitive` çağrısı</span>

Son olarak, ortam değişkenini kontrol etmemiz gerekiyor. Ortam değişkenleriyle çalışma fonksiyonları standart kütüphanedeki `env` modülündedir, 
bu yüzden bu modülü *src/lib.rs* dosyasının üst kısmına getiriyoruz. 
Ardından, Liste 12-23'te gösterildiği gibi, `IGNORE_CASE` adlı bir ortam değişkeni için herhangi bir değer ayarlanıp 
ayarlanmadığını kontrol etmek için `env` modülündeki `var` fonksiyonunu kullanacağız.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-23/src/lib.rs:here}}
```

<span class="caption">Liste 12-23: IGNORE_CASE` adlı bir ortam değişkeninde herhangi bir değer olup olmadığını kontrol 
etmek</span>

Burada, yeni bir `ignore_case` değişkeni oluşturuyoruz. 
Değeri ayarlamak için `env::var` fonksiyonunu çağırıyoruz ve ona `IGNORE_CASE` ortam değişkeninin adını aktarıyoruz.
`env::var` fonksiyonu, ortam değişkeni herhangi bir değere ayarlanmışsa, ortam değişkeninin değerini içeren başarılı `Ok` 
değişkeni olacak bir `Result` döndürür. Ortam değişkeni ayarlanmamışsa `Err` değişkenini döndürür.

Ortam değişkeninin ayarlanıp ayarlanmadığını kontrol etmek için `Result` üzerinde `is_ok` metodunu kullanıyoruz, 
bu da programın büyük/küçük harfe duyarlı olmayan bir arama yapması gerektiği anlamına geliyor.
`IGNORE_CASE` ortam değişkeni herhangi bir şeye ayarlanmamışsa, `is_ok` yöntemi `false` değerini döndürür ve 
program büyük/küçük harfe duyarlı bir arama gerçekleştirir. Ortam değişkeninin değeriyle henüz ilgilenmiyoruz, 
sadece ayarlı ya da ayarsız olmasıyla ilgileniyoruz, bu yüzden `unwrap`, `expect` ya da `Result`'ta 
gördüğümüz diğer metodlardan birini kullanmak yerine `is_ok`'u kontrol ediyoruz.

`ignore_case` değişkenindeki değeri `Config` örneğine aktarırız, böylece `run` fonksiyonu bu değeri okuyabilir ve 
Liste 12-22'de yaptığımız gibi `search_case_insensitive` veya `search` çağrısı oluşturup oluşturmayacağına karar verebilir.

Hadi deneyelim! İlk olarak, programımızı ortam değişkeni ayarlanmadan; `to` sorgusuyla çalıştıracağız; bu sorgu,
`to` sözcüğünü tümüyle küçük harfli şekilde içeren herhangi bir satırla eşleşmelidir:

```console
{{#include ../listings/ch12-an-io-project/listing-12-23/output.txt}}
```

Görünüşe göre hala çalışıyor! Şimdi, programı `IGNORE_CASE` `1` olarak ayarlanmış şekilde,
aynı sorgu ile çalıştıralım.

```console
$ IGNORE_CASE=1 cargo run to poem.txt
```

PowerShell kullanıyorsanız, ortam değişkenini ayarlamanız ve programı ayrı komutlar olarak 
çalıştırmanız gerekecektir:

```console
PS> $Env:IGNORE_CASE=1; cargo run to poem.txt
```

Bu, kabuk oturumunuzun geri kalanında
`IGNORE_CASE`'in kalıcı olmasını sağlayacaktır. `Remove-Item` `cmdlet`'i ile bu ayar kaldırılabilir:

```console
PS> Remove-Item Env:IGNORE_CASE
```

“to” içeren, büyük harfli olabilecek satırlar görmeliyiz:

<!-- manual-regeneration
cd listings/ch12-an-io-project/listing-12-23
IGNORE_CASE=1 cargo run to poem.txt
can't extract because of the environment variable
-->

```console
Are you nobody, too?
How dreary to be somebody!
To tell your name the livelong day
To an admiring bog!
```

Muhteşem, “To” içeren satırlarımızı da görüyoruz! `minigrep` programımız artık bir ortam değişkeni 
tarafından kontrol edilen büyük/küçük harfe duyarlı olmayan arama yapabiliyor. Artık komut satırı argümanları 
ya da ortam değişkenleri kullanılarak ayarlanan seçenekleri nasıl yöneteceğinizi biliyorsunuz.

Bazı programlar aynı yapılandırma için argümanlara *ve* ortam değişkenlerine izin verir. 
Bu durumlarda, programlar birinin ya da diğerinin öncelikli olduğuna karar verir. 
Kendi başınıza başka bir alıştırma yapmak için, bir komut satırı argümanı veya bir ortam değişkeni aracılığıyla 
büyük/küçük harf duyarlılığını kontrol etmeyi deneyin. Program biri büyük/küçük harfe duyarlı diğeri 
büyük/küçük harfi yok sayacak şekilde ayarlanmış olarak çalıştırılırsa komut satırı argümanının mı yoksa ortam 
değişkeninin mi öncelikli olacağına karar verin.

`std::env` modülü, ortam değişkenleriyle başa çıkmak için daha birçok yararlı özellik içerir:
nelerin mevcut olduğunu görmek için ilgili dokümantasyonuna göz atın.
