# JSON metodları, toJSON

Diyelimki karmaşık bir yapı var, bunu karakter dizisine çevirip ağ üzerinden loglanması için başka bir yere iletilmek isteniyor.

Doğal olarak, bu karakter dizisi tüm önemli özellikleri içermeli

Bu çevirim şu şekilde yapılabilir:

```js run
let kullanici = {
  adi: "Ahmet",
  yasi: 30,

*!*
  toString() {
    return `{adi: "${this.adi}", yasi: ${this.yasi}}`;
  }
*/!*
};

alert(kullanici); // {adi: "Ahmet", yasi: 30}
```

... Fakat geliştirme esnasında yeni özellikler eklendi ve öncekiler ya silindi ya da isim değiştirdi. Böyle bir durumda `toString` metoduyla her zaman değişiklik yapmak oldukça zordur. Özellikleri döngüye sokup buradan değerler alınabilir. Bu durumda da iç içe objelere ne olacak? Bunlarında çevirimlerini yapmak gerekir. Ayrıca ağ üzerinden objeyi göndermeye çalıştığınızda ayrıca bu objenin alan yer tarafından nasıl okunacağına dair bilgi göndermek zorundasınız.

Neyseki bunların hiç biri için kod yazmaya gerek yok. Bu problem bizim için çözülmüş durumda.

[cut]

## JSON.stringify

[JSON](http://en.wikipedia.org/wiki/JSON) (JavaScript Object Notation) genelde objelerin değerlerini ifade eder.[RFC 4627](http://tools.ietf.org/html/rfc4627) standardında tanımı yapılmıştır. Öncelikle JavaScript düşünülerek yapılmış olsa da birçok dil de kendine has kütüphanelerle JSON desteği vermektedir. Böylece client JavaScript kullnırken server Ruby/PHP/Java/Herneyse... kullansa bile JSON kullanımında bir sorun oluşturmaz.

JavaScript aşağıdaki metodları destekler:

- `JSON.stringify` objeyi JSON'a çevirir.
- `JSON.parse` JSON'dan objeye çevirmeye yarar.

Örneğin, aşağıda `JSON.stringify` metodu ögrenci objesi için kullanılmıştır:

```js run
let ogrenci = {
  adi: 'Ahmet',
  yasi: 30,
  adminMi: false,
  dersler: ['html', 'css', 'js'],
  esi: null
};

*!*
let json = JSON.stringify(ogrenci);
*/!*

alert(typeof json); // string dönecektir.!

alert(json);
*!*
/* JSON'a çevirilmiş obje:
{
  "adi": 'Ahmet',
  "yasi": 30,
  "adminMi": false,
  "dersler": ['html', 'css', 'js'],
  "esi": null
}
*/
*/!*
```
`JSON.stringify(ogrenci)` metodu objeyi alır ve bunu karaktere çevirir, buna *Json-kodlanmış* , *seri hale getirilmiş* veya *karakter haline getirilmiş* denir. Bunu ağ üzerinden karşı tarafa göndermek veya basit bir şekilde kaydetmek mümkündür.

JSON kodlanmış objenin normal obje ile arasında bir kaç tane önemli farklılık vardır:

- Karakterler çift tırnak kullanır. JSON'da tek tırnak veya ters tırnak kıllanılmaz. Bundan dolayı `'Ahmet'` -> `"Ahmet"` olur. 
- Obje özelliklerinin isimleri de çift tırnak içinde alınır. Bu da zorunludur. Bundan dolay `yas:30` , `"yas"`:30'olur.

`JSON.stringify` ilkel tiplere de uygulanabilir.

Desteklenen JSON tipleri:

- Objeler `{ ... }`
- Diziler `[ ... ]`
- İlkel Tipler:
    - karakterler,
    - sayılar,
    - boolean değerler `true/false`,
    - `null`.

Örneğin:

```js run
// normal bir sayı JSOn için de normal bir sayıdır.
alert( JSON.stringify(1) ) // 1

// karakterler de JSON içinde karakterdir fakat çift tırnak içinde gösterilir.
alert( JSON.stringify('test') ) // "test"

alert( JSON.stringify(true) ); // true

alert( JSON.stringify([1, 2, 3]) ); // [1,2,3]
```
JSON sadece veriyi tanımlayan diller arası bir şartname bulunmaktadır. Bundan dolayı Javascript'e özel obje özelliklerikleri `JSON.stringify` tarafından pas geçilir.

Yani:

- Fonksiyon özellikleri ( metodlar ).
- Sembolik özellikler.
- `undefined`'ı saklayan özellikler.

```js run
let kullanici = {
  merhaba() { // ihmal edilir
    alert("Merhaba");
  },
  [Symbol("id")]: 123, // ihmal edilir
  baska: undefined // ihmal edilir
};

alert( JSON.stringify(kullanici) ); // {} (boş obje)
```
Bu özellik kabul edilebilir. Eğer istediğiniz bu değilse, bu işlemi nasıl özelleştirebilirsiniz bunu göreceksiniz.

Harika olan ise iç içe objeler otomatik olarak çevrilir.

Örneğin:

```js run
let tanisma = {
  baslik: "Konferans",
*!*
  oda: {
    sayi: 123,
    katilimcilar: ["ahmet", "mehmet"]
  }
*/!*
};

alert( JSON.stringify(tanisma) );
/* tüm yapı karakter şekline çevrildi:
{
  "tanisma":"baslik",
  "oda":{"sayi":23,"katilimcilar":["ahmet","mehmet"]},
}
*/
```
Önemli bir sınırlama: Dairesel referans olmamalıdır.

Örneğin:

```js run
let oda = {
  sayi: 23
};

let tanisma = {
  baslik: "Konferans",
  katilimcilar: ["ahmet", "mehmet"]
};

tanisma.yeri = oda;       // tanisma odaya referans veriyor.
oda.dolduruldu = tanisma; // oda tanismaya referans veriyor

*!*
JSON.stringify(meetup); // Hata: Dairesel yapı JSON'a çevrilememiştir.
*/!*
```
Çeviri yapılırken hata olmasının nedeni: `oda.dolduruldu` `tanisma`'ya referans olurken. `tanisma.yeri` `oda`'ya referans verir.

![](json-meetup.png)


## Excluding and transforming: replacer

The full syntax of `JSON.stringify` is:

```js
let json = JSON.stringify(value[, replacer, space])
```

value
: A value to encode.

replacer
: Array of properties to encode or a mapping function `function(key, value)`.

space
: Amount of space to use for formatting

Most of time, `JSON.stringify` is used with first argument only. But if we need to fine-tune the replacement process, like to filter out circular references, we can use the second argument of `JSON.stringify`.

If we pass an array of properties to it, only these properties will be encoded.

For instance:

```js run
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  participants: [{name: "John"}, {name: "Alice"}],
  place: room // meetup references room
};

room.occupiedBy = meetup; // room references meetup

alert( JSON.stringify(meetup, *!*['title', 'participants']*/!*) );
// {"title":"Conference","participants":[{},{}]}
```

Here we are probably too strict. The property list is applied to the whole object structure. So participants are empty, because `name` is not in the list.

Let's include every property except `room.occupiedBy` that would cause the circular reference:

```js run
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  participants: [{name: "John"}, {name: "Alice"}],
  place: room // meetup references room
};

room.occupiedBy = meetup; // room references meetup

alert( JSON.stringify(meetup, *!*['title', 'participants', 'place', 'name', 'number']*/!*) );
/*
{
  "title":"Conference",
  "participants":[{"name":"John"},{"name":"Alice"}],
  "place":{"number":23}
}
*/
```

Now everything except `occupiedBy` is serialized. But the list of properties is quite long.

Fortunately, we can use a function instead of an array as the `replacer`.

The function will be called for every `(key,value)` pair and should return the "replaced" value, which will be used instead of the original one.

In our case, we can return `value` "as is" for everything except `occupiedBy`. To ignore `occupiedBy`, the code below returns `undefined`:

```js run
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  participants: [{name: "John"}, {name: "Alice"}],
  place: room // meetup references room
};

room.occupiedBy = meetup; // room references meetup

alert( JSON.stringify(meetup, function replacer(key, value) {
  alert(`${key}: ${value}`); // to see what replacer gets
  return (key == 'occupiedBy') ? undefined : value;
}));

/* key:value pairs that come to replacer:
:             [object Object]
title:        Conference
participants: [object Object],[object Object]
0:            [object Object]
name:         John
1:            [object Object]
name:         Alice
place:        [object Object]
number:       23
*/
```

Please note that `replacer` function gets every key/value pair including nested objects and array items. It is applied recursively. The value of `this` inside `replacer` is the object that contains the current property.

The first call is special. It is made using a special "wrapper object": `{"": meetup}`. In other words, the first `(key,value)` pair has an empty key, and the value is the target object as a whole. That's why the first line is `":[object Object]"` in the example above.

The idea is to provide as much power for `replacer` as possible: it has a chance to analyze and replace/skip the whole object if necessary.


## Formatting: spacer

The third argument of `JSON.stringify(value, replacer, spaces)` is the number of spaces to use for pretty formatting.

Previously, all stringified objects had no indents and extra spaces. That's fine if we want to send an object over a network. The `spacer` argument is used exclusively for a nice output.

Here `spacer = 2` tells JavaScript to show nested objects on multiple lines, with indentation of 2 spaces inside an object:

```js run
let user = {
  name: "John",
  age: 25,
  roles: {
    isAdmin: false,
    isEditor: true
  }
};

alert(JSON.stringify(user, null, 2));
/* two-space indents:
{
  "name": "John",
  "age": 25,
  "roles": {
    "isAdmin": false,
    "isEditor": true
  }
}
*/

/* for JSON.stringify(user, null, 4) the result would be more indented:
{
    "name": "John",
    "age": 25,
    "roles": {
        "isAdmin": false,
        "isEditor": true
    }
}
*/
```

The `spaces` parameter is used solely for logging and nice-output purposes.

## Custom "toJSON"

Like `toString` for a string conversion, an object may provide method `toJSON` for to-JSON conversion. `JSON.stringify` automatically calls it if available.

For instance:

```js run
let room = {
  number: 23
};

let meetup = {
  title: "Conference",
  date: new Date(Date.UTC(2017, 0, 1)),
  room
};

alert( JSON.stringify(meetup) );
/*
  {
    "title":"Conference",
*!*
    "date":"2017-01-01T00:00:00.000Z",  // (1)
*/!*
    "room": {"number":23}               // (2)
  }
*/
```

Here we can see that `date` `(1)` became a string. That's because all dates have a built-in `toJSON` method which returns such kind of string.

Now let's add a custom `toJSON` for our object `room`:

```js run
let room = {
  number: 23,
*!*
  toJSON() {
    return this.number;
  }
*/!*
};

let meetup = {
  title: "Conference",
  room
};

*!*
alert( JSON.stringify(room) ); // 23
*/!*

alert( JSON.stringify(meetup) );
/*
  {
    "title":"Conference",
*!*
    "room": 23
*/!*
  }
*/
```

As we can see, `toJSON` is used both for the direct call `JSON.stringify(room)` and for the nested object.


## JSON.parse

To decode a JSON-string, we need another method named [JSON.parse](mdn:js/JSON/parse).

The syntax:
```js
let value = JSON.parse(str[, reviver]);
```

str
: JSON-string to parse.

reviver
: Optional function(key,value) that will be called for each `(key,value)` pair and can transform the value.

For instance:

```js run
// stringified array
let numbers = "[0, 1, 2, 3]";

numbers = JSON.parse(numbers);

alert( numbers[1] ); // 1
```

Or for nested objects:

```js run
let user = '{ "name": "John", "age": 35, "isAdmin": false, "friends": [0,1,2,3] }';

user = JSON.parse(user);

alert( user.friends[1] ); // 1
```

The JSON may be as complex as necessary, objects and arrays can include other objects and arrays. But they must obey the format.

Here are typical mistakes in hand-written JSON (sometimes we have to write it for debugging purposes):

```js
let json = `{
  *!*name*/!*: "John",                     // mistake: property name without quotes
  "surname": *!*'Smith'*/!*,               // mistake: single quotes in value (must be double)
  *!*'isAdmin'*/!*: false                  // mistake: single quotes in key (must be double)
  "birthday": *!*new Date(2000, 2, 3)*/!*, // mistake: no "new" is allowed, only bare values
  "friends": [0,1,2,3]              // here all fine
}`;
```

Besides, JSON does not support comments. Adding a comment to JSON makes it invalid.

There's another format named [JSON5](http://json5.org/), which allows unquoted keys, comments etc. But this is a standalone library, not in the specification of the language.

The regular JSON is that strict not because its developers are lazy, but to allow easy, reliable and very fast implementations of the parsing algorithm.

## Using reviver

Imagine, we got a stringified `meetup` object from the server.

It looks like this:

```js
// title: (meetup title), date: (meetup date)
let str = '{"title":"Conference","date":"2017-11-30T12:00:00.000Z"}';
```

...And now we need to *deserialize* it, to turn back into JavaScript object.

Let's do it by calling `JSON.parse`:

```js run
let str = '{"title":"Conference","date":"2017-11-30T12:00:00.000Z"}';

let meetup = JSON.parse(str);

*!*
alert( meetup.date.getDate() ); // Error!
*/!*
```

Whoops! An error!

The value of `meetup.date` is a string, not a `Date` object. How could `JSON.parse` know that it should transform that string into a `Date`?

Let's pass to `JSON.parse` the reviving function that returns all values "as is", but `date` will become a `Date`:

```js run
let str = '{"title":"Conference","date":"2017-11-30T12:00:00.000Z"}';

*!*
let meetup = JSON.parse(str, function(key, value) {
  if (key == 'date') return new Date(value);
  return value;
});
*/!*

alert( meetup.date.getDate() ); // now works!
```

By the way, that works for nested objects as well:

```js run
let schedule = `{
  "meetups": [
    {"title":"Conference","date":"2017-11-30T12:00:00.000Z"},
    {"title":"Birthday","date":"2017-04-18T12:00:00.000Z"}
  ]
}`;

schedule = JSON.parse(schedule, function(key, value) {
  if (key == 'date') return new Date(value);
  return value;
});

*!*
alert( schedule.meetups[1].date.getDate() ); // works!
*/!*
```



## Summary

- JSON is a data format that has its own independent standard and libraries for most programming languages.
- JSON supports plain objects, arrays, strings, numbers, booleans and `null`.
- JavaScript provides methods [JSON.stringify](mdn:js/JSON/stringify) to serialize into JSON and [JSON.parse](mdn:js/JSON/parse) to read from JSON.
- Both methods support transformer functions for smart reading/writing.
- If an object has `toJSON`, then it is called by `JSON.stringify`.
