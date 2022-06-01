## Ekleme E - Sürümler

Bölüm 1'de `cargo new` komutunun *Cargo.toml* dosyasına sürümle alakalı bilgiler
eklediğini gördünüz. Bu eklemede bunun ne anlama geldiğini konuşacağız!

Rust dili ve derleyicisi altı-haftalık yeni özellikler içeren değişmez bir sürüm döngüsüne sahiptir.
Diğer programlama dilleri yeni sürümleri fazla değişikliklerle seyrek sıklıklarla yayınlarlar,
Rust ise küçük değişikliklerle sıklıkla yayınlar. Daha sonra, tüm küçük değişiklikler toplanır ve yayınlandıktan sonra
geriye bakıp şunu demek zor olabilir: “Vay be, Rust 1.10 ve Rust 1.31'ındaki farka bak, Rust ne kadar da değişmiş!”

Her iki ya da üç yılda bir Rust takımı yeni Rust *sürümü* yayınlar. Her sürüm yeni özellikleri tamamıyla 
dokümantasyonlaşmış ve araç gereçleri tam halde getirir. Yeni özellikler her altı-haftalık yayınlama işleminin
bir parçasıdır.

Yeni sürümler farkli kişiler için farklı işlemlere hizmet eder:

* Aktif Rust kullanıcıları için yeni sürüm kolayca anlaşılabilir paketlere büyük değişiklikler getirir.
* Kullanıcı olmayanlar için yeni sürüm Rust'ı farklı bir bakışta baktıracak önemli yenilikler getirdiğinin sinyalidir.
* Rust'ı geliştirenler için yeni sürüm projenin bütünü için bir dönüm noktasıdır.

Bu yazının zamanında, üç Rust sürümü ulaşılabilirdir: Rust 2015, Rust 2018, Rust 2021. 
Bu kitap Rust 2021 sürümünün kuralları ve deyimleri kullanılarak yazılmıştır.


*Cargo.toml*'daki `edition` anahtarı, kodunuzun hangi derleyici sürümüyle derleneceğini belirtir.
Eğer anahtar bulunmuyorsa, Rust geriye dönük uyumluluktan dolayı `2015` sürümünü kullanır.

Her proje varsayılan olan 2015 sürümünden başka sürümü kullanabilir. Sürümler uyumsuz değişiklikler
barındırabilir, örneğin yeni bir anahtar sözcük kodunuzun derlenememesine yol açabilir. Ancak, bu
değişiklikleri seçmediğiniz sürece, kodunuz yeni Rust derleyici sürümlerini kullansanız bile 
derlenmeye devam edecektir.

Tüm Rust derleyici sürümleri var olan önceki sürümleri destekleyecek şekildedir. Ve farklı
sürümdeki kasaları desteklenen herhangi sürümlerle bağlayabilirsiniz. Sürüm değişiklikleri
sadece derleyicinin ayrıştırdığı kodu etkiler. Öyleyse, eğer Rust 2015 kullanıyorsanız ve kullandığınız
bağımlılık Rust 2018 sürümünü kullanıyorsa, kodunuz bu bağımlılığı kullanarak derlenecektir. Aynı şekilde
tam tersi durumda da derlenecektir.

Açık olmak gerekirse: çoğu özellik tüm sürümlerde ulaşılabilecek Geliştiriciler herhangi bir Rust
sürümünü kullandıklarında dahi, yeni stabil sürümlerin gelişimlerini göreceklerdir. ANcak, bazı
durumlarda, özellikle yeni anahtar sözcükler eklendiğinde bazı yeni özellikler sadece sonraki sürümlerde
ulaşılabilir olmaktadır. Eğer özelliklerin avantajlarını edinmek istiyorsanız, yeni sürümlere geçmeniz gerekmektedir.

Daha fazla detay için, [*Sürüm Kılavuzu*](https://doc.rust-lang.org/stable/edition-guide/) kitabını inceleyerek sürümler arasındaki
farkı görebilir ve yeni sürümlere kodunuzu nasıl is `cargo fix` komutuyla güncelleyebileceğinizi öğrenebilirsiniz.
