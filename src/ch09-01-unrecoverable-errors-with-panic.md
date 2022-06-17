## `panic!` İle Kurtarılamayan Hatalar

Bazen kodunuzda kötü şeyler olur ve bu konuda yapabileceğiniz hiçbir şey yoktur. 
Bu durumlarda Rust'ta `panic!` makrosu kullanılır. Pratikte paniğe yol açmanın iki yolu vardır: 
kodumuzun paniklemesine neden olan bir eylemde bulunmak (sondan sonraki bir diziye erişmek gibi) veya açıkça `panic!` makrosunu
çağırmaktır. 
Her iki durumda da programımızda paniğe neden oluyoruz. Varsayılan olarak, bu panikler bir hata mesajı yazdırır, 
yığıtı temizler ve çıkar. Bir ortam değişkeni aracılığıyla, panik meydana geldiğinde panik kaynağını bulmayı kolaylaştırmak için 
Rust'ın çağrı yığınını görüntülemesini de sağlayabilirsiniz.

> ### Paniğe Tepki Olarak Yığıtı Çözme veya Durdurma
> 
> Varsayılan olarak, bir panik meydana geldiğinde program çözülmeye başlar, bu da Rust'ın yığıtı geri aldığı ve karşılaştığı 
> her fonksiyondan gelen verileri temizlediği anlamına gelir. Ancak, bu geri dönüş ve temizlik çok iştir. 
> Bu nedenle Rust, programı temizlemeden sonlandıran hemen iptal etme alternatifini seçmenize izin verir.
> 
> Programın kullandığı belleğin işletim sistemi tarafından temizlenmesi gerekecektir. Projenizde elde edilen ikili dosyayı mümkün 
> olduğu kadar küçük yapmanız gerekiyorsa, *Cargo.toml* dosyanızdaki uygun `[profile]` bölümlerine `panic = 'abort'` ekleyerek gevşemeden 
> panik durumunda iptal etmeye geçebilirsiniz. Örneğin, yayın modund paniği iptal etmek istiyorsanız şunu ekleyin:
> 
> ```toml
> [profile.release]
> panic = 'abort'
> ```

Basit bir programda `panic!`'i çağırmaya çalışalım:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,should_panic,panics
{{#rustdoc_include ../listings/ch09-error-handling/no-listing-01-panic/src/main.rs}}
```

Programı çalıştırdığınızda, şöyle bir şey göreceksiniz:

```console
{{#include ../listings/ch09-error-handling/no-listing-01-panic/output.txt}}
```

`panic!` çağrısı, son iki satırda bulunan hata mesajına neden olur. İlk satır panik mesajımızı ve kaynak kodumuzda paniğin 
meydana geldiği yeri gösterir: src/main.rs:2:5 src/main.rs dosyamızın ikinci satırı, beşinci karakteri olduğunu gösterir.

Bu durumda belirtilen satır kodumuzun bir parçasıdır ve o satıra gidersek `panic!` makro çağrısını görürüz. 
Diğer durumlarda, `panic!` çağrısı, kodumuzun çağırdığı kodda olabilir ve hata mesajı tarafından bildirilen dosya adı ve 
satır numarası, `panic!` makrosunun çağrıldığı yeri gösterecektir. 
Kodumuzun soruna neden olan kısmını bulmak için `panic!` çağrısının geldiği fonksiyonların geri izini kullanabiliriz. 
Geri izlemeleri ileride daha ayrıntılı olarak tartışacağız.

### `panic!` Geri İzlemesini Kullanma

Kodumuzun doğrudan makroyu çağırması yerine kodumuzdaki bir hata nedeniyle bir kütüphaneden bir `panic!` çağrısı geldiğinde 
nasıl bir şey olduğunu görmek için başka bir örneğe bakalım. Liste 9-1, geçerli dizin aralığının ötesinde bir vektördeki bir 
dizine erişmeye çalışan bazı kodlara sahiptir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust,should_panic,panics
{{#rustdoc_include ../listings/ch09-error-handling/listing-09-01/src/main.rs}}
```

<span class="caption">Liste 9-1: Bir vektörün büyüklüğünden fazla bir indekse erişmeye çalışmak, `panic!` 
çağrısına neden olacaktır.</span>

Burada, vektörümüzün 100. elemanına erişmeye çalışıyoruz (bu, indeksleme sıfırdan başladığı için indeks 99'dadır), 
ancak vektörün sadece 3 elemanı vardır. Bu durumda Rust *paniğe kapılır*. `[]` öğesinin kullanılması bir öğe döndürmesi gerekir, 
ancak geçersiz bir dizini iletirseniz, Rust'ın buraya döndürebileceği hiçbir öğe yoktur, bu da olması gerekendir.

C'de, bir veri yapısının sonunun ötesini okumaya çalışmak tanımsız davranıştır. Bellek o yapıya ait olmasa bile, 
veri yapısındaki o öğeye karşılık gelen bellekteki konumda ne varsa alabilirsiniz. Buna arabellek aşırı okuma denir 
ve bir saldırgan dizini, veri yapısından sonra depolanmasına izin verilmemesi gereken verileri okuyacak şekilde değiştirebiliyorsa, g
üvenlik açıklarına yol açabilir.

Programınızı bu tür bir güvenlik açığından korumak için, var olmayan bir dizindeki bir öğeyi okumaya çalışırsanız, 
Rust yürütmeyi durdurur ve devam etmeyi reddeder. Deneyelim ve görelim:


```console
{{#include ../listings/ch09-error-handling/listing-09-01/output.txt}}
```

Bu hata, dizin 99'a erişmeye çalıştığımız `main.rs`'in 4. satırına işaret ediyor. 
Sonraki not satırı bize, `RUST_BACKTRACE` ortam değişkenini, hataya neden olan şeyin tam olarak geri izini almak için ayarlayabileceğimizi 
söylüyor. Geri izleme, bu noktaya gelmek için çağrılan tüm fonksiyonların bir listesidir. Rust'ta geriye dönük izlemeler, 
diğer dillerde olduğu gibi çalışır: Geri izlemeyi okumanın anahtarı, en baştan başlamak ve yazdığınız dosyaları görene kadar okumaktır. 
Sorunun ortaya çıktığı yer orasıdır. Bu noktanın üzerindeki satırlar, kodunuzun çağırdığı koddur; Aşağıdaki satırlar, kodunuzu çağıran koddur. 
Bu öncesi ve sonrası satırları, temel Rust kodunu, standart kütüphane kodunu veya kullandığınız kasaları içerebilir. 
`RUST_BACKTRACE` ortam değişkenini 0 dışında herhangi bir değere ayarlayarak geri izlemeye almayı deneyelim. 
Liste 9-2, göreceğinize benzer bir çıktı gösteriyor.

<!-- manual-regeneration
cd listings/ch09-error-handling/listing-09-01
RUST_BACKTRACE=1 cargo run
copy the backtrace output below
check the backtrace number mentioned in the text below the listing
-->

```console
$ RUST_BACKTRACE=1 cargo run
thread 'main' panicked at 'index out of bounds: the len is 3 but the index is 99', src/main.rs:4:5
stack backtrace:
   0: rust_begin_unwind
             at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/std/src/panicking.rs:483
   1: core::panicking::panic_fmt
             at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/core/src/panicking.rs:85
   2: core::panicking::panic_bounds_check
             at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/core/src/panicking.rs:62
   3: <usize as core::slice::index::SliceIndex<[T]>>::index
             at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/core/src/slice/index.rs:255
   4: core::slice::index::<impl core::ops::index::Index<I> for [T]>::index
             at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/core/src/slice/index.rs:15
   5: <alloc::vec::Vec<T> as core::ops::index::Index<I>>::index
             at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/alloc/src/vec.rs:1982
   6: panic::main
             at ./src/main.rs:4
   7: core::ops::function::FnOnce::call_once
             at /rustc/7eac88abb2e57e752f3302f02be5f3ce3d7adfb4/library/core/src/ops/function.rs:227
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
```

<span class="caption">Liste 9-2: `RUST_BACKTRACE` ortam değişkeni ayarlandığında görüntülenen 
`panic!` çağrısı tarafından oluşturulan geri izleme</span>

Çok fazla çıktı var! Gördüğünüz tam çıktı, işletim sisteminize ve Rust sürümünüze bağlı olarak farklı olabilir. 
Bu bilgilerle geriye dönük izler almak için hata ayıklama sembollerinin etkinleştirilmesi gerekir. 
Hata ayıklama sembolleri, burada olduğu gibi `--release` bayrağı olmadan `cargo build` veya `cargo run` kullanılırken varsayılan 
olarak etkindir.

Liste 9-2'deki çıktıda, geri izlemenin 6. satırı, projemizde soruna neden olan satırı işaret ediyor: *src/main.rs* dosyasının 4. satırı. 
Eğer programımızın paniğe kapılmasını istemiyorsak, ilk satırın gösterdiği ve yazdığımız bir dosyadan bahseden yerden araştırmamıza başlamalıyız.
Kasten panik yaratacak kod yazdığımız Liste 9-1'de, paniği düzeltmenin yolu, vektör indekslerinin aralığının ötesinde bir öğe talep etmemektir. 
Kodunuz paniklediğinde, paniğe neden olmak için kodun hangi değerlerle hangi eylemi gerçekleştirdiğini ve bunun yerine kodun ne 
yapması gerektiğini bulmanız gerekir.

`panic!`'e geri döneceğiz ve hataları yönetmek için `panic!`'i ne zaman kullanıp kullanmamamız gerektiğini 
[“`panic!`'lemek ya da `panic!`'lememek”][to-panic-or-not-to-panic]<!-- ignore --> bölümünde daha sonra anlatacağız.
Sonraki bölümde, `Result` tanımını kullanarak nasıl hatadan dönülebileceğine bakacağız.

[to-panic-or-not-to-panic]:
ch09-03-to-panic-or-not-to-panic.html#to-panic-or-not-to-panic
