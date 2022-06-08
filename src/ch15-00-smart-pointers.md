# Akıllı İşaretçiler

*İşaretçi*, bellekte bir adres içeren bir değişken için genel bir kavramdır. 
Bu adres, diğer bazı verilere atıfta bulunur veya “işaret eder”. 
Rust'taki en yaygın işaretçi türü, Bölüm 4'te öğrendiğiniz bir referanstır. 
Referanslar `&` sembolü ile gösterilir ve işaret ettikleri değeri ödünç alırlar. 
Verilere atıfta bulunmak dışında herhangi bir özel yetenekleri yoktur ve ek yükleri yoktur.

*Akıllı işaretçiler* ise işaretçi gibi davranan ancak ek meta veri ve yeteneklere sahip veri yapılarıdır. 
Akıllı işaretçiler kavramı Rust'a özgü değildir: akıllı işaretçiler ilk C++'ta ortaya çıkmıştır ve 
diğer birçok dilde de mevcuttur. Rust, standart kütüphanede tanımlanan ve referanslar tarafından sağlananın 
ötesinde işlevsellik sağlayan çeşitli akıllı işaretçilere sahiptir. Genel konsepti keşfetmek için, 
bir *referans sayma* akıllı işaretçi türü de dahil olmak üzere birkaç farklı akıllı işaretçi örneğine bakacağız. 
Bu işaretçi, sahiplerin sayısını takip ederek ve hiçbir sahip kalmadığında verileri temizleyerek verilerin
birden fazla sahibinin olmasına izin vermenizi sağlar.

Sahiplik ve ödünç alma kavramıyla Rust, referanslar ve akıllı işaretçiler arasında ek bir farka sahiptir: 
referanslar yalnızca veri ödünç alırken, çoğu durumda akıllı işaretçiler işaret ettikleri verilere sahiptir.

O zamanlar onları bu kadar çok farklı şekillerde adlandırmasak da, 
Bölüm 8'de konuştuğumuz `String` ve `Vec<T>` dahil olmak üzere bu kitapta birkaç akıllı işaretçiyle zaten 
karşılaştık. Ek olarak bu işaretçiler meta verilere ve ekstra yeteneklere veya garantilere sahiptirler. 
Örneğin `String`, kapasitesini meta veri olarak depolar ve verilerinin her zaman geçerli bir UTF-8 olmasını sağlamak için ekstra yeteneğe sahiptir.

Akıllı işaretçiler genellikle yapılar kullanılarak süreklenirler. Sıradan bir yapının aksine, akıllı işaretçiler `Deref` ve `Drop` tanımlarını uygular. `Deref` özelliği, akıllı işaretçi yapısının bir örneğinin referansı gibi davranmasına izin verir, 
böylece kodunuzu başvurularla veya akıllı işaretçilerle çalışacak şekilde yazabilirsiniz. 
`Drop` tanımı, akıllı işaretçinin bir örneği kapsam dışına çıktığında çalıştırılan kodu özelleştirmenize olanak tanır. 
Bu bölümde, her iki özelliği de tartışacağız ve akıllı işaretçiler için neden önemli olduklarını göstereceğiz.

Akıllı işaretçi kalıbının Rust'ta sıkça kullanılan genel bir tasarım kalıbı olduğu düşünüldüğünde, 
bu bölüm mevcut her akıllı işaretçiyi kapsamayacaktır. Birçok kütüphanenin kendi akıllı işaretçileri 
vardır ve hatta kendinizinkini bile yazabilirsiniz. Bu bölümde standart kütüphanedeki en yaygın akıllı 
işaretçileri ele alacağız:

* `Box<T>`, yığın üzerinde yer tahsis eden bir tür
* `Rc<T>`, çoklu sahiplik sağlayan bir referans sayma türü
* `Ref<T>` ve `RefMut<T>`, `RefCell<T>` aracılığıyla erişilen, derleme zamanı yerine çalışma zamanında ödünç alma kurallarını 
  uygulamaya zorlayan bir tür

Ek olarak, değişmez bir türün bir iç değeri mutasyona uğratmak için çıkardığı iç değişebilirlik modelini ele alacağız. 
Ayrıca *referans döngülerinin* nasıl bellek sızdırabilecekleri ve nasıl önlenebilecekleri hakkında tartışacağız.

Öyleyse, hadi dalalım!
