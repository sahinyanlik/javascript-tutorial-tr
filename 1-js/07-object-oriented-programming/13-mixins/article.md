# Mixinler

JavaScript sadece bir objeden kalıtım yapmaya izin verir. Bir obje için sadece bir tane `[[Prototype]]` olabilir. Ayrıca bir sınıf sadece bir sınıfı genişletebilir.

Bu bizi sınırlandırabilir. Örneğin, `StreetSweeper` ve `Bycicle` adında iki tane sınıfınız var ve bunlardan `StreetSweepingBycicle` adında bir sınıf yaratmak istiyorsunuz.

Veya programlama hakkında konuşacak olursak, `Renderer`adında şablonu uygulayan ve `EventEmitter` adında olayları işleyen bir sınıfımız olsun, ve bu fonksiyonaliteyi birlikte `Page` adında bir sınıfta kullanmak istiyoruz. Böylece page hem şabloları kullanabiliecek hemde hemde olayları yayacak(emit).

Burada bize `mixin` konsepti yardımcı olabilir.

Wikipedia'da şu şekilde tanımlanmıştır: [mixin](https://en.wikipedia.org/wiki/Mixin) sınıfı diğer sınıflar tarafından kullanılacak metodları olan ve bunun için bir üst sınıfa ihtiyaç duymayan yapıdır.

Diğer bir deyişle *mixin* belirli davranışları uygulayan metodları sağlar, fakat bunu tek başına kullanmayız, bunu diğer sınıflara başka davranışlar eklemek için kullanırız.

## Mixin örneği

JavaScript mixini yapmak için en kolay yol kullanışlı metodlarla donatılmış bir objedir. Böylece kolayca birleştirebilir ve herhangi bir sınıfın prototipine koyabiliriz.

Örneğin aşağoda `sayHiMixin`, `User` için "speech" metodunu ekler:

```js run
*!*
// mixin
*/!*
let sayHiMixin = {
  sayHi() {
    alert("Hello " + this.name);
  },
  sayBye() {
    alert("Bye " + this.name);
  }
};

*!*
// usage:
*/!*
class User {
  constructor(name) {
    this.name = name;
  }
}

// copy the methods
Object.assign(User.prototype, sayHiMixin);

// Artık User sayHi metodunu çağırabilir
new User("Dude").sayHi(); // Hi Dude!
```

Burada gördüğünüz gibi kalıtım yoktur. Yapılan sadece basit metod kopyalamadır. `User` diğer sınıfları genişletebilir, hatta bu sınıflar da kendi içerilerinde mixin'lere sahip olabilirler. Örnek vermek gerekirse:

```js
class User extends Person {
  // ...
}

Object.assign(User.prototype, sayHiMixin);
```
Mixinler kendi içlerinde kalıtım benzeri yapılar oluşturabilirler.

Örneğin `sayHiMixin`, `sayMixin`'ten kalıtılmıştır:

```js run
let sayMixin = {
  say(phrase) {
    alert(phrase);
  }
};

let sayHiMixin = {
  __proto__: sayMixin, // (veya Object.create ile de prototipi ayarlayabilirdik)

  sayHi() {
    *!*
    // call parent method
    */!*
    super.say("Hello " + this.name);
  },
  sayBye() {
    super.say("Bye " + this.name);
  }
};

class User {
  constructor(name) {
    this.name = name;
  }
}

// metodları kopyala
Object.assign(User.prototype, sayHiMixin);

// artık kullanıcı sayHi'yi çağırabilir.
new User("Dude").sayHi(); // Hello Dude!
```

Dikkat ederseniz `sayHiMixin` içinde `super.say() çağırıldığında o mixin'in prototipindeki metoduna bakar, sınıfın değil.

![](mixin-inheritance.png)

Çünkü `sayHiMixin` metodları `[[HomeObject]]`'e ayarlanmıştır. Bundan dolayı `super` aslında `User.__proto__` değil de `sayHiMixin.__proto__` anlamına gelir.

## EventMixin

Now let's make a mixin for real life.

The important feature of many objects is working with events.

That is: an object should have a method to "generate an event" when something important happens to it, and other objects should be able to "listen" to such events.

An event must have a name and, optionally, bundle some additional data.

For instance, an object `user` can generate an event `"login"` when the visitor logs in. And another object `calendar` may want to receive such events to load the calendar for the logged-in person.

Or, a `menu` can generate the event `"select"` when a menu item is selected, and other objects may want to get that information and react on that event.

Events is a way to "share information" with anyone who wants it. They can be useful in any class, so let's make a mixin for them:

```js run
let eventMixin = {
  /**
   * Subscribe to event, usage:
   *  menu.on('select', function(item) { ... }
  */
  on(eventName, handler) {
    if (!this._eventHandlers) this._eventHandlers = {};
    if (!this._eventHandlers[eventName]) {
      this._eventHandlers[eventName] = [];
    }
    this._eventHandlers[eventName].push(handler);
  },

  /**
   * Cancel the subscription, usage:
   *  menu.off('select', handler)
   */
  off(eventName, handler) {
    let handlers = this._eventHandlers && this._eventHandlers[eventName];
    if (!handlers) return;
    for(let i = 0; i < handlers.length; i++) {
      if (handlers[i] == handler) {
        handlers.splice(i--, 1);
      }
    }
  },

  /**
   * Generate the event and attach the data to it
   *  this.trigger('select', data1, data2);
   */
  trigger(eventName, ...args) {
    if (!this._eventHandlers || !this._eventHandlers[eventName]) {
      return; // no handlers for that event name
    }

    // call the handlers
    this._eventHandlers[eventName].forEach(handler => handler.apply(this, args));
  }
};
```

There are 3 methods here:

1. `.on(eventName, handler)` -- assigns function `handler` to run when the event with that name happens. The handlers are stored in the `_eventHandlers` property.
2. `.off(eventName, handler)` -- removes the function from the handlers list.
3. `.trigger(eventName, ...args)` -- generates the event: all assigned handlers are called and `args` are passed as arguments to them.


Usage:

```js run
// Make a class
class Menu {
  choose(value) {
    this.trigger("select", value);
  }
}
// Add the mixin
Object.assign(Menu.prototype, eventMixin);

let menu = new Menu();

// call the handler on selection:
*!*
menu.on("select", value => alert("Value selected: " + value));
*/!*

// triggers the event => shows Value selected: 123
menu.choose("123"); // value selected
```

Now if we have the code interested to react on user selection, we can bind it with `menu.on(...)`.

And the `eventMixin` can add such behavior to as many classes as we'd like, without interfering with the inheritance chain.

## Summary

*Mixin* -- is a generic object-oriented programming term: a class that contains methods for other classes.

Some other languages like e.g. python allow to create mixins using multiple inheritance. JavaScript does not support multiple inheritance, but mixins can be implemented by copying them into the prototype.

We can use mixins as a way to augment a class by multiple behaviors, like event-handling as we have seen above.

Mixins may become a point of conflict if they occasionally overwrite native class methods. So generally one should think well about the naming for a mixin, to minimize such possibility.
