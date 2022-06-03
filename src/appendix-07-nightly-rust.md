## Ekleme G - “Gecelik Rust” ve Rust Nasıl Yapıldı

Bu ek, Rust'ın nasıl yapıldığı ve bunun bir Rust geliştiricisi olarak sizi nasıl etkilediği hakkındadır.

### Durgunluk Olmadan Stabilite

Rust, kodunuzun kararlılığına *çok* önem verir. Rust'ı üzerine inşa edebileceğiniz kaya gibi sağlam bir temel olmasını istiyoruz ve işler sürekli değişiyor olsaydı bu imkansız olurdu. Aynı zamanda, yeni özellikleri deneyemezsek kusurları çözemeyiz. Bu soruna bizim çözümümüz “durgunluk olmadan istikrar” dediğimiz şeydir ve yol gösterici ilkemiz şudur: Asla yeni bir kararlı Rust sürümüne geçmekten korkmamalısınız. Her yükseltme sorunsuz olmalı, ancak size yeni özellikler, daha az hata ve daha hızlı derleme süreleri de getirmelidir.

### Çuf çuf! Kanalları Bırakın ve Yeni Trenlere Binin

Rust'ın gelişimi, bir *tren tarifesine* göre çalışır. Yani, tüm geliştirmeler Rust deposunun ana dalında yapılır. Sürümler, Cisco IOS ve diğer yazılım projeleri tarafından kullanılan bir yazılım sürüm dizisi modelini takip eder. 

Rust için üç yayın kanalı vardır:

* Gecelik
* Beta
* Stabil

Çoğu Rust geliştiricisi ana olarak stabil kanalını kullanır fakat deneysel yeni özellikleri denemek isteyen
Rustseverler isterlerse gecelik ya da beta kanallarını da kullanabilirler.

Geliştirme ve sürüm sürecinin nasıl çalıştığına dair bir örnek: 
Rust ekibinin Rust 1.5'in sürümü üzerinde çalıştığını varsayalım. Bu sürüm Aralık 2015'te yayınlandı 
ve Rust'a yeni bir çok özellik eklendi: bunlar eklenirken ana dalda birçok yeni bir taahhüt (commit) gönderilmiş oluyor. 
Her gece, Rust'ın yeni bir gecelik versiyonu üretilir. Her gün bir yayın günüdür ve bu yayınlar yayın altyapımız tarafından otomatik olarak oluşturulur. Zaman geçtikçe, yayınlarımız gecede bir kez şöyle görünür:

```text
nightly: * - - * - - *
```

Her altı haftada bir, bizim için yayın zamanıdır! `beta` dalı Rust deposundan geceliğin kullandığı `master` dalını çıkarır. 
Burada artık iki yayın var olmuş olur:

```text
nightly: * - - * - - *
                     |
beta:                *
```
 
Çoğu Rust kullanıcısı beta yayınlarını aktif olarak kullanmaz, ancak Rust'ın olası sorunları keşfetmesine yardımcı olmak için CI sistemlerinde betaya karşı test yapar. Bu arada, her gece gecelik sürümünün yayınlandığını hatırlatmış olalım:

```text
nightly: * - - * - - * - - * - - *
                     |
beta:                *
```

Diyelim ki bir sorun bulundu. İyi bir şey olacak ki, sorun kararlı bir sürüme girmeden önce `beta` sürümünü test edebilecek vaktimiz kalıyor.
Düzeltme `master`'a uygulanır, böylece oluşan sorunlar gece düzeltilir ve ardından düzeltme `beta` dalına geri aktarılır ve yeni bir `beta` sürümü üretilir:

```text
nightly: * - - * - - * - - * - - * - - *
                     |
beta:                * - - - - - - - - *
```

İlk `beta` oluşturulduktan altı hafta sonra, `stable` sürümünün zamanı geldi!
`stable` dalı `beta` dalından türetilir:

```text
nightly: * - - * - - * - - * - - * - - * - * - *
                     |
beta:                * - - - - - - - - *
                                       |
stable:                                *
```

Yaşasın! Rust 1.5 tamam! Ancak bir şeyi unuttuk: 
altı hafta geçtiği için, Rust'ın bir sonraki sürümü olan 1.6'nın yeni bir `beta` sürümünün de yayınlanması gerekiyor. Bu nedenle, `beta`'nın `stable`'dan dallanmasından sonra, `beta`'nın bir sonraki sürümü her gece tekrar dallanır ve güncellenir:


```text
nightly: * - - * - - * - - * - - * - - * - * - *
                     |                         |
beta:                * - - - - - - - - *       *
                                       |
stable:                                *
```

Buna “tren modeli” denir, çünkü her altı haftada bir, bir sürüm ”istasyonu terk eder”, ancak kararlı bir sürüm olarak gelmeden önce yine de beta kanalında bir yolculuğa daha çıkması gerekir. Rust, saat altı gibi haftada bir çıkıyor. Bir Rust sürümünün tarihini biliyorsanız, bir sonrakinin tarihini de bilirsiniz: altı hafta sonra. Her altı haftada bir planlanan yayınlara sahip olmanın güzel bir yönü, bir sonraki trenin yakında gelmesidir. Bir özellik belirli bir sürümü kaçırırsa, endişelenmenize gerek yok: kısa süre içinde bir yenisi daha geliyor! Bu, son teslim tarihine yakın olası cilasız özellikleri gizlice sokma baskısını azaltmaya yardımcı olur. Bu süreç sayesinde, her zaman Rust'ın bir sonraki sürümünü kontrol edebilir ve yükseltmenin kolay olduğunu kendiniz doğrulayabilirsiniz: bir beta sürümü beklendiği gibi çalışmazsa, bunu ekibe bildirebilir ve güncellemeden önce düzeltmesini sağlayabilirsiniz ve sonraki kararlı sürümde çıkabilecek bir sorun çözülmüş olur! Beta sürümünde çökmeler nispeten nadirdir, ancak `rustc` hala bir yazılım parçasıdır ve sorunlar mevcuttur.


### Kararsız Özellikler

Bu sürüm modeliyle ilgili bir sorun daha var: 
kararsız özellikler. 

Rust, belirli bir sürümde hangi özelliklerin etkinleştirildiğini belirlemek için “özellik bayrakları“ adı verilen bir teknik kullanır. 

Yeni bir özellik aktif olarak geliştiriliyorsa, o özelliği gerekli özellik bayraklarını kullanarak çağırabilirsiniz. En son değişiklikleri
kullanabilmeniz için `nightly` de olmanız gerekebilir. Bir kullanıcı olarak, devam eden çalışmaları denemek istiyorsanız, deneyebilirsiniz, ancak Rust'ın her gece yayınlanan sürümünü kullanıyor olmanız ve istenilen özelliği kullanabilmek için kaynak kodunuza uygun bayrakla açıklama eklemeniz gerekir. Son teknolojiyi kullanmak isteyenler bunu yapabilir ve kaya gibi sağlam bir deneyim isteyenler kararlı bir şekilde kalabilir ve kodlarının kırılmayacağını bilir. Durgunluk olmadan istikrar. Bu kitap yalnızca kararlı özellikler hakkında bilgi içerir, çünkü devam eden özellikler hala değişmektedir ve kesinlikle bu kitabın yazıldığı zaman ile kararlı yapılarda etkinleştirildikleri zaman arasında farklı olacaktır. Yalnızca gecelik özelliklerle ilgili belgeleri çevrimiçi olarak bulabilirsiniz.


### Rustup ve Rust Nightly'nin Rolü

Rustup, farklı yayın kanalları arasındaki dönüşümleri sizin için kolaylaştırır.
Varsayılan olarak, stabil Rust sürümünün yüklü olması gerekir. Gecelik sürümünü yükleyebilimeniz için şu
komutu girebilirsiniz:

```console
$ rustup toolchain install nightly
```

Göründüğü gibi tüm araç takımlarını (Rust'ın sürüm yayınlarını ve bileşenlerini)
`rustup`'la da pekala yükleyebilirsiniz. İşte Windows bilgisayarından bir örnek:

```powershell
> rustup toolchain list
stable-x86_64-pc-windows-msvc (default)
beta-x86_64-pc-windows-msvc
nightly-x86_64-pc-windows-msvc
```

Gördüğünüz gibi, kararlı araç zinciri varsayılandır. 
Çoğu Rust kullanıcısı çoğu zaman kararlı sürümü kullanır. Kök dizindeyken `rustup`'un kullanması gereken tek gecelik araç zincirini ayarlamak için o projenin dizininde `rustup override` komutunu kullanabilirsiniz:

```console
$ cd ~/projects/needs-nightly
$ rustup override set nightly
```

### RFC Süreci ve Ekipler

Peki bu yeni özellikler hakkında nasıl bilgi edineceksiniz? Rust'ın geliştirme modeli, bir *Yorum İsteği* (RFC) sürecini takip eder. 
Rust'ta bir iyileştirme istiyorsanız, RFC adlı bir teklif yazabilirsiniz. 
Rust'ı geliştirmek için herkes RFC'ler yazabilir ve öneriler, birçok konu alt ekibinden oluşan Rust ekibi tarafından incelenir ve tartışılır. 
[Rust'ın web sitesinde](https://www.rust-lang.org/governance), dil tasarımı, derleyici uygulaması, altyapı, dokümantasyon ve daha fazlası gibi projenin her alanı için ekipleri içeren tam bir ekip listesi bulunmaktadır. Uygun ekip teklifi ve yorumları okur, kendi yorumlarını yazar ve sonunda özelliği kabul veya reddetme konusunda fikir birliği sağlanır. Özellik kabul edilirse Rust deposunda bir sorun açılır ve birisi bunu . Bunu çok iyi uygulayan kişi, özelliği ilk etapta öneren kişi olmayabilir! Uygulama hazır olduğunda, [“Kararsız Özellikler”](#unstable-features)<!-- ignore --> bölümünde tartıştığımız gibi, bir özellik kapısının arkasındaki ana dal üzerine iner. Bir süre sonra, her gece yayınlananları kullanan Rust geliştiricileri yeni özelliği deneyebildiklerinde, ekip üyeleri bu özelliği, her gece nasıl çalıştığını tartışacak ve kararlı Rust'a dönüştürüp dönüştürmeyeceğine karar verecek. Bu da gelecek trenin içinde olabileceği anlamına gelebilir!
