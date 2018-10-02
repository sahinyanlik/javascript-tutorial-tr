Önem: 5

---

# Kısma Dekoratörleri

"Sıkma" dekoratörü `throttle(f,ms)` oluşturun ve bu bir kapsayıcı döndersin, bu kapsayıcı çağrıyı `f`'e iletsin ve bu çağrıyı belirtilen `ms` içerisinde sadece bir defa yapabilsin. Geri kalan "cooldown" periyodundakiler görmezden gelinsin.

** `Geri sektiren` dekoratör ile `Kısma` dekoratörü arasındaki fark; görmezden gelinen çağrı eğer belirlenen süre zarfında yaşayabilirse, gecikme sonrasında çağırılır.

Daha iyi anlayabilmek için günlük kullanılan bir uygulamadan yararlanabiliriz.

**Örneğin fare olaylarını takip etmek istiyorsunuz.**

Browser üzerinde bir fonksiyon ile farenin her mikro seviyeli hareketinde gittiği yerlerin bilgileri alınabilir. Aktif fare kullanımı sırasında akıcı bir şekilde çalışacaktır. Her sn'de 100 defa ( 10ms ) çalışabilir.

**İzleme fonksiyonu web sayfası üzerinde bazı bilgileri güncellemeli.**

Updating function `update()` is too heavy to do it on every micro-movement. There is also no sense in making it more often than once per 100ms.

So we'll assign `throttle(update, 100)` as the function to run on each mouse move instead of the original `update()`. The decorator will be called often, but `update()` will be called at maximum once per 100ms.

Visually, it will look like this:

1. For the first mouse movement the decorated variant passes the call to `update`. That's important, the user sees our reaction to his move immediately.
2. Then as the mouse moves on, until `100ms` nothing happens. The decorated variant ignores calls.
3. At the end of `100ms` -- one more `update` happens with the last coordinates. 
4. Then, finally, the mouse stops somewhere. The decorated variant waits until `100ms` expire and then runs `update` runs with last coordinates. So, perhaps the most important, the final mouse coordinates are processed.

A code example:

```js
function f(a) {
  console.log(a)
};

// f1000 passes calls to f at maximum once per 1000 ms
let f1000 = throttle(f, 1000);

f1000(1); // shows 1
f1000(2); // (throttling, 1000ms not out yet)
f1000(3); // (throttling, 1000ms not out yet)

// when 1000 ms time out...
// ...outputs 3, intermediate value 2 was ignored
```

P.S. Arguments and the context `this` passed to `f1000` should be passed to the original `f`.
