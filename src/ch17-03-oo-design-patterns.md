## Nesneye Yönelik Tasarım Modeli Uygulamak

*Durum kalıbı*, nesne yönelimli bir tasarım kalıbıdır. Desenin özü, bir değerin dahili olarak sahip olabileceği bir dizi durum tanımlamamızdır. 
Durumlar bir dizi durum nesnesi ile temsil edilir ve değerin davranışı durumuna bağlı olarak değişir. "draft", "review" veya "published" 
kümesinden bir durum nesnesi olacak şekilde durumunu tutmak için bir alana sahip olan bir blog yazısı yapısı örneği üzerinde çalışacağız.

Durum nesneleri işlevselliği paylaşır: Rust'ta elbette nesneler ve kalıtım yerine yapıları ve özellikleri kullanırız. 
Her durum nesnesi kendi davranışından ve ne zaman başka bir duruma geçmesi gerektiğini yönetmekten sorumludur. 
Bir durum nesnesini tutan değer, durumların farklı davranışları veya durumlar arasında ne zaman geçiş yapılacağı hakkında hiçbir şey bilmez.

Durum kalıbını kullanmanın avantajı, programın iş gereksinimleri değiştiğinde, durumu tutan değerin kodunu veya değeri kullanan kodu 
değiştirmemize gerek kalmamasıdır. Kurallarını değiştirmek ya da belki daha fazla durum nesnesi eklemek için yalnızca 
durum nesnelerinden birinin içindeki kodu güncellememiz gerekecektir. Durum modelini kullanarak bir blog yazısı iş akışını aşamalı 
olarak uygulamaya başlayalım.

Nihai işlevsellik şu şekilde görünecektir:

The final functionality will look like this:

1. Bir blog gönderisi boş bir taslak olarak başlar.
2. Taslak yapıldığında, gönderinin gözden geçirilmesi istenir.
3. Gönderi onaylandığında yayınlanır.
4. Yalnızca yayınlanan blog gönderileri, içeriği yazdırılacak şekilde döndürür, bu nedenle 
   onaylanmamış gönderiler yanlışlıkla yayınlanamaz.

Bir gönderide yapılmaya çalışılan diğer değişikliklerin hiçbir etkisi olmamalıdır. 
Örneğin, inceleme talep etmeden önce taslak bir blog gönderisini onaylamaya çalışırsak, 
gönderi yayınlanmamış bir taslak olarak kalmalıdır.

Liste 17-11 bu iş akışını kod biçiminde göstermektedir: bu, `blog` adlı bir kütüphane kasasına uygulayacağımız API'nin örnek bir kullanımıdır. 
Bu henüz derlenmeyecektir çünkü `blog` crate'ini henüz yazmadık.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch17-oop/listing-17-11/src/main.rs:all}}
```

<span class="caption">Liste 17-11: `blog` kasamızda olmasını istediğimiz davranışı gösteren kod</span>

Kullanıcının `Post::new` ile yeni bir taslak blog yazısı oluşturmasına izin vermek istiyoruz. Blog yazısına metin eklenmesine izin vermek 
istiyoruz. Onaydan önce yazının içeriğini hemen almaya çalışırsak, yazı hala taslak olduğu için herhangi bir metin almamalıyız. 
Gösterim amacıyla koda `assert_eq!` ekledik. Bunun için mükemmel bir birim testi, taslak bir blog gönderisinin içerik yönteminden 
boş bir dize döndürdüğünü iddia etmek olurdu, ancak bu örnek için test yazmayacağız.

Daha sonra, yazının incelenmesi için bir isteği etkinleştirmek istiyoruz ve inceleme beklenirken içeriğin boş bir dize döndürmesini 
istiyoruz. Gönderi onay aldığında yayınlanmalıdır, yani content çağrıldığında gönderi metni döndürülecektir.

Kasadan etkileşimde bulunduğumuz tek türün `Post` türü olduğuna dikkat edin. Bu tür durum kalıbını kullanacak ve bir gönderinin 
taslak halinde, inceleme için bekliyor veya yayınlanmış olabileceği çeşitli durumları temsil eden üç durum nesnesinden biri olacak bir 
değer tutacaktır. Bir durumdan diğerine geçiş `Post` türü içinde dahili olarak yönetilecektir. Durumlar, kütüphanemizin kullanıcıları 
tarafından `Post` tanımı üzerinde çağrılan metodlara yanıt olarak değişir, ancak durum değişikliklerini doğrudan yönetmeleri gerekmez. 
Ayrıca kullanıcılar, bir gönderiyi incelenmeden önce yayınlamak gibi durumlarla ilgili bir hata yapamazlar.

### Post'u Tanımlama ve Taslak Durumunda Yeni Bir Örnek Oluşturma

Kütüphanenin yazılmasına başlayalım! Bazı içerikleri tutan genel bir `Post` yapısına ihtiyacımız olduğunu biliyoruz, 
bu nedenle yapının tanımı ve bir `Post` tanımı oluşturmak için ilişkili bir genel `new` fonksiyonu ile başlayacağız, 
Liste 17-12'de gösterildiği gibi. Ayrıca bir `Post için` tüm durum nesnelerinin sahip olması gereken davranışı tanımlayacak özel bir 
`State` tanımı oluşturacağız.

Ardından `Post`, durum nesnesini tutmak için `state` adlı özel bir alanda bir `Option<T>` içinde `Box<dyn State>`'in `trait` nesnesini 
tutacaktır. `Option<T>`'nin neden gerekli olduğunu birazdan göreceksiniz.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-12/src/lib.rs}}
```

<span class="caption">Liste 17-12: Yeni bir `Post` örneği, bir `State` tanımı ve bir `Draft` yapısı oluşturan `Post` yapısının ve 
`new` fonksiyonunun tanımı</span>

`State` özelliği, farklı posta durumları tarafından paylaşılan davranışı tanımlar. `State` nesneleri `Draft`, `PendingReview` ve `Published`'dir 
ve hepsi `State` tanımını uygular. Şimdilik, tanımın herhangi bir metodu yoktur ve sadece `Draft` durumunu tanımlayarak başlayacağız 
çünkü bir gönderinin başlamasını istediğimiz durum budur.

Yeni bir `Post` oluşturduğumuzda, `state` alanını bir `Box` tutan `Some` değerine ayarlarız. Bu `Box`, `Draft` yapısının yeni bir 
örneğine işaret eder. Bu, yeni bir `Post` örneği oluşturduğumuzda, taslak olarak başlamasını sağlar. `Post`'un durum alanı özel olduğu için, 
başka bir durumda bir `Post` oluşturmanın hiçbir yolu yoktur! `Post::new` fonksiyonunda, `content` alanını yeni, boş bir `String` olarak ayarlarız.

### Gönderi İçeriği Metnini Saklama

Liste 17-11'de `add_text` adlı bir metodu çağırabilmek ve ona blog yazısının metin içeriği olarak eklenecek bir `&str` iletebilmek istediğimizi 
gördük. Bunu, içerik alanını `pub` olarak göstermek yerine bir metod olarak uyguluyoruz, böylece daha sonra içerik alanının 
verilerinin nasıl okunacağını kontrol edecek bir metod uygulayabiliriz. `add_text` metodu oldukça basittir, bu nedenle Liste 17-13'teki 
uygulamayı `impl Post` bloğuna ekleyelim:


<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-13/src/lib.rs:here}}
```

<span class="caption">Liste 17-13: Bir gönderinin `content`'ine metin eklemek için `add_text` metodunu
süreklemek</span>

`add_text` metodu `self` öğesine değişebilir bir referans alır, çünkü `add_text` öğesini çağırdığımız `Post` tanımını değiştiriyoruz. 
Daha sonra `content`'teki `String` üzerinde `push_str`'yi çağırıyoruz ve kaydedilen içeriğe eklemek için metin argümanını iletiyoruz. 
Bu davranış, gönderinin içinde bulunduğu duruma bağlı değildir, bu nedenle durum modelinin bir parçası değildir. 
`add_text` yöntemi `state` alanıyla hiç etkileşime girmez, ancak desteklemek istediğimiz davranışın bir parçasıdır.

### Taslak Gönderinin İçeriğinin Boş Olmasını Sağlama

`add_text` öğesini çağırdıktan ve gönderimize bir miktar içerik ekledikten sonra bile, Liste 17-11'in 7. satırında gösterildiği gibi, 
gönderi hala taslak durumunda olduğu için `content` metodunun boş bir dizgi dilimi döndürmesini istiyoruz. Şimdilik, `content` 
metodunu bu gereksinimi karşılayacak en basit şeyle uygulayalım: her zaman boş bir dizgi dilimi döndürmek. Bunu daha sonra bir gönderinin 
durumunu değiştirip yayınlanabilmesini sağladığımızda değiştireceğiz. Şimdiye kadar, yazılar yalnızca taslak durumunda olabilir, 
bu nedenle yazı içeriği her zaman boş olmalıdır. Liste 17-14 bu yer tutucu uygulamasını göstermektedir:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-14/src/lib.rs:here}}
```

<span class="caption">Liste 17-14: `Post`'a `content` metodu için her zaman boş bir dizgi dilimi 
döndüren bir yer tutucu süreklemesi ekleme</span>

Bu eklenen `content` metoduyla, Liste 17-11'den 7. satıra kadar her şey amaçlandığı gibi çalışır.

### Gönderinin Durumu Değişikliklerinin Gözden Geçirilmesini Talep Etme

Ardından, durumunu `Draft`'tan `PendingReview` olarak değiştirmesi gereken bir gönderinin gözden geçirilmesini istemek 
için işlevsellik eklememiz gerekiyor. Liste 17-15 bu kodu gösterir:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-15/src/lib.rs:here}}
```

<span class="caption">Liste 17-15: `Post` ve `State` tanımı üzerinde `request_review` metodlarının 
yazılması</span>

`Post`'a, `self` öğesine değişken bir referans alacak `request_review` adında bir genel metod veriyoruz. 
Ardından `Post`'un mevcut durumu üzerinde dahili bir `request_review` metodunu çağırıyoruz ve bu ikinci `request_review` metodu 
mevcut durumu tüketip yeni bir durum döndürüyor.

`request_review` metodunu `State` tanımını ekliyoruz; tanımı uygulayan tüm türlerin artık `request_review` metodunu uygulaması gerekecektir. 
Metodun ilk parametresinin `self`, `&self` veya `&mut self` yerine `self` olduğuna dikkat edin: `Box<Self>`. Bu söz dizimi, 
yöntemin yalnızca türü taşıyan bir `Box` üzerinde çağrıldığında geçerli olduğu anlamına gelir. Bu söz dizimi `Box<Self>`'in sahipliğini 
alarak eski durumu geçersiz kılar, böylece `Post`'un durum değeri yeni bir duruma dönüşebilir.

Eski durumu kullanmak için `request_review` yönteminin durum değerinin sahipliğini alması gerekir. `Post`'un `state` alanındaki 
`Option` burada devreye girer: `take` metodunu çağırarak `Some` değerini `state` alanından çıkarırız ve yerine `None` değerini bırakırız, 
çünkü Rust yapılarda doldurulmamış alanlar olmasına izin vermez. Bu, `state` değerini ödünç almak yerine `Post`'un dışına taşımamızı sağlar. 
Daha sonra `post`'un `state` değerini bu işlemin sonucuna ayarlayacağız.

`State` değerinin sahipliğini almak için `self.state = self.state.request_review();` gibi bir kodla doğrudan ayarlamak yerine 
`state` değerini geçici olarak `None` olarak ayarlamamız gerekir. Bu, `Post`'un biz onu yeni bir duruma dönüştürdükten sonra 
eski durum değerini kullanamamasını sağlar.

`Draft` üzerindeki `request_review` yöntemi, bir gönderinin inceleme için beklediği durumu temsil eden yeni bir `PendingReview` yapısının yeni, 
`Box`'un bir örneğini döndürür. `PendingReview` `struct`'ı da `request_review` yöntemini uygular ancak herhangi bir dönüştürme yapmaz. 
Bunun yerine, kendisini döndürür, çünkü zaten `PendingReview` durumunda olan bir gönderi için inceleme istediğimizde, gönderi `PendingReview` 
durumunda kalmalıdır.

Şimdi `state` modelinin avantajlarını görmeye başlayabiliriz: `Post` üzerindeki `request_review` yöntemi, `state` değeri ne olursa olsun aynıdır. 
Her `state` kendi kurallarından sorumludur.

`Post` üzerindeki `content` metodunu olduğu gibi bırakacağız ve boş bir dizgi dilimi döndüreceğiz. Artık hem `PendingReview` durumunda hem de 
`Draft` durumunda bir `Post`'a sahip olabiliriz, ancak `PendingReview` durumunda aynı davranışı istiyoruz. 
Liste 17-11 artık 10. satıra kadar çalışıyor!
   
<!-- Old headings. Do not remove or links may break. -->
<a id="adding-the-approve-method-that-changes-the-behavior-of-content"></a>

### `content`'in Davranışını Değiştirmek için `approve` Ekleme

`approve` metodu `request_review` metoduna benzer olacaktır: `state`'i, mevcut `state`'in onaylandığında sahip olması 
gerektiğini söylediği değere ayarlayacaktır, Liste 17-16'da gösterildiği gibi:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-16/src/lib.rs:here}}
```

<span class="caption">Liste 17-16: `Post` ve `State` tanımı üzerinde `approve` metodunu uygulama</span>

`State` tanımına `approve` metodunu ekliyoruz ve `Published` durum olan `State`'i uygulayan yeni bir `struct` ekliyoruz.

`PendingReview` üzerinde `request_review` metodunun çalışmasına benzer şekilde, bir `Draft` üzerinde `approve` yöntemini çağırırsak, 
`approve` `self` değerini döndüreceği için hiçbir etkisi olmayacaktır. `PendingReview` üzerinde `approve` yöntemini çağırdığımızda, 
`Published` yapısının yeni, `Box`'un bir tanımını döndürür. `Published` `struct`'ı, `State` tanımını uygular ve 
hem `request_review` yöntemi hem de `approve` yöntemi için kendini döndürür, çünkü bu durumlarda gönderi `Published` durumunda kalmalıdır.

Şimdi `Post` üzerindeki `content` metodunu güncellememiz gerekiyor. `Content`'ten döndürülen değerin `Post`'un mevcut durumuna 
bağlı olmasını istiyoruz, bu nedenle `Post`'un Liste 17-17'de gösterildiği gibi durumuna göre tanımlanmış bir `content` metoduna 
temsilci göndermesini sağlayacağız:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch17-oop/listing-17-17/src/lib.rs:here}}
```

<span class="caption">Liste 17-17: `State`'de bir `content` metoduna yetki vermek için 
`Post` üzerindeki `content` metodunu güncelleme</span>

Amaç tüm bu kuralları `State`'i uygulayan yapıların içinde tutmak olduğundan, `state`'teki değer üzerinde bir `content` yöntemi 
çağırıyoruz ve `post` örneğini (yani `self`'i) bir argüman olarak geçiriyoruz. Daha sonra `state` değeri üzerinde `content` metodunu 
kullanarak döndürülen değeri döndürüyoruz.

`Option` üzerinde `as_ref` yöntemini çağırıyoruz çünkü değerin sahibi olmak yerine `Option` içindeki değere bir referans istiyoruz. 
`state` bir `Option<Box<dyn State>>` olduğu için, `as_ref` yöntemini çağırdığımızda bir `Option<&Box<dyn State>>` döndürülür. 
Eğer `as_ref`'i çağırmasaydık, `state`'i fonksiyon parametresinin ödünç alınan `&self`'inin dışına taşıyamayacağımız için bir hata alırdık.

Daha sonra `unwrap` metodunu çağırıyoruz, ki bu metodun asla panik yaratmayacağını biliyoruz, çünkü `Post` üzerindeki metodların, 
bu metodlar tamamlandığında `state`'in her zaman `Some` değeri içereceğini garanti ettiğini biliyoruz. 
Bu, Bölüm 9'un [“Derleyiciden Daha Fazla Bilgiye Sahip Olduğunuz Durumlar”][more-info-than-rustc]<!-- ignore --> kısmında bahsettiğimiz, 
derleyici bunu anlayamasa da None değerinin asla mümkün olmadığını bildiğimiz durumlardan biridir.

Bu noktada, `&Box<dyn State>` üzerinde `content`'i çağırdığımızda, *deref zorlaması* `&` ve `Box` üzerinde etkili olacak, 
böylece `content` yöntemi sonuçta `State` tanımını uygulayan tür üzerinde çağrılacaktır. 
Bu, `State` özellik tanımına içerik eklememiz gerektiği anlamına gelir ve Liste 17-18'de gösterildiği gibi, hangi duruma sahip 
olduğumuza bağlı olarak hangi içeriğin döndürüleceğine ilişkin mantığı buraya koyacağız:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-18/src/lib.rs:here}}
```

<span class="caption">Liste 17-18: `State` tanımına `content` yöntemini ekleme</span>

`content` yöntemi için boş bir dizgi dilimi döndüren varsayılan bir uygulama ekliyoruz. 
Bu, `Draft` ve `PendingReview` yapılarında içerik uygulamamıza gerek olmadığı anlamına gelir. `Published` `struct`'ı, 
`content` yöntemini geçersiz kılacak ve `post.content` içindeki değeri döndürecektir.

Bölüm 10'da tartıştığımız gibi, bu yöntem üzerinde yaşam süresi ek açıklamalarına ihtiyacımız olduğunu unutmayın. 
Argüman olarak bir gönderiye referans alıyoruz ve bu gönderinin bir kısmına referans döndürüyoruz, 
bu nedenle döndürülen referansın yaşam süresi post argümanının yaşam süresiyle ilişkilidir.

Ve işimiz bitti - Liste 17-11'in tamamı artık çalışıyor! `State` modelini blog yazısı iş akışı kurallarıyla uyguladık. 
Kurallarla ilgili mantık, `Post`'un içine dağılmak yerine `state` nesnelerinde yaşıyor.

### Durum Kalıbının Ödünleşimleri

Rust'ın, bir gönderinin her bir durumda sahip olması gereken farklı davranış türlerini kapsüllemek için nesne 
yönelimli durum modelini uygulayabildiğini gösterdik. Post üzerindeki yöntemler çeşitli davranışlar hakkında hiçbir şey bilmiyor. 
Kodu düzenlediğimiz şekilde, yayınlanan bir gönderinin farklı davranış biçimlerini öğrenmek için tek bir yere bakmamız gerekiyor: 
`Published` yapısındaki `State` özelliğinin uygulanması.

`State` kalıbını kullanmayan alternatif bir uygulama oluşturacak olsaydık, bunun yerine `Post` üzerindeki yöntemlerde veya hatta 
gönderinin durumunu kontrol eden ve bu yerlerde davranışı değiştiren ana kodda eşleşme ifadeleri kullanabilirdik. 
Bu, bir gönderinin yayınlanmış durumda olmasının tüm sonuçlarını anlamak için birkaç yere bakmamız gerektiği anlamına gelirdi! 
Bu, ne kadar çok durum eklersek o kadar artacaktır: bu `match` ifadelerinin her biri başka bir kola ihtiyaç duyacaktır.

`State` kalıbı ile `Post` metodları ve `Post`'u kullandığımız yerler eşleşme ifadelerine ihtiyaç duymaz ve yeni bir 
`state` eklemek için sadece yeni bir `struct` eklememiz ve `trait` metodlarını bu `struct` üzerinde uygulamamız gerekir.

`State` kalıbını kullanan uygulamanın daha fazla işlevsellik eklemek için genişletilmesi kolaydır. 
`State` kalıbını kullanan kodu korumanın basitliğini görmek için bu önerilerden birkaçını deneyin:

* Gönderinin durumunu PendingReview'den Draft'a değiştiren bir reddetme yöntemi ekleyin.
* `State` `Published` olarak değiştirilmeden önce onaylamak için iki çağrı yapılmasını zorunlu kılın.
* Kullanıcıların yalnızca bir gönderi `Draft` durumundayken metin içeriği eklemesine izin verin. 
  İpucu: `state` nesnesinin içerikle ilgili değişebilecek şeylerden sorumlu olmasını ancak `Post`'u değiştirmekten sorumlu olmamasını sağlayın.
* `State` modelinin bir dezavantajı, durumlar arasındaki geçişleri durumlar gerçekleştirdiği için bazı durumların birbirine bağlı olmasıdır.
  `PendingReview` ile `Published` arasına `Scheduled` gibi başka bir `state` eklersek, `PendingReview`'daki kodu `Scheduled`'a geçiş yapacak 
   şekilde değiştirmemiz gerekir. `PendingReview`'in yeni bir durum eklendiğinde değişmesi gerekmeseydi daha az iş olurdu, 
   ancak bu başka bir tasarım modeline geçmek anlamına gelir.

Diğer bir dezavantajı ise bazı mantıkları tekrarlamış olmamızdır. Yinelemenin bir kısmını ortadan kaldırmak için, 
`State` özelliğindeki `request_review` ve `approve` yöntemleri için `self` döndüren varsayılan uygulamalar yapmayı deneyebiliriz; 
ancak bu, nesne güvenliğini ihlal eder, çünkü özellik somut `self`'in tam olarak ne olacağını bilmez. `State`'i bir özellik nesnesi olarak 
kullanabilmek istiyoruz, bu nedenle yöntemlerinin nesne güvenli olmasına ihtiyacımız var.

Diğer tekrarlar, `Post` üzerindeki `request_review` ve `approve` yöntemlerinin benzer uygulamalarını içerir. 
Her iki yöntem de `Option`'ın state alanındaki değer üzerinde aynı yöntemin uygulanmasına delege eder ve `state` alanının yeni 
değerini sonuca ayarlar. `Post` üzerinde bu kalıbı izleyen çok sayıda yöntemimiz olsaydı, tekrarı ortadan kaldırmak için bir makro 
tanımlamayı düşünebilirdik (Bölüm 19'daki "Makrolar" bölümüne bakın).

`State` kalıbını tam olarak nesne yönelimli diller için tanımlandığı gibi uygulayarak, Rust'ın güçlü yanlarından olabildiğince 
yararlanamıyoruz. Geçersiz durumları ve geçişleri derleme zamanı hatalarına dönüştürebilecek `blog` kasasında yapabileceğimiz 
bazı değişikliklere bakalım.

#### Durumları ve Davranışları Tür Olarak Kodlama

Farklı bir dizi ödünleşim elde etmek için durum modelini nasıl yeniden düşüneceğinizi göstereceğiz. 
Durumları ve geçişleri tamamen kapsüllemek yerine, dış kodun bunlar hakkında hiçbir bilgiye sahip olmaması için 
durumları farklı türlere kodlayacağız. Sonuç olarak, Rust'ın tür kontrol sistemi, yalnızca yayınlanmış gönderilere izin 
verilen taslak gönderileri kullanma girişimlerini bir derleyici hatası vererek önleyecektir.

Liste 17-11'deki `main`'in ilk bölümünü ele alalım:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch17-oop/listing-17-11/src/main.rs:here}}
```

`Post::new` kullanarak taslak durumunda yeni gönderilerin oluşturulmasını ve gönderinin içeriğine metin ekleme özelliğini hala etkinleştiriyoruz. 
Ancak taslak bir gönderide boş bir dize döndüren bir `content` yöntemine sahip olmak yerine, taslak gönderilerin içerik yöntemine 
hiç sahip olmamasını sağlayacağız. Bu şekilde, bir taslak gönderinin içeriğini almaya çalışırsak, bize yöntemin mevcut 
olmadığını söyleyen bir derleyici hatası alırız. Sonuç olarak, taslak gönderi içeriğini üretimde yanlışlıkla görüntülememiz imkansız olacaktır, 
çünkü bu kod derlenmeyecektir bile. Liste 17-19, bir `Post` yapısının ve bir `DraftPost` yapısının tanımının yanı sıra her birindeki 
yöntemleri gösterir:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-19/src/lib.rs}}
```

<span class="caption">Liste 17-19: `content` yöntemine sahip bir `Post` ve `content` 
metodu olmayan bir `DraftPost`</span>

Hem `Post` hem de `DraftPost` yapıları, blog yazısı metnini saklayan özel bir içerik alanına sahiptir. 
Yapılar artık `state` alanına sahip değildir çünkü `state` kodlamasını yapıların türlerine taşıyoruz. 
`Post` yapısı yayınlanmış bir gönderiyi temsil edecektir ve içeriği döndüren bir içerik yöntemine sahiptir.

Hala bir `Post::new` fonksiyonumuz var, ancak `Post`'un bir örneğini döndürmek yerine `DraftPost`'un bir örneğini döndürüyor. `content`
gizli olduğundan ve `Post` döndüren herhangi bir fonksiyon bulunmadığından, şu anda bir `Post` tanımı oluşturmak mümkün değildir.

`DraftPost` yapısının bir `add_text` yöntemi vardır, bu nedenle daha önce olduğu gibi içeriğe metin ekleyebiliriz, ancak 
`DraftPost`'un tanımlanmış bir `content` yöntemi olmadığını unutmayın! Böylece program tüm gönderilerin taslak gönderiler olarak 
başlamasını sağlar ve taslak gönderilerin içerikleri görüntülenemez. Bu kısıtlamaları aşmaya yönelik herhangi bir girişim derleyici hatasıyla sonuçlanacaktır.

#### Geçişleri Farklı Türlere Dönüşümler Olarak Uygulama

Peki yayınlanmış bir gönderiyi nasıl alacağız? Bir taslak gönderinin yayınlanmadan önce gözden geçirilmesi ve 
onaylanması gerektiği kuralını uygulamak istiyoruz. Bekleyen inceleme durumundaki bir gönderi hala herhangi bir içerik 
göstermemelidir. Bu kısıtlamaları, `PendingReviewPost` adında başka bir yapı ekleyerek, bir `PendingReviewPost` döndürmek için 
`DraftPost` üzerinde `request_review` yöntemini tanımlayarak ve bir `Post` döndürmek için `PendingReviewPost` üzerinde bir `approve` yöntemi
tanımlayarak, Liste 17-20'de gösterildiği gibi yazalım:

#### Geçişleri Farklı Türlere Dönüştürme Olarak Uygulama

Peki yayınlanmış bir gönderiyi nasıl alacağız? Bir taslak gönderinin yayınlanmadan önce gözden geçirilmesi ve 
onaylanması gerektiği kuralını uygulamak istiyoruz. Bekleyen inceleme durumundaki bir gönderi hala herhangi bir içerik göstermemelidir. 
Bu kısıtlamaları, `PendingReviewPost` adında başka bir yapı ekleyerek, bir `PendingReviewPost` döndürmek için `DraftPost` üzerinde 
`request_review` yöntemini tanımlayarak ve bir `Post` döndürmek için `PendingReviewPost` üzerinde bir `approve` yöntemi tanımlayarak, 
Liste 17-20'de gösterildiği gibi uygulayalım:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-20/src/lib.rs:here}}
```

<span class="caption">Liste 17-20: `DraftPost`'ta `request_review` çağrılarak oluşturulan bir 
`PendingReviewPost` ve `PendingReviewPost`'u yayınlanmış bir `Post`'a dönüştüren bir `approve` yöntemi</span>

`request_review` ve `approve` yöntemleri `self`'in sahipliğini alır, böylece `DraftPost` ve `PendingReviewPost` örneklerini 
tüketir ve bunları sırasıyla bir `PendingReviewPost`'a ve yayınlanmış bir `Post`'a dönüştürür. Bu şekilde, üzerlerinde `request_review` çağrısı
yaptıktan sonra kalan `DraftPost` örneklerimiz olmayacaktır. `PendingReviewPost` yapısının üzerinde tanımlanmış bir `content` yöntemi yoktur, 
bu nedenle içeriğini okumaya çalışmak `DraftPost`'ta olduğu gibi bir derleyici hatasıyla sonuçlanır. Tanımlanmış bir `content` yöntemi olan
yayınlanmış bir `Post` örneği almanın tek yolu bir `PendingReviewPost` üzerinde `approve` yöntemini çağırmak olduğundan ve bir 
`PendingReviewPost` almanın tek yolu bir `DraftPost` üzerinde `request_review` yöntemini çağırmak olduğundan, artık blog yazısı 
iş akışını tür sistemine kodladık.

Ancak `main`'de de bazı küçük değişiklikler yapmamız gerekiyor. `request_review` ve `approve` yöntemleri, çağrıldıkları yapıyı 
değiştirmek yerine yeni örnekler döndürür, bu nedenle döndürülen örnekleri kaydetmek için daha fazla `let post =` *gölgeleme* ataması eklememiz 
gerekir. Ayrıca, taslak ve bekleyen inceleme gönderilerinin içerikleriyle ilgili iddiaların boş dizgiler olmasını sağlayamayız ve 
bunlara ihtiyacımız da yok: artık bu durumlardaki gönderilerin içeriğini kullanmaya çalışan kodu derleyemeyiz. 
`main`'deki güncellenmiş kod Liste 17-21'de gösterilmektedir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch17-oop/listing-17-21/src/main.rs}}
```

<span class="caption">Liste 17-21: Blog gönderisi iş akışının yeni süreklemesinin kullanmak için 
`main`'deki değişiklikler</span>

`Post`'u yeniden atamak için `main`'de yapmamız gereken değişiklikler, bu uygulamanın artık nesne yönelimli durum modeline 
tam olarak uymadığı anlamına geliyor: durumlar arasındaki dönüşümler artık tamamen `Post` uygulaması içinde kapsüllenmiyor. 
Bununla birlikte, kazancımız, tür sistemi ve derleme zamanında gerçekleşen tür denetimi nedeniyle geçersiz durumların artık 
imkansız olmasıdır! Bu, yayınlanmamış bir gönderinin içeriğinin görüntülenmesi gibi belirli hataların üretime geçmeden önce 
keşfedilmesini sağlar.

Kodun bu versiyonunun tasarımı hakkında ne düşündüğünüzü görmek için bu bölümün başında önerilen görevleri 
Liste 17-21'den sonra olduğu gibi blog sandığı üzerinde deneyin. Bazı görevlerin bu tasarımda zaten tamamlanmış 
olabileceğini unutmayın.

Rust'ın nesne yönelimli tasarım kalıplarını uygulama yeteneğine sahip olmasına rağmen, durumu tür sistemine kodlamak gibi 
diğer kalıpların da Rust'ta mevcut olduğunu gördük. Bu kalıpların farklı ödünleşimleri vardır. Nesne yönelimli kalıplara 
çok aşina olsanız da, Rust'ın özelliklerinden yararlanmak için sorunu yeniden düşünmek, derleme zamanında bazı hataları önlemek 
gibi faydalar sağlayabilir. Nesne yönelimli kalıplar, nesne yönelimli dillerin sahip olmadığı sahiplik gibi bazı özellikler 
nedeniyle Rust'ta her zaman en iyi çözüm olmayacaktır.

## Özet

Bu bölümü okuduktan sonra Rust'ın nesne yönelimli bir dil olduğunu düşünseniz de düşünmeseniz de, artık Rust'ta bazı nesne 
yönelimli özellikler elde etmek için `trait` nesnelerini kullanabileceğinizi biliyorsunuz. Dinamik gönderim, kodunuza biraz çalışma 
zamanı performansı karşılığında biraz esneklik sağlayabilir. Bu esnekliği, kodunuzun sürdürülebilirliğine yardımcı olabilecek nesne yönelimli 
kalıpları uygulamak için kullanabilirsiniz. Rust ayrıca sahiplik gibi nesne yönelimli dillerin sahip olmadığı başka özelliklere de sahiptir. 
Nesne yönelimli bir kalıp, Rust'ın güçlü yönlerinden yararlanmanın her zaman en iyi yolu olmayacaktır, ancak kullanılabilir bir seçenektir.

Daha sonra, Rust'ın çok fazla esneklik sağlayan bir başka özelliği olan kalıplara bakacağız. 
Kitap boyunca bunlara kısaca baktık ancak henüz tam kapasitelerini görmedik. Hadi başlayalım!

[more-info-than-rustc]: ch09-03-to-panic-or-not-to-panic.html#cases-in-which-you-have-more-information-than-the-compiler
[macros]: ch19-06-macros.html#macros
