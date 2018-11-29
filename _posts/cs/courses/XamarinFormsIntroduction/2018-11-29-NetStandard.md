---
layout: post
title: ".NET Standard"
categories:
            - "Xamarin Forms Úvod"
post_icon: "fas fa-play-circle"
sort_index: 1
author: "David Malý"
---
Co je .NET Standard, PCL, nebo BCL a FCL? A proč by to .NET vývojáře mělo zajímat.
<!--excerpt-->

<iframe src="https://onedrive.live.com/embed?cid=700E9A68882DA0BB&amp;resid=700E9A68882DA0BB%212884&amp;authkey=AJKwdVggzjmnKl0&amp;em=2&amp;wdAr=1.7777777777777777" width="1186px" height="691px" frameborder="0">This is an embedded <a target="_blank" href="https://office.com">Microsoft Office</a> presentation, powered by <a target="_blank" href="https://office.com/webapps">Office Online</a>.</iframe>

Obsah prezentačních slides:
- [.NET Frameworky](#NETFrameworky)
- [BCL a FCL](#BCLandPCL)
- [.NET Standard](#NetStandard)
- [Obsah .NET Standard](#NetStandardObsah)
- [PCL](#PCL)
- [Verze .NET Standard](#NetStandardVerze)
- [Shared](#Shared)
 
# .NET Frameworky
{: #NETFrameworky }
.NET framework byl v posledních letech roztříštěn, jako příklad si můžeme vzít Mono - Open source implementaci .NET Frameworku, která je určená pro multiplatformní vývoj. Forkem Mona vzniklo Unity, které se od původního .NET frameworku ještě vzdálilo.

Nejnověším přírůstkem do .NET frameworků je .NET Core. 

.NET Framework
- zaměřen na vývoj desktopových aplikací
- distribuován jako Windows komponenta
- největší code base mezi frameworky
- primární platforma vývoje v .NET

.NET Core
- konzolové multiplatformní aplikace
- open source
- v balíčku aplikace jsou distribovány všechny části, které aplikace potřebuje ke svému běhu
- kompatibilní s ostatními technologiemi
 
Xamarin
- implementace Mona
- multiplatformní mubilní aplikace
- open source
- vlastní runtime

# BCL a FCL
{: #BCLandPCL }
Base class libraries jsou základem pro každý framework, obsahují např. definice pro datové typy a základní knihovny. Jsou popsané normou ECMA, které popisuje i Common Language Infrastructure.

Na základě BCL jsou vytvořený Framework Class libraries, které jsou specifikcé pro konkrétní účel (WPF, Xamarin.Android). Mají mnohoem více rozšíření (extensions) a tvoří základní code base, se kterou pracuje programátor. Vytváří framework.

Problémem pro programátora je nutnost znalosti BCL i v případě, kdy pracuje s FCL např. by měl vědět, jak funguje Mono, pokud chce pracovat s Xamarin.iOS.

# .Net Standard
{: #NetStandard }
Sjednocuje definice pro BCL, vytváří abstraktní vrstvu, na kterou mohou spoléhat Framework Class Libraries, je tedy možné sdílet kód napříč platformami.

Je distribuován jako nuget balíček ve specifické verzi, kterou používají vývojáři aplikací jako referenci (dependency). Existující .NET implementace (např. Xamarin, .Net Core) se zavazují podporovat .NET Standard specifickou verzi, více o verzích v dalším slide.

.NET Standard je možné si představit jako Hardware Abstraction Layer (HAL) část operačního Windows NT. Proti této vrstvě jsou psány SW aplikace, HW počítače kompatibilní s Windows musí zajistit funkčnost na základě popisu HAL. Stejné to je s .NET Standard, vývojáři aplikací píší svůj kód proti specifické verzi a runtime (Xamarin, .NET Core) se zavazuje implementovat specifickou verzi .NET Standard. Výsledkem je, že vývojář píše kód nezávisle na výsledné platformně. 

V případě, že vývojář potřebuje něco, co cílová platorma nepodporuje je možný jeden ze scénářů:
- při volání API dostane vývojář PlatformNotSupportedException
- API vůbec není dostupné v .NET Standard a je nutné použít nuget balíček, který toto API zpřítupňuje pro konkrétní platformu


# Obsah Net Standard
{: #NetStandardObsah }

# PCL
{: #PCL }

# Verze .NET Standard
{: #NetStandardVerze }

# Shared
{: #Shared }



