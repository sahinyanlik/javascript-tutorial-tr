# An Introduction to JavaScript
# Javascript'e Giriş

Bakalım Javascript'in özelliği ne, ne yapılır ve hangi teknolojilerle birlikte çalışır.

## Javascript  Nedir?

*JavaScript* aslen *"web sayfalarına canlılık"* getirmek için oluşturulmuştur.

Bu dilde yapılan programlara *scripts* denir. Doğrudan HTML kodu içerisine yazılıp sayfa yüklendiğinde doğrudan çalışabilir.

Komutlar herhangi bir derleme ve hazırlığa ihtiyaç duymadan doğrudan çalışırlar.

Bu yönden bakınca Javascript diğer dillere nazaran oldukça farklıdır.Bkz: [Java](http://en.wikipedia.org/wiki/Java).


```smart header="Neden <u>Java</u>Script?"
Javascript ilk yazıldığında, başka bir adı vardı: "LiveScript". Fakat Java dili o dönemlerde çok meşhur olduğundan dolayı yeni bir dil ve "küçük kardeş" gibi görünmesi açısından Javascript olarak değiştirildi.

Fakat Javascript gelişerek kendince şartnameleri [ECMAScript](http://en.wikipedia.org/wiki/ECMAScript) olan bağımsız bir dil haline geldi. Şu anda Java ile hiç bir ilgisi bulunmamaktadır.
```
Günümüzde Javascript sadece web tarayıcıda değil, sunucuda veya
[ JavaScript motoru](https://en.wikipedia.org/wiki/JavaScript_engine) olan her yerde çalışmaktadır.

Tarayıcılar bu Javascript motoru gömülü bir şekilde gelirler. Bu ayrıca "Javascript sanal makinesi" olarak da adlandırılır.

Bu Javascript motorlarından bazıları şunlardır;

- [V8](https://en.wikipedia.org/wiki/V8_(JavaScript_engine)) --  Chrome ve Opera.
- [SpiderMonkey](https://en.wikipedia.org/wiki/SpiderMonkey) --  Firefox.
- Internet Explorer'ın "Trident", "Chakra" kod isimli motorlarının yanında Microsoft Edge için "ChakraCore" adında ayrı bir motoru bulunmaktadır. Safari ise "Nitro","SquirrelFish" gibi kod adlarına sahip Javascript motoru kullanmaktadır.

Yukarıdaki terimleri ezberlerseniz iyi olur, çünkü ileride şöyle bir cümle ile karşılaşabilirsiniz "X özelliği V8 tarafından desteklenmektedir". Bu özelliğin Chrome ve Opera tarafından desteklendiğini anlamanız gerekir.

```smart header="Javascript Motoru Nasıl Çalışır?"

Motorlar çok karmaşık yapılardır. Fakat basit temellere dayanırlar.

1. Eğer bu motor tarayıcıya gömülmüş ise yazılan javascript kodlarını ayrıştırır.
2. Sonra bu kodları makine diline çevirir.
3. Makine bu kodları çok hızlı bir şekilde çalıştırır.

Motor bu sürecin her bir adımında optimizasyon yapar. Hatta derlenmiş ve çalışır halde bulunan kodlardaki veri yapılarını inceler ve bunları optimize ederek daha hızlı hale getirir. Sonuç olarak yazılan bu kodlar çok hızlı bir şekilde çalışır.
```

## Tarayıcı içerisindeki Javascript neler yapabilir?

Modern Javascript "güvenli" bir programlama dilidir. Düşük seviye diller gibi hafıza veya işlemciye doğrudan erişim sağlamaz. Browser için olduğundan dolayı böyle birşeye ihtiyaç duymaz.

Javascript'in yapabilecekçeleri büyük bir oranda ortama dayanır. Örneğin [Node.JS](https://wikipedia.org/wiki/Node.js) Javascript fonksiyonları ile dosyaları okuma yazma veya ağ üzerinden talep etme işlemlerini yapabilir.

Tarayıcı içerisindeki Javascript ise web sayfasında görsel değişikliklere ve kullanıcı ile web sunucu arasındaki etkileşime dair herşeyi yapabilir.

Örneğin tarayıcı içerisindeki Javascript şunları yapabilir:

- Sayfaya yeni HTML kodları ekleme veya öncekileri değiştirme, stilleri değiştirme veya ekleme.
- Kullanıcının aksiyonlarına karşılık verme. Tıklanma veya farenin hareketine göre işlem yaptırabilme.
- Ağ üzerinden talep gönderebilme. Dosya yükleme veya indirebilme ( buna [AJAX](https://en.wikipedia.org/wiki/Ajax_(programming)) and [COMET](https://en.wikipedia.org/wiki/Comet_(programming) teknolojileri denir )
- Tarayıcıdaki cookieleri silme ekleme veya düzeltme işmelerinin yapılması. Mesaj gösterilmesi.
- Kullanıcı tarafında tutulan verilerin hatırlanması ( "local storage")

## Tarayıcı içerisinde bulunan Javascript ne yapamaz ?

Browser içerisinde bulunan Javascript kullanıcı güvenliği dolayısıyla limitlidir. Amaç zararlı web sitelerinin özel bilgilere erişip kullanıcıya zarar vermesini engellemektir.

Engellemeleri şu şekilde sıralayabiliriz :

- Web sayfasında çalışan Javascript dosyalara erişimi olmayabilir, hard diskinizde bulunan programları kopyalayamaz veya çalıştıramaz. İşletim sistemine doğrudan ulaşımı yoktur.

    Modern tarayıcılar dosyalarla çalışmanıza izin verebilir. Fakat bu izim oldukça sınırlıdır. Örneğin sadece dosyayı tarayıcıya taşıyıp bırakabilirsiniz veya `<input>` kullanarak dosyayı seçebilirsiniz.

    Her zaman kullanıcıyla kamera veya mikrofon vasıyasıyla veya diğer cihazlar vasıtasıyla etkileşime geçebilirsiniz. Fakat kullanıcının kesin iznini almanız gerekir. Dolayısıyla Javascript çalışan web sayfası gizliden sizin web kameranızı izleyemez veya etrafınızdaki şeyler hakkında bilgi alamaz. [NSA](https://en.wikipedia.org/wiki/National_Security_Agency)

- Farklı tablar birbiri ile iletişime geçemez ve bilgi alış verişi yapamayabilirler. Bazen yaparlar, örneğin bir tabdan Javascript ile diğer tabı açabilirsiniz. Bu halde bile, bir sayfa diğerinden farklı domain, protokol veya portlarda ise erişemez.

    Bu olaya "Same Origin Policy" ( Aynı köken politikası ) denir. Bunu çözmek için *her iki sayfa* özel bir javascript kodu ile birbirlerini onaylamalılar. Bu engellemeler yine kullanıcının güvenliği içindir. Kullanıcının açtığı `http://anysite.com` sitesi diğer tabda bulunan `http://gmail.com` sitesinden bilgi çalamamalıdır.
- Javascript kolayca bulunduğu sayfadan veri alabilir. Fakat başka site veya domainlerden veri almazsı sorunludur. Mümkün olmasına rağmen her iki taraflı onaya muhtaçtır. Tekrardan bunun nedeni de güvenlik limiteri diyeibliriz.

![](limitations.png)

Bu limitler tarayıcı dışında kullanıldığında ortadan kalkar. Örneğin sunucular daha geniş yetkilere sahiptirler.


## Javascript'i eşsiz yapan nedir ?

Javascript'i eşsiz yapan en az 3 neden vardır:

```compare
+ HTML/CSS ile tamamen entegre çalışması.
+ Kolay şeylerin kolayca yapılabilmesini sağlar.
+ Tüm önemli tarayıcılarda çalışır ve varsayılan olarak etkindir.
```

Bu üç özelliği barındıran Javascript haricinde hiç bir tarayıcı teknolojisi yoktur.

Javascriptin eşsiz olmasının nedeni budur. Bundan dolayı yapılan sitelerde en fazla kullanılan teknoloji Javascripttir.

Yeni bir teknolojiyi öğrenirken geleceğe dair öngörüsü önemlidir. Öyleyse yeni diller ve tarayıcı yetkinlikleri içeren bu trende ayak uydularmalıyız.

## Javascript'e üstün diller

Javascript'in sözdizimi ve yazımı herkese uymayabilir. Her yiğidin yoğurt yiyişi ayrıdır.

Bu beklenen birşey aslında, çünkü projeler ve gereksinimler kişiden kişiye göre değişir.

Bundan dolayı yakın zamanda bir sürü yeni *transpiled* yani çevirilmiş diller türemiştir. Bu dillerde yazdıktan sonra çalıştırılmadan Javascript'e çevriliyor. Modern araçlar bu çeviri işini çok hızlı bir şekilde yapmaktadır. Aslında doğrudan siz yazarken bile çevirme işini yapıp bu yeni dosyayı kullanılabilir hale getirirler.

Bu dillere örnek vermek gerekirse:

- [CofeeScript](http://coffeescript.org) Javascript için "şeker yazım" denebilecek bir dildir. Yazılımı daha kısadır ve daha temiz kod yazmaya yardımcı olur.

- [Typescript](http://www.typescriptlang.org/) statik veri yapıları ile Javascript yazılmasını sağlar. Karmaşık programlar geliştirmeyi kolaylaştırır. Microsoft tarafından geliştirilmiştir.
- [Dart](https://www.dartlang.org/) kendi başına ayrı bir dildir aslında. Browser üzerinde veya telefon uygulamalarında kendi motoru üzerinden çalışılır. Google tarafından Javascript'in yerine önerilmiş olmasına rağmen bu günlerde Javascript'e çeviri yapılarak kullanılmaktadır.

Aslında daha fazla örnek bulunabilir. Yukarıdaki bilseniz bile ne yaptığınızı tam olarak anlamak için Javascript bilmelisiniz.

## Özet
- Javascript başlangıçta sadece web tarayıcılarda kullanılmak üzere geliştirilmiş bir dildi. Fakat günümüzde bir çok çevrede çalışabilir durumda.
- Javascript şu anda HTML/CSS ile entegre olmasından ve geniş uyumluluğundan dolayı benzersizdir.
- Bir çok Javascripte çevirici dil bulunmaktadır. Javascript'i iyi bir şekilde öğrendikten sonra bu dillere de bir bakmanızı öneririm.
