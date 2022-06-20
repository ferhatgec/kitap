## Gelişmiş Fonksiyonlar ve Kapanış İfadeleri

Bu bölümde, fonksiyon işaretçileri ve dönen kapanışlar da dahil olmak üzere fonksiyonlar ve kapanışlarla 
ilgili bazı gelişmiş özellikler incelenmektedir.

### Fonksiyon İşaretçileri

Fonksiyonlara kapanışların nasıl aktarılacağından bahsetmiştik; normal fonksiyonları da fonksiyonlara aktarabilirsiniz! 
Bu teknik, yeni bir kapanış tanımlamak yerine daha önce tanımladığınız bir fonksiyonu geçirmek istediğinizde kullanışlıdır. 
Fonksiyonlar, `Fn` kapanış tanımı ile karıştırılmaması gereken `fn` (küçük `f` harfi ile) türüne zorlanır. `fn` türüne fonksiyon işaretçisi 
denir. Fonksiyonları fonksiyon işaretçileriyle geçirmek, fonksiyonları diğer fonksiyonlara argüman olarak kullanmanızı sağlar.

Bir parametrenin bir fonksiyon işaretçisi olduğunu belirtmek için kullanılan söz dizimi, parametresine bir ekleyen `add_one` 
fonksiyonunu tanımladığımız Liste 19-27'de gösterildiği gibi, kapanışlarınkine benzer. `do_twice` fonksiyonu iki parametre alır: 
bir `i32` parametresi alan ve bir `i32` döndüren herhangi bir fonksiyonun fonksiyon işaretçisi ve bir `i32` değeri. `do_twice` fonksiyonu
`f` işlevini iki kez çağırır, `arg` değerini geçirir ve ardından iki fonksiyon çağrısı sonucunu birbirine ekler. `main` fonksiyonu `do_twice`
fonksiyonunu `add_one` ve `5` argümanlarıyla çağırır.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch19-advanced-features/listing-19-27/src/main.rs}}
```

<span class="caption">Liste 19-27: Bir fonksiyon işaretçisini argüman olarak kabul etmek için `fn` türünü kullanma</span>

Bu kod çıktısı `The answer is: 12` olur. `do_twice` içindeki `f` parametresinin, `i32` türünde bir parametre alan ve bir 
`i32` döndüren bir `fn` olduğunu belirtiriz. Daha sonra `f`'i `do_twice`'ın gövdesinden çağırabiliriz. `main` içinde, 
`add_one` fonksiyon adını `do_twice`'a ilk argüman olarak aktarabiliriz.

Kapanışların aksine, `fn` bir özellikten ziyade bir türdür, bu nedenle `Fn` tanımlarından birine sahip yaygın tür parametresini bir 
tanım bağı olarak bildirmek yerine `fn`'yi doğrudan parametre türü olarak belirtiriz.

Fonksiyon işaretçileri, kapanış tanımlarının (`Fn`, `FnMut` ve `FnOnce`) üçünü de uygular, yani bir kapanış bekleyen bir fonksiyona argüman 
olarak her zaman bir fonksiyon işaretçisi aktarabilirsiniz. Fonksiyonlarınızı yaygın tür ve kapanış tanımlarından birini kullanarak yazmak en iyisidir,
böylece fonksiyonlarınız hem fonksiyonları hem de kapanışları kabul edebilir.

Bununla birlikte, kapanışları değil de yalnızca `fn`'leri kabul etmek isteyeceğiniz bir örnek, kapanışları olmayan harici kodlarla 
arayüz oluştururken ortaya çıkar: C fonksiyonları argüman olarak fonksiyon kabul edebilir, ancak C'de kapanış yoktur.

Satır içi tanımlanmış bir kapanış ya da adlandırılmış bir fonksiyon kullanabileceğiniz bir örnek olarak, standart kütüphanedeki `Iterator` tanımı
tarafından sağlanan `map` metodunun kullanımına bakalım. Sayılardan oluşan bir vektörü dizelerden oluşan bir vektöre dönüştürmek üzere `map` 
fonksiyonunu kullanmak için aşağıdaki gibi bir kapanış kullanabiliriz:

```rust
{{#rustdoc_include ../listings/ch19-advanced-features/no-listing-15-map-closure/src/main.rs:here}}
```

Veya kapanış yerine `map` argümanı olarak bir fonksiyonu şöyle adlandırabiliriz:

```rust
{{#rustdoc_include ../listings/ch19-advanced-features/no-listing-16-map-function/src/main.rs:here}}
```

Daha önce [“Gelişmiş Özellikler”][advanced-traits]<!-- ignore --> bölümünde bahsettiğimiz tam nitelikli söz dizimini kullanmamız 
gerektiğini unutmayın çünkü `to_string` adında birden fazla fonksiyon mevcuttur. Burada, standart kütüphanenin `Display`'i sürekleyen 
tüm türler için süreklediği `ToString` tanımında tanımlanan `to_string` fonksiyonunu kullanıyoruz.

Bölüm 6'daki [“`enum` değerleri”][enum-values]<!-- ignore --> bölümünde tanımladığımız her `enum` varyantının adının aynı 
zamanda bir başlatıcı fonksiyon olduğunu hatırlayın. Bu başlatıcı fonksiyonları, kapanış tanımlarını sürekleyen fonksiyon işaretçileri 
olarak kullanabiliriz; yani başlatıcı fonksiyonları, kapanışları alan metodlar için argüman olarak belirtebiliriz:

```rust
{{#rustdoc_include ../listings/ch19-advanced-features/no-listing-17-map-initializer/src/main.rs:here}}
```

Burada, `Status::Value`'nun başlatıcı fonksiyonunu kullanarak `map`'in çağrıldığı aralıktaki her `u32` değerini kullanarak 
`Status::Value` örnekleri oluşturuyoruz. Bazı insanlar bu stili tercih ederken bazıları da kapanışları kullanmayı tercih eder. 
Her ikisi de aynı koda derlenir, bu nedenle hangi stil sizin için daha iyiyse onu kullanın.

### Dönen Kapanışlar

Kapanışlar tanımlar tarafından temsil edilir, bu da kapanışları doğrudan iade edemeyeceğiniz anlamına gelir. 
Bir tanım döndürmek isteyebileceğiniz çoğu durumda, bunun yerine fonksiyonun dönüş değeri olarak `trait`'i sürekleyen somut tipi kullanabilirsiniz.
Ancak, kapanışlarda bunu yapamazsınız çünkü döndürülebilir somut bir tipleri yoktur; örneğin, `fn` fonksiyon işaretçisini bir dönüş 
tipi olarak kullanmanıza izin verilmez.

Aşağıdaki kod doğrudan bir kapanış döndürmeye çalışır, ancak derlenmez:

Here we create `Status::Value` instances using each `u32` value in the range
that `map` is called on by using the initializer function of `Status::Value`.
Some people prefer this style, and some people prefer to use closures. They
compile to the same code, so use whichever style is clearer to you.

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch19-advanced-features/no-listing-18-returns-closure/src/lib.rs}}
```

Derleyici hatası aşağıdaki gibidir:

```console
{{#include ../listings/ch19-advanced-features/no-listing-18-returns-closure/output.txt}}
```

Hata yine `Sized` tanımına atıfta bulunuyor! Rust, kapanışı depolamak için ne kadar alana ihtiyaç duyacağını bilmiyor. 
Bu sorunun çözümünü daha önce görmüştük. Bir `trait` nesnesi kullanabiliriz:

```rust,noplayground
{{#rustdoc_include ../listings/ch19-advanced-features/no-listing-19-returns-closure-trait-object/src/lib.rs}}
```

Bu kod sorunsuz derlenecektir. `trait` nesneleri hakkında daha fazla bilgi edinmek için 
Bölüm 17'deki  [“Farklı Türlerdeki Değerlere
İzin Veren `trait` Nesnelerini Kullanma”][using-trait-objects-that-allow-for-values-of-different-types]<!--
ignore --> başlığına bakabilirsiniz.

Şimdi devam edelim ve makrolara bakalım!

[advanced-traits]:
ch19-03-advanced-traits.html#advanced-traits
[enum-values]: ch06-01-defining-an-enum.html#enum-values
[using-trait-objects-that-allow-for-values-of-different-types]:
ch17-02-trait-objects.html#using-trait-objects-that-allow-for-values-of-different-types
