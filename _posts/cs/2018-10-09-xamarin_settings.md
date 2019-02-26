---
layout: post
title: "Nastavení prostředí pro tvorbu Xamarin aplikací" 
categories:
            - "visual-studio-2017"
author: "Vojtěch Mádr"
---

V tomto článku si popíšeme, jak nastavit VS2017 a VSforMac, abychom na něm mohli pracovat s Xamarinem.

## Visual Studio 2017


Při práci na Windows PC, je pro vývoj v Xamarinu potřeba mít nainstalované Visual Studio 2017 se zaškrtnutým Xamarinem (při instalaci).

![alt text](/assets/posts/2018-10-09-xamarin_settings/Screenshot_01.png)



[https://www.visualstudio.com/vs/](https://www.visualstudio.com/vs/)


### Android Emulator a Hyper-V

Pro spuštení Android Emulátoru je potřeba mít vypnuté Hyper-V. Pro urychlejí tohoto procesu je možné využit tento příkaz do CMD či Poweshellu. **(Nutný restart PC!)**

``` c#
bcdedit /set hypervisorlaunchtype off
```



**V experimentální rovině jde využít i se zapnutým Hyper-V**

[https://blogs.msdn.microsoft.com/visualstudio/2018/05/08/hyper-v-android-emulator-support/](https://blogs.msdn.microsoft.com/visualstudio/2018/05/08/hyper-v-android-emulator-support/)




## Visual Studio For Mac

Pro práci pro Macu je nutné mít nainstalované VS4Mac.

Více informací zde:

[https://www.visualstudio.com/vs/mac/](https://www.visualstudio.com/vs/mac/)