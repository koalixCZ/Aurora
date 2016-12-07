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

a = "Ahoj sluníčko ;)"
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
