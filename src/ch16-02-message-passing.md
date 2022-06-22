## İş Parçacıkları Arasında Veri Aktarmak için Mesaj Geçişini Kullanma

Güvenli eşzamanlılık sağlamak için giderek daha popüler hale gelen bir yaklaşım, 
iş parçacıklarının veya aktörlerin birbirlerine veri içeren mesajlar göndererek iletişim kurduğu mesaj geçişidir. 
İşte [Go dil dokümantasyonundan](https://golang.org/doc/effective_go.html#concurrency) bir slogan:
“Belleği paylaşarak iletişim kurmayın; bunun yerine iletişim kurarak belleği paylaşın.”

Mesaj gönderme eşzamanlılığını gerçekleştirmek için Rust'ın standart kütüphanesi bir *kanal* süreklemesi sağlar. 
Kanal, verilerin bir iş parçacığından diğerine gönderildiği genel bir programlama kavramıdır.

Programlamada bir kanalı, bir dere veya nehir gibi yönlü bir su kanalı gibi düşünebilirsiniz. 
Bir nehre lastik ördek gibi bir şey koyarsanız, su yolunun sonuna kadar aşağı doğru hareket edecektir.

Bir kanalın iki yarısı vardır: bir verici ve bir alıcı. Verici yarı, nehre lastik ördek koyduğunuz yukarı akış konumudur 
ve alıcı yarı, lastik ördeğin aşağı akışta sona erdiği yerdir. Kodunuzun bir kısmı göndermek istediğiniz verilerle 
vericideki yöntemleri çağırır ve başka bir kısmı da gelen mesajlar için alıcı ucunu kontrol eder.
Verici veya alıcı yarısından herhangi biri düşerse bir kanalın kapalı olduğu söylenir.

Burada, değerler üreten ve bunları bir kanaldan gönderen bir iş parçacığına ve değerleri alıp yazdıracak başka 
bir iş parçacığına sahip bir program üzerinde çalışacağız. Özelliği göstermek için bir kanal kullanarak iş parçacıkları 
arasında basit değerler göndereceğiz. Tekniğe aşina olduktan sonra, sohbet sistemi veya birçok iş parçacığının bir 
hesaplamanın parçalarını gerçekleştirdiği ve parçaları sonuçları toplayan bir iş parçacığına gönderdiği bir sistem gibi 
birbirleri arasında iletişim kurması gereken herhangi bir iş parçacığı için kanalları kullanabilirsiniz.

İlk olarak, Liste 16-6'da bir kanal oluşturacağız ancak onunla hiçbir şey yapmayacağız. 
Bunun henüz derlenmeyeceğini unutmayın çünkü Rust kanal üzerinden ne tür değerler göndermek istediğimizi söyleyemez.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch16-fearless-concurrency/listing-16-06/src/main.rs}}
```

<span class="caption">Liste 16-6: Bir kanal oluşturma ve iki yarıyı `tx` ve `rx` olarak atama</span>

`mpsc::channel` fonksiyonunu kullanarak yeni bir kanal oluşturuyoruz; `mpsc` *çoklu üretici, 
tekli tüketici* anlamına geliyor. Kısacası, Rust'ın standart kütüphanesinin kanalları sürekleme şekli, bir 
kanalın değer üreten birden fazla gönderen uca sahip olabileceği, ancak bu değerleri tüketen yalnızca bir 
alıcı uca sahip olabileceği anlamına gelir. Birden fazla akarsuyun birlikte büyük bir nehre aktığını düşünün: 
akarsulardan herhangi birine gönderilen her şey sonunda tek bir nehirde son bulacaktır. Şimdilik tek bir üretici ile 
başlayacağız, ancak bu örneği çalıştırdığımızda birden fazla üretici ekleyeceğiz.

`mpsc::channel` fonksiyonu bir `tuple` döndürür, ilk elemanı gönderen uç-verici- ve ikinci elemanı alan uç-alıcıdır.
`tx` ve `rx` kısaltmaları geleneksel olarak birçok alanda sırasıyla verici ve alıcı için kullanılır, 
bu nedenle değişkenlerimizi her bir ucu belirtmek için bu şekilde adlandırıyoruz. `tuple`'ları yıkıma uğratan bir kalıp 
ile `let` ifade yapısını kullanıyoruz; `let` ifade yapılarında kalıp kullanımı ve yıkım konusunu Bölüm 18'de tartışacağız. 
Şimdilik, let deyimini bu şekilde kullanmanın `mpsc::channel` tarafından döndürülen `tuple` parçalarını ayıklamak için uygun 
bir yaklaşım olduğunu bilin.

İletim ucunu oluşturulmuş bir iş parçacığına taşıyalım ve bir dize göndermesini sağlayalım, böylece oluşturulmuş iş 
parçacığı Liste 16-7'de gösterildiği gibi ana iş parçacığı ile iletişim kurar. Bu, nehrin yukarısına bir lastik ördek 
koymak veya bir iş parçacığından diğerine bir sohbet mesajı göndermek gibidir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch16-fearless-concurrency/listing-16-07/src/main.rs}}
```

<span class="caption">Liste 16-7: `tx`i doğmuş bir iş parçacığına taşıma ve “hello” gönderme</span>

Yine, yeni bir iş parçacığı oluşturmak için `thread::spawn` kullanıyoruz ve ardından `tx`'i kapanışa taşımak için
`move` kullanıyoruz, böylece oluşturulan iş parçacığı `tx`'e sahip oluyor. Ortaya çıkan iş parçacığının kanal üzerinden 
mesaj gönderebilmesi için vericiye sahip olması gerekir. Verici, göndermek istediğimiz değeri alan bir `send` metoda 
sahiptir. `send` metodu `Result<T, E>` türü döndürür, bu nedenle alıcı zaten bırakılmışsa ve değer gönderilecek bir yer yoksa,
`send` metodu bir hata döndürür. Bu örnekte, hata durumunda paniklemek için `unwrap`'i çağırıyoruz. Ancak gerçek bir 
süreklemede bunu düzgün bir şekilde ele alırız: düzgün hata işleme stratejilerini gözden geçirmek için Bölüm 9'a dönün.

Liste 16-8'de, ana iş parçacığındaki alıcıdan değeri alacağız. Bu, nehrin sonundaki sudan lastik ördeği almak veya 
bir sohbet mesajı almak gibidir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch16-fearless-concurrency/listing-16-08/src/main.rs}}
```

<span class="caption">Liste 16-8: Ana iş parçacığında “hi” değerinin alınması ve yazdırılması</span>

Alıcının iki faydalı yöntemi vardır: `recv` ve `try_recv`. Ana iş parçacığının çalışmasını engelleyecek ve kanaldan bir 
değer gönderilinceye kadar bekleyecek olan `recv`'yi kullanıyoruz. Bir değer gönderildiğinde, `recv` bu değeri `Result<T, E>` 
olarak döndürür. Verici kapandığında, `recv` daha fazla değer gelmeyeceğini belirtmek için bir hata döndürecektir.

`try_recv` yöntemi bloke olmaz, ancak bunun yerine hemen bir `Result<T, E>` döndürür: mevcutsa bir mesaj içeren bir
`Ok` değeri ve bu sefer herhangi bir mesaj yoksa bir `Err` değeri. Bu iş parçacığının mesajları beklerken yapacak
başka işleri varsa `try_recv`'i kullanmak yararlıdır: `try_recv`'i sık sık çağıran, varsa bir mesajı işleyen ve aksi 
takdirde tekrar kontrol edene kadar kısa bir süre başka işler yapan bir döngü yazabiliriz.

Bu örnekte basitlik için `recv` kullandık; ana iş parçacığının mesajları beklemek dışında yapacağı başka bir işimiz yok, 
bu nedenle ana iş parçacığını engellemek uygundur.

Liste 16-8'deki kodu çalıştırdığımızda, ana iş parçacığından yazdırılan değeri göreceğiz:

<!-- Not extracting output because changes to this output aren't significant;
the changes are likely to be due to the threads running differently rather than
changes in the compiler -->

```text
Got: hi
```

Mükemmel!

### Kanallar ve Sahiplik Transferi

Sahiplik kuralları mesaj göndermede hayati bir rol oynar çünkü güvenli, eşzamanlı kod yazmanıza yardımcı olurlar. 
Eşzamanlı programlamada hataları önlemek, Rust programlarınız boyunca sahiplik hakkında düşünmenin avantajıdır. 
Kanalların ve sahipliğin sorunları önlemek için nasıl birlikte çalıştığını göstermek için bir deney yapalım: 
kanaldan aşağı gönderdikten sonra ortaya çıkan iş parçacığında bir `val` değeri kullanmaya çalışacağız. 
Bu koda neden izin verilmediğini görmek için Liste 16-9'daki kodu derlemeyi deneyin:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch16-fearless-concurrency/listing-16-09/src/main.rs}}
```

<span class="caption">Liste 16-9: Kanaldan gönderdikten sonra `val` kullanmayı denemek</span>

Burada, `tx.send` aracılığıyla kanala gönderdikten sonra `val`'ı yazdırmayı deniyoruz. Buna izin vermek kötü 
bir fikir olacaktır: değer başka bir iş parçacığına gönderildikten sonra, biz değeri tekrar kullanmaya çalışmadan 
önce o iş parçacığı değeri değiştirebilir veya düşürebilir. Potansiyel olarak, diğer iş parçacığının değişiklikleri 
tutarsız veya var olmayan veriler nedeniyle hatalara veya beklenmedik sonuçlara neden olabilir. 
Ancak, Liste 16-9'daki kodu derlemeye çalışırsak Rust bize bir hata verir:

```console
{{#include ../listings/ch16-fearless-concurrency/listing-16-09/output.txt}}
```

Eşzamanlılık hatamız bir derleme zamanı hatasına neden oldu. `send` fonksiyonu parametresinin sahipliğini alır ve 
değer taşındığında alıcı onun sahipliğini alır. Bu, değeri gönderdikten sonra yanlışlıkla tekrar kullanmamızı engeller; 
sahiplik sistemi her şeyin yolunda olup olmadığını kontrol eder.

### Birden Fazla Değer Gönderme ve Alıcının Beklediğini Görme

Liste 16-8'deki kod derlendi ve çalıştırıldı, ancak bize iki ayrı iş parçacığının kanal üzerinden birbiriyle konuştuğunu 
açıkça göstermedi. Liste 16-10'da, Liste 16-8'deki kodun eşzamanlı olarak çalıştığını kanıtlayacak bazı değişiklikler yaptık: 
ortaya çıkan iş parçacığı artık birden fazla mesaj gönderecek ve her mesaj arasında bir saniye duracak.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch16-fearless-concurrency/listing-16-10/src/main.rs}}
```

<span class="caption">Liste 16-10: Birden fazla mesaj gönderme ve her biri arasında duraklama</span>

Bu kez, ortaya çıkan iş parçacığı ana iş parçacığına göndermek istediğimiz dizelerden oluşan bir vektöre sahiptir. 
Bunların üzerinde yineleme yaparak her birini ayrı ayrı gönderiyoruz ve `thread::sleep` fonksiyonunu `1` saniyelik bir
`Duration` değeriyle çağırarak her biri arasında duraklatıyoruz.

Ana iş parçacığında, artık `recv` fonksiyonunu açıkça çağırmıyoruz: bunun yerine, `rx`'i yineleyici olarak ele alıyoruz. 
Alınan her değer için onu yazdırıyoruz. Kanal kapatıldığında, yineleme sona erecektir.

Liste 16-10'daki kodu çalıştırdığınızda, her satır arasında `1` saniyelik bir duraklama ile aşağıdaki çıktıyı görmelisiniz:

<!-- Not extracting output because changes to this output aren't significant;
the changes are likely to be due to the threads running differently rather than
changes in the compiler -->

```text
Got: hi
Got: from
Got: the
Got: thread
```

Ana iş parçacığındaki `for` döngüsünde duraklatan veya geciktiren herhangi bir kodumuz olmadığından, 
ana iş parçacığının ortaya çıkan iş parçacığından değer almayı beklediğini söyleyebiliriz.

### Vericiyi Klonlayarak Birden Fazla Üretici Oluşturma

Daha önce `mpsc`'nin çoklu üretici, tekli tüketici için kullanılan bir kısaltma olduğundan bahsetmiştik. 
Şimdi `mpsc`'yi kullanalım ve hepsi aynı alıcıya değer gönderen birden fazla iş parçacığı oluşturmak için Liste 16-10'daki 
kodu genişletelim. Bunu, Liste 16-11'de gösterildiği gibi vericiyi klonlayarak yapabiliriz:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch16-fearless-concurrency/listing-16-11/src/main.rs:here}}
```

<span class="caption">Liste 16-11: Birden fazla üreticiden birden fazla mesaj gönderme</span>

Bu kez, ilk iş parçacığını oluşturmadan önce, vericide `clone` çağrısı yapıyoruz. 
Bu bize ilk iş parçacığına aktarabileceğimiz yeni bir verici verecektir. Orijinal vericiyi ikinci bir 
iş parçacığına aktarırız. Bu bize her biri bir alıcıya farklı mesajlar gönderen iki iş parçacığı verir.

Kodu çalıştırdığınızda, çıktınız aşağıdaki gibi görünmelidir:

<!-- Not extracting output because changes to this output aren't significant;
the changes are likely to be due to the threads running differently rather than
changes in the compiler -->

```text
Got: hi
Got: more
Got: from
Got: messages
Got: for
Got: the
Got: thread
Got: you
```

Sisteminize bağlı olarak değerleri farklı bir sırada görebilirsiniz. Eşzamanlılığı zor olduğu kadar ilginç kılan da budur.
`Thread::sleep` ile denemeler yaparsanız, farklı iş parçacıklarında farklı değerler verirseniz, her çalıştırma daha 
belirsiz olacak ve her seferinde farklı çıktılar oluşturacaktır.

Kanalların nasıl çalıştığına baktığımıza göre, şimdi farklı bir eşzamanlılık yöntemine bakalım.
