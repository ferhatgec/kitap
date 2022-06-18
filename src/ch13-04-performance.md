## Performans Karşılaştırması: Döngüler ve Yineleyiciler

Döngülerin mi yoksa yineleyicilerin mi kullanılacağını belirlemek için, hangi uygulamanın daha hızlı olduğunu bilmeniz gerekir: 
`search` fonksiyonunun açık bir `for` döngüsüne sahip versiyonu ya da yineleyicilere sahip versiyonu.

Sir Arthur Conan Doyle'un *The Adventures of Sherlock Holmes*'un tüm içeriğini bir `String`'e yükleyerek ve içerikte 
kelimeyi arayarak bir kıyaslama yaptık. Burada, `for` döngüsünü kullanan arama sürümü ve yineleyicileri kullanan sürümle 
ilgili karşılaştırmanın sonuçları:

```text
test bench_search_for  ... bench:  19,620,300 ns/iter (+/- 915,700)
test bench_search_iter ... bench:  19,234,900 ns/iter (+/- 657,200)
```

Yineleyici sürümü biraz daha hızlıydı! Burada kıyaslama kodunu açıklamayacağız, 
çünkü mesele iki versiyonun eşdeğer olduğunu kanıtlamak değil, bu iki uygulamanın performans açısından nasıl karşılaştırıldığına 
dair genel bir fikir edinmek.

Daha kapsamlı bir kıyaslama için, içerik olarak çeşitli boyutlarda çeşitli metinler, sorgu olarak farklı kelimeler ve 
farklı uzunluklarda kelimeler ve her türlü diğer varyasyonları kontrol etmelisiniz. 

Mesele şudur: yineleyiciler, üst düzey bir soyutlama olmasına rağmen, alt düzey kodu kendiniz yazmışsınız gibi kabaca aynı koda derlenir. 
Yineleyiciler, Rust'ın *sıfır maliyetli soyutlamalarından* biridir; bununla, soyutlamayı kullanmanın ek çalışma zamanı yükü getirmediğini
kastediyoruz. Bu, C++'ın orijinal tasarımcısı ve uygulayıcısı olan Bjarne Stroustrup'un “Foundations of C++”'ta (2012) 
sıfır yükü tanımlamasına benzer:

> Genel olarak, C++ uygulamaları sıfır genel gider ilkesine uyar: 
> Kullanmadığınız şey için ödeme yapmazsınız. 
> Ve dahası: Ne kullanırsanız kullanın, kodu daha iyi veremezdiniz.

Başka bir örnek olarak, aşağıdaki kod bir ses kod çözücüsünden alınmıştır. 
Kod çözme algoritması, önceki örneklerin doğrusal bir fonksiyonuna dayalı olarak gelecekteki değerleri tahmin 
etmek için doğrusal tahmin matematiksel işlemini kullanır. Bu kod, kapsamdaki üç değişken üzerinde biraz matematik yapmak 
için bir yineleyici zinciri kullanır: bir arabellek veri dilimi, 12 katsayı dizisi ve `qlp_shift`'te verilerin kaydırılacağı miktar. 
Bu örnekte değişkenleri tanımladık ancak onlara herhangi bir değer vermedik; Bu kodun bağlamı dışında pek bir anlamı olmamasına rağmen, 
yine de Rust'ın yüksek seviyeli fikirleri düşük seviyeli koda nasıl çevirdiğinin kısa ve gerçek bir örneğidir.


```rust,ignore
let buffer: &mut [i32];
let coefficients: [i64; 12];
let qlp_shift: i16;

for i in 12..buffer.len() {
    let prediction = coefficients.iter()
                                 .zip(&buffer[i - 12..i])
                                 .map(|(&c, &s)| c * s as i64)
                                 .sum::<i64>() >> qlp_shift;
    let delta = buffer[i];
    buffer[i] = prediction as i32 + delta;
}
```

`prediction` değerini hesaplamak için bu kod, katsayılardaki 12 değerin her birini yineler ve katsayı değerlerini arabellekteki önceki 
12 değerle eşleştirmek için `zip` metodunu kullanır. Ardından, her bir çift için değerleri birlikte çarparız, 
tüm sonuçları toplarız ve toplam `qlp_shift` bitlerindeki bitleri sağa kaydırırız.

Ses kod çözücüler gibi uygulamalardaki hesaplamalar genellikle performansa en yüksek önceliği verir. 
Burada, iki bağdaştırıcı kullanarak bir yineleyici oluşturuyoruz ve ardından değeri kullanıyoruz. 
Bu Rust kodu hangi Assembly koduna derler? Eh, bu yazı itibariyle, elle yazacağınız derlemeye kadar derlenir. 
Katsayılardaki değerler üzerinde yinelemeye karşılık gelen hiçbir döngü yoktur: Rust, 12 yineleme olduğunu bilir, 
bu nedenle döngüyü “açar”. *Açmak*, döngü kontrol kodunun ek yükünü ortadan kaldıran ve bunun yerine döngünün her 
yinelemesi için tekrarlayan kod üreten bir optimizasyondur.

Tüm katsayılar kayıtlarda saklanır, bu da değerlere erişmenin çok hızlı olduğu anlamına gelir. 
Çalışma zamanında dizi erişiminde sınır denetimi yoktur. Rust'ın uygulayabildiği tüm bu optimizasyonlar, 
ortaya çıkan kodu son derece verimli hale getirir. Artık bunu bildiğinize göre, yineleyicileri ve kapanış ifadelerini 
korkmadan kullanabilirsiniz! Kodun daha yüksek bir seviye gibi görünmesini sağlarlar, 
ancak bunu yapmak için bir çalışma zamanı performans *kapitülasyonlarını* vermezler.

## Özet

Kapanış ifadeleri ve yineleyiciler, fonksiyonel programlama dili fikirlerinden ilham alan Rust özellikleridir. 
Rust'ın üst düzey fikirleri düşük düzeyde performansla açıkça ifade etme yeteneğine katkıda bulunurlar. 
Kapanış ifadelerinin ve yineleyicilerin uygulamaları, çalışma zamanı performansının etkilenmeyeceği şekildedir. 
Bu, Rust'ın *sıfır maliyetli soyutlamalar* sağlama hedefinin bir parçasıdır.

G/Ç projemizin dışavurumunu iyileştirdiğimize göre, şimdi projeyi dünyayla paylaşmamıza yardımcı 
olacak `cargo`'nun bazı özelliklerine bakalım.
