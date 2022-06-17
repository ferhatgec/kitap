## Kapsam ve Gizliliği Kontrol Etmek İçin Modüllerin Tanımlanması

Bu bölümde, modüller ve modül sisteminin diğer bölümlerinden, yani öğeleri adlandırmanıza izin veren yollardan bahsedeceğiz; 
kapsam içine bir yol getiren `use` anahtar sözcüğü; ve öğeleri herkese açık hale getirmek için `pub` anahtar sözcüğü. 
Ayrıca `as` anahtar sözcüğünü, harici paketleri ve `glob` operatörünü tartışacağız.

İlk olarak, kodunuzu düzenlerken kolay referans için bir kurallar listesiyle başlayacağız. 
Ardından, kuralların her birini ayrıntılı olarak açıklayacağız.

### Modüllerin Ana Hatları

Burada modüllerin, yolların, `use` anahtar sözcüğünün ve `pub` anahtar sözcüğünün derleyicide nasıl çalıştığı ve 
çoğu geliştiricinin kodlarını nasıl düzenlediği hakkında hızlı bir referans sağlıyoruz. 
Bu bölüm boyunca bu kuralların her birinin örneklerini inceleyeceğiz, ancak şu an, 
modüllerin nasıl çalıştığını hatırlatmak için harika bir zamandır.

- **Sandık kökünden başlamak**: Bir kasayı derlerken, derleyici ilk olarak kasa 
  kök dosyasına bakar (genellikle bir kütüphane kasası için *src/lib.rs* veya bir ikili kasa için *src/main.rs*).
- **Modüller tanımlamak**: Sandık kök dosyasında yeni modüller tanımlayabilirsiniz;
  diyelim ki `mod garden` ile bir “garden” modülü tanımladınız; Derleyici, modülün kodunu şu yerlerde arayacaktır:
  - Noktalı virgül yerine süslü parantez içinde `mod garden`'ı doğrudan takip eden satır içi
  - *src/garden.rs* dosyasında
  - *src/garden/mod.rs* dosyasında
- **Alt modül tanımlamak**: Sandık kökü dışındaki herhangi bir dosyada alt modüller bildirebilirsiniz. 
  Örneğin, `mod vegetables` tanımlayabilirsiniz; *src/garden.rs*'de olacak şekilde. Derleyici, aşağıdaki yerlerde ana modül 
  için adlandırılan dizinde alt modülün kodunu arayacaktır:
  - Satır içi, noktalı virgül yerine süslü parantezler içinde; `mod vegetables`'ın hemen ardından
  - *src/garden/vegetables.rs* dosyasında
  - *src/garden/vegetables/mod.rs* dosyasında
- **Modüllerde kodlama yolları**: Bir modül kasanızın bir parçası olduğunda, gizlilik kurallarının izin verdiği sürece, 
  kodun yolunu kullanarak aynı kasadaki herhangi bir yerden o modüldeki koda başvurabilirsiniz. 
  Örneğin, *vegetables* modülündeki bir `Asparagus` türü `crate::garden::vegetables::Asparagus`'ta bulunur.
- **Özel vs genel**: Bir modül içindeki kod, varsayılan olarak üst modüllerinden özeldir. 
   Bir modülü herkese açık hale getirmek için `mod` yerine `pub mod` ile bildirin. 
   Bir genel modül içindeki öğeleri de herkese açık hale getirmek için, bildirimlerinden önce `pub`'ı kullanın.
- **`use` anahtar sözcüğü**: Bir kapsam içinde, `use` anahtar sözcüğü, uzun yolların tekrarını azaltmak için öğelere kısayollar oluşturur.
  `crate::garden::vegetables::Asparagus` ile ilgili olabilecek herhangi bir kapsamda, `use crate::garden::vegetables::Asparagus` 
  kullanarak bir kısayol oluşturabilirsiniz; ve o andan itibaren bu türden kapsamda faydalanmak için sadece `Asparagus` yazmanız yeterlidir.

Burada, bu kuralları gösteren `backyard` adında bir ikili sandık oluşturuyoruz. Kasanın `backyard` olarak da adlandırılan dizini 
şu dosyaları ve dizinleri içerir:


```text
backyard
├── Cargo.lock
├── Cargo.toml
└── src
    ├── garden
    │   └── vegetables.rs
    ├── garden.rs
    └── main.rs
```

Bu durumda sandık kök dosyası *src/main.rs* şeklindedir ve şunları içerir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/main.rs}}
```

`pub mod garden;` satırı, derleyiciye *src/garden.rs* içinde bulduğu kodu eklemesini söyler;

<span class="filename">Dosya adı: src/garden.rs</span>

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/garden.rs}}
```

Burada `pub mod vegetables`, *src/garden/vegetables.rs* içindeki kodun da dahil olduğu anlamına gelir. Bu kod:

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/garden/vegetables.rs}}
```

Şimdi bu kuralların ayrıntılarına girelim ve bunları uygulamada gösterelim!

### İlgili Kodu Modüllerde Gruplama

*Modüller*, okunabilirlik ve kolay yeniden kullanım için kodu bir kasa içinde düzenlememize izin verir. 
Modüller ayrıca, bir modül içindeki kod varsayılan olarak özel olduğundan, *öğelerin gizliliğini* kontrol etmemizi sağlar. 
Özel öğeler, harici kullanım için mevcut olmayan dahili uygulama ayrıntılarıdır.
Modülleri ve içlerindeki öğeleri herkese açık hale getirmeyi seçebiliriz, 
bu da onları harici kodun kullanmasına ve bunlara bağımlı olmasına izin verecek şekilde ortaya çıkarır.

Örnek olarak bir restoranın fonksiyonelliğini sağlayan bir kütüphane kasası yazalım. 
Fonksiyonların imzalarını tanımlayacağız, ancak bir restoranın süreklenmesinden ziyade kodun organizasyonuna konsantre 
olmak için fonksiyon içlerini boş bırakacağız.

Restoran endüstrisinde, bir restoranın bazı bölümleri *evin önü*, diğerleri ise *evin arkası* olarak adlandırılır. 
Evin önü müşterilerin bulunduğu yerdir; bu, ev sahiplerinin müşterileri oturduğu, sunucuların siparişleri ve ödemeleri aldığı ve 
barmenlerin içecek verdiği yerleri kapsar. Evin arkası, mutfakta şeflerin ve aşçıların çalıştığı, 
bulaşık makinelerinin temizlik yaptığı ve yöneticilerin idari işleri yaptığı yerdir.

Kasamızı bu şekilde yapılandırmak için işlevlerini iç içe modüller halinde düzenleyebiliriz.
`cargo new --lib restaurant` komutunu kullanarak `restaurant` adında yeni kütüphane oluşturun, daha sonra bazı 
modülleri ve fonksiyon imzalarını tanımlamak için Liste 7-1'deki kodu *src/lib.rs* içine girin. İşte evin ön cephesi:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-01/src/lib.rs}}
```

<span class="caption">Liste 7-1: Fonksiyonları içeren ve diğer modülleri içeren bir `front_of_house` 
modülü</span>

`mod` anahtar sözcüğünü ve ardından modülün adını (bu durumda, `front_of_house`) 
takip eden bir modül tanımlarız. Modülün gövdesi süslü parantezlerin içine girer. 
Modüllerin içine, diğer modülleri de yerleştirebiliriz, bu durumda modüllerin barındırılması ve 
sunulmasında olduğu gibi. Modüller ayrıca yapılar, numaralandırmalar, sabitler, özellikler ve -Liste 7-1'de olduğu gibi- 
fonksiyonlar gibi diğer öğeler için tanımları da içerebilir.

Modülleri kullanarak ilgili tanımları birlikte gruplayabilir ve neden ilişkili olduklarını adlandırabiliriz. 
Bu kodu kullanan programcılar, tüm tanımları okumak zorunda kalmadan gruplara göre kodda gezinebilir, 
bu da kendileriyle ilgili tanımları bulmayı kolaylaştırır. Bu koda yeni fonksiyonlar ekleyen programcılar, 
programı düzenli tutmak için kodu nereye yerleştireceklerini bilirler.

Daha önce, *src/main.rs* ve *src/lib.rs*'nin kasa kökleri olarak adlandırıldığından bahsetmiştik. 
Adlarının nedeni, bu iki dosyadan herhangi birinin içeriğinin, kasanın modül yapısının kökünde, 
modül ağacı olarak bilinen `crate` adlı bir modül oluşturmasıdır.

Liste 7-2, Liste 7-1'deki yapı için modül ağacını gösterir.

```text
crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```

<span class="caption">Liste 7-2: Liste 7-1'deki kod için modül ağacı</span>

Bu ağaç, bazı modüllerin birbirinin içine nasıl yuvalandığını gösterir; örneğin, `front_of_house` içinde `hosting`'i barındırır. 
Ağaç ayrıca bazı modüllerin birbiriyle kardeş olduğunu, yani aynı modülde tanımlandıklarını gösterir; 
`hosting` ve `serving`, `front_of_house` içinde tanımlanan kardeşlerdir. 
A modülü B modülünün içindeyse, A modülünün B modülünün *çocuğu* olduğunu ve B modülünün A modülünün *ebeveyni* olduğunu söyleriz. 
Tüm modül ağacının `crate` adlı örtük modül altında köklendiğine dikkat edin.

Modül ağacı size bilgisayarınızdaki dosya sisteminin dizin ağacını hatırlatabilir; 
bu çok uygun bir karşılaştırma! Tıpkı bir dosya sistemindeki dizinler gibi, kodunuzu düzenlemek için modülleri kullanırsınız. 
Ve tıpkı bir dizindeki dosyalar gibi, modüllerimizi bulmanın da kolay bir yoluna ihtiyacımız vardır.
