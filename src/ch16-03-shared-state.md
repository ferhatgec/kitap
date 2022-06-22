## Paylaşılan Durum Eşzamanlılığı

Mesaj geçişi, eşzamanlılığı ele almanın iyi bir yoludur, ancak tek yol değildir. Başka bir yöntem de birden fazla iş 
parçacığının aynı paylaşılan veriye erişmesidir. Go dil dokümantasyonundaki sloganın bu kısmını tekrar düşünün: 
“belleği paylaşarak iletişim kurmayın.”

Bellek paylaşarak iletişim kurmak neye benzer? Buna ek olarak, mesaj geçişi meraklıları neden bellek paylaşımını 
kullanmamaya dikkat ederler?

Bir bakıma, herhangi bir programlama dilindeki kanallar tekil sahipliğe benzer, çünkü bir değeri bir kanaldan aşağı 
aktardığınızda, artık o değeri kullanmamalısınız. Paylaşılan bellek eşzamanlılığı çoklu sahiplik gibidir: birden fazla 
iş parçacığı aynı bellek konumuna aynı anda erişebilir. Akıllı işaretçilerin çoklu sahipliği mümkün kıldığı Bölüm 15'te 
gördüğünüz gibi, çoklu sahiplik karmaşıklık yaratabilir çünkü bu farklı sahiplerin yönetilmesi gerekir. Rust'ın tür sistemi ve 
sahiplik kuralları bu yönetimin doğru yapılmasına büyük ölçüde yardımcı olur. Bir örnek olarak, paylaşılan bellek için 
en yaygın eşzamanlılık ilkellerinden biri olan mutekslere bakalım.

### Aynı Anda Bir İş Parçacığından Veriye Erişime İzin Vermek için Muteksleri Kullanma

*Muteks*, *karşılıklı dışlamanın* kısaltmasıdır, yani bir muteks herhangi bir zamanda yalnızca bir iş
parçacığının bazı verilere erişmesine izin verir. Bir muteks içindeki verilere erişmek için, bir iş parçacığı
önce muteksin kilidini almak isteyerek erişim istediğini belirtmelidir. Kilit, muteksin bir parçası olan 
ve o anda verilere kimin özel erişime sahip olduğunu takip eden bir veri yapısıdır. Bu nedenle muteks, 
kilitleme sistemi aracılığıyla tuttuğu verileri koruyor olarak tanımlanır.

Mutekslerin kullanımı zor olmakla ünlüdür çünkü iki kuralı hatırlamanız gerekir:

* Veriyi kullanmadan önce kilidi elde etmeye çalışmalısınız.
* Muteksin koruduğu verilerle işiniz bittiğinde, diğer iş parçacıklarının kilidi alabilmesi için verilerin kilidini açmanız
  gerekir.

Muteks için gerçek dünyadan bir benzetme yapmak gerekirse, bir konferansta yalnızca bir mikrofonun olduğu bir panel 
tartışması hayal edin. Bir panelist konuşmadan önce mikrofonu kullanmak istediğini söylemeli ya da 
işaret etmelidir. Mikrofonu aldıklarında, istedikleri kadar konuşabilirler ve daha sonra mikrofonu konuşmak
isteyen bir sonraki paneliste verirler. Eğer bir panelist işi bittiğinde mikrofonu vermeyi unutursa, 
başka kimse konuşamaz. Paylaşılan mikrofonun yönetimi yanlış giderse, panel planlandığı gibi çalışmaz!

Mutekslerin yönetimini doğru yapmak inanılmaz derecede zor olabilir, bu yüzden pek çok insan kanallar konusunda 
heveslidir. Ancak Rust'ın tür sistemi ve sahiplik kuralları sayesinde kilitleme
ve kilit açma işlemlerini yanlış yapamazsınız.

### `Mutex<T>` API'si

Bir muteksin nasıl kullanılacağına örnek olarak, Liste 16-12'de gösterildiği gibi tek iş parçacıklı bir bağlamda bir 
muteks kullanarak başlayalım:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch16-fearless-concurrency/listing-16-12/src/main.rs}}
```

<span class="caption">Liste 16-12: Basitlik için tek iş parçacıklı bir bağlamda `Mutex<T>` API'sini keşfetmek</span>

Birçok türde olduğu gibi, ilişkili `new` fonksiyonunu kullanarak bir `Mutex<T>` oluşturuyoruz. `Mutex` içindeki verilere 
erişmek için `lock` metodunu kullanarak kilidi alırız. Bu çağrı mevcut iş parçacığını bloke eder, böylece kilide sahip 
olma sırası bize gelene kadar herhangi bir iş yapamaz.

Kilidi elinde tutan başka bir iş parçacığı paniğe kapılırsa lock çağrısı başarısız olur. Bu durumda, hiç kimse kilidi alamaz, 
bu nedenle böyle bir durumla karşılaşırsak kilidi açmayı ve bu iş parçacığının paniklemesini sağlamayı seçtik.

Kilidi elde ettikten sonra, bu durumda num olarak adlandırılan geri dönüş değerini, içindeki verilere değiştirilebilir bir referans olarak
ele alabiliriz. Tür sistemi, `m` içindeki değeri kullanmadan önce bir kilit elde etmemizi sağlar. `m`'nin tipi `i32` değil
`Mutex<i32>`'dir, bu nedenle `i32` değerini kullanabilmek için `lock`'u çağırmalıyız. Unutmamalıyız; aksi takdirde 
tür sistemi içteki `i32`'ye erişmemize izin vermez.

Tahmin edebileceğiniz gibi, `Mutex<T>` akıllı bir işaretçidir. Daha doğrusu, `lock` çağrısı, `unwrap` çağrısıyla işlediğimiz
bir `LockResult`'a sarılmış `MutexGuard` adlı bir akıllı işaretçi döndürür. `MutexGuard` akıllı işaretçisi, 
iç verilerimize işaret etmek için `Deref`'i uygular; akıllı işaretçi ayrıca, bir `MutexGuard` kapsam dışına 
çıktığında kilidi otomatik olarak serbest bırakan bir `Drop`'a sahiptir, bu da iç kapsamın sonunda gerçekleşir. 

Sonuç olarak, kilidi serbest bırakmayı unutma. Muteksin diğer iş parçacıkları
tarafından kullanılmasını engelleme riskimiz yoktur, çünkü kilit serbest bırakma işlemi otomatik olarak gerçekleşir.

Kilidi bıraktıktan sonra muteks değerini yazdırabilir ve `i32`'yi `6` olarak değiştirebildiğimizi görebiliriz.

### Birden Fazla İş Parçacığı Arasında `Mutex<T>` Paylaşımı

Şimdi, `Mutex<T>` kullanarak bir değeri birden fazla iş parçacığı arasında paylaştırmayı deneyelim. 
10 iş parçacığı oluşturacağız ve her birinin bir sayaç değerini 1 artırmasını sağlayacağız, böylece sayaç 
0'dan 10'a gidecek. Liste 16-13'teki bir sonraki örnekte bir derleyici hatası olacak ve bu hatayı
`Mutex<T>` kullanımı ve Rust'ın bunu doğru kullanmamıza nasıl yardımcı olduğu hakkında daha fazla bilgi edinmek için 
kullanacağız.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch16-fearless-concurrency/listing-16-13/src/main.rs}}
```

<span class="caption">Liste 16-13: On iş parçacığının her biri bir `Mutex<T>` tarafından korunan bir sayacı artırır</span>

Liste 16-12'de yaptığımız gibi, `Mutex<T>` içinde `i32` tutmak için bir `counter` değişkeni oluşturuyoruz. ,
Ardından, bir dizi sayı üzerinde yineleme yaparak 10 iş parçacığı oluşturuyoruz.
`Thread::spawn` kullanıyoruz ve tüm iş parçacıklarına aynı kapanışı veriyoruz: sayacı iş parçacığına taşıyan,
`lock` metodunu çağırarak `Mutex<T>` üzerinde bir kilit elde ediyor ve ardından muteksteki değere 1 eklemiş oluyoruz. 
Bir iş parçacığı kapanışını çalıştırmayı bitirdiğinde, `num` kapsam dışına çıkar ve kilidi serbest bırakır, böylece
başka bir iş parçacığı onu alabilir.

Ana iş parçacığında, tüm birleştirme tutamaçlarını toplarız. Ardından, Liste 16-2'de yaptığımız gibi, tüm iş parçacıklarının 
bittiğinden emin olmak için her bir tanıtıcıda `join` çağrısı yaparız. Bu noktada, ana iş parçacığı kilidi alacak ve bu
programın sonucunu yazdıracaktır.

Bu örneğin derlenmeyeceğini demiştik. Şimdi nedenini bulalım!

```console
{{#include ../listings/ch16-fearless-concurrency/listing-16-13/output.txt}}
```

Hata mesajı, `counter` değerinin döngünün önceki yinelemesinde taşındığını belirtir. Rust bize kilit sayacının sahipliğini
birden fazla iş parçacığına taşıyamayacağımızı söylüyor. Derleyici hatasını Bölüm 15'te tartıştığımız çoklu sahiplik yöntemi
ile düzeltelim.

#### Çoklu İş Parçacığı ile Çoklu Sahiplik

Bölüm 15'te, referans sayılan bir değer oluşturmak için `Rc<T>` akıllı işaretçisini kullanarak bir değere 
birden fazla sahip vermiştik. Burada da aynısını yapalım ve ne olacağını görelim. Liste 16-14'te `Mutex<T>`'yi `Rc<T>`'ye 
saracağız ve sahipliği iş parçacığına taşımadan önce `Rc<T>`'yi klonlayacağız.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch16-fearless-concurrency/listing-16-14/src/main.rs}}
```

<span class="caption">Liste 16-14: Birden fazla iş parçacığının `Mutex<T>`ye sahip olmasına izin vermek için `Rc<T>` kullanılmaya çalışılıyor</span>

Bir kez daha derliyoruz ve... farklı farklı hatalar alıyoruz! Derleyici bize çok şey öğretiyor.

```console
{{#include ../listings/ch16-fearless-concurrency/listing-16-14/output.txt}}
```

İlgiçtir ki, bu hata mesajı çok karışık duruyor! İşte odaklanmanız gereken önemli kısım:
`Rc<Mutex<i32>>` iş parçacıkları arasında güvenli bir şekilde gönderilemez (`` `Rc<Mutex<i32>>` cannot be sent between threads safely ``) . Derleyici bize bunun nedenini de söylüyor:
`Send` tanımı `Rc<Mutex<i32>>` için uygulanmıyor. `Send` hakkında bir sonraki bölümde konuşacağız: `thread`'lerle 
kullandığımız türlerin eşzamanlı durumlarda kullanılmasını sağlayan özelliklerden biridir.

Ne yazık ki, `Rc<T>`'nin iş parçacıkları arasında paylaşılması güvenli değildir. `Rc<T>` referans sayımını yönetirken, 
her `clone` çağrısı için sayıma ekleme yapar ve her klon bırakıldığında sayıdan çıkarma yapar. Ancak, sayıdaki 
değişikliklerin başka bir iş parçacığı tarafından kesintiye uğratılamayacağından emin olmak için herhangi 
bir eşzamanlılık ilkeli kullanmaz. Bu, yanlış sayımlara yol açabilir - bu da bellek sızıntılarına veya 
bir değerin işimiz bitmeden önce bırakılmasına neden olabilecek ince hatalara yol açabilir. 
İhtiyacımız olan şey tam olarak `Rc<T>` gibi bir türdür, ancak referans sayımındaki değişiklikleri iş parçacığı 
güvenli bir şekilde yapan bir türdür.

#### `Arc<T>` ile Atomik Referans Sayma

Neyse ki `Arc<T>`, `Rc<T>` gibi eşzamanlı durumlarda kullanımı güvenli olan bir türdür. `A` *atomik* anlamına gelir, 
yani atomik olarak referans sayılan bir türdür. Atomikler, burada ayrıntılı olarak ele almayacağımız ek bir 
eşzamanlılık ilkelidir: daha fazla ayrıntı için [`std::sync::atomic`][atomic]<!-- ignore --> için standart kütüphane 
dokümantasyonlarına bakın. Bu noktada, atomiklerin ilkel tipler gibi çalıştığını ancak iş parçacıkları 
arasında paylaşılmasının güvenli olduğunu bilmeniz yeterlidir.

O zaman neden tüm ilkel tiplerin atomik olmadığını ve neden standart kütüphane tiplerinin varsayılan 
olarak `Arc<T>` kullanacak şekilde uygulanmadığını merak edebilirsiniz. Bunun nedeni, iş parçacığı güvenliğinin yalnızca 
gerçekten ihtiyaç duyduğunuzda ödemek isteyeceğiniz bir performans cezası ile birlikte gelmesidir. 
Sadece tek bir iş parçacığı içinde değerler üzerinde işlem yapıyorsanız, atomiklerin sağladığı garantileri 
uygulamak zorunda kalmazsanız kodunuz daha hızlı çalışabilir.

Örneğimize geri dönelim: `Arc<T>` ve `Rc<T>` aynı API'ye sahiptir, bu nedenle `use` satırını, `new` çağrısını ve `clone` 
çağrısını değiştirerek programımızı düzeltiriz. Liste 16-15'teki kod nihayet derlenecek ve çalışacaktır:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch16-fearless-concurrency/listing-16-15/src/main.rs}}
```

<span class="caption">Liste 16-15: Sahipliği birden fazla iş parçacığı arasında paylaştırabilmek için `Mutex<T>`yi sarmak üzere bir `Arc<T>` kullanmak</span>

Bu kod aşağıdakileri yazdıracaktır:

<!-- Not extracting output because changes to this output aren't significant;
the changes are likely to be due to the threads running differently rather than
changes in the compiler -->

```text
Result: 10
```

Başardık! 0'dan 10'a kadar saydık, bu çok etkileyici görünmeyebilir, ancak bize `Mutex<T>` ve iş parçacığı güvenliği hakkında 
çok şey öğretti. Bu programın yapısını bir sayacı artırmaktan daha karmaşık işlemler yapmak için de kullanabilirsiniz. 
Bu stratejiyi kullanarak, bir hesaplamayı bağımsız parçalara bölebilir, bu parçaları iş parçacıkları arasında paylaştırabilir ve
ardından her iş parçacığının nihai sonucu kendi parçasıyla güncellemesini sağlamak için `Mutex<T>`'i kullanabilirsiniz.

### `RefCell<T>`/`Rc<T>` ve `Mutex<T>`/`Arc<T>` Arasındaki Benzerlikler

Sayacın değişmez olduğunu ancak içindeki değere değişebilir bir referans alabileceğimizi fark etmiş olabilirsiniz; bu,
`Mutex<T>`'nin `Cell` ailesinin yaptığı gibi iç değişebilirlik sağladığı anlamına gelir. Bölüm 15'te `RefCell<T>`'yi bir `Rc<T>` içindeki içeriği
değiştirmemize izin vermek için kullandığımız gibi, `Mutex<T>`'yi bir `Arc<T>` içindeki içeriği değiştirmek için kullanırız.

Unutulmaması gereken bir diğer ayrıntı da `Mutex<T>` kullandığınızda Rust'ın sizi her türlü mantık hatasından koruyamayacağıdır. 
Bölüm 15'te `Rc<T>` kullanmanın, iki `Rc<T>` değerinin birbirine atıfta bulunduğu ve bellek sızıntılarına neden olan referans döngüleri
oluşturma riskiyle birlikte geldiğini hatırlayın. Benzer şekilde, `Mutex<T>` de kilitlenme yaratma riskini beraberinde getirir. 
Bunlar, bir işlemin iki kaynağı kilitlemesi gerektiğinde ve iki iş parçacığının her biri kilitlerden birini aldığında ortaya çıkar ve 
birbirlerini sonsuza kadar beklemelerine neden olur. Kilitlenmelerle ilgileniyorsanız, kilitlenmeye sahip bir Rust programı oluşturmayı deneyin; 
daha sonra herhangi bir dilde muteksler için kilitlenme azaltma stratejilerini araştırın ve bunları Rust'ta uygulamayı deneyin. `Mutex<T>` ve
`MutexGuard` için standart kütüphane API belgeleri faydalı bilgiler sunar.

Bu bölümü `Send` ve `Sync` tanımlarından ve bunları özel türlerle nasıl kullanabileceğimizden bahsederek tamamlayacağız.

[atomic]: ../std/sync/atomic/index.html
