---
layout: post
title: "Čtečka čárových kódů "
categories:
    - "xamarin"
    - "tools"
sort_index: 0
author: "David Malý"
--- 


## Multiplatfomní barcodová čtečka


Jakub Bednář



Všechen spotřební materiál v našem okolí má dnes nějaké identifikační číslo doplněné čárovým nebo QR kódem. Tento článek popisuje jak jednoduše získat číslo z toho kódu do multiplatformní aplikace.


### ZXing.Net.Mobile


ZXing.Net.Mobile je PCL knihovna určená pro Android, iOS a Win. platformy. Stačí přidat [ZXing](https://components.xamarin.com/view/zxing.net.mobile) nuget do všech solutionu.



Pro zobrazení fotoaparátu čtečky stačí vložit následující kód který otevře novou Page se skenerem (upravené zobrazení fotoaparátu)


```

var scanPage = new ZXingScannerPage();
//Když zachití skenovaný kód vratí se o stránku zpět 
//kde vypíše DisplayAlert hlašku s naskenovaným kódem
scanPage.OnScanResult += (result) => {
    // Přestane skenovat
    scanPage.IsScanning = false;
    // Vrátí se o stránku zpet
    Device.BeginInvokeOnMainThread(() => {
        Navigation.PopAsync();
        DisplayAlert("Scanned Barcode", result.Text, "OK");
    });
};
// Otevíra skener (fotoaparát)
Navigation.PushAsync(scanPage);

```

#### Specifické úpravy pro Android


V droid solution je třeba vložit řádek inicializace **Droid -> MainActivity.cs -> OnCreate() -> vložit**. Inicializaci je nutné umístit až za **global::Xamarin.Forms.Forms.Init(this, bundle);**


```

ZXing.Net.Mobile.Forms.Android.Platform.Init();

```

#### Specifické úpravy pro iOS


V iOS solution je třeba vložit řádek inicializace **iOS -> AppDelegate.cs -> FinishedLaunching() -> vložit** . Inicializaci je nutné umístit až za **global::Xamarin.Forms.Forms.Init();**


```

ZXing.Net.Mobile.Forms.iOS.Platform.Init();

```


Dále je nutné vytvořit oprávnění na používaní fotoaparátu. Toto oprávnění se vytváří v **iOS -> Info.plist -> Zdroj(dole)**. Pod výpisem již zadáních klíčů je **Přidat novou položku** když máme vytvořenou novou položku namísto **Custom Property** nastavíme **Privacy - Camera Usage Description**. Jako Typ nastavíme Řetězec a namísto hodnoty vložíme hlášku tázající se na možnost použít fotoaparát uživatele.



Po vyplnění by měl klíč vypadat takto:



| Vlastnost | Typ | Hodnota |
| --- | --- | --- |
| Privacy - Camera Usage Description | Řetězec | Can we use your camera? |


#### Specifické úpravy pro UWP


V UWP solution je třeba vložit řádek inicializace **UWP -> (Hlavní stranka) -> vložit do konstruktoru**


```

ZXing.Net.Mobile.Forms.WindowsUniversal.ZXingScannerViewRenderer.Init();

```

#### Nastavení ZXing


V případě, že potřebuje upravit nějakou vlastnost čtečky, je zde možnost úpravy vytvořením třídy "options".


```

	//noví objekt nastaveni
MobileBarcodeScanningOptions options = new MobileBarcodeScanningOptions();
//list povolených formátú pro skenovaní
options.PossibleFormats = new List() {ZXing.BarcodeFormat.CODE_128
};
//další nastavení
options.AutoRotate = false;
options.TryHarder = true;

//noví objekt skenru
var scanPage = new ZXingScannerPage(options);

scanPage.OnScanResult += (result) => {
   	//Konec skenování
    scanPage.IsScanning = false;

    // navrat na uvodní stranku a vypis naskenované hodnoty
    Device.BeginInvokeOnMainThread(() => {
        Navigation.PopAsync();
        DisplayAlert("Scanned Barcode", result.Text, "OK");
    });
};

// Odevření skenru
Navigation.PushAsync(scanPage);
```


Pro zprávnou funkčnost musíme mít nalinkované následující knihovny:


```

using ZXing.Net.Mobile.Forms;
using ZXing.Mobile;

```


Příklad ke stažení [ZDE](https://github.com/malyda/Xamarin-BarCodeScanner)

