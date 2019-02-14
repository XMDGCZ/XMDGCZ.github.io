---
layout: post
title: "Datové typy "
categories:
    -"csharp"
    -"intro"
author: "David Malý"
--- 


## Datové typy


Určují, jakých hodnot smí nabývat proměnná. Dělí se na základní datové typy a na ty, které skládají vícero primitivních a vytváří tak nové.


###  Primitivní datové typy 


Jsou takové datové typy, které **se neskládají z jiných datových typů**.
<br>Primitivní datové typy, které mohou být použity v C#.


- char    - jeden znak
- int     - celá čísla (32bit)
- double  - desetinná čísla s plovoucí desetinnou čárkou (64bit)
- decimal - celá čísla (128bit)
- void    - žádná hodnota
- boolean - true/false, někdy je možné reprezentovat jako bit - 0/1



Rozdíl mezi double/float a decimal


- float/double jsou reprezentovány (uloženy) v binární podobě
- 
- decimal je uložen jako desetinné číslo - nedochází k tak velké odchylce při ukládání
- 
- Decimální datové typy jsou kontrolovány vetšinou IDE, tak aby nedošlo k přetečení jejich minimální nebo maximální hodnoty
- 


### Referenční datové typy 


**Vznikají použitím datové struktury, nebo třídy**.
<br>Referenční -> používají odkaz(referenci) na jiný objekt.





Příklady:
<dd> Třída String: jedná se o statickou třídu, která implementuje pole (array) datového typu char.<br><dd> Oproti struktuře String v C/C++ obohacuje String ještě o metody např. lenght;contains...<br> <td> Třída Auto může obsahovat pocetKol(int), nazevAuta(String) a jiné</td><p>
</p>
<i>Pozn. jakékoliv pole je děděné z třídy Object a tak je referenční datový typ.</i></dd>