## Yapıları Tanımlama ve Örnekleme

Yapılar, her ikisinin de birden çok ilişkili değeri içermesi bakımından [“Demet Türü”][tuples]<!--
ignore --> bölümünde tartışılan demetlere benzer. Demetler gibi, bir yapının parçaları farklı türleri olabilir. 
Demetlerden farklı olarak, bir yapı içinde, değerlerin ne anlama geldiğini netleştirmek için her bir veri parçasını adlandıracaksınız. 
Bu adların eklenmesi, yapıların tanımlama gruplarından daha esnek olduğu anlamına gelir: bir örneğin değerlerini belirtmek veya bunlara erişmek için verilerin sırasına güvenmeniz gerekmez.

Bir yapı tanımlamak için, `struct` anahtar sözcüğünü girer ve tüm yapıyı adlandırırız. Bir yapının adı, birlikte gruplandırılan veri parçalarının önemini açıklamalıdır. Ardından süslü parantezler içinde *üye* dediğimiz veri parçalarının adlarını ve türlerini tanımlarız. 
Örneğin, Liste 5-1, bir kullanıcı hesabı hakkında bilgi depolayan bir yapı gösterir.

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-01/src/main.rs:here}}
```

<span class="caption">Liste 5-1: Bir `User` yapı tanımı</span>

Bir yapıyı tanımladıktan sonra kullanmak için, üyelerin her biri için somut değerler belirterek o yapının bir *örneğini* yaratırız. 
Yapının adını belirterek bir örnek oluşturuyoruz ve ardından anahtarların üye adları olduğu ve değerlerin bu üyelerde depolamak istediğimiz veriler olduğu anahtar: değer çiftlerini içeren süslü parantezleri ekliyoruz. 
Üyeleri `struct` içinde belirttiğimiz sırayla belirtmemize gerek yok. 
Başka bir deyişle, `struct` tanımı, tür için genel bir şablon gibidir ve örnekler, 
türün değerlerini oluşturmak için bu şablonu belirli verilerle doldurur. 
Örneğin, Liste 5-2'de gösterildiği gibi belirli bir kullanıcıyı bildirebiliriz.

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-02/src/main.rs:here}}
```

<span class="caption">Liste 5-2: `User` yapısının bir örneğini oluşturma</span>

Bir yapıdan belirli bir değer elde etmek için nokta gösterimini kullanırız. 
Örneğin, bu kullanıcının e-posta adresine erişmek için `user1.email` kullanıyoruz. 
Örnek değişken ise, nokta gösterimini kullanarak ve belirli bir alana atayarak bir değeri değiştirebiliriz. Liste 5-3, 
değiştirilebilir bir `User` örneğinin `email` alanındaki değerin nasıl değiştirileceğini gösterir.

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-03/src/main.rs:here}}
```

<span class="caption">Liste 5-3: Bir `User` örneğinin `email` alanındaki değeri değiştirme</span>

Tüm örneğin değiştirilebilir olması gerektiğini unutmayın; Rust, yalnızca belirli alanları değiştirilebilir olarak 
işaretlememize izin verme. Herhangi bir ifadede olduğu gibi, bu yeni örneği örtük olarak döndürmek 
için fonksiyon gövdesindeki son ifade olarak yapının yeni bir örneğini oluşturabiliriz.

Liste 5-4, verilen `email` ve `username` ile bir `User` örneği döndüren bir `build_user` fonksiyonunu gösterir. 
`active` üyesi `true` değerini, `sign_in_count` ise `1` değerini alır.

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-04/src/main.rs:here}}
```

<span class="caption">Liste 5-4: Bir e-posta ve kullanıcı adı alan ve bir `User` 
örneği döndüren bir `build_user` fonksiyonu</span>

Fonksiyon parametrelerini yapı üyeleriyle aynı adla adlandırmak mantıklıdır, 
ancak `email` ve `username` üye adlarını ve değişkenlerini tekrarlamak biraz sıkıcıdır. 
Yapının daha fazla üyesi olsaydı, her adı tekrarlamak daha da can sıkıcı olurdu. 

Neyse ki, uygun bir kısayol var!

<a id="using-the-field-init-shorthand-when-variables-and-fields-have-the-same-name"></a>
### Üye Başlatıcı Kısayolu Kullanma

Parametre adları ve yapı üye adları Liste 5-4'te tamamen aynı olduğundan, 
`build_user`'ı yeniden yazmak için üye başlatıcı kısayolu söz dizimini kullanabiliriz, 
böylece tam olarak aynı şeyi elde ederiz ve `email` ve `username` tekrarı olmaz. Liste 5-5'te gösterilmiştir.

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-05/src/main.rs:here}}
```

<span class="caption">Liste 5-5: `email` ve `username` parametreleri yapı üyeleriyle 
aynı ada sahip olduğundan üye başlatıcı kısayolunu kullanan `build_user` fonksiyonu</span>

Burada, `email` adlı bir üyeye sahip olan `User` yapısının yeni bir örneğini oluşturuyoruz. 
`email` üyesinin değerini `build_user` fonksiyonunun `email` parametresindeki değere atamak istiyoruz. 
`email` üyesi ve `email` parametresi aynı ada sahip olduğundan, `email: email` yerine sadece `email` yazmamız gerekiyor.

### Yapı Güncelleme Söz Dizimi ile Diğer Örneklerden Örnekler Oluşturma

Başka bir örnekteki değerlerin çoğunu içeren ancak bazılarını değiştiren bir yapının 
yeni bir örneğini oluşturmak genellikle yararlıdır. Bunu *yapı güncelleme söz dizimini* kullanarak yapabilirsiniz.

İlk olarak, Liste 5-6'da, güncelleme söz dizimi olmadan `user2`'de düzenli olarak yeni bir `User` örneğinin nasıl oluşturulacağını 
gösteriyoruz. `email` için yeni bir değer belirledik, ancak bunun dışında `user1`'den Liste 5-2'de oluşturduğumuz aynı değerleri kullanıyoruz

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-06/src/main.rs:here}}
```

<span class="caption">Liste 5-6: `user1`'deki değerlerden birini kullanarak yeni bir `User` 
örneği oluşturma</span>

*Yapı güncelleme söz dizimini* kullanarak, Liste 5-7'de gösterildiği gibi aynı etkiyi daha az kodla elde edebiliriz. 
`..` söz dizimi, açıkça ayarlanmayan kalan üyelerin verilen örnekteki üyelerle aynı değere sahip olması gerektiğini belirtir.

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/listing-05-07/src/main.rs:here}}
```

<span class="caption">Liste 5-7: Bir `User` örneği için yeni bir `email` değeri ayarlamak için *yapı güncelleme söz dizimini* 
`user1`'den kalan değerlerle kullanmak.</span>

Liste 5-7'deki kod ayrıca `user2`'de `email` için farklı bir değere sahip olan ancak `user1`'den `username`, `active` ve `sign_in_count` üyeleri için aynı değerlere sahip bir örnek oluşturur. 
`..user1`'de kalan üyelerin değerlerini `user1`'deki karşılık gelen üyelerden alması gerektiğini belirtmek için en son gelmelidir, 
ancak üyelerin sırasına bakılmaksızın herhangi bir sırayla istediğimiz kadar üye için değer belirtmeyi seçebiliriz.

Yapı güncelleme söz diziminin bir atama gibi `=` kullandığını unutmayın; bunun nedeni, tıpkı [“Değişkenlerin ve Veri Etkileşiminin Yolları: 
Hareket Ettirme”][move]<!-- ignore --> bölümünde gördüğümüz gibi verileri hareket ettirmesidir. 
Bu örnekte, `user1`'in `username` üyesindeki `String`, `user2`'ye taşındığından, `user2` oluşturulduktan sonra artık 
`user1`'i kullanamayız. `user2`'ye hem `email` hem de `username` için yeni `String` değerleri vermiş olsaydık ve 
bu nedenle yalnızca `user1`'den `active` ve `sign_in_count` değerlerini kullansaydık, o zaman `user1`, `user2` oluşturulduktan sonra da 
geçerli olurdu. `active` ve `sign_in_count` türleri, `Copy` tanımını uygulayan türlerdir, bu nedenle 
[“Sadece Yığıtı Kullanan Tür: Copy”][copy]<!-- ignore --> bölümünde tartıştığımız davranış geçerli olur.

### Farklı Türler Oluşturmak için Adlandırılmış Alanlar Olmadan Demet Yapılarını Kullanma

Rust ayrıca, *demet yapıları* adı verilen demetlere benzeyen yapıları da destekler. 
Demet yapıları, yapı adının sağladığı ek anlama sahiptir, ancak alanlarıyla ilişkilendirilmiş adları yoktur; 
daha ziyade, sadece alanların türlerine sahiptirler. Demet yapıları, tüm demete bir ad vermek ve demeti diğer demetlerden farklı bir tür yapmak istediğinizde ve her alanı normal bir yapıdaki gibi adlandırmak ayrıntılı veya gereksiz olduğunda yararlıdır.

Bir demet yapısı tanımlamak için, `struct` anahtar sözcüğü ve yapı adıyla başlayın ve ardından demetteki türleri takip edin. 
Örneğin, burada `Color` ve `Point` adında iki demet yapısı tanımlıyor ve kullanıyoruz:

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/no-listing-01-tuple-structs/src/main.rs}}
```

`black` ve `origin` değerlerinin farklı türler olduğuna dikkat edin, çünkü bunlar farklı demet yapılarının örnekleridir. 
Tanımladığınız her yapı, yapı içindeki üyeler aynı türlere sahip olsa bile kendine özgü türe sahiptir. Örneğin, `Color` türünde bir parametre 
alan bir fonksiyon, her iki tür de üç `i32` değerinden oluşsa bile argüman olarak bir `Point` alamaz.

### Üyesiz Birim Benzeri Yapılar

Ayrıca üyesi olmayan yapılar da tanımlayabilirsiniz! Bunlara *birim benzeri yapılar* denir, çünkü [“Demet Türü”][tuples]<!-- ignore --> bölümünde 
bahsettiğimiz birim tipine `()` benzer şekilde davranırlar. Birim benzeri yapılar, bir tür üzerinde bir özellik uygulamanız gerektiğinde ancak türün kendisinde depolamak istediğiniz herhangi bir veriniz olmadığında faydalı olabilir. Nitelikleri Bölüm 10'da tartışacağız. 
İşte `AlwaysEqual` adlı bir birim yapısının bildirilmesine ve somutlaştırılmasına bir örnek:

```rust
{{#rustdoc_include ../listings/ch05-using-structs-to-structure-related-data/no-listing-04-unit-like-structs/src/main.rs}}
```

`AlwaysEqual`'ı tanımlamak için `struct` anahtar sözcüğünü, istediğimiz adı ve ardından noktalı virgül kullanırız. 
Süslü parantezlere veya parantezlere gerek yok! Daha sonra, benzer bir şekilde konu değişkeninde `AlwaysEqual` örneğini alabiliriz: 
tanımladığımız adı kullanarak, herhangi bir süslü parantez kullanmadan. Daha sonra, her `AlwaysEqual` örneğinin her zaman diğer herhangi bir türün her örneğine eşit olduğu, belki de test amacıyla bilinen bir sonuca sahip olacak şekilde bu tür için davranış uygulayacağımızı hayal edin. Bu davranışı uygulamak için herhangi bir veriye ihtiyacımız olmazdı! Bölüm 10'da özelliklerin nasıl tanımlanacağını ve birim benzeri yapılar da dahil olmak üzere herhangi bir türe nasıl uygulanacağını göreceksiniz.

> ### Yapı Verilerinin Sahipliği
> Liste 5-1'deki `User` yapısı tanımında, `&str` dizgi dilim tipi yerine sahip olunan `String` tipini kullandık. 
> Bu bilinçli bir seçimdir çünkü bu yapının her örneğinin tüm verilerine sahip olmasını ve 
> bu verilerin tüm yapı geçerli olduğu sürece geçerli olmasını istiyoruz.
> 
> Yapıların başka bir şeye ait verilere başvuruları depolaması da mümkündür, ancak bunu yapmak için yaşam sürelerinin kullanılması gerekir; 
> bu, Bölüm 10'da tartışacağımız bir Rust özelliğidir. Ömürler, bir yapı tarafından başvurulan verilerin aşağıdakiler için geçerli olmasını 
> sağlar. yapı olduğu sürece. Diyelim ki aşağıdaki gibi yaşam süreleri belirtmeden bir `struct` içinde bir referans depolamaya çalışıyorsunuz; 
> bu işe yaramaz:
>
> <span class="filename">Dosya adı: src/main.rs</span>
>
> <!-- AYRIŞTIRAMIYOR İMİŞ (CAN'T EXTRACT SEE https://github.com/rust-lang/mdBook/issues/1127) -->
>
> ```rust,ignore,does_not_compile
> struct User {
>     active: bool,
>     username: &str,
>     email: &str,
>     sign_in_count: u64,
> }
>
> fn main() {
>     let user1 = User {
>         email: "someone@example.com",
>         username: "someusername123",
>         active: true,
>         sign_in_count: 1,
>     };
> }
> ```
>
> Derleyici, ömürlük belirteçlere ihtiyaç duyduğundan hata verecektir:
>
> ```console
> $ cargo run
>    Compiling structs v0.1.0 (file:///projects/structs)
> error[E0106]: missing lifetime specifier
>  --> src/main.rs:3:15
>   |
> 3 |     username: &str,
>   |               ^ expected named lifetime parameter
>   |
> help: consider introducing a named lifetime parameter
>   |
> 1 ~ struct User<'a> {
> 2 |     active: bool,
> 3 ~     username: &'a str,
>   |
>
> error[E0106]: missing lifetime specifier
>  --> src/main.rs:4:12
>   |
> 4 |     email: &str,
>   |            ^ expected named lifetime parameter
>   |
> help: consider introducing a named lifetime parameter
>   |
> 1 ~ struct User<'a> {
> 2 |     active: bool,
> 3 |     username: &str,
> 4 ~     email: &'a str,
>   |
>
> For more information about this error, try `rustc --explain E0106`.
> error: could not compile `structs` due to 2 previous errors
> ```
> Bölüm 10'da, referansları yapılarda saklayabilmeniz için bu hataların nasıl düzeltileceğini tartışacağız, 
> ancak şimdilik, `&str` gibi referanslar yerine `String` gibi sahip olunan türleri kullanarak bu gibi hataları 
> düzelteceğiz.

<!-- manual-regeneration
for the error above
after running update-rustc.sh:
pbcopy < listings/ch05-using-structs-to-structure-related-data/no-listing-02-reference-in-struct/output.txt
paste above
add `> ` before every line -->

[tuples]: ch03-02-data-types.html#the-tuple-type
[move]: ch04-01-what-is-ownership.html#ways-variables-and-data-interact-move
[copy]: ch04-01-what-is-ownership.html#stack-only-data-copy
