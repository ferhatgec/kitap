## Sahiplik Nedir?

*Sahiplik*, bir Rust programının belleği nasıl yönettiğini yöneten bir dizi kuraldır. 
Tüm programlar, çalışırken bilgisayarın belleğini kullanma şeklini yönetmek zorundadır. 
Bazı dillerde, program çalışırken düzenli olarak artık kullanılmayan belleği arayan çöp toplama vardır; diğer dillerde, 
programcı belleği açıkça tahsis etmeli ve serbest bırakmalıdır. Rust üçüncü bir yaklaşım kullanır: 
bellek, derleyicinin kontrol ettiği bir dizi kurala sahip bir sahiplik sistemi aracılığıyla yönetilir. 
Kurallardan herhangi biri ihlal edilirse program derlenmeyecektir. Sahiplik özelliklerinin hiçbiri, 
çalışırken programınızı yavaşlatmaz.

Sahiplik birçok programcı için yeni bir kavram olduğu için alışması biraz zaman alıyor. 
İyi haber şu ki, Rust ve sahiplik sisteminin kuralları konusunda ne kadar deneyimli olursanız, 
doğal olarak güvenli ve verimli kod geliştirmeyi o kadar kolay bulacaksınız. Öğrenmeye devam edin!

Sahipliği anladığınızda, Rust'ı benzersiz kılan özellikleri anlamak için sağlam bir temele sahip olacaksınız. 
Bu bölümde, çok yaygın bir veri yapısına odaklanan bazı örnekler üzerinde çalışarak sahipliği öğreneceksiniz (bkz. *dizgiler*).

> ### Yığıt ve Yığın
> Çoğu programlama dili, yığıt ve yığın hakkında çok sık düşünmenizi gerektirecek  özelliklere sahip değildir. 
> Ancak Rust gibi bir sistem programlama dilinde, bir değerin yığıtta mı yoksa yığında mı olması dilin nasıl davrandığını 
> ve neden belirli kararlar vermeniz gerektiğini etkiler. Sahiplik, bu bölümün ilerleyen kısımlarında yığıt ve yığınla ilgili olarak 
> açıklanacaktır, bu nedenle burada hazırlık aşamasında olan kısa bir açıklama bulunmaktadır.
> Hem yığıt hem de yığın, çalışma zamanında kodunuzun kullanabileceği bellek parçalarıdır, 
> ancak bunlar farklı şekillerde yapılandırılmıştır. Yığıt, değerleri aldığı sırayla saklar ve değerleri ters sırada tutar. 
> Buna *son giren ilk çıkar* (LIFO) denir. 
> Bir tabak yığıtını düşünün: daha fazla tabak eklediğinizde, 
> onları yığıtın üstüne koyarsınız ve bir tabağa ihtiyacınız olduğunda üstten bir tane alırsınız. 
> Ortadan veya alttan tabak eklemek veya çıkarmak da işe yaramaz! 
> Veri eklemeye yığıta itme denir ve veri kaldırmaya yığıttan çıkarma denir. 
> Yığıtta depolanan tüm verilerin bilinen, sabit bir boyutu olmalıdır. Derleme zamanında bilinmeyen bir boyuta veya değişebilecek bir
> boyuta sahip veriler, yığın (heap) üzerinde depolanmalıdır.
> 
> Yığın daha az organizedir: yığına veri koyduğunuzda, belirli bir miktar alan talep edersiniz. 
> Bellek ayırıcı, yığında yeterince büyük boş bir nokta bulur, onu kullanımda olarak işaretler ve o konumun adresi olan bir işaretçi
> döndürür. Bu işleme yığın üzerinde ayırma denir ve bazen sadece ayırma olarak kısaltılır (değerleri yığına itmek ayırma olarak kabul edilmez). 
> Yığın işaretçisi bilinen, sabit bir boyut olduğundan, işaretçiyi yığında saklayabilirsiniz, ancak gerçek verileri istediğinizde 
> işaretçiyi izlemelisiniz. Bir restoranda oturduğunuzu düşünün. Girdiğinizde grubunuzdaki kişi sayısını 
> belirtiyorsunuz ve görevliler herkese uygun boş bir masa bulup sizi oraya yönlendiriyor. 
> Grubunuzdan biri geç gelirse, sizi bulmak için nerede oturduğunuzu sorabilir.
> Yığıta itme, yığında tahsis etmekten daha hızlıdır, çünkü ayırıcı hiçbir zaman yeni verileri depolamak için 
> bir yer aramak zorunda kalmaz; bu konum her zaman yığıtın en üstündedir. Nispeten, yığın üzerinde alan tahsis 
> etmek daha fazla iş gerektirir, çünkü tahsis edenin önce verileri tutacak kadar büyük bir alan bulması ve 
> ardından bir sonraki tahsise hazırlanmak için defter tutma yapması gerekir.
> Yığındaki verilere erişmek, oraya ulaşmak için bir işaretçiyi izlemeniz gerektiğinden yığıttaki 
> verilere erişmekten daha yavaştır. Çağdaş işlemciler, bellekte daha az zıplarlarsa daha hızlı 
> olarak kabul edilir. Analojiye devam edersek, bir restoranda birçok masadan sipariş alan bir sunucu düşünün. 
> Bir sonraki masaya geçmeden önce tüm siparişleri bir masada toplamak en verimli yöntemdir. 
> A masasından bir sipariş, sonra B masasından, sonra tekrar A'dan ve sonra tekrar B'den bir sipariş almak çok 
> daha yavaş bir süreç olacaktır. Aynı şekilde, bir işlemci daha uzakta (yığın üzerinde olabileceği gibi) yerine diğer 
> verilere yakın olan (yığıt üzerinde olduğu gibi) veriler üzerinde çalışıyorsa işini daha iyi yapabilir.
> 
> Kodunuz bir fonksiyonu çağırdığında, fonksiyona iletilen değerler (potansiyel olarak yığın üzerindeki verilere işaretçiler de dahil) 
> ve işlevin yerel değişkenleri yığıta aktarılır. Fonksiyon bittiğinde, bu değerler yığıttan atılır.
> Kodun hangi bölümlerinin yığındaki hangi verileri kullandığını takip etmek, yığındaki yinelenen veri miktarını 
> en aza indirmek ve alanınız bitmemek için yığındaki kullanılmayan verileri temizlemek, 
> sahipliğin ele aldığı sorunlardır. Sahipliği anladıktan sonra, yığıt ve yığın hakkında çok sık düşünmenize gerek kalmayacak, 
> ancak sahipliğin asıl amacının yığın verilerini yönetmek olduğunu bilmek, neden böyle çalıştığını açıklamaya yardımcı olabilir.

### Sahiplik Kuralları

İlk olarak, sahiplik kurallarına bir göz atalım. 
Bunları gösteren örnekler üzerinde çalışırken bu kuralları aklınızda bulundurun:

* Rust'ta her değerin bir *sahibi* vardır.
* Aynı zamanda sadece bir sahip olabilir.
* Sahip kapsam dışına çıktığında değer düşürülür.

### Değişken Kapsamı

Artık temel Rust söz dizimini geride bıraktığımıza göre, 
örneklere `fn main() {` kodunu dahil etmeyeceğiz, bu nedenle takip ediyorsanız, aşağıdaki örnekleri
`main` fonksiyonunun içine koyduğunuzdan emin olun. Sonuç olarak, örneklerimiz biraz daha kısa olacak ve 
ortak kod yerine gerçek ayrıntılara odaklanmamıza izin verecek.

İlk sahiplik örneği olarak, bazı değişkenlerin kapsamına bakacağız. 
Kapsam, bir öğenin geçerli olduğu bir program içindeki aralıktır. Aşağıdaki değişkeni bakın:


```rust
let s = "hello";
```

`s` değişkeni, dizgi değerinin programımızın metnine sabit kodlanmış olduğu bir dizgi değişmezi anlamına gelir. 
Değişken, bildirildiği noktadan geçerli kapsamın sonuna kadar geçerlidir. Liste 4-1, `s` değişkeninin nerede geçerli 
olacağını açıklayan açıklamalar içeren bir programı gösterir.


```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-01/src/main.rs:here}}
```

<span class="caption">Liste 4-1: Bir değişken ve geçerli olduğu kapsam
</span>

Başka bir deyişle, burada iki önemli nokta vardır:

* `s`'in *kapsama* girmesi geçerli bir durumdur.
* *kapsam dışına* çıkana kadar geçerliliğini korur.

Bu noktada, kapsamlar ve değişkenlerin ne zaman geçerli olduğu arasındaki ilişki diğer programlama dillerindekine benzer.
Şimdi `String` türünü tanıtarak bu anlayışın üzerine inşa edeceğiz.

### `String` Türü

Sahiplik kurallarını göstermek için, Bölüm 3'ün [“Veri Türleri”][data-types]<!-- ignore --> bölümünde ele aldıklarımızdan daha 
karmaşık bir veri tipine ihtiyacımız var. Yığıt, kapsamı sona erdiğinde ve kodun başka bir bölümünün aynı değeri farklı bir kapsamda kullanması gerekiyorsa, yeni, bağımsız bir örnek oluşturmak için hızlı ve önemsiz bir şekilde kopyalanabilir. 
Ancak yığında depolanan verilere bakmak ve Rust'ın bu verileri ne zaman temizleyeceğini nasıl bildiğini keşfetmek istiyoruz ve `String` türü 
bu durum için harika bir örnek.

`String`'in sahiplikle ilgili kısımlarına odaklanacağız. Bu yönler, standart küyüphane tarafından sağlanmış veya sizin tarafınızdan 
oluşturulmuş olsun (ki diğer karmaşık veri türleri için de geçerlidir). `String`'i [Bölüm 8][ch8]<!-- ignore -->'de daha derinlemesine tartışacağız.

Bir dizgi değerinin programımıza sabit kodlanmış olduğu dizgi değişmezlerini zaten gördük. 
Dizgi değişmezleri uygundur, ancak metin kullanmak isteyebileceğimiz her durum için uygun değildirler. 
Bunun bir nedeni, değişmez olmalarıdır. Bir diğeri ise, kodumuzu yazarken her dizgi değeri bilinemez: 
örneğin, kullanıcı girdisini alıp depolamak istersek ne olur? Bu durumlar için Rust'ın ikinci bir dizgi türü vardır, 
`String`. Bu tür, yığına ayrılan verileri yönetir ve bu nedenle derleme zamanında bizim için bilinmeyen bir miktarda metin depolayabilir. 
`from` fonksiyonunu kullanarak bir dizgi değişmezinden bir `String` oluşturabilirsiniz, şöyle:

```rust
let s = String::from("hello");
```

Çift iki nokta üst üste `::` operatörü, 
`string_from` gibi bir tür isim kullanmak yerine, bu özel fonksiyondan `String` tipi altında isim-alanına izin verir. 
Bu söz dizimini Bölüm 5'in [“Metod Söz Dizimi”][method-syntax]<!-- ignore --> bölümünde ve Bölüm 7'deki [“Modül Ağacındaki Bir Öğeye Başvurma Yolları”][paths-module-tree]<!-- ignore --> daha fazla tartışacağız.

Bu tür bir dizgi de *değiştirilebilir*:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-01-can-mutate-string/src/main.rs:here}}
```

Peki, buradaki fark nedir? Neden `String` değiştirilebilir, ancak değişmezler (*adı üstünde*) değişemezler? 
Aradaki fark, bu iki türün bellekle nasıl başa çıktığıdır.

### Bellek ve Tahsis

Bir dizgi değişmezi durumunda, içeriğini derleme zamanında biliyoruz, bu nedenle metin doğrudan son yürütülebilir
dosyaya sabit kodlanmıştır. Bu nedenle dize değişmezleri hızlı ve verimlidir. Ancak bu özellikler yalnızca dize değişmezinin 
değişmezliğinden gelir. Ne yazık ki, derleme zamanında boyutu bilinmeyen ve programı çalıştırırken boyutu değişebilecek her metin 
parçası için yürütülebilir ikili dosyaya bir bellek bloğu koyamıyoruz.

`String` türüyle; değişebilir, büyütülebilir bir metin parçasını desteklemek ve içeriğini tutmak 
için yığın üzerinde derleme zamanında bilinmeyen bir miktar bellek ayırmamız gerekir. 

Bu, şu anlama gelir:

* Bellek, çalışma zamanında bellek ayırıcıdan talep edilmelidir.
* `String` ile işimiz bittiğinde bu hafızayı ayırıcıya geri döndürmenin 
  bir yoluna ihtiyacımız vardır.

Bu ilk kısım bizim tarafımızdan yapılır: `String::from`'u çağırdığımızda, 
süreklemesi ihtiyaç duyduğu hafızayı ister. Bu, programlama dillerinde oldukça evrenseldir.

Ancak ikinci kısım farklıdır. *Çöp toplayıcı (GC)* olan dillerde, GC artık kullanılmayan belleği izler ve 
temizler. Bellek tahsisi hakkında düşünmemize gerek yoktur. 
GC olmayan çoğu dilde, belleğin artık kullanılmadığını belirlemek ve tıpkı bizim talep ettiğimiz gibi, 
belleği açıkça boşaltmak için kodu çağırmak bizim sorumluluğumuzdadır. Bunu doğru yapmak, tarihsel olarak zor bir programlama problemi olmuştur. Unutursak, hafızayı boşa harcarız. Çok erken yaparsak geçersiz bir değişkenimiz olur. İki kez yaparsak, bu da bir hatadır. 
Tam olarak bir `tahsisi` tam olarak bir `geri verme` ile eşleştirmemiz gerekiyor.

Rust farklı bir yol izler: sahip olduğu değişken kapsam dışına çıktığında bellek otomatik olarak döndürülür. 
Aşağıda, bir dizgi değişmezi yerine bir `String` kullanarak Liste 4-1'deki kapsam örneğimizin farklı bir sürümü verilmiştir:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-02-string-scope/src/main.rs:here}}
```

`String`'imizin ihtiyaç duyduğu belleği ayırıcıya döndürebileceğimiz doğal bir nokta vardır: 
`s`'in kapsam dışına çıkması. Bir değişken kapsam dışına çıktığında Rust bizim için özel bir fonksiyon çağırır. 
Bu fonksiyona `drop` denir ve `String` yazarının belleği geri döndürmek için kodu koyabileceği yerdir. 
Rust çağrıları, kapanış parantezinde otomatik olarak `drop` fonksiyonunu çağırır.

> Not: C++'ta, bir öğenin kullanım süresinin sonunda kaynakları serbest bırakma modeline
> *Resource Acquisition Is Initialization (RAII)* adı verilir. 
> RAII kalıplarını kullandıysanız, Rust'taki `drop` fonksiyonu size tanıdık gelecektir.

Bu model, Rust kodunun yazılma şekli üzerinde derin bir etkiye sahiptir.
Şu anda basit görünebilir, ancak yığında tahsis ettiğimiz verileri birden çok değişkenin kullanmasını istediğimizde, 
daha karmaşık durumlarda kodun davranışı beklenmedik olabilir. 
Şimdi bu durumlardan bazılarını inceleyelim.

#### Değişkenlerin ve Veri Etkileşiminin Yolları: Hareket Ettirme

Birden çok değişken, Rust'ta aynı verilerle farklı şekillerde etkileşime girebilir. Liste 4-2'de, 
tam sayı kullanan bir örneğe bakalım.

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-02/src/main.rs:here}}
```

<span class="caption">Liste 4-2: `x` değişkeninin tam sayı değerini `y`'ye atama</span>

Muhtemelen bunun ne yaptığını tahmin edebiliriz: 
“`5`'i `x`'e ata; sonra `x`'deki değerin bir kopyasını al ve onu `y`'ye ata.".
Yani iki değişkenimiz olmuş oluyor, `x` ve `y`'nin her ikisi de `5`'e eşittir. Bu gerçekten olan şeydir, 
çünkü tam sayılar bilinen, sabit bir boyuta sahip basit değerlerdir ve bu iki `5` değeri yığına itilir.

Hadi `String` versiyonuna bakalım:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-03-string-move/src/main.rs:here}}
```
Bu çok benzer duruyor, bu yüzden çalışma şeklinin aynı olacağını varsayabiliriz: 
yani ikinci satır `s1`'deki değerin bir kopyasını alır ve onu `s2`'ye atar. Ama bu tam olarak ne olduğunu açıklamıyor.

Arka kapılar ardında `String`'e ne olduğunu görmek için Şekil 4-1'e bakın. 
Bir `String`, solda gösterilen üç bölümden oluşur: dizginin içeriğini, uzunluğunu ve kapasitesini tutan belleğe yönelik 
bir işaretçi. 
Bu veri grubu yığıtta depolanır. Sağda, içeriği tutan yığın üzerindeki bellek bulunur.

<img alt="Bellekteki String" src="img/trpl04-01.svg" class="center" style="width: 50%;" />

<span class="caption">Şekil 4-1: `s1`'e atanmış `"hello"` değerini tutan bir `String`'in bellekteki temsili</span>

Uzunluk, `String` içeriğinin bayt cinsinden ne kadar bellek kullandığıdır. 
Kapasite, `String`'in ayırıcıdan aldığı bayt cinsinden toplam bellek miktarıdır. 
Uzunluk ve kapasite arasındaki fark önemlidir, ancak bu bağlamda değil, bu nedenle şimdilik kapasiteyi göz ardı etmekte fayda var.

`s1`'i `s2`'ye atadığımızda, `String` verileri kopyalanır, yani yığıttaki işaretçiyi, uzunluğu ve kapasiteyi kopyalarız. 
İşaretçinin başvurduğu yığın üzerindeki verileri kopyalamayız. 
Başka bir deyişle, bellekteki veri gösterimi Şekil 4-2'ye benzer.

<img alt="s1 ve s2 aynı değere işaret ediyor" src="img/trpl04-02.svg" class="center" style="width: 50%;" />

<span class="caption">Şekil 4-2: İşaretçinin, uzunluğun ve `s1` kapasitesinin bir kopyasına sahip olan `s2` değişkeninin bellekteki temsili</span>

Temsil, Şekil 4-3'e *benzemiyor*; bu, Rust'un yığın verilerini de kopyalaması durumunda belleğin nasıl görüneceğini gösterir. 
Bunu Rust yapsaydı, yığın üzerindeki veriler de büyük olsaydı `s2 = s1` işlemi çalışma zamanı performansı açısından çok pahalı olabilirdi.

<img alt="s1 ve s2 iki farklı yeri işaret ediyor" src="img/trpl04-03.svg" class="center" style="width: 50%;" />

<span class="caption">Şekil 4-3: Rust yığın verilerini de kopyalasaydı `s2 = s1`'in neler yapabileceğine dair başka bir olasılık</span>

Daha önce, bir değişken kapsam dışına çıktığında Rust'ın otomatik olarak `drop` fonksiyonunu çağırdığını ve o değişken için 
yığın belleğini temizlediğini söylemiştik. Ancak Şekil 4-2, aynı konuma işaret eden her iki veri işaretçisini de göstermektedir. 
Bu bir sorundur: `s2` ve `s1` kapsam dışına çıktığında, ikisi de aynı belleği boşaltmaya çalışacaklardır. 
Bu, *çifte serbest bırakma hatası* olarak bilinir ve daha önce bahsettiğimiz bellek güvenlik hatalarından biridir. 
Belleği iki kez boşaltmak bellek bozulmasına neden olabilir ve bu da potansiyel olarak güvenlik açıklarına yol açabilir.

Bellek güvenliğini sağlamak için, `let s2 = s1` satırından sonra Rust, `s1`'in artık geçerli olmadığını düşünür. 
Bu nedenle, `s1` kapsam dışına çıktığında Rust'ın hiçbir şeyi serbest bırakmasına gerek yoktur. 
`s2` oluşturulduktan sonra `s1`'i kullanmaya çalıştığınızda ne olduğuna bakın; çalışmayacaktır:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-04-cant-use-after-move/src/main.rs:here}}
```

Rust, geçersiz kılınan referansı kullanmanızı engellediği için şöyle bir hata alırsınız:

```console
{{#include ../listings/ch04-understanding-ownership/no-listing-04-cant-use-after-move/output.txt}}
```

Diğer dillerle çalışırken *sığ kopyalama* ve *derin kopyalama* terimlerini duymuşsanız, verileri kopyalamadan işaretçiyi, 
uzunluğu ve kapasiteyi kopyalama kavramı muhtemelen sığ bir kopya oluşturmaya benziyor. 
Ancak Rust aynı zamanda ilk değişkeni de geçersiz kıldığı için, onu sığ bir kopya olarak adlandırmak yerine *hareket* olarak adlandırırız. 
Bu örnekte, `s1`'in `s2`'ye hareket ettiğini söyleyebiliriz. Yani gerçekte ne olduğu Şekil 4-4'te gösterilmektedir.

<img alt="s1, s2'ye hareket ettirildi" src="img/trpl04-04.svg" class="center" style="width: 50%;" />

<span class="caption">Şekil 4-4: `s1` geçersiz kılındıktan sonra hafızadaki temsili</span>

Bu bizim sorunumuzu çözüyor! Yalnızca `s2` geçerli olduğunda, 
kapsam dışına çıktığında tek başına belleği boşaltır ve yapmamız gereken bir ley kalmaz.

Ek olarak, bununla ima edilen bir tasarım seçeneği vardır: 
Rust, verilerinizin “derin” kopyalarını asla otomatik olarak oluşturmaz. 
Bu nedenle, herhangi bir otomatik kopyalamanın çalışma zamanı performansı açısından ucuz olduğu varsayılabilir.

#### Değişkenlerin ve Veri Etkileşiminin Yolları: Klonlama

Yalnızca yığıt verilerini değil, `String`'in yığın verilerini de derinlemesine kopyalamak istiyorsak, 
`clone` adı verilen ortak metodu kullanabiliriz. 
Metod söz dizimini Bölüm 5'te tartışacağız, ancak metodlar birçok programlama dilinde ortak bir özellik olduğundan, 
muhtemelen onları daha önce görmüşsünüzdür.

İşte çalışma halindeki `clone` metodunun bir örneği:

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-05-clone/src/main.rs:here}}
```

Bu gayet iyi çalışır ve yığın verilerinin kopyalandığı Şekil 4-3'te gösterilen davranışı açıkça *üretir*.

Bir `clone` çağrısı gördüğünüzde, bazı rastgele kodların yürütüldüğünü ve bu kodun pahalı olabileceğini bilirsiniz. 
Bu, farklı bir şeyin olup bittiğinin bir göstergesidir.

#### Yalnızca Yığıt Kullanan Veriler: Kopyalama

Henüz bahsetmediğimiz başka bir olay daha var. Bir kısmı Liste 4-2'de gösterilen 
tam sayıları kullanan bu kod çalışır ve geçerlidir:


```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/no-listing-06-copy/src/main.rs:here}}
```

Ancak bu kod, az önce öğrendiklerimizle çelişiyor gibi görünüyor: `clone` çağrımız yok, ancak `x` hala geçerli ve `y`'ye hareket ettirilmedi.

Bunun nedeni, derleme zamanında boyutu bilinen tam sayılar gibi türlerin tamamen yığıtta saklanmasıdır, 
bu nedenle gerçek değerlerin kopyaları hızlı bir şekilde oluşturulur. Bu, `y` değişkenini oluşturduktan sonra `x`'in geçerli olmasını engellemek istememiz için hiçbir neden olmadığı anlamına gelir. Başka bir deyişle, burada derin ve sığ kopyalama arasında bir fark yoktur, 
bu nedenle `clone` çağırmak normal sığ kopyalamadan farklı bir şey yapmaz ve bunu dışarıda bırakabiliriz.

Rust'ın, tam sayılar gibi yığıtta depolanan türlere yerleştirebileceğimiz `Copy` tanımı adı verilen özel bir açıklaması vardır 
(tanımlar hakkında [Bölüm 10][traits]<!-- ignore -->'da daha fazla konuşacağız). 
Bir tür, `Copy` özelliğini süreklerse (uygularsa), onu kullanan değişkenler hareket etmez, 
bunun yerine önemsiz bir şekilde kopyalanır, bu da onları başka bir değişkene atandıktan sonra hala geçerli hale getirir.

Rust, tür veya türün herhangi bir parçası `Drop` tanımını süreklemişse, bir türe `Copy` ile açıklama eklememize izin vermez. 
Değer kapsam dışına çıktığında türün özel bir şeye ihtiyacı varsa ve bu türe `Copy` ek açıklamasını eklersek, 
bir derleme zamanı hatası alırız. Niteliği uygulamak için `Copy` ek açıklamasını türünüze nasıl ekleyeceğinizi öğrenmek için 
Ek C'deki [“Türetilebilir Tanımlar”][derivable-traits]<!-- ignore --> başlığına bakın.

Peki, `Copy` tanımını hangi türler sürekler? Emin olmak için verilen türün dokümantasyonunu kontrol edebilirsiniz, 
ancak genel bir kural olarak, herhangi bir basit skaler değer grubu `Copy`'i sürekleyebilir. 
`Copy`'i sürekleyen türlerden bazıları şunlardır:

* Tüm tam sayı türleri, meselax `u32`.
* Boole türü, `bool`, `true` ve `false`.
* Tüm kayan nokta türleri, mesela `f64`.
* Karakter türü, `char`.
* Demetler, eğer tutulan tür de `Copy`'i süreklemiş ise. Mesela,
  `(i32, i32)`, `Copy`i sürekler fakat `(i32, String)` süreklemez.

### Sahiplik ve Fonksiyonlar

Bir fonksiyona değer aktarmanın mekanikleri, bir değişkene değer atamaya benzer. 
Bir fonksiyona değişken iletmek, tıpkı atamada olduğu gibi taşınır veya kopyalanır. 
Liste 4-3, değişkenlerin nerede kapsam içine girip nerede kapsam dışında kaldığını 
gösteren bazı açıklamalar içeren bir örneğe sahiptir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-03/src/main.rs}}
```

<span class="caption">Liste 4-3: Sahiplik ve kapsam açıklamalı fonksiyonlar</span>

`takes_ownership` çağrısından sonra `s`'i kullanmaya çalışırsak, 
Rust bir derleme zamanı hatası verir. Bu tarz statik kontroller bizi hatalardan korur. 
Bunları nerede kullanabileceğinizi ve sahiplik kurallarının bunu yapmanızı nerede engellediğini görmek 
için `s` ve `x` kullanan kodu `main`'e eklemeyi deneyin.

### Dönüş Değerleri ve Kapsam

Dönen değerler de sahipliği aktarabilir. 
Liste 4-4, Liste 4-3'tekilere benzer açıklamalarla bir değer döndüren bir fonksiyon örneğini gösterir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-04/src/main.rs}}
```

<span class="caption">Liste 4-4: Dönüş değerlerinin sahipliğini aktarma</span>

Bir değişkenin sahipliği her seferinde aynı kalıbı takip eder: 
başka bir değişkene bir değer atamak onu hareket ettirir. 
Yığın üzerindeki verileri içeren bir değişken kapsam dışına çıktığında, 
verilerin sahipliği başka bir değişkene taşınmadıkça değer `drop` ile temizlenir.

Bu işe yararken, sahiplik almak ve ardından her fonksiyonla birlikte sahipliğini iade etmek biraz sıkıcıdır. 
Ya bir fonksiyonun bir değer kullanmasına izin vermek istiyorsak ancak sahipliğini almak istemiyorsak? 
Döndürmek isteyebileceğimiz fonksiyonun gövdesinden kaynaklanan herhangi bir veriye ek olarak, 
tekrar kullanmak istiyorsak ilettiğimiz herhangi bir şeyin de geri iletilmesinin gerekmesi oldukça can sıkıcıdır.

Rust, Liste 4-5'te gösterildiği gibi, bir demet kullanarak birden çok değer döndürmemize izin verir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch04-understanding-ownership/listing-04-05/src/main.rs}}
```

<span class="caption">Liste 4-5: Parametrelerin sahipliğini geri döndürme</span>

Ancak bu, yaygın olması gereken bir konsept için çok fazla başımıza iş açıyor. 
Şansımıza Rust, *referans* adı verilen, sahipliği devretmeden bir değeri kullanma özelliğine sahip.

[data-types]: ch03-02-data-types.html#data-types
[ch8]: ch08-02-strings.html
[traits]: ch10-02-traits.html
[derivable-traits]: appendix-03-derivable-traits.html
[method-syntax]: ch05-03-method-syntax.html#method-syntax
[paths-module-tree]: ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html
[drop]: ../std/ops/trait.Drop.html#tymethod.drop
