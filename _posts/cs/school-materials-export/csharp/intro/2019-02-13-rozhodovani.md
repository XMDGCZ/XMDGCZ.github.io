---
layout: post
title: "rozhodovani.php "
categories:
    -"csharp"
    -"csharp"
author: "David Malý"
--- 


## Rozhodování


Programy musí umět reagovat na vícero situací a být schopné vždy zvolit správnou variantu řešení - musí se tedy umět rozhodovat.
<br>Každé rozhodnutí musí splňovat jasně dané podmínky.



Jednoduchý zápis pro rozhodování


```

if(podmínka)
{
 //pokud je podmínka splněna
}
else
{
 // pokud je podmínka nesplněna
}

```


Či ve zkráceném zápisu


```

if(podmínka) // splněná
else // nesplněná

```


Podmínky je možné vnořovat do sebe.


```

if(podmínka)
{if(další podmínka){ if...}else{}
}
else
{if(další podmínka)else
}

```


Nesplní-li se podmínka, je možné zapsat další podmínku takto


```

 else if(podmínka)

```


Podmínka je vždy buď pravda nebo nepravda -> **true/false**


```

// Podmínka - je pravda?
if(true) // je splněna vždy

boolean b = true;
if(b) // pokud je b = true podmínka je opět splněna

```


Všechny podmínky se ptají na výslednou hodnotu = true/false a na jejím základě se provede blok pro splněnou podmínku nebo blok pro nesplněnou.


```

if(isset(proměnná)) provedAkci;// Akce je provedena pokud funkce isset vrátí true
else provedAkci2; // Akce je provedena pokud funkce isset vrátí false

```


Myšlenka pro vyhodnocení, zda je podmínka splněná nebo ne, je použitelná ve všech případech, kde je možné vyhodnocovat podmínky např. if, nebo cykly: while, for atd.


```

if(true) podmínka je vždy splněná
while(true) //nekonečný cyklus, podmínka je vždy splněná

```





### Booleova algebra


Dvě hodnoty se v C# porovnávají pomocí logických funkcí - AND, OR, NOT, atd., nebo pomocí porovnávání GE, EQ, LT atd. a porovnávání typů IS, ISNOT, AS... 

<br>V C#
- AND -> &&
- OR -> ||
- EQ -> ==
- NOT -> !=
- GE -> >=
- GT -> >
- LT -> <
- LE -> <=




Při použití **==** se hodnota výrazu převede do **binární** podoby a použije logická funkce **EQ**, která vrací 1/0 -> true/false (ekvivalence)


```

//Je 1 = 1?
if(1 == 1)// splněná podmínka
else //nesplněná podmínka

```

#### Pokud nelze převést do binární podoby

```

//Je ahoj v proměnné ahoj = ahoj v proměnné ahoj2?
string ahoj = "ahoj";
string ahoj2 ="ahoj";
if(ahoj == ahoj2) //Podmínka je splněná

//Je ahoj v proměnné ahoj = ahoj v proměnné ahoj2?
string ahoj = "ahoj";
string ahoj2 ="ahoj2";
if(ahoj == ahoj2)
// Podmínka je splněná
// V tomto případě == znamená "porovnej", jestli je objekt1 a objekt2 ve stejném paměťovém prostoru. 

```


Podmínka je splněná, protože objekt String/string je vytvořen při zavádění programu a poté je pouze rozšiřován/pozměňován, tedy jeho paměťový prostor je stále stejný.


```

string ahoj = "ahoj"; 
string ahoj2 ="ahoj2";
// Proto podmínka v praxi vypadá
if(string == string)// Což je pravda a podmínka je splněná

```


#### Switch


Je-li potřeba zapsat za sebou mnoho podmínek, kdy je hodnota porovnávaná pokaždé s jinou hodnotou, je možné použít switch.
<br>Porovnává zadanou hodnotu s každou hodnotou v case.

```

string pozdrav = "ahoj"; 
switch(pozdrav) // porovnávaná hodnota
{case "ahoj": // porovnání proměnné ahoj s řetězcem znaků "ahoj"	provedAkci;	break; // pokud je podmínka splněná, další porovnávání je dobré přerušit pomocí breakcase "na shledanou":	provedAkci2;	break;case "sbohem":	provedAkci3;	break;default:  // default je použit, pokud žádná hodnota v case není ekvivalentní s hodnotou udanou podmínce switch	provedAkci4;	break;
}
// ekvivalentem pro tento switch je:
if(pozdrav.equals("ahoj"))provedAkci;
else if( pozdrav.equals("na shledanou") )provedAkci2;
else if( pozdrav.equals("sbohem") )provedAkci3;
else provedAkci4;

```

