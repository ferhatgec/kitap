## Değişkenler ve Değişkenlik

[“Değişkenlerle Değerleri Saklama”][storing-values-with-variables]<!-- ignore --> bölümünde belirtildiği gibi, varsayılan olarak değişkenler değişmezdir. Bu, Rust'ın size sunduğu güvenlik ve kolay eşzamanlılıktan yararlanarak kodunuzu yazmanız için size verdiği birçok dürtüden biridir. Ancak yine de değişkenlerinizi değiştirilebilir yapma seçeneğiniz vardır. Rust'ın sizi değişmezliği tercih etmeye nasıl ve neden teşvik ettiğini ve bazen neden vazgeçmek isteyebileceğinizi keşfedelim.

Bir değişken değişmez olduğunda, bir değer bir ada bağlandıktan sonra bu değeri değiştiremezsiniz. Bunu örneklemek için, `cargo new variables`
komutunu kullanarak *proje dizininizde* *variables* adında yeni bir proje oluşturalım.

Ardından, yeni *variables* dizininizde bulunan *src/main.rs* dosyasını açın ve kodunu aşağıdaki kodla değiştirin. 
Bu kod henüz derlenmeyecek, bundan dolayı önce değişmezlik hatasını inceleyeceğiz.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-01-variables-are-immutable/src/main.rs}}
```

`cargo run` komutunu kullanarak programı kaydedin ve çalıştırın. 
Bu çıktıda gösterildiği gibi bir hata mesajı almalısınız:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-01-variables-are-immutable/output.txt}}
```

Bu örnek, derleyicinin programlarınızdaki hataları bulmanıza nasıl yardımcı olduğunu gösterir.
Derleyici hataları can sıkıcı olabilir, ancak gerçekte bunlar yalnızca programınızın henüz yapmak istediğiniz 
şeyi güvenli bir şekilde yapmadığı anlamına gelir; iyi bir programcı olmadığınız anlamına gelmezler! 
Deneyimli Rustseverler hala derleyici hataları alıyor.

Hata mesajı, hatanın nedeninin, değişmez `x` değişkenine ikinci bir değer atamaya çalıştığınız 
için değişmez "x" değişkenine ikinci kez atayamamanız olduğunu gösterir.

Değişmez olarak belirlenmiş bir değeri değiştirmeye çalıştığımızda derleme zamanı hataları almamız önemlidir çünkü bu durum hatalara yol açabilir. Kodumuzun bir kısmı, bir değerin asla değişmeyeceği varsayımıyla çalışıyorsa ve kodumuzun başka bir kısmı bu değeri değiştiriyorsa, kodun ilk kısmının tasarlandığı şeyi yapmaması olasıdır. Bu tür bir hatanın nedenini, özellikle ikinci kod parçası değeri yalnızca bazen değiştirdiğinde, olaydan sonra bulmak zor olabilir. Rust derleyicisi, bir değerin değişmeyeceğini belirttiğinizde, gerçekten değişmeyeceğini garanti eder, bu nedenle onu kendiniz takip etmek zorunda kalmazsınız. Bu nedenle kodunuzun akıl yürütmesi daha kolaydır.

Ancak değişebilirlik çok faydalı olabilir ve kod yazmayı daha uygun hale getirebilir. Değişkenler yalnızca varsayılan olarak değişmezdir; Bölüm 2'de yaptığınız gibi, değişken adının önüne `mut` ekleyerek bunları değiştirilebilir yapabilirsiniz. `mut` eklemek ayrıca kodun diğer bölümlerinin bu değişkenin değerini değiştireceğini belirterek kodun gelecekteki okuyucularına niyet iletir.

Örneğin, *src/main.rs*'yi aşağıdaki şekilde değiştirelim:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-02-adding-mut/src/main.rs}}
```

Programı şimdi çalıştırdığımızda şunu görürüz:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-02-adding-mut/output.txt}}
```

`mut` kullanıldığında `x`'e bağlı değeri `5`'ten `6`'ya değiştirmemize izin verilir. 
Nihayetinde, `mut` kullanıp kullanmamaya karar vermek size bağlıdır.

### Sabitler

Değişmez değişkenler gibi, 
*sabitler* de bir ada bağlı ve değişmesine izin verilmeyen değerlerdir, 
ancak sabitler ve değişkenler arasında birkaç fark vardır.

İlk olarak, `mut`'u sabitlerle kullanamazsınız. 
Sabitler yalnızca varsayılan olarak değişmez değildir, 
her zaman değişmezdirler. Sabitleri `let` anahtar sözcüğü yerine `const` anahtar sözcüğünü kullanarak bildirirsiniz 
ve değerin türü verilmiş olmalıdır. 
Bir sonraki [“Veri Türleri”][data-types]<!-- ignore --> bölümünde türleri ve tür ek açıklamalarını ele alacağız, 
bu nedenle şu anda ayrıntılar için endişelenmeyin. Her zaman türe değer vermeniz gerektiğini bilin. 
Sabitler, `global` kapsam da dahil olmak üzere herhangi bir kapsamda bildirilebilir, bu da onları kodun birçok bölümünün bilmesi gereken değerler için faydalı kılar. Son fark olarak, sabitlerin çalışma zamanında hesaplanabilecek bir değerin sonucu olarak değil, 
yalnızca sabit bir ifadeye ayarlanabilmesidir. İşte bir sabit bildirim örneği:

```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```

Sabitin adı `THREE_HOURS_IN_SECONDS`'dır ve değeri 60 (bir dakikadaki saniye sayısı) ile 60 (bir saatteki dakika sayısı) ile 3 (bu programda saymak istediğimiz saat sayısı) çarpılmasının sonucuna ayarlanır. Rust'ın sabitler için adlandırma kuralı, sözcükler arasında alt çizgi ile tüm büyük harfleri kullanmaktır. Derleyici, derleme zamanında sınırlı bir dizi işlemi değerlendirebilir; 
bu, bu sabiti 10,800 değerine ayarlamak yerine, bu değeri anlaşılması ve doğrulanması daha kolay bir şekilde yazmayı seçmemize olanak tanır. 
Sabitleri bildirirken hangi işlemlerin kullanılabileceği hakkında daha fazla bilgi için [Rust Reference’ın sabitleri hesaplama][const-eval] bölümüne bakın. Sabitler, bir programın çalıştığı tüm süre boyunca, bildirildikleri kapsam dahilinde geçerlidir. Bu özellik, sabitleri, uygulama etki alanınızdaki, herhangi bir maksimum nokta sayısı gibi, programın birden fazla bölümünün bilmesi gerekebilecek değerler için kullanışlı hale getirir. 
Programınız boyunca kullanılan sabit kodlanmış değerleri sabitler olarak adlandırmak, bu değerin anlamını kodun gelecekteki koruyucularına iletmede faydalıdır. Ayrıca, gelecekte sabit kodlanmış değerin güncellenmesi gerekiyorsa değiştirmeniz gereken kodunuzun yalnızca bir bölümü olacaktır.

### Gölgeleme

[Bölüm 2][comparing-the-guess-to-the-secret-number]<!-- ignore -->'deki tahmin oyununda gördüğünüz gibi, önceki değişkenle aynı ada sahip yeni bir değişken bildirebilirsiniz. Rustseverler, ilk değişkenin ikinci tarafından *gölgelendiğini* söylüyor, bu da ikinci değişkenin, değişkenin adını kullandığınızda derleyicinin göreceği şey olduğu anlamına geliyor. Gerçekte, ikinci değişken birinciyi *gölgede* bırakır ve değişken adının herhangi bir kullanımını kendisi gölgelenene veya kapsam sona erene kadar kendisine alır. Aynı değişkenin adını kullanarak ve `let` anahtar sözcüğünü aşağıdaki gibi tekrarlayarak bir değişkeni gölgeleyebiliriz:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-03-shadowing/src/main.rs}}
```

Bu program önce `x`'e `5` değerini atar, sonra `let x =`'i tekrar ederek, orijinal değeri alıp `1` ekleyerek yeni bir `x` değişkeni oluşturur, 
böylece `x` değeri `6` olur. Parantez içindeki üçüncü `let` ifadesi de `x`'i gölgeler ve `x`'e `12` değerini vermek için 
önceki değeri `2` ile çarparak yeni bir değişken oluşturur. Bu kapsam sona erdiğinde, iç gölgeleme sona erer ve `x`, `6`'ya döner. Bu programı çalıştırdığımızda, aşağıdaki çıktıyı verecektir:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-03-shadowing/output.txt}}
```

Gölgeleme, bir değişkeni `mut` olarak işaretlemekten farklıdır çünkü `let` anahtar sözcüğünü kullanmadan 
yanlışlıkla bu değişkene yeniden atamaya çalışırsak derleme zamanı hatası alırız. `let` kullanarak, bir değer üzerinde birkaç dönüşüm gerçekleştirebiliriz, ancak bu dönüşümler tamamlandıktan sonra değişkenin değişmez olmasını sağlayabiliriz.

`mut` ve shadowing arasındaki diğer fark, `let` anahtar sözcüğünü tekrar kullandığımızda etkin bir şekilde yeni bir değişken oluşturduğumuz için, 
değerin türünü değiştirebilir ancak aynı adı yeniden kullanabiliriz. 
Örneğin, programımızın bir kullanıcıdan boşluk karakterleri girerek bazı metinler arasında kaç boşluk istediğini göstermesini istediğini ve ardından bu girişi bir sayı olarak saklamak istediğimizi varsayalım:

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-04-shadowing-can-change-types/src/main.rs:here}}
```

İlk `spaces` değişkeni bir dize türüdür ve ikinci `spaces` değişkeni bir sayı türüdür. 
Böylece gölgeleme, bizi `space_str` ve `space_num` gibi farklı isimler kullanmaktan kurtarır; bunun yerine, daha basitçe `spaces` adını yeniden kullanabiliriz. Ancak, burada gösterildiği gibi bunun için `mut` kullanmaya çalışırsak, bir derleme zamanı hatası alırız:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-05-mut-cant-change-types/src/main.rs:here}}
```

Hata, bu değişkenin türünü değiştirmemize izin verilmediğini söylüyor:

```console
{{#include ../listings/ch03-common-programming-concepts/no-listing-05-mut-cant-change-types/output.txt}}
```

Artık değişkenlerin nasıl çalıştığını anladığımıza göre, 
sahip olabilecekleri daha fazla veri türüne bakalım.

[comparing-the-guess-to-the-secret-number]:
ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number
[data-types]: ch03-02-data-types.html#data-types
[storing-values-with-variables]: ch02-00-guessing-game-tutorial.html#storing-values-with-variables
[const-eval]: ../reference/const_eval.html
