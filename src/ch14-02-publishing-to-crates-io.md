## Crates.io'da Kasa Yayınlama

Biz [crates.io](https://crates.io/)<!-- ignore -->'daki paketleri projemizin bağımlılıkları olarak kullandık, 
ancak siz de kendi paketlerinizi yayınlayarak kodunuzu diğer insanlarla paylaşabilirsiniz. [crates.io](https://crates.io/)<!-- ignore -->'daki kasa kaydı, 
paketlerinizin kaynak kodunu dağıtır, bu nedenle öncelikle açık kaynak kodunu barındırır.

Rust ve Cargo, yayınladığınız paketin insanlar tarafından bulunmasını ve kullanılmasını kolaylaştıran özelliklere sahiptir. 
Şimdi bu özelliklerden bazılarından bahsedeceğiz ve ardından bir paketin nasıl yayınlanacağını açıklayacağız.

### Faydalı Dokümantasyon Yorumları Yapmak

Paketlerinizi doğru bir şekilde belgelendirmek, diğer kullanıcıların bunları nasıl ve ne zaman kullanacaklarını bilmelerine 
yardımcı olacaktır, bu nedenle belge yazmak için zaman ayırmaya değer. Bölüm 3'te, iki eğik çizgi (`//`) kullanarak Rust 
kodunu nasıl yorumlayacağımızı tartıştık. Rust ayrıca, HTML dokümantasyonu oluşturacak, dokümantasyon 
yorumu olarak bilinen, dokümantasyon için özel bir yorum türüne sahiptir. HTML, kasanızın nasıl süreklendiğinden ziyade 
kasanızın nasıl kullanılacağını bilmek isteyen programcılara yönelik genel API öğeleri için dokümantasyon yorumlarının içeriğini 
görüntüler.

Dokümantasyon yorumları iki yerine üç eğik çizgi (`///`) kullanır ve metni biçimlendirmek için *Markdown* 
gösterimini destekler. Dokümantasyon yorumlarını belgeledikleri öğeden hemen önce yerleştirin. 
Liste 14-1, `my_crate` adlı kasadaki `add_one` fonksiyonu için dokümantasyon yorumlarını göstermektedir.

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch14-more-about-cargo/listing-14-01/src/lib.rs}}
```

<span class="caption">Listing 14-1: Fonksiyon için dokümantasyon yorumu</span>

Burada, `add_one` fonksiyonunun ne işe yaradığına dair bir açıklama veriyoruz, `Examples` başlıklı bir bölüm 
açıyoruz ve ardından `add_one` fonksiyonunun nasıl kullanılacağını gösteren bir kod sunuyoruz. 
Bu dokümantasyon yorumundan, `cargo doc` çalıştırarak HTML dokümantasyonunu oluşturabiliriz. 
Bu komut, Rust ile birlikte dağıtılan `rustdoc` aracını çalıştırır ve oluşturulan HTML belgelerini 
*target/doc* dizinine koyar.

Kolaylık sağlamak için, `cargo doc --open` komutunu çalıştırmak mevcut kasanızın dokümantasyonu için HTML oluşturacak 
(ayrıca kasanızın tüm bağımlılıkları için dokümantasyon) ve sonucu bir web tarayıcısında açacaktır.
`add_one` fonksiyonuna gidin ve Şekil 14-1'de gösterildiği gibi dokümantasyon yorumlarındaki metnin nasıl oluşturulduğunu 
göreceksiniz:

<img alt="`my_crate`'nin `add_one` fonksiyonu için işlenmiş HTML dokümantasyonu" src="img/trpl14-01.png" class="center" />

<span class="caption">Şekil 14-1: `add_one` fonksiyonu için HTML dokümantasyonu</span>

#### Sıkça Kullanılan Bölümler

HTML'de “Examples” başlıklı bir bölüm oluşturmak için Liste 14-1'deki `# Examples` başlığını kullandık. 
İşte kasa yazarlarının belgelerinde yaygın olarak kullandıkları diğer bazı bölümler:

* **Panikler**: Belgelenen fonksiyonun panik yapabileceği senaryolar. Programlarının paniklemesini istemeyen fonksiyonu 
  çağıranlar, bu durumlarda fonksiyonu çağırmadıklarından emin olmalıdırlar.
* **Hatalar**: Fonksiyon `Result` döndürüyorsa, oluşabilecek hata türlerini ve hangi koşulların bu hataların  
  döndürülmesine neden olabileceğini açıklamak, arayanlara yardımcı olabilir, böylece farklı hata türlerini farklı 
  şekillerde ele almak için kod yazabilirler.
* **Güvenlik**: Eğer fonksiyon çağrılması güvenli değilse (güvensizliği Bölüm 19'da tartışacağız), fonksiyonun neden 
  güvensiz olduğunu açıklayan ve fonksiyonun çağıranların uymasını beklediği değişmezleri kapsayan bir bölüm olmalıdır.

Çoğu dokümantasyon açıklamasında bu bölümlerin hepsine gerek yoktur, ancak bu, kodunuzun kullanıcıların bilmek 
isteyeceği yönlerini size hatırlatmak için iyi bir kontrol listesidir.

#### Test Olarak Dokümantasyon Yorumları

Belge açıklamalarınıza örnek kod blokları eklemek, kütüphanenizin nasıl kullanılacağını göstermeye yardımcı olabilir ve 
bunu yapmanın ek bir avantajı vardır: `cargo test`'i çalıştırmak, belgelerinizdeki kod örneklerini test olarak 
çalıştıracaktır! Hiçbir şey örnekli dokümantasyondan daha iyi olamaz. Ancak hiçbir şey, dokümantasyonun yazılmasından
bu yana kod değiştiği için çalışmayan örneklerden daha kötü olamaz. Liste 14-1'deki `add_one` fonksiyonunun dokümantasyonu ile
`cargo test`'i çalıştırırsak, test sonuçlarında aşağıdaki gibi bir bölüm görürüz:

<!-- manual-regeneration
cd listings/ch14-more-about-cargo/listing-14-01/
cargo test
copy just the doc-tests section below
-->

```text
   Doc-tests my_crate

running 1 test
test src/lib.rs - add_one (line 5) ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.27s
```

Şimdi fonksiyonu ya da örneği değiştirirsek, örnekteki `assert_eq!` panik yapar ve `cargo test`'i tekrar çalıştırırsak, 
dokümantasyon testlerinin örnek ve kodun birbiriyle senkronize olmadığını yakaladığını göreceğiz!

#### İçerdiği Öğeleri Yorumlama

Dokümantasyon yorum satırı (`//!`) stili, belgeleri yorumları takip eden öğeler yerine yorumları içeren öğeye ekler. 
Bu dokümantasyon yorumları genellikle kasa kök dosyasının içinde (geleneksel olarak *src/lib.rs*) veya bir modülün 
içinde kasayı veya modülü bir bütün olarak belgelemek için kullanırız.

Örneğin, `add_one` fonksiyonunu içeren `my_crate` kasasının amacını açıklayan belgeler eklemek için, 
Liste 14-2'de gösterildiği gibi *src/lib.rs* dosyasının başına `//!` ile başlayan belge yorumları ekleriz:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch14-more-about-cargo/listing-14-02/src/lib.rs:here}}
```

<span class="caption">Liste 14-2: Bir bütün olarak `my_crate` kasası için dokümantasyon</span>

Son satırdan sonra `//!` ile başlayan herhangi bir kod olmadığına dikkat edin. 
Yorumlara `///` yerine `//!` ile başladığımız için, bu yorumu takip eden bir öğe yerine bu yorumu içeren öğeyi 
belgeliyoruz. Bu durumda, bu öğe kasa kökü olan *src/lib.rs* dosyasıdır. Bu yorumlar tüm kasayı tanımlar.

`cargo doc --open` komutunu çalıştırdığımızda, bu yorumlar Şekil 14-2'de gösterildiği gibi `my_crate` dokümantasyonunun 
ön sayfasında kasadaki genel öğelerin listesinin üzerinde görüntülenecektir:

<img alt="Bir bütün olarak kasa için bir yorum içeren işlenmiş HTML dokümantasyonu" src="img/trpl14-02.png" class="center" />

Öğeler içindeki dokümantasyon yorumları özellikle kasaları ve modülleri tanımlamak için kullanışlıdır.
Kullanıcılarınızın kasanın organizasyonunu anlamalarına yardımcı olmak için konteynerin genel amacını açıklamak
için bunları kullanın.


### `pub use` ile Kullanışlı Genel API'yi Dışa Aktarma

Genel API'nizin yapısı, bir kasa yayınlarken göz önünde bulundurulması gereken önemli bir husustur.
Kasanızı kullanan kişiler yapıya sizden daha az aşinadır ve kasanızın büyük bir modül hiyerarşisine sahipse
kullanmak istedikleri parçaları bulmakta zorluk çekebilirler.

Bölüm 7'de, `pub` anahtar sözcüğünü kullanarak öğeleri nasıl herkese açık hale getireceğimizi ve `use` anahtar sözcüğünü
kullanarak öğeleri bir kapsama nasıl dahil edeceğimizi ele almıştık. Ancak, bir kasa geliştirirken size mantıklı gelen yapı,
kullanıcılarınız için çok uygun olmayabilir. Yapılarınızı birden fazla seviye içeren bir hiyerarşide
düzenlemek isteyebilirsiniz, ancak bu durumda hiyerarşinin derinliklerinde tanımladığınız bir türü kullanmak isteyen
kişiler bu türün var olduğunu bulmakta zorlanabilir. Ayrıca, `use my_crate::UsefulType;` yerine use
`my_crate::some_module::another_module::UsefulType;` girmek zorunda kalmaktan da rahatsız olabilirler.

İyi haber şu ki, yapı başkalarının başka bir kütüphaneden kullanması için uygun değilse, iç organizasyonunuzu
yeniden düzenlemeniz gerekmez: bunun yerine, `pub use` kullanarak özel yapınızdan farklı bir genel yapı
oluşturmak için öğeleri yeniden dışa aktarabilirsiniz. Yeniden dışa aktarma, bir konumdaki herkese açık bir
öğeyi alır ve sanki diğer konumda tanımlanmış gibi başka bir konumda herkese açık hale getirir.

Örneğin, sanatsal kavramları modellemek için `art` adında bir kütüphane oluşturduğumuzu varsayalım.
Bu kütüphanede iki modül vardır: `PrimaryColor` ve `SecondaryColor` adında iki `enum` içeren bir `kinds` modülü ve
Liste 14-3'te gösterildiği gibi `mix` adında bir fonksiyon içeren bir `utils` modülü:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,noplayground,test_harness
{{#rustdoc_include ../listings/ch14-more-about-cargo/listing-14-03/src/lib.rs:here}}
```

<span class="caption">Liste 14-3: `kinds` ve `utils` halinde düzenlenmiş öğeler içeren bir `art` kütüphanesi</span>

Şekil 14-3, bu kasa için `cargo doc` tarafından oluşturulan belgelerin ön sayfasının nasıl görüneceğini 
göstermektedir:


<img alt="`art` kasası için `kinds` ve `utils` modüllerini listeleyen işlenmiş dokümantasyonlar" src="img/trpl14-03.png" class="center" />

<span class="caption">Şekil 14-3: `kinds` ve `utils` modüllerini listeleyen `art` dokümantasyonunun ön sayfası</span>

`PrimaryColor` ve `SecondaryColor` türlerinin ön sayfada listelenmediğine ve `mix` fonksiyonunun da bulunmadığına 
dikkat edin. Bunları görmek için türlere ve yardımcı programlara tıklamamız gerekiyor.

Bu kütüphaneye bağlı olan başka bir kasa, şu anda tanımlanmış olan modül yapısını belirterek,
`art`'taki öğeleri kapsama getiren `use` ifade yapılarına ihtiyaç duyacaktır. Liste 14-4, `art` kasasındaki
`PrimaryColor` ve `mix` öğelerini kullanan bir kasa örneğini göstermektedir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch14-more-about-cargo/listing-14-04/src/main.rs}}
```

<span class="caption">Liste 14-4: İç yapısı dışa aktarılmış `art` kasasının öğelerini kullanan bir kasa</span>

Liste 14-4'teki `art` kasasını kullanan kodun yazarı,
`PrimaryColor`'ın `kinds` modülünde ve `mix`'in `utils` modülünde olduğunu anlamak zorunda kalmıştır.
`art` kasasının modül yapısı, onu kullananlardan ziyade `art` kasası üzerinde çalışan geliştiriciler için
daha önemlidir. İç yapı, `art` kasasının nasıl kullanılacağını anlamaya çalışan biri için herhangi 
bir yararlı bilgi içermez, aksine kafa karışıklığına neden olur, çünkü onu kullanan geliştiriciler nereye bakacaklarını 
bulmak ve use deyimlerinde modül adlarını belirtmek zorundadır.

Dahili organizasyonu genel API'den kaldırmak için, Liste 14-3'teki `art` kasa kodunu değiştirerek, 
Liste 14-5'te gösterildiği gibi üst düzeydeki öğeleri yeniden dışa aktarmak için `pub use` ifade yapısını
ekleyebiliriz:

<span class="filename">Dosya adı: src/lib.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch14-more-about-cargo/listing-14-05/src/lib.rs:here}}
```

<span class="caption">Liste 14-5: Öğeleri yeniden dışa aktarmak için `pub use` ifade yapıları ekleme</span>

`cargo doc`'un bu kasa için oluşturduğu API dokümantasyonu artık Şekil 14-4'te gösterildiği gibi ön 
sayfada yeniden dışa aktarmaları listeleyecek ve bağlayacak, böylece `PrimaryColor` ve
`SecondaryColor` türleri ile `mix` fonksiyonunun bulunması kolaylaşacaktır.

<img alt="Ön sayfadaki yeniden dışa aktarımlarla birlikte `art` kasası için oluşturulmuş dokümantasyonlar" src="img/trpl14-04.png" class="center" />

<span class="caption">Şekil 14-4: Yeniden dahil etmeyi listeleyen `art` dokümantasyonunun ön sayfası</span>

`art` kasası kullanıcıları, Liste 14-4'te gösterildiği gibi Liste 14-3'teki dahili yapıyı
görmeye ve kullanmaya devam edebilir ya da Liste 14-6'da gösterildiği gibi Liste 14-5'teki
daha kullanışlı yapıyı kullanabilirler:
<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch14-more-about-cargo/listing-14-06/src/main.rs:here}}
```

<span class="caption">Liste 14-6: `art` kasasından dahil edilen öğeleri kullanan bir program</span>

İç içe geçmiş çok sayıda modülün bulunduğu durumlarda, en üst seviyedeki türlerin `pub` kullanımıyla 
yeniden dışa aktarılması, kasayı kullanan kişilerin deneyiminde önemli bir fark yaratabilir.

Kullanışlı bir genel API yapısı oluşturmak bilimden çok bir sanattır ve kullanıcılarınız 
için en iyi çalışan API'yi bulmak için yineleme yapabilirsiniz. `pub` kullanımını seçmek, kasanızı dahili 
olarak nasıl yapılandırdığınız konusunda size esneklik sağlar ve bu dahili yapıyı kullanıcılarınıza 
sunduğunuzdan ayırır. İç yapılarının genel API'lerinden farklı olup olmadığını görmek için yüklediğiniz 
bazı kasaların kodlarına bakın.

### Crates.io Hesabı Oluşturma

Herhangi bir kasayı yayınlayabilmeniz için önce [crates.io](https://crates.io/)<!-- ignore -->'da bir hesap
oluşturmanız ve bir API anahtarı almanız gerekir. Bunu yapmak için [crates.io](https://crates.io/)<!-- ignore --> adresindeki
ana sayfayı ziyaret edin ve bir GitHub hesabı aracılığıyla oturum açın. (GitHub hesabı şu anda bir gerekliliktir, 
ancak site gelecekte hesap oluşturmanın diğer yollarını da gelecekte destekleyebilir). 
Giriş yaptıktan sonra [https://crates.io/me/](https://crates.io/me/)<!-- ignore --> adresinden hesap 
ayarlarınızı ziyaret edin ve API anahtarınızı alın. Ardından `cargo login` komutunu API 
anahtarınızla aşağıdaki gibi çalıştırın:

```console
$ cargo login abcdefghijklmnopqrstuvwxyz012345
```

Bu komut Cargo'ya API token'ınızı bildirecek ve yerel olarak *~/.cargo/credentials* içinde saklayacaktır. 
Bu belirtecin bir sır olduğunu unutmayın: başkasıyla paylaşmayın. Herhangi bir nedenle herhangi 
biriyle paylaşırsanız, iptal etmeli ve [crates.io](https://crates.io/)<!-- ignore
-->'dan yeni bir token oluşturmalısınız.

### Yeni Bir Kasaya Meta Veri Ekleme

Diyelim ki yayınlamak istediğiniz bir kasanız var. Yayınlamadan önce, 
kasanın *Cargo.toml* dosyasının `[package]` bölümüne bazı meta veriler eklemeniz gerekir.

Kasanızın benzersiz bir isme ihtiyacı olacaktır. Yerel olarak bir kasa üzerinde çalışırken, 
sandığa istediğiniz adı verebilirsiniz. Ancak [crates.io](https://crates.io/)<!-- ignore -->'daki kasa
adları ilk gelene ilk hizmet esasına göre tahsis edilir. Bir kasa adı alındıktan sonra, 
başka hiç kimse bu adla bir kasa yayınlayamaz. Bir kasa yayınlamaya çalışmadan önce, 
kullanmak istediğiniz adı arayın. İsim kullanılmışsa, başka bir isim bulmanız ve 
*Cargo.toml* dosyasında `[package]` bölümü altındaki `name` alanını, yayınlama için yeni ismi 
kullanacak şekilde düzenlemeniz gerekecektir:

<span class="filename">Dosya adı: Cargo.toml</span>

```toml
[package]
name = "guessing_game"
```

Benzersiz bir ad seçmiş olsanız bile, bu noktada kasayı yayınlamak için `cargo publish`'i çalıştırdığınızda, 
bir uyarı ve ardından bir hata alırsınız:

<!-- manual-regeneration
cd listings/ch14-more-about-cargo/listing-14-01/
cargo publish
copy just the relevant lines below
-->

```console
$ cargo publish
    Updating crates.io index
warning: manifest has no description, license, license-file, documentation, homepage or repository.
See https://doc.rust-lang.org/cargo/reference/manifest.html#package-metadata for more info.
--snip--
error: failed to publish to registry at https://crates.io

Caused by:
  the remote server responded with an error: missing or empty metadata fields: description, license. Please see https://doc.rust-lang.org/cargo/reference/manifest.html for how to upload metadata
```

Bu hata, bazı önemli bilgilerin eksik olmasından kaynaklanmaktadır: 
insanların kasanızın ne işe yaradığını ve hangi koşullar altında kullanabileceklerini bilmeleri için 
bir açıklama ve lisans gereklidir. *Cargo.toml* dosyasına sadece bir veya iki cümlelik bir açıklama ekleyin, 
çünkü bu açıklama arama sonuçlarında kasanızla birlikte görünecektir. `license` alanı için bir lisans 
tanımlayıcı değeri vermeniz gerekir. [Linux Foundation’un Software Package Data Exchange (SPDX)][spdx] listesi 
bu değer için kullanabileceğiniz tanımlayıcıları listeler. Örneğin, kasanızı MIT Lisansı kullanarak 
lisansladığınızı belirtmek için `MIT` tanımlayıcısını ekleyin:

<span class="filename">Dosya adı: Cargo.toml</span>

```toml
[package]
name = "guessing_game"
license = "MIT"
```

SPDX'te görünmeyen bir lisans kullanmak istiyorsanız, bu lisansın metnini bir dosyaya yerleştirmeniz, 
dosyayı projenize dahil etmeniz ve ardından lisans anahtarını kullanmak yerine bu dosyanın adını belirtmek 
için `license-file` kullanmanız gerekir.

Projeniz için hangi lisansın uygun olduğuna ilişkin rehberlik bu kitabın kapsamı dışındadır. 
Rust topluluğundaki birçok kişi, `MIT VEYA Apache-2.0` ikili lisansını kullanarak projelerini Rust ile 
aynı şekilde lisanslar. Bu uygulama, projeniz için birden fazla lisansa sahip olmak için `OR` ile ayrılmış 
birden fazla lisans tanımlayıcısı da belirtebileceğinizi göstermektedir.

Benzersiz bir ad, sürüm, açıklamanız ve bir lisans eklendiğinde, yayınlamaya hazır bir proje için 
*Cargo.toml* dosyası aşağıdaki gibi görünebilir:

<span class="filename">Dosya adı: Cargo.toml</span>

```toml
[package]
name = "guessing_game"
version = "0.1.0"
edition = "2021"
description = "A fun game where you guess what number the computer has chosen."
license = "MIT OR Apache-2.0"

[dependencies]
```

[Cargo’nun dokümantasyonunda](https://doc.rust-lang.org/cargo/), başkalarının kasanızı daha kolay 
keşfedip kullanabilmesini sağlamak için belirtebileceğiniz diğer meta veriler açıklanmaktadır.

### Crates.io'da Yayınlama Süreci

Artık bir hesap oluşturduğunuza, API token'ınızı kaydettiğinize, kasanız için bir ad seçtiğinize 
ve gerekli meta verileri belirlediğinize göre yayınlamaya hazırsınız! Bir kasa yayınlamak, başkalarının
kullanması için [crates.io](https://crates.io/)<!-- ignore -->'ya belirli bir sürümü yükler.

Dikkatli olun, çünkü yayınlama kalıcıdır. Sürümün üzerine asla yazılamaz ve kod silinemez.
[crates.io](https://crates.io/)<!-- ignore -->'nun en önemli amaçlarından biri kalıcı bir kod arşivi 
olarak hareket etmektir, böylece [crates.io](https://crates.io/)<!-- ignore -->'daki kasalara bağlı olan 
tüm projelerin yapıları çalışmaya devam edecektir. Sürüm silme işlemlerine izin vermek 
bu hedefin gerçekleştirilmesini imkansız hale getirecektir. Bununla birlikte, 
yayınlayabileceğiniz kasa sürümlerinin sayısında bir sınır yoktur.

`cargo publish` komutunu tekrar çalıştırın. Şimdi çalışması gerekir:

<!-- manual-regeneration
go to some valid crate, publish a new version
cargo publish
copy just the relevant lines below
-->

```console
$ cargo publish
    Updating crates.io index
   Packaging guessing_game v0.1.0 (file:///projects/guessing_game)
   Verifying guessing_game v0.1.0 (file:///projects/guessing_game)
   Compiling guessing_game v0.1.0
(file:///projects/guessing_game/target/package/guessing_game-0.1.0)
    Finished dev [unoptimized + debuginfo] target(s) in 0.19s
   Uploading guessing_game v0.1.0 (file:///projects/guessing_game)
```

Tebrikler! Artık kodunuzu Rust topluluğu ile paylaştınız ve herkes kasanızı kolayca 
projelerinin bir bağımlılığı olarak ekleyebilir.

### Mevcut Bir Kasanın Yeni Sürümünü Yayınlama

Sandığınızda değişiklikler yaptığınızda ve yeni bir sürüm yayınlamaya hazır olduğunuzda, 
*Cargo.toml* dosyanızda belirtilen sürüm değerini değiştirir ve yeniden yayınlarsınız. 
Yaptığınız değişiklik türlerine göre uygun bir sonraki sürüm numarasının ne olduğuna 
karar vermek için [Anlamsal Sürüm Oluşturma][semver] kurallarını kullanın. 
Ardından yeni sürümü göndermek için `cargo publish`'ı çalıştırın.

<!-- Old link, do not remove -->
<a id="removing-versions-from-cratesio-with-cargo-yank"></a>

### `cargo yank` ile Crates.io'dan Sürümleri Kaldırma

Bir sandığın önceki sürümlerini kaldıramasanız da, gelecekteki projelerin 
bunları yeni bir bağımlılık olarak eklemesini önleyebilirsiniz. 
Bu, bir kasa sürümü bir nedenle bozulduğunda kullanışlıdır. 
Bu gibi durumlarda, Cargo kasa sürümünün kaldırılmasını destekler.

Bir sürümü çekmek, yeni projelerin o sürüme bağlı olmasını engellerken, 
ona bağlı olan tüm mevcut projelerin devam etmesine izin verir. 
Esasen, çekme, *Cargo.lock*'a sahip tüm projelerin bozulmayacağı ve gelecekte üretilen herhangi 
bir *Cargo.lock* dosyasının çekilmiş sürümü kullanmayacağı anlamına gelir.

Bir kasanın sürümünü çekmek için, daha önce yayınladığınız kasanın dizininde `cargo yank` 
komutunu çalıştırın ve hangi sürümü çekmek istediğinizi belirtin:

```console
$ cargo yank --vers 1.0.1
```

Ayrıca komuta `--undo` ekleyerek bir çekme işlemini geri alabilir ve projelerin yeniden 
bir sürüme bağlı olarak başlamasına izin verebilirsiniz:


```console
$ cargo yank --vers 1.0.1 --undo
```

Bir çekme herhangi bir kodu silmez. Örneğin, yanlışlıkla yüklenen sırları silemez. 
Böyle bir durumda, bu sırları derhal sıfırlamanız gerekir.

[spdx]: http://spdx.org/licenses/
[semver]: http://semver.org/
