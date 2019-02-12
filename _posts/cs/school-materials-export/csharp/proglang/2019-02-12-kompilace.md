## Zpracování zdrojového kódu

Před spuštěním zdrojového kódu, který napsal člověk, je nutné ho přeložit do řeči, kterou používá počítač.  
 Ten se řídí příkazy v nejjednodušší podobě - **instrukcemi**.  
 Používají se dva přístupy, jak přeložit kód pro počítač.

*   Celý najednou
*   Tlumočit ho po větách - interpretovat

#### Strojový kód

Strojový kód je posloupnost instrukcí pro procesor a je uložen v hexadecimální soustavě.  
 Před tím, než jsou instrukce provedeny, je převeden do binární soustavy.

<h3> Kompilace</h3>

Kompilace je převod zdrojového kódu na strojový **na straně vývojáře**.  
 Tento postup umožňuje kontrolu chyb, ladění optimalizací a je zde skoro neomezený čas pro přípravu kódu -> zatížení při vývoji je ulehčení při používání.

**Průběh kompilace:**

*   Preprocesor

    *   Odstraní nepotřebné části kódu např. komentáře
    *   Preprocesoru je možné sdělit nastavení -> direktivy preprocesoru

*   Kompilátor

    *   Zpracuje připravený zdrojový kód do hexadecimální podoby
    *   Provede optimalizace
    *   Zkontroluje chyby a případně zastaví kompilaci

*   Deploy

    *   Přenesení výsledného programu na cílové zařízení

*   Spuštění

    *   Převedení hexadecimálního kódu do binární podoby a zpracování procesorem

![](images/hexToBin.png)

 **Kompilace **je převod zdrojového kódu na strojový, v době vytvoření spustitelné aplikace, tedy na počítači programátora   
 **Interpretace** je převod zdrojového kódu na strojový v době, kdy je potřeba ho spustit 

 **Kompilátor** - kompiluje zdrojový kód na strojový  
 **Interpret** - kompiluje kód za běhu 

### Interpretace

Zpracovává zdrojový kód po menších celcích. Pracuje s myšlenkou tlumočníka, kde je vyřčena věta v jednom jazyce a přeložena do druhého. Stejný postup se opakuje pro další věty.

V případě zdrojového kódu je větou myšlen řádek, který je převeden na instrukce v hexadecimální podobě   
 -> **interpret přečte řádek, zpracuje ho a poté čte další. Interpret je na straně cílového zařízení**.

Tento přístup umožňuje ladění chyb přímo za běhu programu, protože cílový stroj má k dispozici i zdrojový kód (myšlenku).

Pozn.* Existují postupy, kde se tohoto přístupu využívá tak, že program přepisuje sám svůj vlastní zdrojový kód.*

![](images/Interpretace.png)

**Výhody kompilace**

*   Kód je připraven ve formě spustitelné aplikace, není potřeba **žádné zdržení v době spuštění aplikace**
*   Mnohem menší riziko krádeže zdrojového kódu. Zdrojový kód je přenášen jako strojový/bytecode.
*   Většinu chyb odhalí kompilátor při kompilaci -> jejich dřívější řešení.

**Výhody interpretace**

*   Zpracovává se pouze kód, který je aktuálně potřeba -> **aplikace může být upravována za běhu**.
*   Přenáší se na cílový stroj celý zdrojový kód, je možné, aby ho kdokoliv mohl upravit.

Kompilované jazyky - **C, C++** - přenáší se již zkompilovaná aplikace  
 Interpretované jazyky - **PHP, Python** - přenáší se pouze zdrojový kód  
 Jazyky běžící pomocí VirtualMachine: **C#, JAVA**  

<h3>VirtualMachine</h3>

Kombinuje oba předešlé přístupy a bere si z každého to nejlepší:

*   Optimalizační a testovací fáze je odvedena na vývojovém zařízení
*   Kód se nepřenáší v pro člověka čitelné podobě
*   Kód je přenositelný napříč platformami, specializaci pro danou platformu (CPU, instrukční sady) zajišťuje VirualMachine (interpret) na každé platformě
*   Přenositelný kód je nezávislý na programovacím jazyce, je možné napsat program v C# s využitím C++ knihoven a spustit ho na jakékoliv platformě, kde je CLR (VirtualMachine pro C#)

Interpretuje kód, když je ho potřeba a **spouští ho ve vlastním prostoru s přidělenými prostředky**

**Výhody VM**

*   Lze naprosto oddělit chod aplikace od operačního systému, či jiných aplikací (tzv. **SandBox**). V minulosti se mohlo stát, že špatně fungující program mohl narušit chod systému.
*   Zpracovává již připravený zdrojový kód (ByteCode, CIL), takže se **na cílové zařízení nepřenáší ve zdrojové podobě**. **Velká část kompilace je zpracována při finalizaci aplikace a zbytek obstará v době spuštění VM**.
*   Zprostředkovává přístup aplikace ke knihovnám, není je tedy nutné zakompilovávat do aplikace.
![](images/virtualMachine.png)

Rozlišuje se několik druhů interpretace a některé z nich jsou:

*   **JIT** - Just In Time   

		<dd>V době spuštění aplikace se připraví její kód ke zpracování. Efektivní, pokud není dostatek paměti a není možné si aplikaci předpřipravit.</dd>  

		<dd> Pozn. * Tento způsob interpretace se používal v mobilních zařízeních s OS Android do verze Lollipop.*</dd>
*   **AOT** - Ahead Of Time   

		<dd> Příprava aplikace pro její spuštění dříve než je potřeba. Aplikace se nahraje do RAM paměti, kde čeká na spuštění. Efektivní zejména pokud má zařízení k dispozici dostatek operační paměti.</dd>  

		<dd> Pozn.* Tento způsob interpretace v současnosti využívají zařízení s OS Android od verze Lollipop výš.*</dd>

Známé VirtualMachines: Dalvik,ART - Android(Java), Java VM (desktopový VM), CLR - C#

<h4>Zpracování kódu pro VirtualMachine</h4>

Kompilátor kompiluje zdrojový kód do **CIL** (Common Intermediate Language), což je univerzální kód, který je poté interpretován na cílovém zařízení.

Interpret konkretizuje univerzální kód na cílové zařízení.

Díky tomu je kód přenositelný a splňuje **"Write once, run everywhere".**

Průběh kompilace:

*   Preprocesor připraví kód
*   Kompilátor vytvoří přenositelný strojový kód
*   Přenesení strojového kódu na dané zařízení
*   Spuštění aplikace, zde nastupuje VirtualMachine
*   VirtualMachine interpretuje potřebné části kódu, když jsou potřeba  

[Článek k rozšíření daného téma](http://www.itnetwork.cz/csharp/zaklady/c-sharp-tutorial-uvod-do-jazyka-a-dot-net-framework/)
[CIL advanced](https://www.codeproject.com/Articles/362076/Understanding-Common-Intermediate-Language-CIL)