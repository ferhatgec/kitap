## Testlerin Nasıl Çalıştırıldığını Kontrol Etme

Tıpkı `cargo run`'un kodunuzu derlemesi ve ardından ortaya çıkan ikili dosyayı çalıştırması gibi, 
`cargo test` de kodunuzu test modunda derler ve ortaya çıkan test ikili dosyasını çalıştırır. 
`cargo test` tarafından üretilen ikilinin varsayılan davranışı, tüm testleri paralel olarak çalıştırmak ve test çalıştırmaları 
sırasında üretilen çıktıyı yakalamak, çıktının görüntülenmesini önlemek ve test sonuçlarıyla ilgili çıktıyı okumayı kolaylaştırmaktır. 
Bununla birlikte, bu varsayılan davranışı değiştirmek için komut satırı seçenekleri belirleyebilirsiniz.

Bazı komut satırı seçenekleri `cargo test`'e, bazıları ise elde edilen test ikilisine gider. 
Bu iki tür argümanı ayırmak için, `cargo test`'e giden argümanları ve ardından ayırıcıyı `--` ve ardından test ikilisine gidenleri 
listelersiniz. `cargo test --help` komutunu çalıştırdığınızda `cargo test` ile kullanabileceğiniz seçenekler görüntülenir 
ve `cargo test -- --help` komutunu çalıştırdığınızda ayırıcıdan sonra kullanabileceğiniz seçenekler görüntülenir.

### Testleri Paralel veya Ardışık Olarak Çalıştırma

Birden fazla test çalıştırdığınızda, varsayılan olarak iş parçacıkları kullanılarak paralel olarak çalışırlar, 
yani daha hızlı çalışırlar ve daha hızlı geri bildirim alırsınız. Testler aynı anda çalıştığından, 
testlerinizin birbirlerine veya geçerli çalışma dizini veya ortam değişkenleri gibi paylaşılan bir ortam da dahil 
olmak üzere herhangi bir paylaşılan duruma bağlı olmadığından emin olmalısınız.

Örneğin, testlerinizin her birinin diskte *test-output.txt* adında bir dosya oluşturan ve bu dosyaya bazı veriler yazan bir 
kod çalıştırdığını varsayalım. Ardından her test bu dosyadaki verileri okur ve dosyanın her testte farklı olan belirli bir değer 
içerdiğini iddia eder. Testler aynı anda çalıştığından, bir testin dosyayı yazması ve okuması arasında geçen sürede bir test dosyanın 
üzerine yazabilir. Bu durumda ikinci test, kod hatalı olduğu için değil, testler paralel olarak çalışırken birbirine karıştığı 
için başarısız olacaktır. Bir çözüm, her testin farklı bir dosyaya yazdığından emin olmaktır; başka bir çözüm ise testleri teker teker çalıştırmaktır.

Testleri paralel olarak çalıştırmak istemiyorsanız veya kullanılan iş parçacığı sayısı üzerinde daha ayrıntılı kontrol istiyorsanız, 
`--test-threads` bayrağını ve kullanmak istediğiniz iş parçacığı sayısını test ikilisine gönderebilirsiniz. 

Aşağıdaki örneğe bir göz atın:


```console
$ cargo test -- --test-threads=1
```

Test iş parçacığı sayısını `1` olarak ayarladık ve programa herhangi bir paralellik kullanmamasını söyledik. 
Testleri tek bir iş parçacığı kullanarak çalıştırmak, paralel olarak çalıştırmaktan daha uzun sürecektir, 
ancak testler durumu paylaşırlarsa birbirlerini etkilemeyeceklerdir.

### Fonksiyon Çıktısını Gösterme

Varsayılan olarak, bir test geçerse, Rust'ın test kütüphanesi standart çıktıya yazdırılan her şeyi yakalar. 
Örneğin, bir testte `println!` komutunu çağırırsak ve test geçerse, uçbirimde `println!` çıktısını görmeyiz; yalnızca testin 
geçtiğini gösteren satırı görürüz. Bir test başarısız olursa, *başarısız* mesajının geri kalanıyla birlikte 
standart çıktıya ne yazdırıldıysa onu görürüz.

Örnek olarak, Liste 11-10'da parametresinin değerini yazdıran ve `10` döndüren saçma bir fonksiyonun yanı sıra başarılı 
olan bir test ve başarısız olan bir test vardır.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,panics,noplayground
{{#rustdoc_include ../listings/ch11-writing-automated-tests/listing-11-10/src/lib.rs}}
```

<span class="caption">Liste 11-10: `println!` çağrısı yapan bir fonksiyon için testler</span>

Bu testleri `cargo test` ile çalıştırdığımızda aşağıdaki çıktıyı göreceğiz:

```console
{{#include ../listings/ch11-writing-automated-tests/listing-11-10/output.txt}}
```

Bu çıktının hiçbir yerinde `I got the value 4` ifadesini görmediğimize dikkat edin; 
bu, geçen test çalıştırıldığında yazdırılan değerdir. Bu çıktı yakalanmıştır. Başarısız olan testin çıktısı, 
`I got the value 8`, test özeti çıktısının test başarısızlığının nedenini de gösteren bölümünde görünür.

Geçen testler için de yazdırılan değerleri görmek istiyorsak, Rust'a `--show-output` ile başarılı testlerin 
çıktısını da en sonunda göstermesini söyleyebiliriz.

```console
$ cargo test -- --show-output
```

Liste 11-10'daki testleri `--show-output` bayrağı ile tekrar çalıştırdığımızda aşağıdaki çıktıyı görüyoruz:

```console
{{#include ../listings/ch11-writing-automated-tests/output-only-01-show-output/output.txt}}
```

### Bir Test Alt Kümesini Ada Göre Çalıştırma

Bazen tam bir test paketi çalıştırmak uzun zaman alabilir. Belirli bir alandaki kod üzerinde çalışıyorsanız, 
yalnızca o kodla ilgili testleri çalıştırmak isteyebilirsiniz. Çalıştırmak istediğiniz test(ler)in adını veya adlarını 
`cargo test`'e argüman olarak ileterek hangi testlerin çalıştırılacağını seçebilirsiniz.

Testlerin bir alt kümesinin nasıl çalıştırılacağını göstermek için, önce Liste 11-11'de gösterildiği gibi `add_two` 
fonksiyonumuz için üç test oluşturacağız ve hangilerinin çalıştırılacağını seçeceğiz.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch11-writing-automated-tests/listing-11-11/src/lib.rs}}
```

<span class="caption">Liste 11-11: Üç farklı isimle üç test</span>

Testleri daha önce gördüğümüz gibi herhangi bir argüman geçmeden çalıştırırsak, tüm testler paralel olarak çalışacaktır:

```console
{{#include ../listings/ch11-writing-automated-tests/listing-11-11/output.txt}}
```

#### Tekdüze Testleri Çalıştırma

Yalnızca bu testi çalıştırmak için herhangi bir test fonksiyonunun adını `cargo test`'e geçirebiliriz:

```console
{{#include ../listings/ch11-writing-automated-tests/output-only-02-single-test/output.txt}}
```

Sadece `one_hundred` isimli test çalıştı; diğer iki test bu isimle eşleşmedi. 
Test çıktısı, sonunda filtrelenen `2`'yi görüntüleyerek çalışmayan daha fazla testimiz olduğunu bilmemizi sağlar.

Bu şekilde birden fazla testin adını belirtemeyiz; yalnızca `cargo test`'e verilen ilk değer kullanılır. 
Ancak birden fazla test çalıştırmanın bir yolu var.

#### Birden Fazla Test Çalıştırmak için Filtreleme

Bir test adının bir kısmını belirtebiliriz ve adı bu değerle eşleşen herhangi bir test çalıştırılır. 
Örneğin,testlerimizden ikisinin adı `add` içerdiğinden, bu ikisini `cargo test add` komutuyla çalıştırabiliriz:

```console
{{#include ../listings/ch11-writing-automated-tests/output-only-03-multiple-tests/output.txt}}
```

Bu komut, adında add olan tüm testleri çalıştırır ve `one_hundred` adlı testi filtreler. 
Ayrıca, bir testin göründüğü modülün testin adının bir parçası haline geldiğini unutmayın, 
bu nedenle modülün adına göre filtreleme yaparak bir modüldeki tüm testleri çalıştırabiliriz.

### Özel Olarak İstenmedikçe Bazı Testleri Yok Sayma

Bazen belirli birkaç testin yürütülmesi çok zaman alıcı olabilir, bu nedenle `cargo test`'in çoğu çalıştırması 
sırasında bunları hariç tutmak isteyebilirsiniz. Çalıştırmak istediğiniz tüm testleri argüman olarak listelemek yerine, 
zaman alan testleri burada gösterildiği gibi dışlamak için `ignore` niteliğini kullanarak açıklama ekleyebilirsiniz:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch11-writing-automated-tests/no-listing-11-ignore-a-test/src/lib.rs}}
```

`#[test]`'ten sonra, hariç tutmak istediğimiz teste `#[ignore]` satırını ekliyoruz. 
Testlerimizi çalıştırdığımızda `it_works` çalışıyor, ancak `expensive_test` çalışmıyor:

```console
{{#include ../listings/ch11-writing-automated-tests/no-listing-11-ignore-a-test/output.txt}}
```

`expensive_test` fonksiyonu `ignored` olarak listeleniyor. Yalnızca göz ardı edilen testleri çalıştırmak istiyorsak, 
`cargo test -- --ignored`'u kullanabiliriz:

```console
{{#include ../listings/ch11-writing-automated-tests/output-only-04-running-ignored/output.txt}}
```

Hangi testlerin çalışacağını kontrol ederek, `cargo test` sonuçlarınızın hızlı olmasını sağlayabilirsiniz. 
Yok sayılan testlerin sonuçlarını kontrol etmenin mantıklı olduğu bir noktaya geldiğinizde ve sonuçları beklemek için zamanınız 
olduğunda, bunun yerine `cargo test -- --ignored` komutunu çalıştırabilirsiniz. 
Yok sayılsın ya da sayılmasın tüm testleri çalıştırmak istiyorsanız, `cargo test -- --include-ignored` komutunu çalıştırabilirsiniz.
