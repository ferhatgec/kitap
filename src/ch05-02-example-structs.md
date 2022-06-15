## Yapıları Kullanan Örnek Bir Program

Yapıları ne zaman kullanmak isteyebileceğimizi anlamak için, bir dikdörtgenin alanını hesaplayan bir program yazalım. 
Tek değişkenler kullanarak başlayacağız ve ardından bunun yerine yapıları kullanana kadar programı yeniden düzenleyeceğiz.

Piksel cinsinden belirtilen bir dikdörtgenin genişlik ve yüksekliğini alacak *rectangles* adlı,
Cargo ile yeni bir ikili proje oluşturalım ve dikdörtgenin alanını hesaplayalım. 
Liste 5-8, projemizin *src/main.rs* dosyasında tam olarak bunu yapmanın bir yolunu içeren kısa bir program göstermektedir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-08/src/main.rs:all}}
```

<span class="caption">Liste 5-8: Ayrı genişlik ve yükseklik değişkenleri 
tarafından belirtilen bir dikdörtgenin alanını hesaplama</span>

Şimdi, bu programı `cargo run` kullanarak çalıştırın.

```console
{{#include ../listings/ch05-using-structs-to-structure-related-data/listing-05-08/output.txt}}
```

Bu kod, her bir boyutta `alan` fonksiyonunu çağırarak dikdörtgenin alanını bulmayı başarır, ancak bu kodu açık ve okunabilir hale getirmek için daha fazlasını yapabiliriz.

Bu kodla ilgili sorun, `area`'nın imzasında belirgindir:

```rust,ignore
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-08/src/main.rs:here}}
```

`area` fonksiyonunun bir dikdörtgenin alanını hesaplaması gerekiyor, 
ancak yazdığımız fonksiyonun iki parametresi var ve programımızın 
hiçbir yerinde parametrelerin ilişkili olduğu net değil. 
Genişliği ve yüksekliği birlikte gruplamak daha okunaklı ve daha 
yönetilebilir olurdu. Bunu, Bölüm 3'ün [“Demet Türü”][the-tuple-type]<!-- ignore --> bölümünde, demetleri kullanarak yapabileceğimizin bir yolunu zaten tartışmıştık.

### Demetlerle Yeniden Düzenleme

Liste 5-9, programımızın demet kullanan başka bir sürümünü gösterir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-09/src/main.rs}}
```

<span class="caption">Liste 5-9: Bir demet ile 
dikdörtgenin genişliğini ve yüksekliğini belirtme</span>

Bir bakıma bu program daha iyi. Demetler yapı eklememize izin 
verir ve biz sadece bir argümanı geçeceğiz. Ancak başka bir şekilde, 
bu versiyon daha az açıktır: demetler öğelerini adlandırmaz, 
bu nedenle demetin bölümlerine indekslememiz gerekir, 
bu da hesaplamamızı daha az belirgin hale getirir.

Genişlik ve yüksekliğin karıştırılması alan hesabı için önemli değil, 
ancak dikdörtgeni ekranda çizmek istiyorsak önemlidir! Genişliğin `0` küme indeksi ve yüksekliğin küme indeksi `1` olduğunu aklımızda tutmalıyız. 
Eğer bizim kodumuzu kullanacak olsaydı, başka birinin bunu anlaması ve 
akılda tutması daha da zor olurdu. Verilerimizin anlamını kodumuzda iletmediğimiz için, hataları tanıtmak artık daha kolay.

### Yapılarla Yeniden Düzenleme: Daha Fazla Anlam Ekleme

Verileri etiketleyerek anlam eklemek için yapılar kullanırız. 
Kullandığımız demeti, Liste 5-10'da gösterildiği gibi, 
parçaların adlarının yanı sıra bütün için bir ad içeren bir yapıya dönüştürebiliriz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-10/src/main.rs}}
```

<span class="caption">Liste 5-10: Bir `Rectangle` yapı tanımlama</span>

Burada bir yapı tanımladık ve onu `Rectangle` olarak adlandırdık. 
Kıvrımlı parantezler içinde, her ikisi de `u32` tipinde olan alanları 
`width` ve `height` olarak tanımladık. Ardından, ana olarak, 
`30` genişliğinde ve `50` yüksekliğinde belirli bir 
`Rectangle` tanımı oluşturduk.

`area` fonksiyonumuz şimdi, türü bir `struct Rectangle` tanımının değişmez bir ödünç alma şekli olan `rectangle` adını verdiğimiz bir parametre ile tanımlanır. Bölüm 4'te bahsedildiği gibi, yapıyı sahiplenmek yerine ödünç 
almak istiyoruz. Bu şekilde `main`, sahipliğini korur ve fonksiyon 
imzasında ve fonksiyonu çağırdığımız yerde `&'`yi kullanmamızın nedeni 
olan `rect1`'i kullanmaya devam edebilir.

`area` fonksiyonu, `Rectangle` tanımının `width` ve `height` üyelerine
erişir (ödünç alınan bir yapı tanımının üyelerine erişmenin 
üye değerlerini hareket ettirmediğini, bu nedenle sık sık yapı 
ödünçlerini gördüğünüzü unutmayın). 
`area` için fonksiyon imzamız şimdi tam olarak ne demek istediğimizi söylüyor: 
`width` ve `height` üyelerini kullanarak `Rectangle` alanını hesaplayın. 
Bu, genişlik ve yüksekliğin birbiriyle ilişkili olduğunu ifade eder ve 
`0` ve `1`'lik demet indeks değerlerini kullanmak yerine değerlere 
açıklayıcı isimler verir. Bu, netlik için bir kazançtır.

### Türetilmiş Tanımlar ile Faydalı İşlevsellik Ekleme

Programımızda hata ayıklarken bir `Rectangle` tanımını yazdırabilmek 
ve tüm üyelerin değerlerini görebilmek faydalı olacaktır. 
Liste 5-11 [`println!` makrosunu][println]<!-- ignore --> önceki bölümlerde kullandığımız gibi
kullanmayı dener. Ancak bu işe yaramayacaktır.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-11/src/main.rs}}
```

<span class="caption">Liste 5-11: Bir `Rectangle` tanımını yazdırmaya 
çalışmak</span>

Bu kodu derlediğimizde, bir hata alıyoruz:

```text
{{#include ../listings/ch05-using-structs-to-structure-related-data/listing-05-11/output.txt:3}}
```
`println!` makrosu birçok türde biçimlendirme yapabilir ve varsayılan olarak 
süslü parantezler `println!`'e `Display` olarak bilinen biçimlendirmeyi kullanmasını söyler.
Şimdiye kadar gördüğümüz ilkel tipler varsayılan olarak `Display`'i uygular, çünkü bunu yapabileceğiniz
tek yoldur.

```text
{{#include ../listings/ch05-using-structs-to-structure-related-data/listing-05-11/output.txt:9:10}}
```

Hadi deneyelim! `println!` makro çağrısı şimdi `println!("rect1 is {:?}", rect1);`. 
`:?` belirtecini küme parantezlerinin içine koymak şunu söyler: `println!` için `Debug` adında bir 
çıktı formatı kullanmak istiyoruz. `Debug` tanımı yapımızı geliştiriciler için yararlı 
olacak şekilde yazdırmamızı sağlar, böylece kodumuz ayıklanırken değerini görebilirsiniz.

Kodu bu değişiklikle derleyin. Hala bir hata alıyoruz:

```text
{{#include ../listings/ch05-using-structs-to-structure-related-data/output-only-01-debug/output.txt:3}}
```

Ama yine de derleyici bize yardımcı olacak bir not veriyor:

```text
{{#include ../listings/ch05-using-structs-to-structure-related-data/output-only-01-debug/output.txt:9:10}}
```
Rust, hata ayıklama bilgilerini yazdırma fonksiyonunu *içerir*, 
ancak bu fonksiyonu yapımız için kullanılabilir hale getirmek için açıkça seçmeliyiz. 
Bunu yapmak için, Liste 5-12'de gösterildiği gibi, yapı tanımından hemen önce `#[derive(Debug)]` dış niteliğini ekleriz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-12/src/main.rs}}
```

<span class="caption">Liste 5-12: `Debug` tanımını türetmek için özniteliği ekleme ve 
hata ayıklama biçimlendirmesini kullanarak `Rectangle` tanımı yazdırma</span>

Şimdi programı çalıştırdığımızda herhangi bir hata almayacağız ve aşağıdaki çıktıyı göreceğiz:

```console
{{#include ../listings/ch05-using-structs-to-structure-related-data/listing-05-12/output.txt}}
```

Güzel! En güzel çıktı değil, ancak hata ayıklama sırasında kesinlikle yardımcı olacak bu 
örnek için tüm alanların değerlerini gösterir. Daha büyük yapılarımız olduğunda, okunması biraz daha
kolay çıktılara sahip olmak yararlıdır; bu durumlarda `println!`'de `{:?}` yerine `{:#?}` kullanabiliriz. 
Bu örnekte, `{:#?}` stilinin kullanılması şu sonucu verecektir:

```console
{{#include ../listings/ch05-using-structs-to-structure-related-data/output-only-02-pretty-debug/output.txt}}
```

`Debug` formatını kullanarak bir değer yazdırmanın başka bir yolu da [`dbg!`
makrosudur][dbg]<!-- ignore -->.
Bu makro ifadenin sahipliğini alır (bir referans alan `println!`'nin aksine) ve bu ifadenin sonuç değeriyle birlikte kodunuzda 
gerçekleşir ve değerin sahipliğini döndürür.

> Not: `dbg!`'yi çağırmak, `println!`'nin aksine standart hata konsolu 
> akışına (`stderr`) yazdırır. 
> Bölüm 12'deki [“Standart Çıktı Yerine Standart Hataya Hata Mesajları Yazma”][err]<!-- ignore -->
> bölümünde `stderr` ve `stdout` hakkında daha fazla konuşacağız.

İşte, genişlik alanına atanan değerle ve ayrıca `rect1`'deki tüm yapının değeriyle ilgilendiğimiz bir örnek:

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/no-listing-05-dbg-macro/src/main.rs}}
```

`30 * scale` ifadesine `dbg!` makrosunu koyabiliriz çünkü `dbg!` ifadenin değerinin sahipliğini döndürür.
Biz ise `dbg!`'nin `rect1`'in sahipliğini almasını istemiyor olduğumuzdan dolayı bir diğer çağrıda 
`rect1`'in bir referansını kullanacağız.

Bu örneğin çıktısı şuna benzemelidir:

```console
{{#include ../listings/ch05-using-structs-to-structure-related-data/no-listing-05-dbg-macro/output.txt}}
```

Çıktının ilk bitinin *src/main.rs* 10. satırdan geldiğini görebiliriz, 
burada `30 * scale` ifadesinin hatalarını ayıklıyoruz. `dbg!` *src/main.rs* dosyasının 14. satırındaki çağrı, 
`Rectangle` yapısı olan `&rect1` değerini verir. Bu çıktı, `Rectangle` türünün güzel `Debug` biçimlendirmesini kullanır. 
`dbg!` makrosu, kodunuzun ne yaptığını anlamaya çalışırken gerçekten yardımcı olabilir!

Rust, `Debug` tanımına ek olarak, `derive` özelliğiyle birlikte kullanmamız için özel türlerimize faydalı davranışlar ekleyebilecek bir 
dizi özellik sağlamıştır. Bu özellikler ve davranışları [Ekleme C][app-c]<!--
ignore -->'de listelenmiştir. Bu özellikleri özel davranışlarla nasıl uygulayacağınızı ve kendi özelliklerinizi nasıl oluşturacağınızı 
Bölüm 10'da ele alacağız. `derive`'ın dışında da birçok nitelik vardır; daha fazla bilgi için, 
[Rust Reference'ın “Nitelikler”][attributes] bölümüne bakın.

`area` fonksiyonumuz çok spesifiktir: sadece dikdörtgenlerin alanını hesaplar. 
Bu davranışı `Rectangle` yapımıza daha yakından bağlamak faydalı olacaktır, çünkü başka hiçbir türle çalışmayacaktır. 
`area` fonksiyonunu `Rectangle` türümüzde tanımlı bir `area` metoduna çevirerek bu kodu nasıl yeniden düzenlemeye devam 
edebileceğimizebakalım.

[the-tuple-type]: ch03-02-data-types.html#the-tuple-type
[app-c]: appendix-03-derivable-traits.md
[println]: ../std/macro.println.html
[dbg]: ../std/macro.dbg.html
[err]: ch12-06-writing-to-stderr-instead-of-stdout.html
[attributes]: ../reference/attributes.html
