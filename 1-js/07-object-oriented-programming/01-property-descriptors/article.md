
# Özellik bayrakları ve tanımlayıcılar

Objelerin özellikleri saklayabildiğini biliyorsunuz.

Şimdiye kadar özellik basit "anahtar-değer" ikilisiydi. Fakat objenin özelliği aslında bundan daha karmaşık ve daha farklılaştırılabilir özellikler taşımaktadır.

[cut]

## Özellik Bayrakları

Obje özellikleri **`değer`** dışında, 3 özelliğe sahiptir ( bunlara "bayraklar" denir. )

- **`yazılabilir`** -- eğer `true` ise değiştirilebilir aksi halde sadece okunabilir.
- **`sayılabilir`** -- eğer `true` ise döngü içinde listelenmiştir, aksi halde listelenmemiştir.
- **`ayarlanabilir`** -- eğer `true` ise özellik silinebilir ve nitelikler ( attributes ) değiştirilebilir, diğer türlü değiştirilemez.

Bunları henüz görmediniz, genel olarak da zaten pek gösterilmezler. Bir özellik yarattığınızda "normal yolla" bu değerlerin tümü `true` olarak ayarlanır. Fakat biz bunları istediğimiz zaman değiştirebiliriz.

İlk önce bu bayraklar nasıl alınır buna bakalım:

[Object.getOwnPropertyDescriptor](mdn:js/Object/getOwnPropertyDescriptor) metodu bir özellik hakkındaki *tüm* bilgilerin sorgulanabilmesini sağlar.

Yazımı:
```js
let descriptor = Object.getOwnPropertyDescriptor(obj, propertyName);
```

`obj`
: Bilgi alınacak obje

`propertyName`
: Özelliğin ismi

Buradan dönen bir değer döner buna "özellik tanımlayıcısı" denir. Bu obje tüm bayrak bilgilerini içerir.

Örneğin:

```js run
let user = {
  name: "John"
};

let descriptor = Object.getOwnPropertyDescriptor(user, 'name');

alert( JSON.stringify(descriptor, null, 2 ) );
/* property descriptor:
{
  "value": "John",
  "writable": true,
  "enumerable": true,
  "configurable": true
}
*/
```
Bayrakları değiştirmek için [Object.defineProperty](mdn:js/Object/defineProperty) kullanılabilir.

Yazımı:


```js
Object.defineProperty(obj, propertyName, descriptor)
```

`obj`, `propertyName`
: Üzerinde çalışılacak obje ve özellik.

`descriptor`
: Uygulanacak özellik tanımlayıcı

Eğer özellik var ise `defineProperty` bu özelliğin bayraklarını günceller. Diğer türlü, bu özelliği yaratır ve verilen bayrakları ayarlar. Bu durumda eğer bayrak verilmemiş ise `false` kabul edilir.

Örneğin, aşağıda tüm bayrakları `false` olarak tanımlanmış bir `name` özelliğini görmektesiniz.

```js run
let user = {};

*!*
Object.defineProperty(user, "name", {
  value: "John"
});
*/!*

let descriptor = Object.getOwnPropertyDescriptor(user, 'name');

alert( JSON.stringify(descriptor, null, 2 ) );
/*
{
  "value": "John",
*!*
  "writable": false,
  "enumerable": false,
  "configurable": false
*/!*
}
 */
```
Bunu "normal yoll" yaratılmış `user.name` ile karşılaştırdığınızda tüm bayrakların `false` olduğunu görebilirsiniz. Eğer bizim istediğimiz bu değilse bunları `true` yapmakta fayda var.

Şimdi bu bayrakların etkilerini inceleyebiliriz.

## Salt Oku

`user.name`'i sadece okunabilir yapmak için `writable` bayrağının değiştirilmesi gerekir.

```js run
let user = {
  name: "John"
};

Object.defineProperty(user, "name", {
*!*
  writable: false
*/!*
});

*!*
user.name = "Pete"; // Error: Salt okunur özelliğe değer atanamaz.
*/!*
```

Now no one can change the name of our user, unless he applies his own `defineProperty` to override ours.

Here's the same operation, but for the case when a property doesn't exist:

```js run
let user = { };

Object.defineProperty(user, "name", {
*!*
  value: "Pete",
  // for new properties need to explicitly list what's true
  enumerable: true,
  configurable: true
*/!*
});

alert(user.name); // Pete
user.name = "Alice"; // Error
```


## Non-enumerable

Now let's add a custom `toString` to `user`.

Normally, a built-in `toString` for objects is non-enumerable, it does not show up in `for..in`. But if we add `toString` of our own, then by default it shows up in `for..in`, like this:

```js run
let user = {
  name: "John",
  toString() {
    return this.name;
  }
};

// By default, both our properties are listed:
for(let key in user) alert(key); // name, toString
```

If we don't like it, then we can set `enumerable:false`. Then it won't appear in `for..in` loop, just like the built-in one:

```js run
let user = {
  name: "John",
  toString() {
    return this.name;
  }
};

Object.defineProperty(user, "toString", {
*!*
  enumerable: false
*/!*
});

*!*
// Now our toString disappears:
*/!*
for(let key in user) alert(key); // name
```

Non-enumerable properties are also excluded from `Object.keys`:

```js
alert(Object.keys(user)); // name
```

## Non-configurable

The non-configurable flag (`configurable:false`) is sometimes preset for built-in objects and properties.

A non-configurable property can not be deleted or altered with `defineProperty`.

For instance, `Math.PI` is both read-only, non-enumerable and non-configurable:

```js run
let descriptor = Object.getOwnPropertyDescriptor(Math, 'PI');

alert( JSON.stringify(descriptor, null, 2 ) );
/*
{
  "value": 3.141592653589793,
  "writable": false,
  "enumerable": false,
  "configurable": false
}
*/
```
So, a programmer is unable to change the value of `Math.PI` or overwrite it.

```js run
Math.PI = 3; // Error

// delete Math.PI won't work either
```

Making a property non-configurable is a one-way road. We cannot change it back, because `defineProperty` doesn't work on non-configurable properties.

Here we are making `user.name` a "forever sealed" constant:

```js run
let user = { };

Object.defineProperty(user, "name", {
  value: "John",
  writable: false,
  configurable: false
});

*!*
// won't be able to change user.name or its flags
// all this won't work:
//   user.name = "Pete"
//   delete user.name
//   defineProperty(user, "name", ...)
Object.defineProperty(user, "name", {writable: true}); // Error
*/!*
```

```smart header="Errors appear only in use strict"
In the non-strict mode, no errors occur when writing to read-only properties and such. But the operation still won't succeed. Flag-violating actions are just silently ignored in non-strict.
```

## Object.defineProperties

There's a method [Object.defineProperties(obj, descriptors)](mdn:js/Object/defineProperties) that allows to define many properties at once.

The syntax is:

```js
Object.defineProperties(obj, {
  prop1: descriptor1,
  prop2: descriptor2
  // ...
});
```

For instance:

```js
Object.defineProperties(user, {
  name: { value: "John", writable: false },
  surname: { value: "Smith", writable: false },
  // ...
});
```

So, we can set many properties at once.

## Object.getOwnPropertyDescriptors

To get all property descriptors at once, we can use the method [Object.getOwnPropertyDescriptors(obj)](mdn:js/Object/getOwnPropertyDescriptors).

Together with `Object.defineProperties` it can be used as a "flags-aware" way of cloning an object:

```js
let clone = Object.defineProperties({}, Object.getOwnPropertyDescriptors(obj));
```

Normally when we clone an object, we use an assignment to copy properties, like this:

```js
for(let key in user) {
  clone[key] = user[key]
}
```

...But that does not copy flags. So if we want a "better" clone then `Object.defineProperties` is preferred.

Another difference is that `for..in` ignores symbolic properties, but `Object.getOwnPropertyDescriptors` returns *all* property descriptors including symbolic ones.

## Sealing an object globally

Property descriptors work at the level of individual properties.

There are also methods that limit access to the *whole* object:

[Object.preventExtensions(obj)](mdn:js/Object/preventExtensions)
: Forbids to add properties to the object.

[Object.seal(obj)](mdn:js/Object/seal)
: Forbids to add/remove properties, sets for all existing properties `configurable: false`.

[Object.freeze(obj)](mdn:js/Object/freeze)
: Forbids to add/remove/change properties, sets for all existing properties `configurable: false, writable: false`.

And also there are tests for them:

[Object.isExtensible(obj)](mdn:js/Object/isExtensible)
: Returns `false` if adding properties is forbidden, otherwise `true`.

[Object.isSealed(obj)](mdn:js/Object/isSealed)
: Returns `true` if adding/removing properties is forbidden, and all existing properties have `configurable: false`.

[Object.isFrozen(obj)](mdn:js/Object/isFrozen)
: Returns `true` if adding/removing/changing properties is forbidden, and all current properties are `configurable: false, writable: false`.

These methods are rarely used in practice.
