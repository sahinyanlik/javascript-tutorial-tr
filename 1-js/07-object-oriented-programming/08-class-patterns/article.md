
# Sınıf Desenleri

```quote author="Wikipedia"
Sınıf, nesne yönelimli programlama dillerinde nesnelerin özelliklerini, davranışlarını ve başlangıç durumlarını tanımlamak için kullanılan şablonlara verilen addır. Bir sınıftan türetilmiş bir nesne ise o sınıfın örneği olarak tanımlanır.
```

JavaScript dilinde yapıcı `class` sözcüğü bulunmaktadır. Fakat bunu çalışmadan önce, "class" sözcüğünün Nesne Yönelimli Programlamadan geldiğini söylemek gerekir. Tanım yukarıda alıntılanmıştır, dil bağımsızdır.

JavaScript dilinde bilinen birkaç programlama deseni bulunmaktadır, bu şekilde `class` anahtarını kullanmadan bile sınıflar yapılabilir. Öncelikle bunlardan bahsedelim.

`class` yapıcısı bir sonraki bölümde anlatılacaktır. Aslında class yapısı JavaScript'te bu "syntax sugar"'dır yani bir desenin uzantısıdır. 

[cut]


## Fonksiyonel Sınıf Deseni

Tanıma göre aşağıdaki kurucu fonksiyon bir "sınıf"'tır:

```js run
function User(name) {
  this.sayHi = function() {
    alert(name);
  };
}

let user = new User("John");
user.sayHi(); // John
```
Tüm gerekli özellikleri taşımaktadır:

1. Obje yaratmak için "Program-kod-teması"'dır. `new` ile çağırılabilir.
2. Durum için gerekli değerleri sağlar ( parametre'den `name` argümanın alınabilir olması)
3. Metodlar sağlaması ( `sayHi` ).

Buna *Fonksiyonel Sınıf Deseni* denir.

Fonksiyonel desende, `user` içindeki yerel değişkenler ve fonksiyonlar, `this` ile atanmasalar, içeriden görünür fakat dışarıdan erişilemez olurlar.

Bundan dolayı kolayca `calcAge()` gibi iç fonksiyonları ekleyebiliriz:

```js run
function User(name, birthday) {

*!*
  // Sadece User içerisindeki diğer metodlar tarafından kullanılabilir.
  function calcAge() {
    return new Date().getFullYear() - birthday.getFullYear();
  }
*/!*

  this.sayHi = function() {
    alert(name + ', age:' + calcAge());
  };
}

let user = new User("John", new Date(2000,0,1));
user.sayHi(); // John
```

Yukarıdaki kodda `name`, `birthda` ve `calcAge()` fonksiyonları iç fonksiyonlardır objeye *private*(özel)'dir. Sadece kendi içerisinden çağırılabilir.

Diğer taraftan `sayHi` dış *public* bir metoddur. `user`'ı oluşturan kod buna erişebilir.
From the other hand, `sayHi` is the external, *public* method. The external code that creates `user` can access it.

Böylece yardımcı metodları ve başka sınıflar tarafından kullanılmasını istemediğimiz kodları gizleyebiliriz. Bunlar sadece `this`'e atanırsa görünür olurlar.

## Factory Sınıf Deseni

`new` kullanmadan da aslında sınıf yaratmak mümkündür.

Şu şekilde:

```js run
function User(name, birthday) {
  // only visible from other methods inside User
  function calcAge() {
    return new Date().getFullYear() - birthday.getFullYear();
  }

  return {
    sayHi() {
      alert(name + ', age:' + calcAge());
    }
  };
}

*!*
let user = User("John", new Date(2000,0,1));
*/!*
user.sayHi(); // John
```

Gördüğünüz gibi, `User` bir obje döner ve buna ait kamusal özellikler ve metodlar mevcuttur. Bunun en büyük artısı `new` yazmamıza gerek kalmamasıdır;  `let user = new User(...)` yerin  `let user = User(...)` şeklinde yazabiliriz. Diğer bir deyişle fonksiyonel desen ile neredeyse aynıdır.

## Prototype-based classes

Prototype-based classes is the most important and generally the best. Functional and factory class patterns are rarely used in practice.

Soon you'll see why.

Here's the same class rewritten using prototypes:

```js run
function User(name, birthday) {
*!*
  this._name = name;
  this._birthday = birthday;
*/!*
}

*!*
User.prototype._calcAge = function() {
*/!*
  return new Date().getFullYear() - this._birthday.getFullYear();
};

User.prototype.sayHi = function() {
  alert(this._name + ', age:' + this._calcAge());
};

let user = new User("John", new Date(2000,0,1));
user.sayHi(); // John
```

The code structure:

- The constructor `User` only initializes the current object state.
- Methods are added to `User.prototype`.

As we can see, methods are lexically not inside `function User`, they do not share a common lexical environment. If we declare variables inside `function User`, then they won't be visible to methods.

So, there is a widely known agreement that internal properties and methods are prepended with an underscore `"_"`. Like `_name` or `_calcAge()`. Technically, that's just an agreement, the outer code still can access them. But most developers recognize the meaning of `"_"` and try not to touch prefixed properties and methods in the external code.

Here are the advantages over the functional pattern:

- In the functional pattern, each object has its own copy of every method. We assign a separate copy of `this.sayHi = function() {...}` and other methods in the constructor.
- In the prototypal pattern, all methods are in `User.prototype` that is shared between all user objects. An object itself only stores the data.

So the prototypal pattern is more memory-efficient.

...But not only that. Prototypes allow us to setup the inheritance in a really efficient way. Built-in JavaScript objects all use prototypes. Also there's a special syntax construct: "class" that provides nice-looking syntax for them. And there's more, so let's go on with them.

## Prototype-based inheritance for classes

Let's say we have two prototype-based classes.

`Rabbit`:

```js
function Rabbit(name) {
  this.name = name;
}

Rabbit.prototype.jump = function() {
  alert(this.name + ' jumps!');
};

let rabbit = new Rabbit("My rabbit");
```

![](rabbit-animal-independent-1.png)

...And `Animal`:

```js
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  alert(this.name + ' eats.');
};

let animal = new Animal("My animal");
```

![](rabbit-animal-independent-2.png)

Right now they are fully independent.

But we'd want `Rabbit` to extend `Animal`. In other words, rabbits should be based on animals, have access to methods of `Animal` and extend them with its own methods.

What does it mean in the language of prototypes?

Right now methods for `rabbit` objects are in `Rabbit.prototype`. We'd like `rabbit` to use `Animal.prototype` as a "fallback", if the method is not found in `Rabbit.prototype`.

So the prototype chain should be `rabbit` -> `Rabbit.prototype` -> `Animal.prototype`.

Like this:

![](class-inheritance-rabbit-animal.png)

The code to implement that:

```js run
// Same Animal as before
function Animal(name) {
  this.name = name;
}

// All animals can eat, right?
Animal.prototype.eat = function() {
  alert(this.name + ' eats.');
};

// Same Rabbit as before
function Rabbit(name) {
  this.name = name;
}

Rabbit.prototype.jump = function() {
  alert(this.name + ' jumps!');
};

*!*
// setup the inheritance chain
Rabbit.prototype.__proto__ = Animal.prototype; // (*)
*/!*

let rabbit = new Rabbit("White Rabbit");
*!*
rabbit.eat(); // rabbits can eat too
*/!*
rabbit.jump();
```

The line `(*)` sets up the prototype chain. So that `rabbit` first searches methods in `Rabbit.prototype`, then `Animal.prototype`. And then, just for completeness, let's mention that if the method is not found in `Animal.prototype`, then the search continues in `Object.prototype`, because `Animal.prototype` is a regular plain object, so it inherits from it.

So here's the full picture:

![](class-inheritance-rabbit-animal-2.png)

## Summary

The term "class" comes from the object-oriented programming. In JavaScript it usually means the functional class pattern or the prototypal pattern. The prototypal pattern is more powerful and memory-efficient, so it's recommended to stick to it.

According to the prototypal pattern:
1. Methods are stored in `Class.prototype`.
2. Prototypes inherit from each other.

In the next chapter we'll study `class` keyword and construct. It allows to write prototypal classes shorter and provides some additional benefits.
