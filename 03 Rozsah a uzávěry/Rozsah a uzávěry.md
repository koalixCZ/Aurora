#Rozsah a uzávěry
##Co je rozsah
Jedním z paradigmat téměř všech programovacích jazyků je schopnost ukládat
hodnoty v proměnných a pozdější je použít. Tato činnost vytváří _stav_ programu.

Zařazení proměnných do jazyka otevírá problém - kde jsou uloženy a jak je
nalézt - což hovoří pro potřebu sady pravidel, kterou nazýváme _rozsah_.
###Teorie kompilátoru
JS je _dynamický_, nebo také _interpretovaný_ jazyk. Znamená to, že při každém
spuštění provede JS stroj několik kroků, aby porozuměl tomu, co má vykonat.

1. Tokenizace/lexing
2. Parsování
2. Generování kódu

####Tokenizace/lexing
Jednotlivé textové řetězce jsou rozlámány do smyslupných (z hlediska jazyka)
kousků. které jsou nazývány tokeny.
####Parsování
Řetěz tokenů je převeden do stromu vnořených elementů, které dohromady
představují gramatickou strukturu programu. Strom je nazýván AST (_abstract
syntax tree_).
####Generování kódu
AST strom je převeden do spustitelného kódu.

Všechny tyto operace dohromady zaberou JS stroji mikrosekundy.

##Porozumění rozsahu
