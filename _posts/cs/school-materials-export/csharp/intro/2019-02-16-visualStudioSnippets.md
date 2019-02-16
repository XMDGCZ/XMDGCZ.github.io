---
layout: post
title: "Visual Studio snippety "
categories:
    - "csharp"
    - "intro"
sort_index: 6
author: "David Malý"
--- 


##   Visual Studio - Snippety


Tomáš Smetana



Jednou ze zásadních funkcí Visual Studia, která výrazně zjednodušuje psaní opakujícího se zdrojového kódu, jsou **snippety**.


### Vložení snippetu


Snippet se vloží pomocí kliknutí pravého tlačítka myši přes možnost **Insert Snippet**.

![image](images/image1.png)

Otevře se seznam složek obsahující snippety. Vyberme složku C# a snippet s názvem prop. Dvakrát na něj poklepejme.

![image](images/image2.png)

Do zdrojového kódu se vloží definice vlastnosti v C# s 2 zvýrazněnými částmi (datovým typem a názvem vlastnosti).

![image](images/image3.png)

Obě části lze přepisovat a přesouvat se mezi nimi dá tabulátorem. Jedná se o velmi rychlý způsob, jak napsat vlastnost v C#.



Výhoda snippetů je že nemusíte psát nic navíc, jako je např. slovo public a oblast get a set v závorkách.



Vkládání snippetů z nabídky jde celkem špatně a proto existuje ještě jedna možnost, jak je vložit, a to tak, že se do kódu ručně napíše jejich název a stiskne se tabulátor. Snippet není moc dobře odsazen - například odsazení složených závorek.

![image](images/image4.png)
### Tvorba vlastního snippetu


Velkou výhodou je možnost vytvořit si svoje vlastní snippety. Snippet je reprezentován jako XML soubor, který definuje, jak bude snippet vypadat, kde jsou oblasti k zadávání hodnot, atd…



Nejjednodušší příklad snippetu, je ten, který vloží meta hlavičky s informacemi, aby webové prohlížeče načítanou stránku neukládali do cache. Snippet se tedy bude používat v jazyce HTML. V nabídce file se vybere New > File a vyhledá se XML soubor.

![image](images/image5.png)

Potvrzením Open se vytvoří kořenový element CodeSnippets a jako jmenný prostor se nastaví:

http://schemas.microsoft.com/VisualStudio/2005/CodeSnippet
```

?xml version="1.0" encoding="utf-8"?
CodeSnippets xmlns="http://schemas.microsoft.com/VisualStudio/2005/CodeSnippet"

/CodeSnippets


```


Vložení nového elementu CodeSnippet.

![image](images/image6.png)

Visual Studio označí element CodeSnippet se 2 varováními. Jednak se musí specifikovat parametr Format a za druhé musí mít CodeSnippet hlavičku. Musí se tedy doplnit parametr Format na hodnotu 1.0.0 (aktuální podporované snippety mají právě tuto verzi). Dále snippet musí mít hlavičku, jedná se o element Header. Poslední důležitou věcí je, že ve snippetu chybí element Snippet, který definuje samotný kód snippetu. Pořadí hlavičky a Snippetu se nesmí zaměnit, jinak se nebude jednat o validní Snippet a Visual Studio ho podtrhne s varováním. Zdrojový kód snippetu vypadá následovně:


```

?xml version="1.0" encoding="utf-8"?
CodeSnippets xmlns="http://schemas.microsoft.com/VisualStudio/2005/CodeSnippet"CodeSnippet Format="1.0.0"
    	Header

    	/Header
        Snippet

        /Snippet/CodeSnippet
/CodeSnippets


```

### Hlavička snippetu


**IntelliSense** (našeptávač) napovídá, jaké hodnoty by se měli vyplnit.

![image](images/image7.png)
#### Autor


Element **autor** obsahuje jméno nebo instituci autora.


```

AuthorTomáš Smetana
/Author


```

#### Description


**Popisek**, který výstižně označí, k čemu snippet slouží. V tomto případě se jedná o zabránění cachování.


```

DescriptionDisable cache by meta tags in head element
/Description


```

#### HelpUrl


**HelpUrl** označuje adresu, která se otevře při vyvolání nápovědy (F1). Využití odkazu:

http://stackoverflow.com/…1133/2229538
```

HelpUrlhttp://stackoverflow.com/a/1341133/2229538
/HelpUrl


```

#### KeyWords


**Keywords** označuje klíčová slova. Zadávají se v podelementech KeyWord. Použijeme třeba klíčová slova web browsers, cache, force, disable


```

KeywordsKeyword	web browsers/Keyword
/Keywords


```

#### Shortcut


Označuje, pod jakou zkratkou se bude snippet vyvolávat. Měl by být krátký, protože kdyby byl dlouhý, začínal by postrádat smysl a ideálně by měl být snadno zapamatovatelný (např. prop = Property.).


```

Shortcutdiscach
/Shortcut


```

#### SnippetTypes


Typy snippetů můžeme použít 2. Existuje **SurroundsWith a Expansion**. SurroundWith vkládá snippet okolo vybrané oblasti a Expansion ke kurzoru. Typ se udává v elementu SnippetType:


```

SnippetTypesSnippetType	Expansion/SnippetType
/SnippetTypes


```

#### Title


Je samotný **titulek** snippetu. Jedná se o název v správci snippetů a v nabídce výběru snippetu.


```

TitleDisable browser cache
/Title


```

#### Obsah snippetu


Obsah snippetu se udává v elementu **Code**. Code vyžaduje parametr **Language**, který specifikuje, o jaký programovací jazyk se jedná. Obsah kódu se udává v sekci **CDATA**.


```

Code Language="html"
![CDATA[
        ]]
/Code


```


V sekci CDATA budeme mít naše meta hlavičky, které najdeme v odkazu pro nápovědu.



Celý element Code bude vypadat následovně:


```

Code Language="html"![CDATA[meta http-equiv="cache-control" content="max-age=0" /	meta http-equiv="cache-control" content="no-cache" /	meta http-equiv="expires" content="0" /	meta http-equiv="expires" content="Tue, 01 Jan 1980 1:00:00 GMT" /	meta http-equiv="pragma" content="no-cache" />]]
/Code


```


Nakonec se snippet uloží s příponou \*.snippet (File > Save).

![image](images/image8.png)
### Instalace snippetů


Visual Studio zatím neví, že jej chce uživatel použít. Je potřeba znovu otevřít správce snippetů.


#### Přidání celé složky


Visual studio umí načítat snippety ze složky. Dělá se to tlačítkem Add… a následným vybráním složky.


#### Import snippetu


Snippety lze importovat i jednotlivě pomocí tlačítka **Import…** a následným vybráním snippetu.



Snippety lze načítat i ze sítě. Hodí se to zejména ve firmách, kdy zaměstnanci mezi sebou mohou snippety sdílet.



Snippet si naimportujte nebo nechte načítat složku. Pak vytvořme novou webovou stránku HTML (Fie > New > File > HTML page) a zkusme si vložit snippet. Uvidíte, že zdrojový kód se vloží.

