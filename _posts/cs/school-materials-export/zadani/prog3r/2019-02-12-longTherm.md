## Dlouhodobé projekty

### Lodě

1.  Vytvořte v konzoli hru lodě. Hráč určí pozice lodí a poté se do nich pokusí trefovat (naslepo). Pozice pro výstřel a lodě je určena pomocí souřadnic x a y.
2.  Hint: Použitje dvourozměrné pole
	Hráč umístí, před zahájením hry, své lodě na herní plochu (lodě lze otáčet)
3.  Vytvořte hru lodě pro dva hráče (na jednom pc)
4.  Typy lodí:
			![ship types](http://lada.chytrackova.sweb.cz/hry/pics/lode.gif)

5.  Za jedničku: zpracovat hru tak, aby ji bylo možné hrát na dvou počítačích (dva hráči proti sobě). Termín do konce 1. čtrvtletí.

### Šibenice

Naprogramovat hru Hangman (oběšenec)   
 umožnit levelování -> čím vyšší level, tím těžší slovo   

### 21 oko bere

 Program, který reprezentuje hru: 21 oko bere. Používá balíček karet. Program hru implementuje tak, že počítač je bankéř a uživatel je hráč. Bankéř také hraje. 

### Dračí doupě - koncept

    Vytvořte Design dokument (Game Design) ke hře Dračí doupě, který obsahuje následující:

1.  Příběh - větvení, různé postavy

2.  Popis herních mechanik a RPG prvků vč. soubojového systému

3.  Grafický návrh

 Celkový rozsah dokumentu je min. 7 stran A4. 

### Dračí doupě

 Naprogramovat hru na principu Dračího doupěte - zpracování je libovolné, musí však splňovat minimální požadavky: 

*   kód musí splňovat zásady pro tvorbu zdrojového kódu - [SOLID](http://www.codeproject.com/Articles/703634/SOLID-architecture-principles-using-simple-Csharp)
*   kód musí být zdokumentovaný 
*   kód pro UI je oddělený od kódu pro programovou logiku [MVVM ve WPF](https://ucitel.sps-prosek.cz/~maly/PRG/materials/csharp/#wpf) 
*   vlastní třídy pro: hráče, nepřátele, atd. (alespoň 10), využití interface a abstraktní třídy
*   hráčův charakter se skládá ze základních dovedností (síla, zdraví, ...), které se po každém vyhraném souboji, nebo po dosažení vyššího levelu zvýší 
*   Program je uložen na Gitovém serveru (Github, Bitbucket, atd.), kde jsou prokazatelně alespoň 4 commity (nepočítaje inicializační)

 Zapracování: 

*   Příběh viz. VAH - teorie
*   Grafická část práce

#### Nejčastější chyby

*   Vše v jedné metodě - Main, nebo jediná ve Worker -> nepochopení k čemu jsou metody -> nepochopení OOP
*   MVC neznamená, že musíte jasně napsat, zda se jedná o View, Model, Controller, jde o princip komunikace mezi třídami. Např. pokud jde veškerý výstup přes Console, tak to je v pořádku. Je-li, ale nutné výstup pokaždé formátovat, vytvoří se pro něj metoda/funkce a ta se poté vloží do třídy, která shlukuje podobné metody. Častou chybou je nevhodné míchání Console a Vaší View třídy.
*   Tvorba metod/funkcí ještě jednou. Název metody se odvozuje od toho co dělá, jedná se o sled několika málo kroků např. checkUserInputDecission - kontroluje vstup uživatele a zda je v souladu s tím, co program požaduje jako rozhodnutí (číslo volby, slovní zápis atd.). Funkce/Metoda Game, která obsahuje celou hru bez dalších funkcí je špatně. Taková metoda by měla obsahovat pouze další metody např. Inicialization, StartGame atd.
*   V některých IDE je zakázané mít jiný název třídy a souboru, jako Class Osoba v souboru Class1.cs. Porušením nepsaných pravidel je umístění více tříd do jednoho souboru.
*   Programování procedurální i objektové je většinou hierarchické, kód je členěn do úrovní. Chybou je podúrovně zpracovávat v hlavní úrovni.
*   Deklarace proměnných  

    <code class="language-C# ">// Špatně
    String y;
    if(true)
    {
        String x = "pravda";
        y = x;
    }
    else
    {
        String x = "nepravda";
        y = x;
    }
    // Správně
    String y;
    if(true)
    {
        y = "pravda";
    }
    else
    {
        y = "nepravda";
    }// Ideálně StringBuilder
    </code>

*   Kombinace File.Write/Read a StreamWriter/StringBuilder
*     

### Idle hra

Naprogramovat hru na principu [idle](https://en.wikipedia.org/wiki/Incremental_game), s tím rozdílem, že se bude jednat o její strategické rozšíření o více proměnných.  
 V klasické idle hře jsou vydělávány peníze a za ně se kupují vylepšení, v této rozšířené verzi jsou navíc i suroviny, které jsou potřebné pro chod zařízení, a kvalifikovaní zaměstnanci.  
 Např. pro chod pily potřebujete dřevo, za peníze si kupujete její vylepšení, ale tím stoupá i její spotřeba dřeva.  
 Ve hře ANNO 1404 jsou obyvatelé rozděleny na 3 kategorie, 1. skupina nemá žádné požadavky a může vykonávat lehkou práci, 2. skupina již potřebuje větší péči a 3. je nejnáročnější. Každá ze skupin obyvatelstva se hodí na jinou práci.  
 Jedná se o lehkou kombinaci idle hry a rozsáhlých budovatelských strategií ve stylu ANNO 1404, nebo Stronghold  
 Účelem hry je postavit ze všech druhů surovin div světa do jeho maximální úrovně např. 10.  
 Základní požadavky:

*   Grafická hra využívající WinForms nebo WPF
*   Těžba surovin a jejich spotřeba v separátním vláknu, které bude ve vlastní třídě
*   V jednom odvětví aspoň 3 úrovně např. Dřevařství - les, pila, papírna z nichž každá bude mít mnoho stupňů vylepšení
*   Několik kategorií obyvatelstva, každá potřebná pro určité budovy a jejich chod
*   Při nižší dodávce suroviny do budovy se její produkce sníží
*   MVC či jiný model
*   Kód musí být zdokumentovaný
*   Vlastní třídy pro budovy, kategorie obyvatelstva
*   Budova, jejím postavením a maximálním levelem končí hra
	Je možná variace dle hry Banished  