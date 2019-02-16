---
layout: post
title: "Multiplatformní vývoj "
categories:
    - "xamarin"
    - "intro"
sort_index: 0
author: "David Malý"
--- 


## Multiplatformní vývoj


Mnoho aplikací v dnešní době je výsledkem jen vhodného spojení základních operací s daty (přidávání, editace a mazání), to vše v originálním grafickém designu. Z časového i finančního hlediska není efektiní tyto základní operace psát opětovně pro každou platformu. Psání jednoho kódu víckrát také zvyšuje jeho náročnost na údržbu.



Možností, jak napsat kód pro tu samou funkčnost pouze jednou, je hned několik.



Je možné je rozdělit do dvou základních kategorií, ty které:
1. využívají webové technologie
2. pracují na dané platformě nativně


### Webové technologie


Multiplatformní technologie kombinující HTML, CSS a Javascript tvořící vlastní framework. Jsou rychle rozvíjeny a staví na široké základně programátorů, kteří mají zkušenost s vývojem čistě webových aplikací. Výsledné aplikace jsou spouštěny ve WebView a neběží nativně.



Zástupcem takových technologií jsou:


- [Apache Cordova](https://cordova.apache.org/)
- [PhoneGap](http://www.phonegap.com)


### Multiplatformní - nativní technologie


Technologie, které jsou kompilovány do nativních komponentů daných platforem a poskytují tak plnou integraci. Většinou se jedná o technologie, které jsou těžší na naučení, ale poskytují mnohem lepší nástroje pro čistě nativní vývoj. Výsledné aplikace běží naprosto nativně.



Zástupci takových technologií jsou:


- [React Native](https://facebook.github.io/react-native/)
- [Xamarin](https://www.xamarin.com/)


### Přístup k nativnímu API
[Phonegap API](http://www.tricedesigns.com/2012/03/01/phonegap-native-plugins/)
[React native bridge](https://tadeuzagallo.com/blog/react-native-bridge/)
[Xamarin Library Binding](https://developer.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/part_1_-_understanding_the_xamarin_mobile_platform/)