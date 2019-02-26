---
layout: post
title: ".NET Standard"
categories:
            - "xamarin-forms-uvod"
            - ".NET"
post_icon: "fas fa-play-circle"
sort_index: 1
author: "David Malý"
---
Co je .NET Standard, PCL, nebo BCL a FCL? A proč by to .NET vývojáře mělo zajímat.
<!--excerpt-->

<iframe src="https://onedrive.live.com/embed?cid=700E9A68882DA0BB&amp;resid=700E9A68882DA0BB%212884&amp;authkey=AJKwdVggzjmnKl0&amp;em=2&amp;wdAr=1.7777777777777777" frameborder="0">This is an embedded <a target="_blank" href="https://office.com">Microsoft Office</a> presentation, powered by <a target="_blank" href="https://office.com/webapps">Office Online</a>.</iframe>

Obsah prezentačních slides:
- [.NET Frameworky](#NETFrameworky)
- [BCL a FCL](#BCLandPCL)
- [.NET Standard](#NetStandard)
- [Obsah .NET Standard](#NetStandardObsah)
- [Verze .NET Standard](#NetStandardVerze)
 
## .NET Frameworky
{: #NETFrameworky }
.NET framework byl v posledních letech roztříštěn, jako příklad si můžeme vzít Mono - Open source implementaci, která je určená pro multiplatformní vývoj. Forkem Mona vzniklo Unity, které se od původního .NET frameworku ještě vzdálilo. Nejnověším přírůstkem k těmto frameworkům je .NET Core. 

Různé frameworky = různé výhody i nevýhody.

.NET Framework
- zaměřen na vývoj desktopových aplikací
- distribuován jako Windows komponenta
- největší code base mezi frameworky
- primární platforma vývoje v .NET

.NET Core
- multiplatformní serverové aplikace
- open source
- v balíčku aplikace jsou distribovány všechny části, které aplikace potřebuje ke svému běhu
- kompatibilní s ostatními technologiemi
 
Xamarin
- implementace Mona
- multiplatformní mubilní aplikace
- open source
- vlastní runtime

## BCL a FCL
{: #BCLandPCL }
Base class libraries jsou základem pro každý framework, obsahují např. definice pro datové typy a základní knihovny. Jsou popsané normami [ECMA](https://visualstudio.microsoft.com/license-terms/ecma-c-common-language-infrastructure-standards/), které popisují i Common Language Infrastructure (CLI).

Na základě BCL jsou vytvořeny Framework Class libraries (FCL), které jsou specifické pro konkrétní účel (WPF, Xamarin.Android). Mají mnohoem více rozšíření (extensions) a tvoří základní code base, se kterou pracuje programátor. Vytváří framework.

Problémem pro programátora je nutnost znalosti BCL i v případě, kdy pracuje s FCL např. by měl vědět, jak funguje Mono Class Library, pokud chce pracovat s Xamarin.iOS, nebo [CoreFX](https://github.com/dotnet/corefx) pro využití např. kolekcí v .NET Core.

Base Class libraries je název pro základní set knihoven pro .NET Framework, Core library je název pro knihovny pro .NET Core a Mono Class Library pro Mono.

## .Net Standard
{: #NetStandard }
Sjednocuje definice pro BCL, vytváří abstraktní vrstvu, na kterou mohou vývojáři spoléhat, je tedy možné sdílet kód napříč platformami.

Je distribuován jako nuget balíček ve specifické verzi, kterou používají vývojáři aplikací jako referenci (dependency). Existující .NET implementace (např. Xamarin, .Net Core) se zavazují podporovat .NET Standard specifickou verzi, více o verzích v dalším slide.

.NET Standard je možné si představit jako Hardware Abstraction Layer (HAL) část operačního Windows NT. Proti této vrstvě jsou psány SW aplikace, HW počítače kompatibilní s Windows musí zajistit funkčnost na základě popisu HAL. Stejné to je s .NET Standard, vývojáři aplikací píší svůj kód proti specifické verzi a runtime (Xamarin, .NET Core) se zavazuje implementovat specifickou verzi .NET Standard. Výsledkem je, že vývojář píše kód nezávisle na výsledné platformně. 

Ukázka HAL ve Windows NT:

<a title="The original uploader was Grm wnr at English Wikipedia.
Later versions were uploaded by Xyzzy n at en.wikipedia. [GFDL (http://www.gnu.org/copyleft/fdl.html) or CC-BY-SA-3.0 (http://creativecommons.org/licenses/by-sa/3.0/)], via Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File:Windows_2000_architecture.svg"><img width="384" alt="Windows 2000 architecture" src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/5d/Windows_2000_architecture.svg/512px-Windows_2000_architecture.svg.png"></a>

V případě, že vývojář potřebuje něco, co cílová platorma nepodporuje, je možný jeden ze scénářů:
- při volání API ``PlatformNotSupportedException``
- API vůbec není dostupné v .NET Standard a je nutné použít nuget balíček, který toto API zpřítupňuje pro konkrétní platformu
- emulace API - Mono pomocí .ini souborů emuluje Windows registry


## Obsah Net Standard
{: #NetStandardObsah }
.NET Standard základní set API (pro 1. verzi) vznikl analýzou společných API v .NET Framework a Mono. Na základě této analýzy se API rozdělily na 2 kategorie:
1. Potřebné API - API sdílené v obou frameworcích a vhodné pro sdílené v rámci celého .NET ekosystému
1. Volitelné API - platformně závislé API

Volitelné API (platformně závislé) jsou vyčleněny z .NET Standard nuget balíčku a jsou distribuované jako samostatné nuget balíčky. Důsledkem tohoto vyčlenění jsou odstraněny i API, které jsou využité pouze v případech konkrétních platforem a jejich sdílení by nedávalo smysl.

Příkladem vyčlenění je část AppDomain, konkrétně Code Access Security, které je distribuováno jako samostatný balíček, ale AppDomain je součástí .NET Standard.

Prvních několik verzí .NET Standard je základem pro .NET Core, proto byly verzovány společně.

## Verze .NET Standard
{: #NetStandardVerze }

Verzování je aditivní (nová verze obsahuje předešlé verze) a imutabilní (neměnné). 

První verze vtvořily podporu pro všechny PCL profily, proto obsahovaly pouze API, která bylo možné sdílet na všech těchto platformách (např. Windows Phone), celkový set těchto API byl nejmenší ze všech dalších verzí.

Pozdější verze cílily na přidání co největšího množství dalších API. V tomto ohledu se nedalo vyhnout znekompatibilnění některých platforem pro novější verze. Proto některé platformy podporují starší verzi .NET Standard např. Windows Phone podporuje jako poslední verzi 1.2.

.NET Standard se stejně jako samotné platformy neustále vyvíjí, to je dobře vidět na verzování např. .NET Framework, kdy jeho verze 4.5 je podporována verzí 1.0 .NET Standard, a verze 4.6.1 je podporována od verze 1.4.

Výsledkem je:
- čím nižší verze .NET Standard, tím více je možné podporovat platforem
- čím, vyšší verze .NET Standard, tím více APIs je k dispozici za cenu ztráty kompatibility se staršími platformami

Verze 2.0 výrazně snížila množství nuget závislostí (dependencies). Například závislosti pro JSON.NET

```
.NETStandard 1.0
    Microsoft.CSharp (>= 4.3.0)
    NETStandard.Library (>= 1.6.1)
    System.ComponentModel.TypeConverter (>= 4.3.0)
    System.Runtime.Serialization.Primitives (>= 4.3.0)

.NETStandard 1.3
    Microsoft.CSharp (>= 4.3.0)
    NETStandard.Library (>= 1.6.1)
    System.ComponentModel.TypeConverter (>= 4.3.0)
    System.Runtime.Serialization.Formatters (>= 4.3.0)
    System.Runtime.Serialization.Primitives (>= 4.3.0)
    System.Xml.XmlDocument (>= 4.3.0)

.NETStandard 2.0
    No dependencies.
```

Zdroje:

[.NET Standard verze](https://github.com/dotnet/standard/blob/master/docs/versions.md)

[MSDN Blog - Introduction to .NET Standard](https://blogs.msdn.microsoft.com/dotnet/2016/09/26/introducing-net-standard/)