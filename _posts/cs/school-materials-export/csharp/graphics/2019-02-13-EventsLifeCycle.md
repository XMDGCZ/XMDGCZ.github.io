---
layout: post
title: "EventsLifeCycle.php "
categories:
    -"csharp"
    -"csharp"
author: "David Malý"
--- 


## Životní cyklus


Podobně jako má každá věc svůj životní cyklus, od jejího vzniku až po její zánik, mají své životní cykly i veškeré objekty.



Pod pojmem životní cyklus si lze představit sled událostí, které mění stav jakéhokoliv objektu počínajících jeho vznikem a končící jeho zánikem. Do životního cyklu patří i všechny stavy, kterých daný objekt nabyl.



Příkladem může být životní cyklus osobního vozu.
 Vůz je vyroben a tím nabude svého prvního skutečného stavu, vznikne. Poté je vůz prodán, používán, po prvních poruchách opraven, časem prodán a na samém konci je rozebrán a zaniká.



V programování můžeme některé výrazné změny stavů zachytávat a adekvátně na ně reagovat, takovou změnou může být vznik a zánik objektu zachycen pomocí konstruktoru a destruktoru.


### Životní cyklus WinForms formuláře


Jakýkoliv formulář ve WinForms je obyčejná třída (dědící různé vlastnosti dle potřeby z jiných tříd), jako taková může nabývat různých stavů a ty lze odchytávat pomocí metod.


#### Vytváření formuláře

- Control.HandleCreated - kdy je ovládací prvek zobrazen
- Control.BindingContextChanged - kdy je změna informací o ovládacím prvku
- Form.Load - kdy je celý formulář plně inicializován
- Control.VisibleChanged - kdy je změna visibility formuláře
- **Form.Activated** - kdy je formulář aktivovaný (má focus)
- **Form.Shown** - kdy je formulář poprvé zobrazen


#### Zánik formuláře

- **Form.Closing** - kdy je formulář právě zavírán
- Form.FormClosing - jiný zápis pro Form.Closing
- **Form.Closed** - formulář je zavřen a všechny jeho zdroje jsou uvolněny
- Form.FormClosed - jiný zápis pro Form.Closed
- **Form.Deactivate** - kdy formulář ztratí focus

[Další události a jejich životní cykly](https://msdn.microsoft.com/en-us/library/86faxx0d%28v=vs.110%29.aspx)