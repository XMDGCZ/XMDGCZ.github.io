---
layout: post
title: "Push notifikace v Xamarin aplikaci pomocí AppCenter"
categories:
            - "Xamarin Forms Úvod"
author: "David Malý"
---
Většina dnešních aplikací potřebuje přijímat vzdáleně odesílané notifikace - push notifikace. V tomto článku je ukázáno, jak docílit porpojení AppCenter Push nofikací s Xamarin.Forms aplikací.
<!--excerpt-->

Následující článek se dělí na 2 části:
- [Přidání aplikace do AppCenter](#s1)
- Inicializace AppCenter v aplikaci Xamarin a nastavení aplikace pro přijímání notifikací
- Zaregistrování do Firebase
- Nastavení aplikace, aby zpracovávala push notifikace

> Při práci s AppCenter je nutno mít na paměti, že některé funkce jsou stále v přeběžném přístupu.

{: #s1 }
# AppCenter - vytvoření aplikace

V první řadě je doporučené ve [webovém portálu AppCenter](https://appcenter.ms/apps) vytvořit projekt (aplikaci), protože se jedná o nejjednodušší způsob, jak si zobrazit aktuální tutoriál pro integraci AppCenter do již existující aplikace.

![AppCenterInit](/assets/posts/courses/2018-11-19-PushNotifications_AppCenter/AppCenterInit.gif)


#  Xamarin aplikace - nastavení aplikace
Po přidání projektu do AppCenter se zobrazí tutoriál, který se skládá z:
- Přidání Nuget balíčku, pro tuto ukázku využejeme nuget balíčku: `Microsoft.AppCenter.Push`, který použijeme pro PushNotifikace.
- V aplikaci spustit komunikaci s AppCenter pomocí vygenerovaného klíče

![AppCenterKey](/assets/posts/courses/2018-11-19-PushNotifications_AppCenter/AppCenterKey.png)

Do nově vytvořené (nebo existující) Xamarin.Forms aplikace je třeba tento balíček naisntalovat do knihovny (.NET Standard) i do příslušných podprojektů (Android), tak jako je tomu na následujícím obrázku.

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
Nejprve je potřeba vytvořit projekt ve [webovém portálu Firebase](https://console.firebase.google.com). 

Po vytvoření projektu přidat aplikaci do Firebase, při zadávání **musí jméno balíčku přesně odpovídat jménu balíčku Android aplikace**, tak jako tomu je na následujícím obrázku:

![FirebaseAppInit](/assets/posts/courses/2018-11-19-PushNotifications_AppCenter/FirebaseJson.png)

Po vyplnění formuláře stáhnout google-services.json a vložit ho do Xamarin.Android aplikace. Po přidání je nutné nastavit akci sestavení jako **GoogleServicesJson**.
> Pokud tato možnost není dostupná (bug), stačí restartovat Visual Studio

![FirebaseAppInit](/assets/posts/courses/2018-11-19-PushNotifications_AppCenter/XamarinJson.png)

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

Posledním krokem je povolení AppCenter využívání služeb Firebase, tak že je to AppCenter přidán ServerKey z Firebase.

Ve FireBase je server key k nalezení v Project Settings -> Cloud Messaging. V App Center je vložení Server Key posledním krokem v Push.

![FirebaseAppInit](/assets/posts/courses/2018-11-19-PushNotifications_AppCenter/FirebaseInAppCenter.png)

# Handler pro příchozí zprávy
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