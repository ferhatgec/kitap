# Bir G/Ç Projesi: Bir Komut Satırı Programı Yazmak

Bu bölüm, şimdiye kadar öğrendiğiniz birçok becerinin bir özeti ve birkaç standart 
kütüphane özelliğinin daha keşfidir. Şu anda sahip olduğunuz bazı Rust 
konseptlerini süreklemek için dosya ve komut satırı giriş/çıkış ile etkileşime giren 
bir komut satırı aracı oluşturacağız.

Rust'ın hızı, güvenliği, tek ikili yürütülebilir çıktısı ve platformlar arası desteği, 
onu komut satırı araçları oluşturmak için ideal bir dil haline getiriyor, bu nedenle projemiz için 
klasik komut satırı arama aracı `grep`'in (**g**lobally search a **r**egular **e**xpression and **p**rint) kendi sürümümüzü yapacağız. En basit kullanım durumunda, `grep`, belirtilen bir dize için belirtilen bir dosyayı arar. 
Bunu yapmak için `grep`, argümanları olarak bir dosya adı ve bir dizgi alır. Sonra dosyayı okur, 
o dosyada dizgi argümanını içeren satırları bulur ve bu satırları yazdırır.

Bu arada, komut satırı aracımızın diğer birçok komut satırı aracının kullandığı üçbirim özelliklerini kullanmasını nasıl sağlayacağımızı göstereceğiz. Kullanıcının aracımızın davranışını yapılandırmasına izin vermek için bir ortam 
değişkeninin değerini okuyacağız. Ayrıca hata mesajlarını standart çıktı (`stdout`) yerine standart hata konsolu 
akışına (`stderr`) yazdıracağız, böylece kullanıcı ekranda hata mesajlarını görmeye devam ederken başarılı çıktıyı bir dosyaya yönlendirebilir.

Bir Rust topluluğu üyesi olan Andrew Gallant, `ripgrep` adlı tam özellikli, çok hızlı bir `grep` sürümü oluşturmuştur. Karşılaştırıldığında, bizim versiyonumuz oldukça basit olacak, ancak bu bölüm size `ripgrep` gibi gerçek dünyadaki bir projeyi anlamak için ihtiyaç duyduğunuz bazı arka plan bilgilerini verecektir.

* Kodu organize etmek ([Bölüm 7][ch7]<!--
  ignore -->'de modüllerle ilgili öğrendiklerinizi kullanarak)
* Vektörleri ve dizgileri kullanmak ([Bölüm 8][ch8]<!-- ignore -->'deki koleksiyonlar)
* Hataları işlemek ([Bölüm 9][ch9]<!-- ignore -->)
* Uygun yerlerde tanımları ve ömürlükleri kullanmak ([Bölüm 10][ch10]<!-- ignore
  -->)
* Testler yazmak ([Bölüm 11][ch11]<!-- ignore -->)

Ayrıca Bölüm [13][ch13]<!-- ignore --> ve [17][ch17]<!-- ignore -->'de ayrıntılı olarak ele alınacak olan kapanışları, 
yineleyicileri ve tanım nesnelerini kısaca tanıtacağız.

[ch7]: ch07-00-managing-growing-projects-with-packages-crates-and-modules.html
[ch8]: ch08-00-common-collections.html
[ch9]: ch09-00-error-handling.html
[ch10]: ch10-00-generics.html
[ch11]: ch11-00-testing.html
[ch13]: ch13-00-functional-features.html
[ch17]: ch17-00-oop.html
