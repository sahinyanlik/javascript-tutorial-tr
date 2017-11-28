# Fonksiyonlar

Çoğu zaman kod yazarken belirli bölümleri tekrarlama ihtiyacı duyulur.

Örneğin kullanıcı hesabına giriş yaptığında veya çıktığında güzel görünümlü bir mesaj göstermek istenebilir.

Fonksiyonlar programın "yapı taşıdır". Bir çok defa bu fonksiyonlar çağırılarak tekrardan bu kodları yazmaktan kurtulunur.

[cut]

Aslında var olan `alert(mesaj)`, `prompt(mesaj,varsayilan)` ve `confirm(soru)` gibi fonksiyonları gördük. Fakat artık bunları yazmanın zamanı geldi.

## Fonksiyon Tanımlama

Fonksiyon tanımlamak için *function tanım* kullanılır.

Aşağıdaki gibi görünür:
```js
function mesajGoster() {
  alert( 'Merhaba millet!' );
}
```

`function` kelimesi önce yazılır, ardından *fonksiyonun adı* ve sonra parametlerin yazılacağı parantez açılır ve ihtiyaç duyulan parametreler yazılır, sonrasında ise kapatılıp süslü parantez ile *fonksiyon gövdesi*ne başlanır.

![](function_basics.png)

Yeni fonksyion ismiyle şu şekilde çağırılır: `mesaGoster()`.

Örneğin:

```js run
function mesajGoster() {
  alert( 'Merhaba millet' );
}

*!*
mesajGoster();
mesajGoster();
*/!*
```

`mesajGoster()` fonksiyonu kodu çalıştırır. Bu kod sonrasında `Merhaba millet` uyarsını iki defa göreceksiniz.

Bu örnek açıkça fonksiyonların ana amacını gösteriyor: Kod tekrarından kaçınma.

Eğer mesajı değiştirmek istersek bir yerde değiştirdiğimizde bu fonksiyonu kullanan her yerde değişiklik olacaktır.

## Yerel değişkenler

Fonksiyon içinde tanımlanan değişkene sadece o fonksiyon içerisinde erişilebilir.

Örneğin:

```js run
function mesajGoster() {
*!*
  let mesaj = "Merhaba! Ben Javascript"; // Yerel Değişken
*/!*

  alert( mesaj );
}

mesajGoster(); // Merhaba! Ben Javascript

alert( mesaj ); // <-- Hata! Bu değişken `mesajGoster` fonksiyonuna aittir.
```

## Dış Değişkenler

Fonksiyon, kendi dışında oluşturulmuş değişkenlere erişebilir. Örneğin:

```js run no-beautify
let *!*kullaniciAdi*/!* = 'Adem';

function mesajGoster() {
  let mesaj = 'Hello, ' + *!*kullaniciAdi*/!*;
  alert(mesaj);
}

mesajGoster(); // Merhaba, Adem
```

Fonksiyon dışarıda bulunan değişkenlere tam kontrol sağlar. Hatta modifiye edebilir.

Örneğin:

```js run
let *!*kullaniciAdi*/!* = 'Adem';

function mesajGoster() {
  *!*kullaniciAdi*/!* = "Yusuf"; // (1) dışarıda bulunan değişkenin değeri değişti.

  let mesaj = 'Merhaba, ' + *!*userName*/!*;
  alert(mesaj);
}

alert( mesaj ); // Fonksiyon çağırılmadan  *!*Adem*/!* 

mesajGoster();

alert( kullaniciAdi ); // fonksiyon çağırıldıktan sonra *!*Yusuf*/!*, 
```

Dışarıda bulunan değişkenler eğer yerel değişken yoksa kullanılırlar. Bazen eğer `let` ile değişken oluşturulmazsa karışıklık olabilir.

Eğer aynı isim ile fonksiyon içerisinde bir değişken oluşturulmuş ise, fonksiyon içerisindeki değişkenin değeri düzenlenebilir.  Örneğin aşağıda yerel bir değişken dıştaki değişken ile aynı isimdedir. Dolayısıyla yerel değişken düzenlenecektir. Dıştaki değişken bundan etkilenmeyecektir.

```js run
let kullaniciAdi = 'Adem';

function mesajGoster() {
*!*
  let kullaniciAdi = "Yusuf"; // yerel değişken tanımla
*/!*

  let mesaj = 'Merhaba, ' + kullaniciAdi; // *!*Yusuf*/!*
  alert(message);
}

// buradaki fonksiyon kendi değişkenini yaratacak ve onu kullanacak.
mesajGoster();

alert( userName ); // *!*Adem*/!*, değişmedi!!!, fonksiyon dışarıda bulunan değişkene erişmedi.
```

```smart header="Evrensel(Global) Değişkenler"

Fonksiyonların dışına yazılan her değişken, yukarıda bulunan `kullaniciAdi` gibi, *evrensel* veya  *global* değişken olarak adlandırılırlar.

Global değişkenlere her fonksiyon içerisinden erişilebilir.(Yerel değişkenler tarafından aynı isimle bir değişken tanımlanmamışsa)

Genelde fonksiyonlar yapacakları işe ait tüm değişkenleri tanımlarlara, global değişkenler ise sadece proje seviyesinde bilgi tutarlar, çünkü proje seviyesinde bilgilerin projenin her yerinden erişilebilir olması oldukça önemlidir. Modern kodda az veya hiç global değer olmaz. Çoğu fonksiyona ait değişkenlerdir.

```

## Parametreler
Paramterelere isteğe bağlı olarak veri paslanabilir. Bunlara *fonksiyon argümanları* da denir.

Aşağıdaki fonksiyon iki tane parametreye sahiptir. `denBeri` ve `metin`

```js run
function mesajGoster(*!*gonderen, metin*/!*) { // argümanlar: gonderen, metin
  alert(gonderen + ': ' + metin);
}

*!*
mesajGoster('Ahmet', 'Merhaba!'); // Ahmet: Merhaba! (*)
mesajGoster('Mehmet', "Naber?"); // Mehmet: Naber? (**)
*/!*
```

Eğer fonksiyonlar `(*)` ve `(**)` deki gibi yazılırsa doğrudan fonksiyonda `gonderen` ve `metin` yerel değişkenlerine atanırlar. Sonrasında fonksiyon bunları kullanabilir.

Aşağıda `gonderen` değişkeni fonksiyona paslanmakta. Dikkat ederseniz fonksiyon içerisinde `from` değişse bile bu dışarıda bulunan değişkeni etkilememekte. Çünkü fonksiyon bu değişkenin her zaman kopyasını kullanır:


```js run
function mesajGoster(gonderen,metin) {

*!*
  gonderen = '*' + gonderen + '*'; // "gonderen" biraz daha güzel hale getirildi.
*/!*

  alert( gonderen + ': ' + metin );
}

let gonderen = "Mahmut";

mesajGoster(gonderen, "Merhaba"); // *Mahmut*: Merhaba

// "from" değişkeninin değeri sadece fonksiyon içerisinde değişti. Çünkü bu değişken paslandığında fonksiyon yerel bir kopyası üzerinde çalışır
alert( gonderen ); // Mahmut
```

## Varsayılan Değerler

Eğer fonksiyon argümanına bir değer gönderilmemişse fonksiyon içerisinde bu değişken `undefined` olur.

Örneğin `mesajGoster(gonderen,metin)` fonksiyonu tek argüman ile de çağırılabilir.

```js
mesajGoster("Mahmut");
```
Bu bir hata değildir. Fonksiyon eğer bu şekilde çağırılırsa, yani `metin` yoksa, `metin == undefined` varsayılır. Yukarıdaki fonksiyon çağırıldığında sonuç "Mahmut: undefined" olacaktır.

Eğer "varsayılan" olarak `metin` değeri atamak istiyorsanız, `=` işareti ile tanımlamanız gerekmekte.

```js run
function mesajGoster(gonderen, *!*metin = "metin gönderilmedi"*/!*) {
  alert(gonderen + ": " + metin );
}

mesajGoster("Mahmut"); // Mahmut: metin gönderilmedi
```
Eğer `metin` değeri paslanmazsa, `"metin gönderilmedi"` çıktısı alınır.

`"metin gönderilmedi"` şu anda karakter dizisidir. Fakat daha karmaşık yapılar olabilir. Sadece parametre gönderilmez ise bu değer atanır. Aşağıdaki kullanım da pekala doğrudur.

```js run
function mesajGoster(gonderen, metin = digerFonksiyon()) {
  // eğer metin gönderilmez ise digerFonksiyon çalışır ve sonucu "metin" değişkenine atanır.
}
```


````smart header="Eski tip varsayılan parametreler"
Eski tip JavaScript varsayılan parametreleri desteklememekteydi. Bundan dolayı farklı yöntemler geliştirdi. Eğer eskiden yazılmış kodları okursanız bu kodlara rastlayabilirsiniz.

Örneğin doğrudan  `undefined` kontrolü:
```js
function mesajGoster(gonderen, metin) {
*!*
  if (metin === undefined) {
    text = 'metin gönderilmedi';
  }
*/!*

  alert( gonderen + ": " + metin );
}
```
...Veya `||` operatörü:


```js
function mesajGoster(gonderen, metin) {
  // eğer metin yanlış değer ise( bu durumda undefined yanlış değerdir hatırlarsanız ) 'metin gönderilmedi' ata.
  text = text || 'metin gönderilmedi';
  ...
}
```


````


## Returning a value

A function can return a value back into the calling code as the result.

The simplest example would be a function that sums two values:

```js run no-beautify
function sum(a, b) {
  *!*return*/!* a + b;
}

let result = sum(1, 2);
alert( result ); // 3
```

The directive `return` can be in any place of the function. When the execution reaches it, the function stops, and the value is returned to the calling code (assigned to `result` above).

There may be many occurrences of `return` in a single function. For instance:

```js run
function checkAge(age) {
  if (age > 18) {
*!*
    return true;
*/!*
  } else {
*!*
    return confirm('Got a permission from the parents?');
*/!*
  }
}

let age = prompt('How old are you?', 18);

if ( checkAge(age) ) {
  alert( 'Access granted' );
} else {
  alert( 'Access denied' );
}
```

It is possible to use `return` without a value. That causes the function to exit immediately.

For example:

```js
function showMovie(age) {
  if ( !checkAge(age) ) {
*!*
    return;
*/!*
  }

  alert( "Showing you the movie" ); // (*)
  // ...
}
```

In the code above, if `checkAge(age)` returns `false`, then `showMovie` won't proceed to the `alert`.

````smart header="A function with an empty `return` or without it returns `undefined`"
If a function does not return a value, it is the same as if it returns `undefined`:

```js run
function doNothing() { /* empty */ }

alert( doNothing() === undefined ); // true
```

An empty `return` is also the same as `return undefined`:

```js run
function doNothing() {
  return;
}

alert( doNothing() === undefined ); // true
```
````

````warn header="Never add a newline between `return` and the value"
For a long expression in `return`, it might be tempting to put it on a separate line, like this:

```js
return
 (some + long + expression + or + whatever * f(a) + f(b))
```
That doesn't work, because JavaScript assumes a semicolon after `return`. That'll work the same as:

```js
return*!*;*/!*
 (some + long + expression + or + whatever * f(a) + f(b))
```
So, it effectively becomes an empty return. We should put the value on the same line instead.
````

## Naming a function [#function-naming]

Functions are actions. So their name is usually a verb. It should briefly, but as accurately as possible describe what the function does. So that a person who reads the code gets the right clue.

It is a widespread practice to start a function with a verbal prefix which vaguely describes the action. There must be an agreement within the team on the meaning of the prefixes.

For instance, functions that start with `"show"` usually show something.

Function starting with...

- `"get…"` -- return a value,
- `"calc…"` -- calculate something,
- `"create…"` -- create something,
- `"check…"` -- check something and return a boolean, etc.

Examples of such names:

```js no-beautify
mesajGoster(..)     // shows a message
getAge(..)          // returns the age (gets it somehow)
calcSum(..)         // calculates a sum and returns the result
createForm(..)      // creates a form (and usually returns it)
checkPermission(..) // checks a permission, returns true/false
```

With prefixes in place, a glance at a function name gives an understanding what kind of work it does and what kind of value it returns.

```smart header="One function -- one action"
A function should do exactly what is suggested by its name, no more.

Two independent actions usually deserve two functions, even if they are usually called together (in that case we can make a 3rd function that calls those two).

A few examples of breaking this rule:

- `getAge` -- would be bad if it shows an `alert` with the age (should only get).
- `createForm` -- would be bad if it modifies the document, adding a form to it (should only create it and return).
- `checkPermission` -- would be bad if displays the `access granted/denied` message (should only perform the check and return the result).

These examples assume common meanings of prefixes. What they mean for you is determined by you and your team. Maybe it's pretty normal for your code to behave differently. But you should have a firm understanding of what a prefix means, what a prefixed function can and cannot do. All same-prefixed functions should obey the rules. And the team should share the knowledge.
```

```smart header="Ultrashort function names"
Functions that are used *very often* sometimes have ultrashort names.

For example, [jQuery](http://jquery.com) framework defines a function `$`, [LoDash](http://lodash.com/) library has it's core function named `_`.

These are exceptions. Generally functions names should be concise, but descriptive.
```

## Functions == Comments

Functions should be short and do exactly one thing. If that thing is big, maybe it's worth it to split the function into a few smaller functions. Sometimes following this rule may not be that easy, but it's definitely a good thing.

A separate function is not only easier to test and debug -- its very existence is a great comment!

For instance, compare the two functions `showPrimes(n)` below. Each one outputs [prime numbers](https://en.wikipedia.org/wiki/Prime_number) up to `n`.

The first variant uses a label:

```js
function showPrimes(n) {
  nextPrime: for (let i = 2; i < n; i++) {

    for (let j = 2; j < i; j++) {
      if (i % j == 0) continue nextPrime;
    }

    alert( i ); // a prime
  }
}
```

The second variant uses an additional function `isPrime(n)` to test for primality:

```js
function showPrimes(n) {

  for (let i = 2; i < n; i++) {
    *!*if (!isPrime(i)) continue;*/!*

    alert(i);  // a prime
  }
}

function isPrime(n) {
  for (let i = 2; i < n; i++) {
    if ( n % i == 0) return false;
  }
  return true;
}
```

The second variant is easier to understand isn't it? Instead of the code piece we see a name of the action (`isPrime`). Sometimes people refer to such code as *self-describing*.

So, functions can be created even if we don't intend to reuse them. They structure the code and make it readable.

## Summary

A function declaration looks like this:

```js
function name(parameters, delimited, by, comma) {
  /* code */
}
```

- Values passed to a function as parameters are copied to its local variables.
- A function may access outer variables. But it works only from inside out. The code outside of the function doesn't see its local variables.
- A function can return a value. If it doesn't then its result is `undefined`.

To make the code clean and easy to understand, it's recommended to use mainly local variables and parameters in the function, not outer variables.

It is always easier to understand a function which gets parameters, works with them and returns a result than a function which gets no parameters, but modifies outer variables as a side-effect.

Function naming:

- A name should clearly describe what the function does. When we see a function call in the code, a good name instantly gives us an understanding what it does and returns.
- A function is an action, so function names are usually verbal.
- There exist many well-known function prefixes like `create…`, `show…`, `get…`, `check…` and so on. Use them to hint what a function does.

Functions are the main building blocks of scripts. Now we've covered the basics, so we actually can start creating and using them. But that's only the beginning of the path. We are going to return to them many times, going more deeply into their advanced features.
