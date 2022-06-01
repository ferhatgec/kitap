## Dosya Okumak

Şimdi, `filename` argümanında belirtilen dosyayı okumak için işlevsellik ekleyeceğiz. 
İlk olarak, bunu test etmek için örnek bir dosyaya ihtiyacımız var: 
Birkaç satırda metin içeren ve bazı tekrarlanan kelimeler içeren bir dosya kullanacağız. 
Liste 12-3'te işe yarayacak bir Emily Dickinson şiiri var! Projenizin kök dizininde *poem.txt* adlı bir dosya oluşturun 
ve “Ben Hiçkimse'yim! Sen kimsin?"

<span class="filename">Dosya adı: poem.txt</span>

```text
{{#include ../listings/ch12-an-io-project/listing-12-03/poem.txt}}
```

<span class="caption">Liste 12-3: Emily Dickinson'ın bir şiiri iyi bir test örneği yapıyor</span>

Metin yerindeyken, *src/main.rs* dosyasını düzenleyin ve okunacak dosya için kod ekleyin.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,should_panic,noplayground
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-04/src/main.rs:here}}
```

<span class="caption">Liste 12-4: İkinci argüman tarafından belirtilen dosyanın içeriğini okuma</span>

İlk olarak, standart kitaplığın ilgili bir bölümünü bir `use` ifadesi ile getiriyoruz: dosyaları işlemek için `std::fs`'ye ihtiyacımız var.

`main`'de, `fs::read_to_string` ifadesi `filename` argümanını alır, dosyayı açar, ve dosyanın içeriğini tutan
`Result<String>`'i döndürür.

Bundan sonra, geçici olarak bir `println!` ifadesi koyacağız ki dosya okunduktan sonra içindekileri okuyabilelim.

Hadi şimdi bu kodu herhangi bir ilk komut satırı argümanıyla  (çünkü henüz
dosya arama kısmını süreklemedik) ve *poem.txt* dosyasını ikinci bir argüman olarak
kullanarak çalıştıralım. 

```console
{{#rustdoc_include ../listings/ch12-an-io-project/listing-12-04/output.txt}}
```

Güzel! Bu kod dosyanın içeriğini okur ve sonra içeriğini yazar. Ama kod bazı sorunlara sahip.
`main` fonksiyonunun birden fazla işlevi var. Genel olarak tek bir fikre dayalı fonksiyonlar daha kolay
karşılanır ve sürdürülür. Bir diğer problem olaraktan, biz henüz herhangi bir hatayı işlemiyoruz.
Program küçük yani bu sıkıntılar büyük bir program değil ama program büyüdükçe bu tarz sıkıntıları temizce
çözmek zorlaşacaktır. Bu tarz sıkıntıları kodunuz büyümeden çözmek iyi bir pratik olacaktır. Bunu sonra yapacağız.
