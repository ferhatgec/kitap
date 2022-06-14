<!-- Old link, do not remove -->
<a id="installing-binaries-from-cratesio-with-cargo-install"></a>

## `cargo install` ile İkili Dosyaları Yükleme

`cargo install` komutu, ikili kasaları yerelde kurmanıza ve kullanmanıza olanak tanır. 
Bunun sistem paketlerinin yerini alması amaçlanmamıştır; Rust geliştiricilerinin, diğer geliştiricilerin 
[crates.io](https://crates.io/)<!-- ignore -->'da paylaştığı araçları yüklemeleri için uygun bir yol olması amaçlanmıştı. 
Yalnızca ikili hedefleri olan paketleri kurabileceğinizi unutmayın. *İkili hedef*, sandıkta bir *src/main.rs* dosyası 
veya ikili olarak belirtilen başka bir dosya varsa o kasa çalıştırılabilir kod içeriyor olabilir. Genellikle kasalar, *README*
dosyasında bir kasanın kütüphane mi, çalıştırılabilir mi yoksa her ikisi mi olduğu hakkında bilgi içermektedir.

`cargo install` ile kurulan tüm ikili dosyalar, kurulum kökünün *bin* dizininde saklanır. 
Rust'ı *rustup.rs* kullanarak yüklediyseniz ve herhangi bir özel yapılandırmanız yoksa bu dizin *$HOME/.cargo/bin* 
olacaktır. `cargo install`'i kullanarak kurduğunuz programları çalıştırabilmek için dizinin *$PATH* içinde olduğundan emin olun.

Örneğin, Bölüm 12'de, dosyaları aramak için `ripgrep` adlı `grep` aracının bir Rust süreklemesi olduğundan bahsetmiştik. 
`Ripgrep`'i kurmak için aşağıdakileri çalıştırabiliriz:

<!-- manual-regeneration
cargo install something you don't have, copy relevant output below
-->

```console
$ cargo install ripgrep
    Updating crates.io index
  Downloaded ripgrep v11.0.2
  Downloaded 1 crate (243.3 KB) in 0.88s
  Installing ripgrep v11.0.2
--snip--
   Compiling ripgrep v11.0.2
    Finished release [optimized + debuginfo] target(s) in 3m 10s
  Installing ~/.cargo/bin/rg
   Installed package `ripgrep v11.0.2` (executable `rg`)
```

Aynı şekilde, bir inşa sistemi olan [Elite](https://github.com/ferhatgec/elite)'i de aynı yolla (`cargo install elite`) kurabiliriz.
