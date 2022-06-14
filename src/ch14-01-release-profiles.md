## Dağıtım Profilleri ile Yapıları Özelleştirme

Rust'ta dağıtım profilleri, bir programcının kod derlemek için çeşitli seçenekler üzerinde daha fazla kontrole sahip 
olmasını sağlayan farklı konfigürasyonlara sahip önceden tanımlanmış ve özelleştirilebilir profillerdir. 
Her profil diğerlerinden bağımsız olarak yapılandırılır.

Cargo'nun iki ana profili vardır: Cargo'nun, `cargo build`'i çalıştırdığınızda kullandığı `dev` profili ve 
`cargo build --release`'i çalıştırdığınızda kullandığı `release` profili geliştirme için iyi varsayılanlarla tanımlanır 
ve dağıtım profili, dağıtım derlemeleri için iyi varsayılanlara sahiptir.

Bu profil adları, yapılarınızın çıktısından tanıdık gelebilir:

<!-- manual-regeneration
anywhere, run:
cargo build
cargo build --release
and ensure output below is accurate
-->

```console
$ cargo build
    Finished dev [unoptimized + debuginfo] target(s) in 0.0s
$ cargo build --release
    Finished release [optimized] target(s) in 0.0s
```

`dev` ve `release`, derleyici tarafından kullanılan farklı tür profillerdir.

Cargo'nun, projenin *Cargo.toml* dosyasına açıkça herhangi bir `[profile.*]` bölümü eklemediğinizde uygulanan profillerin 
her biri için varsayılan ayarları vardır. Özelleştirmek istediğiniz herhangi bir profil için 
`[profil.*]` bölümlerini ekleyerek, varsayılan ayarların herhangi bir alt kümesini geçersiz kılarsınız. 
Örneğin, `dev` ve `release` profilleri için `opt-level` ayarının varsayılan değerleri şunlardır:

<span class="filename">Dosya adı: Cargo.toml</span>

```toml
[profile.dev]
opt-level = 0

[profile.release]
opt-level = 3
```

Optimizasyon düzeyi (`opt-level`) ayarı, 0 ila 3 aralığında Rust'ın kodunuza uygulayacağı optimizasyonların 
sayısını kontrol eder. Bu nedenle `dev` profili için varsayılan tercih düzeyi 0'dır. Kodunuzu yayınlamaya hazır olduğunuzda, 
derlemeye daha fazla zaman harcamak en iyisidir. Dağıtım modunda yalnızca bir kez derlersiniz, 
ancak derlenmiş programı birçok kez çalıştırırsınız, bu nedenle dağıtım modu, daha hızlı çalışan kod için 
daha uzun derleme süresi değiştirir. Bu nedenle, `release` profili için varsayılan tercih düzeyi 3'tür.

*Cargo.toml*'da bunun için farklı bir değer ekleyerek bir varsayılan ayarı geçersiz kılabilirsiniz. 
Örneğin geliştirme profilinde birinci optimizasyon seviyesini kullanmak istiyorsak projemizin *Cargo.toml* 
dosyasına şu iki satırı ekleyebiliriz:

<span class="filename">Dosya adı: Cargo.toml</span>

```toml
[profile.dev]
opt-level = 1
```

Bu kod, varsayılan `0` ayarını geçersiz kılar. Şimdi, `cargo build`'i çalıştırdığımızda, 
Cargo, `dev` profili için varsayılanları ve ayrıca `opt-level` özelleştirmemizi kullanacak. 
`opt-level`'i `1` olarak ayarladığımız için, Cargo varsayılandan daha fazla optimizasyon uygulayacak, 
ancak bir `release` yapısındaki kadar değil.

Her profil için yapılandırma seçeneklerinin ve varsayılanların tam listesi için
[Cargo'nun dokümantasyonuna](https://doc.rust-lang.org/cargo/reference/profiles.html) bakın.
