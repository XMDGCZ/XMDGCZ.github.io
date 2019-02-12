## První kroky s Xamarin.Forms

Založení Xamarin.Forms PCL projektu ve Visual Studio 2015.

### Založení projektu

 Xamarin.Forms projekt je k dispozici v předkonfigurovatelných šablonách a zakládá se stejně jako jakýkoliv jiný projekt ve Visual Studio. 

	![](images/newProject.png)

 <p>Pozn.*Pro ukázky je použito Visual Studio 2015 Update 3 běžící na Win 10 (Xamarin for Visual Studio)*</p> Šablony Xamarin.Forms jsou k nalezení ve **Visual C# -> Cross-platform**. K nalezení jsou zde šablony pro: * Nativní vývoj Portable i Shared * Xamarin.Forms Portable i Shared * XAML projekt * PCL knihovna * UI test Pokračovat budeme s **Blank App (Xamarin.Forms Portable)** 

	![](images/PCL.png)

 Po vytvoření nového projektu ho Visual Studio automaticky načte a zobrazí. 

![](images/createdProject.png)

 Vpravo je zobrazena struktura projektu rozdělená do několika částí/podprojektů. Aplikace se jmenuje ShowApp a stejně tak i jmenný prostor. Droid, iOS, UWP, Windows, WinPhone jsou projekty určené pro platformě závislý obsah. * Portable - PCL se sdíleným kódem mezi ostatními projekty * Droid - Android projekt * iOS - projekt pro všechna iOS zařízení * UWP - projekt pro celou UWP platformu (destktop Windows, XBox, novější WinPhone) * Windwos - Windows 8.1 * WinPhone - Windows Phone 8.1 Pokud nebude programátor pracovat s jedním nebo více projekty, je možné je ze Solution vymazat, což může zrychlit build. 

![](images/projectStructure.png)