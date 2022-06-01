## Merhaba, Dünya!

Artık Rust'u yüklediğinize göre ilk Rust programınızı yazalım. Yeni bir dil öğrenirken `Hello, world!` metnini yazdıran küçük bir program yazmak gelenekseldir. Bu yüzden burada da aynısını yapacağız!

> Not: Bu kitap, komut satırına temel düzeyde aşina olduğunuzu varsayar. 
> Rust, araçlarınızı ya da kodunuzun nerede tutulduğunu umursamaz, çoğu editörde Rust dili desteği vardır. 
> Bu nedenle komut satırı yerine entegre bir geliştirme ortamı (IDE) kullanmayı tercih ederseniz, favori IDE'nizi kullanmaktan çekinmeyin. 
> Birçok IDE artık bir dereceye kadar Rust desteğine sahiptir; ayrıntılar için IDE dokümantasyonlarına bakabilirsiniz. 
> Son zamanlarda, Rust ekibi harika IDE desteği sağlamaya odaklandı ve bu cephede hızla ilerleme kaydedildi!

### Proje Dizini Oluşturma

Rust kodunuzu tutmak için bir *dizine* ihtiyacınız var, Rust bunu otomatik olarak oluşturur. Normalde,
Rust için kodunuzun nerede tutulduğu hiç de önemli değildir ancak bu kitaptaki alıştırmalar ve projeler için ana dizininizde bir proje dizini oluşturmanızı ve tüm projelerinizi orada tutmanızı öneririz.

Bir *proje dizini* ve başlangıç kodu oluşturmak için bir uçbirim açın ve aşağıdaki komutları girin:

Linux'ta, macOS'ta ve PowerShell'de (Windows'ta) çalıştırmak için şunları girin:

```console
$ mkdir ~/projects
$ cd ~/projects
$ mkdir hello_world
$ cd hello_world
```

Windows Komut Satırı (CMD) için şunları girin:

```cmd
> mkdir "%USERPROFILE%\projects"
> cd /d "%USERPROFILE%\projects"
> mkdir hello_world
> cd hello_world
```

### Rust Programı Yazma ve Çalıştırma

Ardından, yeni bir kaynak dosya oluşturun ve *main.rs* olarak adlandırın. Rust dosyaları her zaman *.rs* uzantısıyla biter. 
Dosya adınızda birden fazla kelime kullanıyorsanız, bunları ayırmak için alt çizgi kullanın. Örneğin, *helloworld.rs* yerine *hello_word.rs* kullanın.

Şimdi az önce oluşturduğunuz main.rs dosyasını açın ve Liste 1-1'deki kodu yapıştırın.

<span class="filename">Dosya adı: main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

<span class="caption">Liste 1-1: `Hello, world!` Yazan Bir Program</span>

Dosyayı kaydedin ve uçbirime geri dönün. Linux'ta ya da macOS'ta derlemek ve çalıştırmak için şu komutları girin:

```console
$ rustc main.rs
$ ./main
Hello, world!
```

Windows'ta, komutu `./main` şeklinde değil `.\main.exe` şeklinde girin:

```powershell
> rustc main.rs
> .\main.exe
Hello, world!
```

İşletim sisteminizden bağımsız olarak `Hello, world!` dizgisi uçbirime yazılmış olmalıdır. Eğer çıktıyı göremiyorsanız, 
[“Hata giderme”][troubleshooting]<!-- ignore --> kısmına giderek çözüm arayın, büyük ihtimalle bir şeyi kaçırmışssınızdır!

Eğer `Hello, world!` çıktısını gördüyseniz, tebrikleeeeeeer! Rust programı yazdınız. Bu da sizi Rust programcısı yapar—Hoş geldiniz!

### Bir Rust Programının Anatomisi

Hadi sizin “Hello, world!” programınızda nelerin olduğunu detaylıca inceleyelim. Burası yapbozun ilk parçası:

```rust
fn main() {

}
```

Bu satırlar Rust'ta bir fonksiyonu tanımlar. `main` fonksiyonu özel bir fonksiyondur: yürütülebilir her Rust programında çalışan ilk koddur. 
İlk satır, parametresi olmayan ve hiçbir şey döndürmeyen `main` adlı bir işlev bildirir. Parametreler olsaydı, parametreler parantez `()` içine girerlerdi.

Ayrıca, fonksiyon gövdesinin `{}` süslü parantezlerine sarıldığına dikkat edin. Rust'ta bunlar gereklidir. Aralarına bir boşluk ekleyerek, giriş süslü parantezini fonksiyon ile aynı satıra yerleştirmek iyi bir stildir.

Rust projelerinde standart bir stile bağlı kalmak istiyorsanız, kodunuzu belirli bir stille biçimlendirmek için `rustfmt` adlı otomatik biçimlendirici aracı kullanabilirsiniz. Rust ekibi, bu aracı `rustc` gibi standart Rust dağıtımına dahil etti, bu nedenle bilgisayarınızda zaten yüklü olmalıdır! Daha fazla ayrıntı için çevrimiçi belgelere bakın.

`main` fonksiyonunun içinde aşağıdaki kod bulunur:

```rust
    println!("Hello, world!");
```

Bu satır, bu küçük programdaki tüm işi yapar: metni ekrana yazdırır. Burada dikkat edilmesi gereken dört önemli detay var.

İlk olarak, Rust stili bir TAB karakteriyle değil, dört boşlukla girinti yapar.

İkincisi, `println!` bir Rust makrosu çağırır. Bunun yerine bir fonksiyon çağırsaydı, 
`println` olarak girilirdi (`!` olmadan). Rust makrolarını Bölüm 19'da daha ayrıntılı olarak tartışacağız. Şimdilik, bir `!` normal bir fonksiyon yerine bir makro çağırdığınız ve makroların her zaman fonksiyonlarla aynı kurallara uymadığı anlamına gelir.

Üçüncüsü, `"Hello, world!"` dizgisi. Bu dizgiyi `println!`'e bir argüman olarak iletiyoruz ve dizgi ekrana yazdırılıyor.

Dördüncü olarak, satırı noktalı virgül (`;`) ile bitiriyoruz, bu karakter ile ifadenin bittiğini ve bir sonrakinin başlamaya hazır olduğunu gösteriyoruz. 
Rust kodunun çoğu satırı noktalı virgülle biter.

### Derleme ve Çalıştırma Ayrı Adımlardır

Az önce yeni oluşturulmuş bir programı çalıştırdınız, bu yüzden süreçteki her adımı inceleyelim. 
Bir Rust programını çalıştırmadan önce, Rust derleyicisini kullanarak, yani `rustc` komutunu girerek ve 
kaynak dosyanızın adını ona şu şekilde ileterek onu derlemelisiniz:

```console
$ rustc main.rs
```

C veya C++ geçmişiniz varsa, bunun `GCC` veya `Clang`'a benzediğini fark edeceksiniz. Başarılı bir şekilde derlendikten sonra Rust, derlenmiş ve yürütülebilir dosya çıkarır. Linux, macOS ve PowerShell'de (yani Windows'ta), kabuğunuza `ls` komutunu girerek yürütülebilir dosyayı görebilirsiniz. Linux ve macOS'ta iki dosya göreceksiniz. PowerShell (Windows) ile, CMD kullanarak göreceğiniz aynı üç dosyayı göreceksiniz.

```console
$ ls
main  main.rs
```

Windows'ta CMD kullanırsanız, şunları göreceksinizdir:

```cmd
> dir /B %= /B seçeneği yalnızca dosya adlarını göstermeyi garantiler =%
main.exe
main.pdb
main.rs
```

Bu, `.rs` uzantılı kaynak kod dosyasını, yürütülebilir dosyayı (Windows'ta `main.exe`, ancak diğer tüm platformlarda `main`) ve Windows kullananlar için `.pdb` uzantılı hata ayıklama bilgilerini içeren bir dosyayı gösterir. Buradan `main` veya `main.exe` dosyasını şu şekilde çalıştırırsınız:

```console
$ ./main # ya da Windows üzerinde .\main.exe şeklinde kullanılabilir
```

Eğer derlenmiş *main.rs*, “Hello, world!” programı ise, çalıştırdığınız zaman `Hello, world!` çıktısını uçbiriminizde görmüşsünüzdür.

Ruby, Python veya JavaScript gibi dinamik bir dille haşır neşirseniz, bir programı ayrı adımlar olarak derlemeye ve çalıştırmaya alışkın olmayabilirsiniz. Rust *derlenmeye ihtiyaç duyan* bir dildir, yani bir programı derleyebilir ve yürütülebilir dosyayı başka birine verebilirsiniz ve onlar Rust'u kurmadan bile çalıştırabilirler. Birine *.rb*, *.py* veya *.js* dosyası verirseniz, o kişinin (sırasıyla) bir Ruby, Python veya JavaScript süreklemesine sahip olması gerekir. Ancak bu dillerde, programınızı derlemek ve çalıştırmak için yalnızca bir komuta ihtiyacınız vardır. Dil tasarımında her şey bir değiş tokuştur.

Basit programları `rustc` kullanarak derlemek hoştur fakat projeniz büyüdükçe kodunuzu kolayca paylaşabilmeniz ve 
yönetebilmeniz için bir yöneticiye ihtiyacınız olacaktır. Sonraki aşamalarda size, siz gerçek-dünya programları yazarken size yardımcı
olacağını düşündüğümüz Cargo aracını tanıtacağız.

[troubleshooting]: ch01-01-installation.html#troubleshooting
