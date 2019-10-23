# Tarayıcı Ortamı, Özellikleri

Javascript dili başlangıçta internet tarayıcıları için oluşturuldu. O zamandan beri geliştirildi ve bir çok kullanımı ve platformu ile bir dil haline geldi.


Bir platform tarayıcı veya bir web sunucusu veya bir çamaşır makinesi veya başka bir sunucu olabilir. Bunların her biri platforma özgü fonksiyonlar sağlar. Javascript özelliği bunu bir sunucu ortamı olarak adlandırılır.

Bir sunucu ortamı dil çekirdeğine ek olarak platforma özgü nesneler ve fonksiyonlar sağlar. İnternet tarayıcıları internet sayfalarını kontrol etmek için bir yol sunar. Node.js sunucu tarafı özellikleri vb. 


İşte javascriptin internet tarayıcısında çalıştığında elimizde ne olduğunu gösteren bir kuş bakışı.

![](windowObjects.png)

`window` denilen bir "kök" nesnesi var. İki rolü vardır.

1. Birincisi, Javascript kodu için evrensel bir nesnedir. bölümde açıklandığı gibi [Evrensel nesneler](https://github.com/sahinyanlik/javascript-tutorial-tr/blob/master/1-js/06-advanced-functions/05-global-object/article.md)
2. İkincisi, "tarayıcı penceresini" temsil eder ve kontrol etmek için yöntemler sağlar. 

Örneğin, burada `window`u evrensel bir nesne olarak kullandık.

```js run
function selamSoyle() {
  alert("Selam");
}

// evrensel değişkenler `window` özellikleri olarak erişilebilir.
window.selamSoyle();
```

Ve burada `window`u pencerenin yüksekliğini görmek için tarayıcı penceresi olarak kullandık: 

```js run
alert(window.innerHeight); // İç pencere yüksekliği
```

Daha fazla `window`a özgü yöntemler ve özellikler var, bunlardan daha sonra bahsedeceğiz.

## Document Object Model (DOM)

`document` nesnesi sayfa içeriğine erişimi sağlar. Sayfada herhangi bir şeyi değiştirebilir ya da oluşturabiliriz.

Örneğin:
```js run
// arka plan rengini kırmızı olarak değiştirelim.
document.body.style.background = 'red';

// 1 saniye sonra tekrar değiştirelim
setTimeout(() => document.body.style.background = '', 1000);
```

Burada `document.body.style` kullandık but daha fazla parametreler var. Özellikleri ve yöntemleri tanımlamada açıklanmıştır. Tesadüf ki, bunu geliştiren iki grup vardır. 

1. [W3C](https://en.wikipedia.org/wiki/World_Wide_Web_Consortium) -- belgesi <https://www.w3.org/TR/dom> linktedir.
2. [WhatWG](https://en.wikipedia.org/wiki/WHATWG), <https://dom.spec.whatwg.org> 'da yayınlanır..

Burada olduğu gibi, 2 grup her zaman aynı fikirde değil. Bu yüzden 2 standartımız var fakat birbirileri ile temasta ve sonuç olarak bir noktada birleşiyorlar. Yani bu kaynaklardan bulabileceğiniz bilgiler birbirlerine çok yakın, 99% gibi bir eşleşme var. Farklılıklar var ama büyük ihtimal bunu fark etmeyeceksiniz.

Şahsen <https://dom.spec.whatwg.org> kullanmayı daha keyifli buluyorum.

Eskiden hiç bir standart yoktu. -- her tarayıcı her ne istiyorsa onu uyguladı. Bu yüzden farklı tarayıcıların aynı şeyler için farklı metotları ve özellikleri vardı ve geliştiriciler her bir tarayıcı için farklı kodlar yazmak zorunda kalıyordu. Karanlık ve dağaınık zamanlar.

Şimdi bile bazen tarayıcıları özgü özellikleri kullanan ve uyumsuzluklar etrafında çalışan eski kodlarla çalışabiliriz ama bu derste modern şeyler kullanacağız: Onlara ihtiyacın olana kadar eski şeyler öğrenmeye gerek yok (şansın yüksek değil). 

Daha sonra herkesi ortak noktada toplamak için DOM standartı belirlendi. İlk versiyon "DOM Level 1" idi, sonra DOM Level 2 tarafından genişletildi, sonra DOM Level 3 ve şimdi DOM Level 4. WhatWG grubundan insanlar sürümden sıkıldılar ve numara olmadan sadece DOM olarak adlandırdılar. Öyleyse biz yapacağız.

```smart header="DOM yalnızca tarayıcı için değildir."
DOM özelliği bir belgenin yapısını açıklar ve onu işlemek için nesne sağlar. Onu kullanan tarayıcı olmayan araçlarda var.

Örneğin, HTML sayfalarını indiren ve işleyen sunucu-taraflı araçlar. Ancak DOM spesifikasyonunun sadece bir bölümü destekleyebilir.
```

```smart header="Stil için CSSOM"
CSS kuralları ve stil sayfaları HTML yapısına benzemez. Bu yüzden nesneler olarak nasıl temsil edildiklerini ve nasıl okunup yazılacağını açıklayan bir tanımlama vardır. [CSSOM](https://www.w3.org/TR/cssom-1/)

CSSOM, belgi için stil kurallarını değiştirdiğimizde DOM ile birlikte kullanılıyor. Pratikte olsa CSSOM nadiren gereklidir. Çünkü genelde CSS kuralları statiktir. Javascript'e CSS kuralları ekleme/çıkarma nadiren ihtiyacımız var. Bu yüzden onu kapatmayız.
```

## BOM (part of HTML spec)

Browser Object Model (BOM) are additional objects provided by the browser (host environment) to work with everything except the document.

For instance:

- [navigator](mdn:api/Window/navigator) object provides background information about the browser and the operation system. There are many properties, but two most widely known are: `navigator.userAgent` -- about the current browser, and `navigator.platform` -- about the platform (can help to differ between Windows/Linux/Mac etc).
- [location](mdn:api/Window/location) object allows to read the current URL and redirect the browser to a new one.

Here's how we can use the `location` object:

```js run
alert(location.href); // shows current URL
if (confirm("Go to wikipedia?")) {
  location.href = 'https://wikipedia.org'; // redirect the browser to another URL
}
```

Functions `alert/confirm/prompt` are also a part of BOM: they are directly not related to the document, but represent pure browser methods of communicating with the user.


```smart header="HTML specification"
BOM is the part of the general [HTML specification](https://html.spec.whatwg.org).

Yes, you heard that right. The HTML spec at <https://html.spec.whatwg.org> is not only about the "HTML language" (tags, attributes), but also covers a bunch of objects, methods and browser-specific DOM extensions. That's "HTML in broad terms".
```

## Summary

Talking about standards, we have:

DOM specification
: Describes the document structure, manipulations and events, see <https://dom.spec.whatwg.org>.

CSSOM specification
: Describes stylesheets and style rules, manipulations with them and their binding to documents, see <https://www.w3.org/TR/cssom-1/>.

HTML specification
: Describes HTML language (tags etc) and also BOM (browser object model) -- various browser functions: `setTimeout`, `alert`, `location` and so on, see <https://html.spec.whatwg.org>. It takes DOM specification and extends it with many additional properties and methods.

Now we'll get down to learning DOM, because the document plays the central role in the UI, and working with it is the most complex part.

Please note the links above, because there's so many stuff to learn, it's impossible to cover and remember everything.

When you'd like to read about a property or a method -- the Mozilla manual at <https://developer.mozilla.org/en-US/search> is a nice one, but reading the corresponding spec may be better: more complex and longer to read, but will make your fundamental knowledge sound and complete.
