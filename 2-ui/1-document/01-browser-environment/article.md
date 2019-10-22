# Tarayıcı Ortamı, Özellikleri

Javascript dili başlangıçta internet tarayıcıları için oluşturuldu. O zamandan beri geliştirildi ve bir çok kullanımı ve platformu ile bir dil haline geldi.


Bir platform tarayıcı veya bir web sunucusu veya bir çamaşır makinesi veya başka bir sunucu olabilir. Bunların her biri platforma özgü fonksiyonlar sağlar. Javascript özelliği bunu bir sunucu ortamı olarak adlandırılır.

Bir sunucu ortamı dil çekirdeğine ek olarak platforma özgü nesneler ve fonksiyonlar sağlar. İnternet tarayıcıları internet sayfalarını kontrol etmek için bir yol sunar. Node.js sunucu tarafı özellikleri vb. 



Here's a bird-eye view of what we have when JavaScript runs in a web-browser:
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

The `document` object gives access to the page content. We can change or create anything on the page using it.

For instance:
```js run
// change the background color to red
document.body.style.background = 'red';

// change it back after 1 second
setTimeout(() => document.body.style.background = '', 1000);
```

Here we used `document.body.style`, but there's much, much more. Properties and methods are described in the specification. By chance, there are two working groups who develop it:

1. [W3C](https://en.wikipedia.org/wiki/World_Wide_Web_Consortium) -- the documentation is at <https://www.w3.org/TR/dom>.
2. [WhatWG](https://en.wikipedia.org/wiki/WHATWG), publishing at <https://dom.spec.whatwg.org>.

As it happens, the two groups don't always agree, so we have like 2 sets of standards. But they are in the tight contact and eventually things merge. So the documentation that you can find on the given resources is very similar, there's like 99% match. There are very minor differences, you probably won't notice them.

Personally, I find <https://dom.spec.whatwg.org> more pleasant to use.

In the ancient past, there was no standard at all -- each browser implemented whatever it wanted. So different browsers had different sets methods and properties for the same thing, and developers had to write different code for each of them. Dark, messy times.

Even now we can sometimes meet old code that uses browser-specific properties and works around incompatibilities. But in this tutorial we'll use modern stuff: there's no need to learn old things until you really need those (chances are high you won't).

Then the DOM standard appeared, in an attempt to bring everyone to an agreement. The first version was "DOM Level 1", then it was extended by DOM Level 2, then DOM Level 3, and now it's DOM Level 4. People from WhatWG group got tired of version and are calling that just "DOM", without a number. So will do we.

```smart header="DOM is not only for browsers"
The DOM specification explains the structure of a document and provides objects to manipulate it. There are non-browser instruments that use it too.

For instance, server-side tools that download HTML pages and process them. They may support only a part of the DOM specification though.
```

```smart header="CSSOM for styling"
CSS rules and stylesheets are structured not like HTML. So there's a separate specification [CSSOM](https://www.w3.org/TR/cssom-1/) that explains how they are represented as objects, how to read and write them.

CSSOM is used together with DOM when we modify style rules for the document. In practice though, CSSOM is rarely required, because usually CSS rules are static. We rarely need to add/remove CSS rules from JavaScript, so we won't cover it right now.
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
