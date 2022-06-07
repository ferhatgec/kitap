# Otomatize Testler Yazma

Edsger W. Dijkstra'nın 1972 tarihli “The Humble Programmer” adlı makalesinde, 
“Program testi, hataların varlığını göstermek için çok etkili bir yol olabilir, ancak onların yokluğunu göstermek için umutsuzca yetersizdir” dedi. Bu, elimizden geldiğince test etmeye çalışmamamız gerektiği anlamına gelmez!

Programlarımızdaki doğruluk, kodumuzun yapmayı amaçladığımız şeyi ne ölçüde yaptığıdır. Rust, programların doğruluğu konusunda yüksek derecede endişe ile tasarlanmıştır, ancak doğruluğu karmaşıktır ve kanıtlanması kolay değildir. Rust'ın tür sistemi bu yükün büyük bir kısmını omuzlar, ancak bu tür sistemi her şeyi yakalayamaz. Bu nedenle Rust, otomatize yazılım testlerini yazma desteğini
dahili olarak içermektedir.

Kendisine iletilen sayıya 2 ekleyen bir `add_two` fonksiyonu yazdığımızı varsayalım. 
Bu fonksiyonun yapısı, parametre olarak bir tam sayı kabul eder ve sonuç olarak bir tam sayı döndürür. 
Bu fonksiyonu süreklediğimizde ve derlediğimizde; Rust, örneğin, bu fonksiyona bir `String` değeri veya geçersiz bir referans geçirmediğimizden emin olmak için şimdiye kadar öğrendiğiniz tüm tür kontrollerini ve ödünç alma kontrollerini yapar. 
Ancak Rust, bu fonksiyonun tam olarak neyi amaçladığımızı anlayamaz ve bunu *kontrol edemez*; 
bu, örneğin parametre artı 10 veya parametre eksi 50 yerine parametre artı 2'yi döndürür! 
Testlerin gerektiği yer burasıdır.

Örneğin, `add_two` fonksiyonuna `3`'ü ilettiğimizde, döndürülen değerin `5` olduğunu iddia eden testler yazabiliriz.

Testler oluşturmak karmaşık bir beceridir: iyi testlerin nasıl yazılacağına dair her ayrıntıyı bir bölümde ele alamasak da, Rust'ın test tesislerinin mekaniğini tartışacağız. Testlerinizi yazarken kullanabileceğiniz ek açıklamalar ve makrolar, testlerinizi çalıştırmak için sağlanan varsayılan davranış ve seçenekler ve testlerin birim testleri ve entegrasyon testleri halinde nasıl organize edilebileceği hakkında konuşacağız.
