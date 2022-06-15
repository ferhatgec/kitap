<a id="the-match-control-flow-operator"></a>
## `match` Kontrol Akışı Yapısı

Rust, bir değeri bir dizi modelle karşılaştırmanıza ve ardından hangi modelin eşleştiğine göre kod yürütmenize olanak tanıyan, 
`match` adı verilen son derece güçlü bir kontrol akışı yapısına sahiptir. Modeller değişmez değerlerden, 
değişken adlarından, joker karakterlerden ve diğer birçok şeyden oluşabilir; 
Bölüm 18, tüm farklı kalıp türlerini ve ne yaptıklarını kapsar. 
`match`'in gücü, modellerin ifade gücünden ve derleyicinin tüm olası vakaların ele alındığını doğrulamasından gelir.

Bir `match` ifadesini madeni para ayırma makinesi gibi düşünün: madeni paralar, üzerinde çeşitli büyüklükte delikler 
bulunan bir raydan aşağı kayar ve her madeni para, karşılaştığı ilk delikten girer ve sığar. Aynı şekilde, 
değerler bir `match`'te her bir modelden geçer ve ilk modelde değer “uyar”, değer, yürütme sırasında kullanılacak ilgili kod bloğuna düşer.

Madeni paralardan bahsetmişken, onları `match`'i kullanırken örnek verebiliriz! 
Bir Birleşik Devletler madeni parasını alan ve sayma makinesine benzer bir şekilde, 
hangi madeni para olduğunu belirleyen ve burada Liste 6-3'te gösterildiği gibi değerini 
sent olarak döndüren bir fonksiyon yazabiliriz.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-03/src/main.rs:here}}
```

<span class="caption">Liste 6-3: Bir `enum` ve bir `match` ifadesi, 
model olarak `enum` türüne sahip</span>

`value_in_cents` fonksiyonundaki `match`'i parçalayalım. 
İlk olarak, eşleşen anahtar kelimeyi ve ardından bu durumda jeton değeri olan bir ifadeyi listeleriz. 
Bu, `if` ile kullanılan bir ifadeye çok benziyor, ancak büyük bir fark var: 
`if` ile ifadenin bir Boole değeri döndürmesi gerekiyor, ancak burada herhangi bir tür döndürebilir. 
Bu örnekteki jeton türü, ilk satırda tanımladığımız `Coin`; `enum`'dur.

Sıradaki `match` kolları. Bu kolların iki parçası vardır: 
bir model ve bir miktar kod. Buradaki ilk kol `Coin::Penny` değerinde bir modele ve ardından model ile çalıştırılacak kodu ayıran 
`=>` operatörüne sahiptir. Bu durumda kod sadece `1` değeridir. Her kol bir sonrakinden virgülle ayrılır.

`match` ifadesi yürütüldüğünde, elde edilen değeri sırayla her bir kolun modeliyle karşılaştırır. 
Bir model değerle eşleşirse, o kalıpla ilişkili kod yürütülür. Bu model değerle eşleşmezse, 
yürütme bir madeni para sıralama makinesinde olduğu gibi bir sonraki kola devam eder. 
İhtiyacımız olduğu kadar çok kola sahip olabiliriz: Liste 6-3'te `match`'imizde dört kol var.

Her kolla ilişkili kod bir ifadedir ve eşleşen koldaki ifadenin sonuç değeri, 
tüm `match` ifadesi için döndürülen değerdir.

Her bir kolun yalnızca bir değer döndürdüğü Liste 6-3'te olduğu gibi, 
`match` kolu kodu kısaysa genellikle süslü parantezler kullanmayız. Bir `match` kolunda birden çok kod satırı 
çalıştırmak istiyorsanız, süslü parantezleri kullanmanız gerekir ve kolu takip eden virgül isteğe bağlıdır. 
Örneğin, aşağıdaki kod “Lucky penny!” yazdırır. Metod bir `Coin::Penny` ile her çağrıldığında, 
ancak bloğun son değeri olan `1`'i döndürür:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-08-match-arm-multiple-lines/src/main.rs:here}}
```

### Değerlere Bağlanan Modeller

`match` kollarının bir başka kullanışlı özelliği de değerlerin modelle eşleşen kısımlarına bağlanabilmeleridir. 
`enum` değişkenlerinden değerleri bu şekilde çıkarabiliriz.

Örnek olarak, `enum` değişkenlerimizden birini veriyi içinde tutacak şekilde değiştirelim. 
1999'dan 2008'e kadar Amerika Birleşik Devletleri, bir taraftaki 50 eyaletin her biri için farklı tasarımlara sahip paralar bastı. 
Başka hiçbir madeni paranın bu tarz bir devlet tasarımı yoktur, bu nedenle yalnızca çeyrekler buna sahiptir. 
Bu bilgiyi, `Quarter` değişkenini, burada Liste 6-4'te yaptığımız, içinde saklanan bir `UsState` değerini içerecek şekilde değiştirerek 
`enum`'a ekleyebiliriz.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-04/src/main.rs:here}}
```

<span class="caption">Liste 6-4: `Quarter` değişkeninin de bir `UsState` değerine sahip olduğu bir `Coin` `enum`'u</span>

Bir arkadaşın 50 eyalet parasının tamamını toplamaya çalıştığını düşünelim. 
Bozuk paramızı madeni para türüne göre sıralarken, her üç aylık dönemle ilişkili eyaletin adını da söyleyeceğiz, böylece arkadaşımızın sahip olmadığı
bir şey varsa, koleksiyonlarına ekleyebilirler.

Bu kodun `match` ifadesinde, `Coin::Quarter` varyantının değerleriyle eşleşen modele `state` adlı bir değişken ekleriz. 
`Coin::Quarter` eşleştiğinde, `state` değişkeni o çeyreğin durumunun değerine bağlanır. 
Daha sonra bu kolun kodundaki `state`'i şu şekilde kullanabiliriz:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-09-variable-in-pattern/src/main.rs:here}}
```

`value_in_cents(Coin::Quarter(UsState::Alaska))` olarak çağıracak olsaydık, 
`coin` `Coin::Quarter(UsState::Alaska)` olurdu. Bu değeri `match` kollarının her biri ile karşılaştırdığımızda, 
`Coin::Quarter(state)` değerine ulaşana kadar hiçbiri eşleşmez. 
Bu noktada, `state` için değer `UsState::Alaska` olacaktır. Daha sonra bu atamayı `println!`'de kullanabiliriz.

### `Option<T>`'la Eşleştirme

Önceki bölümde, `Option<T>` kullanırken `Some` durumundan iç `T` değerini almak istedik; 
`Option<T>` ile `Coin` `enum`'da yaptığımız gibi `match`'i kullanarak da işleyebiliriz! 
Madeni paraları karşılaştırmak yerine, `Option<T>`'nin türevlerini karşılaştıracağız, 
ancak `match` ifadesinin çalışma şekli aynı kalıyor.

Diyelim ki `Option<i32>` alan ve içinde bir değer varsa bu değere `1` ekleyen bir fonksiyon yazmak istiyoruz. 
İçinde bir değer yoksa, fonksiyon `None` değerini döndürmeli ve herhangi bir işlem yapmaya çalışmamalıdır.

Bu fonksiyonun yazılması, `match` sayesinde çok kolaydır ve Liste 6-5'e benzeyecektir.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:here}}
```

<span class="caption">Liste 6-5: `Option<i32>` için `match` ifadesi kullanan bir fonksiyon</span>

Şimdi `plus_one`'ın ilk süreklemesini daha detaylı inceleyelim. 
`plus_one(five` dediğimizde, `plus_one` gövdesindeki `x` değişkeni `Some(5)` değerine sahip olacaktır. 
Daha sonra bunu her bir `match` koluyla karşılaştırırız.


```rust,ignore
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:first_arm}}
```

`Some(5)` değeri `None` ile eşleşmediğinden dolayı sonraki kolla devam ediyoruz:  

```rust,ignore
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:second_arm}}
```

`Some(5)` `Some(i)` ile eşleşiyor mu? Neden evet olduğuna bakalım! 
Aynı varyanta sahibiz. `i`'ye `Some` içindeki değer atanır, bu yüzden `5` değerini alır. 
`match` kolundaki kod daha sonra çalıştırılır, bu yüzden `i`'nin değerine `1` ekliyoruz ve içindeki 
toplam `6` ile yeni bir `Some` değeri oluşturuyoruz.

Şimdi, `x`'in `None` olduğu Liste 6-5'teki ikinci `plus_one` çağrısını ele alalım. 
`match`'e giriyoruz ve ilk kolla karşılaştırıyoruz.

```rust,ignore
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:first_arm}}
```

Eşleşiyor! Eklenecek bir değer yoktur, bu nedenle program durur ve `=>` öğesinin sağ tarafındaki `None` değerini döndürür. 
İlk kol eşleştiği için diğer kollar karşılaştırılmaz.

`match` ve `enum`'u birleştirmek çoğu durumda yararlıdır. 
Bu modeli birçok Rust kodunda göreceksiniz: `enum` eşleştirmek, içindeki verilere bir değişken bağlamak ve ardından buna dayalı olarak kod yürütmek. 
İlk başta biraz zor ama alıştıktan sonra tüm dillerde olmasını dileyeceksiniz. Rustseverlerin favorisidir.

### `match`'ler Kapsamlıdır

`match`'in tartışmamız gereken başka bir yönü daha var: 
kollardaki modeller tüm olasılıkları kapsamalıdır. 
Bir hata içeren ve derlenmeyecek olan `plus_one` fonksiyonumuzun bu sürümünü düşünün:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-10-non-exhaustive-match/src/main.rs:here}}
```

`None` durumunu ele almadık yani eğer `None` `match`'e argüman olarak verilirse bir sorun ortaya çıkacaktır.
Şansımıza ki, bu sorun Rust'ın nasıl çözeceğini bildiği bir sorundur. Eğer kodu çalıştırırsak, Rust bize bu hatayı
verecektir:

```console
{{#include ../listings/ch06-enums-and-pattern-matching/no-listing-10-non-exhaustive-match/output.txt}}
```
  
Rust, olası her durumu ele almadığımızı ve hatta hangi modeli unuttuğumuzu biliyor! 
Rust'taki `match`'ler *kapsamlıdır*: Kodun geçerli olması için son tüm olasılıkları ele almalıyız. 
Özellikle `Option<T>` durumunda; Rust, `None` durumunu açıkça ele almayı unutmamızı engellediğinde, 
`null`'a sahip olabileceğimiz bir değere sahip olduğumuzu varsaymaktan bizi korur, 
böylece daha önce tartışılan milyar dolarlık hatayı imkansız hale getirir.
  
### Tümünü Yakalama Modelleri ve `_` Yer Tutucusu

`enum`'u kullanarak, belirli birkaç değer için özel eylemler de yapabiliriz, 
ancak diğer tüm değerler için bir varsayılan eylem gerçekleştirir. Bir zar atarken 3 atarsanız, 
oyuncunuzun hareket etmediği, bunun yerine yeni bir süslü şapka aldığı bir oyun uyguladığımızı hayal edin. 
7 atarsanız, oyuncunuz süslü bir şapka kaybeder. Diğer tüm değerler için, oyuncunuz oyun tahtasında bu sayıda 
yeri hareket ettirir:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-15-binding-catchall/src/main.rs:here}}
```

İlk iki kol için modeller, 3 ve 7 değişmez değerleridir. Diğer tüm olası değerleri kapsayan son kol için, 
model, diğer olarak adlandırmayı seçtiğimiz değişkendir. Diğer kol için çalışan kod, 
değişkeni `move_player` fonksiyonuna geçirerek kullanır.

Bu kod, bir `u8`'in sahip olabileceği tüm olası değerleri listelememiş olsak bile derlenir, 
çünkü son model özel olarak listelenmemiş tüm değerlerle eşleşecektir. Bu tümünü yakalama modeli, 
`match`'in kapsamlı olması gerekliliğini karşılar. Modeller sırayla değerlendirildiği için tümünü yakalama kolunu 
en sona koymamız gerektiğini unutmayın. Her şeyi yakalama kolunu daha erken koyarsak, diğer kollar asla çalışmaz, 
bu yüzden hepsini yakalamadan sonra kolları eklersek Rust bizi uyarır!

Rust'ta ayrıca hepsini yakalamak istediğimizde ancak tümünü yakalama modelindeki değeri kullanmak istemediğimizde kullanabileceğimiz 
bir model vardır: `_` herhangi bir değerle eşleşen ve o değere bağlanmayan özel bir modeldir. 
Bu, Rust'a değeri kullanmayacağımızı söyler, bu nedenle Rust bizi kullanılmayan bir değişken hakkında uyarmaz.

Oyunun kurallarını değiştirelim: şimdi, 3 veya 7'den başka bir şey atarsanız, tekrar atmanız gerekir. 
Artık tümünü yakalama değerini kullanmamız gerekmiyor, bu nedenle kodumuzu `other` adlı değişken yerine `_` kullanacak şekilde 
değiştirebiliriz:  

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-16-underscore-catchall/src/main.rs:here}}
```
  
Bu örnek ayrıca, son koldaki diğer tüm değerleri açıkça yok saydığımız için ayrıntılı olma gereksinimini de karşılamaktadır; 
hiçbir şeyi unutmadık.

Son olarak, oyunun kurallarını bir kez daha değiştireceğiz, böylece 3 veya 7'den başka bir şey atarsanız, 
başla bir şey ortaya çıkmaz. Bunu, `_` ile gelen kod olarak birim değeri kullanarak ifade edebiliriz:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-17-underscore-unit/src/main.rs:here}}
```

Burada, Rust'a açıkça daha önceki bir koldaki bir modelle eşleşmeyen başka bir değer kullanmayacağımızı ve 
bu durumda herhangi bir kod çalıştırmak istemediğimizi söylüyoruz.

Modeller ve eşleştirme hakkında [Bölüm
18][ch18-00-patterns]<!-- ignore -->'de ele alacağımız daha çok şey var. 
Şimdilik, `match` ifadesinin biraz kullanışsız olduğu durumlarda faydalı olabilecek `if let` söz dizimine geçeceğiz.

[tuples]: ch03-02-data-types.html#the-tuple-type
[ch18-00-patterns]: ch18-00-patterns.html
