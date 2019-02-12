## Multiplatfomní barcodová čtečka

Jakub Bednář

Všechen spotřební materiál v našem okolí má dnes nějaké identifikační číslo doplněné čárovým nebo QR kódem. Tento článek popisuje jak jednoduše získat číslo z toho kódu do multiplatformní aplikace.

### ZXing.Net.Mobile

ZXing.Net.Mobile je PCL knihovna určená pro Android, iOS a Win. platformy. Stačí přidat [ZXing](https://components.xamarin.com/view/zxing.net.mobile) nuget do všech solutionu.

Pro zobrazení fotoaparátu čtečky stačí vložit následující kód který otevře novou Page se skenerem (upravené zobrazení fotoaparátu)

    <code class="language-C# ">var scanPage = new ZXingScannerPage();
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
    Navigation.PushAsync(scanPage);</code>

#### Specifické úpravy pro Android

V droid solution je třeba vložit řádek inicializace **Droid -> MainActivity.cs -> OnCreate() -> vložit**. Inicializaci je nutné umístit až za **global::Xamarin.Forms.Forms.Init(this, bundle);**

    <code class="language-C# ">ZXing.Net.Mobile.Forms.Android.Platform.Init();
    </code>

#### Specifické úpravy pro iOS

V iOS solution je třeba vložit řádek inicializace **iOS -> AppDelegate.cs -> FinishedLaunching() -> vložit** . Inicializaci je nutné umístit až za **global::Xamarin.Forms.Forms.Init();**

    <code class="language-C# ">ZXing.Net.Mobile.Forms.iOS.Platform.Init();
    </code>

Dále je nutné vytvořit oprávnění na používaní fotoaparátu. Toto oprávnění se vytváří v **iOS -> Info.plist -> Zdroj(dole)**. Pod výpisem již zadáních klíčů je **Přidat novou položku** když máme vytvořenou novou položku namísto **Custom Property** nastavíme **Privacy - Camera Usage Description**. Jako Typ nastavíme Řetězec a namísto hodnoty vložíme hlášku tázající se na možnost použít fotoaparát uživatele.

Po vyplnění by měl klíč vypadat takto:

<table class="table-basic">
	<tr class="mdl-color--primary">
		<th>Vlastnost</th>
		<th>Typ</th>
		<th>Hodnota</th>
	</tr>
	<tr>
		<td>Privacy - Camera Usage Description</td>
		<td>Řetězec</td>
		<td>Can we use your camera?</td>
	</tr>
</table>

#### Specifické úpravy pro UWP

V UWP solution je třeba vložit řádek inicializace **UWP -> (Hlavní stranka) -> vložit do konstruktoru**

    <code class="language-C# ">ZXing.Net.Mobile.Forms.WindowsUniversal.ZXingScannerViewRenderer.Init();
    </code>

#### Nastavení ZXing

V případě, že potřebuje upravit nějakou vlastnost čtečky, je zde možnost úpravy vytvořením třídy "options".

    <code class="language-C# ">    //noví objekt nastaveni
    MobileBarcodeScanningOptions options = new MobileBarcodeScanningOptions();
    //list povolených formátú pro skenovaní
    options.PossibleFormats = new List<zxing.barcodeformat>() {
        ZXing.BarcodeFormat.CODE_128
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
    Navigation.PushAsync(scanPage);</zxing.barcodeformat></code>

Pro zprávnou funkčnost musíme mít nalinkované následující knihovny:

    <code class="language-C# ">using ZXing.Net.Mobile.Forms;
    using ZXing.Mobile;
    </code>

Příklad ke stažení [ZDE](https://github.com/malyda/Xamarin-BarCodeScanner)