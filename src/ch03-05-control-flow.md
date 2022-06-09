## Kontrol Akışı

Bir koşulun doğru olup olmadığına bağlı olarak bazı kodları çalıştırma veya bir
koşul doğruyken bazı kodları tekrar tekrar çalıştırma yeteneği, 
çoğu programlama dili için temel yapı taşıdır. Rust kodunun yürütme akışını kontrol etmenizi 
sağlayan en yaygın yapılar `if` ifadeleri ve döngülerdir.

### `if` İfadeleri

Bir `if` ifadesi, koşullara bağlı olarak kodunuzu dallandırmanıza olanak tanır. 
Bir koşul sağlarsınız ve ardından “Eğer bu koşul karşılanırsa, bu kod bloğunu çalıştırın. Koşul karşılanmazsa, bu kod bloğunu çalıştırmayın” emrini verirsiniz.

`if` ifadesini keşfetmek için proje dizininizde *branches* adında yeni bir proje oluşturun. 
*src/main.rs* dosyasına aşağıdakini girin:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-26-if-true/src/main.rs}}
```

Tüm `if` ifadeleri, `if` anahtar sözcüğüyle başlar ve ardından bir koşul gelir. 
Bu durumda koşul, `number`'ın 5'ten küçük bir değere sahip olup olmadığını kontrol eder. 
Koşul doğruysa yürütülecek kod bloğunu, koşulun hemen ardından süslü parantezler içine yerleştiririz. 
`if` ifadelerindeki koşullarla ilişkili kod blokları, tıpkı Bölüm 2'deki [“Tahmin ile Gizli Numarayı Karşılaştırma”][comparing-the-guess-to-the-secret-number]<!--
ignore --> bölümünde tartıştığımız `match` ifadelerindeki *kollar* gibi bazen *kol* olarak adlandırılır.

İsteğe bağlı olarak, koşulun yanlış olarak değerlendirilmesi durumunda programa yürütülecek alternatif bir kod bloğu
vermek için burada yapmayı seçtiğimiz başka bir ifade de ekleyebiliriz. Başka bir ifade sağlamazsanız ve koşul yanlışsa, 
program `if` bloğunu atlar ve bir sonraki kod parçasına geçer.

Bu kodu çalıştırmayı deneyin, aşağıdaki çıktıyı görmelisiniz:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-26-if-true/output.txt}}
```

Ne olduğunu görmek için `number`ın değerini koşulu `false` yapan bir değerle değiştirmeyi deneyelim:
 
```rust,ignore
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-27-if-false/src/main.rs:here}}
```

Programı tekrar çalıştırın ve çıktıya bakın:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-27-if-false/output.txt}}
```

Bu koddaki koşulun `bool` türünden *olması gerektiğini* de belirtmekte fayda var. 
Koşul `bool` değilse, bir hata alırız. Örneğin, aşağıdaki kodu çalıştırmayı deneyin:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-28-if-condition-must-be-bool/src/main.rs}}
```

`if` koşulu bu sefer `3` değerini değerlendiriyor ve Rust buna karşılık bir hata veriyor:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-28-if-condition-must-be-bool/output.txt}}
```

Hata, Rust'ın bir `bool` beklediğini ancak bir tam sayı aldığını gösteriyor. 
Ruby ve JavaScript gibi dillerin aksine Rust, Boole olmayan türleri otomatik olarak 
Boole'a dönüştürmeye çalışmaz. Açık olmalısınız ve her zaman koşulun Boole olup olmadığını sağlamalısınız. 
Örneğin `if` kod bloğunun yalnızca bir sayı `0`'a eşit olmadığında çalışmasını istiyorsak, `if` ifadesini aşağıdaki gibi değiştirebiliriz:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-29-if-not-equal-0/src/main.rs}}
```

Bu kodu çalıştırmak bize şu çıktıyı verecektir: `number was something other than zero`.

#### `else if` ile Birden Çok Koşulun İşlenmesi

Bir `else if` ifadesini `if` ve `else` ile birleştirerek birden çok koşul durumunda kullanabilirsiniz. 
Örneğin:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-30-else-if/src/main.rs}}
```

Bu programın alabileceği dört olası durum vardır. Çalıştırdıktan sonra aşağıdaki çıktıyı görmelisiniz

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-30-else-if/output.txt}}
```

Bu program yürütüldüğünde, sırayla her bir `if` ifadesini kontrol eder ve koşulun doğru 
olduğu ilk gövdeyi yürütür. `6`'nın 2`'`ye bölünebilmesine rağmen, çıktıda `number is divisible by 2`'yu görmüyor 
ve `else`'e rağmen çıktıda `number is not divisible by 4, 3, or 2`'yu görmüyoruz. 
Bunun nedeni, Rust'ın bloğu yalnızca ilk gerçek koşul için çalıştırmasıdır ve 
bir kez doğru koşulu bulduğunda gerisini kontrol bile etmez.

Çok fazla `else if` ifadesi kullanmak kodunuzu karıştırabilir, bu nedenle birden fazla varsa, 
kodunuzu yeniden düzenlemek isteyebilirsiniz. Bölüm 6, bu durumlar için `match` adı verilen güçlü bir 
Rust dallanma yapısını açıklar.

#### `if`'i `let`'te İfade Yapısı Olarak Kullanmak

`if` bir ifade olduğu için, Liste 3-2'de olduğu gibi sonucu bir değişkene atamak için `let` ifadesinin sağ tarafında kullanabiliriz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-02/src/main.rs}}
```

<span class="caption">Liste 3-2: Bir `if` ifadesinin sonucunu bir değişkene atama</span>

`number` değişkenine, `if` ifadesinin sonucuna göre bir değer atanacaktır. 
Ne olduğunu görmek için bu kodu çalıştırın:

```console
{{#include ../listings/ch03-common-programming-concepts/listing-03-02/output.txt}}
```

Kod bloklarının içlerindeki son ifadeyi değerlendirdiğini ve sayıların 
kendi başlarına da ifadeler olduğunu unutmayın. Bu durumda, `if` ifadesinin tamamının değeri, 
hangi kod bloğunun yürütüldüğüne bağlıdır. Bu, `if`'in her bir kolundan sonuç alma potansiyeline sahip 
değerlerin aynı tür olması gerektiği anlamına gelir; Liste 3-2'de, hem `if` kolunun hem de `else` kolunun sonuçları 
`i32` tam sayı türündendi. Aşağıdaki örnekte olduğu gibi, türler uyumsuzsa bir hata alırız:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-31-arms-must-return-same-type/src/main.rs}}
```

Bu kodu derlemeye çalıştığımızda bir hata alacağız. `if` ve `else` kollarının uyumsuz değer türleri vardır 
ve Rust, sorunun programda tam olarak nerede bulunacağını bir hata mesajıyla belirtir:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-31-arms-must-return-same-type/output.txt}}
```

`if` bloğundaki ifade bir tam sayı olarak değerlendirilir ve `else` bloğundaki ifade bir 
dizgi olarak değerlendirilir. Bu işe yaramaz çünkü değişkenlerin tek bir türü olması gerekir ve 
Rust'ın derleme zamanında sayı değişkeninin ne tür olduğunu kesin olarak bilmesi gerekir. 
Sayının türünü bilmek, derleyicinin sayıyı kullandığımız her yerde türün geçerli olduğunu doğrulamasını sağlar. 
Sayının türü yalnızca çalışma zamanında belirlenmiş olsaydı Rust bunu yapamazdı; derleyici daha karmaşık olurdu ve herhangi bir değişken için birden çok varsayımsal türü takip etmesi gerekiyorsa kod hakkında daha az garanti verirdi.

### Döngülerle Yinelemek

Bir kod bloğunu bir kereden fazla yürütmek genellikle yararlıdır. 
Bu görev için Rust, döngü gövdesi içindeki kodu sonuna kadar çalıştıracak ve ardından hemen 
baştan başlayacak birkaç *döngü* sağlar. Döngüleri denemek için *loops* adında yeni bir proje yapalım.

Rust'un üç tür döngüsü vardır: `loop`, `while` ve `for`. Her birini deneyelim.

#### `loop` ile Kod Yinelemek

`loop` anahtar sözcüğü, Rust'a bir kod bloğunu sonsuza kadar veya siz açıkça durmasını söyleyene kadar tekrar tekrar yürütmesini söyler.

Örnek olarak, *loops* dizininizdeki *src/main.rs* dosyasını şöyle görünecek şekilde değiştirin:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-32-loop/src/main.rs}}
```

Bu programı çalıştırdığımızda, programı manuel olarak durdurana kadar sürekli olarak `again!` 
yazdırıldığını göreceğiz. Çoğu uçbirim <span class="keystroke">ctrl-c</span> kısayolunu döngüden çıkabilmek
için sunar. Bir şans verin:

<!-- manual-regeneration
cd listings/ch03-common-programming-concepts/no-listing-32-loop
cargo run
CTRL-C
-->

```console
$ cargo run
   Compiling loops v0.1.0 (file:///projects/loops)
    Finished dev [unoptimized + debuginfo] target(s) in 0.29s
     Running `target/debug/loops`
again!
again!
again!
again!
^Cagain!
```

`^C` sembolü, <span class="keystroke">ctrl-c</span> tuşlarına bastığınız yeri gösterir.
Kesme sinyalini aldığınızda kodun döngüde nerede olduğuna bağlı olarak `^C`'den sonra döngü durur.

Neyse ki, Rust ayrıca kod kullanarak bir döngüden çıkmanın bir yolunu da sağlar. 
Programa döngüyü yürütmeyi ne zaman durduracağını söylemek için `break` anahtar sözcüğünü döngünün içine yerleştirebilirsiniz. 
Bunu Bölüm 2'deki [“Doğru Tahminden Sonra Çıkma”][quitting-after-a-correct-guess]<!-- ignore
-->  bölümündeki tahmin oyununda, kullanıcı doğru sayıyı tahmin ederek oyunu kazandığında programdan çıkmak için yaptığımızı hatırlayın.

Ayrıca, bir döngüde programa döngünün bu yinelemesinde kalan herhangi bir kodu atlamasını ve bir sonraki yinelemeye geçmesini söyleyen tahmin oyununda `continue`'ı kullandık.

#### Döngülerden Değer Döndürmek

`loop`'un kullanımlarından biri, bir iş parçacığının işini tamamlayıp tamamlamadığını kontrol etmek 
gibi başarısız olabileceğini bildiğiniz bir işlemi yeniden denemektir. Ayrıca, bu işlemin sonucunu döngüden kodunuzun geri kalanına aktarmanız gerekebilir. Bunu yapmak için, döngüyü durdurmak için kullandığınız `break` ifadesinden sonra döndürülmesini istediğiniz değeri ekleyebilirsiniz; bu değer, burada gösterildiği gibi kullanabilmeniz için döngüden döndürülecektir:

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-33-return-value-from-loop/src/main.rs}}
```

Döngüden önce `counter` adında bir değişken tanımlıyoruz ve onu `0` olarak başlatıyoruz. 
Ardından döngüden dönen değeri tutacak `result` adında bir değişken tanımlıyoruz. 
Döngünün her yinelemesinde `counter` değişkenine `1` ekliyoruz ve ardından `counter`'ın `10`'a eşit olup olmadığını kontrol ediyoruz. Eşitse `counter * 2` değerini `break` anahtar sözcüğüyle kullanıyoruz. Döngüden sonra noktalı virgül kullanıyoruz. 
`result`'a değer atayan ifadeyi bitirmek için; son olarak, `20` olan `result` değerini yazdırıyoruz.

#### Birden Çok Döngü Arasındaki Belirsizliği Gidermek için Döngü Etiketleri

Döngüler içinde döngüleriniz varsa, o noktada en içteki döngü için `break` ve `continue` ifadeleri uygulanır. 
İsteğe bağlı olarak bir döngü üzerinde bir *döngü etiketi* belirleyebilirsiniz ve daha sonra bu anahtar sözcüklerin en içteki döngü yerine etiketli döngüye uygulanacağını belirtmek için `break` veya `continue` ile kullanabiliriz.
İşte iki iç içe döngü içeren bir örnek:

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-32-5-loop-labels/src/main.rs}}
```

Dış döngü `'counting_up` etiketine sahiptir ve 0'dan 2'ye kadar sayar. 
Etiketsiz iç döngü 10'dan 9'a geri sayım yapar. Bir etiket belirtmeyen ilk `break` yalnızca iç döngüden çıkar. 
`break 'counting_up`; ifadesi dış döngüden çıkar:

```console
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-32-5-loop-labels/output.txt}}
```

#### `while` ile Koşullu Döngüler

Bir programın genellikle bir döngü içindeki bir koşulu değerlendirmesi gerekir. 
Koşul doğru olduğunda döngü çalışır. Koşul doğru olmadığında, program `break`'i çağırarak döngüyü durdurur. 
Böyle bir davranışı `loop`, `if`, `else` ve `break` kombinasyonunu kullanarak uygulamak mümkündür; 
İsterseniz bunu şimdi bir programda deneyebilirsiniz. Ancak, bu model o kadar yaygındır ki, 
Rust'ın bunun için `while` döngüsü adı verilen yerleşik bir dil yapısı vardır. 
Liste 3-3'te, programı üç kez döngüye almak, her seferinde geri saymak ve ardından döngüden sonra bir mesaj yazdırıp çıkmak 
için `while` kullanıyoruz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-03/src/main.rs}}
```

<span class="caption">Liste 3-3: Bir koşul doğruyken kodu çalıştırmak için `while` döngüsü kullanma</span>

Bu yapı, `loop`, `if`, `else` ve `break` kullandıysanız gerekli olacak birçok iç içe yerleştirmeyi ortadan kaldırır ve daha
nettir. Bir koşul doğru olduğunda kod çalışır; aksi takdirde döngüden çıkar.

#### `for` ile Bir Koleksiyonda Yineleme Yapmak

Dizi gibi bir koleksiyonun öğeleri üzerinde döngü oluşturmak için `while` yapısını kullanmayı seçebilirsiniz. 
Örneğin, Liste 3-4'teki döngü `a` dizisindeki her öğeyi yazdırır.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-04/src/main.rs}}
```

<span class="caption">Liste 3-4: Bir `while` döngüsü kullanarak bir koleksiyonun her bir öğesi arasında döngü yapmak</span>

Burada kod, dizideki öğeleri sayar. `0` dizininde başlar ve ardından dizideki son dizine ulaşana kadar döner 
(yani `index < 5` doğru olmayana kadar). Bu kodu çalıştırmak dizideki her öğeyi yazdıracaktır.

```console
{{#include ../listings/ch03-common-programming-concepts/listing-03-04/output.txt}}
```

Beş dizi değerinin tümü beklendiği gibi terminalde görünür. 
Dizin bir noktada `5` değerine ulaşacak olsa da, diziden altıncı bir değer getirmeye çalışmadan önce 
döngü kendini yürütmeyi durdurur.

Ancak bu yaklaşım hataya açıktır; `index` değeri veya test koşulu yanlışsa programın paniğe 
kapılmasına neden olabiliriz. Örneğin, `a` dizisinin tanımını dört öğeye sahip olacak şekilde değiştirdiyseniz 
ancak `index < 4` iken koşulu güncellemeyi unuttuysanız, kod panikleyecektir. 
Ayrıca bu yavaştır, çünkü derleyici döngü boyunca her yinelemede dizinin dizinin sınırları içinde olup 
olmadığının koşullu kontrolünü gerçekleştirmek için çalışma zamanı kodu ekler.

Daha özlü bir alternatif olarak, bir `for` döngüsü kullanabilir ve bir koleksiyondaki her öğe için bir miktar kod çalıştırabilirsiniz. `for` döngüsü, Liste 3-5'teki koda benzer.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/listing-03-05/src/main.rs}}
```

<span class="caption">Liste 3-5: Bir `for` döngüsü kullanarak bir koleksiyonun her bir öğesi arasında yinelemek</span>

Bu kodu çalıştırdığımızda, Liste 3-4'teki çıktının aynısını göreceğiz. 
Daha da önemlisi, artık kodun güvenliğini artırdık ve dizinin sonunun ötesine geçmek veya 
yeterince uzağa gitmemek ve bazı öğeleri kaçırmaktan kaynaklanabilecek hata olasılığını ortadan kaldırdık.

`for` döngüsünü kullanarak, Liste 3-4'te kullanılan yöntemde olduğu gibi dizideki değerlerin 
sayısını değiştirdiyseniz, başka herhangi bir kodu değiştirmeniz gerekmez.

`for` döngülerinin güvenliği ve kısa olması, onları Rust'ta en yaygın kullanılan 
döngü yapısı haline getirir. Liste 3-3'te `while` döngüsü kullanan geri sayım örneğinde olduğu gibi, bazı kodları belirli sayıda çalıştırmak istediğiniz durumlarda bile, çoğu `Rustacean` bir `for` döngüsü kullanır. 
Bunu yapmanın yolu, bir sayıdan başlayıp diğer bir sayıdan önce biten tüm sayıları sırayla üreten 
standart kütüphane tarafından sağlanan `Range` tanımını kullanmaktır.

Aralığı tersine çevirmek için bir `for` döngüsü ve henüz bahsetmediğimiz başka bir yöntem olan `rev` kullanarak geri 
sayım işleminin nasıl görüneceği aşağıda açıklanmıştır:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-34-for-range/src/main.rs}}
```

Bu kod sizce de daha hoş durmuyor mu?

## Özet

Başardın! Bu oldukça büyük bir bölümdü: değişkenler, skaler ve bileşik veri türleri, fonksiyonlar, yorumlar, 
`if` ifadeleri ve döngüler hakkında çokça bilgi edindiniz! 
Bu bölümde tartışılan kavramlarla pratik yapmak için aşağıdaki programları oluşturmaya
çalışın:

* Fahrenheit ve Santigrat türleri arasında dönüşüm yapan programı yazın.
* n'inci Fibonaccı sayısını oluşturan programı yazın.
* Şarkıdaki tekrarlardan yararlanarak Noel şarkısı “The Twelve Days of Christmas”'ın sözlerini yazdırın.

Devam etmeye hazır olduğunuzda, Rust'ta diğer programlama dillerinde *olmayan* bir kavram olan *sahiplikten*
bahsedeceğiz.

[comparing-the-guess-to-the-secret-number]:
ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number
[quitting-after-a-correct-guess]:
ch02-00-guessing-game-tutorial.html#quitting-after-a-correct-guess
