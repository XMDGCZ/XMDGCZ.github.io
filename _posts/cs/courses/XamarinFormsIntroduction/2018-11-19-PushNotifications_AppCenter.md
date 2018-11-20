---
layout: post
title: "Push notifikace pomocí AppCenter"
categories:
            - "Xamarin Forms Úvod"
author: "David Malý"
---
Většina dnešních aplikací potřebuje přijímat vzdáleně odesílané notifikace - push notifikace. V tomto článku je ukázáno, jak docílit propojení AppCenter Push nofikací s Xamarin.Forms aplikací.
<!--excerpt-->

Následující článek se dělí na několik částí:
- [Přidání aplikace do AppCenter](#AppCenter)
- [Inicializace AppCenter v aplikaci Xamarin a nastavení aplikace pro přijímání notifikací](#NastaveniAplikace)
- [Zaregistrování do Firebase](#Firebase)
- [Nastavení aplikace, aby zpracovávala push notifikace](#Handler)
- [Workaroud: Aplikace nezpracovává notifikace](#Workaround)

> Při práci s AppCenter je nutno mít na paměti, že některé funkce jsou stále v přeběžném přístupu.

# AppCenter - vytvoření aplikace
{: #AppCenter }
V první řadě je doporučené ve [webovém portálu AppCenter](https://appcenter.ms/apps) vytvořit projekt (aplikaci), protože se jedná o nejjednodušší způsob, jak si zobrazit aktuální tutoriál pro integraci AppCenter do již existující aplikace.

![AppCenterInit](/assets/posts/courses/2018-11-19-PushNotifications_AppCenter/AppCenterInit.gif)


#  Xamarin aplikace - nastavení aplikace
{: #NastaveniAplikace }
Po přidání projektu do AppCenter se zobrazí tutoriál, který se skládá z dvou kroků:
- Přidání Nuget balíčku, pro tuto ukázku využejeme nuget balíčku: `Microsoft.AppCenter.Push`, který použijeme pro PushNotifikace
- V aplikaci spuštění komunikace s AppCenter pomocí vygenerovaného klíče

![AppCenterKey](/assets/posts/courses/2018-11-19-PushNotifications_AppCenter/AppCenterKey.png)

Do nově vytvořené (nebo existující) Xamarin.Forms aplikace je třeba tento balíček naisntalovat do knihovny sdíleného kódu (.NET Standard) i do příslušných podprojektů (Android), tak jako je tomu na následujícím obrázku.

![XamarinNuget](/assets/posts/courses/2018-11-19-PushNotifications_AppCenter/XamarinNuget.png)

A do App.xaml.cs ve sdíleném projektu přidat:
``` c#
using Microsoft.AppCenter.Push;
using Microsoft.AppCenter;

AppCenter.Start("2dea1c45-bb01-4dcf-9878-f11f7ddbf509", typeof(Push));
```

Výsledná třída App.xaml.cs může vypadat takto:
``` c#
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();

            AppCenter.Start("2dea1c45-bb01-4dcf-9878-f11f7ddbf509", typeof(Push));
            MainPage = new MainPage();
        }
     ....
     }
```

# Zaregistrování do Firebase
{: #Firebase }
Nejprve je potřeba vytvořit projekt ve [webovém portálu Firebase](https://console.firebase.google.com). 

Po vytvoření projektu přidat aplikaci do Firebase, při zadávání **musí jméno balíčku přesně odpovídat jménu balíčku Android aplikace**, tak jako tomu je na následujícím obrázku:

![FirebaseAppInit](/assets/posts/courses/2018-11-19-PushNotifications_AppCenter/FirebaseAppInit.png)

Po vyplnění formuláře stáhnout google-services.json a vložit ho do Xamarin.Android aplikace. Po přidání je nutné nastavit akci sestavení jako **GoogleServicesJson**.

![FirebaseAppInit](/assets/posts/courses/2018-11-19-PushNotifications_AppCenter/FirebaseJson.png)

![FirebaseAppInit](/assets/posts/courses/2018-11-19-PushNotifications_AppCenter/XamarinJson.png)

> Pokud tato možnost není dostupná (bug), stačí restartovat Visual Studio
Dalším krokem je editace AndroidManifest.xml, kam jsou vloženy řádky označené komentářem:

``` xml
<application ...>
<!-- Přidat tyto řádky -->
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
    </intent-filter>
    </receiver>
<!-- Toť vše :) -->
</application>
```

Posledním krokem je povolení AppCenter využívání služeb Firebase, tak že je do AppCenter přidán ServerKey z Firebase.

Ve FireBase je ServerKey k nalezení v Project Settings -> Cloud Messaging. V App Center je vložení Server Key posledním krokem v Push.

![FirebaseAppInit](/assets/posts/courses/2018-11-19-PushNotifications_AppCenter/FirebaseInAppCenter.png)


# Handler pro příchozí zprávy
{: #Handler }
Zachycení zprávy je v aplikaci vyřešené pomocí handleru Push.PushNotificationReceived.

Do App.xaml.cs pod inicializaci AppCenter stačí přidat následující kód:


``` csharp
public App()
{
    InitializeComponent();

    AppCenter.Start("2dea1c45-bb01-4dcf-9878-f11f7ddbf509", typeof(Push));
    // Přidaný kód
    if (!AppCenter.Configured)
    {
        Push.PushNotificationReceived += (sender, e) =>
        {
            // Add the notification message and title to the message
            var summary = $"Push notification received:" +
                                $"\n\tNotification title: {e.Title}" +
                                $"\n\tMessage: {e.Message}";

            // If there is custom data associated with the notification,
            // print the entries
            if (e.CustomData != null)
            {
                summary += "\n\tCustom data:\n";
                foreach (var key in e.CustomData.Keys)
                {
                    summary += $"\t\t{key} : {e.CustomData[key]}\n";
                }
            }

            // Send the notification summary to debug output
            System.Diagnostics.Debug.WriteLine(summary);
        };
    }
}
```
> V některých případech se stane, že zpráva dojde do zařízení, ale PushNotificationReceived se nezavolá. Pro tento případ je zde popsaný workaround, který tento problém řeší.

# Wokraround: Zpráva není zpracována aplikací
{: #Workaround }
Chyba je nejspíše na straně nuget balíčku AppCenter.Push. Naštěstí lze celou část kódu pro přijetí zprávy nativně reprodukovat.

K tomu je potřeba:
- [Vytvořit Android Service, který je zavolaný po přijetí zprávy](#AndroidService)
- [Pomocí Xamarin.Forms MessagingCenter notifikovat o přijetí zprávy](#MessagingCenter)


## Vytvoření Android Service
{: #AndroidService }
V projektu pro Android vytvoříme třídu ForegroundFirebaseMessagingService, která dědí z FirebaseMessagingService, tedy využívá binding na nativní Firebase balíček (bez AppCenter).

Tato třída registruje IntentFilter pro firebase.MESSAGING_EVENT a obsahuje metodu, která je zavolaná po přijetí zprávy OnMessageReceived. V této metodě, kromě logů do konzole je i zaslání zprávy MessagingCentrem.

``` csharp
using Firebase.Messaging;
... namespace
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class ForegroundFirebaseMessagingService : FirebaseMessagingService
{
    const string TAG = "MyFirebaseMsgService";
    public override void OnMessageReceived(RemoteMessage message)
    {
        Log.Debug(TAG, "From: " + message.From);
        Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
        MessagingCenter.Send<object, string>(this, "PushNotificationForegroundMessage", message.GetNotification().Body);
    }
}
```

## MessagingCenter zasílající informaci z Android části projektu do Xamarin.Forms sdíleného kódu
{: #MessagingCenter }
Pro přijímání zpráv z MessagingCenter je nutné, aby ji měl kdo přijímat (zaregistrování k odběru). To můžeme provést v App.xaml.cs. 

Pomocí arrow funkce je možné spustit svůj kód po obdržení zprávy, kód v ukázce zobrazuje Alert s obsahem z cloud notifikace.
``` csharp
MessagingCenter.Subscribe<object, string>(this, "PushNotificationForegroundMessage", (sender, args) => 
{
    Xamarin.Forms.Device.BeginInvokeOnMainThread(async () =>
    {
        await DisplayAlert("PushNotificationForegroundMessage", args, "ok");
    });
});
```

> Data, která jsou zasílána pomocí MessagingCenter je možné libovolně upravit. MessagingCenter není součástí AppCenter ani Firebase.

