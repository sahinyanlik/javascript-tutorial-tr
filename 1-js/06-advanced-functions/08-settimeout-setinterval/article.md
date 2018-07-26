#Zamanlama: setTimeout ve setInterval 

Bir fonksiyon hemen çalıştırılmak istenmeyebilir, belirli bir zaman sonra çalışması istenebilir. Buna "çağrıyı zamanlama" denir.

Bunun için iki metod var:

- `setTimeout` fonksiyonu belirli bir zaman sonra çalıştırmaya yarar.
- `setInterval` fonksiyonun belirli aralıklar ile sürekli çalışmasını sağlar.

Bu metodlar JavaScript'in tanımları arasında yer almaz. Fakat çoğu ortam bu metodları sunar. Daha özele inecek olursak tüm tarayıcılar ve NodeJS bu metodları sağlar.

[cut]

## setTimeout

Yazımı:

```js
let zamanlayiciId = setTimeout(fonk|kod, bekleme[, arg1, arg2...])
```

Parametreler:

`fonk|kod`
: Fonksiyon veya çalıştırılacak kodun karakter dizisi hali. Genelde bu fonksiyon olur. Uyumluluk dolayısıyla karakter dizisi de gönderilebilir fakat önerilmez.

`bekleme`
: Milisaniye cinsiden çalışmadan önceki bekleme süresi.(1000 ms = 1 saniye).

`arg1`, `arg2`...
: Fonksiyon için gerekli argümanlar.( IE9 öncesinde çalışmaz.)

Örneğin aşağıdaki kod `selamVer()` fonksiyonunu bir saniye sonra çalıştırır:

```js run
function selamVer() {
  alert('Selam');
}

*!*
setTimeout(selamVer, 1000);
*/!*
```

Argümanlı versiyonu:

```js run
function selamVer(ifade, kim) {
  alert( ifade + ', ' + kim );
}

*!*
setTimeout(selamVer, 1000, "Merhaba", "Ahmet"); // Merhaba, Ahmet
*/!*
```

Eğer ilk argüman karakter dizisi ise, sonrasında JavaScript bundan fonksiyon üretir.

Aşağıdaki de aynı şekilde çalışacaktır:

```js run no-beautify
setTimeout("selamVer('Merhaba')", 1000);
```
Karakter dizisi olarak fonksiyon göndermek aslında pek önerilmez, bunun yerine aşağıdaki gibi fonksiyon kullanılması daha doğrudur:

```js run no-beautify
setTimeout(() => alert('Merhaba'), 1000);
```

````smart header="Fonksiyon gönder fakat çalıştırma."
Yeni başlayan arkadaşlar bazen yanlışlıkla fonksiyonun sonuna `()` ekleyebilir:


```js
// yanlış!
setTimeout(selamVer(), 1000);
```

Bu çalışmaz, çünkü `setTimeout` referans bir fonksiyon beklemektedir. Burada `selamVer()` derseniz fonksiyonu çalıştırırsınız ve *bunun sonucu* `setTimeout` fonksiyonu tarafından kullanılır. Bizim durumumuzda `selamVer()` `undefined` döndürür. ( fonksiyon ile alakalı bir sorun yok ) bundan dolayı hiç birşey zamanlanmaz.
````

### clearTimeout fonksiyonu ile iptal etme

`setTimeout` çağrısı "timer identifier" döner. Bu `timerId` ile zamanlayıcıyı iptal edebiliriz.

Yazımı aşağıdaki gibidir:

```js
let timerId = setTimeout(...);
clearTimeout(timerId);
```

Aşağıdaki kodda önce bir zamanlayıcı test eder sonrasında ise bunu iptal eder. Sonuç olarak hiçbir şey olmaz:


```js run no-beautify
let timerId = setTimeout(() => alert("Birşey olmayacak"), 1000);
alert(timerId); // timer identifier

clearTimeout(timerId);
alert(timerId); // same identifier (iptal ettikten sonra null olmaz)
```

`alert` çıktısından da göreceğiniz gibi timer bir id numarası ile tanımlanır. Diğer ortamlarda bu başka birşey olabilir. Örneğin Node.Js bir sayı yerine farklı metodları olan timer objesi döner.

Tekrar söylemek gerekirse üzerinde anlaşılmış bir şartname bulunmamaktadır.

Tarayıcılar için zamanlayıcılar [zamanlayıcı bölümünde](https://www.w3.org/TR/html5/webappapis.html#timers) belirtilmiştir.

## setInterval

`setInterval` `setTimeout` ile aynı yazıma sahiptir:

```js
let timerId = setInterval(func|code, delay[, arg1, arg2...])
```

Tüm argümanlar aynı anlama gelir. Fakat `setTimeout`'a nazaran fonksiyonu sadece bir defa değil belirtilen zamanda sürekli olarak çalıştırır.

Bu zamanyalayıcı iptal etmek için `clearInterval(timerId)` kullanılmalıdır.

Aşağıdaki örnekte mesaj her iki saniyede bir gönderilecektir. 5 saniye sonunda ise durdurulur.

```js run
// her iki sn'de tekrar et
let timerId = setInterval(() => alert('tick'), 2000);

// 5 saniye sonunda durdur.
setTimeout(() => { clearInterval(timerId); alert('stop'); }, 5000);
```

```smart header="Popup ekranında Chrome/Opera/Safari zamanı durdurur."

IE ve Firefox tarayıcılarda ekranda `alert/confirm/prompt` olduğu sürece zamanlayıcı çalışmaya devam eder, fakat Chrome, Opera ve Safari bu zamanı durdurur.

Bundan dolayı eğer yukarıdi kodu çalıştırır ve iptal'e basmazsanız Firefox/IE'de bir sonraki `alert` durmadan gösterilir. Fakat Chrome/Opera/Safari'de kapatıldıktan sonra 2 sn sonra tekrar alert gelir.
```

## Tekrarlı setTimeout

Bir kodu düzenli olarak çalıştırmanın iki yolu bulunmaktadır.

İlki `setInterval` diğeri ise aşağıdaki gibi kullanılan `setTimeout`:

```js
/** instead of:
let timerId = setInterval(() => alert('tick'), 2000);
*/

let timerId = setTimeout(function tick() {
  alert('tick');
*!*
  timerId = setTimeout(tick, 2000); // (*)
*/!*
}, 2000);
```

`setTimeout` bir sonraki çağrıyı o anki çağrı bittiği ana planlar `(*)` 

Kendini tekrar eden `setTimeout` `setInterval`'den daha esnektir. Bu şekliyle kullanıldığında bir sonraki planlanan çağrı ana çağrının durumuna göre ötelebilir veya daha geriye alınabilir.

Örneğin, her 5 sn'de bir sunucudan veri isteyen bir servis yazmamız gerekmektedir. Fakat server'a fazladan yük binerse bunun 10,20,40 sn olarak değiştirilmesi gerekmektedir.

Sözde kod aşağıdaki gibidir:
```js
let delay = 5000;

let timerId = setTimeout(function request() {
  ...talep gönder...

  if (sunucu yüklenmesinden dolayı eğer talep iptal olursa) {
    // bir sonraki talep için gerekli süreyi uzat.
    delay *= 2;
  }

  timerId = setTimeout(request, delay);

}, delay);
```

Eğer CPU-aç görevleriniz varsa bu görevlerin süresini ölçüp buna göre bir çalışma planı oluşturmak mümkündür.


**Kendini tekrar eden `setTimeout` iki çağrı arasındaki süreyi garanti eder fkat `setInterval` bunu garanti etmez.**

Aşağıdaki iki kod parçacığı karşılaştırılacak olursa:

```js
let i = 1;
setInterval(function() {
  func(i);
}, 100);
```

İkincisi tekrarlı `setTimeout` kullanmaktadır.

```js
let i = 1;
setTimeout(function run() {
  func(i);
  setTimeout(run, 100);
}, 100);
```

`setInterval` `func(i)` fonksiyonunu her 100ms'de bir çalıştırır.

![](setinterval-interval.png)

Dikkatinizi çekti mi?...


**`func` çağrıları arasındaki geçen süre koddan daha kısa.**

Doğal olan bu aslında çünkü `func` çalıştığında bu arlığın bir kısmını harcar.

Hatta bu `func` çalışmasının bizim beklediğimiz `100ms`'den fazla olması da mümkündür.

Bu durumda JS Motoru `func` fonksiyonunun bitmesini bekler, sonra planlayıcıyı kontrol eder eğer zaman geçmişse hiç beklemeden tekrar çalıştırır.

Bu durumda ile karşılaşıldığında fonksiyon hiç beklemeden sürekli çalışır.

Aşağıda ise kendini çağıran `setTimeout` gösterilmiştir:

![](settimeout-interval.png)

**Kendini çağıran `setTimeout` arada geçen sürenin aynı olmasını garanti eder.(burada 100ms).**

Bunun nedeni yeni çağrının önceki çağrının bitiminde hesaplanmasından dolayıdır.

````smart header="Garbage collection" ( Çöp Toplama)


When a function is passed in `setInterval/setTimeout`, an internal reference is created to it and saved in the scheduler. It prevents the function from being garbage collected, even if there are no other references to it.

```js
// the function stays in memory until the scheduler calls it
setTimeout(function() {...}, 100);
```

For `setInterval` the function stays in memory until `cancelInterval` is called.

There's a side-effect. A function references the outer lexical environment, so, while it lives, outer variables live too. They may take much more memory than the function itself. So when we don't need the scheduled function any more, it's better to cancel it, even if it's very small.
````

## setTimeout(...,0)

There's a special use case: `setTimeout(func, 0)`.

This schedules the execution of `func` as soon as possible. But scheduler will invoke it only after the current code is complete.

So the function is scheduled to run "right after" the current code. In other words, *asynchronously*.

For instance, this outputs "Hello", then immediately "World":

```js run
setTimeout(() => alert("World"), 0);

alert("Hello");
```

The first line "puts the call into calendar after 0ms". But the scheduler will only "check the calendar" after the current code is complete, so `"Hello"` is first, and `"World"` -- after it.

### Splitting CPU-hungry tasks

There's a trick to split CPU-hungry task using `setTimeout`.

For instance, syntax highlighting script (used to colorize code examples on this page) is quite CPU-heavy. To hightlight the code, it performs the analysis, creates many colored elements, adds them to the document -- for a big text that takes a lot. It may even cause the browser to "hang", that's unacceptable.

So we can split the long text to pieces. First 100 lines, then plan another 100 lines using `setTimeout(...,0)`, and so on.

For clarity, let's take a simpler example for consideration. We have a function to count from `1` to `1000000000`.

If you run it, the CPU will hang. For server-side JS that's clearly noticeable, and if you are running it in-browser, then try to click other buttons on the page -- you'll see that whole JavaScript actually is paused, no other actions work until it finishes.

```js run
let i = 0;

let start = Date.now();

function count() {

  // do a heavy job
  for(let j = 0; j < 1e9; j++) {
    i++;
  }

  alert("Done in " + (Date.now() - start) + 'ms');
}

count();
```

The browser may even show "the script takes too long" warning (but hopefully won't, the number is not very big).

Let's split the job using the nested `setTimeout`:

```js run
let i = 0;

let start = Date.now();

function count() {

  // do a piece of the heavy job (*)
  do {
    i++;
  } while (i % 1e6 != 0);

  if (i == 1e9) {
    alert("Done in " + (Date.now() - start) + 'ms');
  } else {
    setTimeout(count, 0); // schedule the new call (**)
  }

}

count();
```

Now the browser UI is fully functional during the "counting" process.

We do a part of the job `(*)`:

1. First run: `i=1...1000000`.
2. Second run: `i=1000001..2000000`.
3. ...and so on, the `while` checks if `i` is evenly divided by `100000`.

Then the next call is scheduled in `(*)` if we're not done yet.

Pauses between `count` executions provide just enough "breath" for the JavaScript engine to do something else, to react on other user actions.

The notable thing is that both variants: with and without splitting the job by `setInterval` -- are comparable in speed. There's no much difference in the overall counting time.

To make them closer let's make an improvement.

We'll move the scheduling in the beginning of the `count()`:

```js run
let i = 0;

let start = Date.now();

function count() {

  // move the scheduling at the beginning
  if (i < 1e9 - 1e6) {
    setTimeout(count, 0); // schedule the new call
  }

  do {
    i++;
  } while (i % 1e6 != 0);

  if (i == 1e9) {
    alert("Done in " + (Date.now() - start) + 'ms');
  }

}

count();
```

Now when we start to `count()` and know that we'll need to `count()` more -- we schedule that immediately, before doing the job.

If you run it, easy to notice that it takes significantly less time.

````smart header="Minimal delay of nested timers in-browser"
In the browser, there's a limitation of how often nested timers can run. The [HTML5 standard](https://www.w3.org/TR/html5/webappapis.html#timers) says: "after five nested timers..., the interval is forced to be at least four milliseconds.".

Let's demonstrate what it means by the example below. The `setTimeout` call in it re-schedules itself after `0ms`. Each call remembers the real time from the previous one in the `times` array. What the real delays look like? Let's see:

```js run
let start = Date.now();
let times = [];

setTimeout(function run() {
  times.push(Date.now() - start); // remember delay from the previous call

  if (start + 100 < Date.now()) alert(times); // show the delays after 100ms
  else setTimeout(run, 0); // else re-schedule
}, 0);

// an example of the output:
// 1,1,1,1,9,15,20,24,30,35,40,45,50,55,59,64,70,75,80,85,90,95,100
```

First timers run immediately (just as written in the spec), and then the delay comes into play and we see `9, 15, 20, 24...`.

That limitation comes from ancient times and many scripts rely on it, so it exists for historical reasons.

For server-side JavaScript, that limitation does not exist, and there exist other ways to schedule an immediate asynchronous job, like [process.nextTick](https://nodejs.org/api/process.html) and [setImmediate](https://nodejs.org/api/timers.html) for Node.JS. So the notion is browser-specific only.
````

### Allowing the browser to render

Another benefit for in-browser scripts is that they can show a progress bar or something to the user. That's because the browser usually does all "repainting" after the script is complete.

So if we do a single huge function then even if it changes something, the changes are not reflected in the document till it finishes.

Here's the demo:
```html run
<div id="progress"></div>

<script>
  let i = 0;

  function count() {
    for(let j = 0; j < 1e6; j++) {
      i++;
      // put the current i into the <div>
      // (we'll talk more about innerHTML in the specific chapter, should be obvious here)
      progress.innerHTML = i;
    }
  }

  count();
</script>
```

If you run it, the changes to `i` will show up after the whole count finishes.

And if we use `setTimeout` to split it into pieces then changes are applied in-between the runs, so this looks better:

```html run
<div id="progress"></div>

<script>
  let i = 0;

  function count() {

    // do a piece of the heavy job (*)
    do {
      i++;
      progress.innerHTML = i;
    } while (i % 1e3 != 0);

    if (i < 1e9) {
      setTimeout(count, 0);
    }

  }

  count();
</script>
```

Now the `<div>` shows increasing values of `i`.

## Summary

- Methods `setInterval(func, delay, ...args)` and `setTimeout(func, delay, ...args)` allow to run the `func` regularly/once after `delay` milliseconds.
- To cancel the execution, we should call `clearInterval/clearTimeout` with the value returned by `setInterval/setTimeout`.
- Nested `setTimeout` calls is a more flexible alternative to `setInterval`. Also they can guarantee the minimal time *between* the executions.
- Zero-timeout scheduling `setTimeout(...,0)` is used to schedule the call "as soon as possible, but after the current code is complete".

Some use cases of `setTimeout(...,0)`:
- To split CPU-hungry tasks into pieces, so that the script doesn't "hang"
- To let the browser do something else while the process is going on (paint the progress bar).

Please note that all scheduling methods do not *guarantee* the exact delay. We should not rely on that in the scheduled code.

For example, the in-browser timer may slow down for a lot of reasons:
- The CPU is overloaded.
- The browser tab is in the background mode.
- The laptop is on battery.

All that may decrease the minimal timer resolution (the minimal delay) to 300ms or even 1000ms depending on the browser and settings.
