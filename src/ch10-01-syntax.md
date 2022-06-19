## Yaygın Veri Türleri

Fonksiyon imzaları veya yapılar gibi öğeler için tanımlar oluşturmak için yaygınları kullanırız ve bunları daha sonra birçok farklı 
somut veri türüyle kullanabiliriz. İlk olarak yaygınları kullanarak fonksiyonları, yapıları, `enum`'ları ve metodları nasıl tanımlayacağımıza 
bakalım. Daha sonra yaygınların kod performansını nasıl etkilediğini tartışacağız.

### Fonksiyon Tanımlarında

Yaygın kullanan bir fonksiyon tanımlarken, yaygınları fonksiyonun imzasına, genellikle parametrelerin ve dönüş değerinin 
veri tiplerini belirttiğimiz yere yerleştiririz. Bunu yapmak kodumuzu daha esnek hale getirir ve kod tekrarını önlerken 
fonksiyonumuzu çağıranlara daha fazla işlevsellik sağlar.

`largest` fonksiyonumuzla devam edersek, Liste 10-4'te her ikisi de bir dilimdeki en büyük değeri bulan iki fonksiyon gösterilmektedir. 
Daha sonra bunları yaygın kullanan tek bir fonksiyonda birleştireceğiz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-04/src/main.rs:here}}
```

<span class="caption">Liste 10-4: Yalnızca adlarında ve imzalarındaki türlerde 
farklılık gösteren iki fonksiyon</span>

`largest_i32` fonksiyonu, bir dilimdeki en büyük `i32`'yi bulan Liste 10-3'te çıkardığımız fonksiyondur. 
`largest_char` fonksiyonu bir dilimdeki en büyük `char` değerini bulur. Fonksiyon gövdeleri aynı koda sahiptir, 
bu nedenle tek bir fonksiyona yaygın tür parametresi ekleyerek yinelemeyi ortadan kaldıralım.

Yeni bir tek fonksiyonda türleri parametrelendirmek için, tıpkı bir fonksiyonun değer parametreleri için yaptığımız gibi tür 
parametresini adlandırmamız gerekir. Tür parametresi adı olarak herhangi bir tanımlayıcı kullanabilirsiniz. 
Ancak biz `T` kullanacağız çünkü Rust'ta parametre adları genellikle sadece bir harf olmak üzere kısadır ve Rust'ın tür adlandırma kuralı 
`CamelCase`'dir. “tür, type” kelimesinin kısaltması olan `T`, çoğu Rust programcısının varsayılan tercihidir.

Fonksiyonun gövdesinde bir parametre kullandığımızda, parametre adını imzada bildirmemiz gerekir, böylece derleyici bu adın ne anlama geldiğini 
bilir. Benzer şekilde, bir fonksiyon imzasında bir tür parametre adı kullandığımızda, kullanmadan önce tür parametre adını bildirmemiz gerekir. 
Yaygın `largest` fonksiyonunu tanımlamak için, tür adı bildirimlerini fonksiyonun adı ile parametre listesi arasına köşeli parantezler 
(`<>`) içinde yerleştirin, aşağıdaki gibi:

```rust,ignore
fn largest<T>(list: &[T]) -> T {
```

Bu tanımı şu şekilde okuyabiliriz: `largest` fonksiyonu bazı `T` türleri üzerinde yaygındır. Bu fonksiyonun `list` adında bir 
parametresi vardır ve bu parametre `T` türünde bir değer dilimidir. `largest` fonksiyonu aynı `T` türünde bir değer döndürecektir.

Liste 10-5, imzasında yaygın veri tipini kullanan birleşik `largest` fonksiyon tanımını gösterir. 
Liste ayrıca, fonksiyonu `i32` değerlerinden oluşan bir dilim ya da `char` değerleriyle nasıl çağırabileceğimizi de gösterir. 
Bu kodun henüz derlenmeyeceğini unutmayın, ancak bu bölümün ilerleyen kısımlarında bunu düzelteceğiz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-05/src/main.rs}}
```

<span class="caption">Liste 10-5: Yaygın tür parametreleri kullanan `largest` fonksiyonu; 
bu henüz derlenmiyor</span>

Kodu şimdi derlemeye çalışırsak, şu hatayı alırız:

```console
{{#include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-05/output.txt}}
```

Notta bir tanım olan `std::cmp::PartialOrd`'dan bahsedilmektedir. Tanımlar hakkında bir sonraki bölümde konuşacağız. 
Şimdilik, bu hatanın `largest`'in gövdesinin `T`'nin olabileceği tüm olası türler için çalışmayacağını belirttiğini bilin. 
Gövdede `T` türündeki değerleri karşılaştırmak istediğimiz için, yalnızca değerleri sıralanabilen türleri kullanabiliriz. 
Karşılaştırmaları etkinleştirmek için, standart kütüphanede türler üzerinde uygulayabileceğiniz `std::cmp::PartialOrd` tanımı vardır 
(bu tanım hakkında daha fazla bilgi için Ekleme C'ye bakın). Yaygın bir türün belirli bir tanıma sahip olduğunu nasıl belirteceğinizi 
[“Parametre Olarak Tanımlar”][traits-as-parameters]<!-- ignore --> bölümünde öğreneceksiniz. 
Bu kodu düzeltmeden önce ([“Tanım Sınırları ile Fonksiyonu Düzeltme”][fixing]<!-- ignore --> bölümünde), yaygın tür parametrelerini 
kullanmanın diğer yollarını inceleyelim.

### Struct Tanımlarında

Ayrıca, `<>` söz dizimini kullanarak bir veya daha fazla alanda yaygın tür parametresi kullanmak için 
`struct`'ları tanımlayabiliriz. Liste 10-6, herhangi bir türdeki `x` ve `y` koordinat değerlerini tutmak için bir 
`Point<T>` `struct`'ı tanımlar.
    
<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-06/src/main.rs}}
```

<span class="caption">Liste 10-6: `T` türünde `x` ve `y` değerlerini tutan bir `Point<T` 
yapısı</span>

Yapı tanımlarında yaygın türlerin kullanımı için söz dizimi, fonksiyon tanımlarında kullanılan söz dizimine benzer. 
İlk olarak, `struct` adından hemen sonra köşeli parantezler içinde tür parametresinin adını bildiririz. 
Ardından, `struct` tanımında somut veri türlerini belirteceğimiz yerde yaygın türü kullanırız.

`Point<T>`'yi tanımlamak için yalnızca bir yaygın tür kullandığımızdan, bu tanımın `Point<T>` yapısının bazı `T` türleri üzerinde 
yaygın olduğunu ve `x` ve `y` üyelerinin her ikisinin de, bu tür ne olursa olsun, aynı tür olduğunu söylediğine dikkat edin. 
Liste 10-7'de olduğu gibi, farklı türlerde değerlere sahip bir `Point<T>` tanımı oluşturursak, kodumuz derlenmeyecektir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-07/src/main.rs}}
```

<span class="caption">Liste 10-7: Her ikisi de aynı genel veri türü `T`'ye sahip olduğundan, 
`x` ve `y` üyeleri aynı türde olmalıdır.</span>

Bu örnekte, `x`'e `5` tam sayı değerini atadığımızda, derleyiciye `T` yaygın türünün bu `Point<T>` tanımı için bir tam sayı olacağını bildiririz. 
Daha sonra, `x` ile aynı türe sahip olacak şekilde tanımladığımız `y` için `4.0` değerini belirttiğimizde, aşağıdaki gibi tür 
uyuşmazlığı hatası alırız:

```console
{{#include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-07/output.txt}}
```

`x` ve `y`'nin her ikisinin de yaygın olduğu ancak farklı türlere sahip olabileceği bir `Point` yapısını tanımlamak için birden fazla 
yaygın tür parametresi kullanabiliriz. Örneğin, Liste 10-8'de, `Point` tanımını `T` ve `U` türleri üzerinde yaygın olacak şekilde 
değiştiririz; burada `x` `T` tipinde ve `y` `U` tipindedir.
 
    
<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-08/src/main.rs}}
```

<span class="caption">Liste 10-8: İki tür üzerinde yaygın bir `Point<T, U>`, böylece `x` ve `y` farklı türlerin 
değerleri olabilir</span>

Artık gösterilen tüm `Point` tanımlarına izin verilmektedir! Bir tanımda istediğiniz kadar yaygın tür parametresi kullanabilirsiniz, 
ancak birkaç taneden fazla kullanmak kodunuzun okunmasını zorlaştırır. Kodunuzda çok sayıda yaygın türe ihtiyaç duyuyorsanız, 
bu kodunuzun daha küçük parçalar halinde yeniden yapılandırılması gerektiğini gösterebilir.

### `enum` Tanımlarında
    
Yapılarda yaptığımız gibi, yaygın veri türlerini varyantlarında tutmak için `enum`'ları tanımlayabiliriz. 
Standart kütüphanenin sağladığı ve Bölüm 6'da kullandığımız `Option<T>` `enum`'una bir kez daha göz atalım:
    
```rust
enum Option<T> {
    Some(T),
    None,
}
```

Bu tanım şimdi size daha anlamlı gelecektir. Gördüğünüz gibi `Option<T>` `enum`'u `T` türü üzerinde yaygındır ve iki çeşidi vardır: 
`T` türünde bir değer tutan `Some` ve herhangi bir değer tutmayan `None` varyantı. `Option<T>` `enum`'unu kullanarak, 
isteğe bağlı bir değerin soyut kavramını ifade edebiliriz ve `Option<T>` yaygın olduğu için, isteğe bağlı değerin türü ne olursa 
olsun bu soyutlamayı kullanabiliriz.

`enum`'lar birden fazla yaygın tür de kullanabilir. Bölüm 9'da kullandığımız `Result` `enum` tanımı buna bir örnektir:
   

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

`Result` `enum`'u, `T` ve `E` olmak üzere iki tür üzerinde yaygındır ve iki çeşidi vardır: `T` türünde bir değer tutan `Ok` ve 
`E` türünde bir değer tutan `Err`. Bu tanım, başarılı (`T` türünde bir değer döndüren) veya başarısız (`E` türünde bir hata döndüren) 
olabilecek bir işlemimiz olan her yerde `Result` `enum`'unu kullanmayı kolaylaştırır. Aslında, Liste 9-3'te bir dosyayı açmak 
için kullandığımız şey buydu; dosya başarıyla açıldığında `T`, `std::fs::File` türüyle atandı ve dosyanın açılmasında sorun olduğunda 
`E`, `std::io::Error` türüyle atandı.

Kodunuzda, yalnızca tuttukları değerlerin türlerinde farklılık gösteren birden fazla `struct` veya `enum` tanımının bulunduğu 
durumları fark ettiğinizde, bunun yerine yaygın türleri kullanarak yinelemeyi önleyebilirsiniz.

### Metod Tanımlarında
    
Yapılar ve `enum`'lar üzerinde metodlar uygulayabilir (Bölüm 5'te yaptığımız gibi) ve tanımlarında yaygın türleri kullanabiliriz. 
Liste 10-9, Liste 10-6'da tanımladığımız `Point<T>` yapısını ve üzerinde uygulanan `x` adlı bir metodu göstermektedir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-09/src/main.rs}}
```

<span class="caption">Liste 10-9: `T` türündeki `x` üyesine bir başvuru döndürecek olan `Point<T>` yapısında 
`x` adlı metodun süreklenmesi</span>

Burada, `x` üyesindeki verilere bir referans döndüren `Point<T>` üzerinde `x` adında bir metod tanımladık.

`T`'yi `impl`'den hemen sonra bildirmemiz gerektiğine dikkat edin, böylece `T`'yi `Point<T>` türünde metodlar tanımladığımızı belirtmek için
kullanabiliriz. `T`'yi `impl`'den sonra yaygın bir tür olarak bildirerek, Rust, `Point`'teki köşeli parantez içindeki türün somut bir tür 
yerine yaygın bir tür olduğunu belirleyebilir. Bu yaygın parametre için `struct` tanımında bildirilen yaygın parametreden farklı bir 
isim seçebilirdik, ancak aynı ismi kullanmak gelenekseldir. Yaygın türü bildiren bir `impl` içinde yazılan metodlar, 
yaygın türün yerine hangi somut tür geçerse geçsin, türün herhangi bir tanımı üzerinde tanımlanacaktır.

Tür üzerinde metod tanımlarken yaygın türler üzerinde kısıtlamalar da belirtebiliriz. 
Örneğin, herhangi bir yaygın türe sahip `Point<T>` tanımları yerine yalnızca `Point<f32>` tanımları üzerinde metodlar uygulayabiliriz. 
Liste 10-10'da somut `f32` türünü kullanıyoruz, yani `impl`'den sonra herhangi bir tür bildirmiyoruz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-10/src/main.rs:here}}
```

<span class="caption">Liste 10-10: Yaygın tür parametresi `T` için yalnızca belirli bir somut türe sahip bir 
yapıya tanımlanan bir `impl` bloğu</span>

Bu kod, `Point<f32>` türünün bir `distance_from_origin` metoduna sahip olacağı anlamına gelir; 
`T`'nin `f32` türünde olmadığı diğer `Point<T>` örneklerinde bu metod tanımlı olmayacaktır. Metod, noktamızın `(0.0, 0.0)` 
koordinatlarındaki noktadan ne kadar uzakta olduğunu ölçer ve yalnızca kayan nokta türleri için kullanılabilen matematiksel işlemleri kullanır.

Bir `struct` tanımındaki yaygın tür parametreleri her zaman aynı `struct`'ın metod imzalarında kullandıklarınızla aynı değildir. 
Liste 10-11, örneği daha açık hale getirmek için `Point` `struct`'ı için `X1` ve `Y1` yaygın türlerini ve `mixup` metod imzası için `X2` `Y2`'yi
kullanır. Metod, kendi `Point`'inden (`X1` türünde) alınan `x` değeri ve aktarılan `Point`'ten (`Y2` türünde) alınan `y` değeriyle yeni bir 
`Point` tanımı oluşturur.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-11/src/main.rs}}
```

<span class="caption">Liste 10-11: Yapısının tanımından farklı yaygın türleri 
kullanan bir metod</span>

`main`'de, `x` için bir `i32` (değeri `5`) ve `y` için bir `f64` (değeri `10,4`) olan bir `Point` tanımladık. `p2` değişkeni, 
`x` için bir dizgi dilimine (`"Hello"` değeriyle) ve `y` için bir `char` değerine (`c` değeriyle) sahip bir `Point` `struct`'tır. 
`p1` üzerinde `p2` argümanıyla `mixup` çağrıldığında, `x` `p1`'den geldiği için `x` için bir `i32`'ye sahip olan `p3` elde edilir. 
`p3` değişkeninde `y` için bir `char` olacaktır, çünkü `y` `p2`'den gelmiştir. `println!` makro çağrısı `p3.x = 5, p3.y = c` yazdıracaktır.

Bu örneğin amacı, bazı yaygın parametrelerin `impl` ile bildirildiği ve bazılarının metod tanımıyla bildirildiği bir durumu göstermektir. 
Burada, `X1` ve `Y1` yaygın parametreleri `impl`'den sonra bildirilir, çünkü bunlar `struct` tanımıyla birlikte tanımlanmıştır. 
`X2` ve `Y2` yaygın parametreleri `fn mixup`'tan sonra bildirilir, çünkü bunlar yalnızca metodla ilgilidir.

### Yaygınları Kullanan Kodun Performansı
    
Yaygın tür parametrelerini kullanırken bir çalışma zamanı maliyeti olup olmadığını merak ediyor olabilirsiniz. 
İyi haber şu ki, yaygın türleri kullanmak çalışmanızı somut tiplere göre daha yavaş hale getirmeyecektir.

Rust bunu, derleme zamanında yaygınları kullanarak kodun *monomorfizasyonunu* gerçekleştirerek başarır. 
*Monomorfizasyon*, derlendiğinde kullanılan somut tiplerin içini doldurarak genel kodu belirli bir koda dönüştürme işlemidir. 
Bu süreçte derleyici, Liste 10-5'teki yaygın fonksiyonu oluşturmak için kullandığımız adımların tersini yapar: 
derleyici, genel kodun çağrıldığı tüm yerlere bakar ve genel kodun çağrıldığı somut türler için kod oluşturur.

Standart kütüphanenin yaygın `Option<T>` `enum`'unu kullanarak bunun nasıl çalıştığına bakalım:

```rust
let integer = Some(5);
let float = Some(5.0);
```

Rust bu kodu derlediğinde, *monomorflaştırma* gerçekleştirir. Bu işlem sırasında, derleyici `Option<T>` tanımlarında kullanılan değerleri 
okur ve iki tür `Option<T>` tanımlar: biri `i32` ve diğeri `f64`. Bu nedenle, `Option<T>`'nin yaygın tanımını `Option_i32` ve 
`Option_f64` olarak genişletir, böylece genel tanımı özel olanlarla değiştirir.

Kodun monomorfize edilmiş versiyonu aşağıdaki gibi görünür:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
enum Option_i32 {
    Some(i32),
    None,
}

enum Option_f64 {
    Some(f64),
    None,
}

fn main() {
    let integer = Option_i32::Some(5);
    let float = Option_f64::Some(5.0);
}
```

Yaygın `Option<T>`, derleyici tarafından oluşturulan özel tanımlarla değiştirilir. 
Rust, yaygın kodu her örnekte türü belirten koda derlediğinden, yaygınları kullanmak için çalışma zamanı maliyeti ödemeyiz. 
Kod çalıştığında, her bir tanımı elle çoğaltmış olsaydık nasıl çalışacaksa öyle çalışır. Monomorfizasyon süreci, 
Rust'ın yaygınlarını çalışma zamanında son derece verimli hale getirir.
    
[traits-as-parameters]: ch10-02-traits.html#traits-as-parameters
[fixing]: ch10-02-traits.html#fixing-the-largest-function-with-trait-bounds
