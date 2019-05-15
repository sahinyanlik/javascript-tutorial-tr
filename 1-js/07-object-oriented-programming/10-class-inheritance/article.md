
# Sınıf kalıtımı, super

Sınıflar başka sınıfları genişletebilirler. Bunun için prototip kalıtımı tabanlı güzel bir yazılışı bulunmaktadır.

Diğer bir sınıftan kalıtım sağlamak için `"extends"` ile belirtmek gerekmektedir. 

[cut]

Aşağıda `Animal`'dan kalıtım alan `Rabbit` sınıfı gösterilmektedir:

```js run
class Animal {

  constructor(name) {
    this.speed = 0;
    this.name = name;
  }

  run(speed) {
    this.speed += speed;
    alert(`${this.name} runs with speed ${this.speed}.`);
  }

  stop() {
    this.speed = 0;
    alert(`${this.name} stopped.`);
  }

}

*!*
// Inherit from Animal
class Rabbit extends Animal {
  hide() {
    alert(`${this.name} hides!`);
  }
}
*/!*

let rabbit = new Rabbit("White Rabbit");

rabbit.run(5); // White Rabbit runs with speed 5.
rabbit.hide(); // White Rabbit hides!
```

`extends` kelimesi aslında  `Rabbit.prototype`'dan referans alıp bunun  `[[Prototype]]`'ını `Animal.prototype`'a ekler. Aynen daha önce de gördüğümüz gibi.

![](animal-rabbit-extends.png)

Artık `rabbit` hem kendi metodlarına hem de `Animal` metodlarına erişebilir.

````smart header="`extends`'ten sonra her türlü ifade kullanılabilir."

`Extends`'ten sonra sadece sınıf değil her türlü ifade kullanılabilir.

Örneğin, üst sınıfı yaratan yeni bir fonksiyon çağrısı:

```js run
function f(phrase) {
  return class {
    sayHi() { alert(phrase) }
  }
}

*!*
class User extends f("Hello") {}
*/!*

new User().sayHi(); // Hello
```
Burada `class User` `f("Hello")`'nun sonucunu kalıtır.

Bu belki çok ileri teknik programlama kalıpları için  kullanışlı olabilir. Böylece birçok koşula göre fonksiyonları kullanarak farklı sınıflar oluşturabilir ve bunlardan kalıtım alınabilir. 
````

## Bir metodu geçersiz kılma, üstüne yazma.

Şimdi biraz daha ileri gidelim ve metodun üstüne yazalım. Şimdiden sonra `Rabbit` `stop` metodunu kalıtım alır, bu metod `this.speed=0`'ı `Animal` sınıfında ayarlamaya yarar.

Eğer `Rabbit` içerisinde kendi `stop` metodunuzu yazarsanız buna üstüne yazma denir ve `Animal`'da yazılmış `stop` metodu kullanılmaz.

```js
class Rabbit extends Animal {
  stop() {
    // ... rabbit.stop() için artık bu kullanılacak.
  }
}
```

...Fakat genelde üst metodun üzerine yazmak istenmez, bunun yerine küçük değişiklikler yapmak veya fonksiyonliteyi genişletmek daha fazla tercih edilen yöntemdir. Metodda birçeyler yapar ve genelde bundan önce/sonra veya işlerken üst metodu çağırırız.

Sınıflar bunun için `"super"` anahtar kelimesini sağlarlar.


- `super.method(...)` üst class'ın metodunu çağırmak için.

- `super(...)` üst metodun yapıcısını ( constructor) çağırmak için kullanılır.

Örneğin, Rabbit otomatik olarak durduğunda gizlensin.

```js run
class Animal {

  constructor(name) {
    this.speed = 0;
    this.name = name;
  }

  run(speed) {
    this.speed += speed;
    alert(`${this.name} runs with speed ${this.speed}.`);
  }

  stop() {
    this.speed = 0;
    alert(`${this.name} stopped.`);
  }

}

class Rabbit extends Animal {
  hide() {
    alert(`${this.name} hides!`);
  }

*!*
  stop() {
    super.stop(); // üst sınıfın stop metodunu çağır.
    this.hide(); // sonra gizle
  }
*/!*
}

let rabbit = new Rabbit("White Rabbit");

rabbit.run(5); // White Rabbit runs with speed 5.
rabbit.stop(); // White Rabbit stopped. White rabbit hides!
```
Artık `Rabbit`, `stop` metodunda üst sınıfın `super.stop()`'unu çağırmaktadır.

````smart header="Ok fonksiyonlarının `super`'i bulunmamaktadır."
<info:arrow-functions> bölümünde bahsedildiği gibi, ok fonksiyonlarının `super`'i bulunmamaktadır.

Eğer erişim olursa bu `super` dışarıdaki fonksiyonundur. Örneğin:
```js
class Rabbit extends Animal {
  stop() {
    setTimeout(() => super.stop(), 1000); // üst'ün stop'unu 1 sn sonra çağır. 
  }
}
```
Ok fonksiyonu içerisindeki `super` ile `stop()` içerisine yazılan `super` aynıdır. Eğer "sıradan" bir fonksiyon tanımlarsak bu hataya neden olabilir:

```js
// Unexpected super
setTimeout(function() { super.stop() }, 1000);
```
````


## Yapıcı metodu ezmek.

Yapıcı metodlar ile yapılan şeyler biraz çetrefillidir.

Şimdiye kadar `Rabbit` kendisine ait `yapıcı`'ya sahipti.

[Şartname](https://tc39.github.io/ecma262/#sec-runtime-semantics-classdefinitionevaluation)'ye göre eğer bir sınıf diğer başka bir sınıftan türer ve `constructor`'a sahip değil ise aşağıdaki `yapıcı` otomatik olarak oluşturulur.

```js
class Rabbit extends Animal {
  // yapıcısı olmayan ve türetilen sınıf için oluşturulur.
*!*
  constructor(...args) {
    super(...args);
  }
*/!*
}
```
Gördüğünüz gibi aslında üst sınıfın `yapıcı`'sını tüm argümanları göndererek çağırır. Eğer kendimiz bir yapıcı yazmazsak bu meydana gelir.

Özel olarak uyarlanmış bir yapıcı oluşturalım. Bu isim ile birlikte `earLength`'i de tanımlasın:

```js run
class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  // ...
}

class Rabbit extends Animal {

*!*
  constructor(name, earLength) {
    this.speed = 0;
    this.name = name;
    this.earLength = earLength;
  }
*/!*

  // ...
}

*!*
// Çalışmaz!
let rabbit = new Rabbit("White Rabbit", 10); // Error: this is not defined.
*/!*
```
Nasıl ya! Hata aldık. Şimdi de rabbit oluşturamıyoruz. Neden peki?

Kısa cevap: Türemiş sınıftaki yapıcı kesinlikle `super(...)` i çağırmalıdır. Bu `this`'den önce olmalıdır.

...Peki neden? Çok garip değilmi?

Tabi bu açıklanabilir bir olay. Detayına girdikçe daha iyi anlayacaksınız.

JavaScript'te "türeyen sınıfın yapıcı fonksiyonu" ve diğerleri arasında farklılıklar mevcuttur. Türemiş sınıflarda eş yapcıı fonksiyonlar içsel olarak `[[ConstructorKind]]:"derived"` şeklinde etiketlenir. 

Farklılık:

- Normal yapıcı çalıştığında boş bir objeyi `this` olarak yaratır ve bunun ile devam eder.
- Fakat türemiş sınıfın yapıcısı çalıştığında bunu yapmaz. Üst fonksiyonun yapıcısının bunu yapmasını bekler.

Eğer kendimiz bir yapıcı yazarsak bundan dolayı `super` i çağırmamız gerekmektedir. Aksi halde `this` referansı oluşturulmaz ve biz de hata alırız.

`Rabbit`'in çalışabilmesi için `this`'den önce `super()` çağırılmalıdır.

```js run
class Animal {

  constructor(name) {
    this.speed = 0;
    this.name = name;
  }

  // ...
}

class Rabbit extends Animal {

  constructor(name, earLength) {
*!*
    super(name);
*/!*
    this.earLength = earLength;
  }

  // ...
}

*!*
// Şimdi düzgün bir şekilde çalışır.
let rabbit = new Rabbit("White Rabbit", 10);
alert(rabbit.name); // White Rabbit
alert(rabbit.earLength); // 10
*/!*
```

## Super: dahililer, [[HomeObject]]

Artık `super`'in derinliklerine dalma vakti geldi. Altında yatan ilginç şeyler nelermiş bunları göreceğiz.

Öncelikle, şimdiye kadar öğrendiklerimizle `super` ile çalışmak mümkün değil.

Ever gerçekten, kendimize soralım, nasıl teknik olarak böyle birşey çalışabilir? Bir obje metodu çalıştığında var olan objeyi `this` olarak alır. Eğer biz `super.method()`'u çağırırsak `metod`'u nasıl alabilir? Doğal olarak `method`'u var olan objenin prototipinden almak gerekmektedir. Peki teknik olarak bunu JavaScript motoru nasıl halledebilir?

Belki `this`in `[[Prototype]]`'ını `this.__proto__.method` olarak alıyordur? Malesef böyle çalışmıyor.

Bunu test edelim. Sınıflar olmadan basit objelerle, fazladan karmaşıklaştırmadan deneyelim.

Aşağıda `rabbit.eat()`, kendisinin üst metodu `animal.eat()`'i çağırmalıdır:

```js run
let animal = {
  name: "Animal",
  eat() {
    alert(this.name + " eats.");
  }
};

let rabbit = {
  __proto__: animal,
  name: "Rabbit",
  eat() {
*!*
    // bizim tahminimze göre super.eat() bu şekilde çalışabilir.
    this.__proto__.eat.call(this); // (*)
*/!*
  }
};

rabbit.eat(); // Rabbit eats.
```
`(*)` satırında `animal` prototipinden `eat`'i almakta ve var olan obje kaynağından çağırmaktayız. Dikkat edin burada `.call(this)` oldukça önemlidir. Çünkü basit `this.__proto__.eat()` üst `eat`'i prototipin kaynağı ile çağırır, var olan objenin değil.

Yukarıdaki kod beklendiği gibi çalışmaktadır. Doğru `alert` vermektedir.

Şimdi bu zincire bir tane daha obje ekleyelim. İşler nasıl bozuluyor görelim:

```js run
let animal = {
  name: "Animal",
  eat() {
    alert(this.name + " eats.");
  }
};

let rabbit = {
  __proto__: animal,
  eat() {
    // ...tavşan-stili ayla ve üst sınıfı çağır.
    this.__proto__.eat.call(this); // (*)
  }
};

let longEar = {
  __proto__: rabbit,
  eat() {
    // ...uzun kulaklar ile birşeyler yap ve üst sınıfı çağır.
    this.__proto__.eat.call(this); // (**)
  }
};

*!*
longEar.eat(); // Error: Maximum call stack size exceeded
*/!*
```
Yazdığınız kod artık çalışmıyor! `longEar.eat()`'i çağırırken hata olduğunu görebilirsiniz.

Bu çok açık olmayabilir, fakat `longEar.eat()` in hata kodlarını takip ederseniz nedenini anlayabilirsiniz. `(*)` ve `(**)` satırlarında `this` var olan (`longEar`) objesidir. Hatırlayın: Tüm objeclerin metodları `this` olarak var olan objeyi alır, prototipini değil.

Öyleyse, `(*)`,`(**)` ve `this.__proto__` tamamen aynıdır: `rabbit`. Hepsi `rabbit.eat`'i sonsuz zincire çıkmadan çağırır.

Aşağıda ne olduğunu daha iyi anlatan bir görsel bulunmakta:

![](this-super-loop.png)

1. `longEar.eat()` içerisinde `(**)` satırı `rabbit.eat`'i `this=longEar` olarak çağırmakta.
    ```js
    // longEar.eat() içerisinde this = longEar şeklinde kullanmaktayız.
    this.__proto__.eat.call(this) // (**)
    // olur
    longEar.__proto__.eat.call(this)
    // bu da 
    rabbit.eat.call(this);
    ```
2. Sonra `rabbit.eat`'in `(*)` satırı içerisinde bu zinciri daha üstlere çıkarmaya çalışıyoruz, fakat `this=longEar`, yani `this.__proto__.eat` yine `rabbit.eat`!

    ```js
    // rabbit.eat() içerisinde thiss= longEar bulunmakta
    this.__proto__.eat.call(this) // (*)
    // olur
    longEar.__proto__.eat.call(this)
    // veya (yine)
    rabbit.eat.call(this);
    ```

3. ... Artık `rabbit.eat` 'in kendisini neden sonsuz defa çağırdığını görmüş olduk.

Problem sadece `this` kullanılarak çözülemez.

### `[[HomeObject]]`

Buna bir çözüm sağlamak için, JavaScript fonksiyonlar için bir tane dahili özellik eklemiştir: `[[HomeObject]]`

**Bir fonksiyon sınıf veya obje metodu olarak tanımlandığında, bunun `[[HomeObject]]`'i kendisi olur**

Bu aslında bağımsız fonksiyonlar fikrini bozmaktadır, çünkü metodlar kendi objelerini hatırlamaktadır. Ayrıca `[[HomeObject]]` değiştirilemez, yani bu bağ sonsuza kadardır. Aslında bu dilde yapılan oldukça büyük bir değişiklik.

Fakat bu değişiklik güvenlidir. `[[HomeObject]]` sadece üst sınıfın metodlarını `super`'de çağırmaya yarar. Bundan dolayı uyumluluğu bozmaz.

Şimdi `super` ile nasıl çalışıyor bunu inceleyelim --tekrardan, sade objeleri kullanalım:

```js run
let animal = {
  name: "Animal",
  eat() {         // [[HomeObject]] == animal
    alert(this.name + " eats.");
  }
};

let rabbit = {
  __proto__: animal,
  name: "Rabbit",
  eat() {         // [[HomeObject]] == rabbit
    super.eat();
  }
};

let longEar = {
  __proto__: rabbit,
  name: "Long Ear",
  eat() {         // [[HomeObject]] == longEar
    super.eat();
  }
};

*!*
longEar.eat();  // Long Ear eats.
*/!*
```
Her metod kendi objesinin `[[HomeObject]]` özelliğini hatırlamakta. Sonra `super`bunu üst objenin prototipini çözerken kullanır.

`[[HomeObject]]` sınıflar veya sade objeler'de tanımlanan metodlar için tanımlanır. Fakat objeler için, metodlar aynen şu şekilde tanımlanmalıdır: `method()`,  `"method:function()"` şeklinde değil.

Aşağıdaki örnekte karşılaştırma için metod-olmayan yazım kullanılmıştır. `[[HomeObject]]` özelliği tanımlanmadı bundan dolayı da kalıtım çalışmayacaktır.

```js run
let animal = {
  eat: function() { // kısa yazım: eat() {...} olmalıdır.
    // ...
  }
};

let rabbit = {
  __proto__: animal,
  eat: function() {
    super.eat();
  }
};

*!*
rabbit.eat();  // super'i çalıştırırken hata oldu çünkü [[HomeObject]] bulunmamakta.
*/!*
```

## Static methods and inheritance

The `class` syntax supports inheritance for static properties too.

For instance:

```js run
class Animal {

  constructor(name, speed) {
    this.speed = speed;
    this.name = name;
  }

  run(speed = 0) {
    this.speed += speed;
    alert(`${this.name} runs with speed ${this.speed}.`);
  }

  static compare(animalA, animalB) {
    return animalA.speed - animalB.speed;
  }

}

// Inherit from Animal
class Rabbit extends Animal {
  hide() {
    alert(`${this.name} hides!`);
  }
}

let rabbits = [
  new Rabbit("White Rabbit", 10),
  new Rabbit("Black Rabbit", 5)
];

rabbits.sort(Rabbit.compare);

rabbits[0].run(); // Black Rabbit runs with speed 5.
```

Now we can call `Rabbit.compare` assuming that the inherited `Animal.compare` will be called.

How does it work? Again, using prototypes. As you might have already guessed, extends also gives `Rabbit` the `[[Prototype]]` reference to `Animal`.


![](animal-rabbit-static.png)

So, `Rabbit` function now inherits from `Animal` function. And `Animal` function normally has `[[Prototype]]` referencing `Function.prototype`, because it doesn't `extend` anything.

Here, let's check that:

```js run
class Animal {}
class Rabbit extends Animal {}

// for static propertites and methods
alert(Rabbit.__proto__ == Animal); // true

// and the next step is Function.prototype
alert(Animal.__proto__ == Function.prototype); // true

// that's in addition to the "normal" prototype chain for object methods
alert(Rabbit.prototype.__proto__ === Animal.prototype);
```

This way `Rabbit` has access to all static methods of `Animal`.

### No static inheritance in built-ins

Please note that built-in classes don't have such static `[[Prototype]]` reference. For instance, `Object` has `Object.defineProperty`, `Object.keys` and so on, but `Array`, `Date` etc do not inherit them.

Here's the picture structure for `Date` and `Object`:

![](object-date-inheritance.png)

Note, there's no link between `Date` and `Object`. Both `Object` and `Date` exist independently. `Date.prototype` inherits from `Object.prototype`, but that's all.

Such difference exists for historical reasons: there was no thought about class syntax and inheriting static methods at the dawn of JavaScript language.

## Natives are extendable

Built-in classes like Array, Map and others are extendable also.

For instance, here `PowerArray` inherits from the native `Array`:

```js run
// add one more method to it (can do more)
class PowerArray extends Array {
  isEmpty() {
    return this.length == 0;
  }
}

let arr = new PowerArray(1, 2, 5, 10, 50);
alert(arr.isEmpty()); // false

let filteredArr = arr.filter(item => item >= 10);
alert(filteredArr); // 10, 50
alert(filteredArr.isEmpty()); // false
```

Please note one very interesting thing. Built-in methods like `filter`, `map` and others -- return new objects of exactly the inherited type. They rely on the `constructor` property to do so.

In the example above,
```js
arr.constructor === PowerArray
```

So when `arr.filter()` is called, it internally creates the new array of results exactly as `new PowerArray`. And we can keep using its methods further down the chain.

Even more, we can customize that behavior. The static getter `Symbol.species`, if exists, returns the constructor to use in such cases.

For example, here due to `Symbol.species` built-in methods like `map`, `filter` will return "normal" arrays:

```js run
class PowerArray extends Array {
  isEmpty() {
    return this.length == 0;
  }

*!*
  // built-in methods will use this as the constructor
  static get [Symbol.species]() {
    return Array;
  }
*/!*
}

let arr = new PowerArray(1, 2, 5, 10, 50);
alert(arr.isEmpty()); // false

// filter creates new array using arr.constructor[Symbol.species] as constructor
let filteredArr = arr.filter(item => item >= 10);

*!*
// filteredArr is not PowerArray, but Array
*/!*
alert(filteredArr.isEmpty()); // Error: filteredArr.isEmpty is not a function
```

We can use it in more advanced keys to strip extended functionality from resulting values if not needed. Or, maybe, to extend it even further.
