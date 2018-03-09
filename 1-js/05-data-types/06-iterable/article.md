
# Sıralı erişim ( Iterable )

*Iterable* objeleri dizilerin genelleştirilmiş halidir. Bu her objenin `for..of` döngüsünde kullanılmasına olanak verir.

Diziler zaten tekrarlanabilirdir. Fakat sadece diziler değil, karakter dizileri de tekrarlanabilir.

Sıralı erişim JavaScript çekirdeğince oldukça fazla kullanılır. Varolan operatörler ve metodların birçoğu buna bel bağlar.

[cut]

## Symbol.iterator

Sıralı erişimin matığını en iyi şekilde kendimiz bir tane yaparak anlayabiliriz.

Örneğin bir objeniz var, dizi değil, fakat `for..of` için uygun duruyor.

Örneğin `aralik` objesi iki sayı arasını tanımlasın.

```js
let aralik = {
  baslangic: 1,
  bitis: 5
};

// for..of 'un
// for(let sayi of aralik) ... sayi=1,2,3,4,5 şeklinde çalışmasını istiyoruz.
```
`aralik`'e sıralı erişim yapabilmek ( `for..of` ile çalıştırabilmek  ) için `Symbol.iterator` isminde bir metoda sahip olması gerekmektedir. ( özel bir sembol)


- `for..of` başladığında, bu metod çağırılır ve eğer bulunamazsa hata verir.
- metod *iterator* döndürmelidir. ( Sıralı erişim objesi) bu obje `next` metoduna sahip olmalıdır.
- `for..of` bir sonraki değeri istediğinde `next()` metodu çağırılacaktır.
- `next()` metodu sonrasında `{done:Boolean, value:any}`, `done = true` dönerse sıralı erişimin bittiği anlaşılır. Aksi halde `value` yeni değer olacaktır.

Aşağıda `aralik` fonksiyonunun uygulamasını görebilirsiniz:


```js run
let aralik = {
  baslangic: 1,
  bitis: 5
};

// for..of çağırıldığında doğrudan aşağıdaki metod çağırılır.
aralik[Symbol.iterator] = function() {

  // 2. geriye sıralı erişim elemanı döndürür:
  return {
    current: this.baslangic,
    last: this.bitis,      

    // 3. next() is called on each iteration by the for..of loop
    // for..of her defasında next() metodunu çağırır.
    next() {
      // 4. bu metod geriye şu şakilde obje döndürmeli {done:.., value :...}
      if (this.current <= this.last) {
        return { done: false, value: this.current++ };
      } else {
        return { done: true };
      }
    }
  };
};

// çalışması!
for (let num of aralik) {
  alert(num); // 1, then 2, 3, 4, 5
}
```
Bu kod için bir tane çok önemli problem mevcuttur:

- `aralik` fonksiyonunun kendisi `next()` metoduna sahip değildir.
- Bunun yerine, diğer bir obje, `aralik[Symbol.iterator]()`  ile yaratılmaktadır ve bu sıralı erişimi sağlar.

Bundan dolayı sıralı erişim objesi aslında sıralı erişilecek objeden farklıdır.

Teknik olarak `aralik` içerisine bu metodu yazarak kodu daha sade yapabiliriz.

Aşağıdaki gibi:
```js run
let aralik = {
  baslangic: 1,
  bitis: 5,

  [Symbol.iterator]() {
    this.current = this.baslangic;
    return this;
  },

  next() {
    if (this.current <= this.bitis) {
      return { done: false, value: this.current++ };
    } else {
      return { done: true };
    }
  }
};

for (let num of aralik) {
  alert(num); // 1, then 2, 3, 4, 5
}
```
Şu anda `aralik[Symbol.iterator]()` gerçek `aralik` objesini gönderir: gerekli olan `next()` metodunu dönderir ve o anki tekrar durumunu `this.current` ile hatırlar. Bazen bu da iyidir. Bunun kötü tarafı ise iki tane `for..of` olamamasıdır. Çünkü bu döngüler objelerin üzerinden aynı anda geçerler: tek bir tane obje olduğundan dolayı döngünün durumunu paylaşırlar bu da karışıklığa neden olur.

```smart header="Sonsuz sıralı döngüler"

Sonsuz sıralı döngüler de yapılabilirdir. Örneğin `aralik` `range.to = Infinity` olursa sonsuza kadar gider. Bunun yanında rasgele sayılar üreterek bu sırayı öldürmeyen bir döngü yapmak da mümkündür.

`next` için bir limitasyon yoktur, istendiği kadar çok değer gönderebilir.

Tabiki böyle bir durumda `for..of` döngüsü sonsuza kadar devam eder. Bunun yanında bu döngüyü `break` ile kırmakta mümkündür.
```


## Karakter dizilerine sıralı erişim

Diziler ve karakter dizileri(string) en fazla kullanılan sıralı erişime sahip tiplerdir.

Karakter için `for..of` karakterleri üzerinden geçer:

```js run
for(let char of "test") {
  alert( char ); // t, sonra e, sonra s, sonra t
}
```
Vekil çiflerin yerine geçerek de çalışabilir.

```js run
let str = '𝒳😂';
for(let char of str) {
    alert(char); // 𝒳, sonra 😂
}
```

## Sıralı erişim elemanlarını dışardan çağırma

Normalde, sıralı erişim elemanları dışardan kod çağırmaya kapatılmıştır. `for..of` döngüsü çalışır ve bu da tek bilinmesi gereken olaydır.

Olayı daha derinlemesine anlayabilmek için dışarıdan nasıl sıralı erişim yaratılır buna bakalım.

Karakter dizisini aynı `for..of` gibi döneceğiz fakat doğrudan çağrılarla. Bu kod karakter dizisi erişim elemanını alır ve bunu *manuel* bir şekilde yapar:

```js run
let str = "Hello";

// for (let char of str) alert(char);
// ile aynı şekilde çalışır

let iterator = str[Symbol.iterator]();

while(true) {
  let result = iterator.next();
  if (result.done) break;
  alert(result.value); //karakterlerin bir bir çıktısını verir.
}
```
Buna çok nadir ihtiyaç olur. Fakat bu bize `for..of`'tan daha fazla kontrol yetkisi verir. Örneğin bu sıralı erişim olayını bazen çalıştırıp bazen çalıştırma veya o ara birşeyler yaptırma mümkün olmaktadır.

## Döngüler ve dizi-benzerleri

İki tane resmi tanım vardır. Birbirlerine çok benzeseler de aslında çok farklıdırlar. Lütfen ikisini de iyi bir şekilde anlayın böylece karmaşıklıktan kurtulabilirsiniz.

- *Iterables*  `Symbol.iterator` methodunun uygulamasını yapan objelerdir.
- *Array-likes* index ve `length` özelliklerine sahip dizi benzeri objelerdir.

Doğal olarak bu özellikler birleştirilebilir. Örneğin, karakterler hem iterable(sıralı döngü elemanı, `for..of` kullanmaya müsaittir) hemde dizi benzeri ( sayısal indeksleri bulunur ve `length` özelliğine sahiptirler.)

Fakat her *iterable* obje dizi benzeri olmayabilir. Diğeri de doğrudur yani her dizi benzeri, *iterable* olmayabilir.

Örneğin, yukarıda bulunan `aralık` fonksiyonu *iterable*'dır. Fakat dizi benzeri değildir. Çünkü indekslenmiş özellikleri veya `length` özelliği bulunmamaktadır.

Aşağıda dizi benzeri olan fakat *iterable* olmayan obje gösterilmiştir.

```js run
let diziBenzeri = { //  indekslere ve uzunluğa sahiptir => dizi-benzeri
  0: "Merhaba",
  1: "Dünya",
  length: 2
};

*!*
// Hata Symbol.iterator bulunmamaktadır.
for(let item of arrayLike) {}
*/!*
```

Ortak noktalaraı ikisinin de *dizi* olmamasıdır. Bunların `push` veya `pop` gibi metodları bulunmamaktadır. Eğer dizi ile çalışmak istiyorsanız bunlar yetersiz kalırlar.

## Array.from

Bunları bir araya getirip dizi yapmaya yarayan [Array.from](mdn:js/Array/from) metodudur. Sonrasında dizi metodları çağrılabilir.

Örneğin:

```js run
let diziBenzeri = {
  0: "Merhaba",
  1: "Dünya",
  length: 2
};

*!*
let arr = Array.from(diziBenzeri); // (*)
*/!*
alert(arr.pop()); // Dünya (metod çalışmakta)
```

`(*)` satırında bulunan `Array.from` objeyi alır. Objenin sıralı erişim objesi mi yoksa dizi-benzeri mi olduğunu kontrol eder ve ardından bu değerleri kopyalayarak yeni dizi yaratır.

Aynısı sıralı erişim objesi için de yapılabilir:

```js
// Aralığın yukarıdaki örnekten alındığını varsayarsanız.
let arr = Array.from(aralik);
alert(arr); // 1,2,3,4,5 (dizinin toString metodu çalışır)
```
Bunun yanında `Array.from` metodu opsiyonel olarak "mapping" fonksiyonuna izin verir:

```js
Array.from(obj[, mapFn, thisArg])
```
`mapFn` argümanı her elemanın diziye eklenmeden önce uygulanacağı fonksiyondur, ve `thisArg` bunun için `this`i ayarlar.

Örneğin:

```js
// aralik'in yukarıdan alındığı varsayılırsa

// her sayının karesinin alınması.
let arr = Array.from(aralik, num => num * num);

alert(arr); // 1,4,9,16,25
```

burada `Array.from` kullanarak karakter karakter dizisi haline getirilmiştir.
 
```js run
let str = '𝒳😂';

// karakterden karakterler dizisi yapma
let chars = Array.from(str);

alert(chars[0]); // 𝒳
alert(chars[1]); // 😂
alert(chars.length); // 2
```

`str.split`'e benzemeksizin, karakter dizisinin tekrar edilebilirliğine göre `for..of` gibi vekil çiftler ile doğru bir şekilde çalışır.

Teknik olarak burada da aynısı yapılmaktadır:

```js run
let str = '𝒳😂';

let chars = []; // Array.from içinde aynı şeyi yapmaktadır.
for(let char of str) {
  chars.push(char);
}

alert(chars);
```

...fakat daha kısa.    

Hatta vekil-farkında `slice` yapılabilir. 

```js run
function slice(str, start, end) {
  return Array.from(str).slice(start, end).join('');
}

let str = '𝒳😂𩷶';

alert( slice(str, 1, 3) ); // 😂𩷶

// native method does not support surrogate pairs
alert( str.slice(1, 3) ); // garbage (two pieces from different surrogate pairs)
```


## Summary

Objects that can be used in `for..of` are called *iterable*.

- Technically, iterables must implement the method named `Symbol.iterator`.
    - The result of `obj[Symbol.iterator]` is called an *iterator*. It handles the further iteration process.
    - An iterator must have the method named `next()` that returns an object `{done: Boolean, value: any}`, here `done:true` denotes the iteration end, otherwise the `value` is the next value.
- The `Symbol.iterator` method is called automatically by `for..of`, but we also can do it directly.
- Built-in iterables like strings or arrays, also implement `Symbol.iterator`.
- String iterator knows about surrogate pairs.


Objects that have indexed properties and `length` are called *array-like*. Such objects may also have other properties and methods, but lack built-in methods of arrays.

If we look inside the specification -- we'll see that most built-in methods assume that they work with iterables or array-likes instead of "real" arrays, because that's more abstract.

`Array.from(obj[, mapFn, thisArg])` makes a real `Array` of an iterable or array-like `obj`, and then we can use array methods on it. The optional arguments `mapFn` and `thisArg` allow to apply a function to each item.
