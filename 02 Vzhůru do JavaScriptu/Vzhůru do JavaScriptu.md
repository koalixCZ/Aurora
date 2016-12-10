#Vzhůru do JavaScriptu
##Hodnoty a typy
V [úvodní kapitole](../01 Vzhůru do programování/Vzhůru do programování.md) bylo
zmíněno, že v JS mají hodnoty typ. Základními (vestavěnými) typy jsou:
 * `string`
 * `number`
 * `boolean`
 * `null` a `undefined`
 * `object`

Typ hodnoty je možné získat pomocí operátoru `typeof`. Hodnota vrácená
operátorem je textový řetězec (`string`).
```JavaScript
var a;

console.log(typeof a);      // "undefined"

a = "Ahoj sluníčko ;)";
console.log(typeof a);      // "string"

a = 6;
console.log(typeof a);      // "number"

a = true;
console.log(typeof a);      // "booelan"

a = null;
console.log(typeof a);      // "object" - známá chyba

a = undefined;
console.log(typeof a);      // "undefined"

a = ["a", "b", "c"];
console.log(typeof a);      // "object"

a = {b: "c"};
console.log(typeof a);      // "object"
```
##Objekty
Typ `object` odkazuje na hodnotu, v níž je možné nastavovat vlastnosti a
každá vlastnost může držet hodnotu libovolného typu.
```JavaScript
var obj = {
    a: "Ahoj sluníčko",
    b: 6,
    c: true
};
```
K vlastnostem objektu lze přistupovat pomocí tečkové (`obj.a`), nebo závorkové
notace (`obj["a"]`). Obě mají svoje výhody; tečková notace je kratší,
čitelnější, a proto obecně i více doporučovaná. Závorkou notaci lze použít v
případě, kdy jméno vlastnosti obsahuje specíální znak, nebo klíčové slovo.
```JavaScript
console.log(obj.a);         // Ahoj sluníčko
console.log(obj["b"]);      // 6
```
Závorková notace také umoňuje přístup k vlasnosti, jejíž jméno je uloženo v
proměnné.

```JavaScript
var obj = {
    a: "Ahoj sluníčko",
    b: 6
};

var b = "a";
console.log(obj[b]);        // Ahoj sluníčko
```