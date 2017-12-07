# JavaScript incelikleri

Bu bölümde kısaca JavaScript dilinde hali hazırda öğrendiğiniz fakat özellikle dikkat etmeniz gereken inceliklerden bahsedilecektir.

[cut]

## Kod Yapısı

Cümleler birbirinden noktalı virgül ile ayrılır:

```js run no-beautify
alert('Merhaba'); alert('Dünya');
```

Genelde, yeni satıra geçmekte noktalı virgül görevi görür. Bundan dolayı aşağıdaki kod da çalışır:

```js run no-beautify
alert('Merhaba')
alert('Dünya')
```
Buna "otomatik noktalı virgül koyma" denir. Bazen çalışmaz, örneğin:

```js run
alert("Bu mesajdan sonra hata verecek")

[1, 2].forEach(alert)
```

Çoğu kod klavuzu her cümlenizin sonuna noktalı virgül kullanmanız gerektiği kanısındadır.

Noktalı virgüller `{..}` kod bloğu sonunda gerekli değildir, örneğin döngüler:

```js
function f() {
  // fonksiyon tanımından sonra noktalı virgül yazılmaz
}

for(;;) {
  // döngüden sonra noktalı virgül yazılmaz
}
```
... Diyelim ki yine de noktalı virgül koymak istediniz. Bu bir hata değildir, önemsenmez.

Daha fazlasına <info:structure> bölümünden bakabilirsiniz.

## Sıkı Mod

JavaScript'in tüm modern özelliklerini kullanabilmek için, `"use strict"` kullanmanız gerekmektedir.

```js
'use strict';

...
```
Bu talimatı dosyanın başında veya fonksiyonun başında belirtmeniz gerekmektedir.

`"use strict"` kullanmadan da herşey çalışır. Fakat eski tipte ve uyumluluk modunda çalışır. Modern davranışı seçerseniz böylece son yenilikleri uyumluluk modu olmadan da çalıştırabilirsiniz.

Bazı modern özellikler ise uyumluluk modunda da çalışmaz sadece sıkı modda çalışır. Bunlara ilerleyen zamanlarda değinilecektir.
Dahası için: <info:strict-mode>.

## Değişkenler

Şu şekillerde tanımlanabilir:

- `let`
- `const` (sabit, değiştirilemez)
- `var` (eski tip)

Değişkenler isimlendirilirken aşağıdakileri içerebilir:
- Harf ve sayıları içerebilir fakat ilk karakter sayı olamaz.
- `$` ve `_` gibi karakterler diğer karakterle aynı niteliktedir ve her yerde kullanılabilir.
- Latin olmayan yani Arapça, Japonca, Çince gibi diller de kullanılabilir fakat genelde kullanılmaz. 

Değişkenler dinamik yazıma sahiptir ve herşeyi tutabilirler:

```js
let x = 5;
x = "Ahmet";
```

7 çeşit veri tipi bulunmaktadır:

- `number` ( sayı ) floating-point ve doğal sayılar için kullanılır.
- `string` (karakter dizileri),
- `boolean` Mantıksal değerler için `dogru/yanlis`,
- `null` -- sadece `null` değerini tutar ve bu da "boş" veya "varolmayan" anlamına gelir,
- `undefined` -- sadece `undefined` değerine sahiptir. Bu da "değer atanmamış" demektir,
- `object` ve `symbol` -- karmaşık veri yapıları için ve tek tanıtıcı(unique identifier) için kullanılabilir. Bu konular henüz anlatılmadı.

`typeof` operatörü değerin tipini dönderir, fakat şu hallerde hata verir:

```js
typeof null == "object" // hata verir
typeof function(){} == "function" // fonksiyonlara özel davranılır.
```

Dahası için: <info:variables> ve <info:types> konularına bakabilirsiniz.

## Etkileşim

Şu anda tarayıcıyı çalışma ortamı olarak kullandığınızdan dolayı, bazı basit arayüz fonksiyonlarını bilmekte fayda var:

[`prompt(soru[, varsayılan])`](mdn:api/Window/prompt)
: `soru` sor ve kullanıcının girdiği değeri dönder. Eğer kullanıcı "iptal" tuşuna bakarsa `null` dönder.

[`confirm(soru)`](mdn:api/Window/confirm)
: `soru` sor ve "Tamam" mı yoksa "İptal" mi diye seçenekler sun. Sonuçta seçilene göre  `true/false` dönder.

[`alert(mesaj)`](mdn:api/Window/alert)
: Mesajın çıktısını ekrana uyarı olarak ver.

tüm bo fonksiyonlar *modal* dır. Tekrara hatırlatmak gerekirse modal kullanıcının etkileşimi olana kadar kodu durdururlar. Yani kullanıcıdan cevabı beklerler.

Örneğin:

```js run
let ziyaretci = prompt("Adınız?", "İbrahim");
let cayIstermi = confirm("Biraz çay ister misiniz?");

alert( "Ziyaretçi: " + ziyaretci ); // İbrahim
alert( "Çay isteriyor mu?: " + cayIstermi ); // true
```

Dahası için: <info:alert-prompt-confirm>.

## Operatörler

JavaScript aşağıdaki operatörleri destekler:

Aritmetiksel
: Normal işlemler: `* + - /`, mod alma `%`  ve `**` üs alma için bu operatörler kullanılır.

    Eğer operandlardan birisi karakter ise diğer taraf sayı bile olsa `+` kullanıldığında bu iki değer de karakter olarak varsayılır

    ```js run
    alert( '1' + 2 ); // '12', karakter dizisi
    alert( 1 + '2' ); // '12', karakter dizisi
    ```

Değer atama
: Basit bir şekilde `a = b` şeklinde kullanılabilir. Veya birleşik olarak  `a *= 2` gibi de kullanıma sahiptir.

Bitwise
: Bitwise operators work with integers on bit-level: see the [docs](mdn:/JavaScript/Reference/Operators/Bitwise_Operators) when they are needed.

Ternary
: The only operator with three parameters: `cond ? resultA : result B`. If `cond` is truthy, returns `resultA`, otherwise `resultB`.

Logical operators
: Logical AND `&&` and OR `||` perform short-circuit evaluation and then return the value where it stopped.

Comparisons
: Equality check `==` for values of different types converts them to a number (except `null` and `undefined` that equal each other and nothing else), so these are equal:

    ```js run
    alert( 0 == false ); // true
    alert( 0 == '' ); // true
    ```

    Other comparisons convert to a number as well.

    The strict equality operator `===` doesn't do the conversion: different types always mean different values for it, so:

    Values `null` and `undefined` are special: they equal `==` each other and don't equal anything else.

    Greater/less comparisons compare strings character-by-character, other types are converted to a number.

Logical operators
: There are few others, like a comma operator.

More in: <info:operators>, <info:comparison>, <info:logical-operators>.

## Loops

- We covered 3 types of loops:

    ```js
    // 1
    while (condition) {
      ...
    }

    // 2
    do {
      ...
    } while (condition);

    // 3
    for(let i = 0; i < 10; i++) {
      ...
    }
    ```

- The variable declared in `for(let...)` loop is visible only inside the loop. But we can also omit `let` and reuse an existing variable.
- Directives `break/continue` allow to exit the whole loop/current iteration. Use labels to break nested loops.

Details in: <info:while-for>.

Later we'll study more types of loops to deal with objects.

## The "switch" construct

The "switch" construct can replace multiple `if` checks. It uses `===` for comparisons.

For instance:

```js run
let age = prompt('Your age?', 18);

switch (age) {
  case 18:
    alert("Won't work"); // the result of prompt is a string, not a number

  case "18":
    alert("This works!");
    break;

  default:
    alert("Any value not equal to one above");
}
```

Details in: <info:switch>.

## Functions

We covered three ways to create a function in JavaScript:

1. Function Declaration: the function in the main code flow

    ```js
    function sum(a, b) {
      let result = a + b;

      return result;
    }
    ```

2. Function Expression: the function in the context of an expression

    ```js
    let sum = function(a, b) {
      let result = a + b;

      return result;
    }
    ```

    Function expression can have a name, like `sum = function name(a, b)`, but that `name` is only visible inside that function.

3. Arrow functions:

    ```js
    // expression at the right side
    let sum = (a, b) => a + b;

    // or multi-line syntax with { ... }, need return here:
    let sum = (a, b) => {
      // ...
      return a + b;
    }

    // without arguments
    let sayHi = () => alert("Hello");

    // with a single argument
    let double = n => n * 2;
    ```


- Functions may have local variables: those declared inside its body. Such variables are only visible inside the function.
- Parameters can have default values: `function sum(a = 1, b = 2) {...}`.
- Functions always return something. If there's no `return` statement, then the result is `undefined`.


| Function Declaration | Function Expression |
|----------------------|---------------------|
| visible in the whole code block | created when the execution reaches it |
|   - | can have a name, visible only inside the function |

More: see <info:function-basics>, <info:function-expressions-arrows>.

## More to come

That was a brief list of JavaScript features. As of now we've studied only basics. Further in the tutorial you'll find more specials and advanced features of JavaScript.
