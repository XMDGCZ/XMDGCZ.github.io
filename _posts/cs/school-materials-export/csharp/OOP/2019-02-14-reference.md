---
layout: post
title: "Reference objektu "
categories:
    -"csharp"
    -"OOP"
author: "David Malý"
--- 


## Reference objektu


Když programátor vytvoří proměnnou, je pro ni vymezena paměť, programátor poté pracuje pouze s ukazatelem na tuto paměť, který se předává napříč programem (odkazuje na paměť, kde jsou data proměnné).



<br>Instance třídy je chápána jako proměnná a pracuje se pouze s ukazatelem na paměť, kde je uložena.


### Předání instance třídy metodě
<br>V objektově orientovaném programovacím jazyku je několik způsobů, jak předat data metodě.

- Jako primitivní datový typ
- Jako primitivní pole
- Jako jednoduchý objekt např. String
- Složitý objekt např. instance třídy Osoba
- Nějaká forma seznamu např. List

<br>U prvních dvou případů platí, že se vytvoří lokální kopie daného parametru a s ní daná metoda pracuje, poté se zahodí. 
<br>Ve zbylých případech se předává pouze odkaz na vytvořenou instanci objektu, se kterou je možné dále pracovat a ovlivnit tak i celý objekt.
<br>Jednoduchý program, který vytvoří pole a vypíše jeho hodnoty<br>
```

 using System.Collections.Generic;

// Vytvoření nové instance třídy List obalující datový typ int
List list = new List();

// Naplnění hodnotami
list.Add(1);
list.Add(2);

// Vypsání seznamu hodnot
foreach (int one in list) Debug.WriteLine(one);

// Záměna 1. a 2. prvku v seznamu 
switchFunction(list);

// Vypsání seznamu hodnot
Debug.WriteLine("");
foreach (int one in list) Debug.WriteLine(one);


public static void switchFunction(List list)
{int t = list[0];list[0] = list[1];list[1] = t;
}

```
<br>Výstup je:<br>
```

1
2

2
1

```
**Jinak řečeno, ovlivníme-li hodnotu u referenčního datového typu, ovlivníme ji pro celý program, ne jen pro konkrétní metodu.**
