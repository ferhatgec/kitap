# Yaygın Koleksiyonlar

Rust'ın standart kütüphanesi, *koleksiyon* adı verilen bir dizi çok kullanışlı veri yapısını içerir. 
Diğer veri türlerinin çoğu belirli bir değeri temsil eder, ancak koleksiyonlar birden çok değer içerebilir. 
Yerleşik dizi ve tanımlama grubu türlerinin aksine, bu koleksiyonların işaret ettiği veriler öbek üzerinde depolanır; bu, veri miktarının derleme zamanında bilinmesine gerek olmadığı ve program çalışırken büyüyüp küçülebileceği anlamına gelir. 
Her toplama türünün farklı yetenekleri ve maliyetleri vardır ve mevcut durumunuza uygun olanı seçmek zamanla geliştireceğiniz bir beceridir. Bu bölümde, Rust programlarında çok sık kullanılan üç koleksiyonu tartışacağız:

* Bir *vektör* değişken sayıda aynı tür değerleri yan yana saklamanıza izin verir.
* Bir *dizgi* karakter *koleksiyonudur*. `String` türünden önceden de bahsetmiştik ama artık daha derine ineceğiz.
* Bir *kilit koleksiyonu* bir değeri belirli bir anahtarla ilişkilendirmenizi sağlar. *map* diye tanımlanan veri yapısının
  daha özel bir süreklemesidir (uygulamasıdır).

Standart kütüphane tarafından sunulan diğer tür koleksiyonlar hakkında bilgi almak için [dokümantasyona][collections]
göz atabilirsiniz.

Vektörlerin, dizgilerin ve kilit koleksiyonlarının nasıl oluşturulacağını ve 
güncelleneceğini ve ayrıca her birini neyin özel kıldığını tartışacağız.

[collections]: ../std/collections/index.html
