## Kurulum

İlk adım Rust'ı kurmaktır. Rust'ı, Rust sürümlerini 
ve ilgili araçları yönetmek için bir komut satırı aracı olan `rustup` aracılığıyla indireceğiz. 
İndirmek için bir internet bağlantısına ihtiyacınız olacak.

> Not: Eğer bir nedenden ötürü `rustup` kullanmak istemiyorsanız, lütfen
> [Rust'ı Kurmanın Diğer Yolları sayfasına][otherinstall] bir göz atın.

[otherinstall]: https://forge.rust-lang.org/infra/other-installation-methods.html

Aşağıdaki adımlar, Rust derleyicisinin en son kararlı sürümünü yükler. 
Rust'ın kararlılık garantisi, kitaptaki tüm örneklerin daha yeni Rust sürümleriyle derlenmeye devam etmesini sağlar. 
Çıktı, sürümler arasında biraz farklılık gösterebilir, çünkü Rust yeni sürümlerde genellikle hata mesajlarını ve uyarıları iyileştirir. 
Başka bir deyişle, bu adımları kullanarak kurduğunuz her yeni, kararlı Rust sürümü bu kitabın içeriğiyle beklendiği gibi çalışmalıdır.

> ### Komut Satırı Gösterimi
>
> Bu bölümde hatta kitabın çoğu yerinde komutları uçbirimde kullanıldığı haliyle
> göstereceğiz. Yazacağınız satırlar `$` ile başlamalıdır. Bu karakteri yazmanıza 
> gerek yoktur, sadece komutun başladığını belirtir ve her komutta belirir
> `$` ile başlamayan satırlar çoğu zaman önceki komutun çıktısını gösterir.
> Farklı olarak, PowerShell özelindeki örneklerde `>` karakterini kullanacağız.

### Linux ya da macOS üzerinde `rustup`'ı indirmek

Eğer Linux ya da macOS kullanıyorsanız, uçbirimi açın ve şu komutu girin.

```console
$ curl --proto '=https' --tlsv1.3 https://sh.rustup.rs -sSf | sh
```

Bu komut bir betik indirir ve Rust'ın son stabil sürümünü kuran araç olan `rustup`'ı başlatır. 
Eğer çıkış hariç herhangi bir seçeneği seçerseniz şifrenizi girmeniz gerektiği belirtilmiş olmalıdır. 
Eğer kurulum başarılı olursa, şu satırlar görünmüş olmalıdır:

```text
Rust is installed now. Great!
```

Ayrıca bir Rust'ın kullandığı, derlenmiş çıktıları tek dosyala toplayan bir bağlayıcıya ihtiyacınız olacak. 
Büyük ihtimalle sizde bir tanesi vardır, eğer herhangi bir bağlayıcı hatası alıyorsanız Bağlayıcı içeren 
bir C derleyicisi yüklemeniz gerekir. Bir C derleyicisi ayrıca C kodu içeren Rust paketlerini derlemek için de 
kullanışlıdır.

macOS'ta C derleyicisini şu kodu çalıştırarak elde edebilirsiniz:

```console
$ xcode-select --install
```

Linux kullanıcıları dağıtımlarının dokümantasyonlarına bağlı olarak genel olarak GCC ya da Clang yüklemelidir. 
Örnek olarak, eğer Ubuntu kullanıyorsanız, `build-essential` paketini yükleyebilirsiniz.

### Windows üzerinde `rustup`'ı indirmek

Windows'ta, [https://www.rust-lang.org/tools/install][install] sitesine gidin ve 
Rust'ı kurmak için belirtilen yönergeleri uygulayın. Yüklemenin bazı noktalarında
Visual Studio 2013 ya da yeni sürümleri için C++ inşa araçlarına ihtiyacınız olduğuna
dair bir mesaj alacaksınız. En kolay yolla gerekli inşa araçlarını alabilmek için [Build Tools for Visual Studio 2019][visualstudio]'ı
kurabilirsiniz. Her ne zaman hangilerini indirmeniz gerektiği sorulduğunda “C++ inşa araçları”,
Windows 10 SDK ve İngilizce dil paketi dahil edilmş olmalıdır.

[install]: https://www.rust-lang.org/tools/install
[visualstudio]: https://visualstudio.microsoft.com/visual-cpp-build-tools/

Bu kitap hem *cmd.exe* de hem de PowerShell de çalışan komutları kullanmaktadır. Eğer bir farklılık var ise
hangisini kullanmanız gerektiğini açıklayacağız.

### Güncelleme ve Kaldırma

`rustup` yoluyla kurduktan sonra son sürüme güncelleştirmek aşırı kolaydır. Kabuğunuzdan (PowerShell) şu güncelleme betiğini çalıştırın:

```console
$ rustup update
```

Rust ve `rustup`'ı kaldırmak için kabuğunuzdan şu kaldırma betiğini çalıştırın:

```console
$ rustup self uninstall
```

### Sorun giderme

Rust'ı doğru yüklediğinizden emin olmak için kabuğunuzu açın ve şu betiği girin:

```console
$ rustc --version
```

Versiyon sayısını, son stabil sürüm için depoya gönderilen gönderim tarihini görmüş olmalısınızdır:

```text
rustc x.y.z (abcabcabc yyyy-mm-dd)
```

Eğer bu bilgileri görebiliyorsanız, Rust'ı doğru bir biçimde kurmuşsunuz demektir!
Eğer göremiyorsanız ve Windows üzerindeyseniz, `%PATH%` sistem değişkenini kontrol edinç
Eğer her şey yolunda ve Rust hala çalışmıyorsa, yardım alabileceğiniz birçok yer vardır. 
En kolay yol, [resmi Rust Discord sunucusunda][discord] #beginners kanalına mesaj atmaktır. 
Burada, size yardımcı olabilecek diğer Rustseverler (Rustaceans) ile mesajlaşabilirsiniz.
Diğer güzel kaynaklara [Kullanıcılar forumu][users] ve [Stack Overflow][stackoverflow] örnek verilebilir.

[discord]: https://discord.gg/rust-lang
[users]: https://users.rust-lang.org/
[stackoverflow]: https://stackoverflow.com/questions/tagged/rust

### Yerel Dokümantasyon

Rust kurulumu ayrıca dokümantasyonun bir kopyasını yerelde tutar, yani bunu çevrimdışı da 
okuyabilirsiniz. Tarayıcınızda okumak için `rustup doc` komutunu çalıştırabilirsiniz.

Standart kütüphanede bulunan ve nasıl ya da nerede kullanacağınızı bilmediğiniz tür ya da fonksiyonları
uygulama programlama arayüzü (API) dokümantasyonunu kullanarak bulabilirsiniz!
