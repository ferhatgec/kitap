## Merhaba, Cargo!

Cargo, Rust'ın yapı sistemi ve paket yöneticisidir. Çoğu Rustsever, Rust projelerini yönetmek için bu aracı kullanır çünkü Cargo, kodunuzu oluşturmak, kodunuzun bağlı olduğu kitaplıkları indirmek ve bu kitaplıkları derlemek gibi birçok görevi sizin yerinize gerçekleştirir. 

Şimdiye kadar yazdığımız gibi en basit Rust programlarının herhangi bir bağımlılığı yoktur. Yani “Hello, world!” projeniz Cargo ile oluşturulduğunda, yalnızca Cargo'nun kodunuzu oluşturmayı yöneten bölümünü kullanır. 
Daha karmaşık Rust programları yazdıkça, bağımlılıklar ekleyeceksiniz ve Cargo kullanarak bir projeye başlarsanız, bağımlılıkları eklemek çok daha kolay olacaktır.

Rust projelerinin büyük çoğunluğu Kargo kullandığından, bu kitabın tamamında sizin de Cargo kullandığınız
varsayılır. Eğer resmi yükleyicileri [“Yükleme”][installation]<!-- ignore --> kısmından çekerek çalıştırdıysanız,
Cargp önceden yüklü olarak gelmiş olur. Eğer farklı yollarla Rust'ı yüklediyseniz, Cargo'nun yüklü olup olmadığını şu kodu 
uçbiriminizde çalıştırarak öğrenebilirsiniz:

```console
$ cargo --version
```

Eğer sürüm numarasını görüyorsanız, zaten yüklüdür! Eğer hata görüyorsanız, mesela `komut bulunamadı`, yükleme metodunuzun 
dokümantasyonuna bakarak Cargo'yu nasıl ayrı olarak kurabileceğinizi bulabilirsiniz.

### Cargo'yla Proje Oluşturmak 

Hadi Cargo ile yeni proje oluşturalım ve orijinal “Hello, world!” projesiyle olan farklılıklarına bir göz gezdirelim. 
Projelerinizi tuttuğunuz (örneğin *projects* dizini) dizine ya da kodunuzu tutmak istediğiniz dizinde 
herhangi bir işletim sistemi farklılığı gözetmeksizin şu komutu çalıştırın:

```console
$ cargo new hello_cargo
$ cd hello_cargo
```

İlk komut *hello_cargo* adında yeni bir dizin oluşturdu. Biz projemize *hello_cargo* adını vermek istedik ve Cargo 
aynı addaki dizine temel dosyaları oluşturdu. 

*hello_cargo* dizinine gidin ve dosyaları listeleyin. Göreceksiniz ki Cargo sizin için
iki tane dosya oluşturmuş: bir *Cargo.toml* dosyası ve içinde *main.rs*'i tutan bir *src* dizini.

Cargo ayrıca yeni bir Git deposunu *.gitignore* dosyası oluşturmakla beraber başlatır. 
Git dosyaları eğer halihazırda bir Git deposundaysanız `cargo new` komutuyla oluşturulmaz.
Bu davranışı `cargo new --vcs=git` komutunu kullanarak değiştirebilirsiniz ve Git dosyaları otomatik olarak oluşturulmuş olur.

> Not: Git yaygın bir versiyon kontrol sistemidir. `cargo new` komutunu `--vcs` argümanıyla birlikte 
> farklı bir versiyon kontrol sistemiyle ya da VKS (VCS) olmadan da kullanabilirsiniz.
> `cargo new --help` komutunu çalıştırarak seçenekleri görebilirsiniz.

*Cargo.toml* dosyasını yazı editörünüzle açabilirsiniz. Liste 1-2'dekine benzer bir kodla
karşılaşmanız beklenir.

<span class="filename">Dosya adı: Cargo.toml</span>

```toml
[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2021"

[dependencies]
```

<span class="caption">Liste 1-2: *Cargo.toml*'ın içeriği `cargo
new` tarafından oluşturulmuştur</span>

Bu dosya, [*TOML*](https://toml.io)<!-- ignore --> (*Tom’un Bariz,
Minimal Dili*) Cargo'nun kullandığı dahili konfigürasyon formatıyla oluşturulmuştur.

İlk satır `[package]`, konu başlığını belirtir. Bu başlık bize üye yapıları hakkında bazı bilgiler 
verir ve onları sınırlandırmamızı sağlar.

Sonraki üç satır Cargo'nun kodunuzu derlemesi için gerekli konfigürasyon bilgilerini içerir: paketinizin adı, sürümü ve hangi
Rust sürümünü kullandığı. [Ekleme E][appendix-e]<!-- ignore -->'de `edition` anahtarı hakkında daha fazla konuşacağız.

Son satırda `[dependencies]`, projenizin kullandığı bağımlılıkların bir listesidir. 
Rust'ta, kod paketleri *kasalar* olarak adlandırılır. Bu basit proje için bir diğer kasaya ihtiyacımız yok fakat
Bölüm 2'de bağımlılıklar konusunu işleyeceğiz.

Şimdi *src/main.rs* dosyasını açın ve bir bakış atın:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

Cargo sizin için bir “Hello, world!” programı oluşturmuştu aynı Liste 1-1'de yazdığımız gibi!
Çok yakın, eski projemiz ile arasındaki farklardan bazıları: Cargo kodu *src* dizininde oluşturdu, biz ise kök dizinde oluşturduk. 
Ayrıca biz *Cargo.toml* şeklinde bir dosya oluşturmadık. 

Cargo sizin tüm kaynak dosyalarınızın *src* dizininde olmasını bekler. Kök dizin daha çok BENİOKU (README) dosyaları, lisans bilgileri,
konfigürasyon dosyaları gibi çeşitli yardımcı elemanlar için kullanılması beklenir. Burada her şey için bir yer var ve her şeyin de 
bir yeri var ve her şey yerli yerinde olmalı.

Cargo kullanmayan “Hello, world!” projenizi Cargo kullanabilir hale getirmek getirmek için tüm kodlarınızı *src* dizinine taşıyabilir ve *Cargo.toml* adında bir konfigürasyon dosyası oluşturabilirsiniz.

### Cargo Projesini Derleme ve Çalıştırma

Şimdi “Hello, world!” projesinde neyin farklı olduğunu bulalım! *hello_cargo* dizininizde, projenizi şu kodla derleyebilirsiniz:

```console
$ cargo build
   Compiling hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 2.85 secs
```

Bu komut kök dizinde oluşturmak yerine *target/debug/hello_cargo* dizininde yürütülebilir bir dosya oluşturur (Windows'ta
*target\debug\hello_cargo.exe* dizininde). Bu dosyayı şu komutla çalıştırabilirsiniz:

```console
$ ./target/debug/hello_cargo # Windows'ta .\target\debug\hello_cargo.exe komutunu kullanın 
Hello, world!
```

Eğer her şey yolunda gitmişse, `Hello, world!` uçbirimde yazılmış olmalıdır. `cargo
build` komutunu ilk defa çalıştırmak ayrıca kök dizinde *Cargo.lock* adında bir dosya oluşturur. 
Bu dosya projenizin bağımlılıklarını kayıtta tutar. Tabii bu projenin standart kütüphane hariç herhangi bir bağımlılığı olmadığından
dolayı dosya biraz boş görünebilir. Bu dosyayı elle değiştirmeniz gerekmez, Cargo bunu sizin için otomatik yönetir.

`cargo build` ile derledik ve `./target/debug/hello_cargo` ile çalıştırdık, ama ayrıca `cargo run` komutunu da kullanarak
kodu derleyip çalıştırabiliriz:

```console
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/hello_cargo`
Hello, world!
```

Not olarak, eğer kaynak kodunuzu değiştirmediyseniz, Cargo herhangi bir derleme gereksinimi olmadan programınızı çalıştıracaktır.
Eğer kaynak kodunuzu değiştirdiyseniz, Cargo projenizi yeniden derleyecektir ve eğer kodunuz sorunsuz ise şu çıktıyı görmeniz olasıdır:

```console
$ cargo run
   Compiling hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.33 secs
     Running `target/debug/hello_cargo`
Hello, world!
```

Cargo ayrıca `cargo check` adında bir komut sunar. Bu komut kodunuzu hızlıca kontrol 
eder ve onu derlenebilir hale sokar fakat herhangi bir yürütülebilir dosya oluşturmaz:

```console
$ cargo check
   Checking hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.32 secs
```

Niye yürütülebilir dosya istenmesin ki? Yaygın olarak, `cargo check` `cargo build`'ten daha hızlıdır, 
çünkü yürütülebilir dosya oluşturma kısmını es geçer. Eğer kod yazarken kodunuzu sürekli kontrol ediyorsanız
`cargo check` kullanmak işlemlerinize hız katacaktır! Ayrıca, çoğu Rustsever `cargo check` komutunu derleneceğinden
emin olabilmek için sıklıkla çalıştırır. Onlar ayrıca `cargo build` komutunu her ne zaman proje yürütülebilirliğe hazır olduğu vakit
çalıştırırlar.

Hadi şimdi Cargo ile neler öğrendiğimize yakından bakalım:

* `cargo new` ile proje oluşturabiliyoruz.
* `cargo build` ile proje derleyebiliyoruz.
* `cargo run` ile hem derleyip hem çalıştırabiliyoruz.
* Yürütülebilir kod oluşturmadan `cargo check` ile kodumuzu kontrol edebiliyoruz.
* Aynı dizinde yürütülebilirleri tutmak yerine Cargo'nun *target/debug* dizininde
  tuttuğunu artık biliyoruz.

Ayrıca iyi bir avantaj olaraktan, Cargo her ne işletim sistemini kullanıyorsanız olun aynı komutlara ve işleve sahiptir.
Yani, bu saatten sonra işletim sistemlerine yönelik spesifik talimatlar sunmayacağız.

### Yayın için Derlemek

Her ne zaman projeniz yayınlanmak için hazırsa, `cargo build --release` komutunu kullanarak
kodunuzu optimizasyonlarla derleyebilirsiniz. Bu komut, yürütülebilir dosyaları *target/debug* dizini
yerine *target/release* dizininde tutar. Optimizasyonlar Rust kodunuzu hızlandırır fakat
derlenmesi için gerekli yer ve sürenizi artırır. İşte bu neden iki farklı profil türüne sahip olduğumuzu açıklar. 
Eğer kodunuzun çalıştırılma zamanını merak ediyor ve bunu test etmek istiyorsanız `cargo build --release` komutuyla
derlediğinizden emin olun.

### Cargo Hakkında

Basit projelerde Cargo direkt `rustc` kullanmanın önüne aşırı yenilikler katmıyor fakat bu halen
kodunuzun büyüdükçe ve karmaşıklaştıkça, farklı farklı kasalarla birlikte kullanıldığında Cargo ile koordine bir 
biçimde derlememenin daha kolay olacağı kanaatine varıyorsunuz.

Hatta `hello_cargo` projesi basit bir proje olmasına rağmen Rust kariyerinizde her zaman kullanacağınız
önemli araca ev sahipliği yapmış oluyor. Halihazırda var olan projeler üzerinde çalışmak için şu komutla
yerelde bu depoyu tutabilir, depoyu derleyebilirsiniz:

```console
$ git clone example.org/someproject
$ cd someproject
$ cargo build
```

Cargo hakkında daha fazla bilgi almak için, kontrol edin [its documentation].

[its documentation]: https://doc.rust-lang.org/cargo/

## Özet

Rust serüveninize iyi bir başlangıç yaptınız! Tüm bu bölümde birçok yeni şey öğrendiniz, bunlardan bazıları:

* `rustup` ile son stabil sürümü yükleme
* En sonki Rust sürümüne güncelleme
* Yereldeki dokümantasyonu açma
* Direkt `rustc` komutunu kullanarak “Hello, world!” programı yazıp çalıştırma
* Cargo projesi oluşturma ve çalıştırma

Okuduklarınız ve yazdıklarınızla daha karmaşık programlar yazmanın tam zamanı. Yani, Bölüm 2'de
bir tahmin oyunu inşa edeceğiz. Eğer daha önceden yaygın programlama kavramlarını öğrenmek istiyorsanız,
Bölüm 3'e bakabilirsiniz ve sonra tekrar Bölüm 2'ye dönebilirsiniz.

[installation]: ch01-01-installation.html#installation
[appendix-e]: appendix-05-editions.html
