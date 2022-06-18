## Test Organizasyonu

Bölümün başında da belirtildiği gibi, test karmaşık bir disiplindir ve farklı kişiler farklı terminoloji ve organizasyon kullanmaktadır. 
Rust topluluğu testleri iki ana kategoride ele alır: birim testleri ve entegrasyon testleri. Birim testleri küçük ve daha odaklıdır, 
her seferinde tek bir modülü izole olarak test eder ve özel arayüzleri test edebilir. Entegrasyon testleri kütüphanenizin tamamen dışındadır 
ve kodunuzu diğer harici kodlarla aynı şekilde kullanır, yalnızca genel arayüzü kullanır ve potansiyel olarak test başına birden fazla modülü test eder.

Her iki tür testin de yazılması, kütüphanenizin parçalarının ayrı ayrı ve birlikte beklediğiniz şeyi yaptığından emin olmak için önemlidir.

### Birim Testleri

Birim testlerinin amacı, kodun nerede beklendiği gibi çalışıp çalışmadığını hızlı bir şekilde belirlemek için her bir kod birimini 
kodun geri kalanından ayrı olarak test etmektir. Birim testlerini, test ettikleri kodun bulunduğu her dosyanın *src* dizinine koyarsınız. 
Alışılagelmiş yöntem, her dosyada test işlevlerini içerecek `tests` adında bir modül oluşturmak ve modüle `cfg(test)` ile açıklama eklemektir.

#### Testler Modülü ve `#[cfg(test)]`

`tests` modülündeki `#[cfg(test)]` ek açıklaması, Rust'a test kodunu yalnızca `cargo test`'i çalıştırdığınızda derlemesini ve 
çalıştırmasını söyler, `cargo build`'i çalıştırdığınızda değil. Bu, yalnızca kütüphaneyi derlemek istediğinizde derleme süresinden 
tasarruf sağlar ve testler dahil edilmediği için sonuçta derlenen yapıda yer tasarrufu sağlar. Entegrasyon testleri farklı bir dizine 
gittiği için `#[cfg(test)]` ek açıklamasına ihtiyaç duymadıklarını göreceksiniz. Ancak, birim testleri kodla aynı dosyalara girdiği için, 
derlenen sonuca dahil edilmemeleri gerektiğini belirtmek için `#[cfg(test)]` kullanacaksınız.

Bu bölümün ilk kısmında yeni `adder` projesini oluşturduğumuzda, Cargo'nun bu kodu bizim için oluşturduğunu hatırlayın:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch11-writing-automated-tests/listing-11-01/src/lib.rs}}
```

Bu kod otomatik olarak oluşturulan test modülüdür. `cfg` niteliği *yapılandırma* anlamına gelir ve 
Rust'a aşağıdaki öğenin yalnızca belirli bir yapılandırma seçeneği verildiğinde dahil edilmesi gerektiğini söyler. 
Bu durumda, *yapılandırma* seçeneği, testlerin derlenmesi ve çalıştırılması için Rust tarafından sağlanan `test`'tir. 
`cfg` niteliğini kullanarak, `cargo test` kodumuzu yalnızca testleri `cargo test` ile aktif olarak çalıştırırsak derler. 
Bu, `#[test]` ile açıklanan fonksiyonlara ek olarak, bu modül içinde olabilecek tüm yardımcı fonksiyonları içerir.

#### Özel Fonksiyonları Test Etme

Test topluluğu içinde gizli fonksiyonların doğrudan test edilip edilmemesi gerektiği konusunda tartışmalar vardır ve 
diğer diller gizli fonksiyonları test etmeyi zorlaştırır veya imkansız hale getirir. Hangi test ideolojisine bağlı olursanız olun, 
Rust'ın gizlilik kuralları gizli fonksiyonları test etmenize izin verir. Liste 11-12'deki kodu gizli fonksiyon `internal_adder` ile 
birlikte düşünün.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch11-writing-automated-tests/listing-11-12/src/lib.rs}}
```

<span class="caption">Liste 11-12: Özel bir fonksiyonu test etme</span>

`internal_adder` fonksiyonunun `pub` olarak işaretlenmediğine dikkat edin. Testler sadece Rust kodudur ve `test` modülü sadece başka bir modüldür. 
[“Modül Ağacında Bir Öğeye Başvurma Yolları”][paths]<!-- ignore --> bölümünde tartıştığımız gibi, alt modüllerdeki öğeler ata 
modüllerindeki öğeleri kullanabilir. Bu testte, `test` modülünün ebeveyninin tüm öğelerini `use super::*` ile kapsama alırız ve 
ardından test `internal_adder`'ı çağırabilir. Gizli fonksiyonların test edilmemesi gerektiğini düşünüyorsanız, 
Rust'ta sizi bunu yapmaya zorlayacak hiçbir şey yoktur.

### Entegrasyon Testleri

Rust'ta entegrasyon testleri kütüphanenizin tamamen dışındadır. Kütüphanenizi diğer kodlarla aynı şekilde kullanırlar, 
yani yalnızca kütüphanenizin genel API'sinin bir parçası olan fonksiyonları çağırabilirler. Amaçları, kütüphanenizin birçok parçasının 
birlikte doğru çalışıp çalışmadığını test etmektir. Kendi başlarına doğru çalışan kod birimleri entegre edildiğinde sorun yaşayabilir, 
bu nedenle entegre kodun test kapsamı da önemlidir. Entegrasyon testleri oluşturmak için öncelikle bir test dizinine ihtiyacınız vardır.

#### *tests* Dizini

*tests* dizinindeki her dosya ayrı bir kasadır, bu nedenle kütüphanemizi her test kasasının kapsamına getirmemiz gerekir. 
Bu nedenle, birim testlerinde ihtiyaç duymadığımız `use adder`'ı kodun en üstüne ekliyoruz.

*tests/integration_test.rs* içindeki herhangi bir koda `#[cfg(test)]` ile açıklama eklememize gerek yok.
Cargo, *tests* dizinini özel olarak ele alır ve bu dizindeki dosyaları yalnızca `cargo test`'i çalıştırdığımızda derler.
Şimdi `cargo test`'i çalıştırın:

<span class="filename">Dosya adı: tests/integration_test.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch11-writing-automated-tests/listing-11-13/tests/integration_test.rs}}
```

<span class="caption">Liste 11-13: `adder` kasasındaki bir fonksiyonun 
entegrasyon testi</span>

Çıktının üç bölümü birim testlerini, entegrasyon testini ve dokümantasyon testlerini içerir. 
Birim testleri için ilk bölüm gördüğümüzle aynıdır: her birim testi için bir satır (Liste 11-12'de eklediğimiz `internal` adlı bir satır) 
ve ardından birim testleri için bir özet satırı.

Entegrasyon testleri bölümü `Running tests/integration_test.rs` satırıyla başlar. Ardından, bu entegrasyon testindeki her bir test 
fonksiyonu için bir satır ve `Doc-tests adder` bölümü başlamadan hemen önce entegrasyon testinin sonuçları için bir özet satırı vardır.

Her entegrasyon testi dosyasının kendi bölümü vardır, bu nedenle *tests* dizinine daha fazla dosya eklersek, 
daha fazla entegrasyon testi bölümü olacaktır.

Test fonksiyonunun adını `cargo test`'e argüman olarak belirterek belirli bir entegrasyon testi fonksiyonunu çalıştırabiliriz. 
Belirli bir entegrasyon testi dosyasındaki tüm testleri çalıştırmak için, `cargo test`'in `--test` argümanını ve ardından dosyanın adını kullanın:

```console
{{#include ../listings/ch11-writing-automated-tests/output-only-05-single-integration/output.txt}}
```

Bu komut yalnızca *tests/integration_test.rs* dosyasındaki testleri çalıştırır.

#### Entegrasyon Testlerinde Alt Modüller

Daha fazla entegrasyon testi ekledikçe, bunları düzenlemeye yardımcı olmak için *tests* dizininde daha fazla dosya oluşturmak isteyebilirsiniz; 
örneğin, test fonksiyonlarını test ettikleri işlevselliğe göre gruplayabilirsiniz. Daha önce de belirtildiği gibi, 
*tests* dizinindeki her dosya kendi içinde ayrı kasa olarak derlenir, bu da son kullanıcıların kasanızı kullanma şeklini daha yakından 
taklit etmek için ayrı kapsamlar oluşturmak için kullanışlıdır. Ancak bu, Bölüm 7'de kodu modüllere ve dosyalara nasıl ayıracağınızı 
öğrendiğiniz gibi, *tests* dizinindeki dosyaların *src*'deki dosyalarla aynı davranışı paylaşmadığı anlamına gelir.

*tests* dizini dosyalarının farklı davranışı en çok, birden fazla entegrasyon test dosyasında kullanılacak bir dizi yardımcı 
fonksiyonunuz olduğunda ve bunları ortak bir modüle çıkarmak için 
Bölüm 7'deki [“Modülleri Farklı Dosyalara Ayırma”][separating-modules-into-files]<!-- ignore --> bölümündeki adımları izlemeye 
çalıştığınızda fark edilir. Örneğin, *tests/common.rs* dosyasını oluşturur ve içine `setup` adında bir fonksiyon yerleştirirsek, 
`setup` dosyasına test dosyalarındaki test fonksiyonlarından çağırmak istediğimiz bazı kodlar ekleyebiliriz:

<span class="filename">Dosya adı: tests/common.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch11-writing-automated-tests/no-listing-12-shared-test-code-problem/tests/common.rs}}
```

Testleri tekrar çalıştırdığımızda, *common.rs* dosyası için test çıktısında yeni bir bölüm göreceğiz, 
ancak bu dosya herhangi bir test fonksiyonu içermiyor ve `setup` fonksiyonunu herhangi bir yerden çağırmadık:

```console
{{#include ../listings/ch11-writing-automated-tests/no-listing-12-shared-test-code-problem/output.txt}}
```

Test sonuçlarında yaygın olarak `running 0 tests`'in görüntülenmesi istediğimiz şey değildi. 
Sadece bazı kodları diğer entegrasyon test dosyalarıyla paylaşmak istedik.

`common`'ın test çıktısında görünmesini önlemek için *tests/common.rs* oluşturmak yerine *tests/common/mod.rs* oluşturacağız. 
Bu, Rust'ın da anladığı alternatif bir adlandırma kuralıdır. Dosyayı bu şekilde adlandırmak, Rust'a `common` modülünü bir entegrasyon test 
dosyası olarak ele almamasını söyler. `setup` fonksiyonu kodunu *tests/common/mod.rs* dosyasına taşıdığımızda ve *tests/common.rs* dosyasını sildiğimizde, test çıktısındaki bölüm artık görünmeyecektir. *tests* dizininin alt dizinlerinde yer alan dosyalar ayrı kasalar olarak derlenmez 
veya test çıktısında bölümlere sahip olmaz.

*tests/common/mod.rs* dosyasını oluşturduktan sonra, bunu entegrasyon test dosyalarından herhangi birinde modül olarak kullanabiliriz. 
Aşağıda, *tests/integration_test.rs* dosyasındaki `it_adds_two` testinden `setup` fonksiyonunun çağrılmasına bir örnek verilmiştir:

<span class="filename">Dosya adı: tests/integration_test.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch11-writing-automated-tests/no-listing-13-fix-shared-test-code-problem/tests/integration_test.rs}}
```

`mod common;` bildiriminin Liste 7-21'de gösterdiğimiz modül bildirimiyle aynı olduğuna dikkat edin. 
Daha sonra test fonksiyonunda `common::setup()` fonksiyonunu çağırabiliriz.

#### İkili Kasalar için Entegrasyon Testleri

Projemiz yalnızca bir *src/main.rs* dosyası içeren ve bir *src/lib.rs* dosyasına sahip olmayan ikili bir kasaysa, 
*tests* dizininde entegrasyon testleri oluşturamaz ve *src/main.rs* dosyasında tanımlanan fonksiyonları bir `use` ifade yapısı ile kapsama alamayız. 
Yalnızca kütüphane kasaları diğer kasaların kullanabileceği fonksiyonları açığa çıkarır; ikili kasalar kendi başlarına çalıştırılmak üzere tasarlanmıştır.

Bu, bir ikili dosya sağlayan Rust projelerinin *src/lib.rs* dosyasında bulunan mantığı çağıran basit bir 
*src/main.rs* dosyasına sahip olmasının nedenlerinden biridir. Bu yapıyı kullanarak entegrasyon testleri, 
önemli fonksiyonları kullanılabilir hale getirmek için kütüphane kasasını kullanarak test edebilir. 
Önemli işlevsellik çalışırsa, *src/main.rs* dosyasındaki az miktarda kod da çalışacaktır ve bu az miktarda kodun test edilmesi gerekmez.

## Özet

Rust'ın test özellikleri, siz değişiklik yapsanız bile kodun beklediğiniz gibi çalışmaya devam etmesini sağlamak 
için kodun nasıl çalışması gerektiğini belirtmenin bir yolunu sunar. Birim testleri, bir kütüphanenin farklı bölümlerini 
ayrı ayrı çalıştırır ve özel uygulama ayrıntılarını test edebilir. Entegrasyon testleri, kütüphanenin birçok parçasının 
birlikte doğru çalışıp çalışmadığını kontrol eder ve kodu harici kodun kullanacağı şekilde test etmek için kütüphanenin 
genel API'sini kullanır. Rust'ın tür sistemi ve sahiplik kuralları bazı hata türlerini önlemeye yardımcı olsa da,
kodunuzun nasıl davranması beklendiğiyle ilgili mantık hatalarını azaltmak için testler hala önemlidir.

Bu bölümde ve önceki bölümlerde öğrendiğiniz bilgileri bir proje üzerinde çalışmak için birleştirelim!

[paths]: ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html
[separating-modules-into-files]:
ch07-05-separating-modules-into-different-files.html
