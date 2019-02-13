---
layout: post
title: "abstraction_student.php "
categories:
    -"csharp"
    -"csharp"
author: "David Malý"
--- 


## Abstrakce systému


Michal Moudrý a Martin Zbořil





Abstrakce je způsob zjednodušení systému. Princip je snaha o snížení duplikace informací v programu, toto může být zobecněno jako tzv. "don't repeat yourself" princip . Programátor tedy pracuje s rozhraními/abstraktními třídami, které bude využívat pro přidávání další funkcionality do komplexnějšího programu. Výhodou je snažší přidávání funkcionality do programu a snažší práce s věcmi, které by vyžadovali o mnoho více práce. Nevýhodou je nadměrné používání abstrakce v jednodušších programech v případě nezkušenosti programátora (využití tzv. "[KISS](https://en.wikipedia.org/wiki/KISS_principle "KISS princip")" principu).<br>

<br>Příklad 1: Služby v rámci programovacího modelu MVVM. (Abstrakce metod, které využívá ViewModel.)
<br>Příklad 2: [HAL vrstva](https://ucitel.sps-prosek.cz/~prochap/OSY/struktury.pdf "Popis HAL vrstvy") v OS.
<br>Příklad 3: Programátor bude mít v programu abstraktní třídu, která bude sloužit jako základ pro uživatelské nastavení aplikace. Tuto třídu konkretizuje ve třídě pro ukládání nastavení do lokálního uložiště a pak například ve třídě pro ukládání nastavení aplikace na server.


### Nástroje





#### Abstraktní třída
<br>    Abstrakční třída je jedním z nástrojů abstrakce sytému. V praxi slouží abstraktní třída ke sdružování společných atributů a metod. Abstraktní třída nemůže být instancována, musí být konkretizována další třídou. Třída, která konkretizuje abstraktní třídu zdědí všechny její atributy a metody, také může mít svoje vlastní. Metody můžou být společné pro všechny třídy a jsou automaticky implementovány (abstraktní třída definuje hlavičku a tělo metody) nebo jsou jen předepsané a každá podtřída si je musí implementovat (tzv. abstraktní metody, kdy abstraktní třída definuje hlavičku metody stejně jako u realizace rozhraní).




<br>Příklad: Auto má nějaké vlastnosti (atributy) a může se pohybovat (metoda Pohyb()), ale každé auto má jinou spotřebu (abstraktní metoda *SpotřebaPaliva()*). Třída Tesla tedy bude definovat tělo metody jinak než třída Audi, vzhledem k vlastnostem výrobního modelu. Například do vzorce pro výpočet spotřeby automobilu Tesla se musí zadat více faktorů než u aut se spalovacím motorem.



![Ukázka Abstraktní třídy](images/diagram_abst_trida.png "Ukázka Abstraktní třídy")



<br>Praktická ukázka: AppData.Settings helper pro UWP aplikace viz. [AppData.Settings helper - Martin Suchan](https://gist.github.com/martinsuchan/9f31502a03cab5120c10c1b161eef33e "AppData.Settings helper - Martin Suchan").
<br>Poznámka 1: *Konkretizace je v UML stejně značena (prázdný trojúhelník) jako generalizace.*
<br>Poznámka 2: *Abstraktní třídy a metody se v UML značí kurzívou.*








#### Dědičnost
<br>Jedná se o vazbu na úrovni tříd, kdy jedna třída (podtřída nebo také child) využívá pro svou definici, definici druhé třídy (nadtřída nebo také parent). To znamená, že třída získává všechny nebo některé vlastnosti (atributy a metody) jiné třídy.











<br>Příklad: Osoba má nějaké vlastnosti, jako jméno, věk. Student má také tyto vlastnosti a proto třída Student bude dědit ze třídy Osoba viz. Obrázek. 1. Výsledkem je, že třída Student pak bude mít své vlastní vlastnosti a k tomu vlastnosti třídy Osoba.



![Ukázka dědění](images/inheritance.png "Ukázka dědění")

<br>Typy dědění:<br>
- **Jednoduché dědění**
Třída využívá pro svou definici, definici druhé třídy viz. Obrázek 1.
- **Vícenásobné dědění**
Třída využívá pro svou definici, definice více tříd.
- **Víceúrovňové dědění**
Třída využívá pro svou definici, definici druhé třídy, která využívá pro svou definici, definici třetí třídy, ...
<br>        Příklad: syn-otec-děda, kdy třída Syn zdědí vlastnosti třídy Otec, která zdědila vlastnosti třídy Děda viz. Obrázek 2
- **Hierarchické dědění**
Třída slouží jako nadtřída pro více než jednou podtřídu.

![Ukázka víceúrovňového dědění](images/multilevel_inheritance.png "Ukázka víceúrovňového dědění")
<br>Poznámka 1: *Některé třídy mohou být označeny jako neděditelné, tzn. nemůže mít podtřídy. V C# jsou takové třídy označeny klíčovým slovem **sealed** viz. [MSDN](https://msdn.microsoft.com/en-us/library/88c54tsw.aspx "MS Developer Network").*
<br>Poznámka 2: *Dědičnosti se také říká generalizace.*


### Redukce vazeb na třídu




<br>    Redukce vazeb na třídu je způsob zjednodušení práce s mnoha třídami, které stejně či podobně chovají. Principem redukce je využití jedné třídy, která agreguje či kompozicuje své podtřídy (viz. [Rozdíl mezi agregací a kompozicí](https://cs.m.wikipedia.org/wiki/Diagram_tříd#Agregace_.28Aggregation.29 "Rozdíl mezi agregací a kompozicí")) za účelem snažší práce s více třídami.



<br>Příklad 1:  HAL vrstva v OS, kdy tato vrstva poskytuje přístup k různě fungujícímu HW. Na Obrázku 4 je třída Ovladač, která přistupuje na HW přes vrstvu HAL, která je reprezentována třídou HAL. Tato třída agreguje třídu HW, která agreguje a kompozicuje třídy reprezentující HW komponenty.



![Ukázka redukce vazeb č. 1](images/diagram_redukce_vazeb_HAL.png "Ukázka redukce vazeb č. 1")



<br>Příklad 2: Existuje mnoho různě fungujících souborových systémů a OS má ve své struktuře vrstvu jménem Virtuální Souborový Systém, který poskytuje jednotné rozhraní pro přístup k těmto souborovým systémům viz. Obrázek 5. Na tomto obrázku je třída Operační systém, která agreguje třídu VFS, která slouží jako jednotné rozhraní pro třídy znázorňující různé souborové systémy.

![Ukázka redukce vazeb č. 2](images/diagram_redukce_vazeb_VFS.png "Ukázka redukce vazeb č. 2")

