# An Introduction to JavaScript
# Javascript'e Giriş

Bakalım Javascript'in özelliği ne, ne yapılır ve hangi teknolojilerle birlikte çalışır.

## Javascript  Nedir?

*JavaScript* aslen *"web sayfalarına canlılık"* getirmek için oluşturulmuştur.

Bu dilde yapılan programlara *scripts* denir. Doğrudan HTML içerisine yazılıp sayfa yüklendiğinde doğrudan çalışabilir.

Komutlar hergi bir derleme ve hazırlığa ihtiyaç duymadan doğrudan çalışırlar.

Bu yönden bakınca Javascript diğer dillere nazaran oldukça farklıdır.Bkz: [Java](http://en.wikipedia.org/wiki/Java).


```smart header="Neden <u>Java</u>Script?"
Javascript ilk yazıldığında, başka bir adı vardı: "LiveScript". Fakat Java dili o dönemlerde çok meşur olduğundan dolayı yeni bir dil ve "küçük kardeş" gibi görünmesi açısından Javascript olarak değiştirildi edildi.

Fakat Javascript gelişerek kendince şartnameleri [ECMAScript](http://en.wikipedia.org/wiki/ECMAScript) olan bağımsız bir dil haline geldi. Şu anda Java ile hiç bir ilgisi bulunmamaktadır.
```
Şu anda Javascript sadece web tarayıcıda değil sunucuda veya 
[ JavaScript motoru](https://en.wikipedia.org/wiki/JavaScript_engine) olan her yerde çalışmaktadır.

Tarayıcılar bu Javascript motoru gömülü bir şekilde gelirler. Bu ayrıca "Javascript sanal makinesi" olarak da adlandırılır.

Bu Javascript motorlarından bazıları şunlardır;

- [V8](https://en.wikipedia.org/wiki/V8_(JavaScript_engine)) --  Chrome ve Opera.
- [SpiderMonkey](https://en.wikipedia.org/wiki/SpiderMonkey) --  Firefox.
- Internet Explorer'ın "Trident", "Chakra" kod isimli motorlarının yanında Microsoft Edge için "ChakraCore" adında ayrı bir motoru bulunmaktadır. Safari ise "Nitro","SquirrelFish" gibi kod adlarına sahip Javascript motoru kullanmaktadır.

Yukarıdaki terimleri ezberlerseniz iyi olur, çünkü ileride şöyle bir cümle gelebilir  "X özelliği V8 tarafından desteklenmektedir". Bu özelliğin Chrome ve Opera tarafından desteklendiğini anlamanız gerekir.

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

## What CAN'T in-browser JavaScript do?

JavaScript's abilities in the browser are limited for the sake of the user's safety. The aim is to prevent an evil webpage from accessing private information or harming the user's data.

The examples of such restrictions are:

- JavaScript on a webpage may not read/write arbitrary files on the hard disk, copy them or execute programs. It has no direct access to OS system functions.

    Modern browsers allow it to work with files, but the access is limited and only provided if the user does certain actions, like "dropping" a file into a browser window or selecting it via an `<input>` tag.

    There are ways to interact with camera/microphone and other devices, but they require a user's explicit permission. So a JavaScript-enabled page may not sneakily enable a web-camera, observe the surroundings and send the information to the [NSA](https://en.wikipedia.org/wiki/National_Security_Agency).
- Different tabs/windows generally do not know about each other. Sometimes they do, for example when one window uses JavaScript to open the other one. But even in this case, JavaScript from one page may not access the other if they come from different sites (from a different domain, protocol or port).

    This is called the "Same Origin Policy". To work around that, *both pages* must contain a special JavaScript code that handles data exchange.

    The limitation is again for user's safety. A page from `http://anysite.com` which a user has opened must not be able to access another browser tab with the URL `http://gmail.com` and steal information from there.
- JavaScript can easily communicate over the net to the server where the current page came from. But its ability to receive data from other sites/domains is crippled. Though possible, it requires explicit agreement (expressed in HTTP headers) from the remote side. Once again, that's safety limitations.

![](limitations.png)

Such limits do not exist if JavaScript is used outside of the browser, for example on a server. Modern browsers also allow installing plugin/extensions which may get extended permissions.

## What makes JavaScript unique?

There are at least *three* great things about JavaScript:

```compare
+ Full integration with HTML/CSS.
+ Simple things done simply.
+ Supported by all major browsers and enabled by default.
```

Combined, these three things exist only in JavaScript and no other browser technology.

That's what makes JavaScript unique. That's why it's the most widespread tool to create browser interfaces.

While planning to learn a new technology, it's beneficial to check its perspectives. So let's move on to the modern trends that include new languages and browser abilities.


## Languages "over" JavaScript

The syntax of JavaScript does not suit everyone's needs. Different people want different features.

That's to be expected, because projects and requirements are different for everyone.

So recently a plethora of new languages appeared, which are *transpiled* (converted) to JavaScript before they run in the browser.

Modern tools make the transpilation very fast and transparent, actually allowing developers to code in another language and autoconverting it "under the hood".

Examples of such languages:

- [CoffeeScript](http://coffeescript.org/) is a "syntax sugar" for JavaScript, it introduces shorter syntax, allowing to write more precise and clear code. Usually Ruby devs like it.
- [TypeScript](http://www.typescriptlang.org/) is concentrated on adding "strict data typing", to simplify development and support of complex systems. It is developed by Microsoft.
- [Dart](https://www.dartlang.org/) is a standalone language that has its own engine that runs in non-browser environments (like mobile apps). It was initially offered by Google as a replacement for JavaScript, but as of now, browsers require it to be transpiled to JavaScript just like the ones above.

There are more. Of course even if we use one of those languages, we should also know JavaScript, to really understand what we're doing.

## Summary

- JavaScript was initially created as a browser-only language, but now it is used in many other environments as well.
- At this moment, JavaScript has a unique position as the most widely-adopted browser language with full integration with HTML/CSS.
- There are many languages that get "transpiled" to JavaScript and provide certain features. It is recommended to take a look at them, at least briefly, after mastering JavaScript.
