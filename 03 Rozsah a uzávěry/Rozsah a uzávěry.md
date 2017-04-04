# Rozsah a uzávěry
## Co je _rozsah_
Jedním z paradigmat téměř všech programovacích jazyků je schopnost ukládat
hodnoty v proměnných a později je použít. Tato činnost vytváří _stav_ programu.

Zařazení proměnných do jazyka otevírá problém - kde jsou uloženy a jak je
nalézt - což hovoří pro potřebu sady pravidel, kterou nazýváme _rozsah_.
### Teorie kompilátoru
JS je _dynamický_, nebo také _interpretovaný_ jazyk. Znamená to, že při každém
spuštění provede JS stroj několik kroků, aby porozuměl tomu, co má vykonat.

1. **Tokenizace/lexing** - Jednotlivé textové řetězce jsou rozlámány do
(z hlediska jazyka) smysluplných kousků zvaných tokeny.

2. **Parsování** - Řetěz tokenů je převeden do stromu vnořených elementů, které
dohromady představují gramatickou strukturu programu - AST strom
(_abstract syntax tree_).

3. **Generování kódu** - AST strom je převeden do spustitelného kódu.

Všechny tyto operace dohromady zaberou JS stroji mikrosekundy.

### Porozumění _rozsahu_
Vezměme následující příklad:
```JavaScript
var a = 2;
```
Ačkoliv nám se kód jeví jako jeden příkaz, pro JS jsou to dva; jeden, který
zpracuje _kompilátor_ a druhý, který vykoná _stroj_.

Kompilátor udělá  _tokenizaci/lexing_ kódu do _tokenů_, z nichž sestaví AST
strom. Jakmile narazí na `var a`, ověří, zda proměnná `a` existuje pro
konkrétním rozsah. Pokud ano, ignoruje deklaraci a pokračuje dál. V opačném
případě se v kolekci pro daný rozsah deklaruje proměnná `a`.

Následně _kompilátor_ vyprodukuje kód pro _stroj_ pro pozdější přiřazení
`a = 2`. _Stroj_ pak při jeho vykonání nejprve ověří, zda je proměnná dostupná
v kolekci daného rozsahu. Pokud ano, použije ji. Pokud ne, podívá se jinam (do
_vnořeného rozsahu_).

Nalezne-li _stroj_ proměnnou, přiřadí ji hodnotu `2`. Pokud ne, vyhlásí chybu.

Shrnutí: pro přiřazení hpdnoty proměnné se vykonají dvě odlišné akce.
1. _Kompilátor_ deklaruje proměnnou v aktálním rozsahu.
2. _Stroj_ vyhledá proměnnou v aktuálním rozsahu a je-li nalezena, přiřadí ji
hodnotu.

### V řeči kompilátoru
Když _stroj_ provádí kód vyprodukovaný _kompilátorem_, musí vyhledat proměnnou
`a`, aby ověřil, zda byla deklarována. Způsob vyhledávání ovlivňuje výsledek.

V tomto případě _stroj_ provádí LHS (_lefthand side_) vyhledávání a je tím
míněma strana operace přiřazení. Druhý typ se nazývá RHS.

Jinými slovy, LHS vyhledávání je provedeno když se proměnná vyskytuje na levé
straně operace přiřazení, RHS na pravé.

Přesněji řečeno, RHS je pro naše účely těžko odlišitelné od hledání hodnoty
proměnné, zatímco LHS se se pokouší nalézt vlastní kontejner proměnné.

Řekneme-li:
```JavaScript
console.log(a);
```
Reference na `a` je RHS, protože zde není přiřazení, namísto toho se snažíme o
získání hodnot proměnné `a`, která je předána do metody `console.log()`.

Naproti tomu:
```JavaScript
a = 2;
```
Reference na `a` je LHS, protože se nestaráme o hodnotu proměnné, ale chceme
najít proměnnou jako cíl pro přiřazení hodnoty.

Uvažujme následující program:
```JavaScript
function hello(a) {
    console.log(a);     // 3. RHS a, RHS console, RHS log, 4. LHS/RHS a, LHS arg1 = 2
}

hello(2);               // 1. RHS vrať hello, 2. LHS a = 2
```
1. Zavolání funkce `hello()` vyžaduje RHS referenci na `hello` ve smyslu "najdi"
hodnotu `hello` a vrať mi ji. Operátor `()` napovídá, že se jedná o funkci.

2. Je tu také málo patrné (implicitní) přiřazení `a = 2`, které nastane, když je
hodnota `2` předána jako argument funkci `hello`.

3. RHS reference vrací hodnotu parametru `a` do metody `console.log()`. Ta sama
potřebuje RHS referenci pro objekt `console` a další pro jeho metodu `log`.

4. Konečně je zde také LHS/RHS výměna předávané hodnoty `2` do metody `log`,
uvnitř jejíž nativní implementace lze předpokládat přiřazení hodnoty `2` do
nějakého parametru (například `arg1`).
### Vnořený rozsah
Tak jako může být blok nebo funkce vnořena uvnitř jiného bloku nebo funkce,
může být vnořen i rozsah uvnitř jiného rozsahu. Tedy, není-li proměnná nalezena
v bezprostředním rozsahu, ověřuje _stroj_ další nadřazený (vnější)
rozsah a tak pokračuje, dokud nedosáhne nejvzdálenějšího (t.j. globálního).

Uvažujme násleudjící:
```JavaScript
function x(a) {
    console.log(a + b);
}

var b = 1;

x(2);
```
RHS reference pro proměnnou `b` nemůže být vyřešena uvnitř funkce `x`, může však
být vyřešena v obalujícím rozsahu (v tomto případě globálním).

Jednoduché pravidlo pro přecházení vnořených rozsahů zní: _stroj_ začíná v
aktuálně vykonávaném rozsahu, kde hledá proměnnou. Není-li nalezena, pokračuje v
nadřazeném a takto postupuje, dokud není dosažen globální rozsah. Zde končí bez
ohledu na to, zda byla proměnná nalezena, nebo ne.

### Chyby
Proč je důležité, jestli voláme LHS, nebo RHS?

Neboť oba principy se zachovají odlišně v případě, kdy proměnná nebyla
deklarována (tedy nebyla nalezena v aktuálním rozsahu).

Mějme:
```JavaScript
function x(a) {
    console.log(a + b);
}

var b = 1;

x(2);
```
Když dojde poprvé k RHS vyhledávání proměnné `b`, není nalezena. Je to tedy
"nedeklarovaná" proměnná, protože nebyla nalezena v rozsahu a selže-li RHS
vyhledávání proměnné i v nadřazených rozsazích, je výsledkem `ReferenceError`.
Je důležité poznamenat, že tato chyba je typu `ReferenceError`.

Naproti tomu pokud _stroj_ provádí LHS vyhledávání a dospěje k nejvyššímu
(globálnímu) rozsahu bez toho, aniž by proměnnou nalezl, potom, neběží-li v
"strict mode", je v globálním rozsahu vytvořena nová proměnná toho jména a
vrácena _stroji_.

Jednou z odlišností chování programu ve "strict mode" je zákaz automatického/
implicitního vytváření globálních proměnných. Není-li tedy při LHS vyhledávání
nalezena proměnná ani v globálním rozsahu, _stroj_ podobně jako v případě RHS
vrátí `ReferenceError`.

Pokud je proměnná při RHS vyhledávání nalezena, ale s její hodnotou je provedena
nepodporovaná operace (například zavolání funkce na hodnotě, která není funkcí,
získání vlastnosti na `null` nebo `undefined`), _stroj_ vrátí `TypeError`.

`ReferenceError` je chyba vázaná k operacím s rozsahem, zatímco `TypeError`
říká, že _rozsah_ je v pořádku, ale byla provedena zakázaná/neproveditelná
operace nad výsledkem.
### Shrnutí
Rozsah je sada pravidel určujících kde a jak může být proměnná hledána. Toto
vyhledávání může být buď z důvodu přiřazení hodnoty do proměnné (LHS rerence),
nebo vrácení její hodnoty (RHS reference).

LHS referencuje výsledek z operace přiřazení. Přiřazení k rozsahu může nastávat
buď s operátorem `=`, nebo předáním argumentu do funkce (a tím jeho přiřazením
parametru).

JS _stroj_ nejprve zkompiluje kód a přitom rozdělí příkaz `var a = 2;` na dva
oddělené kroky.

1. Deklaruje `a` v rozsahu (toto je provedeno na začátku před vykonáním kódu).
2. Vyhledá proměnnou (LHS reference) a přiadí do ní hodnotu `a = 2`.

Obě hledání - LHS i RHS, začínají v aktuálním rozsahu a pokud je potřeba (a to
je, pokud zde nenaleznou, co hledají), pokračují v nadřazeném rozsahu, dokud
nedosáhnou globálního, kde skončí, ať našly, nebo ne.

Nenaplněná RHS vrací `ReferenceError`. Nenaplněná LHS vrací automatickou, v
globálním rozsahu vytvořenou referenci toho jména (není-li "strict mode"),
nebo `ReferenceError` (v případě "strict mode").

## Lexikální rozsah
V [první kapitole](./Rozsah%20a%20uz%C3%A1v%C4%9Bry.md#co-je-rozsah) jsme
definovali _"rozsah"_ jako sadu pravidel určujících jak _stroj_ vyhledává
proměnnou podle jejího jména buď v aktuálním rozsahu, nebo v některém
z nadřazených, v nichž je umístěn.

Existují dva hlavní modely fungování rozsahu. První z nich, používaný většinou
programovacích jazyků, se nazývá _lexikální rozsah_, druhý se nazývá _dynamický
rozsah_. My se zaměříme na _lexikální rozsah_, který je používán JavaScriptem.

### Čas lexingu
Jak bylo řečeno v [první kapitole](./Rozsah%20a%20uz%C3%A1v%C4%9Bry.md#co-je-rozsah),
první tradiční fáze standardního překladače se nazývá _lexing_ (také
_tokenizace_). Pro zopakování, _lexing_ přezkoumá textové řetězce zdrojového
kódu a přiřadí semantický význam tokenům jakožto výsledek stavové analýzy.

Definováno kruhem, _lexikální rozsah_ je rozsah definovaný v čase _lexingu_.
Jinými slovy, lexikální rozsah je založen na tom, kde jsou proměnné a bloky
v době vzniku kódu zapsány, a jako takové jsou (většinou), vytesány do
kamene v době, kdy _lexer_ zpracovává kód.

Mějme následující příklad:
```JavaScript
function x(a) {
    var b = a * 2;

    function y(c) {
        console.log(a, b, c);
    }

    y(b * 3);
}

x(2);                           // 2, 4, 12
```
Objevují se v něm tři vnořené rozsahy, kde:

1. Zahrnuje globální rozsah, obsahuje identifikátor `x`.
2. Zahrnuje rozsah funkce `x`, identifikátory: `a`, `y` a `b`.
3. Zahrnuje rozsah funkce `y` s identifikátorem `c`.

Rozsahy jsou definovány bloky kódu, jeden je vložen do druhého. Žádný rozsah
nemůže souběžně existovat ve dvou vnějších rozsazích stejně tak, jako funkce
nemůže být uvnitř dvou rodičovských funkcích.

### Vyhledávání
V předcházející ukázce vykonává _stroj_ příkaz `console.log()` a hledá tři
proměnné `a`, `b` a `c`. Začne v nejvnitřnějším rozsahu funkce `y`,
kde proměnnou `a` nenajde, pokračuje proto do nadřazeného rozsahu funkce `x`.
Zde nalezne `a`, a to stejné platí i pro `b`. Proměnnou `c` nalezne přímo uvnitř
`y`.

Pokud by se stalo, že by proměnná `c` byla jak v rozsahu `y`, tak `x`, příkaz
`console.log()` by vždy použil proměnnou z `y`.

Prohledávání se vždy zastaví na prvním nalezeném výsledku. Stejný identifikátor
může být uveden v ruzných vrstvách rozsahů. Bez ohledu na takové _překrývání_
začíná vyhledávání vždy v právě prováděném rozsahu a pokračuje směrem vzhůru,
dokud není nalezen záznam.

Nezáleží na tom odkud ani jak je je funkce zavolána, její lexikální rozsah je
definován pouze tím, kde byla deklarována.

Vyhledávání v lexikálním rozsahu je aplikováno pouze na identifikátory první
třídy (_first class citizens_), jako je `a`, `b` a `c`. Existuje-li v nějaké
části kódu například reference `x.y.z`, lexikální rozsah je aplikován pouze k
nalezení identifikátoru `x`. Jakmile je nalezen, jsou pro `y` a `z` aplikována
pravidla pro pro přístup k vlastnostem.

### Podvádění lexikálního rozsahu
Je-li lexikální rozsah definován pouze tam, kde je deklarována funkce, což je
pouze v době autorova návrhu, jak je možné podvádět lexikální rozsah za běhu?

JS má dva takové mechanismy, oba jsou velkou částí komunity přijímány s
nelibostí coby špatný zvyk. Typický protiargument však obvykle postrádá pointu -
podvádění lexikálního rozsahu vede k horšímu výkonu kódu.

#### eval
Funkce `eval()` dostává jako argument textový řetězec a s jeho obsahem zachází
tak, jako by to byl programátorem zapsaný kus kódu. Jinými slovy, je možné
generovat kód uvnitř napsaného kódu a spouštět jej, jako by vznikl již v době
psaní.

Je zřejmé jak `eval()` umožňuje měnit prostředí lexikální rozsah předstíráním,
že se jedná o kód, který tam je po celou dobu.

Na řádcích po `eval()` JS stroj neví (nestará se), že byl předcházející kód
dynamický a ovlivnil lexikální rozsah. Prohledává jej jako vždy.
```JavaScript
function x(str, a) {
	eval(str);
	console.log(a, b);
}

var b = 2;

x("var b = 3;", 1);     // 1, 3
```
S řetězcem `"var b=3;"` se zachází, z pohledu zavolání `eval()`, jako s kódem,
který zde byl po celou dobu, a protože kód deklaruje novou proměnnou `b`, mění
lexikální rozsah funkce `x`. Vytvořením nové proměnné `b` dojde k překrytí
stejnojmenné proměnné deklarované ve vnějším (globálním) rozsahu.

Když dojde na zavolání `console.log()`, jsou `a` i `b` nalezeny v rozsahu funkce
`x` a nikdy tak nedojde k hledání `b` ve vnějším rozsahu. Proto je na výstupu
hodnota "1, 3" místo "1, 2", jak by tomu bylo v normálním případě.

Standardně, pokud řetezec, který `eval()` provádí, obsahuje jednu nebo více
deklarací (ať proměnných, či funkcí), tato operace upravuje stávající lexikální
rozsah, v němž `eval()` přebývá. Technicky může být `eval()` vykonán různými
způsoby, které místo úpravy aktuálního rozsahu zapříčiní změnu globálního.
V obou případech však `eval()` za běhu mění lexikální rozsah ustanovený v době
psaní kódu.

Je-li `eval()` použit v "strict mode", kód operuje ve vlastním lexikálním
rozsahu. To znamená, že deklarace jsou omezeny uvnitř `eval()` a nedochází tak
k modifikaci obalujícího rozsahu.
```JavaScript
function x(str) {
    "use strict";
    eval(str);
    console.log(a);     // ReferenceError: a is not defined
}

x("var a = 2;");
```
V JS existují i jiná zařízeni umožňující dosáhnout podobnéh účinku, jako
`eval()`. Funkce `setTimeout()` a `setInterval()` mohou vzít textový řetězec
jako první argument, jeho hodnota je pak vyhodnocena jako kód dynamicky
vytvořené funkce. Tento hřích minulosti je již dlouhou dobu zastaralý a neměl by
být využíván.

Podobně konstrukční funkce `new Function()` přebírá textový řetězec jako
poslední argument a převádí jej do dynamicky generované funkce. Ačkoliv je tato
konstrukční funkce o něco bezpečnější než `eval()`, mělo by se jejímu používání
v kódu také vyhýbat.

Případy použití dynamicky generovaného kódu v programu jsou vzácné, jelikož
dopad na výkon za tuto schopnost téměř nikdy nestojí.

#### with
Další dnes již zastaralou konstrukcí v JS švindlující s lexikálním rozsahem je
klíčové slovo `with`.

Typicky je `with` vysvětlován jako zkratka pro vícenásobné referencování
vlastností objektu bez nutnosti pokaždé opakovat jeho referenci.

Například:
```JavaScript
var obj = {
    a: 1,
    b: 2
};

// "zdlouhavější" zápis s opakováním "obj"
obj.a = 3;
obj.b = 4;
obj.c = 5;

// "snadnější" zkratka
with (obj) {
    a = 3;
    b = 4;
    c = 5;
}
```
Nicméně, jde o mnohem více než o praktickou zkratku pro přístup k vlastnostem
objektu:
```JavaScript
function x(obj) {
    with (obj) {
        a = 2;
    }
}

var o1 = {
    a: 3
};

var o2 = {
    b: 3
};

x(o1);
console.log(o1.a);      // 2

x(o2);
console.log(o2.a);      // undefined
console.log(a);         // 2 - změněn globální rozsah!
```
V uvedeném příkladu byly vytvořeny objekty `o1` a `o2` z nichž první měl
vlastnost `a`. Funkce `x` bere jako argument referenci na objekt `obj` a zavolá
na ní `with`. Uvnitř bloku `with` provedeme něco, co se zdá být jako přiřazení
hodnoty `2` proměnné `a` (LHS).

Když předáme `o1`, příkaz `a = 2` najde vlastnost `o1.a` a přiřadí jí hodnotu
`2`. Jakmile předáme `o2`, které nemá vlastnost `a`, žádná není vytvořena a
`o2.a1 vrátí `undefined`.

Poté můžeme sledovat svérázný vedlejší efekt - vytvoření globální proměnné `a`
příkazem `a = 2`. Jak k tomu došlo?

Příkaz `with` vezme objekt a zachází s ním jako když se jedná o samostatný
lexikální rozsah, a tak je zacházeno i s jeho vlastnostmi - jako s lexikálně
definovanými identifikátory v tomto rozsahu.

Zatímco `eval()` může pozměnit existující lexikální rozsah tehdy, dostane-li
textový řetězec s kódem obsahujícím deklaraci, příkaz `with` vytváří zčistajasna
celý nový lexikální rozsah z objektu, který mu byl předán.

Chápáno takto, rozsah deklarovaný příkazem `with` byl po předání `o1` - `o1` a
tento rozsah obsahoval identifikátor korespondující s vlasností `o1.a`. Když
jsme však použili jako rozsah `o2`, který neměl žádný takový identifikátor,
provedení `a = 2` automaticky vyústilo ve vytvoření globální proměnné (pokud
jsme nebyli v "strict mode"). 

Je to poněkud zvláštní vidět `with` měnící za běhu programu objekt a jeho
vlastnosti do rozsahu s identifikátory, ovšem je to nejsrozumitelnější
vysvětlení toho, co lze prakticky sledovat.

### Výkon
Oba - `eval()` i `with` podvádí lexikální rozsah modifikací, nebo rovnou
vytvořením nového lexikálního rozsahu za běhu programu.

Co je ze problém, přináší-li propracovanější funkcionalitu a flexibilitu v kódu?
Nejsou to dobré vlasnosti? Nejsou.

JS stroj totiž provádí řadu optimalizací, které provádí při kompilaci. Některé z
nich dokáží staticky analyzovat kód podle lexů a určit tak, kde jsou všechny
deklarace proměnných a funkcí, takže vyřešení identifikátorů při spuštění potom
zabere mnohem méně času.

Pokud stroj nalezne `eval()`, nebo `with`, musí předpokládat, že všechny
poznatky o umístění identifikátorů mohou být neplatné, protože v době lexingu
neví, jaký kód se může dostat do `eval()` a jak ovlivní lexikální rozsah, nebo
obsah objektu, který lze poslat do `with` k vytvoření nového lexikálního
rozsahu. Mnoho optimalizací, které by mohly být provedeny, se tak stává
bezpředmětnými, je-li použit `eval()` nebo `with`, takže se neprovádí
optimalizace vůbec.

Kód má tendenci běžet pomaleji pouze samotnnou přítomností `eval()` nebo `with` 
a nezáleží na tom jak chytrý může být stroj ve zkoušení omezovat vedlejší efekty
pesimistických předpokladů.

### Shrnutí
Lexikální rozsah znamená rozsah definovaný v době vzniku kódu na základě
rozhodnutí jeho autora, kde jsou funkce deklarovány. Lexikální fáze kompilace
(_lexing_) je schopná poznat kde a jak jsou identifikátory deklarovány, a tak
predikovat, jak budou vyhledávány za běhu.

Dva mechanismy v JS mohou švindlovat s lexikálním rozsahem - `eval()` a `with`.
První dokáže za běhu měnit stávající lexikální rozsah vyhodnocováním řetězce
kódu s jednou nebo více deklaracemi. Druhý v podstatě vytvoří celý nový
lexikální rozsah (opět za běhu) nakládáním s referencí na objekt jako s rozsahem
a s jeho vlastnostmi jako s identifikátory.

Nevýhodou je, že obě konstrukce maří schopnost provádět optimalizace v době
kompilace týkající se rozsahu a vyhledávání, protože stroj musí pesimisticky
předpokládat, že takové optimalizace budou neplatné. V důsledku toho poběží kód
pomaleji. Jednoznačné doporučení je tyto kosntrukce nepoužívat.

## Rozsah funkce versus rozsah bloku
Co vytváří nový rozsah? Je to pouze funkce, nebo mohou i další struktury v JS
vytvářet nový rozsah?

### Rozsah od funkcí
Nejběžnější odpoveď na tuto otázku je, že JavaScript má na funkcích založený
rozsah, tedy každá deklarovaná funkce pro sebe vytváří svůj vlastní rozsah.
Není to však úplně pravda.

Mějme následující příklad:
```JavaScript
function x(a) {
    var b = 2;

    // nějaký kód
	
    function y() {
        // nějaký kód
    }
	// nějaký kód
	
	var c = 3;
}
```
Rozsah funkce `x` obsahuje identifikátory `a`, `b`, `c` a `y`. Nezáleží na tom
kde se v něm nachází deklarace, proměnná, nebo funkce náleží rozsahu bez ohledu
na to.

Funkce `y` má svůj vlastní rozsah, stejně tak globální, který má jeden
identifikátor - `x`.

Vzhledem k tomu, že `a`, `b`, `c` a `y` patří do rozsahu funkce `x`, nejsou
dostupné vně a při pokusu o jejich použití z globálního rozsahu dojde k chybě.

Na funkcích založený rozsah podporuje myšlenku, že všechny proměnné náleží
funkci a mohou být využity v celém jejím rozsahu. Tento přístup na jedné straně
umožňuje využít dynamických vlastností jazyka, na druhou stranu existence
proměnných v celé funkci může vést k nečekaným pastem.

### Skrývání v rozsahu
Tradiční pojetí funkcí spočívá v tom, že ji deklarujete a následně napíšete její
kód. Opačné myšlení je srovnatelně silné a užitečné; vezměte část kódu, který
jste napsali a zabalte jej do funkce. Praktickým dopadem je vytvoření nového
rozsahu a ukrytí deklarací.

Proč může být ukrývání proměnných a funkcí užitečná technika?

Existuje mnoho různých motivací pro skrývání založené na rozsahu, které mají
tendenci vznikat z designového principu nejnižších privilegií (_Principle of
Least Privilege_, též _Least Authority_ či _Least Exposure_). Tento princip je
založený na tom, že v designu software (jako je API pro modul/objekt), by mělo
být vystaveno pouze nutné minimum a vše ostatní by mělo být ukryto.

Tento princip se vztahuje na výběr, který rozsah má obsahovat proměnné a funkce.
Pokud by všechny byly v globálním, byly by samozřejmě dostupné z jakéhokoliv
vnořeného, což by zmiňovaný princip nejnižších privilegií porušovalo vystavením
mnoha proměnných nebo funkcí, jež by měly být udržovány coby privátní.
```JavaScript
function x(a) {
    b = a + y(a * 2);
    console.log(b * 3);
}

function y(a) {
    return a - 1;
}

var b;

x(2);
```
V příkladu výše, proměnná `b` a funkce `y` jsou spíše soukromými detaily funkce
`x`. Poskytnutí přítupu `b` tak není pouze zbytečné, ale v některých případech
může být i nebezpečné, neboť může být, ať záměrně či omylem, použita
neočekávaným způsobem. Přístupnost funkce `y` takové riziko nepředstavuje, ovšem
z hlediska ukrytí implementačních detailů představuje rovněž porušení tohoto
pravidla. Vhodnější návrh by měl skrýt tyto implementační detaily dovnitř
rozsahu `x()`.
```JavaScript
function x(a) {
    function y(a) {
        return a - 1;
    }
    var b;
    
    b = a + y(a * 2);
    console.log(b * 3);
}

x(2);
```
Nyní jsou `b` i `y()` ukryté před vnějšími vlivy a jsou kontrolovány pouze
funkcí `x`. Funkcionalita ani výsledek nejsou dotčeny, ale návrh udržuje
soukromé informace ukryté, což je považováno za lepší řešení.

### Vyhýbání se kolizím
Další výhodou skrývání proměnných a funkcí uvnitř rozsahu je vyhýbání se
bezděčným kolizím mezi dvěma identifikátory se stejným názvem, ale různě
zamýšleným použitím. Kolize ústí v často neočekávané přepisování hodnot.
```JavaScript
function isFibonacciNumber(num) {
    function fibonacci(n) {
        if (n > 1) {
            i = numbers[n - 1] + numbers[n - 2];
            numbers[n] = i;
        } else {
            i = numbers[n];     // mění 'i' v nadřazeném rozsahu
        }
        return i;
    }

    var i = 0,
        fibonacciNumber,
        numbers = [0, 1];

    do {
        fibonacciNumber = fibonacci(i++);   // nekonečná smyčka
    } while (fibonacciNumber < num);

    return fibonacciNumber === num;
}

console.log(isFibonacciNumber(1000));
```
Předcházející příklad představuje funkci ověřující, zda číslo náleží do
[Fibonacciho posloupnosti](https://cs.wikipedia.org/wiki/Fibonacciho_posloupnost),
u níž platí: _F(n) = F(n-1) + F(n-2) s F(0) = 0 a F(1) = 1_.

Nejjednodušší implementace výpočtu čísla této posloupnosti může vypadat takto:
```JavaScript
function fibonacci(n) {
    switch (n) {
    case 0:
    case 1:
    	return n;
	default:
        return fibonacci(n - 1) + fibonacci(n - 2);
    }
}
```
nevýhodou je extrémní neefektivita. Určitým řešením je pamatovat si dvě poslední
čísla. V našem případě se v poli pamatuje celá řada počítaná v cyklu
`do - while` postupným voláním funkce `fibonacci`. Kód však nefunguje - skončí
v nekonečné smyčce. Proč?

Důvodem je, že v funkci `fibonacci` dojde k "neočekávanému" přepsání proměnné
`i` deklarované v obalující funkci `isFibonacciNumber`. Jednoduchým řešením je
buď nová deklarace proměnné `var i;` uvnitř funkce `fibonacci`, čímž dojde k
jejímu překrytí, nebo pojmenování proměnné jiným identifikátorem.

### Globální jmenné prostory
Ukázkovým místem kolize názvů se může stát globální rozsah, kde mohou různé
knihovny použité v programu snadno navzájem kolidovat, pokud vhodně neskryjí
svoje vnitřní funkce a proměnné.

Takové knihovny často vytvoří jedinou deklaraci proměnné (typicky objekt), jež
je dostatečně unikátním jménem v globálním rozsahu. Tento objekt ja pak použit
pro tuto knihovnu coby jmenný prostor (_namespace_). Funkcionalita určená k
zveřejnění je pak realizována jako vlastnosti tohoto objektu spíše než
identifikátory na nejvyšším lexikálním rozsahu.
```JavaScript
var MathLibrary = {
    version: "1.2.3",
    addition: function (a, b) {
        return a + b;
    },
    subtraction: function (a, b) {
        return a - b;
    }
}
```
## Funkce jako rozsahy
Obalením jakékoliv části kódu funkcí ukryje deklarace proměnných nebo funkcí z
vnějšího rozsahu do vnitřního rozsahu funkce.
```JavaScript
var r = 2;

function a() {          // <-- obalující funkce
	var r = 1;
	
	console.log(r);     // 1
}                       // <-- obalující funkce
a();                    // <-- zavolání obalující funkce

console.log(r);         // 2
```
Jakoliv je tato technika funkční, není ideální, neboť se tu vyskytuje několik
problémů. Za prvé, deklarování funkce `a` samo o sobě zanáší uzavírající (v
tomto případě globální) rozsah. Za druhé, je nutné explicitně zavolat funkci
`a();`, aby došlo k provedení zabaleného kódu.

Bylo by ideální, pokud by funkce nepotřebovala jméno (či spíše aby její jméno 
nezanášelo vnější rozsah) a pokud by byla provedena automaticky.

Naštěstí má JS řešení pro oba problémy.
```JavaScript
var r = 2;

(function a() {
    var r = 1;

    console.log(r);     // 1
})();

console.log(r);         // 2
```
Namísto zacházení s funkcí jako se standardní deklarací je s ní naloženo jako s
výrazem funkce - _function expression_, viz
[Immediately Invoked Function Expression (IIFE)](../02%20Vzh%C5%AFru%20do%20JavaScriptu/Vzh%C5%AFru%20do%20JavaScriptu.md#immediately-invoked-function-expression-iife).

Hlavní rozdíl mezi deklarací a výrazem je k čemu směřuje jméno coby
identifikátor. V prvním případě je to k vnějšímu rozsahu, můžeme přímo zavolat
`a()`. Ve druhém pouze k vnitřku vlastní funkce. Ukrytí jména v sobě samém
znamená, že nedochází k zanášení vnějšího rozsahu.

Nejjednodušší cesta jak rozlišit mezi deklarací a výrazem je pozice slova
`function` v příkazu (nikoliv na řádku). Je-li `function` první věc v příkazu,
jde o deklaraci, jinak se jedná o výraz.
### Anonymní versus pojmenované
Pravděpodobně vám není cizí _function expression_ použitý jako parametr pro
zpětné volání:
```JavaScript
setTimeout(function () {
    console.log("Počkali jste jednu minutu a... svět se změnil.");
}, 60000);
```
Říká se tomu anonymní výraz funkce (_anonymous function expression_), protože
`function() {...` nemá jmenný identifikátor. Výraz může být anonymní, deklarace
naopak musí mít jméno. Anonymní výrazy funkce jsou jednoduché a snadné na psaní
a řada knihoven tíhne k tomuto stylu kódu, který má však také několik nevýhod k
uvážení:
 1. Anonymní funkce nemají jméno k zobrazení v zásobníku volání, což činí ladění
 programu složitějším.
 2. Pokud je třeba aby funkce odkazovala sama na sebe (rekurze, odhlášení sebe
 sama jako handleru události po jejím zpracování), je v případě nepojmenované
 funkce nutno použít zastaralou a odmítanou konstrukci `arguments.callee`.
 3. Chybějící jméno snižuje čitelnost a pochopitelnost kódu.

Výrazy funkce jsou užitečným nástrojem a otázka zda anonymní, či pojmenované,
na tom nic nemění. Pojmenování vyřeší všechny uvedené nevýhody a nemá žádné
stinné stránky. Doporučení zní, vždy pojmenovávat výrazy funkce.
```JavaScript
setTimeout(function timeoutHandler() {
    console.log("Počkali jste jednu minutu a... svět se změnil.");
}, 60000);
```
### Bezprostřední vykonání výrazu funkce
Zabalením výrazu funkce do závorek `( )` je možné funkci provést přidáním
dalšího páru `()` na konec.
```JavaScript
(function a() {
    console.log("Ahoj");
})();
```
První pár vytváří funkci jako výraz, druhý ji zavolá. Tomuto vzoru se říká IIFE
([_Immediately Invoked Function Expression_](../02%20Vzh%C5%AFru%20do%20JavaScriptu/Vzh%C5%AFru%20do%20JavaScriptu.md#immediately-invoked-function-expression-iife)).
Jak již bylo uvedeno, IIFE nepotřebuje název. Obvyklejší forma je anonymní,
nicméně její pojmenování přináší výše uvedené výhody a je tedy dobrým postupem
ji osvojit.

Existuje nepatrný rozdíl mezi formou zápisu IIFE:
```JavaScript
(function a () {})();

(function b () {}());
```
Obě formy jsou identické co se funkcionality týče a jejich volba je čistě
záležitostí stylu.

Další vcelku běžnou variací je využití skutečnosti, že se v podstatě jedná o
zavolání funkce a je tedy možné předat argumenty.
```JavaScript
var x = 1;

(function a(global) {
    var x = 2;
    
    console.log(x);             // 2
    console.log(global.x);      // 1
})(window);
```
## Bloky jako rozsahy
Ačkoliv jsou funkce nejrozšířenějšími jednotkami rozsahu, existují i další a
jejich použití může vést dokonce k lepšímu kódu.

Mnoho jazyků (narozdíl od JS) podporuje blokový rozsah, a tak jsou na ně
vývojáři zvyklí, zatímco ti, kdo se setkali pouze s JS mohou tento koncept
považovat za cizí.

Ani kdybyste nenapsali jediný řádek kódu v tomto stylu, jste pravděpodobně
seznámeni s tímto běžným způsobem psaní v JS:
```JavaScript
for (var i = 0; i < 10; i++) {
    console.log(i);
}
```
Deklarovali jsme proměnnou `i` přímo uvnitř `for` cyklu, protože naším záměrem
bylo využít ji pouze ve smyčce, ignorujíce skutečnost, že proměnná náleží
obalujícímu rozsahu (funkce, nebo globální).

O tom je blokový rozsah - deklarovat proměnnou co nejblíže místu, kde je
použita.
```JavaScript
var r = 1;

if (r > 0) {
    var circumference = 2 * 3.14159 * r;
    
    console.log(circumference);
}
```
V předchozím příkladu byla použita proměnná `circumference` pouze v kontextu
`if`, dávalo by tedy smysl deklarovat tuto proměnnou pouze uvnitř `if` bloku.
Kde deklarujeme proměnnou však není při použití `var` relevantní, protože vždy
náleží vnějšímu rozsahu. Ukázka představuje falešný blokový rozsah použitý ze
stylistických důvodů spoléhající na sebekázeň nepoužít náhodně proměnnou v tomto
rozsahu.

Blokový rozsah je nástroj pro rožšíření dříve uvedeného _principu nejnižších
oprávnění_ od skrývání informací ve funkcích k skrývání informací v blocích
kódu.

Mějme opět příklad s `for`:
```JavaScript
for (var i = 0; i < 10; i++) {
    console.log(i);
}
```
Proč zaneřádit celý rozsah funkce proměnnou `i`, která je (nebo správně, měla by
být), použita pouze ve `for` cyklu?

Programátoři mohou upřednostňovat sebekontrolu před náhodným opětovným použitím
proměnné mimo její zamyšlený účel obdržením chyby o použití neznámé proměnné.
Blokový rozsah (pokud by byl k dispozici), by proměnnou `i` zpřístupnil pouze
pro smyčku `for`, způsobujíce chybu, pokud by byla dotazována odjinud. To
zabraňuje používání proměnných neočekávaným způsobem.

Smutnou skutečností je, že JS nemá blokový rozsah.

To je třeba vědět, než se vrhnete dále.
### with
Ačkoliv je `with` dnes již zastaralým konstruktem, představuje ukázku blokového
rozsahu, v němž rozsah vytvořený z objektu existuje pouze po dobu příkazu `with`
a ne ve vnějším rozsahu.

### try/catch
Je málo známým faktem, že ve specifikaci ES3 je proměnná deklarovaná v `catch`
klauzuli viditelná pouze pro `catch` blok.
```JavaScript
try {
    r();
} catch (error) {
    console.log(error);
}

console.log(error);         //  ReferenceError: error is not defined
```

### let
ES6 přináší nové klíčové slovo `let` nacházející se po boku `var` jako jiný
způsob deklarace proměnných.

Klíčové slovo `let` připojuje deklaraci proměnné k rozsahu jakéhokoliv bloku
(obvykle páru `{...}`), v němž je obsaženo. Jinými slovy, `let` se implicitně
zmocní jakéhokoliv blokového rozsahu pro svoji deklaraci proměnné.
```JavaScript
var r = 2;

if (r > 0) {
    let circumference = 2 * 3.14159 * r;
    
    console.log(circumference);
}

console.log(circumference);     ReferenceError
```
Připojení proměnné do existujícího bloku je při použití `let` implicitní. Jak se
mění kód, může snadno dojít k omylu, kterému bloku proměnná náleží. Explicitní
kód je více žádoucí než implicitní, či rafinovaný. Dosáhnout stylu
zvýrazňujícího blokové rozsahy je snadné a přirozeně funguje s tím, jak pracují
blokové rozsahy v jiných jazycích:
```JavaScript
var r = 2;

if (r > 0) {
    {   // <- vytvoří explicitní blok
        let circumference = 2 * 3.14159 * r;

        console.log(circumference);
    }
}

console.log(circumference);     ReferenceError
```
Můžeme vytvořit blok pro zachycení `let` jednoduchým vložením `{..}`. V tomto
případě vytvoříme explicitní blok uvnitř `if`, který může být v budoucnu,
v případě refaktoringu, snadno přesunut bez dopadu na obalující `if`.

Deklarace pomocí `let` nejsou přesouvány na začátek bloku v němž se nachází.
Naopak, nebude v bloku "existovat" až do svého výskytu.
```JavaScript
{
    console.log(r);     // ReferenceError!
    let r = 2;
}
```
#### Úklid paměti
Další důvod užitečnosti blokového rozsahu se váže k uzávěrám a uvolňování
paměti. V stručnosti uvažujme:
```JavaScript
function a (data) {
    // do some stuff
}

var bigData = {};

a(bigData);

var button = document.getElementById("button");

button.addEventListener("click", function click (event) {
	// do some other stuff
}, false);
```
Ačkoliv funkce `click` zpracovávající stejnojmennou událost nepotřebuje
proměnnou `bigData` (a ta by mohla být po zavolání funkce `a` uvolněna z
paměti), přesto bude (v závislosti na implementaci JS stroje) držena v paměti
tak dlouho, dokud bude mít funkce `click` uzávěru přes celý rozsah.

Blokový rozsah může tento problém vyřešit upřesněním, že proměnnou není třeba
dále držet.
```JavaScript
function a (data) {
    // do some stuff
}

{
    let bigData = {};
    
    a(bigData);
}

var button = document.getElementById("button");

button.addEventListener("click", function click (event) {
	// do some other stuff
}, false);
```
#### let - smyčky
Konkrétní ukázkou výhod `let` je použití v `for` cyklu. Nejenom, že proměnná `i`
je deklarováno pouze pro `for` cyklus, ale dokonce je vytvářena opakovaně při
každém průchodu.
```JavaScript
for (let i = 0; i < 10; i++) {
    console.log(i);
}

console.log(i);     // ReferenceError
```
Jelikož je `let` vázán spíše k bloku než k rozsahu funkce, je třeba při
nahrazování deklarací `var` za `let` dávat pozor na situace, kdy se skrytě
spoléhá na viditelnost proměnných deklarovaných pomocí `var`.
```JavaScript
var isTrue = true,
    x = 1;

if (isTrue) {
    var y = 3;
    
    if (x > y) {
        // do some funny stuff
    }
}
```
Může být přepracován takto::
```JavaScript
var isTrue = true,
    x = 1;

if (isTrue) {
    var y = 3;
}

if (x > y) {
    // do some funny stuff
}
```
Při použití blokových proměnných je však třeba dávat pozor:
```JavaScript
var isTrue = true,
    x = 1;

if (isTrue) {
    let y = 3;
    
    if (x > y) {    // Při přesunu nezapomenout na proměnnou y!
        // do some funny stuff
    }
}
```
### const

