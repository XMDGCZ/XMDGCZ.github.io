---
layout: post
title: "graphElements.php "
categories:
    -"xamarin"
    -"xamarin"
author: "David Malý"
--- 


## Grafické elementy v Xamarin Forms


Nedílnou součástí každé aplikace jsou ovládací prvky, které ji umožní ovládat. Ovládacími prvky můžeme chápat výzvy aplikace k činosti uživatele např. Stiskněte libovolnou klávesu, nebo pasivní prvky např. tlačítka, či agresivní např. vyskakovací oznámení. Xamarin.Forms nabízí většinu ovládacích prvků, které jsou k nalezení na nejpoužívanějších platformách (tlačítka, labely, přepínače atd.).<br>


### Grafické elementy


Xamarin.Forms využívá grafických elementů, které jsou podobné na každé platformě. Mezi takové prvky lze zařadit tlačítka, textová pole, přepínače, layouty, navigaci i složitější např. Mapy či WebView. Speciální kategorií pro rozšíření grafických prvků jsou animace.<br>



Každý grafický element v Xamarin.Forms je reprezentován odpovídající třídou.<br>


```

Button button = new Button
{Text = "Stiskni"
};

```

```

Label label = new Label
{Text = "Oznamovací text"
};

```


Také mohou být zapsány v XAML a poté zpracovávany pomocí code-behind.


```

<Button Text = "Stiskni" />

```

```

<Label Text="Oznamovací text"/>

```


Tyto elemetny budou vykresleny vždy s nativním nastavením dané platformy, ale zpracovávají se pomocí sdíleného kódu stejně.<br>


### Různá nastavení pro různé platformy


Xamarin.Forms umožňuje měnit nastavení dle platformy, na které aplikace zrovna běží. Stejně jako u zápisu grafických elementů je možné toto nastevení měnit v C# i v XAML.<br>



Pro nastavení v C# použijeme Device.OnPlatform(iOS,Android,WinPhone), kde na místě jmen platforem můžeme použít různé hodnoty. Při změně velikosti fontu dle platformy v C# <br>


```

FontSize = Device.OnPlatform(15, 13, 14);

```


Pro stejný efekt, ale v XAML použijeme následující kód.<br>


```

<Button.FontSize>
    <OnPlatform x:TypeArguments="x:double" iOS="15" Android="13" WinPhone="14" />
</Button.FontSize>

```


Záleží pouze na programátorovi, jestli upřednostní code-behind, nebo XAML přístup. Doporučuje se XAML.<br>

[Code-behind or XAML?](http://stackoverflow.com/questions/1002604/xaml-or-c-sharp-code-behind)
### Xamarin.Forms native UI
![xamarin-form-cheat](http://cdn1.xamarin.com/webimages/images/infographics/xamarin-infographic-top-mobile-app-controls.png )