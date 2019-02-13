---
layout: post
title: "taskExtended.php "
categories:
    -"csharp"
    -"csharp"
author: "David Malý"
--- 


## Rozšířená práce s vlákny


S instancemi vláken je možné pracovat jako s běžnými objekty, a jako takové mají i své atributy a metody



Vlákna lze pro přehlednost pojmenovat a popř. i jejich jméno získávat.



Dalšími možnostmi jsou priorita, nebo jestli běží na pozadí. Vlákna běžící na pozadí typicky běží dlouho dobu (long-therm).


```

using System.Threading.Tasks;
Console.WriteLine(Thread.CurrentThread.Name);

Thread workerThread = new Thread(NeverEndingProc);
workerThread.Name = "workerThread";
workerThread.Priority = ThreadPriority.Normal;
workerThread.IsBackground = true;

workerThread.Start();       

```

### Předávání dat do vláken


Při předávání dat do vláken se využívají stejné způsoby jako při práci s metodami.



Nejpoužívanějším postupem je DataStore, nějaký objekt, který je předán jako parametr funkce a poté s ním ostatní části aplikace pracují.



Kombinuje výhody jedné proměnné, ve které jsou potřebná data, udržuje návrh programovacího modelu a konkretně v případě metody NeverEndingProc() je možné skrz tento objekt vracet data z nekonečného cyklu bez použití return.


```

DataStore data = new DataStore();
Debug.WriteLine("before task " + data.Cislo);
// Instance Task
instance.StartNeverEndingProc(data);
Thread.Sleep(1000);

Debug.WriteLine("after task" + data.Cislo);

class DataStore
{private int cislo;public int Cislo{	get{return cislo;}	set{cislo = value;}}
}

```

### Zastavování vláken


V příkladech výše je použita metoda NeverEndingProc();, která vypadá následovně:


```

private void NeverEndingProc()
{Console.WriteLine(Thread.CurrentThread.Name);int i = 0;while (true){	i++;}
}    

```


Jedná se o nekonečný while, pro jeho zastavení je dobré použít instanci třídy CancellationToken a tu předat jako parametr funkce NeverEndingProc(CancellationToken token)


```

CancellationTokenSource cts = new CancellationTokenSource();
Thread myNewThread = new Thread(() => NeverEndingProc(token) );
Task.Factory.StartNew(() => NeverEndingProc(token));

private void NeverEndingProc(CancellationToken token)
{int i = 0;while (true){	//token.ThrowIfCancellationRequested();	if (token.IsCancellationRequested) return;	i++;}
}

```

