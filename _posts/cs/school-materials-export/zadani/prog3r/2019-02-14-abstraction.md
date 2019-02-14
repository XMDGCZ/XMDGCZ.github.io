---
layout: post
title: "Nástroje abstrakce "
categories:
    -"zadani"
    -"prog3r"
author: "David Malý"
--- 


## Nástroje abstrakce

### Dědičnost

1. Vytvořte program pro evidenci uživatelů.		Uživatel se může přihlásit.

    Administrátor může mazat a vytvářet uživatele.		- administrátor se ověřuje proti master heslu -> zadané natvrdo v kódu		- pouze administrátor může přistupovat k poli uživatelů
2. Realizujte následující diagram jako evidenci knih.		![](images/inheritance.png)


### Interface


Realizujte následující diagram a vytvořte program, který umí uložit a načíst data jedné třídy např. Osoba v různých formátech (dle diagramu).	![](images/diagram-CSVJsonXML.png)


### Abstraktní třída


Realizujte následující diagram jako program, kde je možné se přihlásit jako Student, nebo jako zaměstnanec.	![](images/diagram-AbstractClass.png)


## Implementace abstrakčních nástrojů

### Lidé ve škole


Realizujte následující diagram a vytvořte program, který umožňuje spravovat zaměstance školy a studenty. Všechny metody v diagramu budou realizovány a jejich použítí je možné skrze konzolovou aplikaci.	![](images/diagram-AbstractClassImplementation.png)


### Inventář


Vytvořte program, který bude fungovat jako inventář pro herní postavu. Program vytvořte tak, aby byla využita co nejvyšší míra abstrakce.<br>



Typy předmětů:<br>


1. Brnění: boty, kalhoty, hrudní zbroj, nárameníky, helmy
2. Zbraně: na blízko, na dálku, speciální
3. Spotřební: jídlo + doplňkové efekty, náboje
4. Možnost vylepšení předmětů a přidávání různých efektů



**Vypracujte Class diagram Vašeho programu.**![](https://i.stack.imgur.com/2ajCN.gif)


### Strategy pattern


Realizujte následující diagram jako souboj hráče s příšerou. Příšera na základně určité podmínky (např. počet životů) mění svou soubojovou strategii během boje.



Diagram dle potřeby rozšiřte.<br>

![](images/MonstersClassDiagram.png)