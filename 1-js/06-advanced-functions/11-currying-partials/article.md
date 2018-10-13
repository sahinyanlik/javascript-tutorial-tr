libs:
  - lodash

---

# Tımarlama ve kısmi fonksiyonlar

Şimdiye kadar fonksiyon bağlar iken sadece `this` hakkında konuşmuştuk. Bunu bir adım ileri götürme vakti geldi.

Aslında sadece `this` değil argümanları da bağlamak mümkün. Çok nadir yapılan bir teknik fakat bilmekte fayda var.

[cut]

`bind`'ın yazımı:

```js
let bound = func.bind(context, arg1, arg2, ...);
```

Bu kaynağı `this` olarak bağlamaya ve ardından argümanları tanımlaya olanak verir.

Örneğin çarpma fonksiyonu `mul(a,b)`:

```js
function mul(a, b) {
  return a * b;
}
```

Bunun iki katını almak için `double` fonksiyonunu şu şekilde bağlayarak yaratabiliriz:

```js run
*!*
let double = mul.bind(null, 2);
*/!*

alert( double(3) ); // = mul(2, 3) = 6
alert( double(4) ); // = mul(2, 4) = 8
alert( double(5) ); // = mul(2, 5) = 10
```

`mul.bind(null,2)` ile `double` fonksiyonu yaratılır. Bu fonksiyon `mul`'a kaynağı `null` yaparak fakat ilk argüman 2 olacak şekilde iletilir. Bundan sonraki argümanlar da "olduğu gibi" iletilir.

Bu olaya [kısmi fonksiyon uygulaması](https://en.wikipedia.org/wiki/Partial_application) denir -- var olan fonksiyonun parametrelerini değiştirerek yeni bir fonksiyon yaratma olayı.

Dikkat ederseniz aslında biz burada `this`'i hiç kullanmıyoruz. Fakat `bind`'ın buna ihtiyacı var bundan dolayı `null` gibi bir değer koymak zorundayız.

Üç ile çarpma olayını ( `triple` ) ise şu şekilde yazabiliriz 

```js run
*!*
let triple = mul.bind(null, 3);
*/!*

alert( triple(3) ); // = mul(3, 3) = 9
alert( triple(4) ); // = mul(3, 4) = 12
alert( triple(5) ); // = mul(3, 5) = 15
```

Neden kısmi fonksiyon kullanıyoruz? 

Burada amaç var olan fonksiyon üzerinden okunabilir bağımsız bir fonksiyon yaratmaktır ( `double`, `triple`) Böylece bunu kullanabilir ve her defasında ilk argümanı yazmak zorunda kalmayız çünkü `bind` ile bu sabitlenmiş olur.

Diğer bir durumda kısmı uygulamalar jenerik fonksiyon yaratmada oldukça yararlıdır, ayrıca daha genel fonksiyondan özele doğru inmeye yarar. Kullanışlılık böylece artar.

Örneğin, `send(from, to, text)` adında bir fonksiyonumuz olsun. `user` objesinin içerisinde bunun bir farklı versiyonu olan ve o anki kullanıcıyı gönderen `sendTo(to,text)` kullanmak isteyelim.

## İçerik olmadan kısmı fonksiyon kullanımı

Diyelim ki bazı argümanları düzeltmek istiyorsunuz fakat `this` ile bağlamak istemiyorsunuz?

Bildiğiniz gibi `bind` buna izin vermez. Doğrudan kaynağı atlayıp argümanları yazamazsınız.

Neyseki sadece argümanları bağlayabilen bir `kısmi` fonksiyon çok kolay bir şekilde yazılabilir.

Şu şekilde:

```js run
*!*
function partial(func, ...argsBound) {
  return function(...args) { // (*)
    return func.call(this, ...argsBound, ...args);
  }
}
*/!*

// kullanımı:
let user = {
  firstName: "John",
  say(time, phrase) {
    alert(`[${time}] ${this.firstName}: ${phrase}!`);
  }
};

// herhangi bir şey söyleyen kısmi bir metod ile ilk argümanı düzeltebilirsiniz.
user.sayNow = partial(user.say, new Date().getHours() + ':' + new Date().getMinutes());

user.sayNow("Hello"); // kaynak bulunmamakta 
// Aşağıdaki gibi:
// [10:00] Hello, John!
```

`partial(func[, arg1, arg2...])` saklayıcıyı çağırıyor ve `(*)` fonksiyonu `func`'ı aşağıdaki bilgiler ile çağırıyor.

- `this` burada ( `user.sayNow` , `user`'ı çağırır)
- Sonra `...argsBound` -- `kısmi` fonksiyondan gelen değer (`"10:00"`)
- Sonra `...args` -- saklayıcıya gönderilen argüman (`"Hello"`)

- Same `this` as it gets (for `user.sayNow` call it's `user`)
- Then gives it `...argsBound` -- arguments from the `partial` call (`"10:00"`)
- Then gives it `...args` -- arguments given to the wrapper (`"Hello"`)

Yayma operatörü ile oldukça kolay değil mi?

Bu olayın hazır halini [_.partial](https://lodash.com/docs#partial) lodash kütüphanesinde bulabilirsiniz.

## Tımarlamak

Bazen programcılar kısmı fonksiyonlar ile tımarlama olayını karıştırmaktadırlar. Tımarlama fonksiyonlar ile yapılabilecek ilginç bir tekniktir. Burada bahsetmekte fayda var.

[Tımarlama](https://en.wikipedia.org/wiki/Currying) `f(a, b, c)` olarak çağırılan bir fonksiyonu `f(a)(b)(c)` şeklinde çağırılabilmesini sağlayan bir tekniktir.

Binary fonksiyonunu tımarlamala işlemi aşağıdaki gibi yapılır. Bu fonksiyon `f(a, b)`'yı `f(a)(b)` şekline getirir:

```js run
*!*
function curry(func) {
  return function(a) {
    return function(b) {
      return func(a, b);
    };
  };
}
*/!*

// kullanımı
function sum(a, b) {
  return a + b;
}

let carriedSum = curry(sum);

alert( carriedSum(1)(2) ); // 3
```
Gördüğünüz gibi aslında birçok saklayıcının ard arda uygulanmasından meydana gelmektedir.

- `curry(func)`'ın sonucu `function(a)`'nın saklanmasıdır.
- `sum(1)` gibi bir çağrı yapıldığında arüman sözcük ortamına kaydedilir, ve yeni bir saklayıcı `function(b)` döndürürlür.
- Sonrasında `sum(1)(2)` en sonunda `function(b)`'i `2` değeri ile çağırır,  çağrıyı argümanların hepsi ile `sum` fonksiyonuna iletir.

Tımarlamanın daha gelişmiş bir versiyonu [_.curry](https://lodash.com/docs#curry) lodash kütüpyanesinde uygulanmıştır. Bu fonksiyonlar tüm argümanlar sağlandığında bir fonksiyonun normal olarak çalışmasını sağlayan saklayıcı fonksiyonu döndürür. Eğer tüm argümanlar sağlanmaz ise kısmı fonksiyon döndürür.

```js
function curry(f) {
  return function(..args) {
    // if args.length == f.length (f'in sahip olduğu kadar argüman var ise),
    //   çağrıyı f'e ilet.
    // diğer türlü args'ı ilk argüman olarak sabitleyen kısmı fonksiyon döndürülür.
  };
}
```

## Tımarlama? Neden yapılmalı?

Tımarlayarak hem fonksiyon normal olarak çağırılabilir  hem de kısmi olarak alınabilir. Yararını anlayabilmek için gerçekten de iyi bir örneğe gerek var.

Örneğin, bir loglama fonksiyonu olsun `log(data, importance, message)` gelen veriye göre çıktıyı formatlayabilsin. Projelerde böyle fonksiyonlar bunun yanında bir çok özelliğe sahip olabilir. Örneğin bunları ağ üzerinden iletmek veya filtrelemek gibi.

```js
function log(date, importance, message) {
  alert(`[${date.getHours()}:${date.getMinutes()}] [${importance}] ${message}`);
}
```

Hadi tımarlayalım!

```js
log = _.curry(log);
```
Bu `log` işleminden sonra hala normal olarak çalışır:

```js
log(new Date(), "DEBUG", "some debug");
```
... Bunun yanında tımarlı şekilde çağırılabilir:

```js
log(new Date())("DEBUG")("some debug"); // log(a)(b)(c)
```
Bu günün loglarını daha kolay bir şekilde alabileceğimiz bir fonksiyon alalım:

```js
// todayLog bu günün değeri sabit olacak şekilde oluşturulmuş bir kısmi fonksiyondur
let todayLog = log(new Date());

// kullanımı
todayLog("INFO", "message"); // [HH:mm] INFO message
```
Şimdi ise bu günün Debug değerlerini alabileceğimiz diğer bir fonksiyon yapalım:

```js
let todayDebug = todayLog("DEBUG");

todayDebug("message"); // [HH:mm] DEBUG message
```

Sonuç olarak:
1. We didn't lose anything after currying: `log` is still callable normally.
2. We were able to generate partial functions that are convenient in many cases.

## Advanced curry implementation

In case you're interested, here's the "advanced" curry implementation that we could use above.

```js run
function curry(func) {

  return function curried(...args) {
    if (args.length >= func.length) {
      return func.apply(this, args);
    } else {
      return function(...args2) {
        return curried.apply(this, args.concat(args2));
      }
    }
  };

}

function sum(a, b, c) {
  return a + b + c;
}

let curriedSum = curry(sum);

// still callable normally
alert( curriedSum(1, 2, 3) ); // 6

// get the partial with curried(1) and call it with 2 other arguments
alert( curriedSum(1)(2,3) ); // 6

// full curried form
alert( curriedSum(1)(2)(3) ); // 6
```

The new `curry` may look complicated, but it's actually pretty easy to understand.

The result of `curry(func)` is the wrapper `curried` that looks like this:

```js
// func is the function to transform
function curried(...args) {
  if (args.length >= func.length) { // (1)
    return func.apply(this, args);
  } else {
    return function pass(...args2) { // (2)
      return curried.apply(this, args.concat(args2));
    }
  }
};
```

When we run it, there are two branches:

1. Call now: if passed `args` count is the same as the original function has in its definition (`func.length`) or longer, then just pass the call to it.
2. Get a partial: otherwise, `func` is not called yet. Instead, another wrapper `pass` is returned, that will re-apply `curried` providing previous arguments together with the new ones. Then on a new call, again, we'll get either a new partial (if not enough arguments) or, finally, the result.

For instance, let's see what happens in the case of `sum(a, b, c)`. Three arguments, so `sum.length = 3`.

For the call `curried(1)(2)(3)`:

1. The first call `curried(1)` remembers `1` in its Lexical Environment, and returns a wrapper `pass`.
2. The wrapper `pass` is called with `(2)`: it takes previous args (`1`), concatenates them with what it got `(2)` and calls `curried(1, 2)` with them together.

    As the argument count is still less than 3, `curry` returns `pass`.
3. The wrapper `pass` is called again with `(3)`,  for the next call `pass(3)` takes previous args (`1`, `2`) and adds `3` to them, making the call `curried(1, 2, 3)` -- there are `3` arguments at last, they are given to the original function.

If that's still not obvious, just trace the calls sequence in your mind or on the paper.

```smart header="Fixed-length functions only"
The currying requires the function to have a known fixed number of arguments.
```

```smart header="A little more than currying"
By definition, currying should convert `sum(a, b, c)` into `sum(a)(b)(c)`.

But most implementations of currying in JavaScript are advanced, as described: they also keep the function callable in the multi-argument variant.
```

## Summary

- When we fix some arguments of an existing function, the resulting (less universal) function is called *a partial*. We can use `bind` to get a partial, but there are other ways also.

    Partials are convenient when we don't want to repeat the same argument over and over again. Like if we have a `send(from, to)` function, and `from` should always be the same for our task, we can get a partial and go on with it.

- *Currying* is a transform that makes `f(a,b,c)` callable as `f(a)(b)(c)`. JavaScript implementations usually both keep the function callable normally and return the partial if arguments count is not enough.

    Currying is great when we want easy partials. As we've seen in the logging example: the universal function `log(date, importance, message)` after currying gives us partials when called with one argument like `log(date)` or two arguments `log(date, importance)`.  
