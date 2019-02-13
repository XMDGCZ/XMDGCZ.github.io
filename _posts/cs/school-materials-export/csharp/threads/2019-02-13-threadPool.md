---
layout: post
title: "threadPool.php "
categories:
    -"csharp"
    -"csharp"
author: "David Malý"
--- 


## ThreadPool


Mnoho aplikací vytváří vlákna, která stráví většinu svého času spánkem a čekání na událost. ThreadPool sdružuje všechna vlákna a umožňuje jejich efektivní správu.



ThreadPool spolu s vlákny ukládá i jejich data, která jsou při obnovení činnosti vlákna znovu použita. Takto fungují pouze data označena jako ThreadStaticAttribute.



ThreadPool může být využit i tak, že nějaká metoda bude dynamicky volat vlákna dle potřeby pomocí QueueUserWorkItem.



Vždy je **jeden ThreadPool na jeden proces**.



ThreadPool je primárně **určen pro vlákna běžící na pozadí**.


### Vlákna běžící na pozadí a v popředí


Nejdůležitějším rozdílem je: **vlákna běžící na pozadí neudržují běžící prostředí**, jsou stavěna na čekání, či uspání.



Ve chvíli, kdy jsou všechna vlákna běžící na popředí zastavena, systém ukončí aplikaci společně s vlákny na pozadí.



Všechna vlákna, která jsou vytvořena pomocí **new Thread()** jsou defaultně označena jako vlákna běžící **v popředí**. Všechna vlákna vytvořena v **ThreadPool** jsou označena jako vlákna běžící **na pozadí**.

<br>Každá metoda přidaná do ThreadPool se automaticky zavolá hned, jak to bude možné.<br>
```

ThreadPool.QueueUserWorkItem(new WaitCallback(NeverEndingProc), token);

```
