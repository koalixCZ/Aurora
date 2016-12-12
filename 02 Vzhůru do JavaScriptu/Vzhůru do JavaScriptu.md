#Vzhůru do JavaScriptu
##Hodnoty a typy
V [úvodní kapitole](../01%20Vzh%C5%AFru%20do%20programov%C3%A1n%C3%AD/Vzh%C5%AFru%20do%20programov%C3%A1n%C3%AD.md#hodnoty-a-typy)
bylo zmíněno, že v JS mají hodnoty typ. Základními (vestavěnými) typy jsou:
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
console.log(array.length);  // 3
```
Při vymazání prvku z pole se zbývající prvky za vymazaným nepřesouvají,
zůstávají na svých pozicích a nemění se ani délka pole. V poli vznikne _"díra"_,
při dotazu na hodnotu se vrátí `undefined`. U objektu se s tím nesetkáme,
protože `object` je v tomto pojetí mapa klíč - hodnota. Pokud bychom chtěli
odstranit prvek z pole a zajistit, že se změní délka pole, museli bychom
vytvořit nové a překopírovat do něj zbývající hodnoty.

Vzhledem tomu, že se `array` v JS chová spíše jako _asociativní pole_, je možné
do něj přidávat prvky libovolně bez nutnosti stanovit jeho délku. Pole se
automaticky zvětší.
```JavaScript
var array = [];

console.log(array.length);      // 0
array[9] = "j";
console.log(array.length);      // 10
```
###Funkce
Dalším podtypem typu `object` je funkce (`function`). Operátor `typeof` vrací v
jejím případě správně `"function"`.
```JavaScript
function circumference(r) {
    return 2 * 3.14159 * r;
}

console.log(typeof circumference);      // "function"
```
Stejně jako v případě objektu a pole lze i funkci přiřazovat vlastnosti, ovšem
podobně jako u pole je to záležitost úzkého okruhu použití.
```JavaScript
circumference.a = 6;
console.log(circumference.a);       // 6
```
Objekt typu `object` je vhodný k ukládání struktur nebo jako mapa
(klíč-hodnota). Pole se hodí v případech, kdy je vyžadován přístup podle indexu
(prvky pole je také možné řadit). Funkce slouží coby pojmenovaný blok kódu,
práci s jejími vlastnostmi je spíše doménou řízení toku programu než ukládání
uživatelských dat.
##Metody vestavěných typů
Vestavěné typy mají k dispozici metody a vlastnosti umožňující operace nad daty.
```JavaScript
var a = "Ahoj sluníčko :)";
var b = 3.14159;

console.log(a.length);          // 16
console.log(a.toUpperCase());   // "AHOJ SLUNÍČKO :)"
console.log(b.toFixed(4));      // 3
```
Způsob, jakým hodnoty získají tyto schopnosti se nazývá _boxing_. Každý
primitivní typ (`number`, `string`, `boolean`) má odpovídající objektovou obálku
(_wrapper_, `Number`, `String`, `Boolean`), která tyto metody definuje. Když se
s primitvní hodnotou začne pracovat jako s objektem, JS ji automaticky převede
do konkrétního objektového protějšku a poté, co je metoda zavolána, ji vrátí
zpět do jejího literálního typu.
##Porovnávání hodnot
Dva hlavní typy porovnávání jsou ověřovíní na _rovnost_ a _nerovnost_. Výsledkem
provnávání je, bez ohledu na typ porovnávaných hodnot, logická (`boolean`)
hodnota (`true`, nebo `false`).
###Typová konverze (přetypování)
V první kapitole již byla [typová konverze](../01%20Vzh%C5%AFru%20do%20programov%C3%A1n%C3%AD/Vzh%C5%AFru%20do%20programov%C3%A1n%C3%AD.md#konverze-typů)
stručně zmíněna. V JS se vyskytuje dvojí, a to _implicitní_ a _explicitní_.
Explicitní typová konverze znamená, že z kódu je jasně čitelné z jakého typu na
jaký se hodnota převádí.
```JavaScript
var a = "3.14159";
var b = Number(a);

console.log(typeof a);      // "string"
console.log(typeof b);      // "number"
```
Implicitní konverzí se rozumí situace, kdy změna typu nastává jako vedlejší
efekt nějaké operace.
```JavaScript
var a = "3.14159";
var b = a * 2;

console.log(typeof a);      // "string"
console.log(typeof b);      // "number"
```
###Pravdivé & nepravdivé
V [první kapitole](../01%20Vzh%C5%AFru%20do%20programov%C3%A1n%C3%AD/Vzh%C5%AFru%20do%20programov%C3%A1n%C3%AD.md#konverze-typů)
se v tabulce implicitních konverzí objevila pravdivá a nepravdivá (_truthy_ a
_falsy_) povaha hodnot, tedy jakým způsobem jsou v logických operacích chápány
hodnoty jiných typů než `boolean`.

Kráce lze vyjádřit, že v JS jsou hodnoty:
 * `""` (prázdný řetězec)
 * `0`, `-0`, `NaN` (nevalidní číslo)
 * `null`, `undefined`
 * `false`
považovány jako nepravdivé (_falsy_), všechny ostatní jako pravdivé (_truthy_).

##Rovnost
JS poskytuje čtyři operátory rovnosti, a to: `==`, `===`, `!=`,
`!==`, kde znak `!` představuje negaci (_ne-rovnost_, nezaměňovat s nerovností).

Rozdíl mezi rovností `==` a identitou (striktní rovnost) `===` spočívá v tom, že
při použití `==` se pro odlišné typy provádí implicitní konverze, zatímco v
případě striktního porovanání `===` nikoliv.
```JavaScript
var a = 1;
var b = "1";

console.log(a == b);        // true
console.log(a === b);       // false
```
V případě porovnání pomocí `==` JS zjistil nerovnost typů a provedl sérii kroků
ke konverzi jedné, nebo obou hodnot. V našem případě jsou možné dva scénáře, a
to buď `42 == 42`, anebo `"42" == "42"`. Zde došlo k porovnání čísel, tedy
`42 == 42`. Přesná pravidla jakým způsobem se provádí typová konverze jsou k
dispozici ve specifikaci
[ES5, kap. 11.9.3](http://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3).

Výraz `a === b` končí hodnotou `false`, protože typová konverze není povolena.
V současnosti se prosazuje používání striktního porovnání `===`, neboť je pro
programátory predikovatelnější.

Důvod proč v určitých případech naopak preferovat `==` před `===` je, že jeho 
použití může vést k čitelnějšímu kódu. Obecně, názory na programovací techniky a
přístupy jak psát kód se v čase mění, zvláště pak v tak expresivní jazycem,
jakým JS je.

Jednoduché pravidlo pro použití `==` zní:
 * je-li kterákoliv hodnota výrazu `true` nebo `false`, použijte `===`
 * je-li kterákoliv hodnota výrazu `0`, `""`, `{}`, nebo `[]` , použijte `===`
 * ve všech ostatních případech je bezpečné použít `==` a kód bude navíc i čitelnější

Pokud jste si jisti hodnotami, použijte `==`, pokud ne, `===`.

Všechna uvedená pravidla platí symetricky i pro operátory nerovnosti. Operátor
`!=` páruje s `==` a operátor `!==` s `===`.

Při porovnávání dvou ne-primitivních hodnot (`object`, `function`, `array`)
je ověřováno, zda odpovídají reference obou objektů. Není proto žádný rozdíl
mezi použitím `==` a `===`.
```JavaScript
var a = [1,2,3];
var b = [1,2,3];
var c = "1,2,3";

console.log(a == c);        // true
console.log(b == c);        // true
console.log(a == b);        // true
```
Standardně je `array` při porovnávání s primitivní hodnotou převedena na textový
řetězec (`string`), přičemž jednotlivé položky pole jsou odděleny `,`. Mohlo by
se zdát, že dvě identická pole se v tom případě budou navzájem rovnat, ale není
tomu tak. Důvodem je ověřování referencí objektů.

##Nerovnost
Relační operátory `<`, `>`, `<=` a `>=` se typicky používají pro porovnávání
ordinální hodnot - čísel (`3 < 4`).

V JS ovšem mohou být porovnávány i textové řetězce (`"abc" < "def"`).

Platí zde podobná (ne stejná!) pravidla jako při porovnávání pomocí `==`, viz
[ES5, kap. 11.8.5](http://www.ecma-international.org/ecma-262/5.1/#sec-11.8.5).
Neexistuje žádný "striktní" operátor nerovnosti, který by umožnil zákaz typové
konverze podobně jako to dělá `===`.
)
Specifikace stanovuje, jsou-li dva oba operadny řetězce (`string`), probíhá
porovnání lexikograficky. Pokud jeden (nebo oba) nejsou řetězec, obě hodnoty
jsou převedeny na čísla a takto porovnány.

Pokud není možné převést hodnotu na platné číslo, je převedena na hodnotu `NaN`
(nevalidní číslo). Platí, že `NaN` není ani větší ani menší, než jiná hodnota.
```JavaScript
var a = 1;
var b = "ahoj";

console.log(a < b);         // false
console.log(a > b);         // false
console.log(a == b);        // false
```
Porovnáné `==` selže, protože ani `1 == NaN`, ani `"1" == "ahoj"` není pravdivé.