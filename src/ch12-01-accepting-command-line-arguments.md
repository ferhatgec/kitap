## Komut Satırı Argümanlarını Kabul Etme

Her zaman olduğu gibi `cargo new` ile yeni bir proje oluşturalım. Sisteminizde mevcut olabilecek `grep` 
aracıyla çakışmaması için projemizi `minigrep` olarak adlandıracağız

```console
$ cargo new minigrep
     Created binary (application) `minigrep` project
$ cd minigrep
```

İlk görev, `minigrep`'in iki komut satırı argümanını kabul etmesini sağlamaktır: 
dosya adı ve aranacak bir dize. Yani, programımızı `cargo run`'la, aranacak bir dize ve aranacak bir 
dosyanın yolu ile çalıştırabilmek istiyoruz, şunun gibi:

```console
$ cargo run searchstring example-filename.txt
```

Şu anda, `cargo new` tarafından oluşturulan program, ona verdiğimiz argümanları işleyemiyor. 
[crates.io](https://crates.io/)'daki bazı mevcut kütüphaneler, komut satırı argümanlarını kabul eden bir program yazmaya yardımcı olabilir, 
ancak bu kavramı yeni öğrendiğiniz için, hadi bunu kendimiz sürekleyelim.

### Argüman Değerlerini Okumak

`minigrep`'in kendisine ilettiğimiz komut satırı argümanlarının değerlerini okumasını sağlamak için, 
Rust'ın standart kütüphanesinde bulunan `std::env::args` fonksiyonuna ihtiyacımız olacak. 
Bu fonksiyon, `minigrep`'e iletilen komut satırı argümanlarının bir yineleyicisini döndürür. 
[Bölüm 13][ch13]<!-- ignore
-->'te yineleyicileri tam olarak ele alacağız. Şimdilik, yineleyiciler hakkında yalnızca iki ayrıntıyı bilmeniz gerekir: 
yineleyiciler bir dizi değer üretir ve bir yineleyicide onu bir koleksiyona dönüştürmek için `collect` metodunu çağırabiliriz, 
örneğin: yineleyicinin ürettiği tüm öğeleri içeren vektör.

Liste 12-1'deki kod, `minigrep` programınızın kendisine iletilen herhangi bir komut satırı argümanını okumasını ve 
ardından değerleri bir vektörde toplamasını sağlar.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-01/src/main.rs}}
```

<span class="caption">Liste 12-1: Komut satırı argümanlarını bir vektörde toplama ve yazdırma</span>

İlk olarak, `args` fonksiyonunu kullanabilmemiz için `std::env` modülünü `use` ifade yapısı ile kapsama alıyoruz. 
`std::env::args` fonksiyonunun iki modül düzeyinde tutuluyor olduğuna dikkat edin. 
[Bölüm 7][ch7-idiomatic-use]<!-- ignore -->'de tartıştığımız gibi, istenen fonksiyonun birden fazla modülde iç içe olduğu durumlarda, 
ana modülü fonksiyon yerine kapsama almak gelenekseldir. Bunu yaparak, `std::env`'deki diğer fonksiyonları kolayca kullanabiliriz. 
Ayrıca `use std::env::args` eklemekten ve ardından fonksiyonu yalnızca `args` ile çağırmaktan daha az belirsizdir, 
çünkü `arg`'ler kolayca geçerli modülde tanımlanan bir fonksiyonla karıştırılabilir.

> ### `args` Fonksiyonu ve Geçersiz Unicode
> 
> Herhangi bir argüman geçersiz Unicode içeriyorsa `std::env::args` öğesinin panikleyeceğini unutmayın. 
> Programınızın geçersiz Unicode içeren argümanları kabul etmesi gerekiyorsa, 
> bunun yerine `std::env::args_os` kullanın. Bu fonksiyon, `String` değerleri yerine `OsString` değerleri üreten bir 
> yineleyici döndürür. Basitlik için burada `std::env::args` kullanmayı seçtik, çünkü `OsString` değerleri platforma göre farklılık 
> gösterir ve onlarla çalışmak `String` değerlerinden daha karmaşıktır.

`main`'in ilk satırında, `env::args`'ı çağırırız ve yineleyiciyi yineleyici tarafından üretilen tüm değerleri 
içeren bir vektöre dönüştürmek için `collect` kullanırız. Pek çok türde koleksiyon oluşturmak için `collect` 
fonksiyonunu kullanabiliriz, bu nedenle bir dizi vektörü istediğimizi belirtmek için açıkça `arg` türüne açıklama ekleriz. 
Rust'ta türlere çok nadiren açıklama eklememiz gerekse de, `collect`, genellikle açıklama eklemeniz gereken bir fonksiyondur çünkü Rust, 
istediğiniz koleksiyon türünü çıkaramaz.

Son olarak, vektörü hata ayıklama biçimlendiricisini (`:?`) kullanarak yazdırırız. 
Kodu önce bağımsız değişken olmadan ve ardından iki bağımsız değişkenle çalıştırmayı deneyelim:

```console
{{#include ../listings/ch12-an-io-project/listing-12-01/output.txt}}
```

```console
{{#include ../listings/ch12-an-io-project/output-only-01-with-args/output.txt}}
```

Vektördeki ilk değerin, ikili dosyamızın adı olan `"target/debug/minigrep"` olduğuna dikkat edin. 
Bu, programların yürütülürken çağrıldıkları adı kullanmasına izin vererek, 
C'deki argüman listesinin davranışıyla eşleşir. Mesajlarda yazdırmak veya programı çağırmak için 
hangi komut satırı diğer adının kullanıldığına bağlı olarak programın davranışını değiştirmek istemeniz durumunda, 
program adına erişiminiz olması genellikle uygundur. Ancak bu bölümün amaçları doğrultusunda, onu görmezden geleceğiz 
ve yalnızca ihtiyacımız olan iki argümanı kaydedeceğiz.

### Değişkenlerde Argüman Değerlerini Tutma

Program şu anda komut satırı argümanları olarak belirtilen değerlere erişebilir. 
Şimdi değerleri programın geri kalanında kullanabilmemiz için iki argümanın değerlerini değişkenlere 
kaydetmemiz gerekiyor. Bunu Liste 12-2'de yapıyoruz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,should_panic,noplayground
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-02/src/main.rs}}
```

<span class="caption">Liste 12-2: Sorgu argümanını ve 
dosya adı argümanını tutmak için değişkenler oluşturma</span>

Vektörü yazdırdığımızda gördüğümüz gibi, programın adı `args[0]`'daki vektördeki ilk değeri alır, 
dolayısıyla argümanları indeks `1`'den başlatıyoruz. `minigrep`'in aldığı ilk argüman, aradığımız dizgidir, 
bu yüzden değişken sorgusundaki ilk argümana bir referans koyduk. İkinci argüman dosya adı olacaktır, 
bu yüzden dosya adı değişkenine ikinci argümana bir referans koyduk.

Kodun istediğimiz gibi çalıştığını kanıtlamak için bu değişkenlerin değerlerini geçici olarak yazdırırız. 
Bu programı `test` ve `sample.txt` argümanları ile tekrar çalıştıralım:

```console
{{#include ../listings/ch12-an-io-project/listing-12-02/output.txt}}
```

Harika, program çalışıyor! İhtiyacımız olan argümanların değerleri doğru değişkenlere kaydediliyor. 
Daha sonra, örneğin kullanıcının hiçbir argüman sağlamaması gibi bazı olası hatalı 
durumlarla başa çıkmak için bazı hata işleme ekleyeceğiz, şimdilik
bu durumu görmezden geleceğiz ve bunun yerine dosya okuma yetenekleri eklemeye çalışacağız.

[ch13]: ch13-00-functional-features.html
[ch7-idiomatic-use]: ch07-04-bringing-paths-into-scope-with-the-use-keyword.html#creating-idiomatic-use-paths
