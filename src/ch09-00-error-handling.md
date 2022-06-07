# Hata Yönetimi

Hatalar, yazılımlar için hayatın bir gerçeğidir, 
bu nedenle Rust, bir şeylerin yanlış gittiği durumları ele almak için bir dizi özelliğe sahiptir. 
Çoğu durumda, Rust, bir hata olasılığını kabul etmenizi ve kodunuz derlenmeden önce bazı işlemler yapmanızı gerektirir. 
Bu gereksinim, kodunuzu üretime dağıtmadan önce hataları keşfetmenizi ve bunları uygun şekilde işlemenizi sağlayarak programınızı daha sağlam hale getirir!

Rust, hataları iki ana kategoriye ayırır: *kurtarılabilir* ve *kurtarılamaz* hatalar. *Dosya bulunamadı* hatası gibi 
kurtarılabilir bir hata için, büyük olasılıkla sorunu kullanıcıya bildirmek ve işlemi yeniden denemek istiyoruz. 
Kurtarılamaz hatalar her zaman bir dizinin sonunun ötesindeki bir konuma erişmeye çalışmak gibi hataların belirtileridir ve bu nedenle programı hemen durdurmak istiyoruz.

Çoğu dil, bu iki tür hatayı ayırt etmez ve istisnalar gibi mekanizmalar kullanarak her ikisini de aynı şekilde ele alır. Rust'ın istisnaları yoktur. Bunun yerine, kurtarılabilir hatalar ve panik durumu için `Result<T, E>` türüne sahiptir! Program kurtarılamaz bir hatayla karşılaştığında yürütmeyi durduran şey burada `panic!` makrosudur. Bu bölüm öncelikle `panic!`'i çağırmayı kapsar ve sonrasında `Result<T, E>` değerlerini döndürmekten bahseder. Ek olarak, bir hatadan kurtulmaya veya yürütmeyi durdurmaya karar verirken göz önünde bulundurulması gereken noktaları keşfedeceğiz.
