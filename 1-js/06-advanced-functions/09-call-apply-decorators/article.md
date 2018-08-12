# Dekoratörler ve iletilme, call/apply

JavaScript fonksiyonlar ile uğraşırken inanılmaz derecede esneklik sağlamaktadır. Fonksiyonlar başka fonksiyonlara gönderilebilir, obje olarak kullanılabilir. Şimdi ise bunların nasıl *iletileceği* ve nasıl *dekore* edileceğinden bahsedilecektir;

[cut]

## Saydam Saklama

Diyelim ki `slow(x)` diye yoğun işlemci gücüne ihtiyaç duyan bir fonksiyonunuz olsun, buna rağmen sonucları beklediğiniz şekilde vermekte.

Eğer bu fonksiyon sık sık çağırılıyor ise farklı x'ler için sonucu saklamak bizi tekrar hesaplamadan kurtarabilir.

Fakat bunu `slow()` fonksiyonunun içine yazmak yerine yeni bir wrapper yazmak daha iyi olacaktır. Göreceğiniz üzere size oldukça fazla yardımı olacaktır.

Kod aşağıdaki gibidir:

```js run
function slow(x) {
  // burada baya yoğun işlemci gücüne ihtiyaş duyan işler yapılmaktadır.
  alert(`${x} ile çağırıldı`);
  return x;
}

function cachingDecorator(func) {
  let cache = new Map();

  return function(x) {
    if (cache.has(x)) { // eğer sonuç map içerisinde ise 
      return cache.get(x); // değeri gönder
    }

    let result = func(x); // aksi halde hesap yap

    cache.set(x, result); // sonra sonucu sakla 
    return result;
  };
}

slow = cachingDecorator(slow);

alert( slow(1) ); // slow(1) saklandı
alert( "Tekrar: " + slow(1) ); // aynısı döndü

alert( slow(2) ); // slow(2) saklandı
alert( "Tekrar: " + slow(2) ); // bir önceki ile aynısı döndü.
```

Yuarkıdaki kodda `cachingDecorator` bir *dekoratör*'dür: Diğer bir fonksiyonu alan ve bunun davranışını değiştiren özel bir fonksiyon fonksiyon.

Aslında her bir fonksiyon için `cachingDecorator` çağrılabilir ve o da saklama mekanizmasını kullanır. Harika, bu şekilde ihtiyacı olacak bir çok fonksiyonumuz olabilir. Tek yapmamız gereken bu fonksiyonlara `cachingDecorator` uygulamak.

Saklama olayını ana fonksiyonldan ayırarak aslında daha temiz bir yapıya da geçmiş olduk.

Detayına inmeye başlayabiliriz.

`cachingDecorator(func)` bir çeşit "wrapper(saklayıcı)"'dır. Bu işlem `func(x)` i "saklama" işine yarar.

![](decorator-makecaching-wrapper.png)

Gördüğünüz gibi, saklayıcı `func(x)`'ı olduğu gibi dönderir. Saklayıcının dışındaki `yavaş` olan fonksiyon hala aynı şekilde çalışır. Aslında davranışın üstüne sadece saklama(caching) mekanizması gelmiştir.

Özetlersek, ayrı bir `cachingDecorator` kullanmanın faydaları şu şekildedir:

- `cachingDecorator` tekrar kullanılabilir. Başka bir fonksiyona da uygulanabilir.
- Saklama(caching) mantığı ayrılmıştır böylece `yavaş` kodun içine daha fazla kod yazıp karışıklaştırılmamaktadır.
- Eğer ihtiyaç birden fazla dekoratör birlikte kullanılabilir.

## Kaynak için "func.all" kullanmak.

Yukarıda bahsettiğimiz saklama dekoratörü obje metodları ile çalışmak için müsait değildir.

Örneğin aşağıdaki kodda `user.format()` dekorasyondan sonra çalışmayı durdurur:

```js run
//  worker.slow sakla yapılacaktır.
let worker = {
  someMethod() {
    return 1;
  },

  slow(x) {
    // burada çok zorlu bir görev olabilir.  
    alert("Called with " + x);
    return x * this.someMethod(); // (*)
  }
};

// eskisiyle aynı kod
function cachingDecorator(func) {
  let cache = new Map();
  return function(x) {
    if (cache.has(x)) {
      return cache.get(x);
    }
*!*
    let result = func(x); // (**)
*/!*
    cache.set(x, result);
    return result;
  };
}

alert( worker.slow(1) ); // orjinal metod çalışmakta

worker.slow = cachingDecorator(worker.slow); // şimdi saklamaya alındı.

*!*
alert( worker.slow(2) ); // Whoops! Error: Özellik okunamamaktadır. `someMethod` tanımsız.
*/!*
```
`(*)` satırında hata olur `this.someMethod`'a erişmeye çalışır fakat başırılı olamaz. Nedeni ne olabilir ?

Sebebi `(**)` satırında orjinal `func(x)` çağırılmıştır. Bu şekilde çağırıldığında, fonksiyon `this = undefined` alır.

Aşağıdaki kod çalıştırılırsa da aynısı görülebilir:

```js
let func = worker.slow;
func(2);
```

Saklayıcı çağrıyı gerçek çalışacak metoda gönderir. Fakat `this` olmadığından dolayı hata alır.

Bunu düzeltmek için.

Özel bir metod bulunmaktadır [func.call(context, ...args)](mdn:js/Function/call) `this`'i belirterek doğrudan fonksiyonu çağırmaya yarar.

Yazımı aşağıdaki gibidir:

```js
func.call(context, arg1, arg2, ...)
```

İlk argüman `this`'dir diğerleri ise fonksiyon için gerekli argümanlardır.

Kullanımı şu şekildedir:

```js
func(1, 2, 3);
func.call(obj, 1, 2, 3)
```

Her ikisi de aslında `func` fonksiyonlarını `1`, `2`, `3` argümanları ile çağırır tek fark `func.call` fonksiyonunda `this`de gönderilir.

Örneğin, aşağıdaki kod `sayHi` metodunu iki farklı objeye değer atayarak çağırır. Birinci satırda `this=user` ikinci satırda ise `this=admin` değeri atanarak bu çağrı gerçekleştirilir.

```js run
function sayHi() {
  alert(this.name);
}

let user = { name: "John" };
let admin = { name: "Admin" };

// farklı objeler "this" objesi olarak gönderilebilir.
sayHi.call( user ); // John
sayHi.call( admin ); // Admin
```

Burada `say` metodunu çağırarak ne söyleneceğini gönderiyoruz:


```js run
function say(phrase) {
  alert(this.name + ': ' + phrase);
}

let user = { name: "John" };

// user `this` olmakta ve `phrase` ilk argüman olmaktadır. 
say.call( user, "Hello" ); // John: Hello
```

Bizim durumumuzda saklayıcı içinde `call` kullanarak içeriği orijinal fonksiyona aktarabiliriz:


```js run
let worker = {
  someMethod() {
    return 1;
  },

  slow(x) {
    alert(x + "ile çağırıldı");
    return x * this.someMethod(); // (*)
  }
};

function cachingDecorator(func) {
  let cache = new Map();
  return function(x) {
    if (cache.has(x)) {
      return cache.get(x);
    }
*!*
    let result = func.call(this, x); // "this" is passed correctly now
*/!*
    cache.set(x, result);
    return result;
  };
}

worker.slow = cachingDecorator(worker.slow); // now make it caching

alert( worker.slow(2) ); // çalışır
alert( worker.slow(2) ); // orjinali değilde hafızadaki çalışır.
```

Şimdi her şey beklendiği gibi çalışıyor.

Daha açıklayıcı olması için `this`'in nasıl ilerlediğini inceleyebiliriz:

1. Dekorasyon işleminden sonra `worker.slow` artık `function(x){ ...}` halini almıştır.
2. Öyleyse `worker.slow(2)` çalıştırıldığında saklayıcı `2` ve `this=worker` ( noktadan önceki obje ) argümanlarını alır.
3. Saklayıcı(wrapper) içinde sonucun henüz belleğe alınmadığını varsayarsak `func.call(this,x)` o anki `this` (`=worker`) ve ('=2`) değerini orjinal metoda gönderir.


## "func.apply" ile çoklu argüman kullanımı

`cachingDecorator` daha evrensel yapmak için ne değişiklikler yapmalıdır?

```js
let worker = {
  slow(min, max) {
    return min + max; // CPU'ya çok yük bindiren bir işlem.
  }
};

// aynı argüman ile çağırılmalıdır.
worker.slow = cachingDecorator(worker.slow);
```

Burada çözmemiz gereken iki problem bul

İlki `min` ve `max` değerlerinin bu `bellek` haritasında anahtar olarak nasıl tutulacağı. Önceki konuda tek `x` argümanı için `cache.set(x,result)` şeklinde sonucu belleğe kaydetmiş ve sonra `cache.get(x)` şeklinde almıştık. Fakat şimdi sonucu *argümanların birleşimi* şeklinde hatırlamak gerekmektedir. Normalde `Map` anahtarı tek değer olarak almaktadır.


Bu sorunun çözümü için bazı çözümler şu şekildedir:

1. Map-benzeri bir yapı kurarak birkaç anahtarı kullanabilen bir veri yapısı oluşturmak.
2. İç içe map kullanarak; Örneğin `cache.set(min)` aslında `(max, result)`'ı tutmaktadır. Böylece `result` `cache.get(min).get(max)` şeklinde alınabilir.
3. İki değeri teke indirerek. Bizim durumumuzda bu `"min,max"` şeklinde bir karakter dizisini `Map`'in anahtarı yapmak olabilir. Ayrıca *hashing fonksiyonu*'u dekoratöre sağlayabiliriz. Bu fonksiyon da birçok değerden bir değer yapabilir.

Çoğu uygulama için 3. çözüm yeterlidir. Biz de bu çözüm ile devam edeceğiz.

İkinci görev ise fonksiyona birden fazla argümanın nasıl gönderileceğidir. Şu anda saklayıcı fonksiyona `function(x)` şeklinde tek argüman gönderilmektedir. Bu da `func.call(this,x)` şeklinde uygulanır.

Burada kullanılacak diğer metod [func.apply](mdn:js/Function/apply)'dır.

Yazımı:

```js
func.apply(context, args)
```

Bu `func`'ı `this=context` ve args için dizi benzeri bir argüman dizisi ile çalıştırır.

Örneğin aşağıdaki iki çağrı tamamen aynıdır.

```js
func(1, 2, 3);
func.apply(context, [1, 2, 3])
```
Her ikisi de `func`'ı `1,2,3`argümanları ile çalıştırır. Fakat `apply` ayrıca `this=context`'i ayarlar.

```js run
function say(time, phrase) {
  alert(`[${time}] ${this.name}: ${phrase}`);
}

let user = { name: "John" };

let messageData = ['10:00', 'Hello']; // time, phrase'e dönüşür.

*!*
// this = user olur , messageData liste olarak (time,phrase) şeklinde gönderilir.
say.apply(user, messageData); // [10:00] John: Hello (this=user)
*/!*
```
`call` argüman listesi beklerken `apply` dizi benzeri bir obje ile onları alır.

Yayma operatörü <info:rest-parameters-spread-operator>  konusunda `...` yayma operatörünün ne iş yaptığını işlemiştik. Dizilerin argüman listesi şeklinde gönderilebileceğinden bahsemiştik. Öyleyse `call` ile bunu kullanırsak neredeyse `apply`'ın işlevini görebiliriz.

Aşağıdaki iki çağrı birbirinin aynısıdır:

```js
let args = [1, 2, 3];

*!*
func.call(context, ...args); // dizileri yayma operatörü ile liste şeklinde gönderir.
func.apply(context, args);   // aynısını apply ile yapar.
*/!*
```

İşleme daha yakından bakılacak olursa `call` ile `apply` arasında oldukça küçük bir fark vardır.

- The spread operator `...` allows to pass *iterable* `args` as the list to `call`.
- The `apply` accepts only *array-like* `args`.

So, these calls complement each other. Where we expect an iterable, `call` works, where we expect an array-like, `apply` works.

And if `args` is both iterable and array-like, like a real array, then we technically could use any of them, but `apply` will probably be faster, because it's a single operation. Most JavaScript engines internally optimize is better than a pair `call + spread`.

One of the most important uses of `apply` is passing the call to another function, like this:

```js
let wrapper = function() {
  return anotherFunction.apply(this, arguments);
};
```

That's called *call forwarding*. The `wrapper` passes everything it gets: the context `this` and arguments to `anotherFunction` and returns back its result.

When an external code calls such `wrapper`, it is undistinguishable from the call of the original function.

Now let's bake it all into the more powerful `cachingDecorator`:

```js run
let worker = {
  slow(min, max) {
    alert(`Called with ${min},${max}`);
    return min + max;
  }
};

function cachingDecorator(func, hash) {
  let cache = new Map();
  return function() {
*!*
    let key = hash(arguments); // (*)
*/!*
    if (cache.has(key)) {
      return cache.get(key);
    }

*!*
    let result = func.apply(this, arguments); // (**)
*/!*

    cache.set(key, result);
    return result;
  };
}

function hash(args) {
  return args[0] + ',' + args[1];
}

worker.slow = cachingDecorator(worker.slow, hash);

alert( worker.slow(3, 5) ); // works
alert( "Again " + worker.slow(3, 5) ); // same (cached)
```

Now the wrapper operates with any number of arguments.

There are two changes:

- In the line `(*)` it calls `hash` to create a single key from `arguments`. Here we use a simple "joining" function that turns arguments `(3, 5)` into the key `"3,5"`. More complex cases may require other hashing functions.
- Then `(**)` uses `func.apply` to pass both the context and all arguments the wrapper got (no matter how many) to the original function.


## Borrowing a method [#method-borrowing]

Now let's make one more minor improvement in the hashing function:

```js
function hash(args) {
  return args[0] + ',' + args[1];
}
```

As of now, it works only on two arguments. It would be better if it could glue any number of `args`.

The natural solution would be to use [arr.join](mdn:js/Array/join) method:

```js
function hash(args) {
  return args.join();
}
```

...Unfortunately, that won't work. Because we are calling `hash(arguments)` and `arguments` object is both iterable and array-like, but not a real array.

So calling `join` on it would fail, as we can see below:

```js run
function hash() {
*!*
  alert( arguments.join() ); // Error: arguments.join is not a function
*/!*
}

hash(1, 2);
```

Still, there's an easy way to use array join:

```js run
function hash() {
*!*
  alert( [].join.call(arguments) ); // 1,2
*/!*
}

hash(1, 2);
```

The trick is called *method borrowing*.

We take (borrow) a join method from a regular array `[].join`. And use `[].join.call` to run it in the context of `arguments`.

Why does it work?

That's because the internal algorithm of the native method `arr.join(glue)` is very simple.

Taken from the specification almost "as-is":

1. Let `glue` be the first argument or, if no arguments, then a comma `","`.
2. Let `result` be an empty string.
3. Append `this[0]` to `result`.
4. Append `glue` and `this[1]`.
5. Append `glue` and `this[2]`.
6. ...Do so until `this.length` items are glued.
7. Return `result`.

So, technically it takes `this` and joins `this[0]`, `this[1]` ...etc together. It's intentionally written in a way that allows any array-like `this` (not a coincidence, many methods follow this practice). That's why it also works with `this=arguments`.

## Summary

*Decorator* is a wrapper around a function that alters its behavior. The main job is still carried out by the function.

It is generally safe to replace a function or a method with a decorated one, except for one little thing. If the original function had properties on it, like `func.calledCount` or whatever, then the decorated one will not provide them. Because that is a wrapper. So one need to be careful if one uses them. Some decorators provide their own properties.

Decorators can be seen as "features" or "aspects" that can be added to a function. We can add one or add many. And all this without changing its code!

To implement `cachingDecorator`, we studied methods:

- [func.call(context, arg1, arg2...)](mdn:js/Function/call) -- calls `func` with given context and arguments.
- [func.apply(context, args)](mdn:js/Function/apply) -- calls `func` passing `context` as `this` and array-like `args` into a list of arguments.

The generic *call forwarding* is usually done with `apply`:

```js
let wrapper = function() {
  return original.apply(this, arguments);
}
```

We also saw an example of *method borrowing* when we take a method from an object and `call` it in the context of another object. It is quite common to take array methods and apply them to arguments. The alternative is to use rest parameters object that is a real array.


There are many decorators there in the wild. Check how well you got them by solving the tasks of this chapter.
