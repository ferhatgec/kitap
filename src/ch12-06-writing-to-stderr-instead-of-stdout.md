## Standart Çıktı Yerine Standart Hataya Hata Mesajları Yazma

Şu anda çıktımızın tamamını `println!` makrosunu kullanarak uçbirime yazıyoruz. 
Çoğu uçbirimde iki tür çıktı vardır: genel bilgiler için standart çıktı (`stdout`) ve hata mesajları için standart hata (`stderr`). 
Bu ayrım, kullanıcıların bir programın başarılı çıktısını bir dosyaya yönlendirmeyi, 
ancak yine de ekrana hata mesajlarını yazdırmayı seçmelerini sağlar.

`prıntln!` makrosu yalnızca standart çıktıya yazdırabilir, bu nedenle standart hataya yazdırmak 
için başka bir şey kullanmamız gerekir.

### Hataların Nerede Yazıldığını Kontrol Etme

Öncelikle `minigrep` tarafından yazdırılan içeriğin şu anda standart çıktıya nasıl yazıldığını gözlemleyelim, 
bunun yerine standart hataya yazmak istediğimiz herhangi bir hata mesajı dahil. Bunu, kasıtlı olarak bir hataya 
neden olurken standart çıktı akışını bir dosyaya yeniden yönlendirerek yapacağız. Standart hata akışını yeniden 
yönlendirmeyeceğiz, bu nedenle standart hataya gönderilen herhangi bir içerik ekranda görüntülenmeye devam edecektir.

Komut satırı programlarının standart hata akışına hata mesajları göndermesi beklenir, böylece standart çıktı akışını 
bir dosyaya yönlendirsek bile ekranda hata mesajlarını görebiliriz.

Bu davranışı göstermek için programı `>` ve standart çıktı akışını yönlendirmek istediğimiz 
*output.txt* dosya adıyla çalıştıracağız. Hataya neden olacak herhangi bir argüman iletmiyoruz:

```console
$ cargo run > output.txt
```

`>` söz dizimi, kabuğa standart çıktının içeriğini ekrana değil de *output.txt* dosyasına yazmasını söyler. 
Ekrana yazdırılmasını beklediğimiz hata mesajını görmedik, bu da dosyada bittiği anlamına geliyor. *output.txt* şunları içerir:

```text
Problem parsing arguments: not enough arguments
```

Evet, hata mesajımız standart çıktıya yazdırılıyor. Bunun gibi hata mesajlarının standart hataya 
yazdırılması çok daha kullanışlıdır, bu nedenle dosyada yalnızca başarılı bir çalıştırmadan 
elde edilen veriler biter. Bunu değiştireceğiz.


### Hataları Standart Hataya Yazdırma

Hata mesajlarının nasıl yazdırılacağını değiştirmek için Liste 12-24'teki kodu kullanacağız. 
Bu bölümde daha önce yaptığımız yeniden düzenleme nedeniyle, hata mesajlarını yazdıran tüm kodlar tek bir fonksiyondadır. 
Standart kütüphane, standart hata akışına yazdıran `eprintln!` makrosunu sağlar, bu yüzden hataları yazdırmak için `println!` 
dediğimiz iki yeri değiştirelim ve bunun yerine `eprintln!` kullanalım.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-24/src/main.rs:here}}
```

<span class="caption">Liste 12-24: `eprintln!` kullanarak standart çıktı yerine 
hata mesajlarını standart hataya yazma</span>

Şimdi programı aynı şekilde, herhangi bir argüman olmadan ve standart çıktıyı `>` ile yeniden 
yönlendirerek tekrar çalıştıralım:


```console
$ cargo run > output.txt
Problem parsing arguments: not enough arguments
```

Şimdi ekranda hatayı görüyoruz ve *output.txt* hiçbir şey içermiyor, bu da komut satırı programlarından beklediğimiz davranış.

Programı, hataya neden olmayan ancak yine de standart çıktıyı bir dosyaya yönlendiren argümanlarla tekrar çalıştıralım, şöyle:

```console
$ cargo run to poem.txt > output.txt
```

Uçbirimde herhangi bir çıktı görmeyeceğiz ve *output.txt* sonuçlarımızı içerecektir:

<span class="filename">Dosya adı: output.txt</span>

```text
Are you nobody, too?
How dreary to be somebody!
```

Bu, başarılı çıktı için standart çıktıyı ve uygun olduğu şekilde hata çıktısı için standart hatayı kullandığımızı gösterir.

## Özet

Bu bölüm, şimdiye kadar öğrendiğiniz bazı temel kavramları özetledi ve Rust'ta genel *G/Ç/ işlemlerinin nasıl gerçekleştirileceğini ele aldı. 
Komut satırı bağımsız değişkenlerini, dosyaları, ortam değişkenlerini ve `eprintln!` makrosunu kullanmaya ve komut 
satırı uygulamaları yazmaya hazırsınız. Önceki bölümlerdeki kavramlarla birleştiğinde, kodunuz iyi organize edilecek, 
verileri uygun veri yapılarında etkin bir şekilde depolayacak, hataları güzel bir şekilde ele alacak ve iyi test edilecektir.

Ardından, fonksiyonel dillerden etkilenen bazı Rust özelliklerini keşfedeceğiz: kapanış ifadeleri ve yineleyiciler.
