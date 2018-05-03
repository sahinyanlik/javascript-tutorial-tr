

```js run
let kullanicilar = [
  { ad: "Ahmet", yas: 20, soyad: "Zurnacı" },
  { ad: "Hideo", yas: 18, surname: "Konami" },
  { ad: "Jane", yas: 19, surname: "Hathaway" }
];

*!*
function alanIle(alan) {
  return (a, b) => a[alan] > b[alan] ? 1 : -1;
}
*/!*

users.sort(alanIle('ad'));
users.forEach(user => alert(user.name)); // Ann, John, Pete

users.sort(alanIle('yas'));
users.forEach(user => alert(user.name)); // Pete, Ann, John
```

