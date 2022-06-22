## `Drop` Tanımı ile Temizleme Üzerinde Kod Çalıştırma

Akıllı işaretçi modeli için önemli olan ikinci tanım, bir değer kapsam dışına çıkmak üzereyken ne olacağını 
özelleştirmenize olanak tanıyan `Drop`'tur. Herhangi bir tür üzerinde `Drop` için bir sürekleme sağlayabilirsiniz 
ve bu kod, dosyalar veya ağ bağlantıları gibi kaynakları serbest bırakmak için kullanılabilir.
`Drop`'u akıllı işaretçiler bağlamında tanıtıyoruz çünkü `Drop`'un işlevselliği neredeyse her zaman bir akıllı 
işaretçi uygulanırken kullanılır. Örneğin, bir `Box<T>` bırakıldığında, yığın üzerinde kutunun işaret ettiği alan 
belleğe iade edilir.

Bazı dillerde, bazı türler için, programcı bu türlerin bir örneğini kullanmayı her bitirdiğinde belleği veya kaynakları 
boşaltmak için kod çağırmalıdır. Örnekler arasında dosya tutamaçları, soketler veya kilitler yer alır. Eğer 
unuturlarsa, sistem aşırı yüklenebilir ve çökebilir. Rust'ta, bir değer kapsam dışına çıktığında belirli bir 
kod parçasının çalıştırılmasını belirtebilirsiniz ve derleyici bu kodu otomatik olarak ekleyecektir. 
Sonuç olarak, belirli bir türün bir örneğinin bittiği bir programın her yerine temizleme kodu yerleştirme 
konusunda dikkatli olmanız gerekmez—yine de kaynak sızdırmazsınız!

`Drop` tanımını sürekleyerek bir değer kapsam dışına çıktığında çalıştırılacak kodu belirlersiniz.
`Drop`, `self` öğesine değiştirilebilir bir referans alan `drop` adlı bir metodu tanımlamanızı gerektirir. 
Rust'ın `drop`'u ne zaman çağırdığını görmek için şimdilik `drop`'u `println!` ifade yapılarıyla kullanalım.

Liste 15-14, Rust'ın `drop` fonksiyonunu ne zaman çalıştırdığını göstermek için, örnek kapsam dışına çıktığında
`Dropping CustomSmartPointer!` yazdıracak olan tek gizli fonksiyonu olan bir `CustomSmartPointer` yapısını gösterir.

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch15-smart-pointers/listing-15-14/src/main.rs}}
```

<span class="caption">Liste 15-14: Temizleme kodumuzu koyacağımız `Drop`'u sürekleyen bir `CustomSmartPointer` yapısı</span>

`Drop` tanımı genel Rust yapısına dahil edilmiştir, bu nedenle onu kapsam içine almamıza gerek yoktur.
`CustomSmartPointer` üzerinde `Drop`'u sürekliyoruz ve `println!` çağrısı yapan `drop` metodu için bir tanımlama sağlıyoruz.
`Drop` fonksiyonunun gövdesi, türünüzün bir örneği kapsam dışına çıktığında çalıştırmak istediğiniz herhangi bir mantığı yerleştireceğiniz yerdir. 
Rust'ın `drop`'u ne zaman çağıracağını görsel olarak göstermek için burada bazı metinler yazdırıyoruz.

`main`'de, iki `CustomSmartPointer` örneği oluşturuyoruz ve ardından oluşturulan `CustomSmartPointer`'ları yazdırıyoruz.
`main`'in sonunda, `CustomSmartPointer` örneklerimiz kapsam dışına çıkacak ve Rust, `drop`'a koyduğumuz kodu çağırarak son 
mesajımızı yazdıracak. `drop` metodunu açıkça çağırmamıza gerek olmadığına dikkat edin.

Bu programı çalıştırdığımızda aşağıdaki çıktıyı göreceğiz:

```console
{{#include ../listings/ch15-smart-pointers/listing-15-14/output.txt}}
```

Örneklerimiz kapsam dışına çıktığında Rust bizim için otomatik olarak `drop`'u çağırdı ve belirttiğimiz kodu çalıştırdı. 
Değişkenler oluşturulma sıralarının tersine bırakılır, bu nedenle `d`, `c`'den önce bırakılır. Bu örneğin amacı,
`drop` metodunun nasıl çalıştığına dair görsel bir kılavuz sunmaktır; genellikle bir yazdırma mesajı yerine türünüzün çalışması gereken 
temizleme kodunu belirtirsiniz.

### `std::mem::drop` ile Bir Değeri Erken Bırakma

Ne yazık ki, otomatik `drop` fonksiyonunu devre dışı bırakmak kolay değildir. `drop`'u devre dışı bırakmak genellikle gerekli değildir;
`Drop` tanımının tüm amacı bunun otomatik olarak halledilmesidir. Ancak bazen, bir değeri erkenden temizlemek isteyebilirsiniz. 
Buna bir örnek, kilitleri yöneten akıllı işaretçiler kullanırken verilebilir: kilidi serbest bırakan `drop` metodunu zorlamak isteyebilirsiniz, 
böylece aynı kapsamdaki diğer kodlar kilidi alabilir. Rust, `Drop`'un `drop` metodunu manuel olarak çağırmanıza izin vermez; 
bunun yerine, bir değeri kapsamının sonundan önce bırakılmaya zorlamak istiyorsanız standart kütüphane tarafından sağlanan
`std::mem::drop` işlevini çağırmanız gerekir.

Liste 15-15'te gösterildiği gibi, Liste 15-14'teki `main` fonksiyonunu değiştirerek `Drop`'un `drop` metodunu manuel olarak çağırmaya çalışırsak, 
bir derleyici hatası alırız:

<span class="filename">Dosya adı: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch15-smart-pointers/listing-15-15/src/main.rs:here}}
```

<span class="caption">Liste 15-15: Erken temizlemek için `Drop` tanımından `drop` metodunu manuel olarak çağırma girişimi</span>

Bu kodu derlemeye çalıştığımızda alacağımız hata budur:

```console
{{#include ../listings/ch15-smart-pointers/listing-15-15/output.txt}}
```

Bu hata mesajı, `drop`'u açıkça çağırmamıza izin verilmediğini belirtir. Hata mesajı, bir örneği temizleyen bir 
fonksiyon için genel programlama terimi olan *yıkıcı* terimini kullanır. Bir yıkıcı, bir örnek oluşturan bir yapıcıya benzer. 
Rust'taki `drop` fonksiyonu belirli bir yıkıcıdır.

Rust, `drop`'u açıkça çağırmamıza izin vermez, çünkü Rust yine de `main`'in sonundaki değer üzerinde otomatik olarak `drop`'u çağıracaktır. 
Bu, Rust aynı değeri iki kez temizlemeye çalışacağı için *çift serbest bırakma hatasına* neden olur.

Bir değer kapsam dışına çıktığında `drop`'un otomatik olarak eklenmesini devre dışı bırakamayız ve `drop` metodunu açıkça çağıramayız. 
Bu nedenle, bir değeri erken temizlenmeye zorlamamız gerekiyorsa, `std::mem::drop` fonksiyonunu kullanırız.

`std::mem::drop` fonksiyonu `Drop` tanımındaki `drop` metodundan farklıdır. Düşürmeye zorlamak istediğimiz değeri argüman olarak ileterek çağırırız. 
Fonksiyon genel Rust yapısındadır, bu nedenle Liste 15-15'teki `main`'i, Liste 15-16'da gösterildiği gibi `drop` fonksiyonunu çağıracak şekilde 
değiştirebiliriz:

<span class="filename">Dosya adı: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch15-smart-pointers/listing-15-16/src/main.rs:here}}
```

<span class="caption">Liste 15-16: Bir değeri kapsam dışına çıkmadan önce açıkça bırakmak için `std::mem::drop` çağrısı</span>

Bu kodu çalıştırmak aşağıdakileri yazdıracaktır:

```console
{{#include ../listings/ch15-smart-pointers/listing-15-16/output.txt}}
```

`CustomSmartPointer` oluşturulduğu esnada ```Dropping CustomSmartPointer with data (some data)!``` metni yazdırılır ve 
`CustomSmartPointer dropped before the end of main.` metni, `drop` metod kodunun bu noktada `c`'yi bırakmak için çağrıldığını gösterir.

`Drop` tanım süreklemesinde belirtilen kodu, temizlemeyi kolay ve güvenli hale getirmek için birçok şekilde kullanabilirsiniz: 
örneğin, kendi bellek ayırıcınızı oluşturmak için kullanabilirsiniz! `Drop` tanımı ve Rust'ın sahiplik sistemi sayesinde, 
temizlemeyi hatırlamak zorunda kalmazsınız çünkü Rust bunu otomatik olarak yapar.

Ayrıca, hala kullanımda olan değerlerin yanlışlıkla temizlenmesinden kaynaklanan sorunlar hakkında endişelenmenize de gerek yoktur: 
referansların her zaman geçerli olmasını sağlayan sahiplik sistemi, `drop`'un değer artık kullanılmadığında yalnızca bir kez çağrılmasını da sağlar.

`Box<T>`'yi ve akıllı işaretçilerin bazı özelliklerini incelediğimize göre, şimdi standart kütüphanede tanımlanan diğer birkaç akıllı 
işaretçiye bakalım.
