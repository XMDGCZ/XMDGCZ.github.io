<h2>.NET</h2>

Platfroma .NET je soubor technologií umožňující, co největší přenositelnost aplikací na různé operační systémy a typy HW.  
 Tyto nástroje vytváří jednotné prostředí se zárukou sdílených knihoven.  
 Nabízí řešení problému, kdy mnoho aplikací využívalo stejné knihovny, které musely být pokaždé zakompilované do aplikace. .NET tyto knihovny poskytuje všem programům, které je potřebují.  

Výhody:

*   **Přenositelnost a multiplatformost** 
*   Aktualizace externích nástrojů je na správci OS
*   Vždy obsahuje **CLR** (VirtualMachine) 
*   Stará se o přidělování zdrojů zařízení - paměť, čas procesoru, přístup k I/O zařízení
*   Definuje a sjednocuje chápání základních datových typů
*   Aplikace může být napsána ve více programovacích jazycích 

.NET lze dělit na:

*   Microsoft .NET Framework - pro osobní počítače.
*   Microsoft .NET Compact Framework - pro kapesní počítače a mobilní zařízení s Windows Mobile
*   Microsoft .NET Micro Framework - pro embedded zařízení, s minimálním HW výbavou
*   Projekt Mono - je produktem nezávislé OpenSource komunity implementující .NET pro operační systému Linuxového typy (CentOS, Debian, Mac OS X)

Technologie obsažené v .NET:

*   ASP.NET - technologie pro webové aplikace
*   WCF - Windows Communication Foundation - webové služby a komunikační infrastruktura
*   **WPF** - technologie pro tvorbu grafických aplikací
*   **LINQ** - objektový přístup k datům

Podporované programovací jazyky: VisualBasic.NET, Delphy, F#, J#, C#, je podporována většina rozšířených jazyků.

<h4>Kompilace na .NET</h4>

Jakýkoliv zdrojový kód podporovaného programovacího jazyka je zkompilován do CIL, a poté interpretován na cílové platformě pomocí CLR (VM).

<h3>ECMA 335</h3>

Standard ECMA popisuje CIL a rozděluje ho na:

1.  Část 1: Concept a architektura - určuje základní architekturu aplikace a poskytuje normalizované informace o   

		<dd>○ CTS - **popisuje, jak jsou reprezentovány definice a specifické hodnoty v paměti** -> udává jednotné prostředí pro všechny prog. Jazyky, které jsou kompilovány do CIL</dd>  

		<td>○ VES - informace pro virtuální stroj, který zpracovává aplikaci v CIL</td>  

		<dd>○ CLS - specifikace pro unifikované nástroje -> **rozhraní pro práci s již vytvořenými knihovnami, bez nutnosti znát v jakém jazyku byly napsány**</dd>
2.  Část 2: Metadata a sémantika -> poskytuje normalizovaný pohled na popis metadat -> jak pracovat s určitými typy souborů, logických pohledů -> jak pohlížet např. Na tabulky a jejich vazby
3.  Část 3: Popisuje Instrukční set -> **základní sada instrukcí**, které jsou používané pro zpracování aplikace procesorem
4.  Část 4: profily a knihovny -> poskytuje přehled o knihovnách CIL
5.  Část 5: Popisuje standardní cestu pro debugging
6.  Část 6: obsahuje několik ukázkových programů napsaných v CIL -> podobné assembleru , pro ukázku základních postupů a použití instrukčních setů atd.
[Celé znění normy](http://www.ecma-international.org/publications/standards/Ecma-335.htm)