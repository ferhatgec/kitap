## G/Ç Projemizi Geliştirmek

Yineleyiciler hakkındaki bu yeni bilgiyle, koddaki yerleri daha açık ve öz hale getirmek için yineleyicileri kullanarak Bölüm 12'deki 
G/Ç projesini geliştirebiliriz. Şimdi yineleyicilerin `Config::new` fonksiyonu ve `search` fonksiyonu uygulamamızı nasıl 
geliştirebileceğine bakalım.

### Yineleyici Kullanarak bir `clone`'u Kaldırma

Liste 12-6'da, `String` değerlerinin bir dilimini alan ve dilime indeksleme yapıp değerleri klonlayarak `Config` yapısının bir 
örneğini oluşturan ve `Config` yapısının bu değerlere sahip olmasını sağlayan kodu ekledik. Liste 13-17'de, `Config::new` fonksiyonunun 
uygulamasını Liste 12-23'te olduğu gibi yeniden ürettik:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch13-functional-features/listing-12-23-reproduced/src/lib.rs:ch13}}
```

<span class="caption">Liste 13-17: Liste 12-23'ten `Config::new` işlevinin çoğaltılması</span>

O zaman, verimsiz `clone` çağrıları konusunda endişelenmememizi çünkü gelecekte bunları kaldıracağımızı söylemiştik. 
İşte o zaman şimdi!

Burada `clone`'a ihtiyacımız vardı çünkü parametre `args`'ta `String` öğeleri olan bir dilimimiz var, 
ancak yeni fonksiyon `args`'a sahip değil. Bir `Config` örneğinin sahipliğini döndürmek için `Config`'in `query` ve `filename` 
alanlarındaki değerleri klonlamamız gerekiyordu, böylece `Config` örneği kendi değerlerine sahip olabilirdi.

Yineleyiciler hakkındaki yeni bilgilerimizle, yeni fonksiyonu bir dilimi ödünç almak yerine argümanı olarak bir
yineleyicinin sahipliğini alacak şekilde değiştirebiliriz. Dilimin uzunluğunu kontrol eden ve belirli konumlara indeksleyen 
kod yerine yineleyici fonksiyonu kullanacağız. Bu, `Config::new` fonksiyonunun ne yaptığını netleştirecektir çünkü yineleyici 
değerlere erişecektir.

`Config::new` yineleyicinin sahipliğini aldığında ve ödünç alan indeksleme işlemlerini kullanmayı bıraktığında, 
`String` değerlerini `clone`'u çağırmak ve yeni bir tahsisat yapmak yerine yineleyiciden `Config`'e taşıyabiliriz.

#### Döndürülen Yineleyiciyi Doğrudan Kullanma

G/Ç projenizin aşağıdaki gibi görünmesi gereken *src/main.rs* dosyasını açın:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch13-functional-features/listing-12-24-reproduced/src/main.rs:ch13}}
```

Liste 12-24'te sahip olduğumuz `main` fonksiyonun başlangıcını Liste 13-18'deki kodla değiştireceğiz. 
Bu, biz `Config::new`'i de güncelleyene kadar derlenmeyecektir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-18/src/main.rs:here}}
```

<span class="caption">Liste 13-18: `env::args` dönüş değerinin `Config::new` 
öğesine iletilmesi</span>

`env::args` fonksiyonu bir yineleyici döndürür! Yineleyici değerlerini bir vektörde toplamak ve ardından bir 
dilimi `Config::new`'e aktarmak yerine, şimdi `env::args`'dan dönen yineleyicinin sahipliğini doğrudan `Config::new`'e aktarıyoruz.

Daha sonra, `Config::new`'in tanımını güncellememiz gerekiyor. G/Ç projenizin *src/lib.rs* dosyasında, 
`Config::new`'in imzasını Liste 13-19'daki gibi görünecek şekilde değiştirelim. Bu yine de derlenmeyecektir çünkü fonksiyon 
gövdesini güncellememiz gerekir.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-19/src/lib.rs:here}}
```

<span class="caption">Liste 13-19: Bir yineleyici beklemek için `Config::new` 
imzasını güncelleme</span>

`env::args` fonksiyonunun standart kütüphane belgeleri, döndürdüğü yineleyicinin türünün `std::env::Args` olduğunu ve bu türün 
`Iterator` özelliğini uyguladığını ve `String` değerleri döndürdüğünü gösterir.

`Config::new` fonksiyonunun imzasını güncelledik, böylece `args` parametresi `&[String]` yerine tanım sınırları olan 
`impl Iterator<Item = String>` ile yaygın bir türe sahip olacak. Bölüm 10'un [“Parametreler Olarak Tanımlar”][impl-trait]<!-- ignore --> bölümünde
tartıştığımız `impl Trait` söz diziminin bu kullanımı, `args`'nin `Iterator` türünü uygulayan ve `String` öğeleri döndüren herhangi bir tür 
olabileceği anlamına gelir.

`args`'nin sahipliğini aldığımız ve üzerinde yineleme yaparak `args`'yi mutasyona uğratacağımız için, `mut` anahtar sözcüğünü `args` 
parametresinin belirtimine ekleyerek onu mutasyona uğratılabilir hale getirebiliriz.

#### İndeksleme Yerine `Iterator` Tanım Yöntemlerini Kullanma

Sonra, `Config::new`'in gövdesini düzelteceğiz. `args`, `Iterator` tanımını uyguladığı için, bir sonraki 
yöntemi çağırabileceğimizi biliyoruz! Liste 13-20, `next` yöntemini kullanmak için Liste 12-23'teki kodu günceller:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-20/src/lib.rs:here}}
```

<span class="caption">Liste 13-20: Yineleyici yöntemlerini kullanmak için `Config::new` gövdesini değiştirme</span>

`env::args`'ın dönüş değerindeki ilk değerin programın adı olduğunu unutmayın. 
Bunu yok saymak ve bir sonraki değere ulaşmak istiyoruz, bu yüzden önce `next`'i çağırıyoruz ve geri dönüş değeriyle hiçbir şey yapmıyoruz. 
İkinci olarak, `Config'`in `query` alanına koymak istediğimiz değeri almak için `next`'i çağırıyoruz. `next`, `Some` döndürürse, 
değeri çıkarmak için `match` kullanırız. `None` döndürürse, yeterli argüman verilmediği anlamına gelir ve bir `Err` değeriyle erken döneriz. 
Aynı şeyi `filename` değeri için de yaparız.

### Yineleyici Bağdaştırıcılar ile Kodu Daha Anlaşılır Hale Getirme

G/Ç projemizdeki `search` fonksiyonunda da yineleyicilerden yararlanabiliriz; bu fonksiyon burada Liste 12-19'da olduğu gibi 
Liste 13-21'de yeniden üretilmiştir:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-19/src/lib.rs:ch13}}
```

<span class="caption">Liste 13-21: Liste 12-19'dan `search` fonksiyonunun yazılması</span>

Bu kodu yineleyici bağdaştırıcı metodlarını kullanarak daha kısa bir şekilde yazabiliriz. 
Bunu yapmak aynı zamanda değiştirilebilir bir ara `results` vektörüne sahip olmaktan kaçınmamızı sağlar. 
Fonksiyonel programlama stili, kodu daha anlaşılır hale getirmek için değiştirilebilir durum miktarını en aza indirmeyi tercih eder. 
Değişken durumu kaldırmak, `results` vektörüne eşzamanlı erişimi yönetmek zorunda kalmayacağımız için gelecekte yapılacak bir 
geliştirmeyle aramanın paralel olarak yapılmasını sağlayabilir. Liste 13-22 bu değişikliği göstermektedir:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch13-functional-features/listing-13-22/src/lib.rs:here}}
```

<span class="caption">Liste 13-22: "arama" işlevinin uygulanmasında yineleyici bağdaştırıcı 
yöntemlerini kullanma</span>

`search` fonksiyonunun amacının, `query`'i içeren içerikteki tüm satırları döndürmek olduğunu hatırlayın. Liste 13-16'daki 
`filter` örneğine benzer şekilde, bu kod yalnızca `line.contains(query)` öğesinin `true` döndürdüğü satırları tutmak için `filter` 
kullanır. Daha sonra eşleşen satırları `collect` ile başka bir vektörde topluyoruz. Çok daha basit! 
Aynı değişikliği `search_case_insensitive` fonksiyonunda yineleyici yöntemlerini kullanmak için de yapmaktan çekinmeyin.

Bir sonraki mantıksal soru, kendi kodunuzda hangi stili ve neden seçmeniz gerektiğidir: Liste 13-21'deki orijinal uygulama mı yoksa 
Liste 13-22'deki yineleyicileri kullanan sürüm mü? Çoğu Rust programcısı yineleyici stilini kullanmayı tercih eder. 
İlk başta alışmak biraz daha zordur, ancak çeşitli yineleyici uyarlayıcılarını ve ne yaptıklarını bir kez hissettiğinizde, 
yineleyicileri anlamak daha kolay olabilir. Döngünün çeşitli kısımlarıyla uğraşmak ve yeni vektörler oluşturmak yerine, 
kod döngünün üst düzey hedefine odaklanır. Bu, sıradan kodların bazılarını soyutlaştırır, böylece yineleyicideki her bir öğenin 
geçmesi gereken filtreleme koşulu gibi bu koda özgü kavramları görmek daha kolaydır.

Ancak iki uygulama gerçekten eş değer midir? Sezgisel varsayım, daha düşük seviyeli döngünün daha hızlı olacağı yönünde olabilir. 
Şimdi performans hakkında konuşalım.

[impl-trait]: ch10-02-traits.html#traits-as-parameters
