# Yaygın Türler, Tanımlar ve Ömürler

Her programlama dili, kavramların tekrarını etkin bir şekilde ele almak için araçlara sahiptir. 
Rust'ta bu araç *yaygınlardır*. 
Kodu derlerken ve çalıştırırken yerlerinde ne olacağını bilmeden yaygınların davranışını veya diğer yaygınlarla 
nasıl ilişkili olduklarını ifade edebiliriz.

Fonksiyonlar, `i32` veya `String` gibi somut bir tür yerine bazı yaygın türdeki parametreleri alabilir; 
aynı şekilde, bir işlevin aynı kodu birden çok somut değerde çalıştırmak için bilinmeyen değerlere sahip 
parametreleri alması gibi. Aslında, Bölüm 6'da `Option<T>`, Bölüm 8'de `Vec<T>` ve `HashMap<K, V>` ve 
Bölüm 9'da `Result<T, E>` ile yaygınları zaten kullandık. 
Bu bölümde, yaygınları kullanarak kendi türlerinizi, 
fonksiyonlarınızı ve metodlarınızı nasıl tanımlayacabileceğinizi keşfedeceksiniz!

İlk olarak, kod tekrarını azaltmak için bir fonksiyonun nasıl çıkarılacağını inceleyeceğiz. 
Daha sonra, yalnızca parametrelerinin türlerinde farklılık gösteren iki fonksiyondan yaygın bir 
fonksiyon yapmak için aynı tekniği kullanacağız. 
Ayrıca `struct` ve `enum` tanımlarında yaygın türlerin nasıl kullanılacağını açıklayacağız.

Ardından, davranışı yaygın bir şekilde tanımlamak için *tanımları* nasıl kullanacağınızı öğreneceksiniz. 
Yaygın bir türü, herhangi bir türün aksine, yalnızca belirli bir davranışı olan türleri kabul edecek şekilde sınırlamak için tanımları genel türlerle birleştirebilirsiniz.

Son olarak, derleyiciye referansların birbirleriyle nasıl ilişkili olduğu hakkında bilgi veren çeşitli yaygın türleri olan *yaşam sürelerini* tartışacağız. Ömürler, derleyiciye ödünç alınan değerler hakkında yeterli bilgi vermemize izin verir, 
böylece referansların bizim yardımımız olmadan yapabileceğinden daha fazla durumda geçerli olmasını sağlayabilir.

## Bir Fonksiyonu Ayıklayarak Fazlalığı Atmak

Yaygınlar, kod çoğaltmasını kaldırmak için belirli türleri birden çok türü temsil eden bir yer tutucuyla 
değiştirmemize olanak tanır. Yaygınların söz dizimine dalmadan önce, 
belirli değerleri birden çok değeri temsil eden bir yer tutucuyla değiştiren bir fonksiyonu çıkararak, 
yaygın türleri içermeyen bir şekilde çoğaltmanın nasıl ayıklanabileceğine bakalım. 
Ardından, yaygın bir fonksiyonu ayıklamak için aynı tekniği uygulayacağız! 
Bir fonksiyondan çıkarabileceğiniz fazlalık kodu nasıl tanıyacağınıza bakarak, 
yaygınları kullanabilen fazlalık kodu tanımaya başlayacaksınız.

Liste 10-1'deki listedeki en büyük sayıyı bulan kısa programla başlıyoruz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-01/src/main.rs:here}}
```

<span class="caption">Liste 10-1: Sayı listesindeki en büyük sayıyı bulma</span>

`number_list` değişkeninde bir tam sayı listesi saklarız ve listedeki ilk sayıyı 
`largest` adlı bir değişkene atarız. Daha sonra listedeki tüm sayılar arasında yineleniriz ve 
mevcut sayı `largest`'ta saklanan sayıdan büyükse, o değişkendeki sayıyı değiştiririz. 
Ancak, mevcut sayı o ana kadar görülen en büyük sayıdan küçük veya ona eşitse, 
değişken değişmez ve kod listedeki bir sonraki sayıya geçer. 
Listedeki tüm sayıları göz önünde bulundurduktan sonra, `largest`, bu durumda 100 olan en büyük sayıyı tutmalıdır.

Şimdi iki farklı sayı listesindeki en büyük sayıyı bulmakla görevlendirildiğimize göre, 
bunu yapmak için Liste 10-1'deki kodu çoğaltmayı seçebilir ve Liste 10-2'de gösterildiği gibi programdaki
iki farklı yerde aynı mantığı kullanabiliriz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-02/src/main.rs}}
```

<span class="caption">Liste 10-2: *İki* sayı listesindeki en büyük sayıyı bulmak için kullanılabilecek kod</span>

Bu kod çalışsa da, kodu kopyalamak sıkıcı ve hataya açık. 
Ayrıca kodu değiştirmek istediğimizde birden çok yeri de güncellemeyi unutmamalıyız.

Bu tekrarı ortadan kaldırmak için, bir parametrede geçirilen herhangi bir tam sayı listesinde çalışan bir fonksiyon tanımlayarak bir soyutlama oluşturacağız. Bu çözüm, kodumuzu daha net hale getirir ve bir listedeki en büyük sayıyı bulma kavramını soyut olarak ifade etmemizi sağlar.

Liste 10-3'te, en büyük sayıyı bulan kodu `largest` adlı bir fonksiyona çıkarıyoruz. 
Ardından, Liste 10-2'deki iki listedeki en büyük sayıyı bulmak için bu fonksiyonu çağırırız. 
Bu fonksiyonu, gelecekte diğer `i32` değerleri listesinde de kullanabiliriz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch10-generic-types-traits-and-lifetimes/listing-10-03/src/main.rs:here}}
```

<span class="caption">Liste 10-3: İki listedeki en büyük sayıyı bulmak için kullanılabilecek soyut kod</span>

`largest` fonksiyonunun, fonksiyona aktarabileceğimiz herhangi bir somut `i32` değer
dilimini temsil eden `list` adında bir parametresi vardır. Sonuç olarak, fonksiyonu çağırdığımızda kod, 
ilettiğimiz belirli değerler üzerinde çalışır. Şimdilik `for` döngüsünün söz dizimini dert etmeyin. 
Burada bir `i32` referansına herhangi bir atıfta bulunmuyoruz; `for` döngüsünün aldığı her `&i32`'yi 
modelle eşleştiriyor ve yok ediyoruz, böylece `item` döngü gövdesi içinde olduğu sürece türü `i32` olacaktır. 
Model eşleştirmeyi [Bölüm 18][ch18]<!-- ignore -->'de daha ayrıntılı olarak ele alacağız.

Özetle, kodu Liste 10-2'den Liste 10-3'e değiştirmek için attığımız adımlar şunlardır:

1. Yinelenen kodu tanımlamak.
2. Yinelenen kodu fonksiyonun gövdesine çıkarmak 
   ve bu kodun girdilerini ve dönüş değerlerini fonksiyon tanımında belirtmek.
3. Fonksiyonu çağırmak için yinelenen kodun iki örneğini de güncellemek.

Daha sonra, kod tekrarını azaltmak için aynı adımları yaygınlarla birlikte kullanacağız. 

Fonksiyon gövdesinin belirli değerler yerine soyut bir liste üzerinde çalışabilmesi gibi, 
yaygınlar da kodun soyut türler üzerinde çalışmasına izin verir.

Örneğin, iki fonksiyonumuz olduğunu varsayalım: 
biri `i32` değerleri dilimindeki en büyük öğeyi bulan ve diğeri bir `char` değerleri dilimindeki en büyük öğeyi bulan. 
Bu tekrarı nasıl ortadan kaldıracağız? 

Hadi bulalım!

[ch18]: ch18-00-patterns.html
