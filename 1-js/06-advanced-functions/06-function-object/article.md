
# Fonksiyon Objeleri, NFE

Bildiğiniz gibi JavaScript'te fonksiyonlar deperdir.

Her değerin bir tipi olduğuna göre fonksiyonun tipi nedir?

JavaScript'te fonksiyon bir objedir.

Daha iyi görselleyebilmek adına fonksiyonlara "aksiyon objeleri" denebilir. Sadece çağırarak değil, obje olarak da davranabilirsiniz: özellik ekleme çıkarma, referans paslama vs.

## "name" özelliği

Fonksiyon objelerinin kullanışlı özellikleri bulunmaktadır.

Örneğin, fonksiyonun ismi "name" özelliği ile alınabilir.

```js run
function selamVer() {
  alert("Selam");
}

alert(selamVer.name); // selamVer
```
"name" özelliği atama o kadar akıllıdır ki, fonksiyon tanımlama ifadelerindeki ismi bile doğru alır.

```js run
let selamVer = function() {
  alert("Selam");
}

alert(selamVer.name); // selamVer 
```
Hatta atama varsayılan değer ile yapıldığında bile çalışır:

```js run
function f(selamVer = function() {}) {
  alert(selamVer.name); // selamVer (çalıştı!)
}

f();
```
Tanımda bu özelliğe "bağlamsal isim" denir. Eğer fonksiyonda böyle birşey yoksa, tanımlama bunu içerikten alır.

Object metodlarının da isimleri vardır:
```js run
let kullanici = {

  selamVer() {
    // ...
  },

  yolcuEt: function() {
    // ...
  }

}

alert(kullanici.selamVer.name); // Merhaba
alert(kullanici.yolcuEt.name); // Güle güle
```
Burada bir sihir yoktur. İsmin çıkarılamadığı birçok durum meydana gelebilir.

Böyle durumlarda aşağıdaki gibi boş dönerler:

```js
// Dizinin içerisinde fonksiyon yaratılması
let arr = [function() {}];

alert( arr[0].name ); // <boş>
// motorun doğru ismi bulmasına imkan yok bundna dolayı boş dönüyor.
```
Pratikte çoğu fonksiyonun ismi bulunmaktadır.

## "length" özelliği 
"length" adında ayrı bir özellik daha bulunmaktadır. Bu özellik fonksiyon parametrelerinin sayısını döndürür:

```js run
function f1(a) {}
function f2(a, b) {}
function many(a, b, ...more) {}

alert(f1.length); // 1
alert(f2.length); // 2
alert(many.length); // 2
```
Gördüğünüz gibi geriye kalan parametresi `...` sayılmamaktadır.

`length` özelliği bazen diğer fonksiyonların üzerinde çalışan fonksiyonlara bir iç bakış oluşturur.

Mesela, aşağıdaki kodda `sor`fonksiyonu `soru` parametresi alır ve belirli olmayan sayıda `isleyici` fonksiyonunu çağırır.

Kullanıcı cevap verdiğinde `isleyici`(handler) çağırılır. İki türlü işleyici gönderilebilir:

- Argümansız fonksiyon, sadece pozitif cevaplarda çağırılır.
- Argümanlı fonksiyonlar, cevap alınan her durumda çağırılır.

Mantık şu şekildedir; cevap pozisit olduğunda argüman almayan isleyici calisir, fakat evrensel isleyiciye de izin verir.

`isleyici`'lerin doğru çalışması için, `length` özelliğinden faydalanılabilir.

```js run
function sor(soru, ...isleyiciler) {
  let dogruMu = confirm(soru);

  for(let isleyici of isleyiciler) {
    if (isleyici.length == 0) {
      if (dogruMu) isleyici();
    } else {
      isleyici(dogruMu);
    }
  }

}

// Olumlu cevap için, her ikisi çalışırken
// Olumsuz cevap için sadece ikincisi çalışmaktadır.
sor("Soru?", () => alert('Evet dedin'), sonuc => alert(sonuc));
```
Bu duruma [polymorphism](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)) denilmektedir. Bu argümanlara tiplerine göre farklı davranma olayıdır. Bu bizim durumumuzda `length`'e bağlıdır. Bu fikir  JavaScript  kütüphanelerinde de kullanılmaktadır.

## Özelleştirilmiş özellikler

Kendi özelliğinizi eklemek de mümkündür.
Örneğin aşağıda `counter` özelliği ile toplan çağrı sayısı tutulmaktadır:

```js run
function selamVer() {
  alert("Selam");

  *!*
  // Kaç defa çağırıldığını tutar.
  sayHi.counter++;
  */!*
}
sayHi.counter = 0; // ilk değer

selamVer(); // Selam
selamVer(); // Selam

alert( `selamVer ${selamVer.counter} defa çağırılmıştır` ); // selamVer 2 defa çağırılmıştır.
```

```warn header="Özellik değişken değildir"
Fonksiyona atanan `selamVer.counter = 0` selamVer fonksiyonunun içerisinde `counter` değişkenini tanımlamaz. Diğer bir deyişle `counter` özelliği ile `let counter` birbirinden tamamen farklı şeylerdir.

Fonksiyona obje gibi davranıp özellik eklenebilir. Bu çalışmasında bir etki yaratmaz. Değişkenler fonksiyon özelliklerini kullanmaz, keza fonksiyon özellikleri de değişkenleri kullanmaz.
```

Fonksiyon özellikleri closure kullanılarak tekrardan yazılabilir. Örneğin yukarıdaki sayaç örneğini <info:closure> kullanarak tekrardan yazacak olursak:

```js run
function makeCounter() {
    // let count = 0 yazmak yerine

  function counter() {
    return counter.count++;
  };

  counter.count = 0;

  return counter;
}

let counter = makeCounter();
alert( counter() ); // 0
alert( counter() ); // 1
```
`count` artık fonksiyonun içerisinde bulunur, dış ortamda değil.

Closure kullanmak iyi mi kötü mü?


The main difference is that if the value of `count` lives in an outer variable, then an external code is unable to access it. Only nested functions may modify it. And if it's bound to function, then such thing is possible:

```js run
function makeCounter() {

  function counter() {
    return counter.count++;
  };

  counter.count = 0;

  return counter;
}

let counter = makeCounter();

*!*
counter.count = 10;
alert( counter() ); // 10
*/!*
```

So it depends on our aims which variant to choose.

## Named Function Expression

Named Function Expression or, shortly, NFE, is a term for Function Expressions that have a name.

For instance, let's take an ordinary Function Expression:

```js
let sayHi = function(who) {
  alert(`Hello, ${who}`);
};
```

...And add a name to it:

```js
let sayHi = function *!*func*/!*(who) {
  alert(`Hello, ${who}`);
};
```

Did we do anything sane here? What's the role of that additional `"func"` name?

First let's note, that we still have a Function Expression. Adding the name `"func"` after `function` did not make it a Function Declaration, because it is still created as a part of an assignment expression.

Adding such a name also did not break anything.

The function is still available as `sayHi()`:

```js run
let sayHi = function *!*func*/!*(who) {
  alert(`Hello, ${who}`);
};

sayHi("John"); // Hello, John
```

There are two special things about the name `func`:

1. It allows to reference the function from inside itself.
2. It is not visible outside of the function.

For instance, the function `sayHi` below re-calls itself with `"Guest"` if no `who` is provided:

```js run
let sayHi = function *!*func*/!*(who) {
  if (who) {
    alert(`Hello, ${who}`);
  } else {
*!*
    func("Guest"); // use func to re-call itself
*/!*
  }
};

sayHi(); // Hello, Guest

// But this won't work:
func(); // Error, func is not defined (not visible outside of the function)
```

Why do we use `func`? Maybe just use `sayHi` for the nested call?


Actually, in most cases we can:

```js
let sayHi = function(who) {
  if (who) {
    alert(`Hello, ${who}`);
  } else {
*!*
    sayHi("Guest");
*/!*
  }
};
```

The problem with that code is that the value of `sayHi` may change. The function may go to another variable, and the code will start to give errors:

```js run
let sayHi = function(who) {
  if (who) {
    alert(`Hello, ${who}`);
  } else {
*!*
    sayHi("Guest"); // Error: sayHi is not a function
*/!*
  }
};

let welcome = sayHi;
sayHi = null;

welcome(); // Error, the nested sayHi call doesn't work any more!
```

That happens because the function takes `sayHi` from its outer lexical environment. There's no local `sayHi`, so the outer variable is used. And at the moment of the call that outer `sayHi` is `null`.

The optional name which we can put into the Function Expression is exactly meant to solve this kind of problems.

Let's use it to fix the code:

```js run
let sayHi = function *!*func*/!*(who) {
  if (who) {
    alert(`Hello, ${who}`);
  } else {
*!*
    func("Guest"); // Now all fine
*/!*
  }
};

let welcome = sayHi;
sayHi = null;

welcome(); // Hello, Guest (nested call works)
```

Now it works, because the name `"func"` is function-local. It is not taken from outside (and not visible there). The specification guarantees that it always references the current function.

The outer code still has it's variable `sayHi` or `welcome` later. And `func` is an "internal function name", how it calls itself privately.

```smart header="There's no such thing for Function Declaration"
The "internal name" feature described here is only available for Function Expressions, not to Function Declarations. For Function Declarations, there's just no syntax possibility to add a one more "internal" name.

Sometimes, when we need a reliable internal name, it's the reason to rewrite a Function Declaration to Named Function Expression form.
```

## Summary

Functions are objects.

Here we covered their properties:

- `name` -- the function name. Exists not only when given in the function definition, but also for assignments and object properties.
- `length` -- the number of arguments in the function definition. Rest parameters are not counted.

If the function is declared as a Function Expression (not in the main code flow), and it carries the name, then it is called Named Function Expression. The name can be used inside to reference itself, for recursive calls or such.

Also, functions may carry additional properties. Many well-known JavaScript libraries make a great use of this feature.

They create a "main" function and attach many other "helper" functions to it. For instance, the [jquery](https://jquery.com) library creates a function named `$`. The [lodash](https://lodash.com) library creates a function `_`. And then adds `_.clone`, `_.keyBy` and other properties to (see the [docs](https://lodash.com/docs) when you want learn more about them). Actually, they do it to less pollute the global space, so that a single library gives only one global variable. That lowers the chance of possible naming conflicts.

So, a function can do a useful job by itself and also carry a bunch of other functionality in properties.
