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

##Pojmenování proměnných
V JS musí být jména proměnných (a funkcí) validními identifikátory. Při zahrnutí
netradičních znaků z Unicode sady jsou pravidla komplexní, pokud uvažujeme pouze
typické ASCII alfanumerické znaky, musí identifikátor začínat `a-z`, `A-Z`, `$`,
nebo `_` a obsahovat může kterýkoliv z těchto znaků včetně číslic `0-9`.

Stejná pravidla platí i pro názvy vlastností s tou výjimkou, že zatímco název
vlastnosti může být i klíčové slovo (např. `for`, `in`, `delete`...), stejně
jako `null`, `true`, `false`, název proměnné nikoliv.
##Rozsahy funkce
Klíčovým slovem `var` se deklaruje proměnná, která patří do aktuálního oboru
funkce, nebo do globálního, je-li vně jakékoliv funkce.

###Vyzvedávání deklarace
Kdekoliv se objeví deklarace `var`, je vztažena k aktuálnímu rozsahu a je v něm
odkudkoliv dostupná. Tomuto chování, kdy je deklarace `var` "přesunuta" na
vrchol obalujícího rozsahu, se říká vyzvedávání (_hoisting_).
```JavaScript
var r = 2;

circumference();

function circumference() {
    r = 0;
    console.log(2 * 3.14159 * r);       // 0

    var r;
}

console.log(r);                         // 2
```
Je zvykem při psaní kódu, že by deklarace měla být uvedena před prvním použitím.
V předchozím případě byly v globálním rozsahu definovány proměnná `r = 2` a
funkce `circumference()`. Ta byla zavolána před vlastní deklarací a stejně tak i
v ní byla použita proměnná `r = 1` deklarovaná na konci funkce.
##Vnořené rozsahy
S rozsahy jsme se setkali již v
[první kapitole](../01%20Vzh%C5%AFru%20do%20programov%C3%A1n%C3%AD/Vzh%C5%AFru%20do%20programov%C3%A1n%C3%AD.md#rozsah).
Proměnná je dostupná kdekoliv uvnitř rozsahu, v němž je deklarována, a ve všech
jeho vnitřních rozsazích.
```JavaScript
function a() {
	var x = 1;
	
	function b() {
		var y = 2;
		
		function c () {
			var z = 3;
			
			console.log(x, y, z);       // 1 2 3
		}

		c();
		console.log(x, y);              // 1 2
	}
	
	b();
	console.log(x);                     // 1
}

a();
```
Při použití hodnoty proměnné v rozsahu, v němž není tato proměnná dostupná, je
vyvolána výjimka `ReferenceError`. Není-li proměnná formálně deklarována, liší se
chování v závislosti na použití
[`"strict mode"`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode).
V případě `"strict mode"` nastane chyba, jinak se proměnná stává globální
proměnnou (chyba!), proto je nutné ji vždy formálně deklarovat.
##Podmínky
Stručný úvod do podmínek přinesla
[první kapitola](../01%20Vzh%C5%AFru%20do%20programov%C3%A1n%C3%AD/Vzh%C5%AFru%20do%20programov%C3%A1n%C3%AD.md#podmínky),
v níž byly zmíněny konstrukce `if` - `else`, `switch` a [podmínkový (_ternární_)
operátor](../01%20Vzh%C5%AFru%20do%20programov%C3%A1n%C3%AD/Vzh%C5%AFru%20do%20programov%C3%A1n%C3%AD.md#operátory)
`? :`.

V případech, kdy vzniká podobná série `if` - `else` - `if` příkazů:
```JavaScript
if (a === 0) {
    zero();
} else if (a === 1) {
    one();
} else if (a === 2) {
    two();
} else {
    any();
}
```
je možné provést zápis pomocí `switch`
```JavaScript
switch (a) {
case 0:
    zero();
    break;
case 1:
    one();
    break;
case 2:
    two();
    break;
default:
    any();
    break;
}
```
Pozor, při vynecházní `break` se provedou všechny následující `case` za tím,
který byl vyhodnocen jako pravdivý bez ohledu na to, zda se přiřadí, či nikoliv.
Toho ovšem lze i cíleně využít při ověřování sérií očekávaných hodnot:
```JavaScript
switch (a) {
case 0:
case 1:
case 2:
    knownValue();
    break;
default:
    reportUnknown();
    break;
}
```
Podmínkový (_ternární_) operátor `? :` je forma podmínky podobné konstrukci
`if` - `else`. Pro svůj úsporný zápis se často používá k přiřazení hodnot.
```JavaScript
var r = 1;

var o = r > 0 ? circumference(r) : 0;

// nahrazuje:

// if (r > 0) {
//     o = circumference(r);
// } else {
//    o = 0;
// }
```
##Strict mode
Od verze jazyka ES5 je v JS dostupný ["strict mode"](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode).
Jedná se o režim, kdy jsou aktivní některé restrikce, jejichž úkolem je pomáhat
držet kód bezpečnější a snáze optimalizovatelný pro běhové prostředí.

Volba režimu se děje pomocí vložení pragma `"use strict"` do kódu. Režim lze
zapnout jak pro jednu funkci, tak pro celý soubor v závislosti na tom, kam byla
vložena.

Porovnej:
```JavaScript
function a() {
    "use strict";
    
    // this code is in strict mode
    
    function b() {
        // this code is in strict mode
    }
}

// this code is not in strict mode
```
versus
```JavaScript
"use strict";

function a() {

    // this code is in strict mode
    
    function b() {
        // this code is in strict mode
    }
}

// this code is in strict mode
```
Mezi omezení, jež "strict mode" přínáší patří kontrola zabraňující implicitní
deklaraci globální proměnné, která nastává při neuvedení formální deklarace.
```JavaScript
function circumference() {
    "use strict";
    r = 2;      // chybí var - vyvolá výjimku ReferenceError      
}

circumference();
```
Používejte "strict mode". Není to jen další prostředek k lepšímu a rychlejšímu
kódu, je také předzvěstí budoucího směru jazyka, kam se bude rozvíjet.
##Funkce jako hodnota
Zatím bylo o funkci v JS pojednáno jako o pojmenovaném opakovatelně volaném
bloku kódu, který vytváří vlastní rozsah platnosti. Doposud jsme také
deklarovali funkci následujícím způsobem:
```JavaScript
function circumference(r) {
    return 2 * 3.14159 * r;
}
```
Ačkoliv to není ze zápisu patrné, `circumference` je proměnná deklarovaná v
globálním rozsahu, jíž je přiřazena reference na funkci. Funkce sama je tedy
hodnotou, stejně jako třeba číslo `2`, nebo `[1, 2, 3]`.

Co z toho plyne? Nemusíte předávat pouze hodnoty (_argumenty_) do funkce, ale
funkce je hodnota, jež může být přiřazena do proměnné, předána jako argument do
funkce nebo vrácena funkcí.
```JavaScript
var x = function () {
    // ...
}
var y = function z () {
    // ...
}
```
První výraz (_anonymous function expression_) přiřazuje do proměnné `x`
nepojmenovanou (_anonymní_) funkci. V druhém (_named function expression_) se do
proměnné `y` přiřazuje pojmenovaná funkce `z`.

Ačkoliv je _anonymous function expression_ běžný a používá se velmi často,
doporučuje se používat _named function expression_, a to například z důvodu
lepší čitelnosti výpisu volání (_callstack_) při ladění programu. Pojmenování
funkce také usnadňuje některé pokročilé programovací techniky.
###Immediately Invoked Function Expression (IIFE)
Pod pojmem IIFE se rozumí výraz, v němž je funkce provedena ihned poté, co byla
definována. V předchozích případech, pokud jsme chtěli vykonat funkci, bylo
nutné vložit například `circumference()`.

Existuje však další způsob, jak to provést.
```JavaScript
(function hello() {
    console.log("Ahoj sluníčko");
})();
```
Obalující závorky `(...)` zabraňují, aby byl výraz zpracován jako běžná
deklarace funkce. Závorky `()` na konci `})();` zajistí okamžité spuštění.

Ačkoliv to vypadá podivně, jediným rozdílem jsou pouze obalující závorky:
```JavaScript
function x() {
    // ...
}

x();
```
versus
```JavaScript
(function y() {
    // ...
})();
```
Protože je IIFE funkce (a ta vytváří vlastní rozsah), používá se k _izolaci_
proměnných, které nechceme propagovat do zbytku kódu. V případě potřeby nám
takto izolovaný kód například v budoucnu usnadní změnu implementace. IIFE také
umí vracet hodnotu. 
```JavaScript
function circumference(r) {
    var PI = (function () {
        //return 355 / 113;
        return 48 * Math.atan(1/18) + 32 * Math.atan(1/57) - 20 * Math.atan(1/239);
    })();

    return 2 * PI * r;
}
```
##Uzávěra
Uzávěra je důležitý koncept v JS přirovnatelný k časové schránce. Je to způsob,
jak si _"zapamatovat"_ a umožnit přístup k proměnným z rozsahu funkce, a to
dokonce i poté, kdy tato funkce ukončila svůj běh.
```JavaScript
function counter(value) {
    function next() {
        return value++;
	}

	return next;
}
```
Funkce `counter` dostane hodnotu a vrátí referenci na funkci `next`. Ta vytvoří
uzávěru a po každém zavolání uchovanou hodnotu navýší o jedna.
```JavaScript
var a = counter(0);

console.log(a);         // 1
console.log(a);         // 2

var b = counter(6);

console.log(b);         // 7
console.log(a);         // 3
```
Jakkoliv může vypadat předcházející příklad složitě, koncept uzávěry je
jednoduchý a souvisí s rozsahem (_scope_), respektive jejich řetězem
(_scope chain_). Ten je dán při definici a vytváří se tak ještě před zavoláním
funkce.
##`this`
Dalším důležitým a stejně tak i málo pochopeným konceptem v JS je `this`.

Klíčové slovo `this` představuje referenci na objekt, v jehož kontextu funkce
operuje. Jeho hodnotu určuje, jak byla funkce zavolána.

Mějme funkci `circumference` (tohle matematikům závidím), v níž z _tohoto_
(`this`) kontextu použij proměnnou `r`. 
```JavaScript
var r = 1;

function circumference() {
    return 2 * 3.14159 * this.r;
}

circumference();     // 6.28318
```
V příkladu výše, protože není použit "strict mode", je hodnota `this` reference
na globální objekt. V opačném případě by `this` mělo hodnotu `undefined`.

```JavaScript
var sphere = {
    r: 2,
    circumference: circumference
};

sphere.circumference();     // 12.56636
```
Nyní jsme vytvořili objekt `sphere` s dvěma vlasnosti - hodnotou `r` a metodou
`circumference` (pokud je _funkce_ vlastností objektu, nazývá se _metodou_).
V tomto případě je hodnota `this` reference na objekt `sphere`.

Kontext lze i nastavit, a to pomocí například pomocí funkce `apply`, která je
jednou z metod objektu
[`Function`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function).
Metoda [`apply`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
přebírá jako první parametr objekt kontextu, další parametry odpovídají
parametrům volané funkce. Hodnota `this` je reference na objekt `circle`.
```JavaScript
var circle = {
    r: 0.5
};

circumference.apply(circle);    // 3.14159
```
Čtvrtým způsobem je operátor `new` vytvářející novou instanci objektu. To se
používá ve spojení s _konstruční_ funkcí. Hodnota `this` je potom reference na
nově vznikající objekt.  
```JavaScript
function Sphere(r) {
    this.r = r;
    this.circumference = circumference;
}

var mySphere = new Sphere(1);

mySphere.r;                     // 1
mySphere.circumference();       // 6.28318
```
Název konstrukční funkce by měl začínat velkým písmenem, aby bylo zřejmé, že má
být volána s operátorem `new`. Neuvedete-li jej, místo vytvoření nové instance
objektu se zavolá funkce, v níž je `this` reference na globální objekt.
```JavaScript
function Sphere(r) {
	this.r = r;
}

var mySphere = Sphere(1);

console.log(mySphere);          // undefined
console.log(r);                 // 1
```
##Prototypy
Mechanismus prototypu slouží v JS k sdílení vlastností mezi objekty, proto se v
této souvislosti hovoří také o _prototypové dědičnosti_.

Pokud není na objektu nalezena vlastnost, JS automaticky použije vnitřní
referenci na _prototyp_ k nalezení jiného objektu, na němž je požadovaná
vlastnost hledána (_řetěz prototypů_).
```JavaScript
var a = {
    x: 1
};

var b = Object.create(a);       // vytvoří objekt sváže jej s 'a'

console.log(b.x);               // 1 - vlastnost nalezena na 'a'
```
K svázání objektu s prototypem jsme použili vestavěnou metodu `Object.create()`.

Přibude-li do prototypu nová vlasnost, je dostupná všem instancím. 
```JavaScript
a.y = 3;

console.log(b.y);               // 3
```
Řetěz prototypů vrací první nalezenou hodnotu.
```JavaScript
a.z = 4;
b.z = 6;

console.log(b.z);               // 6
```
##Vývoj jazyka
JavaScript je jednou z implementací skriptovacího jazyka ECMAScript, který je
průběžně vyvíjen a následně standardizován v jednotlivých edicích. V současnosti
je plně podporována verze ES5.1 a existuje specifikace ES6, jejíž podpora se
implementuje. Již nyní však lze používat některé nové rysy jazyka, a to pomocí
dvou technik - polyfily (_polyfilling_) a transpilace (_transpilling_).

###Polyfy
Technika polyfilů spočívá v převzetí definice nové funkcionality a její
přenesení do staršího běhového prostředí JS. Příkladem budiž metoda
`Number.isNaN()` z ES6, která nahrazuje původní `isNaN()`.
```JavaScript
if (!Number.isNaN) {
    Number.isNaN = function isNaN(x) {
        return x !== x;
    };
}
```
Podmínka hlídá vytvoření polyfilu v prostředí s podporou ES6. Ne každá nová
vlastnost je tímto způsobem plně zastupitelná, mohou se vyskytovat rozdíly.
Pro jednoduchost lze využít například existující
[ES5-Shim](https://github.com/es-shims/es5-shim) a
[ES6-Shim](https://github.com/es-shims/es6-shim).
###Transpilace
Možnosti polyfilů jsou limitovány novou syntaxí. Řešením jak ji umožnit v kódu
používat _transpilace_ (transformace + kompilace), kdy nástroj (_transpiler)
převede kód napsaný novou syntaxí do staré, aby byl srozumitelný pro běhové
prostředí. Takový krok se pak typicky stává součástí sestavovacího procesu
programu.

Důvody proč používat novou syntaxi jsou:
 * Nová syntaxe umožňuje čitelnější a čistší kód.
 * Transpilovaný kód může být distribuován pouze pro starší prostředí, novější
 může využívat optimalizací spojených s novou syntaxí.

Populárními transpilery jsou dnes například [Babel](https://babeljs.io/) nebo
[Traceur](https://github.com/google/traceur-compiler).
##Globální objekt a hostující prostředí
JS kód musí být obsažen uvnitř objektu, proto běhové prostředí na pozadí vytváří
globální objekt. Jeho součástí je i tzv. _hostující_ objekt pro interakci mezi
vlastním JS strojem a zbytkem světa.

Globálním objektem v prohlížeči je 
[`window`](https://developer.mozilla.org/en-US/docs/Web/API/Window),
hostujícím například
[`document`](https://developer.mozilla.org/en-US/docs/Web/API/document)
pro práci s DOM (_Document Object Model_). Technicky vzato je to
`window.document`, globální objekt se ovšem obvykle neuvádí. Mezi další metody
hostujícího prostředí patří také `console.log()`. Tyto metody nejsou součástí JS
stroje ani specifikace jazyka. Vyskytují se však v každém kódu, který budete
psát.
