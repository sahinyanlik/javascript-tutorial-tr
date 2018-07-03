
# "new Function" yazımı

Çok az kullanılsa da bir çeşit daha fonksiyon yaratma şekli vardır. Çok az kullanılsa da bazen alternatifsizdirler.

[cut]

## Yazım

Fonksiyon yaratmak için:

```js
let func = new Function('a', 'b', 'return a + b');
```

`new Function`'ın tüm argümanları karakter dizisidir. Parametreler önce, en son olarak yazılır.

Örneğin:

```js run
let sum = new Function('arg1', 'arg2', 'return arg1 + arg2');

alert( sum(1, 2) ); // 3
```

Eğer argüman yok ise, sadece gövde ile fonksiyon yaratılır:

```js run
let selamVer = new Function('alert("Selam")');

selamVer(); // Selam
```

Diğer yöntemlere göre en büyük farklılık -- fonksiyon gerçektende sadece karakter dizisinden oluşuyor, bu çalışma anında gerçekleşiyor.

Diğer tüm tanımlamalar programcıların kod yazmasını gerektirir.

Fakat `new Function` herhangi bir metini fonksiyona çevirebilir. Örneğin sunucudan metin olarak bir fonksiyon alıp bunu çalıştırmak mümkündür.

```js
let str = ... Serverdan dinamik olarak gelen metin...

let func = new Function(str);
func();
```

Tabi bunlar çok özel haller, örneğin sunucudan bir metini alıp çalıştırmak, veya temadan dinamik olarak derleme. Bunun gibi ihtiyaçlar genelde geliştirmenin ileriki safhalarında karşılaşılır.

## Closure
Fonksiyon genelde doğduğu yeri hatırlar `[[Ortam]]`. Bulunduğu Sözcüksel Ortama yaratıldığı yerden referans verir.

Bir fonksiyon `new Function` ile yaratıldığında `[[Ortam]]` referansı o anki bulunduğu ortamı değil de evrensel ortama referans verir.

```js run

function FonkAl() {
  let deger = "test";

*!*
  let func = new Function('alert(deger)');
*/!*

  return func;
}

FonkAl()(); // hata: deger tanımlı değildir.
```

Normal davranış şu şekildedir:

```js run 
function FonkAl() {
  let deger = "test";

*!*
  let func = function() { alert(deger); };
*/!*

  return func;
}

getFunc()(); // *!*"test"*/!*, FonkAl'ın sözcüksel ortamından.
```

This special feature of `new Function` looks strange, but appears very useful in practice.

Imagine that we really have to create a function from the string. The code of that function is not known at the time of writing the script (that's why we don't use regular functions), but will be known in the process of execution. We may receive it from the server or from another source.

Our new function needs to interact with the main script.

Maybe we want it to be able to access outer local variables?

But the problem is that before JavaScript is published to production, it's compressed using a *minifier* -- a special program that shrinks code by removing extra comments, spaces and -- what's important, renames local variables into shorter ones.

For instance, if a function has `let userName`, minifier replaces it `let a` (or another letter if this one is occupied), and does it everywhere. That's usually a safe thing to do, because the variable is local, nothing outside the function can access it. And inside the function minifier replaces every mention of it. Minifiers are smart, they analyze the code structure, not just find-and-replace, so that's ok.

...But if `new Function` could access outer variables, then it would be unable to find `userName`.

**Even if we could access outer lexical environment in `new Function`, we would have problems with minifiers.**

The "special feature" of `new Function` saves us from mistakes.

And it enforces better code. If we need to pass something to a function, created by `new Function`, we should pass it explicitly as arguments.

The "sum" function actually does that right:

```js run 
*!*
let sum = new Function('a', 'b', ' return a + b; ');
*/!*

let a = 1, b = 2;

*!*
// outer values are passed as arguments
alert( sum(a, b) ); // 3
*/!*
```

## Summary

The syntax:

```js
let func = new Function(arg1, arg2, ..., body);
```

For historical reasons, arguments can also be given as a comma-separated list. 

These three mean the same:

```js 
new Function('a', 'b', ' return a + b; '); // basic syntax
new Function('a,b', ' return a + b; '); // comma-separated
new Function('a , b', ' return a + b; '); // comma-separated with spaces
```

Functions created with `new Function`, have `[[Environment]]` referencing the global Lexical Environment, not the outer one. Hence, they can not use outer variables. But that's actually good, because it saves us from errors. Explicit parameters passing is a much better thing architecturally and has no problems with minifiers.