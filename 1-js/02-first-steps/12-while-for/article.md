# Döngüler: while ve for

Çoğu zaman aynı bir sıra ile tekrar yapma ihtiyacı duyulabilirsiniz.

Örneğin bir listede bulunan ürünlerin sıra ile çıktısını almak. Veya aynı kodu 1-10'a kadar olan sayılar için çalıştırmak.

*Döngü* aynı kod parçacığının birden fazla defa çalıştırılmasına denir.

[cut]

## "while" döngüsü

`while` döngüsü aşağıdaki gibi bir yazıma sahiptir:

```js
while (koşul) {
  // kod
  // veya döngünün gövdesi 
}
```

`koşul` `doğru` iken(while), `döngü gövdesinde` bulunan kod çalıştırılır.

Örneğin, aşağıdaki kod `i < 3` `iken` çalışır.

```js run
let i = 0;
while (i < 3) { // önce 0, sonra 1, sonra 2
  alert( i );
  i++;
}
```
Döngünün gövdesinde bulunan kodun her çalışmasına *tekerrür(iteration)* denir. Yukarıdaki örnekte gövde 3 defa tekerrür etmiştir.

Eğer `i++` gibi bir kod olmasaydı, teoride kod sonsuza kadar devam ederdi. Pratikte ise tarayıcınız bu kodun uzun süre çalışmasını engeller, sunucu tabanlı JavaScript yazdığınızda ise bu işlem durdurulur.

Sadece karşılaştırma değil, bir ifade veya değişken koşul olabilir. `While` döngüsü tarafından alınan tüm ifadeler boolean'a dönüştürülür.

Örneğin, `while(i != 0 )` `while(i)`'de olabilir.

```js run
let i = 3;
*!*
while (i) {  // i 0 olduğunda koşul `yanlış` olur ve döngü biter.
*/!*
  alert( i );
  i--;
}
```

````smart header="Tek satır gövde varsa süslü paranteze gerek kalmaz."
Eğer döngü gövdesi tek satır ise süslü parantezi yazmayabilirsiniz. `{..}`:

```js run
let i = 3;
*!*
while (i) alert(i--);
*/!*
```
````

## "do..while" döngüsü

`do..while` döngüsü kullanarak koşul kontrolünü *sonda* yapmak mümkündür.

```js
do {
  // döngü gövdesi
} while (condition);
```
Döngü önce gövdeyi çalıştırır, sonra koşul kontrolü yapar ve eğer doğruysa tekrar döngü gövdesini çalıştırır.

Örneğin:

```js run
let i = 0;
do {
  alert( i );
  i++;
} while (i < 3);
```
Bu şekilde döngü yazımı çok nadir olarak kullanılır. Kullanılmasının en önemli amacı **en azından bir defa** ne olursa olsun gövdenin çalıştırılma istenmesidir. Genelde `while(..){}` şekli tercih edilir.

## "for" döngüsü

`for` döngüsü en fazla kullanılan döngüdür.

Aşağıdaki şekilde kullanıma sahiptir:

```js
for (başla; koşul; adım) {
  // ... döngü gövdesi ...
}
```
Örnekler üzerinden incenecek olursa. Aşağıdaki döngü `alert(i)` yi `i` `0` dan `3` olana kadar çalıştırır.(3 dahil değil)

```js run
for (let i = 0; i < 3; i++) { // shows 0, then 1, then 2
  alert(i);
}
```
Bölüm bölüm inceleyecek olursak

| bölüm  |             |                                                                            |
|--------|-------------|----------------------------------------------------------------------------|
| başla  | `i = 0`     | Döngüye girildiğinde çalışır.                                              |
| koşul  | `i < 3`     | Her tekerrürden önce çalışır, eğer `yanlış` ise döngü durur.               |
| adım   | `i++`       | Gövdenin tekerrür etmesinden sonra fakat koşuldan önce çalışır             |
| gövde  | `alert(i)`  | koşul doğru olduğu sürece durmadan çalışır                                 |

Genel döngü algoritması aşağıdaki gibidir:

```
Çalışmaya başlar
→ (if koşul → gövdeyi çalıştır ve adımı çalıştır.)
→ (if koşul → gövdeyi çalıştır ve adımı çalıştır.)
→ (if koşul → gövdeyi çalıştır ve adımı çalıştır.)
→ ...
```

Eğer döngüleri yeni görüyorsanız, belki geri dönüp bu olanları sırasıyla kağıda yazarak takip ederseniz sizin için daha iyi olacaktır.

Yukarıdaki kodda tam olarak ne oluyor peki:


```js
// for (let i = 0; i < 3; i++) alert(i)

// Çalışmaya başla
let i = 0
// if koşul → gövdeyi çalıştır ve adımı çalıştır
if (i < 3) { alert(i); i++ }
// if koşul →  gövdeyi çalıştır ve adımı çalıştır
if (i < 3) { alert(i); i++ }
// if koşul →  gövdeyi çalıştır ve adımı çalıştır
if (i < 3) { alert(i); i++ }
// ...bitir, çünkü şimdi i=3
```

````smart header="Satır arasında değişken tanımlama"
Sayaç değişkeni olan `i` döngüye girdiğinde oluşturulur. Buna "satır arası" değişken tanımlama denir. Bu değişken sadece döngü içerisinde kullanılabilir.

```js run
for (*!*let*/!* i = 0; i < 3; i++) {
  alert(i); // 0, 1, 2
}
alert(i); // hata! böyle bir değişken bulunamadı.
```
Değişken tanımlamak yerine var olan da kullanılabilir:

```js run
let i = 0;

for (i = 0; i < 3; i++) { // var olan değişkeni kullan
  alert(i); // 0, 1, 2
}

alert(i); // 3, görünür halde çünkü değişken döngünün dılında tanımlandı.
```

````


### Bazı bölümlerin pas geçilmesi

`for` döngüsünün her bölümü pas geçilebilir.

Örneğin `başlangıç` bölümüne ihtiyaç yoksa pas geçilebilir.

Örneğin:
```js run
let i = 0; // i'yi tanımlanıp 0 değeri atandı

for (; i < 3; i++) { // "başlangıç"'a ihtiyaç yok
  alert( i ); // 0, 1, 2
}
```
`basamak` bilgisini silmek de mümkün:

```js run
let i = 0;

for (; i < 3;) {
  alert( i++ );
}
```

The loop became identical to `while (i < 3)`.

We can actually remove everything, thus creating an infinite loop:

```js
for (;;) {
  // repeats without limits
}
```

Please note that the two `for` semicolons `;` must be present, otherwise it would be a syntax error.

## Breaking the loop

Normally the loop exits when the condition becomes falsy.

But we can force the exit at any moment. There's a special `break` directive for that.

For example, the loop below asks the user for a series of numbers, but "breaks" when no number is entered:

```js
let sum = 0;

while (true) {

  let value = +prompt("Enter a number", '');

*!*
  if (!value) break; // (*)
*/!*

  sum += value;

}
alert( 'Sum: ' + sum );
```

The `break` directive is activated in the line `(*)` if the user enters an empty line or cancels the input. It stops the loop immediately, passing the control to the first line after the loop. Namely, `alert`.

The combination "infinite loop + `break` as needed" is great for situations when the condition must be checked not in the beginning/end of the loop, but in the middle, or even in several places of the body.

## Continue to the next iteration [#continue]

The `continue` directive is a "lighter version" of `break`. It doesn't stop the whole loop. Instead it stops the current iteration and forces the loop to start a new one (if the condition allows).

We can use it if we're done on the current iteration and would like to move on to the next.

The loop above uses `continue` to output only odd values:

```js run no-beautify
for (let i = 0; i < 10; i++) {

  // if true, skip the remaining part of the body
  *!*if (i % 2 == 0) continue;*/!*

  alert(i); // 1, then 3, 5, 7, 9
}
```

For even values of `i` the `continue` directive stops body execution, passing the control to the next iteration of `for` (with the next number). So the `alert` is only called for odd values.

````smart header="The directive `continue` helps to decrease nesting level"
A loop that shows odd values could look like this:

```js
for (let i = 0; i < 10; i++) {

  if (i % 2) {
    alert( i );
  }

}
```

From a technical point of view it's identical to the example above. Surely, we can just wrap the code in the `if` block instead of `continue`.

But as a side-effect we got one more figure brackets nesting level. If the code inside `if` is longer than a few lines, that may decrease the overall readability.
````

````warn header="No `break/continue` to the right side of '?'"
Please note that syntax constructs that are not expressions cannot be used in `'?'`. In particular, directives `break/continue` are disallowed there.

For example, if we take this code:

```js
if (i > 5) {
  alert(i);
} else {
  continue;
}
```

...And rewrite it using a question mark:


```js no-beautify
(i > 5) ? alert(i) : *!*continue*/!*; // continue not allowed here
```

...Then it stops working. The code like this will give a syntax error:


That's just another reason not to use a question mark operator `'?'` instead of `if`.
````

## Labels for break/continue

Sometimes we need to break out from multiple nested loops at once.

For example, in the code below we loop over `i` and `j` prompting for coordinates `(i, j)` from `(0,0)` to `(3,3)`:

```js run no-beautify
for (let i = 0; i < 3; i++) {

  for (let j = 0; j < 3; j++) {

    let input = prompt(`Value at coords (${i},${j})`, '');

    // what if I want to exit from here to Done (below)?

  }
}

alert('Done!');
```

We need a way to stop the process if the user cancels the input.

The ordinary `break` after `input` would only break the inner loop. That's not sufficient. Labels come to the rescue.

A *label* is an identifier with a colon before a loop:
```js
labelName: for (...) {
  ...
}
```

The `break <labelName>` statement in the loop breaks out to the label.

Like here:

```js run no-beautify
*!*outer:*/!* for (let i = 0; i < 3; i++) {

  for (let j = 0; j < 3; j++) {

    let input = prompt(`Value at coords (${i},${j})`, '');

    // if an empty string or canceled, then break out of both loops
    if (!input) *!*break outer*/!*; // (*)

    // do something with the value...
  }
}
alert('Done!');
```

In the code above `break outer` looks upwards for the label named `outer` and breaks out of that loop.

So the control goes straight from `(*)` to `alert('Done!')`.

We can also move the label onto a separate line:

```js no-beautify
outer:
for (let i = 0; i < 3; i++) { ... }
```

The `continue` directive can also be used with a label. In this case the execution jumps to the next iteration of the labeled loop.

````warn header="Labels are not a \"goto\""
Labels do not allow us to jump into an arbitrary place of code.

For example, it is impossible to do this:
```js
break label;  // jumps to label? No.

label: for (...)
```

The call to a `break/continue` is only possible from inside the loop, and the label must be somewhere upwards from the directive.
````

## Summary

We covered 3 types of loops:

- `while` -- The condition is checked before each iteration.
- `do..while` -- The condition is checked after each iteration.
- `for (;;)` -- The condition is checked before each iteration, additional settings available.

To make an "infinite" loop, usually the `while(true)` construct is used. Such a loop, just like any other, can be stopped with the `break` directive.

If we don't want to do anything on the current iteration and would like to forward to the next one, the `continue` directive does it.

`Break/continue` support labels before the loop. A label is the only way for `break/continue` to escape the nesting and go to the outer loop.
