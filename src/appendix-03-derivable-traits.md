## Ekleme C: Türetilebilir Tanımlar

Kıtabın farklı bölümlerinde yapıya ya da numaralandırılmış yapıya dahil edebileceğiniz `derive` tanımını anlatmıştık.
`derive` tanımı, yazdığınız `derive` süreklemesini sürekleyerek kod üretir.

Bu ekte, `derive` ile kullanabileceğiniz standart kütüphanedeki tüm 
tanımların bir referansını sunuyoruz. Her bölüm şunları kapsar:

* Bu tanımı türeten hangi operatörler ve yöntemler etkinleştirilecek
* `derive` tarafından sağlanan tanımın süreklenmesi ne iş yapar
* Tanımın süreklenmesi, tür için ne anlama gelir
* Tanımı süreklemenize izin verilen ve verilmeyen koşullar
* Tanımı gerektirecek operasyonların örnekleri

`derive` tarafından sağlanandan farklı bir davranış istiyorsanız, 
bunların manuel olarak nasıl sürekleneceğine ilişkin ayrıntılar için [standart kütüphanenin dokümantasyonuna](../std/index.html)<!-- ignore --> bakın. 
Standart kütüphanede tanımlanan özelliklerin geri kalanı `derive` kullanılarak türlerinize uygulanamaz. 
Bu özelliklerin mantıklı varsayılan davranışları yoktur, bu nedenle bunları başarmaya çalıştığınız şey için anlamlı olacak şekilde süreklemek size kalmıştır. Son kullanıcılar için biçimlendirmeyi yöneten `Display`, türetilemeyen bir tanıma örnektir. Her zaman bir türü son kullanıcıya göstermenin uygun yolunu düşünmelisiniz. Bir son kullanıcının türün hangi kısımlarını görmesine izin verilmelidir? Hangi kısımları alakalı bulacaklar? Hangi veri formatı onlar için en uygun olur? Rust derleyicisi bu içgörüye sahip değildir, bu nedenle sizin için uygun varsayılan davranışı sağlayamaz. Bu ekte sağlanan türetilebilir tanımların listesi kapsamlı değildir: kütüphaneler, türetmeyi kendi tanımları için sürekleyebilir ve kullanabileceğiniz özelliklerin listesini gerçekten açık uçlu olarak türetebilir. `derive`'ın süreklemesi, Bölüm 19'un [“Makrolar”][macros]<!-- ignore --> bölümünde ele alınan prosedürel bir makro kullanmayı içerir.

### Programcı Çıktısı için `Debug` Tanımı

`Debug` tanımı `{}`'ın içine `:?` koyarak kullanabileceğiniz hata ayıklama formatlaması için dizgi düzenlemesini aktifleştirir. 

`Debug` tanımı nesnelerin türlerini hata ayıklamanız için yazdırmaya izin verir.

`assert_eq!` makrosunun kullanımında `Debug` tanımını kullanmak gereklidir. Bu makro eğer her iki nesne de birbirine
eşit değilse bir hata göndererek yazılımcıların nerede hata olduğunu görmelerine yardımcı olur.

### Eşitlik Kıyaslamaları için `PartialEq` ve `Eq` Tanımları

`PartialEq` tanımı `==` ve `!=` operatörlerini herhangi bir nesne türünde kullanmanız için gerekli olan tanımdır.

`PartialEq`'ı tanımlamak `eq` metodunu otomatik olarak sürekler. Her ne zaman `PartialEq` yapılarda
tanımlanırsa, her iki nesnenin her alt üyesi eşitse nesnelerin eşitliğine karar verir. Numaralandırılmış
yapılar için her varyant kendi içinde eşittir ve diğer varyantlarla eşitlik söz konusu değildir.

Her iki nesnenin eşitliğinin kontrolü için `assert_eq!` makrosunda `PartialEq`'i tanımlamak gereklidir.

`Eq` tanımı herhangi bir metoda sahip değildir. Asıl amacı verilen türün her değeri için eşitliğinin kontrolüdür.
`Eq` tanımı sadece `PartialEq`'i de tanımlayan tanımlar için geçerlidir. Ayrıca `PartialEq`'i sürekleyebilen her tür
`Eq`'i de sürekleyebileceği anlamına gelmez. Bir örnek olarak, kayan nokta sayı türleri örnek verilebilir. Bu türün süreklemesi
her iki nesnenin de birbirine eşit olmadığı durumlarda sayı-değili (`NaN`) döndürür.

Örneğin her ne zaman `Eq`, `HashMap<K, V>` için gerekirse bize her iki nesnenin de eşit olup olmadığını söyleyebilecek bir 
tanımı belirtmiş olur.

### Sıralama Kıyaslamaları için `PartialOrd` ve `Ord` Tanımları

`PartialOrd` tanımı, sıralama amaçları için türleri kıyaslamaya izin verir. `PartialOrd`'ı sürekleyen bir tür,
`<`, `>`, `<=`, ve `>=` operatörlerini de aynı tür için kullanabilir hale gelir. `PartialOrd` tanımını ancak `PartialEq`'i de
tanımlayan tanımlar için kullanabilirsiniz.

`PartialOrd`'ı tanımlamak ayrıca eğer verilen değerler herhangi bir sıralamaya tabii tutulamadığı durumlarda
`None`, diğer durumlarda `Option<Ordering>` döndüren `partial_cmp` metodunu da sürekler.
Bir örnek olaraktan, `partial_cmp`'ı herhangi bir kayan noktasal sayı ile kullanmak `None` döndürecektir.

Her ne zaman yapılar için tanımlanmışsa, `PartialOrd` her iki nesnenin her üye içindeki her değerini kıyaslar.
Numaralandırılmış yapılar için tanımlandığında, tüm varyantları önceden tanımlanandan sonradan tanımlanana şeklinde sıralar.

`PartialOrd` tanımı bu durumlar için gereklidir; örneğin `rand` kasasındaki `gen_range` metodu, verilen aralıkta
 ve ifadede sözde rastgele bir değer üretir.

`Ord` tanımı ayrıca verilen iki değer arasında doğru bir sıralamanın olup olmadığını da 
size belirtir. `Ord` tanımı, `Option<Ordering>` yerine `Ordering` tanımını döndüren `cmp` metodunu sürekler. `Ordering`
tanımını döndürmesinin asıl sebebi, doğru bir sıralamanın her zaman olabileceği ihtimalindendir.

`Ord` tanımını türlere ancak `PartialOrd` ve `Eq`'i (ayrıca `Eq` de `PartialEq`'i tanımlamayı zorunlu kılar) de sürekleyen tanımlar için uygulayabilirsiniz. Yapılar ve numaralandırılmış yapılar üzerinde uygulandığında `cmp` `partial_cmp`'in `PartialOrd`'u süreklerken
yaptığı gibi aynı yolu uygular.

`BTreeSet<T>`'de veriler tutmanız gerektiğinde `Ord` tanımını kullanmanız gerekir çünkü bu veri yapısı
verileri sıralanmış olarak tutar.

### Değerleri Çoğaltmak için `Clone` ve `Copy` Tanımları

`Clone` tanımı değerin derin bir kopyasını oluşturmanıza izin verir ve bu çoğaltma işlemi *rastgele* kod
çalıştırmayı ve yığın verisi kopyalamasına sebep olabilir.
`Clone` hakkında daha fazla bilgi için [“Değişkenler ve Veri Etkileşiminin Yolları: Clone”][ways-variables-and-data-interact-clone]<!-- ignore --> 
bölümüne göz atabilirsiniz.

`Clone`'i tanımlamak ayrıca tüm türler için kullanılabilecek `clone` metodunu da sürekler.

Örneğin bir dilim üzerinde `to_vec` metodunu çalıştırmak için `Clone`'un tanımlanması gerekir.

`Copy` tanımı yığında tutulan bitleri çoğaltmanıza izin verir. 
`Copy` hakkında daha fazla bilgi için [“Yığın Öncelikli Veri Tanımı: Copy”][stack-only-data-copy]<!-- ignore --> 
bölümüne göz atabilirsiniz.

`Copy` tanımı herhangi bir rastgele kod çağırma konusunda yapıyı istismar etmeyi önlemek için herhangi bir metod tanımlamaz.
Bu yönüyle yazılımcılar bu şekilde veriyi kopyalamanın aşırı hızlı olduğunu düşüneceklerdir.

`Copy`'i tüm yapı üyeleri `Copy`'i tanımlayan yapılar için de kullanabilirsiniz. 
`Copy`'i tanımlayan bir nesne ayrıca `Clone`'u da tanımlamak zorundadır çünkü,
`Copy`'i tanımlayan bir tür önemsiz bir `Clone` tanımlamasına da sahiptir, yani `Copy` ile benzer görevi yapar.

`Copy` tanımı çoğu zaman gerekmez; `Copy`'i tanımlayan türler ayrıca optimizasyonlara da sahiptir yani 
`clone` kullanmamanız kodunuzu daha yalın ve öz yapacaktır.

`Copy` ile mümkün olan her şey `Clone` ile de mümkündür fakat `clone` metodunun kullanılması bazı yerlerde
kodunuzu yavaşlatabilmektedir.

### Değerleri Birleştirmek için `Hash` Tanımı

`Hash` tanımı sabit değerli bir türün örneğini almanıza izin verir.
`Hash`'i tanımlamak ayrıca `hash` metodunu da sürekler.

### Varsayılan Değerler için `Default` Tanımı

`Default` tanımı bir türe varsayılan bir değer atamanıza izin verir. `Default`'u tanımlamak
`default` fonksiyonunu da sürekler.

`Default::default` fonksiyonu çoğunlukla [“Yapı Güncelleme Söz Dizimi ile Diğer Örneklerden Örnekler Oluşturma”][creating-instances-from-other-instances-with-struct-update-syntax]<!-- ignore -->
bölümünde de belirtildiği şekliyle yapı güncelleme söz dizimiyle birlikte kullanılır. Yapının üyelerini
düzenleyebilir ve daha sonra geri kalan üyeler için varsayılan tür değerlerini `..Default::default()` ile atayabilirsiniz.

`Option<T>` örnekleri üzerinde  `unwrap_or_default` metodunu kullanabilmeniz için `Default` tanımınının da tanımlanmış olması
gerekir. Örneğin, `Option<T>` `None` ise , `unwrap_or_default` metodu `Option<T>`'de depolanan `T` türü için `Default::default`'un
sonucunu döndürecektir.

[creating-instances-from-other-instances-with-struct-update-syntax]:
ch05-01-defining-structs.html#creating-instances-from-other-instances-with-struct-update-syntax
[stack-only-data-copy]:
ch04-01-what-is-ownership.html#stack-only-data-copy
[ways-variables-and-data-interact-clone]:
ch04-01-what-is-ownership.html#ways-variables-and-data-interact-clone
[macros]: ch19-06-macros.html#macros
