# Büyüyen Projeleri Paketler, Kasalar ve Modüllerle Yönetme

Büyük programlar yazarken, kodunuzu düzenlemek giderek daha önemli hale gelecektir. 
İlgili fonksiyonları gruplandırarak ve kodu farklı özelliklerle ayırarak, belirli bir özelliği uygulayan kodu nerede bulacağınızı ve bir özelliğin nasıl çalıştığını değiştirmek için nereye gideceğinizi netleştireceksiniz.

Şimdiye kadar yazdığımız programlar tek bir dosyada tek bir modülde olmuştur. 
Bir proje büyüdükçe, kodu birden çok modüle ve ardından birden çok dosyaya bölerek düzenlemeniz gerekir.
Bir paket, birden çok ikili kasa ve isteğe bağlı olarak bir kütüphane kasası içerebilir. Bir paket büyüdükçe, 
parçaları dış bağımlılıklar haline gelen ayrı kasalara çıkarabilirsiniz. 
Bu bölüm tüm bu teknikleri kapsar. Birlikte gelişen birbiriyle ilişkili bir dizi paketten oluşan çok büyük projeler için Cargo, Bölüm 14'teki [“Cargo'nun Çalışma Alanları”][workspaces]<!-- ignore --> bölümünde ele alacağımız çalışma alanları sağlar.

Ayrıca, kodu daha yüksek bir düzeyde yeniden kullanmanıza olanak tanıyan kapsülleme uygulama ayrıntılarını da tartışacağız: Bir işlemi uyguladıktan sonra, diğer kod, uygulamanın nasıl çalıştığını bilmek zorunda kalmadan ortak arabirimi aracılığıyla kodunuzu arayabilir. Kodu yazma şekliniz, diğer kodun kullanması için hangi bölümlerin genel olduğunu ve hangi bölümlerin değiştirme hakkını saklı tuttuğunuz özel uygulama ayrıntıları olduğunu tanımlar. Bu, kafanızda tutmanız gereken ayrıntı miktarını sınırlamanın başka bir yoludur.

İlgili bir kavram kapsamdır: Kodun yazıldığı iç içe bağlam, “kapsam dahilinde” olarak tanımlanan bir dizi isme sahiptir. 
Kod okurken, yazarken ve derlerken, programcılar ve derleyiciler, belirli bir noktadaki belirli bir adın bir değişkene, fonksiyona, yapıya, numaralandırılmış yapıya, modüle, sabite veya başka bir öğeye atıfta bulunup bulunmadığını ve bu öğenin ne anlama geldiğini bilmelidir. 
Kapsamlar oluşturabilir ve hangi adların kapsam içinde veya dışında olduğunu değiştirebilirsiniz. Aynı kapsamda aynı ada sahip iki öğeniz olamaz; ad çakışmalarını çözmek için araçlar mevcuttur.

Rust, hangi ayrıntıların açığa çıktığı, hangi ayrıntıların özel olduğu ve programlarınızdaki her kapsamda hangi adların bulunduğu dahil olmak üzere kodunuzun organizasyonunu yönetmenize olanak tanıyan bir dizi özelliğe sahiptir. Bazen toplu olarak modül sistemi olarak adlandırılan bu özellikler şunları içerir:

* **Paketler**: Kasaları oluşturmanıza, test etmenize ve paylaşmanıza olanak tanıyan bir Cargo özelliği
* **Kasalar**: Bir kütüphane ve yürütülebilir dosya oluşturan bir modül ağacı
* **Modüller**: Yolların organizasyonunu, kapsamını ve gizliliğini kontrol etmeye izin
* **Yollar**: Yapı, fonksiyon veya modül gibi bir öğeyi adlandırmanın bir yolu

Bu bölümde, tüm bu özellikleri ele alacağız, nasıl etkileşime girdiklerini tartışacağız ve kapsamı yönetmek için bunların nasıl kullanılacağını açıklayacağız.

Bölüm sonu canavarını kestiğinizde, modül sistemi hakkında sağlam bir anlayışa sahip olmalısınız ve bir usta gibi kapsamlarla çalışabilmelisiniz!

[workspaces]: ch14-03-cargo-workspaces.html
