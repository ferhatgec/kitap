## Yorum Satırları

Tüm programcılar, kodlarının anlaşılmasını kolaylaştırmak için çaba gösterir,
ancak bazen ek açıklamalar gerekir. Bu durumlarda, programcılar kaynak kodlarında derleyicinin görmezden geleceği yorum satırları bırakırlar. 
Bunları kaynak kodu okuyan insanlar faydalı bulabilir. 

İşte basit bir yorum satırı:

```rust
// Merhaba, Dünya!
```

Rust'ta, deyimsel yorum stili bir yoruma iki eğik çizgi ile başlar ve yorum satırın sonuna kadar devam eder. 
Tek bir satırın ötesine geçen yorumlar için, her satıra `//` eklemeniz gerekir, örneğin:


```rust
// Yani burada karmaşık şeyler yapıyoruz sanki, bu kadar uzun
// ve satırlarca süren bir yorum satırı ancak karışık
// fonksiyonlar içindir diye düşünmeliyim gibime geliyor
```

Yorum satırları ayrıca kodunuzun sonuna da konabilir:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-24-comments-end-of-line/src/main.rs}}
```

Ancak, bunların şu biçimde kullanıldığını daha sık göreceksiniz:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-25-comments-above-line/src/main.rs}}
```

Rust'ın ayrıca, Bölüm 14'ün "Bir Kasayı Crates.io'da Yayınlamak" bölümünde tartışacağımız belgeleme 
yorumları gibi başka bir yorum satırı çeşidi daha vardır.
