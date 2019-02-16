---
layout: post
title: "Přístup k uložišti "
categories:
    - "xamarin"
    - "dataAccess"
sort_index: 0
author: "David Malý"
--- 


## Přístup k nativnímu úložišti


    Různé platformy mají různé API, které přistupují k souborovému systému. Používání nativních API je možné pouze pomocí nativního kódu.


### Dependency Injection


    Pomocí Dependency injection je možné volat z PCL projektu kód projektu jiného např. z PCL nativní Android kód.



    Myšlenka Dependency injection stojí na propojení Interface a třídy, která ho realizuje. Veškerý sdílený kód v PCL projektu je programován proti Interface. Dependency service se stará o záměnu Interface za třídu, která ho skutečně realizuje. V případě Xamarin je Interface nahrazeno třídu obsahující nativní kód a je uložena v projektu příslušícímu dané platformě. Díky uložení v podprojektu dané platformy, má tato třída přístup ke všem nativním API platformy.


#### Nativní ukládání a načítání textu ze souboru


    Následující příklad ukazuje, jak přistoupit k nativnímu API pomocí Dependency service. Demonstruje práci se souborem, který je uložen mimo projekt (na cílovém zařízení).


##### PCL Interface


    V PCL projektu vytvoříme Interface, proti kterému budeme programovat zbytek sdíleného kódu (kód v PCL projektu).



    Interface pro ukládání a načítání obsahu souboru.


```

public interface ISaveAndLoad {
    void SaveText (string filename, string text);
    string LoadText (string filename);
}

```


    V PCL sdíleném kódu zavoláme Dependency service a zaměníme Interface za jeho implementaci. Dependency service funguje tak, že na základě Interface najde třídu, která ho implementuje za běhu aplikace. Třída musí být zaregistrovaná do Dependendcy service.


```

string textFromFile = DependencyService.Get().LoadText("temp.txt");

```


    Třída s nativním kódem je uložena v podprojektu dané platformy.



    Ukázka iOS nativního kódu.


```

[assembly: Dependency (typeof (SaveAndLoad))]
namespace WorkingWithFiles {
    public class SaveAndLoad : ISaveAndLoad {
        public void SaveText (string filename, string text) {
            var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var filePath = Path.Combine (documentsPath, filename);
            System.IO.File.WriteAllText (filePath, text);
        }
        public string LoadText (string filename) {
            var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var filePath = Path.Combine (documentsPath, filename);
            return System.IO.File.ReadAllText (filePath);
        }
    }
}

```


    Registrace do Dependency service je nad Namespace.


```

[assembly: Dependency (typeof (SaveAndLoad))]

```


    Výsledná aplikace je napsaná proti Interface ISaveAndLoad. V případě, že je potřeba zavolat nativní kód, Dependendcy Service automaticky najde implementaci Interface a zavolá nativní kód za běhu aplikace.

[Working with files](https://developer.xamarin.com/guides/xamarin-forms/working-with/files/)



[Dependency Service](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/introduction/)
### PCLStorage plugin


    Jakoukoliv nativní funkčnost je možné implementovat pomocí Dependency service. To vyžaduje napsání vlastního Interface, nativního kódu a celkového propojení. V některých případech je mnohem jednodušší využít Nuget balíčků, které se nemusejí debugovat, vychází na ně aktualizace a celkově zkracují čas vývoje.



    Pro přístup k nativnímu úložišti zařízení je možné využít [PCLStorage](https://components.xamarin.com/gettingstarted/pclstorage) plugin



    Celý kód pro práci se souborem se pak zmenší na:


```

IFolder rootFolder = FileSystem.Current.LocalStorage;

// create a folder, if one does not exist already
IFolder folder = await rootFolder.CreateFolderAsync("MySubFolder", CreationCollisionOption.OpenIfExists);

// create a file, overwriting any existing file
IFile file = await folder.CreateFileAsync("MyFile.txt", CreationCollisionOption.ReplaceExisting);

// populate the file with some text
await file.WriteAllTextAsync("Sample Text...");

```
[Zdrojový projekt - Kontakty + JSON](https://github.com/malyda/Xamarin-ListView)