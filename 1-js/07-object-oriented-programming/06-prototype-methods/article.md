
# Metodlar ve prototipler

Bu bölümde prototipler ile çalışmak için ek metodlardan bahsedeceğiz

Bizim bildiğimizin haricinde prototipi ayarlamak ve almak için başka yöntemler de bulunmaktadır:

- [Object.create(proto[, descriptors])](mdn:js/Object/create) -- verilen `proto`'yu `[[Prototype]]` şeklinde alarak ve opsiyonel tanımlayıcı özelliği kullanarak boş bir obje oluşturur.
- [Object.getPrototypeOf(obj)](mdn:js/Object.getPrototypeOf) -- `obj`'nin `[[Prototype]]`'ını döndürür.
- [Object.setPrototypeOf(obj, proto)](mdn:js/Object.setPrototypeOf) -- `obj`'nin `[[Prototype]]`'ını `proto`'ya ayarlar.

[cut]

Örneğin:

```js run
let animal = {
  eats: true
};

// animal prototipi ile yeni bir rabbit objesi yaratma.
*!*
let rabbit = Object.create(animal);
*/!*

alert(rabbit.eats); // true
*!*
alert(Object.getPrototypeOf(rabbit) === animal); // rabbit'in prototipini alma
*/!*

*!*
Object.setPrototypeOf(rabbit, {}); // rabbit'in prototipini {}'e çevirme.
*/!*
```
`Object.create` opsiyonel olarak ikinci bir argümana sahiptir: özellik tanımlayıcı. Aşağıdaki gibi yeni objeye özellikler ekleyebiliriz:

```js run
let animal = {
  eats: true
};

let rabbit = Object.create(animal, {
  jumps: {
    value: true
  }
});

alert(rabbit.jumps); // true
```
Tanımlayıcıların <info:property-descriptors> bölümünde üstünden geçilmiştir. Formatları aynıdır.
`Object.create` kullanarak obje klonlama işlemini yapabiliriz. Bu objenin özelliklerini `for..in` ile dolanmaktan daha etkin bir yöntemdir.

```js
// Objenin yüzeysel klonu
let clone = Object.create(Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj));
```
Bu tam olarak `obj`'nin aynısını verir. Tüm özellikler: dönülebilir veya dönülemez, veri özellikleri, alıcı ve ayarlayıcılar --  herşey, ayrıca doğru `[[Prototype]]` ile

## Tarihçe

`[[Prototype]]`'ı ayarlayabileceğimiz yöntemleri saymaya kalsak baya zorluk yaşarız. Çok fazla yöntem bulunmaktadır.

Neden?

Birçok nedeni bulunmaktadır.

- Yapıcının `"prototype"` özelliği ilk javascript ortaya çıktığından beri bulunmaktadır.
- 2012'de: `Object.create` bir standart olarak oturdu. Bu verilen prototip ile objeleri yaratmaya izin verdi. Fakat bunları almaya veya ayarlamaya değil. Bundan dolayı tarayıcılar bir standart olmayan `__proto__` erişimcilerini uygulayarak alıcı ve ayarlayıcılara ( get/set)'e izin verdi.
- 2015'te: `Object.setPrototypeOf` ve `Object.getPrototypeOf` bir standart olarak eklendi. `__proto__` defakto şeklinde aslında her yerde kullanılmıştı, Bundan dolayı çokta kulllanılan özellikler olmadı, sadece tarayıcı harici çevrelerde kullanılır oldu.

Artık bunların hepsi bizim kullanımımızdadır.

Teknik olarak `[[Prototype]]`'ı istediğimiz an alma/ayarlama işi yapabiliriz. Fakat genelde bunu sadece obje yaratırken kullanır ve daha sonra düzenleme yapmayız: `rabbit`, `animal`dan kalıtım alır fakat onu değiştirmez. JavaScript motorları da bunu yüksek derecede optimize edebilir. Prototipi `Object.setPrototypeOf` veya `obj.__proto__` ile sonradan değiştirmek oldukça yavaş bir operasyondur. Ama mümkündür.

## "En basit" Objeler

Bildiğiniz gibi objeler anahtar/değer ikilisini tutan ilişkisel dizi şeklinde kullanılabilir.

...Eğer içerisinde *kullanıcı-kaynaklı*  anahtarlar ( örneğin kullanıcının girdiği sözlük tipi veriler) var ise, ilginç bir aksaklık meydana gelir: `"__proto_"` haricinde tüm anahtarlar doğru çalışır.

Örneğin:

```js run
let obj = {};

let key = prompt("What's the key?", "__proto__");
obj[key] = "some value";

alert(obj[key]); // [object Object] olarak döner ,  "some value" değil!
```
Eğer kullanıcı tipleri `__proto__` içerisinde ise atama görmezden gelinir!

Bu aslında çok da sürpriz olmasa gerek. `__proto__` farklı bir özelliktir: Ya obje olur veya `null`,  mesela bir karakter dizisi prototip olamaz.

Fakat buradaki amacımız böyle bir davranışı uygulamak değildi, değil mi? Biz key/value ikililerini kaydetmekti. `"__proto__"` anahtarı düzgün bir şekilde kaydedilmedi. Bundan dolayı bu bir bugdır. Burada etkisi berbat değildir fakat diğer durumlarda prototip gerçekten değişebilir ve çalışma tamamen istenmeyen bir sonuca varabilir.

Daha kötüsü -- genelde geliştiriciler böyle bir ihtimali düşünmezler bile. Bundan dolayı böyle bir bug'lar fark edilebilir ve saldırıya açık hale gelirler, özellikle JavaScript server tarafında kullanıldıysa.

Böyle bir olay sadece `__proto__`'da meydana gelir diğer tüm özellikler normalde "atanabilir"'dir.

Bu problemden nasıl kaçınılabilir?

Öncelikle `Map` kullanılabilir, herşey doğru çalışır.

Fakat burada bize `Obje` yardımcı olabilir, çünkü dili yaratıcılar bu konuları uzun zaman önce düşünmüşler.

`__proto__` objenin bir özelliği değildir. Fakat `Object.prototype`'a erişimsağlar( accessor ):

![](object-prototype-2.png)

Bundan dolayı, Eğer `obj.__proto__` okunur veya atanırsa, ilgili alıcı/ayarlayıcı prototipten çağırılır, böylece `[[Prototoy]]` alınır/ayarlanır.

Başlangıçta `__proto__` , `[[Prototype]]`a erişmek için bir yol olarak tanımlanmıştır, `[[Prototype]]` değil.

Eğer, eğer objeyi ilişkisel dizi olarak kullanmak istiyorsanız şu şekilde yapabilirsiniz:

```js run
*!*
let obj = Object.create(null);
*/!*

let key = prompt("What's the key?", "__proto__");
obj[key] = "some value";

alert(obj[key]); // "some value"
```

`Object.create(null)`  prototip'i olmayan boş bir obje yaratır.(`[[Prototype]]` null'dur):

![](object-prototype-null.png)

Bundan dolayı `__proto__`  için atadan kalan alıcı/ayarlayıcı bulunmamaktadır. Artık sıradan bir veri özelliği olarak işlenir, bundan dolayı yukarıdaki örnek doğru bir şekilde çalışır.

Böyle objelere "en basit" veya "saf sözlük objeleri" denir, Çünkü bunlar sıradan objelerden `{...}` bile daha basittirler.

Bu objelerin kötü tarafı ise, içind hiç bir varsayılan metod bulunmaz, Örneğin: `toString`:

```js run
*!*
let obj = Object.create(null);
*/!*

alert(obj); // Error (no toString)
```

...But that's usually fine for associative arrays.

Please note that most object-related methods are `Object.something(...)`, like `Object.keys(obj)` -- they are not in the prototype, so they will keep working on such objects:


```js run
let chineseDictionary = Object.create(null);
chineseDictionary.hello = "ni hao";
chineseDictionary.bye = "zai jian";

alert(Object.keys(chineseDictionary)); // hello,bye
```

## Getting all properties

There are many ways to get keys/values from an object.

We already know these ones:

- [Object.keys(obj)](mdn:js/Object/keys) / [Object.values(obj)](mdn:js/Object/values) / [Object.entries(obj)](mdn:js/Object/entries) -- returns an array of enumerable own string property names/values/key-value pairs. These methods only list *enumerable* properties, and those that have *strings as keys*.

If we want symbolic properties:

- [Object.getOwnPropertySymbols(obj)](mdn:js/Object/getOwnPropertySymbols) -- returns an array of all own symbolic property names.

If we want non-enumerable properties:

- [Object.getOwnPropertyNames(obj)](mdn:js/Object/getOwnPropertyNames) -- returns an array of all own string property names.

If we want *all* properties:

- [Reflect.ownKeys(obj)](mdn:js/Reflect/ownKeys) -- returns an array of all own property names.

These methods are a bit different about which properties they return, but all of them operate on the object itself. Properties from the prototype are not listed.

The `for..in` loop is different: it loops over inherited properties too.

For instance:

```js run
let animal = {
  eats: true
};

let rabbit = {
  jumps: true,
  __proto__: animal
};

*!*
// only own keys
alert(Object.keys(rabbit)); // jumps
*/!*

*!*
// inherited keys too
for(let prop in rabbit) alert(prop); // jumps, then eats
*/!*
```

If we want to distinguish inherited properties, there's a built-in method [obj.hasOwnProperty(key)](mdn:js/Object/hasOwnProperty): it returns `true` if `obj` has its own (not inherited) property named `key`.

So we can filter out inherited properties (or do something else with them):

```js run
let animal = {
  eats: true
};

let rabbit = {
  jumps: true,
  __proto__: animal
};

for(let prop in rabbit) {
  let isOwn = rabbit.hasOwnProperty(prop);
  alert(`${prop}: ${isOwn}`); // jumps:true, then eats:false
}
```
Here we have the following inheritance chain: `rabbit`, then `animal`, then `Object.prototype` (because `animal` is a literal object `{...}`, so it's by default), and then `null` above it:

![](rabbit-animal-object.png)

Note, there's one funny thing. Where is the method `rabbit.hasOwnProperty` coming from? Looking at the chain we can see that the method is provided by `Object.prototype.hasOwnProperty`. In other words, it's inherited.

...But why `hasOwnProperty` does not appear in `for..in` loop, if it lists all inherited properties?  The answer is simple: it's not enumerable. Just like all other properties of `Object.prototype`. That's why they are not listed.

## Summary

Here's a brief list of methods we discussed in this chapter -- as a recap:

- [Object.create(proto[, descriptors])](mdn:js/Object/create) -- creates an empty object with given `proto` as `[[Prototype]]` (can be `null`) and optional property descriptors.
- [Object.getPrototypeOf(obj)](mdn:js/Object.getPrototypeOf) -- returns the `[[Prototype]]` of `obj` (same as `__proto__` getter).
- [Object.setPrototypeOf(obj, proto)](mdn:js/Object.setPrototypeOf) -- sets the `[[Prototype]]` of `obj` to `proto` (same as `__proto__` setter).
- [Object.keys(obj)](mdn:js/Object/keys) / [Object.values(obj)](mdn:js/Object/values) / [Object.entries(obj)](mdn:js/Object/entries) -- returns an array of enumerable own string property names/values/key-value pairs.
- [Object.getOwnPropertySymbols(obj)](mdn:js/Object/getOwnPropertySymbols) -- returns an array of all own symbolic property names.
- [Object.getOwnPropertyNames(obj)](mdn:js/Object/getOwnPropertyNames) -- returns an array of all own string property names.
- [Reflect.ownKeys(obj)](mdn:js/Reflect/ownKeys) -- returns an array of all own property names.
- [obj.hasOwnProperty(key)](mdn:js/Object/hasOwnProperty): it returns `true` if `obj` has its own (not inherited) property named `key`.

We also made it clear that `__proto__` is a getter/setter for `[[Prototype]]` and resides in `Object.prototype`, just as other methods.

We can create an object without a prototype by `Object.create(null)`. Such objects are used as "pure dictionaries", they have no issues with `"__proto__"` as the key.

All methods that return object properties (like `Object.keys` and others) -- return "own" properties. If we want inherited ones, then we can use `for..in`.
