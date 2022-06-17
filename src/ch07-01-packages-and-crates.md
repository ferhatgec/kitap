## Paketler ve Kasalar

Modül sisteminin ele alacağımız ilk kısımları paketler ve kasalardır.

*Kasa*, Rust derleyicisinin bir seferde dikkate aldığı en küçük kod miktarıdır. 
`cargo` yerine `rustc` çalıştırsanız ve tek bir kaynak kod dosyası iletseniz bile (1. Bölüm'ün “Bir Rust Programı Yazma ve Çalıştırma” bölümünde yaptığımız gibi), derleyici bu dosyayı bir kasa olarak kabul eder. Kasalar modüller içerebilir ve modüller, 
sonraki bölümlerde göreceğimiz gibi, kasa ile derlenen diğer dosyalarda tanımlanabilir.

Bir kasa iki biçimde olabilir: ikili kasa veya kütüphane kasası. İkili kasalar, bir komut satırı programı veya bir sunucu gibi 
çalıştırabileceğiniz bir yürütülebilir dosyaya derleyebileceğiniz programlardır. Her birinin, yürütülebilir dosya çalıştığında ne olacağını 
tanımlayan `main` adlı fonksiyonu olmalıdır. Şimdiye kadar yarattığımız tüm kasalar ikili kasalardı.

*Kütüphane kasalarının* `main` fonksiyonu yoktur ve yürütülebilir bir dosyaya derlenmezler. Bunun yerine, birden çok projeyle paylaşılması amaçlanan
fonksiyonları tanımlarlar. Örneğin, Bölüm 2'de kullandığımız `rand` kasası, *sözde rastgele* sayılar üreten işlevsellik sağlar. 
Rustseverler çoğu zaman “kasa” derken, kütüphane kasası demek isterler ve “kasa” sözcüğünü genel programlama kavramı olan 
“kütüphane” ile birbirinin yerine kullanırlar.

Kasa kökü, Rust derleyicisinin başlattığı ve kasanızın kök modülünü oluşturan bir kaynak dosyadır 
([“Kapsam ve Gizliliği Kontrol Etmek için Modüller Tanımlamak”][modules]<!-- ignore --> bölümünde modülleri ayrıntılı olarak açıklayacağız).

Paket, bir dizi işlevsellik sağlayan bir veya daha fazla kasadan oluşan bir pakettir. 
Bir paket, bu kasaların nasıl oluşturulacağını açıklayan bir *Cargo.toml* dosyası içerir. Cargo aslında kodunuzu oluşturmak için kullandığınız 
komut satırı aracı için ikili kasayı içeren bir pakettir. Cargo paketi ayrıca ikili kasanın bağlı olduğu bir kütüphane kasası içerir. 
Diğer projeler, Cargo komut satırı aracının kullandığı mantığı kullanmak için Cargo kütüphane kasasını kullanabilir.

Bir paket, istediğiniz kadar ikili kasa içerebilir, ancak en fazla yalnızca bir kütüphane kasası olabilir. 
Bir paket, ister kitaplık ister ikili kasa olsun, en az bir kasa içermelidir.

Bir paket oluşturduğumuzda neler olduğunu gözden geçirelim. İlk olarak, `cargo new` komutunu giriyoruz:

```console
$ cargo new my-project
     Created binary (application) `my-project` package
$ ls my-project
Cargo.toml
src
$ ls my-project/src
main.rs
```

`cargo new`'i çalıştırdıktan sonra, `cargo`'nun ne oluşturduğunu görmek için `ls` kullanırız. 
Proje dizininde bize bir paket veren bir *Cargo.toml* dosyası var. Ayrıca *main.rs*'i içeren bir *src* dizini vardır. 
Metin düzenleyicinizde *Cargo.toml*'yi açın ve burada *src/main.rs*'den bahsedilmediğini unutmayın. 
Cargo, *src/main.rs* öğesinin, paketle aynı ada sahip bir ikili kasanın kasa kökü olduğu kuralına uyar. 
Benzer şekilde, Cargo, paket dizini *src/lib.rs* içeriyorsa, paketin paketle aynı ada sahip bir kütüphane kasası içerdiğini ve 
*src/lib.rs*'nin bunun kasa kökü olduğunu bilir. Cargo, kütüphaneyi veya ikili dosyayı oluşturmak için 
sandık kök dosyalarını `rustc`'ye iletir.

Burada, yalnızca *src/main.rs* içeren bir paketimiz var, yani yalnızca *my-project* adında bir ikili sandık içeriyor. 
Bir paket *src/main.rs* ve *src/lib.rs* içeriyorsa, iki kasası vardır: ikisi de paketle aynı ada sahip ikili dosya ve kütüphane. 
Bir paket, dosyaları *src/bin* dizinine yerleştirerek birden çok ikili kasaya sahip olabilir: 
her dosya ayrı bir ikili kasa olacaktır.

[modules]: ch07-02-defining-modules-to-control-scope-and-privacy.html
[rand]: ch02-00-guessing-game-tutorial.html#generating-a-random-number
