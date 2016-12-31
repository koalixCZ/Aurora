#Rozsah a uzávěry
##Co je _rozsah_
Jedním z paradigmat téměř všech programovacích jazyků je schopnost ukládat
hodnoty v proměnných a pozdější je použít. Tato činnost vytváří _stav_ programu.

Zařazení proměnných do jazyka otevírá problém - kde jsou uloženy a jak je
nalézt - což hovoří pro potřebu sady pravidel, kterou nazýváme _rozsah_.
###Teorie kompilátoru
JS je _dynamický_, nebo také _interpretovaný_ jazyk. Znamená to, že při každém
spuštění provede JS stroj několik kroků, aby porozuměl tomu, co má vykonat.

1. **Tokenizace/lexing** - Jednotlivé textové řetězce jsou rozlámány do
smyslupných (z hlediska jazyka) kousků. které jsou nazývány tokeny.

2. **Parsování** - Řetěz tokenů je převeden do stromu vnořených elementů, které
dohromady představují gramatickou strukturu programu. Strom je nazýván AST
(_abstract syntax tree_).

3. **Generování kódu** - AST strom je převeden do spustitelného kódu.

Všechny tyto operace dohromady zaberou JS stroji mikrosekundy.

##Porozumění _rozsahu_
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

##V řeči kompilátoru
Když _stroj_ provádí kód vyprodukovaný _kompilátorem_, musí vyhledat proměnnou
`a`, aby ověřil, zda byla deklarována. Způsob vyhledávání ovlivňuje výsledek.

V tomto případě _stroj_ provádí LHS (_lefthand side_) vyhledávání a je tím
míněma strana operace přiřazení. Druhý typ se nazývá RHS.

Jinými slovy, LHS vyhledávání je provedeno když se proměnná vyskytuje na levé
straně operace přiřazení, RHS na pravé.
