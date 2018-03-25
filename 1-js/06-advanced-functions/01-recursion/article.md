# Kendini tekrarlayan ( özçağrı )  ve yığın

Bu bölümde fonksiyonlar daha derinlemesine incelenecektir.

İlk konu *özçağrı*  olacaktır.

Eğer daha önce program yazdıysanız bir sonraki bölüme geçebilirsiniz.

Öz çağrı bir programlama desenidir. Bu desen bir görevin aynı türde daha basit görevcikler haline getirilmesini sağlar. Veya bir görev daha kolay aksiyonlara ve aynı şekilde görevlere dönüştürülebildiğinde, veya daha sonra göreceğiniz gibi, belirli veri yapılarında kullanılabilir.

Bir fonksiyon problemi çözerken birçok farklı fonksiyonu çağırabilir. Bu özel durumda ise fonksiyon *kendisini* çağırır. Bu olaya *özçağrı*, *recursion* denir.

[cut]

## Çift yönlü düşünme
Başlangıçta `us(x,n)` adında bir fonksiyon olsun ve bu `n` üssü `x` i hesaplasın. Diğer bir ifadeyle `x`'i `n` defa kendisiyle çarpsın.

```js
us(2, 2) = 4
us(2, 3) = 8
us(2, 4) = 16
```

Bunu uygulamanın iki yolu bulunmaktadır.

1. Tekrarlı düşünürseniz: `for` döngüsü:

    ```js run
    function us(x, n) {
      let sonuc = 1;
      // x'in x defa kendisiyle çarpımı.
      for(let i = 0; i < n; i++) {
        sonuc *= x;
      }

      return sonuc;
    }

    alert( us(2, 3) ); // 8
    ```

2. Özçağrı: işi daha basite indirgeyerek kendisini çağırsın:

    ```js run
    function us(x, n) {
      if (n == 1) {
        return x;
      } else {
        return x * us(x, n - 1);
      }
    }

    alert( us(2, 3) ); // 8
    ```

Dikkat ederseniz özçağrı fonksiyonu aslen farklıdır.
`us(x,n)` çağrıldığında çalıştırılma iki dala ayrılır.

```js
              if n==1  = x
             /
us(x, n) =
             \       
              else     = x * us(x, n - 1)
```

1. Eğer `n==1` ise geriye kalanlar önemsizdir. Buna *temel* özçağrı denir, çünkü bu belirli bir sonucu çıktı verir: `us(x,1)` eşittir `x` 

2. Diğer türlü `us(x,n)` `x*us(x,n-1)` şeklinde ifade edilebilir. Matematiksel olarak <code>x<sup>n</sup> = x * x<sup>n-1</sup></code> şeklinde ifade edilebilir. Buna *öztekrar basamağı* denir. Görev daha küçük aksiyonlara ( `x` ile çarpma ) indirgenmiş olur. Ayrıca aynı görevi daha basit görevlerle ( `us`'ün daha küçük `n` değeri) indirgenmiş oldu. Bir sonraki sitep ise bunun daha basite indirgene indirgene `n`'in `1` e ulaşmasını sağlamaktır.

Buna `us` *öz çağrı ile* kendisini `n==1` olana kadar çağırır diyebiliriz.

![özçağrı diyagramı](recursion-pow.png)


`us(2,4)`'ü hesaplayabilmek için *özçağrı* şu adımları gerçekleştirir:

1. `us(2, 4) = 2 * us(2, 3)`
2. `us(2, 3) = 2 * us(2, 2)`
3. `us(2, 2) = 2 * us(2, 1)`
4. `us(2, 1) = 2`


*özçağrı* böylece fonksiyon çağrılarını dah abasite indirgemiştir. Daha sonra daha basite ve en sonunda sonuç belirli olana kadar devam etmiştir.

````smart header="Özçağrı genelde tekrarlı olana göre daha kısadır"

Aşağıda aynı fonksiyonun `?` ile tekrar yazılmış hali bulunmaktadır. 

```js run
function us(x, n) {
  return (n == 1) ? x : (x * pow(x, n-1));
}
```
````
Maksimum iç içe çağırma sayısına *özçağrı derinliği* `us` fonksiyonunda bu `n`'dir.

JavaScript motorları maksimum özçağrı derinliğini sınırlamaktadır. Bazı motorlarda 10000, bazılarında 100000 limiti bulunmaktadır. Bunun için otomatik optimizasyonlar bulunmaktadır. Fakat yine de her motorda desteklenmemektedir ve çok basit durumlarda kullanılır.

Bu özçağrı uygulamalarını limitler, fakat yine de çoğu yerde kullanılmaktadırlar. Çoğu görevde özçağrı şeklinde düşünmek daha basit ve sürdürülebilir bod yazmanızı sağlayacaktır.

## Çalıştırma Yığını

Peki *özçağrılar* nasıl çalışır. Bunun için fonksiyonların içinde ne yaptıklarına bakmak gerekmektedir.

Çalışan fonksiyon hakkında bilgi *çalıştırma kaynağında* tutulur.

[Çalıştırma Kaynağı -  Execution Context](https://tc39.github.io/ecma262/#sec-execution-contexts) fonksiyonun çalışması hakkında detayları tutan dahili bir veri yapısıdır: Kontrol akışı nerede, o anki değişkenlerin değeri, `this` neye denk gelir ve bunun gibi detaylar dahili detaylar tutar.

Her fonksiyon çağrısı kendine ait çalıştırma kaynağı tutar.

Eğer bir fonksiyon içeride başka bir çağrı yaparsa şunlar olur:

- O anki fonksiyon durur.
- Bu fonksiyon ile ilintili çalışma kaynağı *çalışma kaynağı yığını* veri yapısı şeklinde kaydedilir.
- Dallanılan çağrı çalıştırılır.
- Bu işlem bittikten sonra çalışma kaynağı yığınından daha önceki çalışmakta olan yer geri alınır, böylece fonksiyon kaldığı yerden görevini tamamlayabilir.

Aşağıda `us(2,3)`'ün çalışması gösterilmiştir.

### us(2, 3)

`us(2,3)` çağrısının başlangıcında, çalışma kaynağı değişkenleri `x=2,n=3` olacak şekilde tutar. Çalışma şu anda birinci satırdadır. 

Bu aşağıdaki gibi gösterilebilir:
<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Çalışma kaynağı: { x: 2, n: 3, birinci satırda }</span>
    <span class="function-execution-context-call">us(2, 3)</span>
  </li>
</ul>

Ardından fonksiyon çalışmaya başlar. `n==1` şartı yanlıştır, bundan dolayı ikinci `if`'e geçer.

```js run
function us(x, n) {
  if (n == 1) {
    return x;
  } else {
*!*
    return x * us(x, n - 1);
*/!*
  }
}

alert( us(2, 3) );
```

Değişkenler aynı fakat satır değiştir, şimdiki kaynak şu şekilde:

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Kaynak: { x: 2, n: 3, 5. satırda }</span>
    <span class="function-execution-context-call">us(2, 3)</span>
  </li>
</ul>

`x*pow(x, n-1)`'i hesaplayabilmek için `us` fonksiyonuna `us(2,2)` şeklinde yeni bir çağrı yapılmalıdır.

### us(2, 2)

Dallanma işleminin yapılabilmesi için JavaScript'in öncelikle o anki çalışma durumunu *çalışma kaynağı yığını*na atması gerekmektedir.

Burada `us` fonksiyonu çağrılmıştır. Bu herhangi bir fonksiyon da olabilirdi, aralarında bu yönden hiç bir farklılık bulunmamaktadır:

1. O anki kaynak yığının en üstüne "hatırlatılır"
2. Alt çağrı için yeni bir kaynak yaratılır.
3. Alt çağrılar bittiğinde -- bir önceki kaynak yığından alınır ve çalışmasına devam eder.

Aşağıda `pow(2,2)` altçağrısına girildiğinde kaynak yığınının durumu gösterilmektedir.

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Kaynak: { x: 2, n: 2, 1. satırda }</span>
    <span class="function-execution-context-call">us(2, 2)</span>
  </li>
  <li>
    <span class="function-execution-context">Kaynak: { x: 2, n: 3, 5. satırda }</span>
    <span class="function-execution-context-call">us(2, 3)</span>
  </li>
</ul>

Üst tarafta o anda çalışan kaynak ( kalın harflerle ), alt tarafta ise "hatırlatılan" kaynak bulunmaktadır.

Altçağrı bittiğinde, daha önceki kalınan kaynaktan devam etmek kolaydır. Çünkü bu her iki değişkeni ve kaldığı satırı tutmaktadır. Burada "satır" denmesine rağmen aslında bunun daha net birşey olduğu bilinmelidir.


### us(2, 1)

İşlem tekrar ediyor: `5.` satırda yeni bir altçağrı yapılmaktadır, argümanlar ise `x=2`, `n=1` şeklindedir.

Yeni çalışma yığını oluşturur, bir önceki yığının üstüne itelenir. 
A new execution context is created, the previous one is pushed on top of the stack:

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Kaynak: { x: 2, n: 1, 1. satır }</span>
    <span class="function-execution-context-call">us(2, 1)</span>
  </li>
  <li>
    <span class="function-execution-context">Kaynak: { x: 2, n: 2, 5. satır }</span>
    <span class="function-execution-context-call">us(2, 2)</span>
  </li>
  <li>
    <span class="function-execution-context">Kaynak: { x: 2, n: 3, 5.satır }</span>
    <span class="function-execution-context-call">us(2, 3)</span>
  </li>
</ul>

Şu anda 2 eski kaynak ve 1 tane çalışmakta olan kaynak bulunmaktadır `us(2,1)`

### Çıkış
`us(2,1)` çalışırken diğerlerinin aksine `n==1` şartı sağlanır, bundan dolayı ilk defa birinci `if` çalışır.

```js
function us(x, n) {
  if (n == 1) {
*!*
    return x;
*/!*
  } else {
    return x * us(x, n - 1);
  }
}
```
Daha fazla dallanan çağrı olmadığından dolayı fonksiyon sona erer ve değeri döner.

Fonksiyon bittiğinden dolayı, çalışma kaynağına gerek kalmamıştır ve dolayısıyla hafızadan silinir. Bir önceki yığından alınır:


<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Kaynak: { x: 2, n: 2, 5. satırda }</span>
    <span class="function-execution-context-call">us(2, 2)</span>
  </li>
  <li>
    <span class="function-execution-context">Kaynak: { x: 2, n: 3, 5. satırda }</span>
    <span class="function-execution-context-call">us(2, 3)</span>
  </li>
</ul>

`us(2,2)` nin çalışması devam etti. `us(2,1)`'in sonucuna sahip olduğundan `x * us(x,n-1)`'in sonucunu bulabilir, bu da `4`'tür.

Ardından bir önceki kaynak geri yüklenir:

<ul class="function-execution-context-list">
  <li>
    <span class="function-execution-context">Kaynak: { x: 2, n: 3, 5. satırda }</span>
    <span class="function-execution-context-call">us(2, 3)</span>
  </li>
</ul>

İşlemler bittiğinde, `us(2,3) = 8` sonucu alınır.

Bu durumda özçağrı derinliği **3**tür

Yukarıda da görüldüğü üzere, özçağrı derinliği yığındaki kaynak sayısı demektir. Bu drumda `n`'in üssü değiştirildiğinde daha fazla hafıza kullanacaktır.

Döngü bazlı algoritma daha az hafıza kullanacaktır:

```js
function us(x, n) {
  let sonuc = 1;

  for(let i = 0; i < n; i++) {
    sonuc *= x;
  }

  return sonuc;
}
```

Tekrar eden `us` fonksiyonu `i` ve `sonuc` kaynağını kullanır ve sürekli bunları değiştirir. Hafıza gereksinimleri oldukça azdır ve bu hafıza büyüklüğü `n`'e bağlı değildir.

**Tüm özçağrılar döngü olarak yazılabilir. Döngü versiyonu daha az kaynak gerektirecektir**

... Bazen yeniden yazmak çok kolay değildir, özellikle fonksiyon alt çağrılarda özçağrı kullanıyorsa, bu çağrılar sonucunda daha karmaşık dallanmalar oluyor ise optimizasyon değmeyebilir.

Özçağrı fonksiyonun daha kısa kod ile yazılmasını sağlar, ayrıca anlaşılmayı da kolaylaştırır. Optimizasyon her yerde gerekli değildir, genelde iyi kod gereklidir, bunun için kullanılır.

## Özçağrı Recursive traversals

Another great application of the recursion is a recursive traversal.

Imagine, we have a company. The staff structure can be presented as an object:

```js
let company = {
  sales: [{
    name: 'John',
    salary: 1000
  }, {
    name: 'Alice',
    salary: 600
  }],

  development: {
    sites: [{
      name: 'Peter',
      salary: 2000
    }, {
      name: 'Alex',
      salary: 1800
    }],

    internals: [{
      name: 'Jack',
      salary: 1300
    }]
  }
};
```

In other words, a company has departments.

- A department may have an array of staff. For instance, `sales` department has 2 employees: John and Alice.
- Or a department may split into subdepartments, like `development` has two branches: `sites` and `internals`. Each of them has the own staff.
- It is also possible that when a subdepartment grows, it divides into subsubdepartments (or teams).

    For instance, the `sites` department in the future may be split into teams for `siteA` and `siteB`. And they, potentially, can split even more. That's not on the picture, just something to have in mind.

Now let's say we want a function to get the sum of all salaries. How can we do that?

An iterative approach is not easy, because the structure is not simple. The first idea may be to make a `for` loop over `company` with nested subloop over 1st level departments. But then we need more nested subloops to iterate over the staff in 2nd level departments like `sites`. ...And then another subloop inside those for 3rd level departments that might appear in the future? Should we stop on level 3 or make 4 levels of loops? If we put 3-4 nested subloops in the code to traverse a single object, it becomes rather ugly.

Let's try recursion.

As we can see, when our function gets a department to sum, there are two possible cases:

1. Either it's a "simple" department with an *array of people* -- then we can sum the salaries in a simple loop.
2. Or it's *an object with `N` subdepartments* -- then we can make `N` recursive calls to get the sum for each of the subdeps and combine the results.

The (1) is the base of recursion, the trivial case.

The (2) is the recursive step. A complex task is split into subtasks for smaller departments. They may in turn split again, but sooner or later the split will finish at (1).

The algorithm is probably even easier to read from the code:


```js run
let company = { // the same object, compressed for brevity
  sales: [{name: 'John', salary: 1000}, {name: 'Alice', salary: 600 }],
  development: {
    sites: [{name: 'Peter', salary: 2000}, {name: 'Alex', salary: 1800 }],
    internals: [{name: 'Jack', salary: 1300}]
  }
};

// The function to do the job
*!*
function sumSalaries(department) {
  if (Array.isArray(department)) { // case (1)
    return department.reduce((prev, current) => prev + current.salary, 0); // sum the array
  } else { // case (2)
    let sum = 0;
    for(let subdep of Object.values(department)) {
      sum += sumSalaries(subdep); // recursively call for subdepartments, sum the results
    }
    return sum;
  }
}
*/!*

alert(sumSalaries(company)); // 6700
```

The code is short and easy to understand (hopefully?). That's the power of recursion. It also works for any level of subdepartment nesting.

Here's the diagram of calls:

![recursive salaries](recursive-salaries.png)

We can easily see the principle: for an object `{...}` subcalls are made, while arrays `[...]` are the "leaves" of the recursion tree, they give immediate result.

Note that the code uses smart features that we've covered before:

- Method `arr.reduce` explained in the chapter <info:array-methods> to get the sum of the array.
- Loop `for(val of Object.values(obj))` to iterate over object values: `Object.values` returns an array of them.


## Recursive structures

A recursive (recursively-defined) data structure is a structure that replicates itself in parts.

We've just seen it in the example of a company structure above.

A company *department* is:
- Either an array of people.
- Or an object with *departments*.

For web-developers there are much better known examples: HTML and XML documents.

In the HTML document, an *HTML-tag* may contain a list of:
- Text pieces.
- HTML-comments.
- Other *HTML-tags* (that in turn may contain text pieces/comments or other tags etc).

That's once again a recursive definition.

For better understanding, we'll cover one more recursive structure named "Linked list" that might be a better alternative for arrays in some cases.

### Linked list

Imagine, we want to store an ordered list of objects.

The natural choice would be an array:

```js
let arr = [obj1, obj2, obj3];
```

...But there's a problem with arrays. The "delete element" and "insert element" operations are expensive. For instance, `arr.unshift(obj)` operation has to renumber all elements to make room for a new `obj`, and if the array is big, it takes time. Same with `arr.shift()`.

The only structural modifications that do not require mass-renumbering are those that operate with the end of array: `arr.push/pop`. So an array can be quite slow for big queues.

Alternatively, if we really need fast insertion/deletion, we can choose another data structure called a [linked list](https://en.wikipedia.org/wiki/Linked_list).

The *linked list element* is recursively defined as an object with:
- `value`.
- `next` property referencing the next *linked list element* or `null` if that's the end.

For instance:

```js
let list = {
  value: 1,
  next: {
    value: 2,
    next: {
      value: 3,
      next: {
        value: 4,
        next: null
      }
    }
  }
};
```

Graphical representation of the list:

![linked list](linked-list.png)

An alternative code for creation:

```js no-beautify
let list = { value: 1 };
list.next = { value: 2 };
list.next.next = { value: 3 };
list.next.next.next = { value: 4 };
```

Here we can even more clearer see that there are multiple objects, each one has the `value` and `next` pointing to the neighbour. The `list` variable is the first object in the chain, so following `next` pointers from it we can reach any element.

The list can be easily split into multiple parts and later joined back:

```js
let secondList = list.next.next;
list.next.next = null;
```

![linked list split](linked-list-split.png)

To join:

```js
list.next.next = secondList;
```

And surely we can insert or remove items in any place.

For instance, to prepend a new value, we need to update the head of the list:

```js
let list = { value: 1 };
list.next = { value: 2 };
list.next.next = { value: 3 };
list.next.next.next = { value: 4 };

*!*
// prepend the new value to the list
list = { value: "new item", next: list };
*/!*
```

![linked list](linked-list-0.png)

To remove a value from the middle, change `next` of the previous one:

```js
list.next = list.next.next;
```

![linked list](linked-list-remove-1.png)

We made `list.next` jump over `1` to value `2`. The value `1` is now excluded from the chain. If it's not stored anywhere else, it will be automatically removed from the memory.

Unlike arrays, there's no mass-renumbering, we can easily rearrange elements.

Naturally, lists are not always better than arrays. Otherwise everyone would use only lists.

The main drawback is that we can't easily access an element by its number. In an array that's easy: `arr[n]` is a direct reference. But in the list we need to start from the first item and go `next` `N` times to get the Nth element.

...But we don't always need such operations. For instance, when we need a queue or even a [deque](https://en.wikipedia.org/wiki/Double-ended_queue) -- the ordered structure that must allow very fast adding/removing elements from both ends.

Sometimes it's worth to add another variable named `tail` to track the last element of the list (and update it when adding/removing elements from the end). For large sets of elements the speed difference versus arrays is huge.

## Summary

Terms:
- *Recursion*  is a programming term that means a "self-calling" function. Such functions can be used to solve certain tasks in elegant ways.

    When a function calls itself, that's called a *recursion step*. The *basis* of recursion is function arguments that make the task so simple that the function does not make further calls.

- A [recursively-defined](https://en.wikipedia.org/wiki/Recursive_data_type) data structure is a data structure that can be defined using itself.

    For instance, the linked list can be defined as a data structure consisting of an object referencing a list (or null).

    ```js
    list = { value, next -> list }
    ```

    Trees like HTML elements tree or the department tree from this chapter are also naturally recursive: they branch and every branch can have other branches.

    Recursive functions can be used to walk them as we've seen in the `sumSalary` example.

Any recursive function can be rewritten into an iterative one. And that's sometimes required to optimize stuff. But for many tasks a recursive solution is fast enough and easier to write and support.
