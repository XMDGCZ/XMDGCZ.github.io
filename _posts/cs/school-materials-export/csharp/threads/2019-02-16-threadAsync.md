---
layout: post
title: "Async "
categories:
    - "csharp"
    - "threads"
sort_index: 5
author: "David Malý"
--- 


##   Vícevláknové a asynchronní programování


Vojtěch Vidra



Operační systém umožňuje mít spuštěno více aplikací najednou, ale počet spuštěných aplikací může být vyšší než počet jader procesoru. Jak je to možné? Operační systém samovolně přepíná mezi aplikacemi, takže uživatel má dojem, že běží všechny aplikace najednou.


### Proces


Aby jakákoliv aplikace mohla běžet musí být spuštěn její proces, neboli instance běžící instance aplikace. Jeden proces většinou reprezentuje jednu běžící instanci aplikace. Ovšem nemusí tomu tak být vždycky, např. Google Chrome využívá jeden proces pro každou otevřenou kartu.


### Vlákno (Thread)


V procesu může běžet několik vláken. Každá aplikace má minimálně jeden proces, ve kterém běží její hlavní vlákno. Hlavní vlákno máme vždy připravené, ale pokud chceme pracovat s více vlákny, musíme si je vytvořit samy. Více vláken aplikace nám umožňuje vykonávat více úkonů najednou např. změna na straně UI a načítání dat z databáze v tu samou chvíli. Kdyby jsme načítali data z databáze synchronně, tak v tu chvíli čtení se aplikace pro uživatele zasekne a může přestat odpovídat.


### Synchronizace


Když v aplikaci vytvoříme více vláken nastane situace, kdy operační systém samovolně přepíná nejen mezi aplikacemi, ale zároveň mezi jednotlivými vlákny jedné aplikace, takže nemůžeme odhadnout, kdy naše další vlákna vykonají práci. Tento problém se dá řešit několika způsoby, ale nejdříve se podíváme na to, jak se taková vícevláknová aplikace vytváří.


### První vícevláknová aplikace


Základ vícevláknové aplikace je práce s vlákny (Thready). Vytvoříme si třídu Prepinac, která bude obsahovat 3 funkce. Vypisuj0 bude vypisovat nuly do nekonečna. Stejně tak Vypisuj1 bude vypisovat jedničky. Ve funkci přepínej si za použití třídy Thread spustíme funkci Vypisuj0, která bude běžet v druhém vlákně, jakoby na pozadí a v hlavním vlákně spustíme funkci Vypisuj1.


```

class Prepinac
{

        public void Vypisuj0()
        {
                while (true)
                {
                        Console.Write("0");
                }
        }

        public void Vypisuj1()
        {
                while (true)
                {
                        Console.Write("1");
                }
        }

        public void Prepinej()
        {
                Thread vlakno = new Thread(Vypisuj0);
                vlakno.Start();
                Vypisuj1();
        }

}

```


Prepinac poté spustíme v Main a uvidíme jaký bude výstup.


```

static void Main(string[] args)
{
        Prepinac prepinac = new Prepinac();
        prepinac.Prepinej();
}

```


Vystup bude vypadat takto:

![Výstup](images/asyncoutput1.jpg)

Na výstupu je vidět, že vlákna neběží vždy stejně dlouho, ale délka běhu se vždy liší.


### Vlastnosti třídy Thread


Třída Thread obsahuje několik vlastností, tyto jsou podstatné:


- IsAlive -  Označuje, zda metoda vlákna běží
- Name - Vlákno si můžeme pojmenovat


### Sleep


Sleep nám umožňuje uspat jádro na požadovaný počet milisekund. Níže můžeme vidět funkci Vypisuj0, která se po každém výpisu vždy pozastaví na 5 milisekund.


```

public void Vypisuj0()
{
        for (int i = 0; i < 100; i++)
        {
                Console.Write("0");
                Thread.Sleep(5);
        }
}

```

### Join


Join nám umožňuje počkat na určitý thread až dokončí svou práci.


```

Vypisovac vypisovac = new Vypisovac();
Thread vlakno1 = new Thread(vypisovac.Vypisuj0);
vlakno1.Start();
vlakno1.Join();
Console.WriteLine("Hotovo");

```


Tento příklad spustí funkci Vypisuj0, ta vypíše 100 nul a poté se připojí hlavní jádro a vypíše “Hotovo”. Pokud by zde nebyl Join, spustila by se funkce Vypisuj0 v druhém vlákně a hned poté se vypsalo Hotovo, takže by bylo hotovo hned na začátku, ale stále by se vypisovaly nuly.


### Lock


Lock umožňuje dokončit kód vlákna dřív, než se přepne na další vlákno. Pokud máme nějaký kritický kód, který kdyby se nedokončil ve správném pořadí, tak by aplikace vyhodila chybu, použijeme Lock a tato chyba zmizí. Ukážeme si příklad na funkci vybírání z bankomatu:


```

class BankomatSafe
{
    private decimal hotovost = 100;
    private object zamek = new object();

    public void VyberVlakny()
    {
        Thread vlakno1 = new Thread(Vyber100);
        vlakno1.Start();
        Vyber100();
        if (hotovost < 0)
            Console.WriteLine("Hotovost je v mínusu, okradli nás.");
    }

    private void Vyber100()
    {
        lock (zamek)
        {
            if (hotovost >= 100)
            {
                Console.WriteLine("Vybírám 100");
                hotovost -= 100;
                Console.WriteLine("na účtu máte ještě {0}.", hotovost);
            }else{
                Console.WriteLine("Nedostatek financí");
            }
        }
    }
}

```


Třída BankomatSafe má dvě funkce, Vyber100 obsahuje zámek, který se stará o to aby se po zkontrolování stavu konta rovnou odečetla hodnota od stavu konta. Funkce VyberVlakny spustí vlákno, které se pokusí vybrat 100 a ještě se sama pokusí vybrat 100. Pokud by zde nebyl zámek, byla by zde nějaká šance, že se ve vlákně zkontroluje stav konta, to se uspí, spoustí se Vyber100, zkontroluje stav konta, to bude stále na 100, odečte 100, poté se probudí uspané vlákno které již zkontrolovalo stav a také odečte 100.

![Výstup](images/asyncoutput2.jpg)

Výstup bude vypadat takto.



Pozn.    *Detailnější popis Sleep, Join, Lock:*https://www.itnetwork.cz/csharp/vlakna/c-sharp-net-tutorial-vlakna-sleep-join-lock


### Shrnutí


Vícevláknové programování využívá pro běh programu více vláken, to znamená že pokud jde o nějaké náročné operace, které vyžadují velký výpočetní výkon použijeme více vláken.


### Async, Await


Druhý způsob jak zpracovávat více věcí najednou je asynchronní programování. Ovšem tyto dva způsoby se nedají zaměňovat, spíše spolu velmi úzce souvisí. Asynchronní běh aplikací může využívat pouze jednoho vlákna, ale toto vlákno pracuje s daty ve chvíli kdy je má k dispozici. Co to znamená? Například když otevřeme aplikaci a tam se mají zobrazit obsahy z 3 tabulek v databázi, tak pomocí synchronních programování bychom se dotázali DB na jednu tabulu, počkali až odpoví, poté ji vypsali, následně se dotázali na druhou atd. Pokud bychom použili asynchronního programování, lze se dotázat na všechny tabulky najednou a vypsat je kdy se nám to hodí. A pokud by databáze byla přetížená, tak mezitím můžeme psát do vyhledávání.



Pozn.    *Velmi kvalitní vysvětlení rozdílu mezi Thread a Async:*https://stackoverflow.com/questions/36417268/async-await-vs-threads-scenario



Async nám umožňujě říct o určité metodě že na ni program nemusí čekat, aby pokračoval v běhu. To se používá již při zmíněném přístupu k databází, nebo Rest API. Příklad asynchronní metody:


```

private async Task DownloadWebsiteAsync()
{
    WebsiteDataModel output = new WebsiteDataModel();
    WebClient client = new WebClient();
    string websiteURL = "https://www.google.com";

    output.WebsiteUrl = websiteURL;
    output.WebsiteData = await client.DownloadStringTaskAsync(websiteURL);

    return output;
}

```




Všimněte si že metoda má před datovým typem parametr async, což říká že je asnychronní. Asnychronní metody mají vždy datový typ Task (pokud nic nevrací), nebo Task