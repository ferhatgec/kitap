## Ekleme D - Kullanışlı Geliştirme Araçları

Bu eklemede, Rust projesinin bize sağladığı bazı kullanışlı geliştirme araçlarından
bahsedeceğiz. Otomatik düzenleyici, hata ve uyarı çözücü, kod düzenleyici ve nasıl 
TGO'lar (IDE) ile kullanabileceğinizi göstereceğiz.

### `rustfmt` ile Otomatik Düzenleme


`rustfmt` aracı kodunuzu topluluk kodu stili şeklinde düzenler. Çoğu proje `rustfmt`'yi hangi Rust stilini 
kullanmakta kararsız kalındığı vakit kullanır: herkes bu aracı kullanarak kodunu düzenler.

`rustfmt`'i yüklemek için şu komutu girin:

```console
$ rustup component add rustfmt
```

Bu komut size `rustfmt` ve `cargo-fmt` araçlarını verir, nasıl Rust'ın `rustc` ve `cargo`'yu birlikte
dağıttığı gibi. Herhangi bir Cargo projesini düzenlemek için, şunu girin:

```console
$ cargo fmt
```

Bu komudu çalıştırmak halihazırdaki kasada bulunan tüm Rust kodlarınızı düzenler. 
Kodunuzun çalışma şeklini ve mantığını değiştirmez sadece kod stilini değiştirir. 
`rustfmt` hakkında daha fazla bilgi almak için [dokümantasyonunu][rustfmt] kullanabilirsiniz.

[rustfmt]: https://github.com/rust-lang/rustfmt

### `rustfix` ile Kodunuzu Çözümleme

rustfix aracı Rust'ın yüklemelerine dahil edilmiş, bazı derleyici hatalarını otomatik olarak
çözümleyen bir araçtır. Eğer Rust'ta kod yazmışsanız, büyük ihtimalle derleyici hatalarını ve 
uyarılarını da görmüşsünüzdür. Örnek olaraktan, şu koda odaklanın:

<span class="filename">Dosya: src/main.rs</span>

```rust
fn do_something() {}

fn main() {
    for i in 0..100 {
        do_something();
    }
}
```

Burada, `do_something` adlı fonksiyonu 100 kez çağırıyoruz ama hiçbir zaman `i` değerini döngü içinde kullanmıyoruz.
İşte bu yüzden Rust bizi bunun hakkında uyaracaktır:

```console
$ cargo build
   Compiling myprogram v0.1.0 (file:///projects/myprogram)
warning: unused variable: `i`
 --> src/main.rs:4:9
  |
4 |     for i in 0..100 {
  |         ^ help: consider using `_i` instead
  |
  = note: #[warn(unused_variables)] on by default

    Finished dev [unoptimized + debuginfo] target(s) in 0.50s
```

Bu uyarı bize `i` yerine `_i` kullanmamız gerektiğini sunar: bu alt çizgi ile
bu değerin kullanılmayacağını söylemiş oluyoruz. Bu öneriyi otomatik olarak eklemek istiyorsanız
`cargo fix` komutu vasıtasıyla `rustfix` aracını kullanın:

```console
$ cargo fix
    Checking myprogram v0.1.0 (file:///projects/myprogram)
      Fixing src/main.rs (1 fix)
    Finished dev [unoptimized + debuginfo] target(s) in 0.59s
```

*src/main.rs* koduna tekrar baktığımızda, `cargo fix`'in bazı yerleri değiştirdiğini görüyoruz:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
fn do_something() {}

fn main() {
    for _i in 0..100 {
        do_something();
    }
}
```

`for` döngüsü değeri artık `_i` şekliyle adlandırıldı, artık uyarı çıkmayacak.

`cargo fix` komutunu ayrıca farklı Rust sürümleri arasında kodunuzu güncelleştirmek
için kullanabilirsiniz. Sürümler Ekleme E'de açıklanacak.

### Clippy ile Daha Fazla Düzenleme

Clippy aracı düzenleme tavsiyelerinin bir koleksiyonunu tutan ve bunlar vasıtasıyla
kodunuzu analiz eden ve size kodunuz hakkında bilgiler ve öneriler veren bir araçtır.

Clippy'i indirmek için, şu komutu girin:

```console
$ rustup component add clippy
```

Herhangi bir Cargo projesinde Clippy’nin düzenlemelerini kullanmak için, şunu girin:

```console
$ cargo clippy
```

Örnek olaraktan, diyelim ki bir matematik sabitini yakınsayarak hesaplamak istiyorsunuz,
mesela pi olsun, bu program onu yapar:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
fn main() {
    let x = 3.1415;
    let r = 8.0;
    println!("the area of the circle is {}", x * r * r);
}
```

`cargo clippy`'i bu projede çalıştırmak bize şu hatayı verir:

```text
error: approximate value of `f{32, 64}::consts::PI` found. Consider using it directly
 --> src/main.rs:2:13
  |
2 |     let x = 3.1415;
  |             ^^^^^^
  |
  = note: #[deny(clippy::approx_constant)] on by default
  = help: for further information visit https://rust-lang-nursery.github.io/rust-clippy/master/index.html#approx_constant
```

Bu hata, Rust'ın bu sabiti daha kesin olarak tanımladığını 
ve bunun yerine sabiti kullanırsanız programınızın daha doğru olacağını belirtir. 
Rust'ın sunduğu PI sabitini kullandığınızda, aşağıdaki kod herhangi bir hata ya da uyarı
vermeden düzenlenir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
fn main() {
    let x = std::f64::consts::PI;
    let r = 8.0;
    println!("the area of the circle is {}", x * r * r);
}
```

Clippy hakkında daha fazla bilgi almak için [dokümantasyonunu][clippy] kullanabilirsiniz.

[clippy]: https://github.com/rust-lang/rust-clippy

### `rust-analyzer` Kullanarak TGO (IDE) Entegrasyonu

TGO entegrasyonu için Rust topluluğu
[`rust-analyzer`][rust-analyzer]<!-- ignore --> kullanmanızı öneriyor.
Bu araç bazı TGO'lar ve dillerin birbirleri arasındaki iletişimi sağlayan [Dil Sunucu Protokolü][lsp]<!-- ignore -->, 
vasıtasıyla derleyici tabanlı bir iletişim ağı oluşturur. Farklı editörler `rust-analyzer`'i kullanabilir, mesela
[Visual Studio Code için Rust analyzer eklentisi][vscode] kullanılabilir.

[lsp]: http://langserver.org/
[vscode]: https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer

Kurulum yönergeleri için `rust-analyzer` projesinin [ana sayfasına][rust-analyzer] gidebilir, daha sonra
TGO'nuz için desteklenen dil sunucusunu kurabilirsiniz. TGO'nuz bazı yenilikler edinecektir, mesela
otomatik tamamlama, tanıma yönlendirme, satır içi hata ve uyarılar vs.

[rust-analyzer]: https://rust-analyzer.github.io
