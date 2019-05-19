# Sınıf kontrolü: "instanceof"

`instanceof` operatörü bir objenin belirli bir sınıfa ait olup olmadığını kontrol eder. Kalıtımı da hesaba kadar.

Böyle bir kontrole bir çok durumda ihtiyacımız olabilir. Aşağıda *polymorphic* fonksiyon inşa etmek için, argümanların tipine göre farklı davranış sergileyen bir yapı yer almaktadır.

[cut]

## instanceof operatorü [#ref-instanceof]


Yazımı şu şekildedir:
```js
obj instanceof Class
```

Eğer `obj`'e `Class`'a aitse `true` döner. ( Veya `Class`'tan türüyorsa)

Örneğin:

```js run
class Rabbit {}
let rabbit = new Rabbit();

// `Rabbit` sınıfının bir objesimidir?
*!*
alert( rabbit instanceof Rabbit ); // true
*/!*
```

Bu yapıcı fonksiyonlar için de çalışır:

```js run
*!*
// instead of class
function Rabbit() {}
*/!*

alert( new Rabbit() instanceof Rabbit ); // true
```

...`Array` gibi gömülü sınıflar için de

```js run
let arr = [1, 2, 3];
alert( arr instanceof Array ); // true
alert( arr instanceof Object ); // true
```

Dikkat edin `arr` ayrıca `Object` sınıfına da aittir. Çünkü `Array` prototipi `Object`'ten kalıtım alır.

`instanceof` operatörü prototip zincirini kontrol eder. `Symbol.hasInstance` statik metodu ile daha performanslı yapılabilir.

`obj instanceof Class` algoritması kabaca aşağıdaki gibi çalışır:

1. Eğer `Symbol.hasInstance` statik metodu var ise onu kullan. Şu şekilde:

    ```js run
    // canEat yapabilen herşeyi animal varsayalım.
    class Animal {
      static [Symbol.hasInstance](obj) {
        if (obj.canEat) return true;
      }
    }

    let obj = { canEat: true };
    alert(obj instanceof Animal); // true: Animal[Symbol.hasInstance](obj) çağırıldı.
    ```

2. Çoğu sınıf `Symbol.hasInstance`'a sahip değildir. Bu durumda eğer `Class.prototype` `obj`'nin bir prototipine zincirde olup olmadığını kontrol eder.

    Diğer bir deyişle:
    ```js
    obj.__proto__ == Class.prototype
    obj.__proto__.__proto__ == Class.prototype
    obj.__proto__.__proto__.__proto__ == Class.prototype
    ...
    ```

    Yukarıdaki örnekte `Rabbit.prototype == rabbit.__proto__`, cevabı doğrudan verir.
    
    Kalıtım yönünden ise `rabbit` üst sınıfın da instanceof'u dur.
    
    ```js run
    class Animal {}
    class Rabbit extends Animal {}

    let rabbit = new Rabbit();
    *!*
    alert(rabbit instanceof Animal); // true
    */!*
    // rabbit.__proto__ == Rabbit.prototype
    // rabbit.__proto__.__proto__ == Animal.prototype (match!)
    ```

Aşağıda `rabbit instanceof Animal`'ın `Animal.prototype`a karşılaştırılmasıgösterilmiştir.

![](instanceof.png)

Ayrıca [objA.isPrototypeOf(objB)](mdn:js/object/isPrototypeOf) metodu ile eğer `objA` `objB`'nin prototip zincirinin herhangi bir yerindeyse `true` döner. `obj instanceof Class` şu şekilde de yazılabilir `Class.prototype.isPrototypeOf(obj)`

`Class` yapıcısının kendisi bu kontrolde yer almaz, garip değil mi? Sadece `Class.prototype` ve prototiplerin zinciri önemlidir.

Bu `prototip` değiştiğinde farklı sonuçlara yol açabilir.

Aşağıdaki gibi:


```js run
function Rabbit() {}
let rabbit = new Rabbit();

// prototip değişti
Rabbit.prototype = {};

// ...artık rabbit değil!
*!*
alert( rabbit instanceof Rabbit ); // false
*/!*
```

Prototip'i değiştirmemeniz ve daha güvenli tutmanız için bir diğer neden daha olmuş oldu. 

## Bonus: Object toString for the type

We already know that plain objects are converted to string as `[object Object]`:

```js run
let obj = {};

alert(obj); // [object Object]
alert(obj.toString()); // the same
```

That's their implementation of `toString`. But there's a hidden feature thank makes `toString` actually much more powerful than that. We can use it as an extended `typeof` and an alternative for `instanceof`.

Sounds strange? Indeed. Let's demistify.

By [specification](https://tc39.github.io/ecma262/#sec-object.prototype.tostring), the built-in `toString` can be extracted from the object and executed in the context of any other value. And its result depends on that value.

- For a number, it will be `[object Number]`
- For a boolean, it will be `[object Boolean]`
- For `null`: `[object Null]`
- For `undefined`: `[object Undefined]`
- For arrays: `[object Array]`
- ...etc (customizable).

Let's demonstrate:

```js run
// copy toString method into a variable for convenience
let objectToString = Object.prototype.toString;

// what type is this?
let arr = [];

alert( objectToString.call(arr) ); // [object Array]
```

Here we used [call](mdn:js/function/call) as described in the chapter [](info:call-apply-decorators) to execute the function `objectToString` in the context `this=arr`.

Internally, the `toString` algorithm examines `this` and returns the corresponding result. More examples:

```js run
let s = Object.prototype.toString;

alert( s.call(123) ); // [object Number]
alert( s.call(null) ); // [object Null]
alert( s.call(alert) ); // [object Function]
```

### Symbol.toStringTag

The behavior of Object `toString` can be customized using a special object property `Symbol.toStringTag`.

For instance:

```js run
let user = {
  [Symbol.toStringTag]: 'User'
};

alert( {}.toString.call(user) ); // [object User]
```

For most environment-specific objects, there is such a property. Here are few browser specific examples:

```js run
// toStringTag for the envinronment-specific object and class:
alert( window[Symbol.toStringTag]); // window
alert( XMLHttpRequest.prototype[Symbol.toStringTag] ); // XMLHttpRequest

alert( {}.toString.call(window) ); // [object Window]
alert( {}.toString.call(new XMLHttpRequest()) ); // [object XMLHttpRequest]
```

As you can see, the result is exactly `Symbol.toStringTag` (if exists), wrapped into `[object ...]`.

At the end we have "typeof on steroids" that not only works for primitive data types, but also for built-in objects and even can be customized.

It can be used instead of `instanceof` for built-in objects when we want to get the type as a string rather than just to check.

## Summary

Let's recap the type-checking methods that we know:

|               | works for   |  returns      |
|---------------|-------------|---------------|
| `typeof`      | primitives  |  string       |
| `{}.toString` | primitives, built-in objects, objects with `Symbol.toStringTag`   |       string |
| `instanceof`  | objects     |  true/false   |

As we can see, `{}.toString` is technically a "more advanced" `typeof`.

And `instanceof` operator really shines when we are working with a class hierarchy and want to check for the class taking into account inheritance.
