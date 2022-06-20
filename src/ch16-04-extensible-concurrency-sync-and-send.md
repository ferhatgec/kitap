## `Sync` ve `Send` Tanımlarıyla Genişletilebilir Eşzamanlılık

İlginç bir şekilde, Rust dili çok az eşzamanlılık özelliğine sahiptir. Bu bölümde şimdiye kadar bahsettiğimiz neredeyse 
tüm eşzamanlılık özellikleri dilin değil, standart kütüphanenin bir parçasıydı. Eşzamanlılığı ele almak için seçenekleriniz 
dil veya standart kütüphane ile sınırlı değildir; kendi eşzamanlılık özelliklerinizi yazabilir veya başkaları tarafından 
yazılanları kullanabilirsiniz.

Ancak, iki eşzamanlılık kavramı dilin içine yerleştirilmiştir: `std::marker` tanımları `Sync` ve `Send`.

### `Send` ile İş Parçacıkları Arasında Sahiplik Aktarımına İzin Verme

`Send` işaretleyici tanımı, `Send`'i sürekleyen türdeki değerlerin sahipliğinin iş parçacıkları arasında aktarılabileceğini gösterir. 
Hemen hemen her Rust türü `Send`'dir, ancak `Rc<T>` gibi bazı istisnalar vardır: bu tür `Send` olamaz, çünkü bir `Rc<T>` değerini klonlarsanız 
ve klonun sahipliğini başka bir iş parçacığına aktarmaya çalışırsanız, her iki iş parçacığı da referans sayısını aynı anda güncelleyebilir. 
Bu nedenle `Rc<T>`, iş parçacığı güvenli performans cezasını ödemek istemediğiniz tek iş parçacıklı durumlarda kullanılmak üzere uygulanmıştır.

Bu nedenle, Rust'ın tür sistemi ve tanım bağlılığı, bir `Rc<T>` değerini asla yanlışlıkla iş parçacıkları arasında güvenli olmayan bir şekilde
gönderemeyeceğinizi garanti eder. Bunu Liste 16-14'te yapmaya çalıştığımızda, `Send` tanımının `Rc<Mutex<i32>>` için süreklenmediği hatasını almıştık.
`Send`'i süreklemiş `Arc<T>`'ye geçtiğimizde kod derlendi.

Tamamen `Send` türlerinden oluşan herhangi bir tür de otomatik olarak `Send` olarak işaretlenir. 
Bölüm 19'da tartışacağımız ham işaretçiler dışında neredeyse tüm ilkel tipler `Send`'dir.

### `Sync` ile Birden Fazla İş Parçacığından Erişime İzin Verme

`Sync` işaretleyici tanımı, `Sync`'i sürekleyen türe birden fazla iş parçacığından başvurulmasının güvenli olduğunu belirtir. 
Başka bir deyişle, `&T` (`T`'ye değişmez bir referans) `Send` ise herhangi bir `T` türü `Sync`'tir, yani referans başka bir iş 
parçacığına güvenle gönderilebilir. `Send`'e benzer şekilde, ilkel tipler `Sync`'tir ve tamamen `Sync` olan tiplerden oluşan tipler de 
`Sync`'tir.

Akıllı işaretçi `Rc<T>` de `Send` olmamasıyla aynı nedenlerden dolayı `Sync` değildir. `RefCell<T>` türü (Bölüm 15'te bahsetmiştik) 
ve ilgili `Cell<T>` türleri ailesi `Sync` değildir. `RefCell<T>`'nin çalışma zamanında yaptığı ödünç alma denetimi uygulaması iş parçacığı 
güvenli değildir. Akıllı işaretçi `Mutex<T>` `Sync`'tir ve [“Bir `Mutex<T>`'i Birden Fazla İş Parçacığı Arasında Paylaşma”][sharing-a-mutext-between-multiple-threads]<!-- ignore --> bölümünde gördüğünüz gibi erişimi birden fazla iş parçacığı ile paylaşmak için kullanılabilir.

### `Send` ve `Sync`'i Manuel Olarak Süreklemek Güvenli Değildir

`Send` ve `Sync` tanımlarından oluşan türler otomatik olarak `Send` ve `Sync` özelliklerine de sahip olduğundan, 
bu özellikleri manuel olarak süreklememiz gerekmez. İşaretleyici tanımlar olarak, süreklenecek herhangi bir metodları bile yoktur. 
Sadece eşzamanlılıkla ilgili değişmezleri uygulamak için kullanışlıdırlar.

Bu tanımların manuel olarak uygulanması, güvenli olmayan Rust kodunun uygulanmasını gerektirir. 
Güvensiz Rust kodunun kullanımı hakkında Bölüm 19'da konuşacağız; şimdilik önemli bilgi, `Send` ve `Sync` parçalarından oluşmayan yeni eşzamanlı 
türler oluşturmanın güvenlik garantilerini korumak için dikkatli düşünmeyi gerektirdiğidir. 
[“The Rustonomicon”][nomicon] bu garantiler ve bunların nasıl korunacağı hakkında daha fazla bilgi içerir.

## Özet

Bu kitapta eşzamanlılıkla ilgili göreceğiniz son şey bu değil: Bölüm 20'deki proje, bu bölümdeki kavramları burada 
tartışılan küçük örneklerden daha gerçekçi bir durumda kullanacaktır.

Daha önce de belirtildiği gibi, Rust'ın eşzamanlılığı nasıl ele aldığının çok azı dilin bir parçası olduğu için, 
birçok eşzamanlılık çözümü kasa olarak süreklenmektedir. Bunlar standart kütüphaneden daha hızlı gelişir, bu
nedenle çok iş parçacıklı durumlarda kullanılacak güncel, son teknoloji ürünü kasalar için doğru çevrimiçi arama yaptığınızdan emin olun.

Rust standart kütüphanesi, mesaj geçişi için kanallar ve eş zamanlı bağlamlarda kullanımı güvenli olan `Mutex<T>` ve `Arc<T>` gibi akıllı 
işaretçi türleri sağlar. Tür sistemi ve ödünç denetleyicisi, bu çözümleri kullanan kodun veri yarışları veya geçersiz referanslarla 
sonuçlanmamasını sağlar. Kodunuzun derlenmesini sağladıktan sonra, diğer dillerde yaygın olan izlenmesi zor hata türleri olmadan 
birden fazla iş parçacığında mutlu bir şekilde çalışacağından emin olabilirsiniz. Eşzamanlı programlama artık korkulacak bir kavram değil: 
gidin ve programlarınızı korkusuzca eşzamanlı hale getirin!

Daha sonra, Rust programlarınız büyüdükçe sorunları modellemenin ve çözümleri yapılandırmanın deyimsel yollarından bahsedeceğiz. 
Ayrıca, Rust'ın deyimlerinin nesne yönelimli programlamadan aşina olabileceğiniz deyimlerle nasıl ilişkili olduğunu tartışacağız.

[sharing-a-mutext-between-multiple-threads]:
ch16-03-shared-state.html#sharing-a-mutext-between-multiple-threads
[nomicon]: ../nomicon/index.html
