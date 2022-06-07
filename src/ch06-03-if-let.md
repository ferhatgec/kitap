## `if let` ile Kontrol Akışı

`if let` söz dizimi, `if` ve `let` sözcüklerini bir modelle eşleşen değerleri işlemek ve gerisini 
yok saymak için daha az ayrıntılı bir şekilde birleştirmenize olanak tanır. Liste 6-6'daki, `config_max` değişkenindeki bir `Option<u8>` değeriyle eşleşen, ancak yalnızca değer `Some` değişkeniyse kodu yürütmek isteyen programı düşünün.
ode if the value is the `Some` variant.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-06/src/main.rs:here}}
```

<span class="caption">Liste 6-6: Değer yalnızca `Some` olduğunda kodu çalıştırmayı önemseyen bir `eşleşme`</span>

Eğer değer `Some` ise, değeri modeldeki `max` değişkenine atayarak `Some` varyantındaki değeri yazdırırız. 
`None` değeriyle hiçbir şey yapmak istemiyoruz. `match` ifadesi için, yalnızca bir değişkeni işledikten sonra `_ => ()` eklemeliyiz, 
bu da eklemesi can sıkıcı bir koddur.

Bunun yerine, `if let` yapısını kullanarak bunu daha kısa bir şekilde yazabiliriz. 
Aşağıdaki kod, Liste 6-6'daki `match` yapısıyla aynı şekilde davranır:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-12-if-let/src/main.rs:here}}
```

`if let` söz dizimi, eşittir işaretiyle ayrılmış bir model ve ifade alır. 
İfadenin `match`'e verildiği ve modelin ilk kolu olduğu bir `match` ile aynı şekilde çalışır. 
Bu durumda, model `Some(max)` şeklindedir ve `max`, `Some` içindeki değere atanır. Daha sonra, `match` yapısında `max`'ı kullandığımız gibi, 
`if let` bloğunun gövdesinde de `max`ı kullanabiliriz. Değer modelle eşleşmezse `if let` bloğundaki kod çalışmaz.

`if let` kullanmak, daha az yazma, daha az girinti ve daha az ilintili kod anlamına gelir.
Ancak, `match`'in zorunlu kıldığı kapsamlı denetimi kaybedersiniz. `match` ve `if let` arasında seçim yapmak, 
kendi özel durumunuzda ne yaptığınıza ve kısalık kazanmanın kapsamlı kontrolü kaybetmek için uygun bir değiş tokuş olup olmadığına bağlıdır.

Başka bir deyişle, `if let` ifadesini, değer bir model eşleştiğinde kodu çalıştıran ve ardından diğer tüm değerleri yok sayan bir `match` için 'sözdizimsel tatlılık' olarak düşünebilirsiniz.

Bir `if let`'e `else` ekleyebiliriz. `else` ile gelen kod bloğu, `if let` ve `else`'e eş değer olan `match` ifadesindeki `_` 
durumu ile kullanılacak kod bloğu ile aynıdır. `Quarter` varyantının da bir `UsState` değerine sahip olduğu Liste 6-4'teki 
`Coin` numralandırılmış yapı tanımını hatırlayın. Çeyreklerin durumunu açıklarken gördüğümüz tüm çeyrek olmayan paraları saymak istersek, 
bunu şöyle bir eşleşme ifadesi ile yapabilirdik:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-13-count-and-announce-match/src/main.rs:here}}
```

Veya şu şekildeki bir `if let` ve `else` ifadesini kullanabiliriz:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-14-count-and-announce-if-let-else/src/main.rs:here}}
```

Programınızın bir `match` kullanarak ifade edemeyecek kadar ayrıntılı bir mantığının olduğu bir durumunuz varsa, 
`if let`'in de Rust araç kutunuzda olduğunu unutmayın.

## Özet

Şimdi, bir dizi numaralandırılmış değerden biri olabilecek özel türler oluşturmak için 
numaralandırmaların nasıl kullanılacağını ele aldık. Standart kütüphanenin sunmuş olduğu `Option<T>` türünün, 
hataları önlemek için tür sistemini kullanmanıza nasıl yardımcı olduğunu gösterdik. 
Numaralandırılmış yapı değerlerinin içinde veriler olduğunda, 
kaç modeli ele almanız gerektiğine bağlı olarak bu değerleri çıkarmak ve kullanmak için `match` veya `if let` kullanabilirsiniz.

Rust programlarınız artık yapıları ve numaralandırmaları kullanarak etki alanınızdaki kavramları ifade edebilir. 
API'nizde kullanılacak özel türler oluşturmak, tür güvenliğini sağlar: derleyici, fonksiyonlarınızın yalnızca her işlevin beklediği türden değerleri almasını sağlar.

Kullanıcılarınıza iyi organize edilmiş, kullanımı kolay ve yalnızca kullanıcılarınızın ihtiyaç duyacaklarını tam olarak ortaya koyan bir API sağlamak için şimdi Rust'ın modüllerine dönelim.
