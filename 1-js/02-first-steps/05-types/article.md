# Data types
# Veri Tipleri

Bir javascript değişkeni her türlü veriyi tutabilir. Önce karakter dizisi(String) atanabilir sonra sayısal değer alabilir.

```js
// Hata yok
let mesaj = "merhaba";
mesaj = 123456;
```

Bu şekilde olaylara izin veren tipdeki dillere "dinamik tip" dil denir. Veri yapıları olsa bile değişkenler bu yapılara bağlı değildir.

JavaScript dilinde yedi farklı veri tipi bulunmaktadır. Şimdilik bu tiplerden bahsedeceğiz gelecek bölümlerde ise daha derinlemesine bu tipleri inceleyeceğiz.


[cut]

##  Number - Sayı

```js
let s = 123;
s = 12.345;
```

*sayı* hem integer hem de floating point sayıları için kullanılır. Sayılar `*`, `/`, `+` veya `-` işlemlerine girebilirler.
Normal sayıların haricinde "özel sayısal değerler" de sayı olarak tanımlanabilir. Bunlar : `Infinity`, `-Infinity` ve `NaN` gibi değerlerdir.


- `Infinity` matematiksel sonsuzluğu ifade eder.
- `Infinity` represents the mathematical [Sonsuz](https://tr.wikipedia.org/wiki/Sonsuz) ∞. Diğer tüm sayılardan büyük olan özel bir sayıdır.

    0'a bölünmede sonuç sonsuzu verir:

    ```js run
    alert( 1 / 0 ); // Infinity
    ```

    veya doğrudan ekranda gösterebilirsiniz:

    ```js run
    alert( Infinity ); // Infinity
    ```
- `NaN` hesaplamalarda bir hata olduğunu gösterir. Hatalı veya tanımsız matematiksel hesapları gösterir, örneğin:

    ```js run
    alert( "Sayı Değil ( Not a Number) " / 2 ); // NaN, böyle bir bölme işlemi yapılamaz.
    ```

    `NaN` is sticky. Any further operation on `NaN` would give `NaN`:

    ```js run
    alert( "not a number" / 2 + 5 ); // NaN
    ```

    Öyleyse matematiksel işlemlerin herhangi bir yerinde `NaN` alınıyorsa bu hesabın tamamını etkiler.

```smart header="Matematiksel hesapların güvenliği"
JavaScript üzerinden matematik hesapları yapmak güvenlidir. Her işlemi yapabilirsiniz. 0'a bölme normal harf dizesini bir sayıya bölmeye çalışma vs.

Kodunuzun tamamı hiç durmadan çalışacaktır. En kötü ihtimalle `NaN` sonucunu alınır.
```
Özel sayısal değerler "number" tipine aittir. Tabiki sayı bizim bildiğimiz tipte sayı değillerdir. 
<info:number> bölümünde sayısal değerler ile çalışmayı daha derinlemesine göreceksiniz.



## String - Karakter Dizisi

JavaScriptte karakter dizileri çift tırnak içerisine alınmalıdır.

```js
let str = "Merhaba";
let str2 = 'Tek tırnak da çalışır';
let phrase = `değer gömülebilir ${str}`;
```
JavaScriptte 3 çeşit tırnak içine alma yöntemi vardır.

1. Çift tırnak: `"Hello"`.
2. Tek tırnak: `'Hello'`.
3. Ters tırnak: <code>&#96;Hello&#96;</code>.

Çift tırnak ile tek tırnak "basit" tırnaklardır. Aralarında bir farklılık yoktur.

Ters tırnak ise "genişletilmiş fonksiyonlu" tırnaktır. Bunu kullanarak karakter dizisi içerisine `${...}` gibi başka bir dizi yerleştirebiliriz. Örneğin:

```js run
let isim = "Ahmet";

// değişken gömme
alert( `Hello, *!*${isim}*/!*!` ); // Merhaba Ahmet!

// ifade gömme
alert( `sonuç : *!*${1 + 2}*/!*` ); //sonuç :  3
```

The expression inside `${…}` is evaluated and the result becomes a part of the string. We can put anything there: a variable like `name` or an arithmetical expression like `1 + 2` or something more complex.

Please note that this can only be done in backticks. Other quotes do not allow such embedding!
```js run
alert( "the result is ${1 + 2}" ); // the result is ${1 + 2} (double quotes do nothing)
```

We'll cover strings more thoroughly in the chapter <info:string>.

```smart header="There is no *character* type."
In some languages, there is a special "character" type for a single character. For example, in the C language and in Java it is `char`.

In JavaScript, there is no such type. There's only one type: `string`. A string may consist of only one character or many of them.
```

## A boolean (logical type)

The boolean type has only two values: `true` and `false`.

This type is commonly used to store yes/no values: `true` means "yes, correct", and `false` means "no, incorrect".

For instance:

```js
let nameFieldChecked = true; // yes, name field is checked
let ageFieldChecked = false; // no, age field is not checked
```

Boolean values also come as a result of comparisons:

```js run
let isGreater = 4 > 1;

alert( isGreater ); // true (the comparison result is "yes")
```

We'll cover booleans more deeply later in the chapter <info:logical-operators>.

## The "null" value

The special `null` value does not belong to any type of those described above.

It forms a separate type of its own, which contains only the `null` value:

```js
let age = null;
```

In JavaScript `null` is not a "reference to a non-existing object" or a "null pointer" like in some other languages.

It's just a special value which has the sense of "nothing", "empty" or "value unknown".

The code above states that the `age` is unknown or empty for some reason.

## The "undefined" value

The special value `undefined` stands apart. It makes a type of its own, just like `null`.

The meaning of `undefined` is "value is not assigned".

If a variable is declared, but not assigned, then its value is exactly `undefined`:

```js run
let x;

alert(x); // shows "undefined"
```

Technically, it is possible to assign `undefined` to any variable:

```js run
let x = 123;

x = undefined;

alert(x); // "undefined"
```

...But it's not recommended to do that. Normally, we use `null` to write an "empty" or an "unknown" value into the variable, and `undefined` is only used for checks, to see if the variable is assigned or similar.

## Objects and Symbols

The `object` type is special.

All other types are called "primitive", because their values can contain only a single thing (be it a string or a number or whatever). In contrast, objects are used to store collections of data and more complex entities. We'll deal with them later in the chapter <info:object> after we know enough about primitives.

The `symbol` type is used to create unique identifiers for objects. We have to mention it here for completeness, but it's better to study them after objects.

## The typeof operator [#type-typeof]

The `typeof` operator returns the type of the argument. It's useful when we want to process values of different types differently, or just want to make a quick check.

It supports two forms of syntax:

1. As an operator: `typeof x`.
2. Function style: `typeof(x)`.

In other words, it works both with parentheses or without them. The result is the same.

The call to `typeof x` returns a string with the type name:

```js
typeof undefined // "undefined"

typeof 0 // "number"

typeof true // "boolean"

typeof "foo" // "string"

typeof Symbol("id") // "symbol"

*!*
typeof Math // "object"  (1)
*/!*

*!*
typeof null // "object"  (2)
*/!*

*!*
typeof alert // "function"  (3)
*/!*
```

The last three lines may need additional explanations:

1. `Math` is a built-in object that provides mathematical operations. We will learn it in the chapter <info:number>. Here it serves just as an example of an object.
2. The result of `typeof null` is `"object"`. That's wrong. It is an officially recognized error in `typeof`, kept for compatibility. Of course, `null` is not an object. It is a special value with a separate type of its own. So, again, that's an error in the language.
3. The result of `typeof alert` is `"function"`, because `alert` is a function of the language. We'll study functions in the next chapters, and we'll see that there's no special "function" type in the language. Functions belong to the object type. But `typeof` treats them differently. Formally, it's incorrect, but very convenient in practice.


## Summary

There are 7 basic types in JavaScript.

- `number` for numbers of any kind: integer or floating-point.
- `string` for strings. A string may have one or more characters, there's no separate single-character type.
- `boolean` for `true`/`false`.
- `null` for unknown values -- a standalone type that has a single value `null`.
- `undefined` for unassigned values -- a standalone type that has a single value `undefined`.
- `object` for more complex data structures.
- `symbol` for unique identifiers.

The `typeof` operator allows us to see which type is stored in the variable.

- Two forms: `typeof x` or `typeof(x)`.
- Returns a string with the name of the type, like `"string"`.
- For `null` returns `"object"` -- that's an error in the language, it's not an object in fact.

In the next chapters we'll concentrate on primitive values and once we're familiar with them, then we'll move on to objects.
