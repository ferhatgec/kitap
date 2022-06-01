# Rust Programlama Dili

[Rust Programlama Dili](title-page.md)
[Ön söz](foreword.md)
[Başlangıç](ch00-00-introduction.md)

## Başlarken

- [Başlarken](ch01-00-getting-started.md)
    - [Yükleme](ch01-01-installation.md)
    - [Merhaba, Dünya!](ch01-02-hello-world.md)
    - [Merhaba, Cargo!](ch01-03-hello-cargo.md)

- [Tahmin Oyunu Programlamak](ch02-00-guessing-game-tutorial.md)

- [Yaygın Programlama Kavramları](ch03-00-common-programming-concepts.md)
    - [Değişkenler ve Değişebilirlik](ch03-01-variables-and-mutability.md)
    - [Veri Türleri](ch03-02-data-types.md)
    - [Fonksiyonlar](ch03-03-how-functions-work.md)
    - [Yorum Satırları](ch03-04-comments.md)
    - [Kontrol Akışı](ch03-05-control-flow.md)

- [Sahipliği Anlamak](ch04-00-understanding-ownership.md)
    - [Sahiplik Nedir?](ch04-01-what-is-ownership.md)
    - [Referanslar ve Ödünç Alma Mekanizması](ch04-02-references-and-borrowing.md)
    - [Dilim Türü](ch04-03-slices.md)

- [İlintili Verileri Yapılandırmak için Yapıları Kullanmak](ch05-00-structs.md)
    - [Yapıları Tanımlamak ve Örneklendirmek](ch05-01-defining-structs.md)
    - [Yapıları Kullanan Örnek Bir Program](ch05-02-example-structs.md)
    - [Metod Söz dizimi](ch05-03-method-syntax.md)

- [Numaralandırılmış Yapılar ve Model Eşleme](ch06-00-enums.md)
    - [Numaralandırılmış Yapı Tanımlamak](ch06-01-defining-an-enum.md)
    - [`match` Kontrol Akış Yapısı](ch06-02-match.md)
    - [`if let` ile Kısa Kontrol Akış Yapısı](ch06-03-if-let.md)

## Temel Rust Kültürü

- [Büyüyen Projeleri Paketler, Kasalar ve Modüllerle Yönetme](ch07-00-managing-growing-projects-with-packages-crates-and-modules.md)
    - [Paketler ve Kasalar](ch07-01-packages-and-crates.md)
    - [Gizlilik ve Kontrol Kapsamı için Modüller Tanımlamak](ch07-02-defining-modules-to-control-scope-and-privacy.md)
    - [Modül Ağacındaki bir Öğeyi Referanslama Yolları](ch07-03-paths-for-referring-to-an-item-in-the-module-tree.md)
    - [`use` Kullanarak Yolları Kapsama Getirmek](ch07-04-bringing-paths-into-scope-with-the-use-keyword.md)
    - [Modülleri Farklı Dosyalara Ayırmak](ch07-05-separating-modules-into-different-files.md)

- [Yaygın Koleksiyon Türleri](ch08-00-common-collections.md)
    - [Veri Listelerini Vektörlerle Tutmak](ch08-01-vectors.md)
    - [UTF-8 ile Kodlanmış Dizgiyi Tutmak](ch08-02-strings.md)
    - [İlişkili Değerlerle Anahtarları Saklamak](ch08-03-hash-maps.md)

- [Hata Yönetimi](ch09-00-error-handling.md)
    - [`panic!` ile Kurtarılamayan Hatalar](ch09-01-unrecoverable-errors-with-panic.md)
    - [`Result` ile Kurtarılabilir Hatalar](ch09-02-recoverable-errors-with-result.md)
    - [`panic!`lemek ya da `panic!`lememek](ch09-03-to-panic-or-not-to-panic.md)

- [Yaygın Türler, Tanımlar ve Ömürlükler](ch10-00-generics.md)
    - [Yaygın Veri Türleri](ch10-01-syntax.md)
    - [Tanımlar: Yaygın Davranış Tanımlama](ch10-02-traits.md)
    - [Ömürlük Yapısıyla Referans Doğrulama](ch10-03-lifetime-syntax.md)

- [Otomatikleşmiş Testler Yazma](ch11-00-testing.md)
    - [Testler Nasıl Yazılır](ch11-01-writing-tests.md)
    - [Testlerin Nasıl Çalıştığını Kontrol Etmek](ch11-02-running-tests.md)
    - [Test Organizasyonu](ch11-03-test-organization.md)

- [Bir G/Ç Projesi: Komut Satırı Programı Yazmak](ch12-00-an-io-project.md)
    - [Komut Satırı Argümanlarını Ayrıştırmak](ch12-01-accepting-command-line-arguments.md)
    - [Dosya Okumak](ch12-02-reading-a-file.md)
    - [Modülerliği ve Hata Yönetimini Geliştirmek için Yeniden Düzenlemek](ch12-03-improving-error-handling-and-modularity.md)
    - [Test Odaklı Geliştirme ile Kütüphanenin İşlevliğini Artırmak](ch12-04-testing-the-librarys-functionality.md)
    - [Ortam Değişkenlerini Kullanmak](ch12-05-working-with-environment-variables.md)
    - [Hata Mesajlarını Standart Çıktı yerine Standart Hata Akışına Yazdırmak](ch12-06-writing-to-stderr-instead-of-stdout.md)

## Rust'ta Düşünmek

- [Fonksiyonel Dil Özellikleri: Yineleyiciler ve Kapanış İfadeleri](ch13-00-functional-features.md)
    - [Kapanış İfadeleri: Ortamda Tutulabilen Anonim Fonksiyonlar](ch13-01-closures.md)
    - [Yineleyicilerle Bir Dizi Öğeyi İşlemek](ch13-02-iterators.md)
    - [G/Ç Projemizi Geliştirmek](ch13-03-improving-our-io-project.md)
    - [Performans Kıyaslaması: Döngüler vs. Yineleyiciler](ch13-04-performance.md)

- [Cargo ve Crates.io Hakkında Daha Fazla Bilgi](ch14-00-more-about-cargo.md)
    - [Dağıtım Profilleriyle İnşaları Özelleştirmek](ch14-01-release-profiles.md)
    - [Crates.io'da Bir Kasa Paylaşmak](ch14-02-publishing-to-crates-io.md)
    - [Cargo Çalışma Alanları](ch14-03-cargo-workspaces.md)
    - [`cargo install` ile Crates.io'dan Derlenmişleri Yüklemek](ch14-04-installing-binaries.md)
    - [Özel Komutlarla Cargo'yu Genişletmek](ch14-05-extending-cargo.md)

- [Akıllı İşaretçiler](ch15-00-smart-pointers.md)
    - [Yığındaki Verileri İşaretlemek için `Box<T>` Kullanmak](ch15-01-box.md)
    - [Akıllı İşaretçilere `Deref` ile Normal Referanslar Gibi Tanım Yapmak](ch15-02-deref.md)
    - [`Drop` Tanımlamasıyla Temizlik Aşamasında Kod Çalıştırmak](ch15-03-drop.md)
    - [`Rc<T>`, Referans Sayaçlı Akıllı İşaretçi](ch15-04-rc.md)
    - [`RefCell<T>` ve İç Değişebilirlik Modeli](ch15-05-interior-mutability.md)
    - [Referans Döngüleri Bellek Sızdırabilir](ch15-06-reference-cycles.md)

- [Korkusuz Eşzamanlılık](ch16-00-concurrency.md)
    - [Kodu Eşzamanlı Çalıştırmak için İş Parçacıklarını Kullanmak](ch16-01-threads.md)
    - [Konular Arasında Veri Aktarmak için İleti Geçişini Kullanmak](ch16-02-message-passing.md)
    - [Paylaşımlı Eşzamanlık](ch16-03-shared-state.md)
    - [`Sync` ve `Send` Tanımlarıyla Genişletilebilir Eşzamanlılık](ch16-04-extensible-concurrency-sync-and-send.md)

- [Rust'taki Nesne Yönemimli Programlama Özellikleri](ch17-00-oop.md)
    - [Nesne Yönelimli Dillerin Karakteristik Özellikleri](ch17-01-what-is-oo.md)
    - [Farklı Türlerdeki Değerlere İzin Veren Özellik Tanımlarını Kullanma](ch17-02-trait-objects.md)
    - [Nesneye Yönelik Tasarım Modelini Süreklemek](ch17-03-oo-design-patterns.md)

## İleri Düzey Konular

- [Modeller ve Eşleştirme](ch18-00-patterns.md)
    - [Modeller Her Yerde Kullanılabilir](ch18-01-all-the-places-for-patterns.md)
    - [Reddedilebilirlik: Modelin Eşleştirmede Başarısız Olup Olmayacağı](ch18-02-refutability.md)
    - [Model Söz Dizimi](ch18-03-pattern-syntax.md)

- [Gelişmiş Özellikler](ch19-00-advanced-features.md)
    - [Güvensiz Rust](ch19-01-unsafe-rust.md)
    - [Gelişmiş Tanımlar](ch19-03-advanced-traits.md)
    - [Gelişmiş Türler](ch19-04-advanced-types.md)
    - [Gelimiş Fonksiyonlar ve Kapanış İfadeleri](ch19-05-advanced-functions-and-closures.md)
    - [Makrolar](ch19-06-macros.md)

- [Final Projesi: Çoklu İş Parçacıklı Web Sunucusu İnşa Etmek](ch20-00-final-project-a-web-server.md)
    - [Tek İş Parçacıklı Web Sunucusu İnşa Etmek](ch20-01-single-threaded.md)
    - [Tek İş Parçacıklı Sunucumuzu Çok Parçacıklı Sunucuya Çevirmek](ch20-02-multithreaded.md)
    - [Zarifçe Kapatmak ve Temizlemek](ch20-03-graceful-shutdown-and-cleanup.md)

- [Eklemeler](appendix-00.md)
    - [A - Anahtar Sözcükler](appendix-01-keywords.md)
    - [B - Operatörler ve Semboller](appendix-02-operators.md)
    - [C - Türetilebilir Tanımlar](appendix-03-derivable-traits.md)
    - [D - Kullanışlı Geliştirme Araçları](appendix-04-useful-development-tools.md)
    - [E - Sürümler](appendix-05-editions.md)
    - [F - Kitap'ın Çevirileri](appendix-06-translation.md)
    - [G - Rust ve “Gecelik Rust” Nasıl Yapıldı](appendix-07-nightly-rust.md)
