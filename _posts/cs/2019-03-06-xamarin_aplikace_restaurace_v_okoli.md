---
layout: post
title: "Xamarin: Aplikace Restaurace v okolí" 
categories:
            - "HowTo"
author: "Matěj Vlk"
---

Jak pomocí frameworku Xamarin Forms jednoduše vytvořit aplikaci pro vyhledání nejlepších restaurací

## Úvod

Vítejte u článku, jehož cílem je prezentovat, jak se pomocí frameworku Xamarin.Forms dá vytvořit jednoduchá mobilní aplikace zobrazující mapu s restauracemi ve Vašem okolí.

Pokud jste se s Xamarin.Forms zatím neseznámili, doporučuji si přečíst nejprve [teoretické seznámení s tímto frameworkem](https://www.skeleton.cz/uvod-do-xamarin-forms) na blogu firmy Skeleton Software a dále první praktické seznámení v podobě [tvorby jednoduché aplikace Občanka](https://www.skeleton.cz/uvod-do-xamarin-forms), kde jsou popsány základy jako je založení projektu, vytvoření stránky, spuštění aplikace, navigace mezi stránkami, tvorba grafického rozhraní a obsloužení událostí prvků grafického rozhraní. Tento článek na ně navazuje a zaměříme se v něm převážně na práci s mapou, tvorbu vlastních rendererů, mechanismus DependencyServices, získávání polohy telefonu a využívání API Google Maps Platform.

Výsledkem naší práce bude aplikace obsahující jednu stránku s mapou, na které bude zobrazena aktuální pozice zařízení a všechny restaurace v nastavené vzdálenosti. Jednotlivé piny na mapě představující restaurace budou navíc barevně odlišeny podle získaného hodnocení v recenzích na Google mapách.

Pusťme se tedy do práce a založme nový Xamarin.Forms projekt s názvem třeba FoodNearMe. Podporovat budeme pouze Android a iOS, takže ostatní projekty kromě těchto dvou a společného můžeme smazat.

## Komponenta Xamarin.Form.Map

Začneme s přípravou mapy

Pracovat budeme s komponentou Map poskytnutou přímo frameworkem Xamarin.Forms. Oficiální tutoriál pro tuto komponentu se nachází [zde](https://docs.microsoft.com/en-gb/xamarin/xamarin-forms/user-interface/map) a doporučuji si jej nastudovat, jelikož obsahuje mnoho dalších užitečných informací, které není možné v rámci tohoto článku zmínit.

Podle zmíněného návodu nejprve přidáme NuGet balíček Xamarin.Forms.Maps do všech projektů v našem řešení (solution). Dále v každém platformním projektu za inicializaci Xamarin.Forms přidáme i inicializaci mapy. Na iOSu se jedná o přidání následujícího řádku do metody FinishedLaunching ve třídě AppDelegate.

```c#
      Xamarin.FormsMaps.Init();
```

Na Androidu v MainActivity v metodě OnCreate přidáme tento řádek.

```c#
      Xamarin.FormsMaps.Init(this, bundle);
```

Pokračujeme potřebnou konfigurací v platformních projektech. Na obou platformách je potřeba vyžádat pro aplikaci oprávnění pro přístup k poloze telefonu. Pro zobrazení mapy na Androidu si budete muset navíc obstarat vlastní API klíč pro Google Maps API v2 a vložit ho do AndroidManifestu. Postup pro jeho získání je popsán [zde](https://docs.microsoft.com/en-gb/xamarin/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key?tabs=windows).

Po úspěšném dokončení prvního tutoriálu můžeme začít využívat tuto základní mapu.

## Vytvoření CustomMap

Na mapě ale chceme používat různobarevné piny pro odlišení kvality restaurací, a to je nad rámec poskytnuté funkčnosti základní Map komponentou. Budeme tedy muset vytvořit vlastní komponentu pojmenovanou CustomMap, která bude tu základní o tuto funkčnost rozšiřovat.

Začneme tvorbou objektu, který bude představovat náš barevný pin. Ten vypadá následovně:

```c#
public class CustomPin
{
    public Gps Location{ get; set; }

    public string Title{ get; set; }

    public string Description { get; set; }

    public Color Color{ get; set; }
}
```

Pro reprezentaci pozice budeme v celé naší aplikaci používat vlastní objekt Gps:

```c#
public class Gps
{
    public static explicit operator Position(Gps gps)
    {
        return new Position(gps.Latitude, gps.Longitude);
    }

    public double Latitude{ get; set; }

    public double Longitude{ get; set; }

    public override string ToString()
    {
        return $"{Latitude.ToString(string.Empty, CultureInfo.InvariantCulture)},{Longitude.ToString(string.Empty, CultureInfo.InvariantCulture)}";
    }

    public override bool Equals(object other)
    {
        return double.Equals(Latitude, (other as Gps).Latitude) & double.Equals(Longitude, (other as Gps).Longitude);
    }
}
```

Dále do společného projektu do složky Controls vytvoříme naši komponentu – třídu CustomMap.cs, která bude představovat rozhraní naší vlastní mapy. Zdědíme ji od Xamarin.Forms.Maps.Map a rozšíříme o kolekci vlastních pinů, které na mapě budeme zobrazovat.

**Controls - CustomMap.cs**

```c#
public class CustomMap : Xamarin.Forms.Maps.Map
{
    private ObservableCollection customPins;

    public CustomMap()
    {
        customPins = new ObservableCollection();
    }

    public ObservableCollection CustomPins
    {
        get{ return this.customPins; }
    }
}
```

## Vlastní rendery

Aplikace vytvořené v Xamarin.Forms po překladu pro konkrétní platformu na ní pak vypadají jako aplikace psané nativně a využívají její nativní komponenty. To je umožněno právě díky rendererům. To jsou třídy v platformních projektech, které se starají o překlad mezi společnou „Formsovou“ komponentou a nativní komponentou dané platformy. Na obrázku níže je vidět na jaké nativní komponenty se pomocí MapRendereru překládá základní Formsová komponenta Map.

![MapClasses](/assets/posts/2019-03-06-xamarin_aplikace_restaurace_v_okoli/MapClasses.png)

Pokud využíváme jen základní Formsové komponenty, tak žádné renderery samozřejmě psát nemusíme – jsou již obsažené ve frameworku Xamarin.Forms. Díky tomu je při vývoji pro více platforem sdílená naprostá většina kódu, což vede k výraznému urychlení vývoje. Pokud bychom naopak potřebovali aplikaci se složitou a platformě specifickou funkčností s větším množstvím vlastních komponent, je potřeba zvážit, zdali čas ušetřený na sdíleném kódu převýší čas vývoje a vyladění vlastních rendererů, které by bylo potřeba pro tyto komponenty dopsat.

Defaultní renderery pro komponentu Map samozřejmě neumí pracovat s našimi nově definovanými CustomPiny. Proto si ukážeme, jak vytvořit vlastní renderer pro každou platformu, kterou chceme podporovat – v našem případě Android a iOS. Nově vytvořené renderery budou ty základní o tuto funkcionalitu rozšiřovat.

Kompletní problematika vytváření vlastních pinů je podrobně popsána v [tomto oficiálním návodu](https://docs.microsoft.com/en-gb/xamarin/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin) a náš článek z něj bude částečně vycházet. Pokud chcete této problematice porozumět více do hloubky, doporučuji jej nastudovat. Dozvíte se v něm i další užitečné informace, které v tomto článku probrány nebudou. Například o upravování informačních náhledů u pinů, reagování na uživatelské vstupy prováděné na těchto náhledech apod.

## iOS

Pusťme se tedy do tvorby rendereru – vytvoříme třídu pojmenovanou CustomMapRenderer, která bude dědit od třídy MapRenderer. Přepíšeme její metodu OnElementChanged, která se volá při vytváření (a dalších změnách) Formsové komponenty a zde provedeme požadované změny. Nesmíme nad naši třídu zapomenout přidat atribut ExportRenderer, kterým frameworku řekneme, pro kterou Formsovou komponentu (ve společné části) se má tento renderer použít.

V rendereru máme přístup ke dvěma základním vlastnostem. Control a Element. Control reprezentuje nativní komponentu, Element společnou Formsovou komponentu.

```c#
public class CustomMapRenderer : Xamarin.Forms.Maps.iOS.MapRenderer
{
    private ObservableCollection<CustomPin> customPins;

    protected override void OnElementChanged(ElementChangedEventArgs<View> e)
    {
        base.OnElementChanged(e);
        if (e.OldElement != null)
        {
            ((CustomMap)e.OldElement).CustomPins.CollectionChanged -= FormsMap_PinsUpdated;
            var nativeMap = Control as MKMapView;
            if (nativeMap != null)
            {
                nativeMap.GetViewForAnnotation = null;
            }
        }

        if (e.NewElement != null)
        {
            ((CustomMap)e.NewElement).CustomPins.CollectionChanged += FormsMap_PinsUpdated;

            var nativeMap = Control as MKMapView;
            if (nativeMap != null)
            {
                nativeMap.GetViewForAnnotation = GetViewForAnnotation;
            }
        }
    }
…
}
```

Při vykreslování komponenty se zavolá metoda OnElementChanged, která má v předaném parametru ElementChangedEventArgs.NewElement uloženou instanci na aktuálně vykreslovanou (renderovanou) Formsovou komponentu. K události, která se vyvolává při změně její kolekce CustomPinů přiřadíme metodu FormsMap_PinsUpdated, která bude zajišťovat překreslení pinů na nativní mapě.

```c#
private void FormsMap_PinsUpdated(object sender, EventArgs e)
{
    var nativeMap = Control as MKMapView;
    if (nativeMap != null)
    {
        customPins = ((CustomMap)Element).CustomPins;
        foreach (var pin in customPins)
        {
            var annotation = new ColorPointAnnotation(pin);
            nativeMap.AddAnnotation(annotation);
        }
    }
}
```

Piny na iOSu se nazývají anotace. Dále tedy do vlastnosti GetViewForAnnotation u nativní mapy přiřadíme metodu GetViewForAnnotation, která na základě předaných informací v MkAnnotation vytvoří MkAnnotationView. Tato metoda zajišťuje, že se na mapě zobrazí naše barevné piny místo defaultních.

```c#
private MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    var anno = annotation as MKPointAnnotation;
    var customPin = GetCustomPin(anno);
    if (customPin == null)
        return null;

    annotationView = mapView.DequeueReusableAnnotation("pin");
    if (annotationView == null)
    {
        annotationView = new MKAnnotationView(annotation, "pin");
        ColorPointAnnotation colorPointAnnotation = annotation as ColorPointAnnotation;
        if (colorPointAnnotation != null)
        {
            annotationView.Image = GetPinImage(colorPointAnnotation.pinColor);
        }
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

Používáme zde další metody. GetCustomPin, která podle Gps pozice najde odpovídající CustomPin a vrátí ho. Dále GetPinImage, která vrací obrázek pinu obarvený požadovanou barvou. Využíváme i nový objekt ColorPointAnnotation zděděný od MkPoinAnnotation. Zmíněné části kódu vypadají následovně:

```c#
private CustomPin GetCustomPin(MKPointAnnotation annotation)
{
    if (annotation == null) return null;

    var position = new Gps()
    {
        Latitude = annotation.Coordinate.Latitude,
        Longitude = annotation.Coordinate.Longitude
    };

    foreach (var pin in customPins)
    {
        if (pin.Location.Equals(position))
        {
            return pin;
        }
    }
    return null;
}

private UIImage GetPinImage(UIColor color)
{
    UIImage image = UIImage.FromBundle("pin.png");
    UIImage image2 = UIImage.FromBundle("pin_contour.png");
    UIImage pinImage = null;

    UIGraphics.BeginImageContextWithOptions(image.Size, false, 0.0f);
    using (CGContext context = UIGraphics.GetCurrentContext())
    {
        context.TranslateCTM(0, image.Size.Height / 2);
        context.ScaleCTM(1.0f, -1.0f);

        var rect = new RectangleF(0, 0, (float)image.Size.Width / 2, (float)image.Size.Height / 2);

        context.SetBlendMode(CGBlendMode.Normal);
        context.DrawImage(rect, image.CGImage);

        context.SetBlendMode(CGBlendMode.SourceIn);
        context.SetFillColor(color.CGColor);
        context.FillRect(rect);

        context.SetBlendMode(CGBlendMode.Normal);
        context.DrawImage(rect, image2.CGImage);

        pinImage = UIGraphics.GetImageFromCurrentImageContext();>
        UIGraphics.EndImageContext();
    }
    return pinImage;
}

sealed class ColorPointAnnotation : MKPointAnnotation
{
    public UIColor pinColor;

    public ColorPointAnnotation(CustomPin pin)
    {
        SetCoordinate(new CLLocationCoordinate2D(pin.Location.Latitude, pin.Location.Longitude));
        Title = pin.Title;
        Subtitle = pin.Description;
        pinColor = pin.Color.ToUIColor();
        Init();
    }
}
```

Tímto je render pro iOS hotov

## Android

Renderer pro Android je o poznání jednodušší. Také se události na změnu kolekce pinů přiřazuje metoda, která je překreslí. Využijeme zde i překrytí metody OnElementPropertyChanged, která se volá při změně vlastnosti v Elementu (Formsové komponentě). Pokud se jedná o vlastnost VisibleRegion, tak také překreslíme piny. Celý renderer vypadá takto:

```c#
using System.ComponentModel;
using Xamarin.Forms;
using Xamarin.Forms.Maps;
using Xamarin.Forms.Platform.Android;
using Android.Gms.Maps.Model;
using FoodNearMe.Controls;
[assembly: ExportRenderer(typeof(FoodNearMe.Controls.CustomMap), typeof(FoodNearMe.Droid.Renderers.CustomMapRenderer)) ]

namespace FoodNearMe.Droid.Renderers
{
    public class CustomMapRenderer : Xamarin.Forms.Maps.Android.MapRenderer
    {
        private bool isDrawn = false;
        
        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);
            if (e.OldElement != null)
            {
                ((CustomMap)e.OldElement).CustomPins.CollectionChanged -= FormsMap_PinsUpdated;
            }
            if (e.NewElement != null)
            {
                ((CustomMap)e.NewElement).CustomPins.CollectionChanged += FormsMap_PinsUpdated;
            }
        }

        private void FormsMap_PinsUpdated(object sender, System.Collections.Specialized.NotifyCollectionChangedEventArgs e)
        { 
            this.CreateNativePins(this.Element as CustomMap);
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs e)                
        {
            base.OnElementPropertyChanged(sender, e);
            if(e.PropertyName == nameof(Element.VisibleRegion)  & !isDrawn)
            {
            this.CreateNativePins(this.Element as CustomMap);
            isDrawn = true;
            }
        }

        private void CreateNativePins(CustomMap customMap)
        {
            if (NativeMap != null)
            {
                NativeMap.Clear();
                if (customMap.CustomPins?.Count > 0)
                {
                    for (int i = 0; i < customMap.CustomPins.Count; i++)
                    {
                        var marker = new MarkerOptions();

                        marker.SetPosition(new LatLng(customMap.CustomPins [i ].Location.Latitude, customMap.CustomPins [i ].Location.Longitude));
                        marker.SetTitle(customMap.CustomPins [i ].Title);
                        marker.SetSnippet(customMap.CustomPins [i ].Description);

                        float[] hsv = new float[3];

                        Android.Graphics.Color.ColorToHSV(customMap.CustomPins [i ].Color.ToAndroid(), hsv);
                        var bitmap = BitmapDescriptorFactory.DefaultMarker(hsv [0 ]);
                        marker.SetIcon(bitmap);
                        NativeMap.AddMarker(marker);
                    }
                }
            }
        }
    }
}
```

Zde stojí za zmínku drobné omezení Androidu a to takové, že pro barvu pinu nemůžeme zvolit úplně libovolnou barvu. Zvolit můžeme pouze položku hue (odstín) z barevného modelu HSV (hue, saturation, value = odstín, sytost, jas). Ostatní položky jsou nastavené vždy na maximum. Například bílou nebo černou barvu tedy přístupnou nemáme.

![HsvModel](/assets/posts/2019-03-06-xamarin_aplikace_restaurace_v_okoli/HsvModel.png)

Tímto máme kompletně připravenou komponentu mapy a můžeme ji začít využívat.

## Zobrazení Mapy

Přidáme do projektu stránku, kterou pojmenujeme MapPage a do ní na celou obrazovku vložíme připravenou komponentu CustomMap.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:map="clr-namespace:FoodNearMe.Controls"
             x:Class="FoodNearMe.MapPage">
    <StackLayout HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand">
        <map:CustomMap x:Name="map" />
    </StackLayout>
</ContentPage>
```

V tuto chvíli by se nám měla mapa zobrazit. Naším požadavkem ale je zobrazit mapu v místě na kterém se nacházíme a s nějakým rozumným přiblížením.

## Získání polohy

V behind kódu MapPage tedy musíme nejdříve zjistit polohu uživatele. Ta se na každé platformě zjišťuje jiným způsobem. Máme ale dvě možnosti, jak k tomu přistoupit. Ta první je jednodušší, rychlejší, a tedy ve většině případů při běžném vývoji preferovanější. Pro získání polohy totiž existuje plugin ve formě NuGet balíčku Xam.Plugin.Geolocator od Jamese Montemagna (velice aktivní člen Xamarin komunity, autor mnoha návodů a pluginů). Plugin stačí nainstalovat do všech projektů v solution a začít používat ve společném projektu podle [této dokumentace](https://jamesmontemagno.github.io/GeolocatorPlugin/). Veškeré platformní záležitosti si už plugin obstará sám. Pro základní využívání tohoto pluginu je i tutoriál na youtube [zde](https://jamesmontemagno.github.io/GeolocatorPlugin/).

Druhou možností je využít mechanismus pojmenovaný DependencyService, který nám umožní ve společné části využívat jakoukoliv nativní funkcionalitu. Podrobně nastudovat si jej můžete [zde](https://docs.microsoft.com/en-gb/xamarin/xamarin-forms/app-fundamentals/dependency-service/). Abychom si DependencyServices představili, tak v naší aplikaci použijeme právě tuto možnost. Zjistíme díky tomu i to, jak se na jednotlivých platformách poloha získává nativně.

Ve společném projektu tedy vytvoříme rozhraní s definicí hlaviček metod, které budeme ve společném projektu používat a jejichž implementace bude v každém platformním projektu zvlášť.

```c#
public interface ILocation
{
    Task<Gps> GetLocation();

    Task<bool> RequestPermissions();
}
```

Díky metodě RequestPermissions se dozvíme, zda uživatel udělil oprávnění k přístupu k poloze telefonu a pokud ano, tak metoda GetLocation tuto polohu následně zjistí.

## Android implementace

V Android projektu vytvoříme třídu Location, která implementuje rozhraní ILocation a zaregistrujeme tuto implementaci do mechanismu DependencyServices pomocí atributu [assembly: Dependency(typeof(„Naše třída implementující rozhraní“))].

Pro práci s polohou na Androidu máme zase v Xamarin dokumentaci připraven [podrobný návod](https://docs.microsoft.com/en-us/xamarin/android/platform/maps-and-location/location). Tento návod je sice koncipován pro nativní Xamarin, ale i v platformním projektu Xamarin.Forms se dá vše z toho, někdy s drobnými úpravami, využít.

Pro získání polohy využijeme tzv. fused location provider. Jedná se o preferovaný způsob získávání pozice na Androidu. Tento provider vrátí pozici z aktuálně nejvýhodnějšího zdroje (GPS / WiFi + mobilní síť / poslední získaná pozice) podle požadavků, které můžeme specifikovat. Fused location provider je součástí Google Play Services. Do Android projektu musíme tedy nainstalovat potřebné NuGet balíčky - Xamarin.GooglePlayServices.Maps a Xamarin.GooglePlayServices.Location. Dále v telefonu musí být nainstalován apk balíček Google Play Services.

Správně bychom měli v kódu zjistit, zda v telefonu Google Play Services jsou nainstalované a aktuální a v opačném případě použít druhý způsob získávání pozice, který na nich závislý není. To v naší jednoduché ukázkové aplikaci dělat sice nebudeme, ale v případě potřeby je pro to popsán jednoduchý postup na výše zmíněném odkazu.

Zde je tedy naše Android implementace rozhraní ILocation.

```c#
using System.Threading.Tasks;
using FoodNearMe.Models;
using FoodNearMe.DependencyServices;
using Android.Gms.Location;
using Plugin.CurrentActivity;
using Plugin.Permissions;
using Plugin.Permissions.Abstractions;
using Xamarin.Forms;

[assembly: Dependency(typeof(FoodNearMe.Droid.DependencyServices.Location)) ]

namespace FoodNearMe.Droid.DependencyServices
{
    public class Location : ILocation
    {
        public async Task<Gps> GetLocation()
        {
            var fusedLocationProviderClient = LocationServices.GetFusedLocationProviderClient(CrossCurrentActivity.Current.Activity);

            Android.Locations.Location location = await fusedLocationProviderClient.GetLastLocationAsync();

            Gps output = new Gps();
            output.Latitude = location.Latitude;
            output.Longitude = location.Longitude;

            return output;
        }

        public async Task<bool> RequestPermissions()
        {
            var status = await CrossPermissions.Current.CheckPermissionStatusAsync(Permission.Location);
            if (status != PermissionStatus.Granted)
            {
                var results = await CrossPermissions.Current.RequestPermissionsAsync(new [ ]{ Permission.Location });
                status = results [Permission.Location ];
            }

            return status == PermissionStatus.Granted;
        }
    }
}
```

V metodě RequestPermissions zjišťujeme, zda máme pro získání pozice oprávnění. Pro zjednodušení používáme NuGet balíček Plugin.Permissions od Jamese Montemagna. Pokud oprávnění máme, využije se i druhá metoda GetLocation, kde získáme z LocationServices referenci na fused location providera. Musíme předat referenci na aktuální android Aktivity. Zde je rozdíl oproti nativnímu Xamarinu. Nejsme přímo v Aktivity, ale v naší třídě Location, a proto použijeme další plugin od Jamese. Tentokrát Plugin.CurrentActivity, který nám referenci na aktuální Activity (v Xamarin.Forms většinou jedinou hlavní MainActivity) poskytne. Plugin je potřeba v této hlavní MainActivity v OnCreate inicializovat příkazem

```c#
CrossCurrentActivity.Current.Init(this, bundle);
```

Pak už z fused location providera zjistíme naposled získanou pozici, což je pro naše účely naprosto dostačující řešení.

Vyžádání průběžných updatů pozice, specifikování požadavků na přesnost, časového intervalu updatů a reakce na události, které fused location provider může vyvolávat pro jednoduchost vynecháme. Je možné je nastudovat ve [zmíněném návodu](https://docs.microsoft.com/en-us/xamarin/android/platform/maps-and-location/location).

## iOS implementace

```c#
using System;
using Xamarin.Forms;
using FoodNearMe.Models;
using System.Threading.Tasks;
using CoreLocation;
using FoodNearMe.DependencyServices;

[assembly: Dependency(typeof(FoodNearMe.iOS.DependencyServices.Location)) ]
namespace FoodNearMe.iOS.DependencyServices
{
    public class Location : ILocation
    {
        private CLLocationManager locationManager = new CLLocationManager();
        
        public async Task GetLocation()
        {
            if (locationManager.Location == null)
            {
                var source = new TaskCompletionSource<CLLocation>();
                EventHandler<CLLocationsUpdatedEventArgs> handler = (sender, e) =>
                {
                    source.TrySetResult(locationManager.Location);
                };
                locationManager.LocationsUpdated += handler;
                locationManager.RequestLocation();             
                await source.Task;
                locationManager.LocationsUpdated -= handler;
            }
            return new Gps()
            {
                Latitude = locationManager.Location.Coordinate.Latitude,
                Longitude = locationManager.Location.Coordinate.Longitude,
            };
        }
        
        public async Task RequestPermissions()
        {
            if (CLLocationManager.Status == CLAuthorizationStatus.AuthorizedAlways || CLLocationManager.Status == CLAuthorizationStatus.AuthorizedWhenInUse)
            {
                return true;
            }
            if (CLLocationManager.Status == CLAuthorizationStatus.Denied || CLLocationManager.Status == CLAuthorizationStatus.Restricted)
            {
                return false;
            }

            var taskCompletionSource = new TaskCompletionSource<bool>()

            locationManager.AuthorizationChanged += (sender, args) =>
            {
                if (args.Status != CLAuthorizationStatus.NotDetermined) //událost se poprvé vždy volá s tímto příznakem, chceme ale počkat až na reakci uživatele
                {
                    switch (args.Status)
                    {
                        case CLAuthorizationStatus.AuthorizedAlways:
                        case CLAuthorizationStatus.AuthorizedWhenInUse:
                            taskCompletionSource.TrySetResult(true);
                            break;
                        case CLAuthorizationStatus.Denied:
                        case CLAuthorizationStatus.Restricted:
                            taskCompletionSource.TrySetResult(false);
                            break;
                        default:
                            taskCompletionSource.TrySetResult(false);
                            break;
                    }
                }
            };

            locationManager.RequestWhenInUseAuthorization();

            return await taskCompletionSource.Task;
        }
    }
}
```

Na iOSu pro práci s pozicí požíváme nativního CLLocationManagera.

V metodě RequestPermissions nejdříve zkontrolujeme, zda už máme z dřívějšího spuštění aplikace uděleno nebo zakázáno oprávnění. Pokud se ale aplikace spouští poprvé, tak příkazem locationManager.RequestWhenInUseAuthorization(); vyvoláme nativní dialog pro povolení nebo zakázání přístupu k poloze. Odchycením události locationManager.AuthorizationChanged čekáme na reakci uživatele po jejímž vyvolání nastavíme odpovídající výsledek do TaskCompletionSource, na který se čeká před návratem z metody.

V metodě GetLocation kontrolujeme, zda LocationManager již zná polohu a pokud ano, tak jí rovnou vrátíme. Pokud ne, tak vyžádáme její zjištění a čekáme na ni v události LocationUpdated, kde ji nastavíme zase do výsledku TaskCompletionSource, na který se čeká před vytvořením objektu Gps a jeho vrácením z metody.

## Načtení restaurací

Nyní, když máme získanou polohu, můžeme se dotázat Google API na restaurace v okolí.

Využívat budeme [Google Maps Platform](https://cloud.google.com/maps-platform/), konkrétně modul [Places API](https://developers.google.com/places/web-service/intro) a jeho funkci Nearby search, jejíž dokumentace se nachází [zde](https://developers.google.com/places/web-service/search). Pro její využívání je nejprve nutné si na [tomto odkazu](https://developers.google.com/places/web-service/get-api-key) obstarat vlastní API klíč.

V [dokumentaci](https://developers.google.com/places/web-service/search) si najdeme, v jakém formátu nám webová služba bude vracet data. Zkopírujeme celý ukázkový JSON, ve společném projektu vytvoříme adresářovou strukturu DataAccess – WebService – Models a vytvoříme model reprezentující odpověď webové služby. Pojmenujeme ho např. RestaurantOutput. Otevřeme ho a ve Visual Studiu klikneme na Edit -> Paste special -> Paste JSON As Classes a tím se celý C# model ze zkopírovaného JSONu vytvoří sám.

O složku výš do WebServices vytvoříme třídu RestaurantsWebRepository a napíšeme metodu GetRestaurants, ve které vytvoříme a odešleme dotaz na API, přečteme odpověď a pomocí pluginu Newtonsoft.Json ji deserializujeme do dříve vytvořeného objektu RestaurantOutput.

```c#
public async Task<List<Restaurant>> GetRestaurants(Gps location, int radiusInMeters, string googleApiKey)
{
    var client = new HttpClient();
    string url = $"https://maps.googleapis.com/maps/api/place/nearbysearch/json?location={location}&radius={radiusInMeters}&type=restaurant&key={googleApiKey}";
    var response = await client.GetAsync(url);
    string data = await response.Content.ReadAsStringAsync();
    var result = JsonConvert.DeserializeObject<RestaurantOutput>(data);
    return this.LoadRestaurants(result);
}
```

Tento deserializovaný objekt předáme metodě LoadRestaurants, která jej přeloží na náš seznam restaurací, se kterými budeme dále v aplikaci pracovat. 

Náš objekt Restaurant vypadá takto

```c#
namespace FoodNearMe.Models
{
    enum RestaurantQuality{ Bad, Good, VeryGood, Amazing}
    
    class Restaurant
    {
        public string DisplayName{ get; set; }
        public Gps Location{ get; set; }
        public RestaurantQuality Quality{ get; set; }
        public string Description{ get; set; }
    }
}
```

A používaná metoda LoadRestaurants takto

```c#
private List<Restaurant> LoadRestaurants(RestaurantOutput result)
{
    var output = new List<Restaurant>(result.results.Length);

    foreach (var restaurant in result.results)
    {
        output.Add(new Restaurant()
        {
            DisplayName = restaurant.name,
            Location = new Gps()
            {
                Latitude = restaurant.geometry.location.lat,
                Longitude = restaurant.geometry.location.lng,
            },
            Quality = LoadRestaurantQuality(restaurant.rating)
        });
    }

    return output;
}
```

Kvalita restaurace chodí z API v atributu rating a je to desetinné číslo v rozmezí 1 až 5. To rozdělíme na kategorie do enumu následujícím způsobem:

```c#
private RestaurantQuality LoadRestaurantQuality(float rating)
{
    if (rating > 4.5)
        return RestaurantQuality.Amazing;
    else if (rating > 4)
        return RestaurantQuality.VeryGood;
    else if (rating > 3)
        return RestaurantQuality.Good;
    else
        return RestaurantQuality.Bad;
}
```

Dále ve společném projektu vytvoříme složku Managers a do ní přidáme třídu RestaurantManager. Ta bude sloužit jako oddělující vrstva mezi View a WebRepository. Budeme v ní nastavovat parametry, které se pak předávají WebRepository pro vytvoření API dotazu. Do konstanty GoogleApiKey vložte vlastní API klíč pro používání Google Maps Platform. Manager je vhodné mít i z důvodu další rozšiřitelnosti, jako je například cachování, řazení výsledků apod.

```c#
class RestaurantManager
{
    public const int SearchRadius = 2000;
    private const string GoogleApiKey = "replace this with your api key";

    public async Task<List<Restaurant>> GetRestaurants(Gps location)
    {
        var webRepository = new RestaurantsWebRepository();
        var output = await webRepository.GetRestaurants(location, SearchRadius, GoogleApiKey);
        return output;
    }
}
```

## MapPage - code behind

Nyní už máme připraveny všechny dílčí části aplikace. Zbývá už jen ta nejhezčí část práce a tou je jejich využití v behind kódu MapPage.

V OnAppearing pomocí mechanismu DependencyService získáme referenci na implementaci ILocation. Vyžádáme si oprávnění k poloze a pokud ho získáme, zjistíme polohu zařízení a přesuneme zobrazení mapy na toto místo. Následně načteme seznam restaurací a překreslíme piny na mapě.

Odpovídající kód vypadá následovně:

```c#
using System;
using System.Threading.Tasks;
using System.Collections.Generic;
using FoodNearMe.Managers;
using FoodNearMe.Models;
using FoodNearMe.DependencyServices;
using Xamarin.Forms;
using Xamarin.Forms.Maps;

namespace FoodNearMe
{
    public partial class MapPage : ContentPage
    {
        public MapPage()
        {
            InitializeComponent();
        }

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            var locationManager = DependencyService.Get<ILocation>();
            Gps location = null;

            if (await locationManager.RequestPermissions())
            {
                location = await locationManager.GetLocation();
                this.map.IsShowingUser = true;
            }

            if (location != null)
            {
                this.map.MoveToRegion(MapSpan.FromCenterAndRadius((Position)location, new Distance(RestaurantManager.SearchRadius)));
                var restaurants = await this.LoadRestaurants(location);
                this.RefreshMapPins(restaurants);
            }
            else
            {
                await DisplayAlert("Poloha", "Aplikaci se nepodařilo získat vaší polohu", "OK");
            }
        }

        private async Task<List<Restaurant>> LoadRestaurants(Gps location)
        {
            try
            {
                var manager = new RestaurantManager();
                var restaurants = await manager.GetRestaurants(location);
                return restaurants;
            }
            catch (Exception)
            {
                await DisplayAlert("Restaurace", "Restaurace se nepodařilo načíst", "OK");
                return null;
            }
        }

        private void RefreshMapPins(List restaurants)
        {
            map.CustomPins.Clear();
            if (restaurants?.Count > 0)
            {
                foreach (var item in restaurants)
                {
                    var pin = new CustomPin()
                    {
                        Color = this.GetColorForType(item.Quality),
                        Description = item.Description,
                        Location = item.Location,
                        Title = item.DisplayName,
                    };
                    map.CustomPins.Add(pin);
                }
            }
        }

        private Color GetColorForType(RestaurantQuality type)
        {
            switch (type)
            {
                case RestaurantQuality.Bad:
                    return Color.Red;
                case RestaurantQuality.Good:
                    return Color.Orange;
                case RestaurantQuality.VeryGood:
                    return Color.Yellow;
                case RestaurantQuality.Amazing:
                   return Color.Green;
                default:
                  return Color.Gray;
            }
        }
    }
}
```

## Hotová aplikace

Pokud se vše povedlo a aplikaci úspěšně nahrajeme do telefonu, tak by měla na Androidu vypadat takto:

![DroidFinal](/assets/posts/2019-03-06-xamarin_aplikace_restaurace_v_okoli/DroidFinal.png)

A na iOSu takto:

![iOSFinal](/assets/posts/2019-03-06-xamarin_aplikace_restaurace_v_okoli/iOSFinal.png)

Pokud si kód nechcete psát celý sami, nebo chcete občas nahlédnout do kompletního řešení, je možné kompletní zdrojové kódy získat na [GitHubu](https://github.com/SkeletonSoftware/FoodNearMe) firmy Skeleton Software.