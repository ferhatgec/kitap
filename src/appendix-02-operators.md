## Ekleme B: Operatörler ve Semboller

Bu ekleme, Rust'ın yaygınlar, tanım sınırları, makrolar, nitelikler, yorum satırları, demetler ve parantezler 
bağlamında beliren operatörlerini ve diğer sembollerini dahil eden bir sözlüğü içerir.

### Operatörler

Tablo B-1 Rust'taki operatörleri, kodda nasıl belireceğini, kısa özetini, 
nasıl aşırı yüklenilebileceğini gösterir.

<span class="caption">Tablo B-1: Operatörler</span>

| Operatör | Örnek | Açıklama | Aşırı Yüklenilebilir mi? |
|----------|---------|-------------|---------------|
| `!` | `ident!(...)`, `ident!{...}`, `ident![...]` | Makro genişlemesi | |
| `!` | `!expr` | Bitsel veya mantıksal tamamlayıcı | `Not` |
| `!=` | `var != expr` | Eşitsizlik karşılaştırıcı | `PartialEq` |
| `%` | `expr % expr` | Modu | `Rem` |
| `%=` | `var %= expr` | Modu ve eşitlemesi | `RemAssign` |
| `&` | `&expr`, `&mut expr` | Referans | |
| `&` | `&type`, `&mut type`, `&'a type`, `&'a mut type` | Referans işaretçi türü | |
| `&` | `expr & expr` | Bitsel VE | `BitAnd` |
| `&=` | `var &= expr` | Bitsel VE ve eşitlemesi | `BitAndAssign` |
| `&&` | `expr && expr` | Kısa devre mantıksal VE | |
| `*` | `expr * expr` | Aritmetik çarpma | `Mul` |
| `*=` | `var *= expr` | Aritmetik çarpma ve eşitlemesi | `MulAssign` |
| `*` | `*expr` | Referansı kaldırma | `Deref` |
| `*` | `*const type`, `*mut type` | Ham işaretçi | |
| `+` | `trait + trait`, `'a + trait` | Bileşik tür kısıtlaması | |
| `+` | `expr + expr` | Aritmetik toplama | `Add` |
| `+=` | `var += expr` | Aritmetik toplama ve eşitlemesi | `AddAssign` |
| `,` | `expr, expr` | Argüman ve öğe ayırıcı | |
| `-` | `- expr` | Aritmetik olumsuzlama | `Neg` |
| `-` | `expr - expr` | Aritmetik çıkarma | `Sub` |
| `-=` | `var -= expr` | Aritmetik çıkarma ve eşitlemesi | `SubAssign` |
| `->` | `fn(...) -> type`, <code>&vert;...&vert; -> type</code> | Fonksiyon ve dönüş tipi | |
| `.` | `expr.ident` | Üye erişimi | |
| `..` | `..`, `expr..`, `..expr`, `expr..expr` | Sağa özgü aralık değişmezi | `PartialOrd` |
| `..=` | `..=expr`, `expr..=expr` | Sağ dahil aralık değişmezi | `PartialOrd` |
| `..` | `..expr` | Yapı değişmezini güncelleme söz dizimi | |
| `..` | `variant(x, ..)`, `struct_type { x, .. }` | Model atama | |
| `...` | `expr...expr` | (Kaldırıldı, yerine `..=` kullanın) Bir modelde: kapsayıcı aralık modeli | |
| `/` | `expr / expr` | Aritmetik bölme | `Div` |
| `/=` | `var /= expr` | Aritmetik bölme ve eşitleme | `DivAssign` |
| `:` | `pat: type`, `ident: type` | Kısıtlama | |
| `:` | `ident: expr` | Yapı alanı oluşturucusu | |
| `:` | `'a: loop {...}` | Döngü adı | |
| `;` | `expr;` | Koşul ve öğe sonlandırıcı | |
| `;` | `[...; len]` | Sabit boyutlu dizi söz diziminin bir parçası | |
| `<<` | `expr << expr` | Sola kaydırma | `Shl` |
| `<<=` | `var <<= expr` | Sola kaydırma ve eşitleme | `ShlAssign` |
| `<` | `expr < expr` | Daha az karşılaştırması | `PartialOrd` |
| `<=` | `expr <= expr` | Daha az ya da eşit karşılaştırması | `PartialOrd` |
| `=` | `var = expr`, `ident = type` | Atama/eşitlik | |
| `==` | `expr == expr` | Eşitlik karşılaştırması | `PartialEq` |
| `=>` | `pat => expr` | match söz diziminin bir parçası | |
| `>` | `expr > expr` | Daha fazla karşılaştırması | `PartialOrd` |
| `>=` | `expr >= expr` | Daha fazla ya da eşit karşılaştırması | `PartialOrd` |
| `>>` | `expr >> expr` | Sağa kaydırma | `Shr` |
| `>>=` | `var >>= expr` | Sağa kaydırma ve eşitleme | `ShrAssign` |
| `@` | `ident @ pat` | Model atama | |
| `^` | `expr ^ expr` | Bitsel Dışlayıcı VEYA işlevi | `BitXor` |
| `^=` | `var ^= expr` | Bitsel Dışlayıcı VEYA işlevi ve eşitleme | `BitXorAssign` |
| <code>&vert;</code> | <code>pat &vert; pat</code> | Model alternatifleri | |
| <code>&vert;</code> | <code>expr &vert; expr</code> | Bitsel YA DA | `BitOr` |
| <code>&vert;=</code> | <code>var &vert;= expr</code> | Bitsel YA DA ve eşitleme | `BitOrAssign` |
| <code>&vert;&vert;</code> | <code>expr &vert;&vert; expr</code> | Kısa devre yapan mantıksal YA DA | |
| `?` | `expr?` | Hata yayılımı | |

### Operatör dışı Semboller

Aşağıdaki liste, operatör işlevi görmeyen tüm harfleri içerir. Yani, bir işlev veya yöntem çağrısı gibi davranmazlar

Tablo B-2, kendi başlarına görünen ve çeşitli şekillerde geçerli olan sembolleri göstermektedir.

<span class="caption">Tablo B-2: Kendi Başına Söz Dizimi</span>

| Sembol | Açıklama |
|--------|-------------|
| `'ident` | Adlandırılmış ömürlük ya da döngü adı |
| `...u8`, `...i32`, `...f64`, `...usize`, vs. | Belirli bir türün sayısal değişmezi |
| `"..."` | Dizgi değişmezi |
| `r"..."`, `r#"..."#`, `r##"..."##`, vs. | Ham dizgi değişmezi, kaçış karakterleri işlenmez |
| `b"..."` | Bitsel dizgi değişmezi; dizgi yerine bitlerden oluşan dizi oluşturur |
| `br"..."`, `br#"..."#`, `br##"..."##`, vs. | Ham bitsel dizgi değişmezi, ham ve bitsel dizgi değişmezi kombinasyonu |
| `'...'` | Karakter değişmezi |
| `b'...'` | ASCII bit değişmezi |
| <code>&vert;...&vert; expr</code> | Kapanış ifadeleri |
| `!` | Farklı fonksiyonlar için her zaman boş alt tür |
| `_` | “Ignored” model atama, ayrıca tam sayı değişmezlerini okunuşlu kılmak için de kullanılır |

Tablo B-3 modül hiyerarşisinden bir öğeye giden yol bağlamında görünen tüm sembolleri gösterir.

<span class="caption">Tablo B-3: Yol Bağlamlı Söz Dizimi</span>

| Sembol | Açıklama |
|--------|-------------|
| `ident::ident` | Ad alanı yolu |
| `::path` | Sandığın köküne giden yol |
| `self::path` | Halihazırdaki modüle giden yol |
| `super::path` | Ana modüle giden yol |
| `type::ident`, `<type as trait>::ident` | İlişkili sabitler, fonksiyonlar ve türler |
| `<type>::...` | Doğrudan adlandırılamayan bir tür için ilişkili bir öğe (örneğin, `<&T>::...`, `<[T]>::...` vs.) |
| `trait::method(...)` | Bir metod çağrısını tanımlayan özelliği adlandırarak belirsizliği giderme |
| `type::method(...)` | Tanımlandığı türü adlandırarak bir metod çağrısının belirsizliğini giderme |
| `<type as trait>::method(...)` | Tanımını ve türünü adlandırarak bir metod çağrısının belirsizliğini giderme |

Tablo B-4 yaygın türü kullanma bağlamında görünen sembolleri gösterir

<span class="caption">Tablo B-4: Yaygınlar</span>

| Sembol | Açıklama |
|--------|-------------|
| `path<...>` | Bir türdeki yaygın tür için parametreleri belirtir (örneğin, `Vec<u8>`) |
| `path::<...>`, `method::<...>` | Bir ifadede yaygın türe, fonksiyona veya metoda ilişkin parametreleri belirtir; genellikle turbofish olarak anılır (örneğin, `"42".parse::<i32>()`) |
| `fn ident<...> ...` | Yaygın fonksiyon tanımlar |
| `struct ident<...> ...` | Yaygın yapı tanımlar |
| `enum ident<...> ...` | Yaygın numaralandırılmış yapı tanımlar |
| `impl<...> ...` | Yaygın süreklemesini tanımlar |
| `for<...> type` | Daha yüksek dereceli şekilde ömürlük sınırlar |
| `type<ident=type>` | Bir veya daha fazla ilişkili türün belirli atamalara sahip olduğu yaygın bir tür (örneğin, `Iterator<Item=T>`) |

Tablo B-5 tanım sınırlamalarıyla yaygın tür parametrelerinin kısıtlanması bağlamında görünen sembolleri gösterir.

<span class="caption">Tablo B-5: Tanıma Bağlı Kısıtlamalar</span>

| Sembol | Açıklama |
|--------|-------------|
| `T: U` | `T` yaygın parametresini `U`'yu uygulayan türlerle sınırlar |
| `T: 'a` | `T` yaygın parametresi, `a`'nın ömründen daha uzun ömürlü olmalıdır |
| `T: 'static` | `T` yaygın parametresi, `static` olanlar dışında referansı alınmış başka bir referansı içeremez |
| `'b: 'a` | `'b` yaygın parametresi, `'a`'nın ömründen daha uzun ömürlü olmalıdır |
| `T: ?Sized` | Yaygın tür parametresinin dinamik olarak boyutlandırılmış bir tür olmasına izin ver |
| `'a + trait`, `trait + trait` | Bileşik tür kısıtlaması |

Tablo B-6 makroları çağırma veya tanımlama ve bir öğedeki nitelikleri belirleme 
bağlamında görünen sembolleri gösterir.

<span class="caption">Tablo B-6: Makrolar ve Özellikler</span>

| Sembol | Açıklama |
|--------|-------------|
| `#[meta]` | Dış özellik |
| `#![meta]` | İç özellik |
| `$ident` | Makro belirteci |
| `$ident:kind` | Makro yakalama |
| `$(…)…` | Makro yineleme |
| `ident!(...)`, `ident!{...}`, `ident![...]` | Makro çağırma |

Tablo B-7 yorum satırı olarak yorumlanan sembolleri gösterir.

<span class="caption">Tablo B-7: Yorum Satırları</span>

| Sembol | Açıklama |
|--------|-------------|
| `//` | Yorum satırı |
| `//!` | İç doküman yorum satırı |
| `///` | Dış doküman yorum satırı |
| `/*...*/` | Yorum bloğu |
| `/*!...*/` | İç doküman yorum bloğu |
| `/**...*/` | Dış doküman yorum bloğu |

Tablo B-8 demet yapısı kullanımı bağlamında görünen sembolleri gösterir.

<span class="caption">Tablo B-8: Demet Yapısı</span>

| Bağlam | Açıklama |
|--------|-------------|
| `()` | Boş demet, hem değişmez hem tür |
| `(expr)` | Parantezli ifade |
| `(expr,)` | Tek elemanlı demet ifadesi |
| `(type,)` | Tek elemanlı demet türü |
| `(expr, ...)` | Demet ifadesi |
| `(type, ...)` | Demet türü |
| `expr(expr, ...)` | Fonksiyon çağrı ifadesi; ayrıca `struct` ve `enum` varyantlarını çağırmak için kullanılır |
| `expr.0`, `expr.1`, etc. | Demet elemanı göstergeci |

Tablo B-9 süslü parantezlerin kullanıldığı bağlamları gösterir.

<span class="caption">Tablo B-9: Süslü Parantezler</span>

| Bağlam | Açıklama |
|---------|-------------|
| `{...}` | Blok ifade |
| `Type {...}` | `struct` değişmezi |

Tablo B-10 köşeli parantezlerin kullanıldığı bağlamları gösterir.

<span class="caption">Tablo B-10: Köşeli Parantezler</span>

| Bağlam | Açıklama |
|---------|-------------|
| `[...]` | Dize değişmezi |
| `[expr; len]` | `expr`'i `len` kadar kopyalayan dize değişmezi |
| `[type; len]` | `type`'ı `len` kadar tutan dize türü ifadesi |
| `expr[expr]` | Koleksiyon elemanı göstergeci. (`Index`, `IndexMut`) aşırı yüklenebilir |
| `expr[..]`, `expr[a..]`, `expr[..b]`, `expr[a..b]` | `Range`, `RangeFrom`, `RangeTo` ya da `RangeFull` kullanarak koleksiyon dilimleyici gibi davranan koleksiyon elemanı göstergeci |
