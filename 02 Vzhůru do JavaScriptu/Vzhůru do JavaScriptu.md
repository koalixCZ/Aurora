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
###Pole
Pole (_array_) je typ objektu, v němž jsou hodnoty libovolného typu uloženy na
číselně indexovaných pozicích.
```JavaScript
var array = [
    "Ahoj sluníčko",
    6,
    true
];
```
První položka v poli je dostupná na indexu 0.
```JavaScript
console.log(array[0]);      // Ahoj sluníčko
console.log(array[1]);      // 6
```
Mimo ostatních vlastností poskytuje objekt typu _pole_ vlastnost `length`
vracející počet prvků uložených v poli. Nejrychlejší způsob jak procházet 
prvky je potom použití cyklu `for`.
```JavaScript
for (var i = 0; i < array.length; i++) {
    console.log(array[i]);
}
```
Ačkoliv je _pole_ speciální objekt, vrací operátor `typeof` hodnotu `"object"`.
```JavaScript
console.log(typeof array);      // "object"
```
Může se to jevit matoucí, novější implementace JS poskytují metodu 
[Array.isArray()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray)
pro zjištění, zda se jedná o `object` nebo `array`.
```JavaScript
console.log(Array.isArray({}));     // false
console.log(Array.isArray([]));     // true
```
V JS je objekt _asociativní_ pole. Bylo by proto možné použít jména vlastností
numerické hodnoty (`0, 1, 2`), nicméně přirozený způsob používání je takový, že
`object` se používá pro pojmenované vlastnosti a `array` pro číselně pozicované.
###Mazání prvků - operátor `delete`
Odstranit prvek z objektu, nebo pole, je možné pomocí operátoru `delete`.
```JavaScript
var obj = {
    a: true
}

delete obj.a;
console.log(obj.a);         // undefined
```
Při pokusu o získání smazaného prvku je vrácena hodnota `undefined`, stejně jako
při snaze o získání neexistujícího prvku. Teoreticky je možné odstranit prvek
nastavením hodnoty vlastnosti na `undefined`, není to však přesně totéž, protože
`undefined` je ve skutečnosti globální proměnná s hodnotou `undefined`, takže
bychom vlastnost neodstravili, pouze jí nastavili hodnotu, která se tak navenek
jeví.

Velmi zjednodušeně lze koncept `null` a `undefined` popsat:
 * `null` - nevím
 * `undefined` - cože?

Pokud je při dotazu na objekt (nebo jeho vlastnost) vrácena hodnota `null`,
znamená to, ano, takový objekt existuje, ale nemá zatím nastavanou žádnou
hodnotu. Naopak hodnota `undefined` říká - o ničem takovém nevím, že by mělo
existovat. Důvodem odlišování obou stavů je skutečnost, že JS je dynamický
jazyk a vlastnosti objektů mohou vznikat za běhu programu. 

Nedoporučuje se nastavovat vlastnosti na hodnotu `undefined`.

Stejně jako lze odstranit vlastnost objektu, lze odstranit i prvek z pole.
```JavaScript
var array = ["a", "b", "c"];

delete array[1];
console.log(array[1]);      // undefined
```
