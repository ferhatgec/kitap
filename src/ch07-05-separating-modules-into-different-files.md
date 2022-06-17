## Modülleri Farklı Dosyalara Ayırma

Şimdiye kadar, bu bölümdeki tüm örnekler tek bir dosyada birden fazla modül tanımladı. 
Modüller büyüdüğünde, kodda gezinmeyi kolaylaştırmak için tanımlarını ayrı bir dosyaya taşımak isteyebilirsiniz.

Örneğin, birden fazla restoran modülüne sahip olan Liste 7-17'deki koddan başlayalım. Tüm modülleri kasa kök dosyasında tanımlamak yerine, 
modülleri dosyalara çıkaracağız. Bu durumda, kasa kök dosyası *src/lib.rs*'dir, ancak bu prosedür, 
kasa kök dosyası *src/main.rs* olan ikili kasalarla da çalışır.

Öncelikle `front_of_house` modülünü kendi dosyasına çıkaracağız. Yalnızca `mod front_of_house`'u bırakarak `front_of_house` modülü için 
süslü parantezlerin içindeki kodu kaldırın; böylece *src/lib.rs* Liste 7-21'de gösterilen kodu içerir. Liste 7-22'de 
*src/front_of_house.rs* dosyasını oluşturana kadar bunun derlenmeyeceğini unutmayın.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-21-and-22/src/lib.rs}}
```

<span class="caption">Liste 7-21: Gövdesi *src/front_of_house.rs* içinde olacak bir `front_of_house` 
modülünün tanımlanması</span>

Ardından, köşeli parantez içindeki kodu Liste 7-22'de gösterildiği gibi *src/front_of_house.rs* adlı yeni bir dosyaya yerleştirin. 
Derleyici bu dosyaya bakması gerektiğini bilir çünkü kasa kökündeki modül bildirimi `front_of_house` adıyla karşılaştı.

<span class="filename">Dosya adı: src/front_of_house.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-21-and-22/src/front_of_house.rs}}
```

<span class="caption">Liste 7-22: *src/front_of_house.rs*'deki `front_of_house` modülünün içindeki tanımlar</span>

Modül ağacınızda yalnızca bir `mod` bildirimi kullanarak bir dosya yüklemeniz gerektiğini unutmayın. 
Derleyici dosyanın projenin bir parçası olduğunu öğrendiğinde (ve `mod` deyimini nereye koyduğunuzdan dolayı kodun modül ağacında 
nerede olduğunu bildiğinde), projenizdeki diğer dosyalar bir yol kullanarak yüklenen dosyanın koduna başvurmalıdır. 
“Modül Ağacındaki Bir Öğeye Başvuru Yolları”][paths]<!-- ignore --> bölümünde anlatıldığı gibi, beyan edildiği yerde. 
Başka bir deyişle `mod`, diğer programlama dillerinde görmüş olabileceğiniz bir “include” işlemi *değildir*.

Ardından, `hosting` modülünü kendi dosyasına çıkaracağız. `hosting`, kök modülün değil, `front_of_house`'ın bir alt modülü olduğundan, 
süreç biraz farklı olacaktır. `hosting` dosyasını modül ağacındaki ataları için adlandırılacak yeni bir dizine yerleştireceğiz, 
bu durumda *src/front_of_house/* olacaktır.

`hosting`'i taşımak için, *src/front_of_house.rs* dosyasını yalnızca `hosting` modülünün bildirimini içerecek şekilde değiştiriyoruz:

<span class="filename">Dosya adı: src/front_of_house.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/no-listing-02-extracting-hosting/src/front_of_house.rs}}
```

Ardından bir *src/front_of_house* dizini ve `hosting` modülünde yapılan tanımları içerecek bir *hosting.rs* dosyası oluşturuyoruz:

<span class="filename">Dosya adı: src/front_of_house/hosting.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/no-listing-02-extracting-hosting/src/front_of_house/hosting.rs}}
```

Bunun yerine *src* dizinine *hosting.rs*'i koyarsak, derleyici *hosting.rs* kodunun kasa kökünde bildirilen bir `hosting` modülünde olmasını 
ve `front_of_house` modülünün alt öğesi olarak bildirilmemesini bekler. Derleyicinin hangi dosyaların hangi modüllerin kodunu kontrol edeceğine 
ilişkin kuralları, dizinlerin ve dosyaların modül ağacıyla daha yakından eşleştiği anlamına gelir.

> ### Alternatif Dosya Yolları
> 
> Şimdiye kadar Rust derleyicisinin kullandığı en deyimsel dosya yollarını ele aldık, 
> ancak Rust ayrıca daha eski bir dosya yolu stilini de destekliyor. 
> Kasa kökünde bildirilen `front_of_house` adlı bir modül için, derleyici modülün kodunu şurada arayacaktır:
> * *src/front_of_house.rs* (şu ana kadar ele aldığımız yol)
> * *src/front_of_house/mod.rs* (eski stil, hala desteklenen yol)
> 
> `front_of_house` alt modülü olan `hosting` adlı bir modül için, derleyici modülün kodunu şurada arayacaktır:
> 
> * *src/front_of_house/hosting.rs* (şu ana kadar ele aldığımız yol)
> * *src/front_of_house/hosting/mod.rs* (eski stil, hala desteklenen yol),
> 
> Aynı modül için her iki stili de kullanırsanız derleyici hatası alırsınız.
> Aynı projede farklı modüller için her iki stili de kullanmaya izin verilir, 
> ancak projenizde gezinen kişiler için kafa karıştırıcı olabilir.
> 
> *mod.rs* adlı dosyaları kullanan stilin ana dezavantajı, projenizin *mod.rs* adlı birçok dosyayla sonuçlanabilmesidir; 
> bu, onları aynı anda kod editörünüzde açtığınızda kafa karıştırıcı olmaya sebebiyet verebilir.

Her modülün kodunu ayrı bir dosyaya taşıdık ve modül ağacı aynı kaldı. `eat_at_restaurant` içindeki fonksiyon çağrıları, 
tanımlar farklı dosyalarda bulunsa bile herhangi bir değişiklik yapılmadan çalışacaktır. 
Bu teknik, modülleri boyut olarak büyüdükçe yeni dosyalara taşımanıza olanak tanır.

*src/lib.rs* içindeki `pub use crate::front_of_house::hosting` ifade yapısının da değişmediğini ve kullanımın kasanın parçası 
olarak hangi dosyaların derlendiği üzerinde herhangi bir etkisi olmadığını unutmayın. `mod` anahtar sözcüğü, modülleri bildirir ve Rust, 
o modüle giren kod için modülle aynı ada sahip bir dosyaya bakar.

## Özet

Rust, bir paketi birden çok kasaya ve bir kasayı modüllere ayırmanıza olanak tanır, 
böylece bir modülde tanımlanan öğelere başka bir modülden başvurabilirsiniz. 
Bunu, mutlak veya göreli yollar belirterek yapabilirsiniz. Bu yollar, bir `use` ifade yapısı ile kapsama alınabilir, 
böylece o kapsamdaki öğenin birden çok kullanımı için daha kısa bir yol kullanabilirsiniz. 
Modül kodu varsayılan olarak gizlidir, ancak `pub` anahtar sözcüğünü ekleyerek tanımları herkese açık hale getirebilirsiniz.

Bir sonraki bölümde, düzenli bir şekilde organize edilmiş kodunuzda kullanabileceğiniz standart kütüphanedeki bazı koleksiyon 
veri yapılarına bakacağız.

[paths]: ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html
