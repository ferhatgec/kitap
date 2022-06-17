## Modül Ağacındaki Bir Öğeye Başvuru Yolları

Bir modül ağacında bir öğeyi nerede bulacağını Rust'a göstermek için, bir dosya sisteminde gezinirken bir yol 
kullandığımız gibi bir yol kullanırız. Bir fonksiyonu çağırmak için yolunu bilmemiz gerekir.

Bir yol iki şekilde olabilir:

* *Mutlak yol*, sandık kökünden başlayan tam yoldur; harici bir kasadan gelen kod için, 
  mutlak yol kasa adıyla başlar ve mevcut kasadan gelen kod için değişmez kasayla başlar.
* *Göreli yol*, geçerli modülden başlar ve geçerli modülde `self`, `super` veya bir tanımlayıcı kullanır.


Hem mutlak hem de göreli yolları, çift iki nokta (`::`) üst üste ile ayrılmış bir veya daha fazla tanımlayıcı izler.

Liste 7-1'e dönersek, `add_to_waitlist` fonksiyonunu çağırmak istediğimizi varsayalım. 
Bu, şunu sormakla aynıdır: `add_to_waitlist` fonksiyonunun yolu nedir? 
Liste 7-3, bazı modüller ve fonksiyonlar kaldırılmış olarak Liste 7-1'i içerir. 
Kasa kökünde tanımlanan yeni bir `eat_at_restaurant` fonksiyonundan sonra `add_to_waitlist` fonksiyonunu çağırmanın iki yolunu göstereceğiz.
`eat_at_restaurant` fonksiyonu, kütüphane kasamızın genel API'sinin bir parçasıdır, 
bu yüzden onu `pub` anahtar kelimesiyle işaretliyoruz. [“`pub` Anahtar Sözcüğüyle Yolları Gösterme”][pub]<!-- ignore
--> bölümünde, `pub` hakkında daha fazla ayrıntıya gireceğiz. Bu örneğin henüz derlenmeyeceğini unutmayın; 
nedenini birazdan açıklayacağız.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-03/src/lib.rs}}
```

<span class="caption">Liste 7-3: Mutlak ve göreli yolları kullanarak `add_to_waitlist`
fonksiyonunu çağırma</span>

`eat_at_restaurant`'ta `add_to_waitlist` fonksiyonunu ilk çağırdığımızda, mutlak bir yol kullanırız. 
`add_to_waitlist` fonksiyonu, `eat_at_restaurant` ile aynı kasada tanımlanır; bu, mutlak bir yol başlatmak için `crate` anahtar sözcüğünü
kullanabileceğimiz anlamına gelir. Ardından, `add_to_waitlist`'e gidene kadar ardışık modüllerin her birini dahil ederiz. 
Aynı yapıya sahip bir dosya sistemi hayal edebilirsiniz: `add_to_waitlist` programını çalıştırmak için 
`/front_of_house/hosting/add_to_waitlist` yolunu belirtirdik; kasa kökünden başlamak için `crate` adını kullanmak, 
kabuğunuzdaki dosya sistemi kökünden başlamak için `/` kullanmaya benzer.

`eat_at_restaurant`'ta `add_to_waitlist`'i ikinci kez çağırdığımızda, göreceli bir yol kullanırız. 
Yol, modül ağacının `eat_at_restaurant` ile aynı düzeyinde tanımlanan modülün adı olan `front_of_house` ile başlar. 
Burada dosya sistemi eşdeğeri `front_of_house/hosting/add_to_waitlist` yolunu kullanıyor olacaktır. 
Bir modül adıyla başlamak, yolun göreceli olduğu anlamına gelir.

Göreceli mi yoksa mutlak yol mu kullanacağınızı seçmek, projenize göre vereceğiniz bir karardır ve 
öğe tanım kodunu öğeyi kullanan koddan ayrı mı yoksa onunla birlikte mi taşıma olasılığınızın daha yüksek olduğuna bağlıdır. 
Örneğin, `front_of_house` modülünü ve `eat_at_restaurant` fonksiyonunu `customer_experience` adlı bir modüle taşırsak, mutlak yolu 
`add_to_waitlist`'e güncellememiz gerekir, ancak göreli yol yine de geçerli olur. Ancak, `eat_at_restaurant` fonksiyonunu `dining` adlı bir 
modüle ayrı olarak taşırsak, `add_to_waitlist` çağrısının mutlak yolu aynı kalır, ancak göreli yolun güncellenmesi gerekir. 
Genel olarak tercihimiz mutlak yollar belirtmektir, çünkü kod tanımlarını ve öğe çağrılarını birbirinden bağımsız olarak taşımak 
isteyeceğimiz daha olasıdır.

Liste 7-3'ü derlemeye çalışalım ve neden henüz derlenmediğini öğrenelim! Aldığımız hata Liste 7-4'te gösterilmektedir.

```console
{{#include ../listings/ch07-managing-growing-projects/listing-07-03/output.txt}}
```

<span class="caption">Liste 7-4: Liste 7-3'te kodun oluşturulmasından kaynaklanan 
derleyici hataları</span>

Hata mesajları, modül `hosting`'in gizli olduğunu söylüyor. Başka bir deyişle, 
`hosting` modülü ve `add_to_waitlist` fonksiyonu için doğru yollara sahibiz, ancak gizli bölümlere erişimi olmadığı 
için Rust bunları kullanmamıza izin vermiyor. Rust'ta tüm öğeler (fonksiyonlar, yöntemler, yapılar, numaralandırmalar, 
modüller ve sabitler) varsayılan olarak üst modüllere gizlidir. Bir fonksiyon veya yapı gibi bir öğeyi gizli yapmak istiyorsanız, 
onu bir modüle koyarsınız.

Bir üst modüldeki öğeler, alt modüllerdeki gizli öğeleri kullanamaz, ancak alt modüllerdeki öğeler, 
üst modüllerindeki öğeleri kullanabilir. Bunun nedeni, alt modüllerin uygulama ayrıntılarını sarması ve gizlemesidir, 
ancak alt modüller tanımlandıkları bağlamı görebilir. Metaforumuza devam etmek için, gizlilik kurallarını bir restoranın 
arka ofisi gibi düşünün: orada olup bitenler restoran müşterilerine özeldir, ancak ofis yöneticileri işlettikleri restoranda
her şeyi görebilir ve yapabilir.

Rust, modül sisteminin bu şekilde çalışmasını seçti, böylece dahili uygulama ayrıntılarını gizlemek varsayılandır. 
Bu şekilde, dış kodu bozmadan iç kodun hangi kısımlarını değiştirebileceğinizi bilirsiniz. Ancak Rust, bir öğeyi herkese açık 
hale getirmek için `pub` anahtar sözcüğünü kullanarak alt modüllerin kodunun iç kısımlarını dış üst modüllere gösterme seçeneği sunar.

### `pub` Anahtar Kelimesiyle Yolları Gösterme

`hosting` modülünün gizli olduğunu söyleyen Liste 7-4'teki hataya dönelim. 
Ana modüldeki `eat_at_restaurant` fonksiyonunun alt modüldeki `add_to_waitlist` fonksiyonuna erişmesini istiyoruz, 
bu nedenle `hosting` modülünü Liste 7-5'te gösterildiği gibi `pub` anahtar sözcüğüyle işaretliyoruz.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-05/src/lib.rs}}
```

<span class="caption">Liste 7-5: `eat_at_restaurant`'tan kullanmak için `hosting` modülünü `pub` 
olarak bildirme</span>

Ne yazık ki, Liste 7-5'teki kod, Liste 7-6'da gösterildiği gibi hala bir hatayla sonuçlanıyor.

```console
{{#include ../listings/ch07-managing-growing-projects/listing-07-05/output.txt}}
```

<span class="caption">Liste 7-6: Liste 7-5'te kodun oluşturulmasından kaynaklanan 
derleyici hataları</span>

Ne oldu? `mod hosting`'in önüne `pub` anahtar sözcüğünü eklemek, modülü herkese açık hale getirir. 
Bu değişiklikle `front_of_house`'a erişebilirsek, `hosting`'e de erişebiliriz. Ancak `hosting` içeriği hala gizlidir; 
modülü herkese açık hale getirmek, içeriğini herkese açık hale getirmez. Bir modüldeki `pub` anahtar sözcüğü, iç koduna erişmesine değil, 
yalnızca ata modüllerindeki kodun ona başvurmasına izin verir. Modüller kapsayıcı olduğundan, yalnızca modülü herkese açık hale getirerek
yapabileceğimiz pek bir şey yoktur; daha ileri gitmemiz ve modül içindeki bir veya daha fazla öğeyi de herkese açık hale getirmeyi 
seçmemiz gerekiyor.

Liste 7-6'daki hatalar, `add_to_waitlist` fonksiyonunun gizli olduğunu söylüyor. 
Gizlilik kuralları yapılar, numaralandırmalar, fonksiyonlar ve yöntemler ile modüller için geçerlidir.

Ayrıca Liste 7-7'deki gibi tanımından önce `pub` anahtar sözcüğünü ekleyerek `add_to_waitlist` fonksiyonunu genel yapalım.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground,test_harness
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-07/src/lib.rs}}
```

<span class="caption">Liste 7-7: `mod hosting` ve `fn add_to_waitlist`'e `pub` eklemek, 
fonksiyonu `eat_at_restaurant`'tan çağırabilmemizi sağlar</span>

Kod derlenecek! `pub` anahtar sözcüğünü eklemenin neden bu yolları `add_to_waitlist`'te gizlilik kurallarına göre kullanmamıza 
izin verdiğini görmek için mutlak ve göreli yollara bakalım.

Mutlak yolda, kasamızın modül ağacının kökü olan `crate` ile başlıyoruz. `front_of_house` modülü kasa kökünde tanımlanır. 
`front_of_house` herkese açık olmasa da, `eat_at_restaurant` fonksiyonu `front_of_house` ile aynı modülde tanımlandığından 
(yani, `eat_at_restaurant` ve `front_of_house` kardeştir), `eat_at_restaurant`'tan `front_of_house`'a başvurabiliriz. 
Sonraki, `pub` ile işaretlenmiş barındırma modülüdür. Barındırma ana modülüne erişebiliriz, 
böylece barındırmaya erişebiliriz. Son olarak, `add_to_waitlist` fonksiyonu `pub` ile işaretlenmiştir ve üst modülüne erişebiliriz, 
böylece bu fonksiyon çağrısı çalışır!

Göreceli yolda mantık, ilk adım dışında mutlak yolla aynıdır: `crate` kökünden başlamak yerine, yol `front_of_house`'dan başlar. 
`front_of_house` modülü, `eat_at_restaurant` ile aynı modül içinde tanımlanır, bu nedenle, `eat_at_restaurant`'ın tanımlandığı modülden 
başlayan göreli yol çalışır. Ardından, `hosting` ve `add_to_waitlist` `pub` ile işaretlendiğinden, yolun geri kalanı çalışır 
ve bu fonksiyon çağrısı da geçerlidir ve çalışır!

Diğer projelerin kodunuzu kullanabilmesi için kütüphane kasanızı paylaşmayı planlıyorsanız, 
genel API'niz, kasanızın kullanıcılarıyla, kodunuzla nasıl etkileşim kurabileceklerini belirleyen sözleşmenizdir. 
İnsanların kasanıza bağımlı olmasını kolaylaştırmak için genel API'nizde yapılan değişiklikleri yönetme konusunda birçok husus vardır. 
Bu düşünceler bu kitabın kapsamı dışındadır; Bu konuyla ilgileniyorsanız, bkz. [Rust API Yönergeleri][api-guidelines].

> #### İkili Program ve Kitaplık İçeren Paketler için En İyi Uygulamalar
> 
> Bir paketin hem bir *src/main.rs* ikili kasa kökü hem de bir *src/lib.rs* kütüphane kasa kökü içerebileceğinden ve 
> her iki kasanın da varsayılan olarak paket adına sahip olacağından bahsetmiştik. Tipik olarak, 
> hem kütüphane hem de ikili kasa içeren bu modele sahip paketler, ikili kasada kütüphane kasasıyla kod çağıran bir yürütülebilir 
> dosyayı başlatmak için yeterli koda sahip olacaktır. Bu, kütüphane kasasının kodu paylaşılabildiğinden, diğer projelerin 
> paketin sağladığı en fazla işlevsellikten faydalanmasını sağlar.
> Modül ağacı *src/lib.rs* içinde tanımlanmalıdır. Ardından, paketin adıyla yolları başlatarak ikili sandıkta herhangi 
> bir genel öğe kullanılabilir. İkili kasa, tıpkı tamamen harici bir kasanın kütüphane kasasını kullanması gibi kütüphane kasasının 
> bir kullanıcısı olur: yalnızca genel API'yi kullanabilir. Bu, iyi bir API tasarlamanıza yardımcı olur; 
> sadece yazar değil, aynı zamanda bir müşterisiniz!
> 
> [Bölüm 12][ch12]<!-- ignore -->'de hem ikili kasa hem de kitaplık kasası içeren bir komut satırı programıyla bu organizasyonel uygulamayı
> göstereceğiz.

### Göreli Yolları `super` ile Başlatma

Yolun başlangıcında `super` kullanarak, geçerli modül veya kasa kökü yerine ana modülde başlayan göreli yollar oluşturabiliriz. 
Bu, `..` söz dizimi ile bir dosya sistemi yolunu başlatmak gibidir. Bu, üst modülde olduğunu bildiğimiz bir öğeye başvurmamızı sağlar; 
bu, modül üst öğeyle yakından ilişkili olduğunda modül ağacının yeniden düzenlenmesini kolaylaştırabilir, 
ancak üst öğe bir gün modül ağacında başka bir yere taşınabilir.

Bir şefin yanlış bir siparişi düzelttiği ve bunu müşteriye kişisel olarak sunduğu durumu modelleyen Liste 7-8'deki kodu göz önünde bulundurun. `back_of_house` modülünde tanımlanan `fix_incorrect_order` fonksiyonu, `super` ile başlayan `deliver_order` yolunu belirterek üst modülde 
tanımlanan `deliver_order` fonksiyonunu çağırır:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground,test_harness
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-08/src/lib.rs}}
```

<span class="caption">Liste 7-8: `super` ile başlayan göreli bir yol kullanarak 
bir fonksiyonu çağırma</span>

`fix_incorrect_order` fonksiyonu `back_of_house` modülündedir, 
bu nedenle `super`'i `back_of_house`'un üst modülüne gitmek için kullanabiliriz, 
bu durumda kasa, köktür. Oradan `deliver_order` arar ve buluruz. Başarılı! Kasanın modül ağacını yeniden düzenlemeye karar vermemiz durumunda, 
`back_of_house` modülünün ve `deliver_order` fonksiyonunun birbiriyle aynı ilişkide kalacağını ve birlikte hareket edeceğini düşünüyoruz. 
Bu nedenle, gelecekte bu kod farklı bir modüle taşınırsa kodu güncellemek için daha az yerimiz olacağı için `super` kullandık.

### Yapıları ve Numaralandırmaları Herkese Açık Yapma

Yapıları ve numaralandırılmış yapıları `public` olarak atamak için `pub`'ı da kullanabiliriz, 
ancak `pub`'ın yapılar ve numaralandırmalarla kullanımına ilişkin birkaç ayrıntı daha vardır. 
Bir `struct` tanımından önce `pub` kullanırsak, `struct`'ı herkese açık yaparız, ancak `struct`'ın alanları yine gizli olur. 
Her bir alanı duruma göre kamuya açık hale getirebilir veya açıklamayabiliriz. Liste 7-9'da, genel bir `toast` üyesi, 
ancak özel bir `seasonal_fruit` üyesi olan bir genel `back_of_house::Breakfast` yapısı tanımladık. 
Bu, müşterinin yemekle birlikte gelen ekmeğin türünü seçebildiği, ancak şefin mevsime ve stokta bulunanlara göre yemeğe hangi meyvenin 
eşlik edeceğine karar verdiği bir restorandaki durumu modellemektedir. Mevcut meyve stoğu hızla değişir, 
bu nedenle müşteriler meyveyi seçemez ve hatta hangi meyveyi alacaklarını göremezler.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-09/src/lib.rs}}
```

<span class="caption">Liste 7-9: Bazı ortak üyeler ve bazı gizli üyeler içeren 
bir yapı</span>

`back_of_house::Breakfast` yapısındaki `toast` alanı genel olduğundan, 
`eat_at_restaurant`'ta nokta gösterimini kullanarak `toast` alanına yazabilir ve okuyabiliriz. 
`seasonal_fruit` gizli olduğu için `eat_at_restaurant`'ta `seasonal_fruit` üyesini kullanamadığımıza dikkat edin. 
Hangi hatayı aldığınızı görmek için `seasonal_fruit` üye değerini değiştirerek satırın yorumunu kaldırmayı deneyin!

Ayrıca, `back_of_house::Breakfast`'ın gizli bir alanı olduğundan, yapının bir `Breakfast` örneği oluşturan ortak bir 
ilişkili fonksiyon sağlaması gerektiğini unutmayın (buraya `summer` adını verdik). Eğer `Breakfast`'ın böyle bir fonksiyonu olmasaydı,
`eat_at_restaurant`'ta özel `seasonal_fruit` üyesinin değerini ayarlayamadığımız için, `eat_at_restaurant`'ta bir `Breakfast` örneği oluşturamazdık.

Buna karşılık, bir numaralandırmayı herkese açık yaparsak, tüm varyantları genel olur. Liste 7-10'da gösterildiği gibi, 
`pub`'a yalnızca `enum` anahtar sözcüğünden önce ihtiyacımız var.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-10/src/lib.rs}}
```

<span class="caption">Liste 7-10: Bir numaralandırmanın genel olarak atanması, 
tüm türevlerini herkese açık hale getirir</span>

`Appetizer` `enum`'u `public` yaptığımız için, `eat_at_restaurant`'ta `Soup` ve `Salad` çeşitlerini kullanabiliriz.

Değişkenleri herkese açık olmadığı sürece, numaralandırmalar pek kullanışlı değildir; her durumda tüm numaralandırma değişkenlerine 
`pub` ile açıklama eklemek can sıkıcı olurdu, bu nedenle numaralandırma değişkenleri için varsayılan değer herkese açık olmaktır. 
Yapılar genellikle alanları herkese açık olmadan yararlıdır, bu nedenle yapı alanları, `pub` ile açıklama yapılmadığı sürece varsayılan olarak 
her şeyin gizli olduğu genel kuralını izler.

`pub` ile ilgili ele almadığımız bir durum daha var ve bu bizim son modül sistemi özelliğimiz: `use` anahtar sözcüğü. 
İlk önce `use`'ı tek başına ele alacağız ve ardından `pub` ve `use`'ın nasıl birleştirileceğini göstereceğiz.

[pub]: ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html#exposing-paths-with-the-pub-keyword
[api-guidelines]: https://rust-lang.github.io/api-guidelines/
[ch12]: ch12-00-an-io-project.html
