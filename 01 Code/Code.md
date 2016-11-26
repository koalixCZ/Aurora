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

