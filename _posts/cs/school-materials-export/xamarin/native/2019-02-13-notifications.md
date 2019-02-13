---
layout: post
title: "notifications.php "
categories:
    -"xamarin"
    -"xamarin"
author: "David Malý"
--- 


## Lokální notifikace


<br>    Notifikace jsou základem většiny dnešních aplikací. Následující článek popisuje vytvoření jednoduché multiplatformní a složitější Android nativní notifikace.<br>


### Multiplatformní notifikace


<br>    Pro vytvoření multiplatformní notifikace dobře poslouží [Notifier plugin](https://github.com/edsnider/Xamarin.Plugins/tree/master/Notifier) do Xamarin. Stačí ho přidat do všech podprojektů v Solution.<br>



<br>    Okamžité zobrazení notifikace, která spustí aplikaci, když na ní uživatel tapne.<br>


```

CrossLocalNotifications.Current.Show("You've got mail", "You have 793 unread messages!");

```


<br>    Metoda Show vyžaduje parametry:<br>    
- Title
- Body



<br>    Tato notifikace slouží pouze jako infromativní, nelze do ní předat data, nebo specifikovat akci po tapnutí.<br>



<br>    Na Androidu lze nastavit i datum a čas, kdy se má notifikace zobrazit.<br>


```

CrossLocalNotifications.Current.Show("Good morning", "Time to get up!", 1, date);

```


<br>    Metoda Show vyžaduje parametry:<br>    
- Title
- Body
- Id notifikace - jeho pomocí je možné plánované zobrazení notifikace zrušit, či aktualizovat obsah
- Date - datum a čas zobrazení notifikace


### Nativní Android notifikace


<br>    Následující příklad ukazuje nativní vytvoření Android notifikace a předání dat aplikaci po tapnutí na notifikaci.<br>



<br>    Pro využití nativního kódu platformy je použit Dependendcy service<br>


#### Dependency service

##### Interface


<br>    Interface předepisující hlavičky metod pro okamžité zobrazení notifikace bez a včetně dat.<br>


```

public interface INotificationHelper
{
    void Notify(string title, string body);

    void Notify(string title, string body, Dictionary<string, string> data);
}

```

##### Realizace interface


<br>    Realizace interface se nalézá v Android podprojektu. Metody Show se od sebe liší pouze vytvořením Bundle objektu, ve kterém jsou předána data.<br>

*Pozn. Bundle je Android nativní třída*
```


using System.Collections.Generic;
using Android.App;
using Android.Content;
using Android.OS;

using LocalNotifications.Droid;
using Xamarin.Forms;
using Application = Android.App.Application;

[assembly: Dependency(typeof(NotificationHelper))]

namespace LocalNotifications.Droid
{
    public class NotificationHelper : INotificationHelper
    {
        Context _context = Application.Context;
        public const string IntentDataKey = "key";
        const int NotificationId = 0;

        public void Notify(string title, string body)
        {
            Intent intent = new Intent(_context, typeof(MainActivity));

            // Create a PendingIntent; we're only using one PendingIntent (ID = 0):
            const int pendingIntentId = 0;
            PendingIntent pendingIntent = PendingIntent.GetActivity(_context, pendingIntentId, intent,
                PendingIntentFlags.OneShot);


            // Instantiate the builder and set notification elements:

            Notification.Builder builder = new Notification.Builder(_context)
                .SetContentIntent(pendingIntent)
                .SetContentTitle(title)
                .SetContentText(body)
                .SetDefaults(NotificationDefaults.All)
                .SetSmallIcon(Resource.Drawable.abc_ic_menu_overflow_material);

            // Build the notification:
            Notification notification = builder.Build();

            // Get the notification manager:
            NotificationManager notificationManager =
                _context.GetSystemService(Context.NotificationService) as NotificationManager;


            // Publish the notification:

            notificationManager.Notify(NotificationId, notification);
        }

        public void Notify(string title, string body, Dictionary<string, string> data)
        {
            Intent intent = new Intent(_context, typeof(MainActivity));

            Bundle b = new Bundle();
            foreach (var item in data)
            {
                b.PutString(item.Key, item.Value);
            }

            intent.PutExtra(IntentDataKey, b);

            // Create a PendingIntent; we're only using one PendingIntent (ID = 0):
            PendingIntent pendingIntent = PendingIntent.GetActivity(_context, NotificationId, intent,
                PendingIntentFlags.OneShot);


            // Instantiate the builder and set notification elements:

            Notification.Builder builder = new Notification.Builder(_context)
                .SetContentIntent(pendingIntent)
                .SetContentTitle(title)
                .SetContentText(body)
                .SetDefaults(NotificationDefaults.All)
                .SetSmallIcon(Resource.Drawable.abc_ic_menu_overflow_material);

            // builder.SetWhen(Java.Lang.JavaSystem.CurrentTimeMillis());
            // Build the notification:
            Notification notification = builder.Build();

            // Get the notification manager:
            NotificationManager notificationManager =
                _context.GetSystemService(Context.NotificationService) as NotificationManager;


            // Publish the notification:

            notificationManager.Notify(NotificationId, notification);
        }
    }

```
[Rozšířený článek o tvorbě Android nativních notifikací v Xamarin](https://developer.xamarin.com/guides/android/application_fundamentals/notifications/local_notifications_in_android/)



[Java Android notifikace](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
#### Zpracování dat z notifikace


<br>    Xamarin Forms běží na platformě Android v jedné Activity, proto se při tvorbě notifikace nemusí žádná specifikovat. Jednoduše je spuštěna aplikace. Co ale dělat ve chvíli, kdy chceme předat s notifikací data a podle nich poté upravit chování aplikace?<br>



<br>    Výše zmíněná realizace interface využívá C# třídy Dictionary přenos dat v PCL. Instance této třídy je změněna na Bundle objekt, který se nativně v Androidu používá pro přenos jednoduchých dat v aplikaci. Tento objekt je zpracován ve třídě MainActivity, kde je zpracována hned po zapnutí aplikace.<br>



<br>    Třída MainActivity, která po vytvoření kontroluje, zda jí byl předán Bundle objekt např. z notifikace a podle něj zobrazí novou ReceiverPage, kam přepošle data.<br>


```

[Activity(Label = "LocalNotifications", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(bundle);

        global::Xamarin.Forms.Forms.Init(this, bundle);
        LoadApplication(new App());


        if (Intent.HasExtra(NotificationHelper.IntentDataKey))
        {
            ProcessNotificationData();
        }
    }


    public void ProcessNotificationData()
    {

        // Get data from notification as Bundle object
        Bundle bundleFromNotification = Intent.Extras.GetBundle(NotificationHelper.IntentDataKey);

        Dictionary<string, string> data = new Dictionary<string, string>();

        // Copy data from bundle to Dictionary
        foreach (var key in bundleFromNotification.KeySet())
        {
            data.Add(key, bundleFromNotification.GetString(key));
        }

        // Replace actual Page with ReceiverPage and pass data
        Xamarin.Forms.Application.Current.MainPage = new ReceiverPage(data);
    }
}

```


<br>    ReceiverPage<br>


```

public class ReceiverPage : ContentPage
{
    ListView _listView = new ListView();

    public ReceiverPage()
    {
        Content = new StackLayout
        {
            Children = {
                new Label { Text = "Hello Page" }
            }
        };
    }

    public ReceiverPage(Dictionary<string, string> data)
    {

        Content = new StackLayout
        {
            Children = {
                new Label { Text = "Data from notification" },
                _listView
            }
        };
        _listView.ItemsSource = data;
    }
}

```

#### Použití notifikace v PCL


<br>    Samotné použití je po složitější implementaci je stejně jednoduché jako v případě nuget balíčku.<br>


```

 DependencyService.Get<INotificationHelper>().Notify("title","body");

```


<br>    V případě předání dat je použití následující<br>



<br>    Příprava dat jako slovník.<br>


```

Dictionary<string, string> data = new Dictionary<string, string>();
data.Add("data1", "value");
data.Add("data2", "value2");

```


<br>    Použití<br>


```

DependencyService.Get<INotificationHelper>().Notify("title","body",data);

```
[Celý vzorový projekt](https://github.com/malyda/Xamarin-LocalNotifications)