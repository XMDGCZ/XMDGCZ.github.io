---
layout: post
title: "Xamarin: Aplikace Azure Chat"
categories:
            - "HowTo"
author: "Kateřina Lebedová"
---

Jak vytvořit mobilní aplikaci pro chat s využitím Xamarin.Forms, .NET Standard a služeb Microsoft Azure. Pokud je pro vás technologie Xamarin.Forms nová, podívejte se na [teoretický úvod](https://www.skeleton.cz/uvod-do-xamarin-forms) nebo návod k vytvoření první [aplikace Občanka](https://www.skeleton.cz/xamarin-aplikace-obcanka) nacházející se na [blogu](https://www.skeleton.cz/blog) firmy Skeleton Software. Tento článek na ně navazuje.

## Úvod

Článek popisuje vývoj jednoduché aplikace pro chatování. Obsahuje stránku pro zadání jména uživatele, seznam všech uživatelů a stránku se zprávami. Na této aplikaci a v článku je demonstrováno vytvoření Xamarin.Forms .NET Standard aplikace pro Android a iOS napojené na služby Microsoft Azure, práce s ListView a DataTemplateSelectory a použití vlastních komponent a fontů.

## Vytvoření služeb a aplikace

Výsledná aplikace bude komunikovat s webovými službami běžícími na Microsoft Azure. Začneme tedy založením backendu. Je potřeba mít vytvořený účet na Azure. Pro tuto aplikaci, která slouží jen na ukázku, postačí cenová úroveň Free.

V portálu [Microsoft Azure](https://portal.azure.com/) ve vyhledávacím poli v horní části vyhledáme „mobile app“ a z výsledků vybereme Mobile Apps Quickstart. Vyplníme požadované informace a dáme Vytvořit. Nasazení služby chvíli trvá, při dokončení vyskočí notifikace. V záložce Všechny prostředky se nyní zobrazuje i naše služba.

![MicrosoftAzure](/assets/posts/2019-03-07-xamarin_aplikace_azure_chat/MicrosoftAzure.png)

V menu vytvořené App Service v kategorii Mobilní vybereme položku Jednoduché tabulky. Na naší aplikaci nyní postačí pouze předkonfigurovaná SQLite databáze. Ta nyní obsahuje ukázkovou tabulku TodoItem, kterou můžeme smazat. Pro jednoduchý chat vytvoříme tabulky person a message. Sloupce id, createdAt, updatedAt, version a deleted jsou součástí každé nové tabulky a jsou potřeba pro správnou funkčnost jednoduchých tabulek. U uživatele si budeme udržovat pouze jeho jméno (id) a u zprávy odesílatele, příjemce a obsah zprávy.

![TableStructure](/assets/posts/2019-03-07-xamarin_aplikace_azure_chat/TableStructure.png)

Vrátíme se zpět na službu a v menu zvolíme Rychlý start, kde si následně vybereme aplikaci pro Xamarin.Forms. Připojení máme již připravené, tak můžeme zvolit pod bodem č. 3 možnost stáhnout novou aplikaci, která je již nakonfigurována pro komunikaci s vytvořeným backendem.

![NewApplication](/assets/posts/2019-03-07-xamarin_aplikace_azure_chat/NewApplication.png)

Stažené solution otevřeme ve Visual Studiu. Na ukázku jsou již připravené třídy TodoItem a TodoItemManager. Podle jejich vzoru vytvoříme modely Person a Message a k nim managery PersonManager a MessageManager. Modely Person a Message představují strukturu stejnojmenných tabulek v databázi. Jejich vlastnosti jsou navázané na sloupce tabulky pomocí atributů JsonProperty. Dále pro práci s aktuálně přihlášeným uživatelem vytvoříme třídu UserManager. Všechny managery jsou navržené dle vzoru singleton.

```c#
public class Message
{
    [JsonProperty(PropertyName = "id")]
    public string Id {get; set; }
    
    [JsonProperty(PropertyName = "messageContent")]
    public string MessageContent {get; set; }
    
    [JsonProperty(PropertyName = "sender")]
    public string Sender {get; set; }
    
    [JsonProperty(PropertyName = "recipient")]
    public string Recipient {get; set; }
    
    [CreatedAt]
    public DateTime Date {get; set; }
    
    [Version]
    public string Version {get; set; }
}
```

## Managery

MessageManager a PersonManager pracují s instancí MobileServiceClient, které se předá adresa webových služeb. Následně tato třída poskytuje generickou metodu GetTable. Té se předá typ tabulky (náš model Person nebo Message). Nad vráceným objektem IMobileServiceTable můžeme používat LINQ výrazy a získávat tak vyfiltrovaná data z databáze.

```c#
private MessageManager()
{
    this.client = new MobileServiceClient(Constants.ApplicationURL);
    this.messageTable = client.GetTable<Message>();
}
```

PersonManager obsahuje metody pro získání detailu osoby (GetPersonAsync), seznamu všech osob (GetPeopleAsync) a uložení nové osoby do databáze (SavePersonAsync). MessageManager má metody pro odeslání zprávy (SaveMessageAsync) a zjištění, zda jsou dostupné novější zprávy pro konkrétního příjemce (IsNewMessageAsync). Dále, abychom mohli realizovat dynamické načítání zpráv, si připravíme metodu GetMessagesAsync, která přijímá v parametru příjemce, poslední přijatou zprávu, příznak, zda chceme načítat starší zprávy nebo novější a počet zpráv k načtení. Díky tomu budeme moct dynamicky při scrollování nahoru načítat starší zprávy nebo dole zobrazovat nově přijaté. UserManager umožňuje přihlášení uživatele pomocí metody SavePersonAsync v PersonManageru a přístup k jeho detailu.

```c#
/// <summary>
/// Zjistí, zda jsou k dispozici nové zprávy
/// </summary>
/// <param name="recipient">username druhé osoby v konverzaci</param>
/// <param name="lastMessage">poslední zpráva (ať už přijatá, nebo odeslaná)</param>
/// <returns></returns>
public async Task<bool> IsNewMessageAsync(string recipient, Message lastMessage)
{
    try
    {
        bool isNew = false;
        var sender = UserManager.Instance.CurrentUser.Username;
        
        var items = await messageTable
            .Where(x => (sender == x.Sender && recipient == x.Recipient) ||
                           (sender == x.Recipient && recipient == x.Sender))
            .Where(x => x.Date > lastMessage.Date)
            .ToEnumerableAsync();
        
        if (items != null && items.Any())
        {
            return true;
        }
        else
        {
            return false;
        }
    }
    catch (MobileServiceInvalidOperationException msioe)
    {
        Debug.WriteLine($"Invalid sync operation: {msioe.Message}");
    }
    catch (Exception e)
    {
        Debug.WriteLine($"Sync error: {e.Message}");
    }
    return false;
}
```

## Vlastní font

V aplikaci budeme později chtít použít ikony např. pro tlačítko odeslání zprávy. Jedním ze způsobů je využití ikonového fontu. Není pak potřeba pracovat s ikonou jako s obrázkem a změna velikosti se provede pouze změnou velikosti fontu bez potřeby ukládání různých rozlišení obrázku.

Nejprve ve společném projektu vytvoříme ve složce Controls třídu IconLabel zděděnou od třídy Xamarin.Forms.Label. Třída bude jinak prázdná, slouží pouze pro navázání rendereru na komponentu, která se pak v XAML použije na místě, kde ikonu chceme, s názvem souboru s fontem bez přípony vyplněným ve vlastnosti FontFamily.

V Androidím projektu poté do složky Renderers přidáme IconLabelRenderer a navážeme ho na naši komponentu pomocí ExportRenderer atributu. Přepíšeme metodu OnElementChanged a v ní načteme požadovaný font podle FontFamily a nastavíme jej komponentě. Dále je potřeba do projektu do složky Assets/fonts přidat požadovaný font.

```c#
[assembly: Xamarin.Forms.ExportRenderer(typeof(AzureChat.Controls.IconLabel), typeof(IconLabelRenderer))]
namespace AzureChat.Droid.Renderers
{
    class IconLabelRenderer : LabelRenderer
    {
        public IconLabelRenderer(Context context) : base(context)
        {        
        }

        protected override void OnElementChanged(ElementChangedEventArgs    <Label> e)
        {
            base.OnElementChanged(e);
            var typeface = Typeface.CreateFromAsset(this.Context.Assets, System.IO.Path.Combine("fonts", $"{Element.FontFamily}.ttf"));
            Control.Typeface = typeface;
        }
    }
}
```        

IconLabelRenderer stejným způsobem přidáme i do projektu pro iOS. Na iOS se ale font nevyhledává dle pojmenování souboru jako na Androidu, ale je potřeba znát přímo název fontu. Proto v rendereru přímo určujeme název fontu z FontFamily, nahrajeme odpovídající font a ten komponentě v OnElementChanged nastavíme. V iOS projektu se náš font přidá do složky Resources/fonts, ale navíc je potřeba do souboru Info.plist přidat položku „Fonts provided by application“ a do pole přidat cestu k fontu – v našem případě fonts/materialFont.ttf.

```xml
<key>UIAppFonts</key>
<array>
    <string>fonts/materialFont.ttf</string>
</array>
```      

## Stránky aplikace

**Přihlášení**

Jako první se uživateli vždy zobrazí stránka s textovým polem a tlačítkem pro zadání a potvrzení přezdívky uživatele. K tomuto účelu vytvoříme LoginPage a k ní LoginViewModel. Při stisku tlačítka se spustí Command, který vyvolá metodu Login v UserManageru. Při úspěšném uložení uživatele do databáze se MainPage nastaví na seznam všech uživatelů – PeopleListPage, v případě chyby se vyvolá dialog.

![Login](/assets/posts/2019-03-07-xamarin_aplikace_azure_chat/Login.png)

```c#
// při úspěšném přihlášení se MainPage nastaví na PeopleListPage, aby nebyla povolená navigace zpět
if (result)
{
    App.Current.MainPage = new NavigationPage(new PeopleListPage());
}
else
{
    await App.Current.MainPage.DisplayAlert("Error", "User could not be logged in.", "OK");
}
```

**Seznam uživatelů**

![Users](/assets/posts/2019-03-07-xamarin_aplikace_azure_chat/Users.png)

PeopleListPage má na starost zobrazení všech uživatelů (metoda GetPeopleAsync ve třídě PersonManager). Aby seznam vypadal trochu zajímavěji, vytvoříme pro položky ListView vlastní ItemTemplate. Ten bude obsahovat jméno uživatele a vpravo ikonu šipky, pro kterou použijeme naši vytvořenou komponentu IconLabel s fontem Material Icons.

Vlastní vzhled položek definujeme tak, že ListView nastavíme vlastnost ItemTemplate. Do ní se vloží DataTemplate, který obsahuje element ViewCell (nebo element od něj zděděný). Následně vytvoříme šablonu pro položku stejným způsobem jako jsme zvyklí z vytváření stránek.

```xml
<ListView.ItemTemplate>
    <DataTemplate>
        <ViewCell>
            <Grid Padding="12" ColumnSpacing="0" RowSpacing="0">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*"/>
                    <ColumnDefinition Width="Auto"/>
                </Grid.ColumnDefinitions>

                <Label Grid.Column="0" Text="{Binding Username}" VerticalOptions="Center" LineBreakMode="TailTruncation"/>
                <controls:IconLabel Grid.Column="1" Grid.Row="0" FontSize="20" Margin="5" FontFamily="materialFont" Text=";" Style="{StaticResource IconLabelStyle}"/>
            </Grid>
        </ViewCell>
    </DataTemplate>
</ListView.ItemTemplate>
```

Při spuštění aplikace na iOS si můžeme všimnout, že ListView zobrazuje prázdné řádky s oddělovači položek. Tento estetický nedostatek se dá vyřešit pomocí efektu aplikovaný na ListView. Efekty umožňují přizpůsobit nativní komponenty na každé platformě zvlášť a typicky se používají pro malé úpravy vlastností a změny vzhledu. Čeho lze dosáhnout efekty, to je možné realizovat i pomocí rendereru, který je však určen spíše na větší změny, kdy je potřeba přepsat chování komponent nebo zvolit vlastní nativní komponentu, která se při renderování použije.

Ve společném projektu ve složce Effects vytvoříme třídu EmptyFooterEffect, která dědí od RoutingEffect. Třída obsahuje pouze konstruktor, který předá bázovému konstruktoru identifikátor efektu ve tvaru ResolutionGroupName.EffectName. ResolutionGroupName je většinou název firmy, případně namespace projektu. EffectName je již konkrétní název efektu v rámci ResolutionGroupName. V iOS také do složky Effects přidáme třídu EmptyFooterEffect, ale tentokrát zděděnou od PlatformEffect, což už představuje efekt na konkrétní platformě. Ve třídě přepíšeme metody OnAttached a OnDetached. V OnAttached metodě zkontrolujeme, zda je použitá komponenta typu UITableView a pokud ano, tak její TableFooterView nastavíme na nové prázdné UIView. Tím se zbavíme prázdných řádků. Efekt je stejně jako renderer potřeba exportovat. V celém projektu se jednou definuje atribut ResolutionGroupName a každý efekt poté použijeme pomocí atributu ExportEffect.

```c#
[assembly: ResolutionGroupName("chatdevdays17")]
[assembly: ExportEffect(typeof(EmptyFooterEffect), "EmptyFooterEffect")]
namespace AzureChat.iOS.Effects
{
    /// <summary>
    /// Ošetřuje, aby se v ListView nezobrazovaly prázdné řádky
    /// </summary>
    public class EmptyFooterEffect : PlatformEffect
    {
        /// <summary>
        /// Volá se, když je komponenta vytvořena
        /// </summary>
        protected override void OnAttached()
        {
            if (this.Control is UITableView)
            {
                (this.Control as UITableView).TableFooterView = new UIView();
            }
            else
            {
                throw new ArgumentException($"Effect EmptyFooterEffect don't support \"{this.Control}\" type");
            }
        }
        
        /// <summary>
        /// Volá se, když už komponenta není zapotřebí
        /// </summary>
        protected override void OnDetached()
        {
        }
    }
}
```

V Androidím projektu není efekt potřeba implementovat, v případě chybějícího efektu se nic nestane. Efekt následně použijeme tak, že v XAML definici ListView přiřadíme do jeho vlastnosti Effects náš vytvořený efekt.

```xml
<ListView VerticalOptions="FillAndExpand" IsRefreshing="{Binding IsLoading, Mode=TwoWay}" HasUnevenRows="True" HorizontalOptions="FillAndExpand" ItemsSource="{Binding Items}" IsPullToRefreshEnabled="True" RefreshCommand="{Binding RefreshCommand}" CachingStrategy="RecycleElement" SelectedItem="{Binding SelectedItem, Mode=TwoWay}">
    <ListView.Effects>
        <effects:EmptyFooterEffect/>
    </ListView.Effects>        
</ListView>
```

Načtení dat pro ListView provádíme v PeopleListViewModelu, kde zároveň vytvoříme vlastnost IsLoading. Ta bude značit, zda aplikace zrovna načítá data. ListView má vlastní indikátor aktivity – vlastnost IsBusy, který můžeme nabindovat na naši vlastnost. Dále umožňuje funkcionalitu Pull-To-Refresh. Abychom ji mohli použít, nastavíme vlastnost ListView IsPullToRefreshEnabled na true a ve viewmodelu vytvoříme command načítající data, který navážeme na RefreshCommand. Díky tomu zajistíme, že když uživatel zatáhne ListView směrem dolů, tak přenačteme data a aplikace to dá najevo indikátorem.

To nás přivádí k problému, který se v Xamarin.Forms občas projeví – pokud má aplikace více práce v hlavním vlákně, tak se někdy stane, že se nějaká činnost v hlavním vlákně neprovede. Příklad: po přechodu na stránku PeopleListPage na Androidu se indikátor činnosti nikdy nezastaví, i přestože je IsLoading nastaveno na false. Je to způsobeno tím, že IsLoading se nastaví těsně poté, co se nahraje stažený seznam uživatelů do kolekce Items. List je v hlavním vlákně zaneprázdněn zobrazením položek a indikátor neschová. Pokud na tento problém narazíme, dá se vyřešit awaitováním metody Task.Yield() mezi přiřazením kolekce a nastavením vlastnosti IsLoading. Tato metoda zjednodušeně zaručí, že se na chvíli pozastaví provádění naší metody a dá přednost hlavnímu vláknu, aby dokončilo svou práci – tedy načtení a zobrazení kolekce v ListView, a až poté pokračuje nastavením IsLoading.

```c#
/// <summary>
/// Provede načtení dat
/// </summary>
/// <returns></returns>
public async Task LoadData()
{
    this.IsLoading = true;
    var manager = PersonManager.DefaultManager;
    var result = await manager.GetPeopleAsync();
    
    if (result == null)
    {
        await App.Current.MainPage.DisplayAlert("Error", "Could not load people.", "OK");
    }
    else
    {
        this.Items = result;
    }
    
    await Task.Yield(); // potřeba počkat na dodělání práce v hlavním vlákně (zobrazení položek v ListView), aby se poté správně provedlo nastavení IsLoading; bez toho se točí kolečko do nekonečna
    
    this.IsLoading = false;
}
```

**Chat**

![Chat](/assets/posts/2019-03-07-xamarin_aplikace_azure_chat/Chat.png)

Po rozkliknutí uživatele v ListView navigujeme na stránku ChatPage. Od této stránky očekáváme, aby nám zobrazovala přijaté a odeslané zprávy graficky odlišené, uměla dynamicky načítat historii zpráv při scrollování nahoru, pravidelně kontrolovala dostupnost nových zpráv a umožňovala odeslání nové zprávy.

Nejprve si připravíme stránku, kde bude jedno ListView a dole Entry a náš IconLabel s ikonou pro odeslání zprávy. Následně si do ResourceDictionary definujeme dvakrát DataTemplate, jeden pro příchozí zprávy a druhý pro odchozí zprávy. V našem případě máme odchozí zprávy zarovnané doprava vybarvené primární barvou a příchozí zprávy vlevo bílé ohraničené primární barvou. Pod každou zprávou je navíc zobrazen datum a čas odeslání.

```xml
<!-- template odeslané zprávy -->
<DataTemplate x:Key="sentMessageTemplate">
    <ViewCell>
        <StackLayout Orientation="Vertical" HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand">
            <Frame OutlineColor="{StaticResource PrimaryColor}" BackgroundColor="{StaticResource PrimaryColor}" HasShadow="False" CornerRadius="10" Margin="60, 5, 5, 0" VerticalOptions="Center" HorizontalOptions="End">
                <Label Text="{Binding Message}" LineBreakMode="WordWrap" Margin="-5" VerticalOptions="Center" HorizontalOptions="Center" TextColor="White"/>
            </Frame>
            <Label Text="{Binding DisplayDate}" TextColor="Gray" FontSize="9" HorizontalOptions="End" Margin="60, -3, 5, 5"/>
        </StackLayout>
    </ViewCell>
</DataTemplate>

<!-- template přijaté zprávy -->
<DataTemplate x:Key="receivedMessageTemplate">
    <ViewCell>
        <StackLayout Orientation="Vertical" HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand">
            <Frame OutlineColor="{StaticResource PrimaryColor}" BackgroundColor="White" Margin="5, 5, 60, 0" HasShadow="False" CornerRadius="10" VerticalOptions="Center" HorizontalOptions="Start">
                <Label Text="{Binding Message}" LineBreakMode="WordWrap" Margin="-5" VerticalOptions="Center" HorizontalOptions="Center" TextColor="Black"/>
            </Frame>
            <Label Text="{Binding DisplayDate}" TextColor="Gray" FontSize="9" HorizontalOptions="Start" Margin="5, -3, 60, 5"/>
        </StackLayout>
    </ViewCell>
</DataTemplate>
```

Pro jednotlivé zprávy si vytvoříme MessageViewModel, který bude obsahovat i příznak IsMine s hodnotou podle toho, zda je zpráva přijatá nebo odeslaná. Abychom mohli graficky odlišit zprávy podle této vlastnosti, tak definujeme vlastní MessageDataTemplateSelector. Ten dědí od třídy DataTemplateSelector a vytvoříme mu dvě vlastnosti typu DataTemplate: SentMessageTemplate a ReceivedMessageTemplate. Dále přepíšeme metodu OnSelectTemplate, která právě podle příznaku IsMine konkrétní položky vrátí požadovaný DataTemplate.

Pod námi definované šablony v XAML v ResourceDictionary přidáme ještě navíc vytvořený MessageDataTemplateSelector, kterému nastavíme vlastnosti pro template odeslané a přijaté zprávy. Použitému ListView pak tento DataTemplateSelector nastavíme do vlastnosti ItemTemplate.

```c#
/// <summary>
/// TemplateSelector pro vybrání templatu zprávy (odeslaná / přijatá)
/// </summary>
class MessageDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate SentMessageTemplate { get; set; }
    public DataTemplate ReceivedMessageTemplate { get; set; }
    
    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((MessageViewModel)item).IsMine ? SentMessageTemplate : ReceivedMessageTemplate;
    }
}
```

Zprávy do ListView jsou bindované z ChatViewModelu, který je získává z MessageManagera. Aby se nám pravidelně zobrazovaly nově přijaté zprávy, je potřeba zprávy udržovat v ObservableCollection a v samostatném vlákně mít spuštěnou aktualizační smyčku. Ta zjišťuje pomocí MessageManagera, zda jsou k dispozici nějaké novější zprávy, než je poslední v seznamu, a pokud ano, tak je přidá na konec ObservableCollection.

Pro urychlení aplikace nechceme rovnou načítat celou historii zpráv, takže na stránce zavedeme funkcionalitu pro dynamické načítání starších zpráv, pokud uživatel scrolluje směrem nahoru (do historie). Při prvním zobrazení stránky načteme pouze určitý počet nejnovějších zpráv (BatchSize). Registrujeme si událost ListView ItemAppearing a při jejím vyvolání na nejstarší načtené zprávě načteme starší zprávy ze serveru. Zároveň kontrolujeme, zda nám metoda GetMessagesAsync vrací stejný počet zpráv jako je BatchSize. Pokud vrátí míň, znamená to, že je uživatel úplně na konci.

Odeslání zprávy je navázáno na IconLabel s využitím TapGestureRecognizeru a Commandu. Po kliknutí se volá metoda SaveMessageAsync ve třídě MessageManager. Po odeslání zprávy nebo přijetí nové zprávy chceme, aby aplikace na zprávu zascrollovala. To se dělá pomocí metody ScrollTo, kterou poskytuje ListView a předává se jí položka, na kterou chceme zascrollovat, způsob, kterým má být zviditelněna (na začátku, na konci, uprostřed) a zda má být přechod animovaný.

```xml
<!-- DataTemplateSelector pro zprávy -->
<dataTemplateSelectors:MessageDataTemplateSelector x:Key="messageDataTemplateSelector" SentMessageTemplate="{StaticResource sentMessageTemplate}" ReceivedMessageTemplate="{StaticResource receivedMessageTemplate}" />
```

## Zdrojové kódy

Veškeré zdrojové kódy aplikace jsou dostupné na [GitHubu](https://github.com/SkeletonSoftware/AzureChat) firmy Skeleton Software.