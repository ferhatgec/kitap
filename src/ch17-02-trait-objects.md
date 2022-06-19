## Farklı Türlerdeki Değerlere İzin Veren Tanım Nesnelerini Kullanma

Bölüm 8'de, vektörlerin bir sınırlamasının yalnızca tek bir türden elemanları saklayabilmeleri olduğundan bahsetmiştik. 
Liste 8-9'da tam sayıları, kayan değerleri ve metni tutmak için varyantları olan bir `SpreadsheetCell` `enum`'u tanımladığımız 
geçici bir çözüm oluşturduk. Bu, her hücrede farklı veri türlerini saklayabileceğimiz ve yine de bir hücre satırını temsil eden 
bir vektöre sahip olabileceğimiz anlamına geliyordu. Bu, değiştirilebilir öğelerimiz kodumuz derlendiğinde bildiğimiz sabit bir tür 
kümesi olduğunda mükemmel bir çözümdür.

Ancak, bazen kütüphane kullanıcımızın belirli bir durumda geçerli olan türler kümesini genişletebilmesini isteriz. 
Bunu nasıl başarabileceğimizi göstermek için, GUI araçları için yaygın bir teknik olan, bir öğe listesini yineleyerek her birini ekrana 
çizmek için bir `draw` yöntemini çağıran örnek bir grafik kullanıcı arayüzü (GUI) aracı oluşturacağız. 
GUI kütüphanesinin yapısını içeren `gui` adında bir kütüphane kasası oluşturacağız. Bu kasa insanların kullanması için 
`Button` veya `TextField` gibi bazı türler içerebilir. Buna ek olarak, gui kullanıcıları çizilebilecek kendi türlerini 
oluşturmak isteyeceklerdir: örneğin, bir programcı bir `Image` ekleyebilir ve bir diğeri bir `SelectBox` ekleyebilir.

Bu örnek için tam teşekküllü bir GUI kütüphanesi uygulamayacağız ancak parçaların birbirine nasıl uyacağını göstereceğiz. 
Kütüphaneyi yazarken, diğer programcıların oluşturmak isteyebileceği tüm türleri bilemeyiz ve tanımlayamayız. 
Ancak `gui`'nin farklı tiplerdeki birçok değeri takip etmesi gerektiğini ve bu farklı tipteki değerlerin her biri için bir 
`draw` metodu çağırması gerektiğini biliyoruz. `draw` metodunu çağırdığımızda tam olarak ne olacağını bilmesine gerek yoktur, 
sadece değerin çağırmamız için bu metoda sahip olması yeterlidir.

Bunu kalıtımın olduğu bir dilde yapmak için, üzerinde `draw` adında bir yöntem bulunan `Component` adında bir sınıf tanımlayabiliriz. 
`Button`, `Image` ve `SelectBox` gibi diğer sınıflar `Component`'ten miras alır ve böylece `draw` yöntemini miras alır. 
Her biri kendi özel davranışlarını tanımlamak için `draw` yöntemini geçersiz kılabilir, ancak çerçeve tüm türlere `Component` örneğiymiş 
gibi davranabilir ve `draw` yöntemini çağırabilir. Ancak Rust'ta kalıtım olmadığı için, kullanıcıların yeni türlerle genişletmesine 
izin vermek üzere `gui` kütüphanesini yapılandırmak için başka bir yola ihtiyacımız var.

### Ortak Davranış için Bir Özellik Tanımlama

`gui`'nin sahip olmasını istediğimiz davranışı uygulamak için, `draw` adında bir metoda sahip olacak `Draw` adında bir `trait` tanımlayacağız. 
Daha sonra bir `trait` nesnesi alan bir vektör tanımlayabiliriz. Bir `trait` nesnesi, hem belirttiğimiz `trait`'i uygulayan bir türün 
örneğine hem de çalışma zamanında bu türdeki `trait` yöntemlerini aramak için kullanılan bir tabloya işaret eder. 
Bir `&` referansı veya `Box<T>` akıllı işaretçisi gibi bir tür işaretçi, ardından dyn anahtar sözcüğü ve ardından ilgili özelliği 
belirterek bir `trait` nesnesi oluştururuz. (Özellik nesnelerinin neden bir işaretçi kullanması gerektiğinden Bölüm 19'da 
[“Dinamik Olarak Boyutlandırılmış Türler ve `Sized` Tanımı”][dynamically-sized]<!-- ignore --> bölümünde bahsedeceğiz). Tanım nesnelerini 
yaygın veya somut bir tip yerine kullanabiliriz. Bir `trait` nesnesi kullandığımız her yerde, Rust'ın tür sistemi derleme zamanında bu 
bağlamda kullanılan herhangi bir değerin `trait` nesnesinin özelliğini uygulamasını sağlayacaktır. Sonuç olarak, derleme zamanında tüm 
olası türleri bilmemize gerek yoktur.

Rust'ta `struct` ve `enum`'ları diğer dillerin nesnelerinden ayırmak için “nesne” olarak adlandırmaktan kaçındığımızdan bahsetmiştik. 
Bir `struct` veya `enum`'da, `struct` alanlarındaki veri ve `impl` bloklarındaki davranış birbirinden ayrılırken, diğer dillerde veri ve 
davranış tek bir kavramda birleştirilir ve genellikle bir nesne olarak etiketlenir. Bununla birlikte, tanım nesneleri, 
veri ve davranışı birleştirmeleri açısından diğer dillerdeki nesnelere daha çok benzemektedir. Ancak `trait` nesneleri, 
bir `trait` nesnesine veri ekleyemediğimiz için geleneksel nesnelerden farklıdır. Özellik nesneleri diğer dillerdeki nesneler kadar 
genel olarak kullanışlı değildir: özel amaçları ortak davranışlar arasında soyutlamaya izin vermektir.

Liste 17-3, `draw` adında bir yöntemle `Draw` adında bir özelliğin nasıl tanımlanacağını gösterir:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-03/src/lib.rs}}
```

<span class="caption">Liste 17-3: `Draw` tanımının tanımı</span>

This syntax should look familiar from our discussions on how to define traits
in Chapter 10. Next comes some new syntax: Listing 17-4 defines a struct named
`Screen` that holds a vector named `components`. This vector is of type
`Box<dyn Draw>`, which is a trait object; it’s a stand-in for any type inside
a `Box` that implements the `Draw` trait.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-04/src/lib.rs:here}}
```

<span class="caption">Liste 17-4: `Draw` tanımını uygulayan tanım nesnelerinden oluşan bir vektörü tutan 
`components` alanına sahip `Screen` yapısının tanımı</span>

`Screen` yapısında, Liste 17-5'te gösterildiği gibi, `components`'in her birinde `draw` yöntemini çağıracak `run` adında bir 
yöntem tanımlayacağız:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-05/src/lib.rs:here}}
```

<span class="caption">Liste 17-5: Her bileşende `draw` yöntemini çağıran `Screen` üzerinde bir `run` yöntemi</span>

Bu, özellik sınırları olan genel bir tür parametresi kullanan bir `struct` tanımlamaktan farklı çalışır. 
Bir yaygın tür parametresi bir seferde yalnızca bir somut tiple değiştirilebilirken, 
tanım nesneleri çalışma zamanında birden fazla somut tipin özellik nesnesinin yerini doldurmasına izin verir. 
Örneğin, `Screen` yapısını Liste 17-6'daki gibi bir yaygın tip ve bir tanım bağı kullanarak tanımlayabilirdik:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-06/src/lib.rs:here}}
```

<span class="caption">Liste 17-6: Yaygınlar ve tanım sınırlarını kullanarak `Screen` yapısının ve `run` yönteminin alternatif bir uygulaması</span>

Bu, bizi, tümü `Button` türünden veya tümü `TextField` türünden bileşenlerin bir listesini içeren bir `Screen` örneğiyle sınırlar. 
Yalnızca homojen koleksiyonlarınız olacaksa, yaygınlar ve özellik sınırlarının kullanılması tercih edilir, çünkü somut türleri kullanmak 
için tanımlar derleme zamanında monomorfize edilecektir.

Öte yandan, özellik nesnelerini kullanan yöntemle, bir `Screen` örneği, `Box<Button>` ve `Box<TextField>` içeren bir `Vec<T>` içerebilir. 
Bunun nasıl çalıştığına bakalım ve ardından çalışma zamanı performans sonuçları hakkında konuşacağız.

### Tanımı Uygulamak

Şimdi `Draw` özelliğini uygulayan bazı türleri ekleyeceğiz. `Button` türünü sağlayacağız. Yine, aslında bir GUI kütüphanesini 
uygulamak bu kitabın kapsamı dışındadır, bu nedenle `draw` yönteminin gövdesinde herhangi bir çalışan süreklemesi olmayacaktır. 
Uygulamanın nasıl görünebileceğini hayal etmek için, bir `Button` yapısında Liste 17-7'de 
gösterildiği gibi `width`, `height` ve `label` alanları olabilir:
  
<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch17-oop/listing-17-07/src/lib.rs:here}}
```

<span class="caption">Liste 17-7: `Draw` tanımını uygulayan bir `Button` 
yapısı</span>

`Button`'daki `width`, `height` ve `label` alanları diğer bileşenlerdeki alanlardan farklı olacaktır; 
örneğin, bir `TextField` türü aynı alanlara ve bir yer tutucu alana sahip olabilir. Ekranda çizmek istediğimiz türlerin her 
biri `Draw` tanımını uygular, ancak burada `Button`'da olduğu gibi (belirtildiği gibi gerçek GUI kodu olmadan) 
söz konusu türün nasıl çizileceğini tanımlamak için draw yönteminde farklı kod kullanır. 
Örneğin `Button` tipi, kullanıcı düğmeye tıkladığında ne olacağıyla ilgili metotları içeren ek bir `impl` bloğuna sahip olabilir. 
Bu tür yöntemler `TextField` gibi türler için geçerli olmayacaktır.

Kütüphanemizi kullanan biri `width`, `height` ve `options` alanları olan bir `SelectBox` yapısını uygulamaya karar verirse, 
Liste 17-8'de gösterildiği gibi `SelectBox` türüne `Draw` tanımını da uygular:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch17-oop/listing-17-08/src/main.rs:here}}
```

<span class="caption">Liste 17-8: Bir `SelectBox` yapısı üzerinde `gui` kullanan ve `Draw` tanımını uygulayan başka bir kasa</span>

Kütüphanemizin kullanıcısı artık bir `Screen` örneği oluşturmak için `main` fonksiyonunu yazabilir. 
`Screen` örneğine bir `SelectBox` ve bir `Button` ekleyebilir ve her birini bir `Box<T>` içine koyarak bir `trait` nesnesi haline getirebilirler. 
Daha sonra `Screen` örneğinde `run` metodunu çağırabilirler, bu da her bir bileşen üzerinde `draw` metodunu çağıracaktır. 
Liste 17-9 bu uygulamayı göstermektedir:
  
<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch17-oop/listing-17-09/src/main.rs:here}}
```

<span class="caption">Liste 17-9: Aynı özelliği uygulayan farklı türlerin değerlerini depolamak için tanım 
nesnelerini kullanma</span>

Kütüphaneyi yazarken, birinin `SelectBox` türünü ekleyebileceğini bilmiyorduk, 
ancak `Screen` uygulamamız yeni tür üzerinde çalışabiliyor ve onu çizebiliyordu çünkü `SelectBox` `Draw` özelliğini uyguluyor, 
yani `draw` yöntemini uyguluyor.

Bu kavram - bir değerin somut türünden ziyade yalnızca değerin yanıt verdiği mesajlarla ilgilenmek - dinamik olarak yazılan dillerdeki *ördek gibi 
yazma* kavramına benzer: ördek gibi yürüyorsa ve ördek gibi vaklıyorsa, o zaman bir ördek olmalıdır! Liste 17-5'teki `run on Screen` 
uygulamasında, `run`'ın her bir bileşenin somut türünün ne olduğunu bilmesine gerek yoktur. Bir bileşenin `Button` ya da `SelectBox` örneği 
olup olmadığını kontrol etmez, sadece bileşen üzerindeki `draw` yöntemini çağırır. Bileşenler vektöründeki değerlerin türü olarak 
`Box<dyn Draw>` belirterek, `Screen`'i `draw` yöntemini çağırabileceğimiz değerlere ihtiyaç duyacak şekilde tanımladık.

Ördek tiplemesi kullanan kodlara benzer kod yazmak için `trait` nesnelerini ve Rust'ın tür sistemini kullanmanın avantajı, 
çalışma zamanında bir değerin belirli bir yöntemi uygulayıp uygulamadığını kontrol etmek zorunda kalmamamız veya bir değer bir yöntemi 
uygulamıyorsa ancak yine de çağırırsak hata alma konusunda endişelenmememizdir. Değerler, özellik nesnelerinin ihtiyaç duyduğu özellikleri 
uygulamıyorsa Rust kodumuzu derlemeyecektir.

Örneğin, Liste 17-10, bileşen olarak `String` içeren bir `Screen` oluşturmaya çalıştığımızda ne olacağını gösterir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch17-oop/listing-17-10/src/main.rs}}
```

<span class="caption">Liste 17-10: Tanım nesnesinin özelliğini uygulamayan bir tür kullanmaya çalışmak</span>

`String`, `Draw` özelliğini uygulamadığı için bu hatayı alıyoruz:

```console
{{#include ../listings/ch17-oop/listing-17-10/output.txt}}
```

Bu hata bize ya `Screen`'e geçmek istemediğimiz bir şey geçirdiğimizi ve bu nedenle farklı bir tür geçirmemiz gerektiğini 
ya da `Screen`'in üzerinde `draw` çağrısı yapabilmesi için `Draw on String`'i uygulamamız gerektiğini bildirir.

### Tanım Nesneleri Dinamik Gönderim Gerçekleştirir

Bölüm 10'daki [“Yaygınları Kullanan Kodun Performansı”][performance-of-code-using-generics]<!-- ignore --> bölümünde, 
yaygınlarda özellik sınırları kullandığımızda derleyici tarafından gerçekleştirilen monomorfizasyon işlemi hakkındaki tartışmamızı 
hatırlayın: derleyici, yaygın tür parametresi yerine kullandığımız her somut tip için fonksiyonların ve metodların yaygın olmayan 
uygulamalarını üretir. Monomorfizasyondan kaynaklanan kod, derleyicinin derleme zamanında hangi yöntemi çağırdığınızı bildiği 
statik gönderim yapıyor. Bu, derleyicinin derleme sırasında hangi yöntemi çağırdığınızı bilemediği dinamik gönderime zıttır. 
Dinamik gönderim durumlarında, derleyici çalışma zamanında hangi yöntemin çağrılacağını belirleyecek kodu yayınlar.

Tanım nesnelerini kullandığımızda, Rust dinamik gönderim kullanacaktır. Derleyici, `trait` nesnelerini kullanan kodla kullanılabilecek tüm 
türleri bilmez, bu nedenle hangi türde hangi yöntemin uygulanacağını bilemez. Bunun yerine, çalışma zamanında Rust, hangi yöntemin çağrılacağını 
bilmek için `trait` nesnesinin içindeki işaretçileri kullanır. Bu arama, statik gönderim ile oluşmayan bir çalışma zamanı maliyetine neden olur. 
Dinamik gönderim ayrıca derleyicinin bir yöntemin kodunu satır içi yapmayı seçmesini engeller ve bu da bazı optimizasyonları önler. 
Ancak, Liste 17-5'te yazdığımız ve Liste 17-9'da destekleyebildiğimiz kodda ekstra esneklik elde ettik, 
bu nedenle dikkate alınması gereken bir değiş tokuş.

[performance-of-code-using-generics]:
ch10-01-syntax.html#performance-of-code-using-generics
[dynamically-sized]: ch19-04-advanced-types.html#dynamically-sized-types-and-the-sized-trait
