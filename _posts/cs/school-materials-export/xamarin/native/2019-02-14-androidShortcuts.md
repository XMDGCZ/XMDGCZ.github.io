---
layout: post
title: "Android zkratky "
categories:
    -"xamarin"
    -"native"
author: "David Malý"
--- 


##   Android Shortcuts (zkratky)


Lukáš Breburda



Android zkratky přišly s Androidem 7.1, a umožňují uživatelům rychle přejít z domovské obrazovky rovnou do určité části aplikace, respektive na konkrétní page.

![](https://blog.xamarin.com/wp-content/uploads/2016/10/3edf51ed-45c7-4ed5-8de8-deea5fd14b25.png)

Každá zkratka směruje k jedné nebo více akcím. Příklady těchto zkratek mohou být následující:


- Navigace k určité lokalitě v mapovací aplikaci
- Poslání zprávy kamarádovi v komunikační aplikaci
- Přehrání další epizody seriálu v mediální aplikaci.
- Vrácení se k poslednímu bodu ve hře.


### Typy zkratek


Existují 3 typy zkratek:


- Statické zkratky jsou definovány ve zdrojovém kódu. S touto metodou musí být celá aplikace aktualizována, aby došlo ke změně obsahu zkratek.
- Dynamické zkratky se přidávájí pomocí Manažeru zkratek při runtime. Po tuto dobu mohou být zkratky vytvářeny, měněny, nebo i mazány.
- Připnuté zkratky se také přidávají pomocí Manažeru zkratek. Při runtime se aplikace může zeptat na připnutí zkratky, kdy tuto akci musí uživatel potvrdit.<br>        Tyto připnuté zkratky se zobrazí v podporovaných launcherech pouze po odsouhlasení uživatelem.


#### Statické zkratky


Při použití statických zkratek je zapotřebí si nejdříve vytvořit XML soubor pro zkratky. Tento soubor musí být umístěn ve složce **Resources** a musí se jmenovat<br>    **shortcuts.xml**. V tomto souboru se poté specifikují jednotlivé zkratky, a to následovně:


```

<shortcuts xmlns:android="http://schemas.android.com/apk/res/android">
  <shortcutandroid:shortcutId="sessions"android:enabled="true"android:icon="@drawable/menu_sessions"android:shortcutShortLabel="Sessions"android:shortcutLongLabel="Sessions"android:shortcutDisabledMessage="Sessions"><intent  android:action="android.intent.action.VIEW"  android:targetPackage="com.xamarin.xamarinevolve"  android:targetClass="com.xamarin.xamarinevolve.MainActivity"  android:data="sessions" /><!-- If your shortcut is associated with multiple intents, include them	 here. The last intent in the list is what the user sees when they	 launch this shortcut. --><categories android:name="android.shortcut.conversation" />
  </shortcut>
  <!-- Specify more shortcuts here. -->
</shortcuts>

```


Zde je možné si všimnout, že **targetClass** je nastavena na MainActivity, ale může být nastavena i na jiné aktivity, pokud je hodnota Export nastavena na true.<br>    V tom případě je dobré specifikovat tyto aktivity manuálně, a to následnovně:


```

[Activity(Label = "Evolve16",Name="com.xamarin.xamarinevolve.MainActivity",Exported = true,Icon = "@drawable/newicon",LaunchMode = LaunchMode.SingleInstance,ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]

```


Nakonec musí existovat i kód pro odezvu při aktivaci zkratky. V tomto případě stačí použít jednoduchého switche, a to následovně:


```

if(string.IsNullOrWhiteSpace(Intent?.Data?.LastPathSegment))
{
  switch(Intent.Data.LastPathSegment)
  {
    case "sessions":
      MessagingService.Current.SendMessage("DeepLinkPage", new DeepLinkPage
        { Page = AppPage.Sessions });
       break;
    case "events":
      MessagingService.Current.SendMessage("DeepLinkPage", new DeepLinkPage
        { Page = AppPage.Events});
      break;
    case "minihacks":
      MessagingService.Current.SendMessage("DeepLinkPage", new DeepLinkPage
        { Page = AppPage.MiniHacks});
      break;
  }
}

```

#### Dynamické zkratky


Dynamické zkratky poskytují odkazy ke specifickým, na kontext citlivým akcím v aplikaci. Tyto akce mohou změnit používání aplikace, a mohou také změnit kdy aplikace poběží.<br>    Mezi dobré příklady patří volání specifické osobě, navigace do specifické lokality, nebo zobrazení současného stavu hry.



Manažer zkratek umožňuje vykonávat následující akce u dynamických zkratek:


- Publikace: Zde se používá funkce setDynamicShortcuts() pro redefinování celého seznamu zkratek nebo addDynamicShortcuts() pro přidání zkratky do již<br>        existujícího seznamu.
- Update: Zde se užívá metody updateShortcuts() pro alteraci dané zkratky.
- Smazání: Odstranění dynamické zkratky se provádí buďto funkcí removeDynamicShortcuts(), nebo smazáním celé kolekce zkratek užitím funkce removeAllDynamicShortcuts().



Pro představu vytvoření dynamické zkratky a její přidělení k aplikaci slouží následující snippet:


```

ShortcutManager shortcutManager = getSystemService(ShortcutManager.class);

ShortcutInfo shortcut = new ShortcutInfo.Builder(this, "id1")
    .setShortLabel("Web site")
    .setLongLabel("Open the web site")
    .setIcon(Icon.createWithResource(context, R.drawable.icon_website))
    .setIntent(new Intent(Intent.ACTION_VIEW,
                   Uri.parse("https://www.mysite.example.com/")))
    .build();

shortcutManager.setDynamicShortcuts(Arrays.asList(shortcut));

```

#### Připnuté zkratky


Od verze Androidu 8.0 a výše je možné vytvářet takzvané připnuté zkratky. Narozdíl od statických nebo dynamických zkratek se připnuté zkratky zobrazují v podporovaných<br>    launcherech jako oddělené ikony.

![Připnuté ikony](https://developer.android.com/images/guide/topics/ui/shortcuts/pinned-shortcuts.png)

Pro připnutí zkratky je potřeba projít následujícímy kroky:


- Použití funkce isRequestPinShortcutSupported() pro ověření, zda launcher podporuje připnuté zkratky
- Vytvoření objektu ShortcutInfo, který buďto obsahuje pouze ID zkratky, která již existuje, nebo i krátký label a intent, pokuď se jedná o novou zkratku
- Pokus o připnutí zkratky zavoláním funkce requestPinShortcut(), při které lze využít objectu PendingIntent, který upozorní aplikaci v případě úspěšného připnutí



Následující snippet demonstruje tento proces:


```

ShortcutManager mShortcutManager =
        context.getSystemService(ShortcutManager.class);

if (mShortcutManager.isRequestPinShortcutSupported()) {
    // Předpokládejme že zkratka již existuje s ID "my-shortcut".
    // Tato zkratka musí být aktivní.
    ShortcutInfo pinShortcutInfo =
            new ShortcutInfo.Builder(context, "my-shortcut").build();

    // Vytvoření PendingIntent objektu pouze pokud aplikace potřebuje zpětnou vazbu
    // která oznámí zda uživatel souhlasil s připnutím zkratky. Pokud však uživatel neodsouhlasí připnutí,
    // aplikace upozorněna nebude. Předpokládejme že aplikace obsahuje metodu
    // zvanou createShortcutResultIntent(), která vrací intent.
    Intent pinnedShortcutCallbackIntent =
            mShortcutManager.createShortcutResultIntent(pinShortcutInfo);

    // Configurace intentu tak, aby aplikace získala úspěšně odezvu.
    PendingIntent successCallback = PendingIntent.getBroadcast(context, 0,
            pinnedShortcutCallbackIntent, 0);

    mShortcutManager.requestPinShortcut(pinShortcutInfo,
            successCallback.getIntentSender());
}

```


Dokumentace: [App Shortcuts](https://developer.android.com/guide/topics/ui/shortcuts.html)

