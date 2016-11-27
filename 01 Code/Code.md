#Kód
Zdrojový kód se skládá ze sady instrukcí zpracovávaných počítačem. V případě 
JavaScriptu jsou přímo vykonávány - interpretovány. 

## Příkazy
Příkaz (_statement_) je skupina slov, čísel a operátorů, které vykonávají určitý
úkol.

V následující ukázce se do proměnné `surface` uloží výsledek výpočtu povrchu 
koule o poloměru definovaném v proměnné `r`.

```JavaScript
surface = 4 * 3.14159 * r * r;
```

 * `surface`, `r` - proměnné (_variables_) sloužící k uchování hodnot používaných programem
 * `4` a `3.14159` - hodnoty (_literal value_), existují samostatně bez uložení v proměnných
 * `=` a `*` - operátory (_operators_) - provádějí akce s proměnnými a hodnotami
 * Většina příkazů v JavaScriptu je zakonečna středníkem (`;`).
 
## Výrazy
Příkazy jsou tvořeny jedním nebo více výrazy (_expression_). Výraz je jakýkoliv 
odkaz na proměnnou nebo hodnotu (nebo jejich skupinu) kombinovaný s operátory.

Příkaz z předchozí ukázky obsahuje několik výrazů:
 * `4` a `3.14159` - _literal value expression_
 * `r` - _variable expression_ - vrací aktuální hodnotu proměnné
 * `4 * 3.14159 * r * r` - _arithmetic expression_ - násobení
 * `surface = 4 * 3.14159 * r * r` - _assignment expression_ - přiřazuje hodnotu násobení (výsledek _arithmetic expression_) do proměnné

Obecný výraz použitý samostatně se nazývá _expression statement_,
```JavaScript
2 * r;
```

který ovšem nemá vliv na program, hodnota výrazu není použita. Mnohem běžnější
použití je _call expression statement_ - zavolání funkce.
```JavaScript
circumference(r);
```
##Spuštění programu
JavaScript je interpretovaný jazyk. Pro spuštění tak vyžaduje běhové prostředí.
Tím může být webový prohlížeč (Internet Explorer, Firefox, Chrome...), nebo 
jiné běhové prostředí, např. [Node.js](https://nodejs.org/).

Pro otevření vývojářské konzole v prohlížeči by měla fungovat klávesová zkratka
**F12**, anebo si prostě najděte vývojářskou konzoli v menu svého prohlížeče.
Napište příkaz, potvrďte, a je to, první kód v JS.

![První kód v JS](img1.png)

Vstup uživatelských dat do konzole je možné pomocí zavolání funkce `prompt()`,
která zobrazí dialogové okno pro zápis hodnoty. Výpis aktuálně zadané hodnoty
lze získat funkcí `console.log()`.

![Vložení hodnoty](img2.png)

##Operátory
Operátory představují akce s proměnnými, nebo hodnotami. Operátor rovnítko `=`
se používá pro přiřazení hodnoty na pravé straně do proměnné uvedené na levé
straně.
```JavaScript
r = 2;
surface = 4 * 3.14159 * r * r; 
```

Mezi nejčastěji používané operátory patří:
 * _přiřazení_ - `=`, např. `r = 2`
 * _matematické_ - `+` (sčítání), `-` (odčítání), `*` (násobení), `/` (dělení), např. `r * 2`
 * _porovnání_ - `==` (rovnost), `===` (striktní rovnost), `!=` (nerovnost), `!==` (striktní nerovnost), např. `r === 2`
 * _rovnost_ - `<` (menší než), `<=` (menší nebo rovno), `>` (větší), `>=` (větší nebo rovno), např. `r > 1`
 * _logické_ - `&&` (logický součin - AND), `||` (logický součet - OR), např. `r > 1 && r < 3`
 * _přistup k vlasnosti objektu_ - `.`, např. `console.log()`
 * _unární_ - `delete`, `typeof`, `instanceof`, např. `typeof r` 
 * _podmínkové (ternární)_ - `condition ? val1 : val2`, např. `typeof r === "number" ? r : parseInt(r, 10)`

Priorita operátorů určuje, v jakém pořadí jsou aplikovány, viz
[MDN - Operator precedence](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Operator_precedence).

Pro úplný výčet operátorů s příklady viz 
[MDN - Expressions and operators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators).

##Hodnoty a typy
Každá hodnota, s níž se v kódu pracuje, má typ. Ten pomáhá k upřesnění operací,
jež lze s danou hodnotou provádět. I v reálném životě se sčítají a odčítají
čísla, tvoří slova pomocí předpon a přípon, či odpovídá prostým ano/ne.

JS má typ pro každou takovou tzv. primitivní hodnotu, jimiž jsou:
 * čísla - `number`, např. `1`, `2`, `3.14159`
 * řetězce - `string`, např. `"a"`, `"abc"`, `"Ahoj sluníčko :)"` 
 * logické - `boolean`, hodnoty `true` a `false`

Jsou-li hodnoty použity napřímo, hovoříme o _literálech_. Řetězcové literály
`string` jsou zapisovány s dvojitými (`"a"`), nebo jednoduchými (`'a'`)
uvozovkami. Rozdíl je pouze stylistický, lze používat oboje, z hlediska
čitelnosti se doporučuje používat jednu formu.
```JavaScript
r = 2;
sphere = true;
animal = "wombat";
```
Kromě primitivních hodnot pracuje JS také s komplexními typy (pole, objekty,
funkce...), které budou popsány dále.
##Konverze typů
Konverze typů umoňuje převádět hodnoty do formy, která je nutná pro další
operace. Vrátíme-li se k ukázce získání vstupu  (poloměr koule) od uživatele,
```JavaScript
r = prompt("Zadej poloměr");
```
zjistíme pomocí operátoru `typeof`, že hodnota v proměnné `r` není číslo, ale
řetězec.
```JavaScript
console.log(typeof r);    // 'string'
```
Pro výpočet potřebujeme, aby hodnota byla typu `number`, čehož docílíme
konverzí.  