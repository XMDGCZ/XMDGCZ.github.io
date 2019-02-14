---
layout: post
title: "Class diagramy "
categories:
    -"zadani"
    -"backsteps"
author: "David Malý"
--- 


## Cvičení na vytváření diagramu tříd

### Dialog postav


<br>    Vytvořte diagram tříd, který bude reprezentovat logiku jedné obrazovky, kde si povídají dvě postavy. V rámci diagramu využijte v největší možné míře princip kompozice a držte se SOLID.<br>



<br>    Do návrhu  zařaďte následující specifikace:<br>


- Každá z postav může mít více textů v rámci dialogu
- Texty postav se střídají
- Dialog je ovládán timerem, který zobrazuje a skrývá jednotlivé texty
- V rámci dialogu se může uživatel rozhodnout a tím ovlivnit další vývoj dialogu
- Scéna navazuje na jednu nebo více dalších scén



<br>    V diagramu vystupují následující třídy, doplňte vlastní:<br>


- Postava - obrázek, jméno
- Scéna (dialog) - obrázek na pozadí, texty, rozhodnutí



<br>    Diagram vytvořte tak, aby bylo možné scénu jednoduše vytvořit, měla by být kompatibilní s Frame, nebo UserControl.<br>

