## Numaralandırılmış Yapı Tanımlamak

Yapıların size `width` ve `height` üyeleri olan bir `Rectangle` gibi ilgili üyeleri ve 
verileri gruplandırmanın bir yolunu verdiği yerde, numaralandırmalar size bir değerin 
olası bir değer kümesinden biri olduğunu söylemenin bir yolunu verir. 
Örneğin, `Rectangle`'ın `Circle` ve `Triangle`'ı da içeren olası şekillerden biri 
olduğunu söylemek isteyebiliriz. Bunu yapmak için Rust, bu olasılıkları bir `enum` 
olarak kodlamamıza izin verir.

Kodda ifade etmek isteyebileceğimiz bir duruma bakalım ve bu durumda numaralandırmaların 
neden yapılardan daha yararlı ve daha uygun olduğunu görelim. IP adresleriyle çalışmamız 
gerektiğini farz edin. Şu anda IP adresleri için iki ana standart kullanılmaktadır: 
V4 ve V6. Programımızın karşılaşacağı bir IP adresi için tek olasılık bunlar olduğundan, 
tüm olası değişkenleri sıralayabiliriz, bu da numaralandırmanın adını aldığı yerdir.

Herhangi bir IP adresi, V4 veya V6 adresi olabilir, 
ancak ikisi aynı anda olamaz. IP adreslerinin bu özelliği, `enum` veri yapısını 
uygun hale getirir, çünkü bir `enum` değeri onun türevlerinden yalnızca biri olabilir. 
Hem V4 hem de V6 adresleri hala temelde IP adresleridir, bu nedenle kod herhangi bir 
IP adresi için geçerli olan durumları işlerken aynı tür olarak ele alınmalıdır.

Bu kavramı bir `IpAddrKind` numaralandırması tanımlayarak ve bir IP adresinin olabileceği 
olası türleri V4 ve V6 olarak listeleyerek kodda ifade edebiliriz. 
Bunlar, numaralandırmanın varyantlarıdır:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-01-defining-enums/src/main.rs:def}}
```

`IpAddrKind` artık kodumuzun başka bir yerinde kullanabileceğimiz özel bir veri türüdür.

### `enum` Değerleri

`IpAddrKind`'in iki varyantının her birinin örneklerini şu şekilde oluşturabiliriz:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-01-defining-enums/src/main.rs:instance}}
```

Numaralandırmanın türevlerinin, tanımlayıcısının altında ad alanlı olduğuna ve 
ikisini ayırmak için çift iki nokta üst üste (`:`) kullandığımıza dikkat edin. 
Bu kullanışlıdır çünkü artık her iki `IpAddrKind::V4` ve `IpAddrKind::V6` değeri aynı 
türdedir: `IpAddrKind`. Daha sonra, örneğin, herhangi bir `IpAddrKind` alan bir 
fonksiyon tanımlayabiliriz:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-01-defining-enums/src/main.rs:fn}}
```

Ve bu fonksiyonu her iki değişkenle de çağırabiliriz:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-01-defining-enums/src/main.rs:fn_call}}
```

Numaralandırma kullanmanın daha da fazla avantajı vardır. IP adresi *türümüz* hakkında daha 
fazla düşünürsek, şu anda gerçek IP adresi verilerini saklamanın bir yolu yok; sadece ne tür olduğunu biliyoruz. Bölüm 5'te yapılar hakkında yeni öğrendiğinize göre, bu sorunu Liste 6-1'de gösterildiği gibi yapılarla çözmeye cazip gelebilirsiniz.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-01/src/main.rs:here}}
```

<span class="caption">Liste 6-1: Bir IP adresinin verilerinin ve `IpAddrKind` değişkeninin
`struct` kullanılarak saklanması</span>

Burada, iki üyesi olan bir `IpAddr` yapısı tanımladık: 
`IpAddrKind` türünde bir tür üyesi (daha önce tanımladığımız numaralandırma) ve 
`String` türünde bir adres üyesi. Bu yapının iki örneğine sahibiz. 
Birincisi `home`'dır ve `127.0.0.1` ilişkili adres verileriyle kendi türünde 
`IpAddrKind::V4` değerine sahiptir. İkinci örnek geri döngüdür. Tür değeri `V6` olarak 
`IpAddrKind`'in diğer türevine sahiptir ve onunla ilişkili `::1` adresine sahiptir. 
Tür ve adres değerlerini bir araya toplamak için bir yapı kullandık, 
bu yüzden şimdi değişken değerle ilişkilendirildi.

Bununla birlikte, aynı kavramı yalnızca bir numaralandırma kullanarak temsil etmek daha özlüdür: 
bir yapı içindeki bir numaralandırma yerine, verileri doğrudan her bir numaralandırma 
değişkenine koyabiliriz. `IpAddr` `enum`'un bu yeni tanımı, 
hem `V4` hem de `V6` varyantlarının ilişkili `String` değerlerine sahip olacağını söylüyor:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-02-enum-with-data/src/main.rs:here}}
```

`enum`'un her türevine doğrudan veri ekliyoruz, bu nedenle ekstra bir yapıya gerek yok. 
Burada ayrıca numaralandırmaların nasıl çalıştığına dair başka bir ayrıntıyı görmek daha kolaydır:
tanımladığımız her bir numaralandırma değişkeninin adı aynı zamanda numaralandırmanın bir örneğini
oluşturan bir fonksiyon haline gelir. Diğer bir deyişle, `IpAddr::V4()`, 
bir `String` bağımsız değişkeni alan ve `IpAddr` türünün bir örneğini döndüren bir fonksiyon çağrısıdır.
`enum`'u tanımlamanın bir sonucu olarak bu yapıcı fonksiyonu otomatik olarak tanımlarız.

Bir yapı yerine bir numaralandırma kullanmanın başka bir avantajı daha vardır: her değişken, 
farklı türde ve miktarda ilişkili veriye sahip olabilir. V4 tip IP adresleri her zaman 
0 ile 255 arasında değerlere sahip dört sayısal bileşene sahip olacaktır:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-03-variants-with-different-data/src/main.rs:here}}
```

V4 ve V6 IP adreslerini depolamak için veri yapılarını tanımlamanın birkaç farklı yolunu gösterdik. 
Ancak, ortaya çıktığı gibi, IP adreslerini saklamak ve hangi tür olduklarını kodlamak istemek o kadar
yaygın ki, [standart kütüphanenin kullanabileceğimiz bir tanımı var!][IpAddr]<!-- ignore --> 
Standart kütüphanenin `IpAddr`'yi nasıl tanımladığına bakalım: tanımladığımız ve kullandığımız 
tam `enum` ve varyantlara sahiptir, ancak adres verilerini varyantların içine, 
her varyant için farklı tanımlanmış iki farklı yapı şeklinde gömer:

```rust
struct Ipv4Addr {
    // --snip--
}

struct Ipv6Addr {
    // --snip--
}

enum IpAddr {
    V4(Ipv4Addr),
    V6(Ipv6Addr),
}
```

Bu kod, herhangi bir türde veriyi bir numaralandırma değişkeninin içine koyabileceğinizi gösterir: 
örneğin dizgiler, sayısal türler veya yapılar. Hatta başka bir numaralandırma ekleyebilirsiniz! 
Ayrıca, standart kütüphane türleri genellikle bulabileceklerinizden çok daha karmaşık değildir.

Standart kütüphanenin `IpAddr` için bir tanım içermesine rağmen, standart kütüphanenin tanımını 
kapsamımıza almadığımız için kendi tanımımızı çakışmadan oluşturup kullanabileceğimize dikkat edin. 
Bölüm 7'de türleri kapsama almak hakkında daha fazla konuşacağız.

Liste 6-2'deki başka bir numaralandırma örneğine bakalım: bu, türevlerinde gömülü çok çeşitli türlere sahiptir.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-02/src/main.rs:here}}
```

<span class="caption">Liste 6-2: Varyantlarının her biri farklı miktar ve türde değerleri saklayan bir
`Message` numaralandırması</span>

Bu numaralandırmanın farklı türlerde dört çeşidi vardır:

* `Quit` onunla ilişkili hiçbir veriye sahip değil.
* `Move` bir yapının yaptığı gibi alanları adlandırmıştır.
* `Write` tek bir `String`'i dahil eder.
* `ChangeColor` üç tane `i32` değerini dahil eder.

Liste 6-2'dekiler gibi değişkenlerle bir numaralandırma tanımlamak, 
farklı türde yapı tanımları tanımlamaya benzer, ancak numaralandırmanın `struct` anahtar sözcüğünü
kullanmaması ve tüm değişkenlerin `Message` türü altında gruplandırılması dışında. 
Aşağıdaki yapılar, önceki numaralandırma değişkenlerinin sahip olduğu aynı verileri tutabilir:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-04-structs-similar-to-message-enum/src/main.rs:here}}
```

Ancak, her biri kendi tipine sahip olan farklı yapıları kullanırsak, 
bu tür mesajların herhangi birini almak için, tek bir mesaj olan Liste 6-2'de 
tanımlanan `Message` `enum`'u ile yapabileceğimiz kadar kolay bir fonksiyon tanımlayamazdık.

Numaralandırmalar ve yapılar arasında bir benzerlik daha vardır: 
`impl` kullanarak yapılar üzerinde yöntemleri tanımlayabildiğimiz gibi, 
`enum`'lar üzerinde de yöntemler tanımlayabiliriz. İşte `Message` `enum`'umuzda 
tanımlayabileceğimiz `call` adında bir metod:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-05-methods-on-enums/src/main.rs:here}}
```

Metodun gövdesi, metodu çağırdığımız değeri almak için `self`'i kullanır.
Bu örnekte, `m` adında `Message::Write(String::from("hello"))`'yu tutan bir değişken oluşturduk 
ve `m.call()` çalıştığında `self` `call` metodunun gövdesinde olacak.

Standart kütüphanedeki çok yaygın ve kullanışlı olan başka bir numaralandırmaya bakalım: 
`Option`.

### `Option` Numaralandırması ve `Null` Değerlerine Göre Avantajları

Bu bölüm, standart kütüphane tarafından tanımlanan başka bir numaralandırma olan 
`Option`'ın örnek olay incelemesini incelemektedir. `Option` türü, bir değerin 
bir şey olabileceği veya hiçbir şey olamayacağı çok yaygın senaryoyu kodlar.

Örneğin, öğeleri içeren bir listenin ilkini talep ederseniz, bir değer alırsınız. 
Boş bir listenin ilk maddesini talep ederseniz, hiçbir şey alamazsınız. Bu kavramı 
tür sistemi cinsinden ifade etmek, derleyicinin, ele almanız gereken tüm durumları ele alıp 
almadığınızı kontrol edebileceği anlamına gelir; bu işlevsellik, diğer programlama dillerinde 
son derece yaygın olan hataları önleyebilir.

Programlama dili tasarımı genellikle hangi özellikleri eklediğinize göre düşünülür, 
ancak hariç tuttuğunuz özellikler de önemlidir. Rust, diğer birçok dilde bulunan 
*null* özelliğine sahip değildir. *null*, hiçbir değer olmadığı anlamına gelen bir değerdir. 
*null* olan dillerde, değişkenler her zaman iki durumdan birinde olabilir: *null* veya *null* değil.

*null*'un mucidi Tony Hoare, “Null References: The Billion Dollar Mistake,” adlı 2009 sunumunda şunları söylüyor:

> Ben buna milyar dolarlık hatam diyorum. O zaman, nesne yönelimli bir dilde 
> referanslar için ilk kapsamlı tip sistemini tasarlıyordum. Amacım, derleyici tarafından 
> otomatik olarak gerçekleştirilen kontrol ile referansların tüm kullanımının kesinlikle güvenli
> olmasını sağlamaktı. Ancak, uygulanması çok kolay olduğu için boş bir referans koymanın 
> cazibesine karşı koyamadım. Bu, son kırk yılda muhtemelen milyarlarca dolarlık acıya 
> ve hasara neden olan sayısız hataya, güvenlik açığına ve sistem çökmesine neden oldu.

Boş değerlerle ilgili sorun, boş olmayan bir değer olarak boş bir değer kullanmaya çalışırsanız, 
bir tür hata almanızdır. Bu boş veya boş olmayan özellik yaygın olduğundan, 
bu tür bir hatayı yapmak son derece kolaydır.

Bununla birlikte, *null*'un ifade etmeye çalıştığı kavram hala kullanışlıdır: 
*null*, şu anda geçersiz olan veya herhangi bir nedenle mevcut olmayan bir değerdir.

Sorun gerçekten konseptte değil, uygulanmasındadır. Bu nedenle, Rust'ın boş değerleri yoktur, 
ancak var olan veya olmayan bir değer kavramını kodlayabilen bir numaralandırmaya sahiptir. 
Bu numaralandırma `Option<T>`'dir ve [standart kütüphane tarafından][option]<!-- ignore -->
aşağıdaki gibi tanımlanır:
   
```rust
enum Option<T> {
    None,
    Some(T),
}
```

`Option<T>` `enum`'u o kadar kullanışlıdır ki, girişe bile dahil edilmiştir; 
Bunu açıkça kapsama sokmanız gerekmez. Varyantları da başlangıç bölümüne dahil edilmiştir: 
`Some` ve `None`'ı doğrudan `Option::` ön eki olmadan kullanabilirsiniz. `Option<T>` numaralandırma 
hala normal bir numaralandırmadır ve `Some(T)` ve `None` hala `Option<T>` türünün varyantlarıdır.

`<T>` sözdizimi, Rust'ın henüz bahsetmediğimiz bir özelliğidir. 
Bu genel bir tür parametresidir ve yaygınları Bölüm 10'da daha ayrıntılı olarak ele alacağız. 
Şimdilik, bilmeniz gereken tek şey `<T>`'nin `Option` `enum`'unun `Some` varyantının herhangi bir 
türden tek bir veri parçasını tutabileceği anlamına geldiğidir. 
Sayı türlerini ve dize türlerini tutmak için `Option` değerlerini kullanmanın bazı örnekleri:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-06-option-examples/src/main.rs:here}}
```

`some_number` türü `Option<i32>` şeklindedir. `some_char` türü, farklı bir tür olan 
`Option<char>`'dır. Rust, `Some` varyantı içinde bir değer belirttiğimiz için bu türlerin 
çıkarımını yapabilir. `absent_number` için Rust, genel `Otpion` türüne açıklama eklememizi gerektirir:
derleyici, yalnızca `None` değerine bakarak karşılık gelen `Some` varyantının tutacağı türü çıkaramaz.
Burada, `absent_number` için `Option<i32>` türünde olmasını kastettiğimizi Rust'a söylüyoruz.

Bir `Some` değerine sahip olduğumuzda, bir değerin mevcut olduğunu ve değerin `Some` içinde tutulduğunu
biliriz. `None` değerine sahip olduğumuzda, bir anlamda *null* ile aynı anlama gelir: 
geçerli bir değerimiz yoktur. Öyleyse neden `Option<T>` seçeneğine sahip olmak boş değere sahip 
olmaktan daha iyidir?

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-07-cant-use-option-directly/src/main.rs:here}}
```

Bu kodu çalıştırırsak şöyle bir hata mesajı alırız:

```console
{{#include ../listings/ch06-enums-and-pattern-matching/no-listing-07-cant-use-option-directly/output.txt}}
```

Aslında bu hata mesajı, farklı türler oldukları için Rust'ın bir `i8` ve bir `Option<i8>` nasıl 
ekleneceğini anlamadığı anlamına gelir. Rust'ta `i8` gibi bir değerimiz olduğunda, 
derleyici her zaman geçerli bir değere sahip olmamızı sağlayacaktır. 
Bu değeri kullanmadan önce *null* değerini kontrol etmek zorunda kalmadan güvenle ilerleyebiliriz. 
Yalnızca bir `Option<i8>` olduğunda (veya hangi tür değerle çalışırsak çalışalım), 
muhtemelen bir değere sahip olmama konusunda endişelenmemiz gerekir ve derleyici, 
değeri kullanmadan önce bu durumu ele aldığımızdan emin olacaktır.

Başka bir deyişle, onunla `T` işlemleri gerçekleştirmeden önce `Option<T>` öğesini `T`'ye dönüştürmeniz
gerekir. Genellikle bu, *null* ile ilgili en yaygın sorunlardan birini yakalamaya yardımcı olur: 
bir şeyin gerçekte boş olmadığını varsaymak.

Yanlış bir şekilde boş olmayan bir değer varsayma riskini ortadan kaldırmak, 
kodunuza daha fazla güvenmenize yardımcı olur. Muhtemelen *null* olabilecek bir değere sahip olmak için, 
bu değerin türünü `Option<T>` yaparak açıkça seçmelisiniz. Ardından, bu değeri kullandığınızda, 
değer boş olduğunda durumu açıkça ele almanız gerekir. Bir değerin `Option<T>` olmayan bir türü 
olduğu her yerde, değerin boş olmadığını güvenle varsayabilirsiniz. 
Bu, Rust'ın *null*'ın yaygınlığını sınırlamak ve Rust kodunun güvenliğini artırmak için kasıtlı bir 
tasarım kararıydı.

Öyleyse, `Option<T>` türünde bir değeriniz olduğunda, bu değeri kullanabilmeniz için `Some` varyantından 
`T` değerini nasıl alırsınız? `Option<T>` `enum`'u, çeşitli durumlarda yararlı olan çok sayıda yönteme
sahiptir; [dokümantasyonundan][docs]<!-- ignore --> kontrol edebilirsiniz. `Option<T>` üzerindeki metodlara aşina olmak, 
Rust ile olan yolculuğunuzda son derece yararlı olacaktır.

Genel olarak, bir `Option<T>` değeri kullanmak için her bir değişkeni işleyecek bir koda sahip olmak
istersiniz. Yalnızca bir `Some(T)` değerine sahip olduğunuzda çalışacak bir kod istiyorsunuz 
ve bu kodun iç `T`'yi kullanmasına izin veriliyor. `None` değeriniz varsa ve bu kodda başka bir kodun
çalıştırılmasını istiyorsunuz. bir `T` değeri mevcuttur. `match` ifadesi, 
numaralandırmalarla kullanıldığında tam da bunu yapan bir kontrol akışı yapısıdır: 
sahip olduğu numaralandırmanın hangi türevine bağlı olarak farklı kod çalıştırır ve bu kod, 
eşleşen değerin içindeki verileri kullanabilir.
    
[IpAddr]: ../std/net/enum.IpAddr.html
[option]: ../std/option/enum.Option.html
[docs]: ../std/option/enum.Option.html
