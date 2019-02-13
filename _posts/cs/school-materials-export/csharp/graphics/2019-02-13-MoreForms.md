---
layout: post
title: "MoreForms.php "
categories:
    -"csharp"
    -"csharp"
author: "David Malý"
--- 


## Více formulářů v jedné aplikaci


Aplikace často vyžaduje více různých oken. Ten nejhorší způsob, kterým lze řešit více oken, je vytvořit jedno okno, které dynamicky obměňovat prvek po prvku.



Dobrou praxí je na každé okno více formulářů, ty jako celky obměňovat.



Jednoduchým příkladem je z jednoho formuláře spouštět druhý např. po stisknutí tlačítka:


```

 private void button1_Click(object sender, EventArgs e)
{// Vytvoření nové instance formulářeForm2 form2 = new Form2();// Zobrazení formulářeform2.Show();
}
```


Takto, se nepřenášíme do nového formuláře žádná data.




Formulář je objekt, tak se i jako objekt chová. Stejnými způsoby lze do něj přenášet data.



To lze pomocí:


1. konstruktoru
2. metod
3. objektu určeného pro přenos dat (DTO)
4. vyčlenění veřejných atributů pro ovlivnění



#### - Pomocí konstruktoru


```
 private void button1_Click(object sender, EventArgs e)
{// Data pro přenos do formulářeString data = "dataToPass";String dataInt = 2;String dataBool = true;// Vytvoření nové instance formuláře a předání datForm2 form2 = new Form2(data, dataInt,dataBool);// Zobrazení formulářeform2.Show();
}
```
Pozn.*Potencionální nevýhodou je, že lze data přenést pouze při vytváření formuláře*


#### - Pomocí metod


```
 private void button1_Click(object sender, EventArgs e)
{// Vytvoření nové instance formulářeForm2 form2 = new Form2();// Data pro přenos do formulářeString data = "dataToPass";String dataInt = 2;String dataBool = true;form2.update(data, dataInt,dataBool);// Zobrazení formulářeform2.Show();
}
```
Pozn.*Metoda update musí být ve form2 deklarovaná a přístupná*


#### - Data tranfer object (DTO)

DTO je objekt, který slouží pouze pro přenos dat, neměl by mít žádné zatěžující metody[více](http://martinfowler.com/eaaCatalog/dataTransferObject.html).
Pro jednoduché použití (není úplně správné bez nastudování DTO problematiku) lze vytvořit objekt pouze s atributy a přístupovými rozhraními a ten předat 1. nebo 2. způsobem viz výše.
```

Form2 form2 = new Form2(dataObject);
form2.update(dataObject);
```


#### - Vyčlenění veřejného rozhraní existujícího ovládacího prvku

Situace: Uživatel v 1. formuláři vyplní své jméno a ve 2. formuláři ho chce zobrazit.
Nabízí se tedy možnost textový box v 2. formuláři prohlásit za public a z 1. formuláře přímo měnit jeho text.
Tento postup se nedoporučuje a většinou je přímo proti programovacímu modelu, místo něj je lepší využít možnost č.2 viz výše.<br>



[Ukázka jednoduché aplikace s více formuláři](attachment/WinFormsToolsMore.zip)

