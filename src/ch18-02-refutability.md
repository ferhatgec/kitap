## Reddedilebilirlik: Bir Model; Eşleşmede Başarısız Olabilir mi?

Modeller iki biçimde gelir: reddedilebilir ve reddedilemez. 
Geçilen herhangi bir olası değer için eşleşecek kalıplar reddedilemez. 
Bir örnek, `let x = 5` ifade yapısındaki `x` olabilir; çünkü `x` herhangi bir şeyle eşleşir 
ve bu nedenle eşleşme başarısız olamaz. Bazı olası değerler için eşleşmeyen kalıplar reddedilebilir. 

Fonksiyon parametreleri, `let` deyimleri ve `for` döngüleri yalnızca reddedilemez kalıpları kabul edebilir, 
çünkü değerler eşleşmediğinde program anlamlı bir şey yapamaz. `if let` ve `while let` ifadeleri reddedilemez ve reddedilemez 
modelleri kabul eder, ancak derleyici reddedilemez kalıplara karşı uyarır çünkü tanımları gereği olası başarısızlığı ele almayı amaçlarlar: 
bir koşulun işlevselliği, başarıya veya başarısızlığa bağlı olarak farklı performans gösterme yeteneğindedir.

Genel olarak, reddedilebilir ve reddedilemez modeller arasındaki ayrım hakkında endişelenmenize gerek yoktur; 
ancak, bir hata mesajında gördüğünüzde yanıt verebilmeniz için reddedilebilirlik kavramına aşina olmanız gerekir. 
Bu durumlarda, kodun amaçlanan davranışına bağlı olarak, modeli veya modeli kullandığınız yapıyı değiştirmeniz gerekir.

Rust'ın reddedilemez bir model gerektirdiği ve bunun tersi olduğu halde, 
çürütülebilir bir model kullanmaya çalıştığımızda ne olduğuna dair bir örneğe bakalım. Liste 18-8, 
bir `let` ifade yapısını gösterir, ancak `Some(x)` belirttiğimiz model için reddedilebilir bir modeldir. 
Tahmin edebileceğiniz gibi, bu kod derlenmeyecektir:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch18-patterns-and-matching/listing-18-08/src/main.rs:here}}
```

<span class="caption">Liste 18-8: `let` ile çürütülebilir bir 
model kullanmaya çalışmak</span>

`some_option_value`, `None` değerinde olsaydı, `Some(x)` modeliyle eşleşmezdi, bu da kalıbın reddedilebilir olduğu anlamına gelir. 
Ancak, `let` ifade yapısı yalnızca reddedilemez bir kalıbı kabul edebilir, çünkü kodun `None` değeriyle yapabileceği geçerli hiçbir şey yoktur. 
Derleme zamanında; Rust, reddedilemez bir modelin gerekli olduğu durumlarda reddedilebilir bir model kullanmaya çalıştığımızdan şikayet edecektir:

```console
{{#include ../listings/ch18-patterns-and-matching/listing-18-08/output.txt}}
```

`Some(x)` modeliyle geçerli her değeri kapsamadığımız (ve kapsayamadığımız) için, Rust haklı olarak bir derleyici hatası üretir.

Çürütülemez bir modele ihtiyaç duyulan bir çürütülebilir modelimiz varsa, modeli kullanan kodu değiştirerek düzeltebiliriz: 
`let` yerine `if let` kullanabiliriz. Ardından, model eşleşmezse, kod, süslü parantezler arasındaki kodu 
atlayarak geçerli bir şekilde devam etmesinin bir yolunu sunar. 
Liste 18-9, Liste 18-8'deki kodun nasıl düzeltileceğini gösterir.

```rust
{{#rustdoc_include ../listings/ch18-patterns-and-matching/listing-18-09/src/main.rs:here}}
```

<span class="caption">Liste 18-9: `let` yerine `if let` kullanan ve reddedilebilir modellere sahip 
bir blok kullanma</span>

Kodu bir çıkış verdik! Bu kod tamamen geçerlidir, ancak bir hata almadan reddedilemez bir model kullanamayacağımız 
anlamına gelir. Liste 18-10'da gösterildiği gibi `x` gibi her zaman eşleşecek bir model verirsek, derleyici bir uyarı verecektir.

```rust
{{#rustdoc_include ../listings/ch18-patterns-and-matching/listing-18-10/src/main.rs:here}}
```

<span class="caption">Liste 18-10: `if let` ile reddedilemez bir model
kullanmaya çalışmak</span>

Rust, reddedilemez bir modelle `if let` kullanmanın mantıklı olmadığından şikayet ediyor:

```console
{{#include ../listings/ch18-patterns-and-matching/listing-18-10/output.txt}}
```

Bu nedenle, eşleşme kolları, kalan değerleri reddedilemez bir modelle eşleştirmesi gereken son kol hariç, 
reddedilebilir modeller kullanmalıdır. Rust, tek kollu bir eşleşmede reddedilemez bir model kullanmamıza izin verir, 
ancak bu söz dizimi çok kullanışlı değildir ve daha basit bir `let` ifade yapısı ile değiştirilebilir.

Artık modeleri nerede kullanacağınızı ve reddedilebilir ve reddedilemez modeller arasındaki farkı 
bildiğinize göre, model oluşturmak için kullanabileceğimiz söz dizimini ele alalım.
