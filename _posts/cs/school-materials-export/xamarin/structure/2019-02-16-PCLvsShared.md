---
layout: post
title: "PCL vs Shared "
categories:
    - "xamarin"
    - "structure"
sort_index: 0
author: "David Malý"
--- 


## PCL vs Shared


V Xamarinu má programátor několik možností, jak svůj kód sdílet na více platforem. Nejzásadnějšími jsou Portable Class Library a Shared projekty.


### Shared


Kód je mezi různé platformy rozdělěn pomocí direktiv compileru a vzniká tak, po kompilaci, několik unikátních projektů, každý speciálně pro danou platformu.


```

#if __ANDROID__
#elif ___IOS__

```


Výhodou se může zdát, že je projekt lépe rozdělen do jednotlivých bloků pro každou platformu.



Nevýhodou je, že takové rozdělení nezapadá do koncepu OOP a s narůstající velikostí projektu se stává kód nepřehledným.


### PCL


Multiplatformní kód je napsaný v přenositelné knihovně, která je poté za běhu zpracovávána. Při kompilaci vzniká jedna společná knihovna a několik menších projektů, které ji využívají, také nesou platformě závislý kód.

[Introduction to PCL](https://developer.xamarin.com/guides/cross-platform/application_fundamentals/pcl/introduction_to_portable_class_libraries/)

Výhodou je zapojení muliplatformního i specializovaného kódu do jedné OOP architektury. Portable Class Library je jednoduše linkovatelná všemi částmi projektu a její tvorba je již mezi programátory zažitá.



Nevýhodou je nutnost větší robustnosti projektů i při méně rozsáhlých aplikacích. Také není schopná využívat knihoven, které jsou přítomné ve více knihovnách (System.IO v MonoTouch a Mono for Android), řešením je Dependency injection.


###  V praxi 


V praxi se více využívá PCL oproti Shared. Nicméně Shared má stále velmi mnoho zastánců.

