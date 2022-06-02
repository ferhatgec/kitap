## Zarifçe Kapatma ve Temizleme

Liste 20-20'deki kod, amaçladığımız gibi bir iş parçacığı havuzu kullanarak isteklere eşzamansız olarak yanıt veriyor. 
Doğrudan kullanmadığımız `workers`, `id` ve `thread` alanları hakkında bize hiçbir şeyi temizlemediğimizi hatırlatan bazı uyarılar alıyoruz. 
Ana iş parçacığını durdurmak için daha az zarif olan <span class="keystroke">ctrl-c</span> yöntemini kullandığımızda, bir isteği sunmanın ortasında olsalar bile diğer tüm iş parçacıkları da hemen durdurulur. 

Şimdi, havuzdaki (thread) her bir iş parçacığına katılmak için Drop tanımını uygulayacağız, böylece onlar kapanmadan önce üzerinde çalıştıkları istekleri tamamlayabilirler. 
Ardından, ileti dizilerine yeni istekleri kabul etmeyi bırakmaları ve kapatmaları gerektiğini söylemenin bir yolunu uygulayacağız. 
Bu kodu çalışırken görmek için, iş parçacığı havuzunu zarif bir şekilde kapatmadan önce sunucumuzu yalnızca iki isteği kabul edecek şekilde değiştireceğiz.

### `ThreadPool`'da `Drop` Tanımını Süreklemek

Hadi havuzumuz için `Drop` tanımını sürekleyelim. Her ne zaman havuz bırakılırsa,
iş parçacıklarımızın tamamı işlerini bitirmelidir. Liste 20-22 bize `Drop` süreklemesinin
ilk girişimini gösteriyor. Tabii bu kod henüz tam anlamıyla çalışmıyor.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch20-web-server/listing-20-22/src/lib.rs:here}}
```

<span class="caption">Liste 20-22: Havuz alandan ayrıldığında her iş parçacığına girmek</span>

İlk olarak, iş parçacığı havuzunun `workers` alanı arasında döngü yaparız. 
Bunun için `&mut` kullanıyoruz çünkü `self` değişken bir referanstır ve ayrıca `worker`'ı değiştirebiliyor olmamız gerekir. 
Her çalışan için, bu belirli çalışanın kapatıldığını söyleyen bir mesaj yazdırırız ve ardından o çalışanın iş parçacığına katılmayı çağırırız. 
Katılma çağrısı (`join`) başarısız olursa, Rust'ı paniğe sürüklemek ve uygunsuz bir kapatmaya gitmek için paketi açarız (`unwrap`).

Bu kodu derlediğimizde aldığımız hata:

```console
{{#include ../listings/ch20-web-server/listing-20-22/output.txt}}
```

Hata bize, her bir çalışanın yalnızca değişken bir referansını almaya sahip olduğumuz ve `join` argümanının sahipliğini üstlendiği için `join` diyemeyeceğimizi söylüyor. Bu sorunu çözmek için, `join`'in `thread`'i tüketebilmesi için `thread`'i `thread`'in sahibi olan `Worker` örneğinin dışına taşımamız gerekiyor. 
Bunu Liste 17-15'te yaptık: 
`Worker` bunun yerine bir `Option<thread::JoinHandle<()>>` tutarsa, değeri `Some` değişkeninin dışına taşımak ve içinde bir `None` değişkeni bırakmak için `Option` üzerindeki `take` fonksiyonunu çağırabiliriz. Başka bir deyişle, çalışan bir `Worker`'ın iş parçacığında `Some` varyantı olacaktır ve bir `Worker`'ı temizlemek istediğimizde, `Worker`'ın çalıştıracak bir iş parçacığı olmaması için `Some`'yi `None` ile değiştiririz.

Dolayısıyla, `Worker` tanımını şu şekilde güncellemek istediğimizi biliyoruz:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch20-web-server/no-listing-04-update-worker-definition/src/lib.rs:here}}
```

Şimdi değişmesi gereken diğer yerleri bulmak için derleyiciye bakalım. 
Bu kodu kontrol ederken iki hata alıyoruz:

```console
{{#include ../listings/ch20-web-server/no-listing-04-update-worker-definition/output.txt}}
```

`Worker::new`'in sonundaki koda işaret eden ikinci hatayı ele alalım; yeni bir `Worker` oluşturduğumuzda, 
iş parçacığı (`thread`) değerini `Some` içine sarmamız gerekir. Bu hatayı düzeltmek için aşağıdaki değişiklikleri yapın:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch20-web-server/no-listing-05-fix-worker-new/src/lib.rs:here}}
```

İlk hata `Drop` süreklemesindedir. `thread`'i `worker`'ın dışına taşımak için `Option` değerini alabilmek için `take` 
fonksiyonunu çağırmayı amaçladığımızdan daha önce bahsetmiştik. 

Aşağıdaki değişiklikler bunu yapacaktır:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,not_desired_behavior
{{#rustdoc_include ../listings/ch20-web-server/no-listing-06-fix-threadpool-drop/src/lib.rs:here}}
```

Bölüm 17'de söylendiği gibi, `Option` üzerindeki `take` fonksiyonu `Some` değişkenini çıkarır ve onun yerine `None`'u bırakır. 
`Some`'ı yok etmek ve ipliği almak için `if let`'i kullanıyoruz; sonra iplik üzerinde `join`'i çağırıyoruz. 
Bir işçinin iş parçacığı zaten `None` ise, işçinin iş parçacığını zaten temizlediğini biliyoruz, 
bu nedenle bu durumda hiçbir şey değişmeyecektir.

### İşleri Dinlemeyi Durdurmak İçin İpliklere Sinyal Vermek

Yaptığımız tüm değişikliklerle kodumuz herhangi bir uyarı olmadan derleniyor.
Ancak kötü haber şu ki, bu kod henüz istediğimiz gibi çalışmıyor. Anahtar, `Worker` örneklerinin 
iş parçacıkları tarafından çalıştırılan kapatmalardaki mantıktır: şu anda buna `join` diyoruz, ancak bu, iş aramak için sonsuza kadar döngü yaptıkları için iş parçacıklarını kapatmaz. Mevcut drop uygulamamızla ThreadPool'umuzu düşürmeye çalışırsak, ana iş parçacığı sonsuza kadar ilk iş parçacığının bitmesini bekleyecek. Bu sorunu çözmek için `ThreadPool` `drop` süreklemesinde bir değişikliğe ve ardından `Worker` döngüsünde (`loop`) bir değişikliğe ihtiyacımız olacak. 

İlk olarak, `ThreadPool` `drop` süreklemesini, ileti dizilerinin bitmesini beklemeden önce göndereni açıkça bırakacak şekilde değiştireceğiz.

Liste 20-23, göndereni açıkça bırakmak (`drop`) için `ThreadPool`'daki değişiklikleri gösterir. Göndericiyi (`sender`) `ThreadPool`'un dışına taşıyabilmek için aynı `Option`u kullanıyoruz ve iş parçacığında yaptığımız gibi tekniği alıyoruz:


<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground,not_desired_behavior
{{#rustdoc_include ../listings/ch20-web-server/listing-20-23/src/lib.rs:here}}
```

<span class="caption">Liste 20-23: Çalışan iş parçacıklarına katılmadan önce `sender`'ı açıkça bırakın</span>

`sender`'ı bırakmak, kanalı kapatır, bu da daha fazla mesaj gönderilmeyeceğini gösterir. Bu olduğunda, 
işçilerin sonsuz döngüde yaptığı tüm `recv` çağrıları bir hata döndürür. Liste 20-24'te, bu durumda 
döngüden zarif bir şekilde çıkmak için `Worker` döngüsünü değiştiriyoruz; bu, `ThreadPool` `drop` süreklemesi, `join` çağrısı yaptığında iş parçacıklarının biteceği anlamına geliyor.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch20-web-server/listing-20-24/src/lib.rs:here}}
```

<span class="caption">Liste 20-24: "recv" bir hata döndürdüğünde açıkça döngüden çıkar</span>

Bu kodu çalışırken görmek için, Liste 20-25'te gösterildiği gibi, 
sunucuyu düzgün bir şekilde kapatmadan önce `main`'i yalnızca iki isteği kabul edecek şekilde değiştirelim.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch20-web-server/listing-20-25/src/main.rs:here}}
```

<span class="caption">Liste 20-25: Döngüden çıkıp iki istek sunduktan sonra sunucuyu kapatır</span>

Gerçek dünya uygulamasında bir web sunucusunun yalnızca iki istek sunduktan sonra kapanmasını istemezsiniz. 
Bu kod, yalnızca zarif kapatma ve temizlemenin çalışır durumda olduğunu gösterir. 

`take` metodu, `Iterator` tanımında tanımlanır ve yinelemeyi en fazla ilk iki öğeyle sınırlar. 
`ThreadPool`, `main` sonunda kapsam dışına çıkacak ve `drop` süreklemesi çalışacaktır. 

Sunucuyu `cargo run` ile başlatın ve üç istekte bulunun. 
Üçüncü istek hata vermeli ve uçbiriminizde buna benzer bir çıktı görmelisiniz:

<!-- manual-regeneration
cd listings/ch20-web-server/listing-20-25
cargo run
curl http://127.0.0.1:7878
curl http://127.0.0.1:7878
curl http://127.0.0.1:7878
third request will error because server will have shut down
copy output below
Can't automate because the output depends on making requests
-->

```console
$ cargo run
   Compiling hello v0.1.0 (file:///projects/hello)
    Finished dev [unoptimized + debuginfo] target(s) in 1.0s
     Running `target/debug/hello`
Worker 0 got a job; executing.
Shutting down.
Shutting down worker 0
Worker 3 got a job; executing.
Worker 1 disconnected; shutting down.
Worker 2 disconnected; shutting down.
Worker 3 disconnected; shutting down.
Worker 0 disconnected; shutting down.
Shutting down worker 1
Shutting down worker 2
Shutting down worker 3
```

Farklı bir çalışan sıralaması ve yazdırılan mesajlar görebilirsiniz. 
Bu kodun nasıl çalıştığını mesajlardan görebiliriz: 
0 ve 3 numaralı işçiler ilk iki isteği aldı. Sunucu, ikinci bağlantıdan sonra bağlantıları kabul etmeyi durdurdu ve `ThreadPool`'daki 
`Drop` süreklemesi, işçi 3 daha işine başlamadan önce yürütülmeye başladı. `sender`'ı bırakmak, tüm çalışanların bağlantısını keser ve onlara kapatmalarını söyler. 

Çalışanların her biri, bağlantıyı kestiklerinde bir mesaj yazdırır ve ardından iş parçacığı havuzu, her bir çalışan iş parçacığının bitmesini beklemek için birleştirme çağırır. 

Bu uygulamanın ilginç bir yönüne dikkat edin: `ThreadPool` `sender`'ı bıraktı (`drop`) ve herhangi bir çalışan bir hata almadan önce, 
işçi 0'a girmeye çalıştık. 

İşçi 0, `recv`'den henüz bir hata almamıştı, bu nedenle ana iş parçacığı, işçi 0'ı beklemeyi engelledi. bitirmek için. Bu arada, işçi 3 bir iş aldı ve ardından tüm iş parçacıkları bir hata aldı. İşçi 0 bittiğinde, ana iş parçacığı diğer işçilerin bitirmesini bekledi. 
Bu noktada, hepsi döngülerinden çıkmış ve durmuşlardı. Tebrikler! Artık projemizi tamamladık; zaman uyumsuz olarak yanıt vermek için bir iş parçacığı havuzu kullanan temel bir web sunucumuz var. Havuzdaki tüm iş parçacıklarını temizleyen sunucunun zarif bir şekilde kapatılmasını gerçekleştirebiliyoruz. 

Referans için tam kod:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch20-web-server/no-listing-07-final-code/src/main.rs}}
```

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch20-web-server/no-listing-07-final-code/src/lib.rs}}
```

Daha fazlasını da yapabilirdik! Eğer bu projeyi geliştirmeye devam etmek istiyorsanız, burada
size yardımcı olacak bazı fikirler var:

* `ThreadPool`'a ve genel yöntemlerine daha fazla belge ekleyin.
* Kütüphanenin işlevselliğine ilişkin testler ekleyin
* Daha kararlı hata işleme için fonksiyon çağrılarına `unwrap` ekleyin.
* Web isteklerini sunmaktan başka bir görevi gerçekleştirmek için `ThreadPool`'u kullanın.
* [crates.io](https://crates.io/)'da iş parçacığı havuzu arayın ve
  yaptığımız web sunucusunu bulduğunuz kasayda sürekleyin.
  Daha sonra süreklediğimizle arasındaki API kararlılığını karşılaştırın.

## Özet

Bravo! 
Kitabın sonuna geldiniz! 
Rust'un bu turuna katıldığınız için size teşekkür etmek istiyoruz. 
Artık kendi Rust projelerinizi uygulamaya ve diğer insanların projelerine yardım etmeye hazırsınız. 
Rust yolculuğunuzda karşılaştığınız her türlü zorlukta size yardım etmeyi sevecek diğer Rustseverlerden 
oluşan hoş bir topluluk olduğunu unutmayın.
