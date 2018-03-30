
# Closure

JavaScript fonksiyon yönelimli bir dildir. Çok bağımsızlık verir. Fonksiyon bir yerde yaratılıp sonra başka bir değişkene atanarak diğer bir fonksiyona argüman olarak gönderilebilir ve sonra tamamen farklı bir yerden çağrılabilir.

Bildiğiniz gibi fonksiyon kendi dışında olan değişkenlere ulaşabilir ve bu özelliklik oldukça fazla kullanılır.

Peki ya dışarıdaki değişken değişirse? Fonksiyon en son değerini mi alacak yoksa yaratıldığında var olan değeri mi?

Ayrıca diyelim ki fonksiyon başka bir yere gönderildi ve oradan çağrıldığında ne olur, yeni yerinden dışarıda bulunan değişkenlere erişebilir mi?

Bu sorulara farklı diller farklı cevaplar vermektedir, bu bölümde JavaScriptin bu sorulara cevabını öğreneceksiniz.

[cut]

## Birkaç soru

Örnek olması amacıyla iki soru formülize edilecek olursa, sonrasında içsel mekanizması parça parça incelenecektir, ileride daha karmaşık sorularacevap verebilirsiniz.

1. `selamVer` fonksiyonu dışarıda bulunan `isim` değişkenini kullanmaktadır. Fonksiyon çalıştığında, hangi `isim` değişkeni kullanılacaktır?

    ```js
    let isim = "Ahmet";

    function selamVer() {
      alert("Merhaba, " + isim);
    }

    isim = "Mehmet";

    *!*
    selamVer(); // "Ahmet" mi yoksa "Mehmet" mi gösterilecek?
    */!*
    ```

    Böyle durumlara tarayıcı ve sunucu tabanlı geliştirmelerde oldukça sık karşılaşılır. Bir fonksiyon yaratıldığı anda değil de daha sonra çalışmak üzere programlanabilir. Örneğin bir kullanıcı aksiyonu veya ağ üzerinden istekler bu gruba girer.
    
    Öyleyse soru: son değişiklikleri alır mı?
    

2. `calisanYarat` diğer bir fonksiyon yaratır ve bunu döner. Bu yeni fonksiyon herhangi bir yerden çağrılabilir. Peki yaratıldığı yerin dışındaki değişkenlere veya çağrılan yerin dışındaki değişkenlere veya ikisine birden erişebilece mi?

    ```js
    function calisanYarat() {
      let isim = "Mehmet";

      return function() {
        alert(isim);
      };
    }

    let isim = "Zafer";

    // fonksiyon yarat
    let is = calisanYarat();

    // çağır
    *!*
    is(); // burada "Mehmet" mi yoksa "Zafer" mi gösterilecek ? 
    */!*
    ```


## Sözcüksel ortam ( Lexical Environment )

Ne olduğunu anlamak için önce "değişken"'in tekniksel anlamı üzerinde tartışmak lazım

JavaScript'te çalışan her fonksiyon, kod bloğu bir bütün olarak "Sözcüksel Ortam" adında bir objeye sahiptir.

Bu "Sözcüksel Ortam" iki bölümden oluşur:

1. *Ortam Kaydı* -- tüm yerel değişkenleri ve özelliklerini ( ve ek özellikleri `this` gibi ) tutan objedir.
2. *Dış Sözcüksel Ortam*'a referans genelde süslü parantezin dışındaki kod ile ilintilidir.

Öyleyse "değişken" içsel objedeki bir özelliktir, çevresel kayıtlar. "değişkeni almak veya değiştirmek" demek "o objenin özelliğini almak veya değiştirmek" demektir.

Örneğin, aşağıdaki kodda sadece bir tane Sözcüksel Ortam bulunmaktadır:

![Sözcüksel Ortam](lexical-environment-global.png)

Buna evrensel sözcük ortamı denilmektedir, kodun tamamıyla alakalıdır. Tüm tarayıcılarda `<script>` etiketleri aynı evrensel ortamı paylaşır.

Yukarıdaki görselde, dikdörtgen ile gösterilen Çevresel Kayıt ( değişken kaynağı ) anlamına gelir ve ok işareti dışsal referanstır. Evrensel Sözcük Ortamından daha dış ortam bulunmamaktadır. Yani `null` dur. 

Aşağıda `let` değişkenlerinin nasıl çalıştığı görsel ile açıklanmıştır:

![Sözcüksel Ortam](lexical-environment-global-2.png)

Sağ tarafta bulunan dikdörtgenler evrensel Sözcük Ortamının çalışırkenki değişikliklerini gösterir.

1. Kod çalışmaya başladığında, Sözcüksel Ortam boştur.
2. `let ifade`  tanımlaması görünür. İlk başta bir değeri bulunmamaktadır bundan `undefined` olarak saklanır.
3. `ifade`'ye değer atanır.
4. `ifade` yeni bir defere referans olur.

Herşey çok basit görünüyor değil mi?

Özetlemek gerekirse:

- Değişken özel bir iç objenin özelliğidir. Bu obje o anda çalışan kod, fonksiyon ile bağlantılıdır.
- Değişkenlerle çalışmak aslında o objenin özellikleri ile çalışmak demektir.

### Fonksiyon tanımı

Fonksiyon tanımları özeldir. `let` değişkenlerine nazaran çalıştırıldıklarında değil de Sözcüksel Ortam yaratıldığında işlenirler, bu da kodun başladığı zamandır.

... Ve bundan dolayı bir fonksiyon tanımından önce çağırılabilir.

Aşağıdaki kodda Sözcüksel Ortam başlangıçta boş değildir. `say`'e sahiptir çünkü bu bir fonksiyon tanımıdır. Sonrasında `ifade` alır ve bunu `let` ile tanımlar:

![Sözcüksel Ortam](lexical-environment-global-3.png)

### İç ve dış Sözcüksel Ortamlar

`say()` fonksiyonu çağrısı sırasında dış değişkenler çağrılır, bu olaya daha detaylı bakacak olursak.

Fonksiyon ilk çalıştığında yeni bir Sözcüksel Çevre otomatik olarak yaratılır. Bu tüm fonksiyonlar için genel bir kuraldır. Bu Sözcüksel Çevre yerel değişkenlerin tutulması ve çağrının tüm parametrelerini tutar.

<!--
```js
let ifade = "Merhaba";

function say(adi) {
  alert( `${ifade}, ${adi}` );
}

say("Ahmet"); // Merhaba, Ahmet
```
-->

Here's the picture of Lexical Environments when the execution is inside `say("John")`, at the line labelled with an arrow:

![lexical environment](lexical-environment-simple.png)

During the function call we have two Lexical Environments: the inner one (for the function call) and the outer one (global):

- The inner Lexical Environment corresponds to the current execution of  `say`. It has a single variable: `name`, the function argument. We called `say("John")`, so the value of `name` is `"John"`.
- The outer Lexical Environment is the global Lexical Environment.

The inner Lexical Environment has the `outer` reference to the outer one.

**When a code wants to access a variable -- it is first searched in the inner Lexical Environment, then in the outer one, then the more outer one and so on until the end of the chain.**

If a variable is not found anywhere, that's an error in strict mode. Without `use strict`, an assignment to an undefined variable creates a new global variable, for backwards compatibility.

Let's see how the search proceeds in our example:

- When the `alert` inside `say` wants to access `name`, it finds it immediately in the function Lexical Environment.
- When it wants to access `phrase`, then there is no `phrase` locally, so it follows the `outer` reference and finds it globally.

![lexical environment lookup](lexical-environment-simple-lookup.png)

Now we can give the answer to the first seed question from the beginning of the chapter.

**A function gets outer variables as they are now, the most recent values.**

That's because of the described mechanism. Old variable values are not saved anywhere. When a function wants them, it takes the current values from its own or an outer Lexical Environment.

So the answer to the first question is `Pete`:

```js run
let name = "John";

function sayHi() {
  alert("Hi, " + name);
}

name = "Pete"; // (*)

*!*
sayHi(); // Pete
*/!*
```


The execution flow of the code above:

1. The global Lexical Environment has `name: "John"`.
2. At the line `(*)` the global variable is changed, now it has `name: "Pete"`.
3. When the function `say()`, is executed and takes `name` from outside. Here that's from the global Lexical Environment where it's already `"Pete"`.


```smart header="One call -- one Lexical Environment"
Please note that a new function Lexical Environment is created each time a function runs.

And if a function is called multiple times, then each invocation will have its own Lexical Environment, with local variables and parameters specific for that very run.
```

```smart header="Lexical Environment is a specification object"
"Lexical Environment" is a specification object. We can't get this object in our code and manipulate it directly. JavaScript engines also may optimize it, discard variables that are unused to save memory and perform other internal tricks, but the visible behavior should be as described.
```


## Nested functions

A function is called "nested" when it is created inside another function.

Technically, that is easily possible.

We can use it to organize the code, like this:

```js
function sayHiBye(firstName, lastName) {

  // helper nested function to use below
  function getFullName() {
    return firstName + " " + lastName;
  }

  alert( "Hello, " + getFullName() );
  alert( "Bye, " + getFullName() );

}
```

Here the *nested* function `getFullName()` is made for convenience. It can access the outer variables and so can return the full name.

What's more interesting, a nested function can be returned: as a property of a new object (if the outer function creates an object with methods) or as a result by itself. And then used somewhere else. No matter where, it still keeps the access to the same outer variables.

An example with the constructor function (see the chapter <info:constructor-new>):

```js run
// constructor function returns a new object
function User(name) {

  // the object method is created as a nested function
  this.sayHi = function() {
    alert(name);
  };
}

let user = new User("John");
user.sayHi(); // the method code has access to the outer "name"
```

An example with returning a function:

```js run
function makeCounter() {
  let count = 0;

  return function() {
    return count++; // has access to the outer counter
  };
}

let counter = makeCounter();

alert( counter() ); // 0
alert( counter() ); // 1
alert( counter() ); // 2
```

Let's go on with the `makeCounter` example. It creates the "counter" function that returns the next number on each invocation. Despite being simple, slightly modified variants of that code have practical uses, for instance, as a [pseudorandom number generator](https://en.wikipedia.org/wiki/Pseudorandom_number_generator), and more. So the example is not quite artificial.

How does the counter work internally?

When the inner function runs, the variable in `count++` is searched from inside out. For the example above, the order will be:

![](lexical-search-order.png)

1. The locals of the nested function.
2. The variables of the outer function.
3. ...And further until it reaches globals.

In that example `count` is found on the step `2`. When an outer variable is modified, it's changed where it's found. So `count++` finds the outer variable and increases it in the Lexical Environment where it belongs. Like if we had `let count = 1`.

Here are two questions for you:

1. Can we somehow reset the `counter` from the code that doesn't belong to `makeCounter`? E.g. after `alert` calls in the example above.
2. If we call `makeCounter()` multiple times -- it returns many `counter` functions. Are they independent or do they share the same `count`?

Try to answer them before going on reading.

...Are you done?

Okay, here we go with the answers.

1. There is no way. The `counter` is a local function variable, we can't access it from the outside.
2. For every call to `makeCounter()` a new function Lexical Environment is created, with its own `counter`. So the resulting `counter` functions are independent.

Here's the demo:

```js run
function makeCounter() {
  let count = 0;
  return function() {
    return count++;
  };
}

let counter1 = makeCounter();
let counter2 = makeCounter();

alert( counter1() ); // 0
alert( counter1() ); // 1

alert( counter2() ); // 0 (independant)
```


Probably, the situation with outer variables is quite clear for you as of now. But in more complex situations a deeper understanding of internals may be required. So let's go ahead.

## Environments in detail

Now as you understand how closures work generally, we may finally descend to the very nuts and bolts.

Here's what's going on in the `makeCounter` example step-by-step, follow it to make sure that you understand everything. Please note the additional `[[Environment]]` property that we didn't cover yet.

1. When the script has just started, there is only global Lexical Environment:

    ![](lexenv-nested-makecounter-1.png)

    At that starting moment there is only `makeCounter` function, because it's a Function Declaration. It did not run yet.

    All functions "on birth" receive a hidden property `[[Environment]]` with the reference to the Lexical Environment of their creation. We didn't talk about it yet, but technically that's a way how the function knows where it was made.

    Here, `makeCounter` is created in the global Lexical Environment, so `[[Environment]]` keeps the reference to it.

    In other words, a function is "imprinted" with a reference to the Lexical Environment where it was born. And `[[Environment]]` is the hidden function property that has that reference.

2. Then the code runs on, and the call to `makeCounter()` is performed. Here's the picture for the moment when the execution is on the first line inside `makeCounter()`:

    ![](lexenv-nested-makecounter-2.png)

    At the moment of the call of `makeCounter()`, the Lexical Environment is created, to hold its variables and arguments.

    As all Lexical Environments, it stores two things:
    1. An Environment Record with local variables. In our case `count` is the only local variable (appears in it when the line with `let count` is executed).
    2. The outer lexical reference, which is set to `[[Environment]]` of the function. Here `[[Environment]]` of `makeCounter` references the global Lexical Environment.

    So, now we have two Lexical Environments: the first one is global, the second one is for the current `makeCounter` call, with the outer reference to global.

3. During the execution of `makeCounter()`, a tiny nested function is created.

    It doesn't matter whether the function is created using Function Declaration or Function Expression. All functions get the `[[Environment]]` property that references the Lexical Environment where they were made. So that new tiny nested function gets it as well.

    For our new nested function the value of `[[Environment]]` is the current Lexical Environment of `makeCounter()` (where it was born):

    ![](lexenv-nested-makecounter-3.png)

    Please note that on this step the inner function was created, but not yet called. The code inside `function() { return count++; }` is not running, we're going to return it.

4. As the execution goes on, the call to `makeCounter()` finishes, and the result (the tiny nested function) is assigned to the global variable `counter`:

    ![](lexenv-nested-makecounter-4.png)

    That function has only one line: `return count++`, that will be executed when we run it.

5. When the `counter()` is called, an "empty" Lexical Environment is created for it. It has no local variables by itself. But the `[[Environment]]` of `counter` is used as the outer reference for it, so it has access to the variables of the former `makeCounter()` call, where it was created:

    ![](lexenv-nested-makecounter-5.png)

    Now if it accesses a variable, it first searches its own Lexical Environment (empty), then the Lexical Environment of the former `makeCounter()` call, then the global one.

    When it looks for `count`, it finds it among the variables `makeCounter`, in the nearest outer Lexical Environment.

    Please note how memory management works here. When `makeCounter()` call finished some time ago, its Lexical Environment was retained in memory, because there's a nested function with `[[Environment]]` referencing it.

    Generally, a Lexical Environment object lives as long as there is a function which may use it. And when there are none, it is cleared.

6. The call to `counter()` not only returns the value of `count`, but also increases it. Note that the modification is done "in place". The value of `count` is modified exactly in the environment where it was found.

    ![](lexenv-nested-makecounter-6.png)

    So we return to the previous step with the only change -- the new value of `count`. The following calls all do the same.

7. Next `counter()` invocations do the same.

The answer to the second seed question from the beginning of the chapter should now be obvious.

The `work()` function in the code below uses the `name` from the place of its origin through the outer lexical environment reference:

![](lexenv-nested-work.png)

So, the result is `"Pete"` here.

...But if there were no `let name` in `makeWorker()`, then the search would go outside and take the global variable as we can see from the chain above. In that case it would be `"John"`.

```smart header="Closures"
There is a general programming term "closure", that developers generally should know.

A [closure](https://en.wikipedia.org/wiki/Closure_(computer_programming)) is a function that remembers its outer variables and can access them. In some languages, that's not possible, or a function should be written in a special way to make it happen. But as explained above, in JavaScript all functions are naturally closures (there is only one exclusion, to be covered in <info:new-function>).

That is: they automatically remember where they are created using a hidden `[[Environment]]` property, and all of them can access outer variables.

When on an interview a frontend developer gets a question about "what's a closure?", a valid answer would be a definition of the closure and an explanation that all functions in JavaScript are closures, and maybe few more words about technical details: the `[[Environment]]` property and how Lexical Environments work.
```

## Code blocks and loops, IIFE

The examples above concentrated on functions. But Lexical Environments also exist for code blocks `{...}`.

They are created when a code block runs and contain block-local variables. Here's a couple of examples.

## If

In the example below, when the execution goes into `if` block, the new "if-only" Lexical Environment is created for it:

<!--
```js run
let phrase = "Hello";

if (true) {
  let user = "John";

  alert(`${phrase}, ${user}`); // Hello, John
}

alert(user); // Error, can't see such variable!
```
-->

![](lexenv-if.png)

The new Lexical Environment gets the enclosing one as the outer reference, so `phrase` can be found. But all variables and Function Expressions declared inside `if` reside in that Lexical Environment and can't be seen from the outside.

For instance, after `if` finishes, the `alert` below won't see the `user`, hence the error.

## For, while

For a loop, every run has a separate Lexical Environment. If the variable is declared in `for`, then it's also local to that Lexical Environment:

```js run
for(let i = 0; i < 10; i++) {
  // Each loop has its own Lexical Environment
  // {i: value}
}

alert(i); // Error, no such variable
```

That's actually an exception, because `let i` is visually outside of `{...}`. But in fact each run of the loop has its own Lexical Environment with the current `i` in it.

After the loop, `i` is not visible.

### Code blocks

We also can use a "bare" code block `{…}` to isolate variables into a "local scope".

For instance, in a web browser all scripts share the same global area. So if we create a global variable in one script, it becomes available to others. But that becomes a source of conflicts if two scripts use the same variable name and overwrite each other.

That may happen if the variable name is a widespread word, and script authors are unaware of each other.

If we'd like to evade that, we can use a code block to isolate the whole script or an area in it:

```js run
{
  // do some job with local variables that should not be seen outside

  let message = "Hello";

  alert(message); // Hello
}

alert(message); // Error: message is not defined
```

The code outside of the block (or inside another script) doesn't see variables in it, because a code block has its own Lexical Environment.

### IIFE

In old scripts, one can find so-called "immediately-invoked function expressions" (abbreviated as IIFE) used for this purpose.

They look like this:

```js run
(function() {

  let message = "Hello";

  alert(message); // Hello

})();
```

Here a Function Expression is created and immediately called. So the code executes right now and has its own private variables.

The Function Expression is wrapped with brackets `(function {...})`, because when JavaScript meets `"function"` in the main code flow, it understands it as a start of Function Declaration. But a Function Declaration must have a name, so there will be an error:

```js run
// Error: Unexpected token (
function() { // <-- JavaScript cannot find function name, meets ( and gives error

  let message = "Hello";

  alert(message); // Hello

}();
```

We can say "okay, let it be Function Declaration, let's add a name", but it won't work. JavaScript does not allow Function Declarations to be called immediately:

```js run
// syntax error because of brackets below
function go() {

}(); // <-- can't call Function Declaration immediately
```

...So the brackets are needed to show JavaScript that the function is created in the context of another expression, and hence it's a Function Expression. Needs no name and can be called immediately.

There are other ways to tell JavaScript that we mean Function Expression:

```js run
// Ways to create IIFE

(function() {
  alert("Brackets around the function");
}*!*)*/!*();

(function() {
  alert("Brackets around the whole thing");
}()*!*)*/!*;

*!*!*/!*function() {
  alert("Bitwise NOT operator starts the expression");
}();

*!*+*/!*function() {
  alert("Unary plus starts the expression");
}();
```

In all cases above we declare a Function Expression and run it immediately.

## Garbage collection

Lexical Environment objects that we've been talking about are subjects to same memory management rules as regular values.

- Usually, Lexical Environment is cleaned up after the function run. For instance:

    ```js
    function f() {
      let value1 = 123;
      let value2 = 456;
    }

    f();
    ```

    Here two values are technically the properties of the Lexical Environment. But after `f()` finishes that Lexical Environment becomes unreachable, so it's deleted from the memory.

- ...But if there's a nested function that is still reachable after the end of `f`, then its `[[Environment]]` reference keeps the outer lexical environment alive as well:

    ```js
    function f() {
      let value = 123;

      function g() { alert(value); }

    *!*
      return g;
    */!*
    }

    let g = f(); // g is reachable, and keeps the outer lexical environment in memory
    ```

- Please note that if `f()` is called many times, and resulting functions are saved, then the corresponding Lexical Environment objects will also be retained in memory. All 3 of them in the code below:

    ```js
    function f() {
      let value = Math.random();

      return function() { alert(value); };
    }

    // 3 functions in array, every of them links to Lexical Environment
    // from the corresponding f() run
    //         LE   LE   LE
    let arr = [f(), f(), f()];
    ```

- A Lexical Environment object dies when it becomes unreachable. That is: when no nested functions remain that reference it. In the code below, after `g` becomes unreachable, the `value` is also cleaned from the memory;

    ```js
    function f() {
      let value = 123;

      function g() { alert(value); }

      return g;
    }

    let g = f(); // while g is alive
    // there corresponding Lexical Environment lives

    g = null; // ...and now the memory is cleaned up
    ```

### Real-life optimizations

As we've seen, in theory while a function is alive, all outer variables are also retained.

But in practice, JavaScript engines try to optimize that. They analyze variable usage and if it's easy to see that an outer variable is not used -- it is removed.

**An important side effect in V8 (Chrome, Opera) is that such variable will become unavailable in debugging.**

Try running the example below with the open Developer Tools in Chrome.

When it pauses, in console type `alert(value)`.

```js run
function f() {
  let value = Math.random();

  function g() {
    debugger; // in console: type alert( value ); No such variable!
  }

  return g;
}

let g = f();
g();
```

As you could see -- there is no such variable! In theory, it should be accessible, but the engine optimized it out.

That may lead to funny (if not such time-consuming) debugging issues. One of them -- we can see a same-named outer variable instead of the expected one:

```js run global
let value = "Surprise!";

function f() {
  let value = "the closest value";

  function g() {
    debugger; // in console: type alert( value ); Surprise!
  }

  return g;
}

let g = f();
g();
```

```warn header="See ya!"
This feature of V8 is good to know. If you are debugging with Chrome/Opera, sooner or later you will meet it.

That is not a bug of debugger, but a special feature of V8. Maybe it will be changed some time.
You always can check for it by running examples on this page.
```
