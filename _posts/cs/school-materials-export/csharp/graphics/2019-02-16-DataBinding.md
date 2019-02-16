---
layout: post
title: "Data binding "
categories:
    - "csharp"
    - "graphics"
sort_index: 6
author: "David Malý"
--- 


## WPF - DATA BINDING


Matěj Černý



    Samotný XAML obsahuje několik dílčích technologií, které slouží pro úpravu vzhledu, reakci na události a nyní si řekneme něco o technologii Data Binding, která slouží pro práci s daty.



**Příklad:**
    Vytvořená textová pole, ve kterých je vložený text do TextBoxu.


```

      	  

```


Pozn.    *dejte si pozor na rozlišování Text**Boxu** a Text**Blocku.** TextBox slouží pro zadávání textu do pole a oproti tomu TextBlock text pouze zobrazuje.*



    Data Binding se stará o provázání dat z textových bloků a TextBoxů, které slouží jako zdroj. To znamená, že text, který se zadá do TextBoxu se ihned zobrazí i v textových blocích.



- kvůli přístupu k vlastnostem TexBoxu, jsme si ho pojmenovali jako "mySource"
- oproti tomu, druhým způsobem je nabindování hodnoty *Text* v TextBlocku.
        Druhý způsob je ekvivaletní prvnímu způsobu s tím rozdílem, že je kratší.

  - ElementName - je jméno elementu, johož vlastnost chceme použít při bindování
  - Path - název vlastnosti



Pozn.    *Pro bindování se používá pouze kratší způsob (z důvodu přehlednosti a rychlosti), proto už budeme pokračovat jen pomocí kratšího způsobu.*



    Chování TextBlock elemtntů: po jakékoli změně textu v TextBoxu se změna automaticky a okamžitě projeví i v nabindovaných vlastnostech v závislosti na zdrojové hodnotě.



**Příklad:**
    Vytvořili jsme si dva TexBoxy.
    První slouží jako zdrojová hodnota pro druhý TexBox a druhý si od něj tuto hodnotu přebírá.


```

    

```


    Na tomto příkladu jsme si ukázali, že pokud uděláme změnu v první TexBoxu, změna se projeví i v TextBoxu druhém. Projeví se změna ve zdroji, změníme li nabindovanou hodnotu? Ano.



    Bindování je tedy implicitně obousměrná synchronizace hodnot.



Pozn.    *Při změně zdrojové hodnoty dojde k okamžité změně i v nabindované hodnotě. Změníme-li ale nabidnovanou hodnotu, zdrojová hodnota se změní až po tzv. "LostFocus", což znamená, že přestane být aktivní.*


### DALŠÍ ATRIBUTY


K ovlivnění chování bindování se používají tyto atributy:



**Mode** - způsob synchronizace hodnot
- Mode = TwoWay\* - hodnoty jsou synchronizovány obousměrně
- Mode = OneWay - hodnoty jsou synchronizovány pouze ze zdroje do nabindované vlastnosti
- Mode = OneTime - hodnoty jsou synchronizovány pouze po startu, po té už žádné změny neproběhnou



**UpdateSourceTrigger** - tento atribut slouží pro určení v jakou chvíli se zdrojová hodnota aktualizuje
- UpdateSourceTrigger = Excplicit - k synchronizaci dojde pouze pokud zavoláme metodu UpdateSource
- UpdateSourceTrigger = LostFocus\* - zdrojová hodnota se aktualizuje jakmile nabindovaná kontrola přestane být "aktivní" (LostFocus)
- UpdateSourceTrigger = PropertyChanged	- k aktualizaci zdrojové hodnoty dojde okamžitě jakmile je nabindovaná hodnota změněna

*\* = defaulní nastavení*


### BINDOVÁNÍ UŽIVATELSKÝCH OBJEKTŮ


**Příklad:**
    Třída s vlastnostmi Jmeno a Prijmeni. Vytvořili jsme aplikaci se dvěma TextBoxy, které po kliknutí nastaví hodnotu.


```
objekt.Jmeno = "Jan"
objekt.Prijmeni = "Novak"
```


**Třída**


```
public class UserInfoClass
{
  private string jmeno = string.Empty;
  private string prijmeni = string.Empty;
   public string Jmeno
  {  get { return jmeno; }  set { jmeno = value; }
  }

  public string Prijmeni
  {  get { return prijmeni; }  set { prijmeni = value; }
  }
}
```


    Abychom v XAMLu mohli bindovat na vlastnosti uživatelského objektu, musí být třída objektu poděděna od interface INotifyPropertyChanged - to umožní avizovat změny v hodnotách vlastností.



**Jakým způsobem se v XAMLu přistupuje k objektu?** Nejjednodušším způsobem je vložení tohoto objektu do vlastnosti DataContext některého z kmenových elementů. DataContext slouží pro určení defaultního zdroje při bindování dat.



    Window1.xaml


```

```

```

    
    
    Vyplnit

```


    Window1.xaml.cs


```
using System;
using System.Windows;
using System.ComponentModel;

namespace DataBinding
{
    public partial class Window1 : System.Windows.Window
    {
        UserInfoClass user;
        public Window1()
        {
            InitializeComponent();

            user = new UserInfoClass();
            myStackPanel.DataContext = user;
        }

        void Fill(object sender, RoutedEventArgs e)
        {
            user.Jmeno = "Jan";
            user.Prijmeni = "Novak";
        }

        public class UserInfoClass : INotifyPropertyChanged
        {
            public event PropertyChangedEventHandler PropertyChanged;
            private void NotifyPropertyChanged(string info)
            {
                if (PropertyChanged != null)
                        PropertyChanged(this, new PropertyChangedEventArgs(info));
            }

            private string jmeno = "jméno";
            private string prijmeni = "příjmení";

            public string Jmeno
            {
                get { return jmeno; }
                set { jmeno = value; NotifyPropertyChanged("Jmeno"); }
            }

            public string Prijmeni
            {
                get { return prijmeni; }
                set { prijmeni = value; NotifyPropertyChanged("Prijmeni"); }
            }
      }
} 
```


- vložili jsme System.ComponentModel namespace, ve kterém se nachází interface INotifyPropertyChanged
- abychom mohli vlastnosti objektu bindovat, podědili jsme třídu UserInfoClass jsme od rozhraní INotifyPropertyChanged
- vytvořili jsme událost PropertyChanged a metodu NotifyPropertyChanged, která tuto událost volá
- metodu NotifyPropertyChanged voláme vždy, když se změní hodnota některé z vlastností (jako parametr je uveden název vlastnosti)
- po kliknutí na tlačítko "Vyplnit" se zavolá metoda Fill, která změní hodnoty objektu user



**Jak se aplikace chová?**
- změna hodnoty v objektu => změna se projeví v TextBoxech
- změna hodnoty v TextBoxu => hodnota se změní v objektu
- kliknutí na tlačítko "Vyplnit" => změní se hodnoty vlastností objektu ⇒ změní se hodnoty v TextBoxech





    Dalo by se říct, že Data Binding svazuje vlastnosti k sobě. Změna v jedné z vlastností se provede i v té drůhé a to platí obousměrně.
    Bez Data Bindingu by se dalo obejít, ale je to zjednodušená varianta programově volaných metod, které v XAML použít nejdou a proto máme k dispozici Data Binding.