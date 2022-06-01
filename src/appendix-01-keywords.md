## Ekleme A: Anahtar Sözcükler

Aşağıdaki listede bulunan anahtar sözcükler Rust dilinde şu anda ya da gelecekte kullanılacağı 
için rezerve edilmiştir. Bu nedenle tanımlayıcı olarak kullanılamazlar. (“[Ham Tanımlayıcılar][raw-identifiers]<!-- ignore -->” kısmında tartışacağımız tanımlayıcılar hariç), isimleri dahil olmak üzere
fonksiyonlar, değişkenler, parametreler, yapı girdileri, modüller, kasalar, değişmezler, makrolar,
statik değerler, öznitelikler, türler, tanımlar ya da ömürlükleri kapsar.

[raw-identifiers]: #raw-identifiers

### Şu anda Kullanılan Anahtar Sözcükler

Aşağıdaki anahtar sözcükler açıklanan işlevselliklere sahiptir. 

* `as` - ilkel dönüşüm yapmak, belirli özelliğin türünün belirsizliğini gidermek, 
   ya da kullanımdaki `use` ve `extern crate` ifadelerini yeniden adlandırmak için kullanılır
* `async` - şu andaki ipliği bloklamak yerine `Future` döndürür
* `await` - `Future`'ın sonucu hazır olana kadar çağrıları susturur
* `break` - döngüden çıkar
* `const` - değişmez öğeleri veya sabit işaretçileri tanımlar
* `continue` - sonraki döngü yineleyiciyle devam ettirir
* `crate` - makroda tanımlanan bir kasa değişkenini ya da harici kasayı bağlar
* `dyn` - bir tanım nesnesine dinamik olarak gönderir
* `else` - `if` ya da `if let` kontrol akış yapılarının B planını uygular
* `enum` - numaralandırılmış yapı tanımlar
* `extern` - harici bir kasayı, fonksiyonu ya da değişkeni bağlar
* `false` - Boole yanlış değişmezi
* `fn` - fonksiyon ya da fonksiyon işaretçi türünü tanımlar
* `for` - bir yineleyici üzerinde öğeler kadar döngü yapar, tanım uygular ya da yüksek dereceli
   ömürlüğü belirtir
* `if` - koşullu bir ifadenin sonucuna dayalı dal oluşturur
* `impl` - var olan veya tanımsal işlevselliğini sürekler
* `in` - `for` döngüsünün söz diziminin bir parçası
* `let` - değişken atar
* `loop` - koşulsuz döngüye girer
* `match` - modelleri kullanarak değer eşleştirir
* `mod` - modül tanımlar
* `move` - bir kapanış yaparak tüm yakalamalarının sahipliğini alır
* `mut` - referanslarda, işaretçilerde ya da model atamalarında değişmemezliği belirtir
* `pub` - yapı alanlarında, `impl` bloklarında ya da modüllerde genel görünümlüğü belirtir
* `ref` - referansla atar
* `return` - fonksiyondan döndürür
* `Self` - tanımladığımız ya da süreklediğimiz bir tür için takma ad
* `self` - halihazırdaki modül belirteci
* `static` - program çalıştığından beri tutulan genel değişken ya da ömürlük
* `struct` - yapı tanımlar
* `super` - halihazırdaki modülün bulunduğu ana modül belirteci
* `trait` - tanım tanımlar
* `true` - Boole doğru değişmezi
* `type` - bir tür takma adı veya ilişkili tür tanımlar
* `union` - [birlik] tanımlar ve sadece birlik tanımlarken bir anahtar sözcük görevi görür
* `unsafe` - güvensiz kodlar, fonksiyonlar, tanımlar ya da süreklemeleri belirtir
* `use` - sembolleri kapsama alır
* `where` - bir türü sınırlayan tümceleri belirtir
* `while` - bir ifadenin sonucuna göre çalışan koşullu döngü tanımlar

[birlik]: ../reference/items/unions.html

### Gelecekte Kullanım için Ayrılmış Anahtar Kelimeler

Aşağıdaki anahtar kelimelerin herhangi bir işlevi yoktur, 
ancak gelecekteki potansiyel kullanımdan dolayı Rust tarafından ayrılmıştır.

* `abstract`
* `become`
* `box`
* `do`
* `final`
* `macro`
* `override`
* `priv`
* `try`
* `typeof`
* `unsized`
* `virtual`
* `yield`

### Ham Tanımlayıcılar

*Ham tanımlayıcılar* normalde kullanılmayacakları yerlerde kullanmanıza izin veren söz dizimidir özelliğidir. 
Anahtar sözcüğünüzün başına `r#` koyarak ham işaretçileri kullanabilirsiniz.

Örneğin, `match` bir anahtar sözcüktür. Eğer fonksiyonunuz ad olarak `match` kullandığında onu derlemeye çalışırsanız:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
fn match(needle: &str, haystack: &str) -> bool {
    haystack.contains(needle)
}
```

şu hatayı alırsınız:

```text
error: expected identifier, found keyword `match`
 --> src/main.rs:4:4
  |
4 | fn match(needle: &str, haystack: &str) -> bool {
  |    ^^^^^ expected identifier, found keyword
```
Bu hata size `match`'i fonksiyon adı olarak kullanamayacağınızı gösterir.
`match`'i fonksiyon adı kullanmak için ham tanımlayıcı söz dizimini kullanmanız gerekir, aynı bunun gibi:

<span class="filename">Filename: src/main.rs</span>

```rust
fn r#match(needle: &str, haystack: &str) -> bool {
    haystack.contains(needle)
}

fn main() {
    assert!(r#match("foo", "foobar"));
}
```

Bu kod hatasız derlenir. Not olaraktan, `r#` öneki `main` adıyla anılan 
özel fonksiyon için de kullanılabilir.

Ham tanımlayıcılar sizi hangi sözcüğü kullanacağınız konusunda özgür tutar. Ekleme olaraktan,
ham işaretçiler kasanızın kullandığından farklı Rust sürümünü kullanan kütüphaneleri kullanmanıza izin verir.
Örnek olarak, `try` 2015 sürümünde bir anahtar sözcük değildi, 2018 sürümünde geldi.
Eğer 2015 sürümünü kullanan bir kütüphaneniz varsa ve bu kütüphane `try` diye bir fonksiyona sahipse, 2018
sürümünde bu fonksiyonu çağırabilmeniz için ham tanımlayıcılar kullanmanız gerekir.
Sürümler hakkında daha fazla bilgiye erişmek için [Ekleme
E][ekleme-e]<!-- ignore --> bölümüne göz atabilirsiniz.

[ekleme-e]: appendix-05-editions.html
