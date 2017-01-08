#Rozsah a uzávěry
##Co je _rozsah_
Jedním z paradigmat téměř všech programovacích jazyků je schopnost ukládat
hodnoty v proměnných a později je použít. Tato činnost vytváří _stav_ programu.

Zařazení proměnných do jazyka otevírá problém - kde jsou uloženy a jak je
nalézt - což hovoří pro potřebu sady pravidel, kterou nazýváme _rozsah_.
###Teorie kompilátoru
JS je _dynamický_, nebo také _interpretovaný_ jazyk. Znamená to, že při každém
spuštění provede JS stroj několik kroků, aby porozuměl tomu, co má vykonat.

1. **Tokenizace/lexing** - Jednotlivé textové řetězce jsou rozlámány do
smyslupných (z hlediska jazyka) kousků. které jsou nazývány tokeny.

2. **Parsování** - Řetěz tokenů je převeden do stromu vnořených elementů, které
dohromady představují gramatickou strukturu programu - AST strom
(_abstract syntax tree_).

3. **Generování kódu** - AST strom je převeden do spustitelného kódu.

Všechny tyto operace dohromady zaberou JS stroji mikrosekundy.

###Porozumění _rozsahu_
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

Následně _kompilátor_ vyprodukuje kód pro _stroj_ pro pozdější provedení - 
přiřazení `a = 2`. _Stroj_ pak při jeho vykonání nejprve ověří, zda je proměnná
dostupná v kolekci daného rozsahu. Pokud ano, použije ji. Pokud ne, podívá se
jinam (do _vnořeného rozsahu_).

Nalezne-li _stroj_ proměnnou, přiřadí ji hodnotu `2`. Pokud ne, vyhlásí chybu.

Shrnutí: pro přiřazení hpdnoty proměnné se vykonají dvě odlišné akce.
1. _Kompilátor_ deklaruje proměnnou (pokud již nebyla dříve) v aktálním rozsahu.
2. _Stroj_ vyhledá proměnnou v aktuálním rozsahu a je-li nalezena, přiřadí ji
hodnotu.

###V řeči kompilátoru
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
###Vnořený rozsah
Tak jako může být blok nebo funkce vnořena uvnitř jiného bloku nebo funkce,
může být vnořen i rozsah uvnitř jiného rozsahu. Tedy, není-li být proměnná 
nalezena v bezprostředním rozsahu, ověřuje _stroj_ další nadřazený (vnější)
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

###Chyby
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
Když dojde poprvé l RHS vyhledávání proměnné `b`, není nalezena. Je to tedy
"nedeklarovaná" proměnná, protože nebyla nalezena v rozsahu a selže-li RHS
vyhledávání proměnné i v nadřazených rozsazích, je výsledkem `ReferenceError`.
Je důležité poznamenat, že tato chyba je typu `ReferenceError`.

Naproti tomu pokud _stroj_ provádí LHS vyhledávání a dospěje k nejvyššímu
(globálnímu) rozsahu bez toho, aniž by proměnnou nalezl, pak neběží-li ve
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
vyhledávání může být z důvodu přiřazení hodnoty do proměnné (LHS rerence),
nebo z důvodu vrácení její hodnoty (RHS reference).

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

##Lexikální rozsah
V [první kapitole](./Rozsah%20a%20uz%C3%A1v%C4%9Bry.md#co-je-rozsah) jsme
definovali _"rozsah"_ jako sadu pravidel určujících jak _stroj_ vyhledává
proměnnou podle jejího jména buď v aktuálním rozsahu, nebo v některém
z nadřazených, v nichž je umístěn.

Existují dva hlavní modely fungování rozsahu. První z nich, používaný většinou
programovacích jazyků, se nazývá _lexikální rozsah_, druhý se nazývá _dynamický
rozsah_. My se zaměříme na _lexikální rozsah_, který je používán JavaScriptem.

###Čas lexingu
Jak bylo řečeno v [první kapitole](./Rozsah%20a%20uz%C3%A1v%C4%9Bry.md#co-je-rozsah),
první tradiční fáze standardního překladače se nazývá _lexing_ (také
_tokenizace_). Pro zopakování, _lexing_ přezkoumá textové řetězce zdrojového
kódu a přiřadí semantický význam tokenům jako výsledek stavové analýzy.

Definováno kruhem, _lexikální rozsah_ je rozsah definovaný v čase _lexingu_.
Jinými slovy, lexikální rozsah je založen na tom, kde jsou proměnné a bloky
autorsky v době vzniku kódu zapsány, a jako takové jsou (většinou), vytesány do
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

Nezáleží na tom odkud je funkce zavolána, ani jak je zavolána, její lexikální
rozsah je definován pouze tím, kde byla deklarována.

Vyhledávání v lexikálním rozsahu je aplikováno pouze na identifikátory první
třídy (_first class citizens_), jako je `a`, `b` a `c`. Existuje-li v nějaké
části kódu například reference `x.y.z`, lexikální rozsah je aplikován pouze k
nalezení identifikátoru `x`. Jakmile je nalezen, jsou pro `y` a `z` aplikována
pravidla pro pro přístup k vlastnostem.

###Podvádění lexikálního rozsahu
Je-li lexikální rozsah definován pouze tam, kde je deklarována funkce, což je
pouze v době autorova návrhu, jak je možné podvádět klexikální rozsah za běhu?

JS má dva takové mechanismy, oba jsou velkou částí komunitou přijímány s
nelibostí coby špatný zvyk. Typický protiargument však obvykle postrádá pointu -
podvádění lexikálního rozsahu vede k horšímu výkonu kódu.

####eval
Funkce `eval()` dostává jako argument textový řetězec a s jeho obsahem zachází,
jakoby to byl programátorem zapsaný kus kódu. Jinými slovy, je možné generovat
kód uvnitř uvnitř napsaného kódu a spouštět jej, jako by vznikl jž v době psaní.

Je zřejmé jak `eval()` umožňuje měnit prostředí lexikální rozsah předstíráním,
že se jedná o autorský kód, který tam je po celou dobu.

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
deklarací (ať proměnných, či funkcí), tato operace upravuje lexikální stávající
rozsah v němž `eval()` přebývá. Technicky může být `eval()` vykonán různými
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
v kódu vyhýbat.

Případy použití dynamicky generovaného kódu v programu jsou vzácné, jelikož
dopad na výkon za tuto schopnost téměř nikdy nestojí.

####with
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
V uvedeném příkladu byly vytvořeny objekty `o1` a `o2` z nichž jeden měl
vlastnost `a`. Funkce `x` bere jako argument referenci na objekt (`obj`) a
zavolá na ní `with`. Uvnitř bloku `with` provedeme něco, co se zdá být jako
přiřazení hodnoty `2` proměnné `a` (LHS).

Když předáme `o1`, příkaz `a = 2` najde vlastnost `o1.a` a přiřadí jí hodnotu
`2`. Jakmile předáme `o2`, které nemá vlastnost `a`, žádná vlasnost není
vytvořena a `o2.a1 vrátí `undefined`.

Poté můžeme sledovat svérázný vedlejší efekt - vytvoření globální proměnné `a`
příkazem `a = 2`. Jak k tomu došlo?

Příkaz `with` vezme objekt zachází s ním jako když se jedná a samostatný
lexikální rozsah a tak je zacházeno i s jeho vlastnostmi jako s lexikálně
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
vysvětlení toho, co lze sledovat.
