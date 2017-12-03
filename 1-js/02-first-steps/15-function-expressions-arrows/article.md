# Fonksiyon ifadeleri ve oklar.

JavaScript'te fonksiyonlar "büyülü dil yapısı" değil sadece özel bir tip değerdir.
[cut]

Daha önceden *fonksiyon tanımlama* için aşağıdaki form kullanılmıştı.

```js
function selamVer() {
  alert( "Merhaba" );
}
```

Diğer bir şekilde de fonksiyon tanımlanabilir. Bu da *fonksiyon ifadesi* olarak adlandırılır.

Aşağıdaki gibi görünür:


```js
let selamVer = function() {
  alert( "Merhaba" );
};
```

Burada fonksiyon yaratıldı ve doğrudan değişkene atandı, tıpkı diğer dillerde olduğu gibi. Fonksiyonun nasıl tanımlandığına bakmaksızın, bu fonksiyon sadece `selamVer` içinde saklanan bir değerdir.

Yukarıdaki kod örneklerinin anlamları aynıdır: "bir fonksiyon yarat ve bunu `selamVer` değişkenine ata"

Hatta yazdığımız fonksiyonu `alert` ile ekrana basmak da mümkündür.

```js run
function selamVer() {
  alert( "Merhaba" );
}

*!*
alert( selamVer ); // fonksiyonun kodunu gösterir.
*/!*
```

Dikkat ederseniz son kodda `()` bulunmamaktadır. Bundan dolayı `selamVer` fonksiyonu çalışmayacaktır. Bazı dillerde ne zaman fonksiyonun ismini verseniz çalışır fakat JavaScript'te çalışabilmesi için `()` kullanmanız gerekmektedir.

JavaScript'te fonksiyonlar değer olduğundan dolayı bunlarla uğraşılabilir. Yukarıdaki kod ekrana kaynak kodunu basar.

Tabiki `selamVer()` diye çağırılabildiğinden dolayı özel bir değerdir.

Fakat yine de değerdir. Bundan dolayı diğer değerlerle uğraşıldığı gibi bununla da aynı şekilde çalışılabilir.

Örneğin bir fonksiyon başka bir değişkene kopyalanabilir.


```js run no-beautify
function selamVer() {   // (1) oluştur
  alert( "Merhaba" );
}

let func = selamVer;    // (2) kopyala

func(); // Merhaba     // (3) kopyası!
selamVer(); // Merhaba    //    kendisi.
```

Detayına bakılacak olursa:

1.`(1)` fonksiyon tanımlanır ve `selamVer` değişkenine atanır.
2. `(2)` bunu `func` değişkenine kopyalar.

    Tekrardan hatırlatmak gerekirse: `selamVer` etrafında parantez bulunmamaktadır. Eğer parantez ile yazılacak olsaydı `func = selamVer()`, `selamVer()` fonksiyonunun çıktısı `func` değişkenine atanırdı fonksiyon değil.

3. Fonksiyon bundan sonra `selamVer()` ve `func()` şeklinde çağırılabilir.
 
Ayrıca ilk satır için *fonksiyon ifadesi* de kullanılabilirdi:

```js
let selamVer = function() { ... };

let func = selamVer;
// ...
```

Herşey aynı olduğu gibi çalışırdı. Hatta neyin ne olduğu daha açık değil mi?


````smart header="Neden sonunda noktalı virgül var"

Aklınıza bir soru takılabilir. Neden *fonksiyon ifadesi*nin sonunda `;` bulunmakta fakat *fonksiyon tanımla*da kullanılmıyor:

```js
function selamVer() {
  // ...
}

let selamVer = function() {
  // ...
}*!*;*/!*
```


Cevap basit:
- Kod bloklarının sonunda `;` e gerek yoktur. Örneğin `if{ ...}`, `for{ ... }`, `for { }`, `function f{}` vs.
- Fonksiyon ifadesi bir ifade içinde kullanıldığından `let selamVer = ....;` bir değerdir. Kod bloğu değildir. Cümle sonlarında değer ne olursa olsun `;` kullanılması önerilir. Bundan dolayı `;` *fonksiyon ifadesi* ile alaklı değildir. Sadece tanımlamanın sonunu göstermek içindir. Tıpkı diğer tanımlamalarda olduğu gibi.
````

## Geriçağrım Fonksiyonları

Fonksiyonların değer olarak paslanması ve fonksiyon ifadelerini biraz daha incelenmesi yerinde olur.

`sor(soru,evet,hayir)` adında 3 parametre alan bir fonksiyon yazılacak olursa:

`soru`
: Soru cümlesi

`evet`
: Eğer doğru ise çalışacak fonksiyon

`hayir`
: Eğer cevap yanlış ise yapılacak fonksiyon

Fonksiyon `soru` sormalı, bu sorunun cevabına göre `evet()` veya `hayir()` fonksiyonları çağırılacaktır.


```js run
*!*
function sor(soru, evet, hayir) {
  if (confirm(soru)) yes()
  else no();
}
*/!*

function tamamGoster() {
  alert( "Kabul ettiniz" );
}

function iptalGoster() {
  alert( "Çalışmasını durdurdunuz" );
}
// kullanım: tamamGoster, iptalGoster fonksiyona parametre olarak gönderilmiştir.
sor("Kabul ediyor musunuz?", tamamGoster, iptalGoster);
```

Daha kısa yolunu yazmadan önce söylemek gerekir ki bu tür fonksiyonlar oldukça sıkça kullanılmaktadır. Gerçek hayattaki örnekleri ile yukarıdaki arasında fark ise gerçek hayatta basit bir `confirm` yerine daha karmaşık olaylar için kullanılıyor olmalarıdır. 

**`sor` fonksiyonunun argümanları *callbacks* veya *geri çağrım fonksiyonları* olarak adlandırılırlar.

Fikir fonksiyonu bizim baştan paslayıp ana fonksiyon içerieinde daha sonra duruma göre çağırılmasından kaynaklanmaktadır. Örneğe bakarsanız `tamamGoster` "evet" cevabı için *geri çağrım fonksiyonu*'dur.

Fonksiyon İfadesi kullanarak aynı fonksiyonu daha kısa bir şekilde yazmak mümkün:

```js run no-beautify
function sor(soru, evet, hayir) {
  if (confirm(soru)) evet()
  else hayir();
}

*!*
ask(
  "Kabul Ediyormusun?",
  function() { alert("Kabul ettin"); },
  function() { alert("Çalışmayı durdurdun."); }
);
*/!*
```
Gördüğünüz gibi yukarıda fonksiyonlar doğrudan `sor(...)` içerisinde tanımlandı. Hiç bir isim kullanılmadığından dolayı. Böyle fonksiyonlara *anonim* veya *anonymous* fonksiyonlar denir. Bu fonksiyonlar `sor` fonksiyonu dışında ulaşılabilir değillerdir(çünkü hiç bir değişkene atanmazlar).

Bu şekilde isimsiz kullanım JavaScript içerisinde çok doğaldır. Bu JavaScript'in ruhunda var diyebiliriz.


```smart header="Fonksiyon "fiil" bildiren bir değerdir."
Normal değerler örneğin karakter dizisi ve sayılar *veri*dir.
Fonksiyon *fiil* olarak adlandırılabilir.

Değişkenler arasında paylaşılabilir. İstendiği zaman çalıştırılabilir.

```


## Fonksiyon ifadesi ile Fonksiyon tanımının karışalaştırılması

Eğer Fonksiyon ifades ile fonksiyon tanımı arasındaki önemli farkları açıklamak gerekirse;

Yazım: Kodda neyin ne olduğunu görme.


- *Fonksiyon Tanımlama:* bir fonksiyon ana kod yapısında farklı bir cümle olarak tanımlanır.


    ```js
    // Fonksiyon Tanımlama
    function toplam(a, b) {
      return a + b;
    }
    ```
- *Fonksiyon ifadesi:* bir fonksiyon ifadenin içinde  veya diğer bir yazım yapısı ile ifade edilir.

    Burada fonksiyon "atama ifadesinin =" sağ tarafında tanımlanmıştır.
    ```js
    // Fonksiyon tanımı
    let toplam = function(a, b) {
      return a + b;
    };
    ```

Daha ince bir değişiklik ise fonksiyonun JavaScript motorunda ne zaman yaratılacağıdır.

**Fonksiyon ifadesi kod çalışırken fonksiyona geldikten sonra kullılır**

Çalışma atamanın sağ tarafaını geçince `let sum = function...`, bu noktadan sonra fonksiyon artık yaratıldı. Bundan böyle çağırılabilir veya başka bir değişkene atanabilir.

Fonksiyon tanımlama ise farklıdır.

**Fonksiyon tanımlama tüm kod bloğu içerisinde kullanılabilir**

Diğer bir deyişle, JavaScript kod bloğunu çalıştırmaya *hazırlandığında*, önce fonksiyon tanımlamalarına bakar ve fonksiyonları yaratır. Bunu bir "başlatma evresi* olarak görmek mümkündür.

Tüm Fonksiyon tanımlamaları tamamlandıktan sonra çalışmaya devam eder.

Sonuç olarak, fonksiyon tanımı ile bu tanımdan önce çağırılabilir.

Örnek verecek olursak:

```js run refresh untrusted
*!*
selamVer("Ahmet"); // Merhaba Ahmet
*/!*

function sayHi(isim) {
  alert( `Merhaba, ${isim}` );
}
```

Fonksiyon Tanımı olan `selamVer` JavaScript'in hazırlanma evresinde tanımlanır. Kod çalıştığında kodun her yerinden bu koda erişmek mümkündür.

Eğer bu bir Fonksiyon tanımı olsaydı, çalışmazdı.


```js run refresh untrusted
*!*
selamVer("Ahmet"); // hata!
*/!*

let selamVer = function(adi) {  // (*) büyü ortadan kalktı
  alert( `Merhaba, ${adi}` );
};
```
Fonksiyon tanımı kendisine ulaştığında çalışır. Yani `(*)`'gelmeden tanımlanmış olmalıydı ki `selamVer("Ahmet")` çalışabilsin.

**Fonksiyon tanımı eğer kod bloğunun içerisinde tanımlanırsa o bloğun içerisinde her yerde kullanılabilir. Fakat dışarıda kullanılamaz.**

Bazen sadece blok içinde o blokta kullanılacak yerel bir fonksiyon yaratmak daha kolay gelebilir. Fakat bu özellik problem yaratabilir.

Örneğin, `hosgeldin()` fonksiyonunu `yas` değişkenine göre tanımlayalım. Böylece sonradan kullanılacak hale getirmiş oluruz.

Aşağıdaki kod çalışmayacaktır:

```js run
let yas = prompt("Yaşın kaç?", 18);

// Şarta göre fonksiyon tanımlama
if (yas < 18) {

  function merhaba() {
    alert("Merhaba!");
  }

} else {

  function merhaba() {
    alert("Merhaba!");
  }

}

// ...sonra kullan...
*!*
merhaba(); // Hata: merhaba() tanımlı değil.
*/!*
```
Burada hata alınmasının nedeni Fonksiyon Tanımının `if..else` bloğu içerisinde tanımlandığından dolayı dışarıdan çağırılamamasından dolayıdır.

Diğer bir örnek:

```js run
let yas = 16; // yaş 16 diyelim.

if (yas < 18) {
*!*
  merhaba();               // \   (çalışır)
*/!*
                           //  |
  function merhaba() {     //  |  
    alert("Merhaba!");     //  |  Fonksiyon tanımı bu blok içirisinde her yerden çağırılabilir.
  }                        //  |  
                           //  |
*!*
  merhaba();               // /   (çalışır)
*/!*

} else {

  function merhaba() {     //  Yaş 16 olduğundan burası hiç bir zaman çalışmaz.
    alert("Merhaba!");
  }
}

// Artık if bloğunun dışında olduğumuzdan dolayı burada fonksiyon tanımlarına ulaşamayız.

*!*
merhaba(); // Error: merhaba tanımlı değil.
*/!*
```

What can we do to make `welcome` visible outside of `if`?

The correct approach would be to use a Function Expression and assign `welcome` to the variable that is declared outside of `if` and has the proper visibility.

Now it works as intended:

```js run
let age = prompt("What is your age?", 18);

let welcome;

if (age < 18) {

  welcome = function() {
    alert("Hello!");
  };

} else {

  welcome = function() {
    alert("Greetings!");
  };

}

*!*
welcome(); // ok now
*/!*
```

Or we could simplify it even further using a question mark operator `?`:

```js run
let age = prompt("What is your age?", 18);

let welcome = (age < 18) ?
  function() { alert("Hello!"); } :
  function() { alert("Greetings!"); };

*!*
welcome(); // ok now
*/!*
```


```smart header="When to choose Function Declaration versus Function Expression?"
As a rule of thumb, when we need to declare a function, the first to consider is Function Declaration syntax, the one we used before. It gives more freedom in how to organize our code, because we can call such functions before they are declared.

It's also a little bit easier to look up `function f(…) {…}` in the code than `let f = function(…) {…}`. Function Declarations are more "eye-catching".

...But if a Function Declaration does not suit us for some reason (we've seen an example above), then Function Expression should be used.
```


## Arrow functions [#arrow-functions]

There's one more very simple and concise syntax for creating functions, that's often better than Function Expressions. It's called "arrow functions", because it looks like this:


```js
let func = (arg1, arg2, ...argN) => expression
```

...This creates a function `func` that has arguments `arg1..argN`, evaluates the `expression` on the right side with their use and returns its result.

In other words, it's roughly the same as:

```js
let func = function(arg1, arg2, ...argN) {
  return expression;
}
```

...But much more concise.

Let's see an example:

```js run
let sum = (a, b) => a + b;

/* The arrow function is a shorter form of:

let sum = function(a, b) {
  return a + b;
};
*/

alert( sum(1, 2) ); // 3

```

If we have only one argument, then parentheses can be omitted, making that even shorter:

```js run
// same as
// let double = function(n) { return n * 2 }
*!*
let double = n => n * 2;
*/!*

alert( double(3) ); // 6
```

If there are no arguments, parentheses should be empty (but they should be present):

```js run
let sayHi = () => alert("Hello!");

sayHi();
```

Arrow functions can be used in the same way as Function Expressions.

For instance, here's the rewritten example with `welcome()`:

```js run
let age = prompt("What is your age?", 18);

let welcome = (age < 18) ?
  () => alert('Hello') :
  () => alert("Greetings!");

welcome(); // ok now
```

Arrow functions may appear unfamiliar and not very readable at first, but that quickly changes as the eyes get used to the structure.

They are very convenient for simple one-line actions, when we're just too lazy to write many words.

```smart header="Multiline arrow functions"

The examples above took arguments from the left of `=>` and evaluated the right-side expression with them.

Sometimes we need something a little bit more complex, like multiple expressions or statements. It is also possible, but we should enclose them in figure brackets. Then use a normal `return` within them.

Like this:

```js run
let sum = (a, b) => {  // the figure bracket opens a multiline function
  let result = a + b;
*!*
  return result; // if we use figure brackets, use return to get results
*/!*
};

alert( sum(1, 2) ); // 3
```

```smart header="More to come"
Here we praised arrow functions for brevity. But that's not all! Arrow functions have other interesting features. We'll return to them later in the chapter <info:arrow-functions>.

For now, we can already use them for one-line actions and callbacks.
```

## Summary

- Functions are values. They can be assigned, copied or declared in any place of the code.
- If the function is declared as a separate statement in the main code flow, that's called a "Function Declaration".
- If the function is created as a part of an expression, it's called a "Function Expression".
- Function Declarations are processed before the code block is executed. They are visible everywhere in the block.
- Function Expressions are created when the execution flow reaches them.


In most cases when we need to declare a function, a Function Declaration is preferable, because it is visible prior to the declaration itself. That gives us more flexibility in code organization, and is usually more readable.

So we should use a Function Expression only when a Function Declaration is not fit for the task. We've seen a couple of examples of that in this chapter, and will see more in the future.

Arrow functions are handy for one-liners. They come in two flavors:

1. Without figure brackets: `(...args) => expression` -- the right side is an expression: the function evaluates it and returns the result.
2. With figure brackets: `(...args) => { body }` -- brackets allow us to write multiple statements inside the function, but we need an explicit `return` to return something.
