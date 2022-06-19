## Modülerliği ve Hata İşlemeyi Geliştirmek için Yeniden Düzenleme

Programımızı iyileştirmek için, programın yapısı ve olası hataları nasıl ele aldığı ile ilgili dört sorunu çözeceğiz. 
İlk olarak, `ma'n` fonksiyonumuz artık iki görevi yerine getiriyor: argümanları ayrıştırıyor ve dosyaları okuyor. 
Programımız büyüdükçe, ana fonksiyonun yerine getirdiği ayrı görevlerin sayısı artacaktır. Bir fonksiyon sorumluluk kazandıkça,
hakkında mantık yürütmek daha zor, test etmek daha zor ve parçalarından birini bozmadan değiştirmek daha zor hale gelir. 
Her fonksiyonun tek bir görevden sorumlu olması için işlevleri ayırmak en iyisidir.

Bu konu aynı zamanda ikinci sorunla da bağlantılıdır: sorgu ve dosya adı programımız için yapılandırma değişkenleri olsa da, 
içerik gibi değişkenler programın mantığını gerçekleştirmek için kullanılır. main ne kadar uzun olursa, 
o kadar çok değişkeni kapsama almamız gerekecektir; ne kadar çok değişkeni kapsama alırsak, her birinin amacını takip etmek o
kadar zor olacaktır. Amaçlarını netleştirmek için yapılandırma değişkenlerini tek bir yapıda gruplamak en iyisidir.

Üçüncü sorun, dosyayı okuma başarısız olduğunda bir hata mesajı yazdırmak için `expect` kullandık, ancak hata mesajı sadece 
`Something went wrong reading the file` yazıyor. Bir dosyayı okumak çeşitli şekillerde başarısız olabilir: örneğin, 
dosya eksik olabilir veya dosyayı açmak için iznimiz olmayabilir. Şu anda, durum ne olursa olsun, her şey için aynı hata mesajını 
yazdırırız ve bu da kullanıcıya hiçbir bilgi vermeyiz.

Dördüncüsü, farklı hataları işlemek için tekrar tekrar `expect` kullanıyoruz ve kullanıcı programımızı yeterli argüman belirtmeden 
çalıştırırsa, Rust'tan sorunu açıkça açıklamayan bir `index out of bounds` hatası alacaktır. Tüm hata işleme kodunun tek bir yerde olması 
en iyisidir, böylece gelecekteki bakımcılar hata işleme mantığının değişmesi gerektiğinde koda başvurmak için tek bir yere sahip olurlar.
Tüm hata işleme kodunun tek bir yerde olması, son kullanıcılarımız için anlamlı olacak mesajları yazdırmamızı da sağlayacaktır.

Projemizi yeniden düzenleyerek bu dört sorunu ele alalım.

### İkili Projeler için Endişelerin Ayrılması

Birden fazla görevin sorumluluğunun ana fonksiyona verilmesine ilişkin organizasyonel sorun, 
birçok ikili projede ortaktır. Sonuç olarak, Rust topluluğu, ana program büyümeye başladığında ikili bir programın ayrı 
endişelerini bölmek için yönergeler geliştirmiştir. Bu süreç aşağıdaki adımlardan oluşur:

* Programınızı *main.rs* ve *lib.rs* olarak ayırın ve programınızın ana mantığını *lib.rs*'e taşıyın.
* Komut satırı ayrıştırma mantığınız küçük olduğu sürece *main.rs* içinde kalabilir.
* Komut satırı ayrıştırma mantığı karmaşıklaşmaya başladığında, *main.rs*'den çıkarın ve *lib.rs*'e taşıyın.

Bu işlemlerden sonra `main` fonksiyonunda kalanlar aşağıdakilerle sınırlı olmalıdır:

* Komut satırı ayrıştırma mantığını argüman değerleriyle çağırmak
* Diğer yapılandırmaları ayarlama
* *lib.rs* içinde bir `run` fonksiyonu çağırma
* `run` bir hata döndürürse hatayı işleme

Bu model endişeleri ayırmakla ilgilidir: *main.rs* programı çalıştırır ve *lib.rs* eldeki görevin tüm mantığını ele alır. 
`main` fonksiyonunu doğrudan test edemeyeceğiniz için, bu yapı programınızın tüm mantığını *lib.rs*'deki fonksiyonlara taşıyarak 
test etmenizi sağlar. *main.rs*'de kalan kod, okunarak doğruluğu teyit edilebilecek kadar küçük olacaktır. 

Bu süreci takip ederek programımızı yeniden düzenleyelim.

#### Argüman Ayrıştırıcısını Çıkarma

Komut satırı ayrıştırma mantığını *src/lib.rs*'ye taşımaya hazırlanmak için argümanları ayrıştırma işlevini 
`main`'in çağıracağı bir fonksiyona çıkaracağız. Liste 12-5, şimdilik *src/main.rs* içinde tanımlayacağımız yeni `parse_config` 
fonksiyonunu çağıran `main`'in yeni başlangıcını göstermektedir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-05/src/main.rs:here}}
```

<span class="caption">Liste 12-5: `parse_config` fonksiyonunun `main`'den çıkarılması</span>

Komut satırı argümanlarını hala bir vektörde topluyoruz, ancak 1. indeksteki argüman değerini sorgu değişkenine ve 
2. indeksteki argüman değerini `main` içindeki dosya adı değişkenine atamak yerine, tüm vektörü `parse_config`'e aktarıyoruz. 
`parse_config` daha sonra hangi argümanın hangi değişkene gideceğini belirleyen mantığı tutar ve değerleri `main`'e geri aktarır. 
`query` ve `filename` değişkenlerini hala `main` içinde oluşturuyoruz, ancak `main` artık komut satırı argümanlarının ve 
değişkenlerin nasıl karşılık geldiğini belirleme sorumluluğuna sahip değil.

Bu yeniden çalışma küçük programımız için aşırı gibi görünebilir, ancak küçük, artan adımlarla yeniden düzenliyoruz. 
Bu değişikliği yaptıktan sonra, argüman ayrıştırmanın hala çalıştığını doğrulamak için programı tekrar çalıştırın. 
İlerlemenizi sık sık kontrol etmek, ortaya çıktıklarında sorunların nedenini belirlemeye yardımcı olmak için iyidir.

#### Yapılandırma Değerlerini Gruplama

`parse_config`'i daha da geliştirmek için küçük bir adım daha atabiliriz. Şu anda bir `tuple` döndürüyoruz, ancak hemen ardından bu 
`tuple`'ı tekrar ayrı parçalara ayırıyoruz. Bu, belki de henüz doğru soyutlamaya sahip olmadığımızın bir işaretidir.

İyileştirme için yer olduğunu gösteren bir başka gösterge de `parse_config`'in `config` kısmıdır, 
bu da döndürdüğümüz iki değerin ilişkili olduğunu ve her ikisinin de bir yapılandırma değerinin parçası olduğunu ima eder. 
Şu anda bu anlamı, iki değeri bir `tuple` olarak gruplamak dışında verinin yapısında aktarmıyoruz; bunun yerine iki değeri 
`struct` içine koyacağız ve `struct` alanlarının her birine anlamlı bir isim vereceğiz. Bunu yapmak, bu kodun gelecekteki 
bakımcılarının farklı değerlerin birbirleriyle nasıl ilişkili olduğunu ve amaçlarının ne olduğunu anlamalarını kolaylaştıracaktır.

Liste 12-6, `parse_config`'de yapılan iyileştirmeleri göstermektedir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,should_panic,noplayground
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-06/src/main.rs:here}}
```

<span class="caption">Liste 12-6: `Config` yapısının örneğini döndürmek için `parse_config` öğesini 
yeniden düzenleme</span>

`query` ve `filename` adında alanlara sahip olacak şekilde tanımlanmış `Config` adında bir yapı ekledik. `parse_config`'in imzası artık 
`Config` değeri döndürdüğünü gösteriyor. Eskiden `args` içindeki `String` değerlerine referans veren dizgi dilimleri döndürdüğümüz 
`parse_config` gövdesinde, artık `Config`'i `String` değerlerini içerecek şekilde tanımlıyoruz. `main` içindeki `args` değişkeni argüman 
değerlerinin sahibidir ve yalnızca `parse_config` fonksiyonunun bunları ödünç almasına izin verir, bu da `Config`'in `args` içindeki 
değerlerin sahipliğini almaya çalışması durumunda Rust'ın ödünç alma kurallarını ihlal edeceğimiz anlamına gelir.

`String` verilerini yönetebileceğimiz birkaç yol vardır; biraz verimsiz olsa da en kolay yol, değerler üzerinde `clone` metodunu çağırmaktır. 
Bu,` Config` örneğinin sahip olması için verilerin tam bir kopyasını oluşturacaktır, bu da dizgi verilerine bir referans depolamaktan daha 
fazla zaman ve bellek gerektirir. Bununla birlikte, verileri klonlamak kodumuzu çok basit hale getirir çünkü referansların yaşam sürelerini
yönetmek zorunda değiliz; bu durumda, basitlik kazanmak için biraz performanstan vazgeçmek değerli bir değiş tokuştur.

> ### `clone` Kullanmanın Getirileri
> 
> Birçok Rustsever arasında, çalışma zamanı maliyeti nedeniyle sahiplik sorunlarını çözmek için 
> `clone` kullanmaktan kaçınma eğilimi vardır. Bölüm 13'te, bu tür durumlarda nasıl daha verimli yöntemler 
> kullanacağınızı öğreneceksiniz. Ancak şimdilik, ilerlemeye devam etmek için birkaç dizgiyi kopyalamanızda bir sakınca yok 
> çünkü bu kopyaları yalnızca bir kez yapacaksınız ve `filename` ve `query` dizginiz çok küçük. İlk geçişinizde kodu aşırı optimize 
> etmeye çalışmaktansa biraz verimsiz çalışan bir programa sahip olmak daha iyidir. Rust ile daha deneyimli hale geldikçe, 
> en verimli çözümle başlamak daha kolay olacaktır, ancak şimdilik `clone` kullanmak tamamen kabul edilebilir.

`main`'i, `parse_config` tarafından döndürülen `Config` örneğini `config` adlı bir değişkene atayacak şekilde güncelledik 
ve daha önce ayrı `query` ve `filename` değişkenlerini kullanan kodu güncelledik, böylece artık bunun yerine `Config` yapısındaki alanları 
kullanıyor.

Artık kodumuz `query` ve `filename`'in ilişkili olduğunu ve amaçlarının programın nasıl çalışacağını yapılandırmak olduğunu daha açık bir 
şekilde ifade ediyor. Bu değerleri kullanan herhangi bir kod, bunları `config` örneğinde amaçlarına göre adlandırılmış alanlarda bulmayı bilir.

#### `Config` için Bir Yapıcı Oluşturma

Şimdiye kadar, komut satırı argümanlarını ayrıştırmaktan sorumlu mantığı `main`'den çıkardık ve `parse_config` fonksiyonuna yerleştirdik. 
Bunu yapmak, `query` ve `filename` değerlerinin ilişkili olduğunu ve bu ilişkinin kodumuzda aktarılması gerektiğini görmemize yardımcı oldu. 
Daha sonra `query` ve `filename`'in amaçlarını adlandırmak ve `parse_config` fonksiyonundan değerlerin adlarını `struct` alan adları olarak 
döndürebilmek için bir `Config` `struct`'ı ekledik.

Artık `parse_config` fonksiyonunun amacı bir `Config` örneği oluşturmak olduğuna göre, `parse_config`'i düz bir fonksiyondan `Config` yapısıyla 
ilişkili `new` adlı bir fonksiyona dönüştürebiliriz. Bu değişikliği yapmak kodu daha deyimsel hale getirecektir. Standart kütüphanedeki `String` 
gibi türlerin örneklerini `String::new` fonksiyonunu çağırarak oluşturabiliriz. Benzer şekilde, `parse_config`'i `Config` ile ilişkili yeni 
bir fonksiyona dönüştürerek, `Config::new`'i çağırarak `Config`'in örneklerini oluşturabileceğiz. Liste 12-7 yapmamız 
gereken değişiklikleri göstermektedir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,should_panic,noplayground
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-07/src/main.rs:here}}
```

<span class="caption">Liste 12-7: `parse_config`'i `Config::new` ile değiştirme</span>

`main`'de `parse_config` çağrısı yaptığımız yeri `Config::new` çağrısı yapacak şekilde güncelledik. 
`parse_config`'in adını `new` olarak değiştirdik ve yeni fonksiyonu `Config` ile ilişkilendiren `impl` bloğunun içine taşıdık. 
Çalıştığından emin olmak için bu kodu tekrar derlemeyi deneyin.

### Hata İşlemeyi Düzeltme

Şimdi hata işlememizi düzeltmeye çalışacağız. `args` vektöründeki değerlere indeks `1` veya indeks `2`'den erişmeye çalışmanın, 
vektör üçten az öğe içeriyorsa programın paniklemesine neden olacağını hatırlayın. Programı herhangi bir argüman olmadan çalıştırmayı 
deneyin; şöyle görünecektir:

```console
{{#include ../listings/ch12-an-io-project/listing-12-07/output.txt}}
```

`index out of bounds: the len is 1 but the index is 1`, programcılara yönelik detaylı bir hata mesajıdır. 
Son kullanıcılarımızın bunun yerine ne yapmaları gerektiğini anlamalarına yardımcı olmaz. Şimdi bunu düzeltelim.

### Hata Mesajını İyileştirme

Liste 12-8'de, yeni fonksiyona 1. ve 2. dizine erişmeden önce dilimin yeterince uzun olduğunu doğrulayacak bir kontrol ekliyoruz. 
Dilim yeterince uzun değilse, program paniğe kapılır ve daha iyi bir hata mesajı görüntüler.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-08/src/main.rs:here}}
```

<span class="caption">Liste 12-8: Bağımsız değişken sayısı için bir kontrol ekleme</span>

Bu kod, [Liste 9-13'te yazdığımız `Guess::new` fonksiyonuna benzer][ch9-custom-types]<!-- ignore -->; burada `value` bağımsız değişkeni 
geçerli değerler aralığının dışında kaldığında `panic!` yapar. Bir değer aralığını kontrol etmek yerine, `args` uzunluğunun en az 
3 olduğunu kontrol ediyoruz ve fonksiyonun geri kalanı bu koşulun sağlandığı varsayımı altında çalışabilir. Eğer `args` üçten az öğeye sahipse, 
bu koşul doğru olur ve programı hemen sonlandırmak için `panic!` makrosunu çağırırız.

Bu ekstra birkaç satırlık yeni kodla, hatanın şimdi nasıl göründüğünü görmek için programı herhangi bir 
argüman olmadan tekrar çalıştıralım:

```console
{{#include ../listings/ch12-an-io-project/listing-12-08/output.txt}}
```

Bu çıktı daha iyi: artık makul bir hata mesajımız var. Ancak, kullanıcılarımıza vermek istemediğimiz gereksiz bilgilere de sahibiz. 
Belki de Liste 9-13'te kullandığımız tekniği burada kullanmak en iyisi değildir: [Bölüm 9'da tartışıldığı gibi][ch9-error-guidelines]<!-- ignore -->, 
`panic!` çağrısı bir kullanım probleminden ziyade bir programlama problemi için daha uygundur. Bunun yerine, 
Bölüm 9'da öğrendiğiniz diğer tekniği kullanacağız— başarı ya da hatayı gösteren [`Result`'u döndürmek][ch9-result]<!-- ignore -->.

#### `panic!` Yerine `new`'den `Result` Döndürme

Bunun yerine, başarılı durumda bir `Config` örneği içeren ve hata durumunda sorunu açıklayan bir `Result` değeri döndürebiliriz. 
`Config::new` `main` ile iletişim kurarken, bir sorun olduğunu belirtmek için `Result` türünü kullanabiliriz. 

Daha sonra `main`'i, `panic!`'in neden olduğu `main` iş parçacığı ve `RUST_BACKTRACE` ile ilgili çevreleyen metin olmadan 
kullanıcılarımız için `Err` varyantını daha pratik bir hataya dönüştürmek için değiştirebiliriz.

Liste 12-9, `Config::new`'in dönüş değerinde ve `Result` döndürmek için gereken fonksiyonun gövdesinde yapmamız gereken 
değişiklikleri göstermektedir. Bunun `main`'i de güncellemeden derlenmeyeceğini unutmayın, ki bunu bir sonraki listede yapacağız.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-09/src/main.rs:here}}
```

<span class="caption">Liste 12-9: `Config::new`'den `Result` Döndürme</span>

Yeni fonksiyonumuz artık başarı durumunda bir `Config` örneği ve hata durumunda `&'static str` içeren bir `Result` döndürmektedir. 
Hata değerlerimiz her zaman `'static` ömüre sahip dizgi değişmezleri olacaktır.

Yeni fonksiyonun gövdesinde iki değişiklik yaptık: kullanıcı yeterli argüman iletmediğinde `panic!` çağrısı yapmak yerine, 
artık bir `Err` değeri döndürüyoruz ve `Config` dönüş değerini bir `Ok` içinde tuttuk. Bu değişiklikler fonksiyonun yeni tür imzasına 
uygun olmasını sağlar.

`Config::new`'den `Err` değeri döndürmek, `main` fonksiyonun yeni fonksiyondan dönen `Result` değerini işlemesini ve hata 
durumunda süreçten daha temiz bir şekilde çıkmasını sağlar.

#### `Config::new` Çağrısı ve Hataların İşlenmesi

Hata durumunu ele almak ve kullanıcı dostu bir mesaj yazdırmak için, Liste 12-10'da gösterildiği gibi, 
`Config::new` tarafından döndürülen `Result`'u ele almak üzere `main`'i güncellememiz gerekir. Ayrıca, komut satırı aracından 
sıfır olmayan bir hata koduyla çıkma sorumluluğunu `panic!`'ten alacağız ve elle sürekleyeceğiz. Sıfır olmayan bir çıkış durumu, 
programımızı çağıran sürece programın bir hata durumuyla çıktığını bildirmek için kullanılan bir kuraldır.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-10/src/main.rs:here}}
```

<span class="caption">Liste 12-10: Yeni bir `Config` oluşturma başarısız olursa bir hata koduyla çıkmak</span>

Bu listelemede, henüz ayrıntılı olarak ele almadığımız bir metod kullandık: standart kütüphane tarafından `Result<T, E>` üzerinde 
tanımlanan `unwrap_or_else`. `unwrap_or_else`'i kullanmak, panik yaratmayan bazı özel hata işleme yöntemleri tanımlamamızı sağlar. 
Eğer `Result` `Ok` değerinde ise, bu metodun davranışı `unwrap`'a benzer: `Ok`'un sarmaladığı iç değeri döndürür. 
Ancak, değer `Err` değeriyse, bu metod, tanımladığımız ve `unwrap_or_else`'ye argüman olarak aktardığımız anonim bir fonksiyon olan 
kapanış ifadesindeki kodu çağırır. Kapanış ifadelerini [Bölüm 13][ch13]<!-- ignore -->'te daha ayrıntılı olarak ele alacağız. 
Şimdilik, `unwrap_or_else`'nin  `Err`'nin iç değerini, yani bu durumda Liste 12-9'da eklediğimiz `"not enough arguments"` statik dizgisini, 
dikey *aktarmalar* arasında görünen `err` argümanı içinde kapanış ifadesini aktaracağını bilmeniz yeterlidir. 
Daha sonra kapanış ifadesi içindeki kod çalıştığında `err` değerini kullanabilir.

Standart kütüphaneden `process`'i kapsam içine almak için yeni bir `use` satırı ekledik. Hata durumunda çalıştırılacak kapanış ifadesi
içindeki kod yalnızca iki satırdır: `err` değerini yazdırırız ve ardından `process::exit`'i çağırırız. `process::exit` fonksiyonu programı 
hemen durduracak ve çıkış durum kodu olarak aktarılan sayıyı döndürecektir. Bu, Liste 12-8'de kullandığımız `panic!`-tabanlı işleme benzer, 
ancak artık tüm ekstra çıktıları almıyoruz. Hadi deneyelim:

```console
{{#include ../listings/ch12-an-io-project/listing-12-10/output.txt}}
```

Harika! Bu çıktı, kullanıcılarımız için çok daha güzel.

### `main`'den Mantığı Çıkarma

Yapılandırma ayrıştırmasını yeniden düzenlemeyi bitirdiğimize göre, şimdi programın mantığına dönelim. 

[“İkili Projeler için İşlerin Ayrılması”](#separation-of-concerns-for-binary-projects)<!-- ignore --> 
bölümünde belirttiğimiz gibi, yapılandırmayı ayarlamak veya hataları ele almakla ilgili olmayan `main`'de şu anda bulunan tüm mantığı 
tutacak `run` adlı bir fonksiyon çıkaracağız. İşimiz bittiğinde, `main` kısa ve öz olacak ve inceleme yoluyla doğrulanması 
kolay olacak ve diğer tüm mantık için testler yazabileceğiz.

Liste 12-11 ayıklanmış çalıştırma fonksiyonunu göstermektedir. Şimdilik sadece fonksiyonu ayıklayarak küçük ve aşamalı bir iyileştirme yapıyoruz.
Fonksiyonu *src/main.rs* içinde tanımlıyoruz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-11/src/main.rs:here}}
```

<span class="caption">Liste 12-11: Program mantığının geri kalanını içeren `run`'ın 
çıkarılması</span>

`run` fonksiyonu artık dosyanın okunmasından başlayarak `main`'den kalan tüm mantığı içerir. 
`run` fonksiyonu `Config` örneğini bir argüman olarak alır.

#### `run` Fonksiyonundan Hata Döndürme

Kalan program mantığının `run` fonksiyonuna ayrılmasıyla, Liste 12-9'da `Config::new` ile yaptığımız gibi hata işlemeyi geliştirebiliriz. 
Bir şeyler ters gittiğinde programın `expect`'i çağırarak paniklemesine izin vermek yerine, `run` fonksiyonu `Result<T, E>` döndürecektir. 
Bu, hataları ele alma mantığını `main`'de kullanıcı dostu bir şekilde daha da birleştirmemizi sağlayacaktır. Liste 12-12, `run`'ın 
imzasında ve gövdesinde yapmamız gereken değişiklikleri göstermektedir.


<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-12/src/main.rs:here}}
```

<span class="caption">Liste 12-12: `Result` döndürmek için `run`'ı değiştirme</span>

Burada üç önemli değişiklik yaptık. İlk olarak, `run` fonksiyonunun dönüş tipini `Result<(), Box<dyn Error>>` olarak değiştirdik. 
Bu fonksiyon daha önce birim tipi olan `()'`i döndürüyordu ve bunu `Ok` durumunda döndürülen değer olarak tutuyoruz.

Hata türü için `Box<dyn Error>` tanım nesnesini kullandık (ve `std::error::Error`'ı en üstte bir `use` deyimiyle kapsama aldık). 
Tanım nesnelerini [Bölüm 17][ch17]<!-- ignore -->'de ele alacağız. Şimdilik, `Box<dyn Error>`'un fonksiyonun `Error` tanımını sürekleyen 
tür döndüreceği anlamına geldiğini bilin, ancak dönüş değerinin hangi tür olacağını belirtmek zorunda değiliz. 
Bu bize farklı hata durumlarında farklı türlerde olabilecek hata değerleri döndürme esnekliği sağlar. 
`dyn` anahtar sözcüğü “dinamikin” kısaltmasıdır.

İkinci olarak, [Bölüm 9][ch9-question-mark]<!-- ignore -->'da bahsettiğimiz gibi `?` operatörü için `expect` çağrısını kaldırdık. 
Bir hatada `panic!` yerine, `?` işleci çağıranın işlemesi için geçerli fonksiyondan hata değerini döndürecektir.

Üçüncü olarak, `run` fonksiyonu artık başarı durumunda bir `Ok` değeri döndürmektedir. İmzada `run` fonksiyonunun başarı tipini 
`()` olarak bildirdik, bu da birim tür değerini `Ok` değerine sarmamız gerektiği anlamına geliyor. Bu `Ok(())` söz dizimi ilk başta biraz 
garip görünebilir, `ancak ()`'yi bu şekilde kullanmak, `run`'ı yalnızca yan etkileri için çağırdığımızı belirtmenin deyimsel yoludur; 
ihtiyacımız olan bir değer döndürmez.

Bu kodu çalıştırdığınızda, derlenecek ancak bir uyarı görüntülenecektir:

```console
{{#include ../listings/ch12-an-io-project/listing-12-12/output.txt}}
```

Rust bize kodumuzun `Result` değerini göz ardı ettiğini ve `Result` değerinin bir hata oluştuğunu gösterebileceğini söyler. 
Ancak bir hata olup olmadığını kontrol etmiyoruz ve derleyici bize muhtemelen burada bazı hata işleme kodlarına sahip olmamız gerektiğini 
hatırlatıyor! Şimdi bu sorunu düzeltelim.

#### `main`'de Çalıştırmadan Dönen Hataları İşleme

Hataları kontrol edeceğiz ve bunları Liste 12-10'da `Config::new` ile kullandığımıza benzer bir teknik kullanarak ele alacağız, 
ancak küçük bir farkla:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch12-an-io-project/no-listing-01-handling-errors-in-main/src/main.rs:here}}
```

`run`'ın bir `Err` değeri döndürüp döndürmediğini kontrol etmek ve döndürürse `process::exit(1)`'i çağırmak için `unwrap_or_else` yerine 
`if let` kullanırız. `run` fonksiyonu, `Config::new`'in `Config` örneğini döndürdüğü gibi `unwrap` etmek istediğimiz bir değer döndürmez. 
`run` başarı durumunda `()` değerini döndürdüğü için, yalnızca bir hatayı tespit etmekle ilgileniyoruz, bu nedenle `unwrap_or_else`'in 
yalnızca `()` değerini döndürmesine gerek yok.

`if let` ve `unwrap_or_else` fonksiyonlarının gövdeleri her iki durumda da aynıdır: hatayı yazdırır ve çıkarız.

### Kodu Kütüphane Kasasına Bölme

`minigrep` projemiz şu ana kadar iyi görünüyor! Şimdi *src/main.rs* dosyasını böleceğiz ve *src/lib.rs* dosyasına bazı kodlar koyacağız. 
Bu şekilde kodu test edebilir ve daha az sorumluluğu olan bir *src/main.rs* dosyasına sahip olabiliriz.

`main` fonksiyonu olmayan tüm kodları *src/main.rs* dosyasından *src/lib.rs* dosyasına taşıyalım:
* `run` fonksiyonu tanımı
* İlişkili `use` deyimleri
* `Config`'in tanımı
* `Config::new` fonksiyon tanımı

*src/lib.rs* dosyasının içeriği Liste 12-13'te gösterilen imzalara sahip olmalıdır (kısa olması için fonksiyonların gövdelerini atladık). 
Liste 12-14'te *src/main.rs*'yi değiştirene kadar bunun derlenmeyeceğini unutmayın.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-13/src/lib.rs:here}}
```

<span class="caption">Liste 12-13: `Config` ve `run`'ı *src/lib.rs*'e taşıma</span>

`pub` anahtar sözcüğünü bolca kullandık: `Config` üzerinde, `Config`'in alanları üzerinde, `new` metodu üzerinde ve `run` fonksiyonu üzerinde. 
Artık test edebileceğimiz herkese açık bir API'ye sahip bir kütüphane kasamız var!

Şimdi *src/lib.rs* dosyasına taşıdığımız kodu, Liste 12-14'te gösterildiği gibi 
*src/main.rs* dosyasındaki ikili kasanın kapsamına almamız gerekiyor.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-14/src/main.rs:here}}
```

<span class="caption">Liste 12-14: *src/main.rs* içinde `minigrep` kütüphanesini kullanma</span>

`Config` türünü kütüphane kasasından ikili kasanın kapsamına getirmek için `use minigrep::Config` satırı ekliyoruz ve 
`run` fonksiyonunun önüne kasa adımızı ekliyoruz. Artık tüm fonksiyonlar birbirine bağlı olmalı ve çalışmalıdır. 
Programı `cargo run` ile çalıştırın ve her şeyin doğru çalıştığından emin olun.

Vay be! Sanki bu **seni** ve *beni* çok yormuş gibi duruyor, ancak gelecekte başarılı olmak için bunları yapmalıydık. 
Artık hataları ele almak çok daha kolay ve kodu daha modüler hale getirdik. Bundan sonraki neredeyse tüm işlerimiz *src/lib.rs* içinde 
yapılacak.

Eski kodla zor olan ancak yeni kodla kolay olan bir şeyi yaparak bu yeni modülerlikten yararlanalım: 
bazı testler yazacağız!

[ch13]: ch13-00-functional-features.html
[ch9-custom-types]: ch09-03-to-panic-or-not-to-panic.html#creating-custom-types-for-validation
[ch9-error-guidelines]: ch09-03-to-panic-or-not-to-panic.html#guidelines-for-error-handling
[ch9-result]: ch09-02-recoverable-errors-with-result.html
[ch17]: ch17-00-oop.html
[ch9-question-mark]: ch09-02-recoverable-errors-with-result.html#a-shortcut-for-propagating-errors-the--operator
