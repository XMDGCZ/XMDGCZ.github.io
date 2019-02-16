---
layout: post
title: "Data binding - ukázka "
categories:
    - "csharp"
    - "graphics"
sort_index: 7
author: "David Malý"
--- 


## Data binding


U každého prvku v XAML je možnost získání hodnoty přímo z proměnné, nebo od jiného prvku. Ukázka níže popisuje získání hodnoty z proměnné a odkaz na celou aplikaci je pod článkem.


### Data binding pro jednu proměnnou


XAML prvek s nastaveným data binding.


```

//valueInXml je název proměnné

```


Každá proměnná, která má notifikovat o své změně, musí implementovat rozhraní **INotifyPropertyChanged**.



C# nastavení proměnné pro data binding.


```

/// Private field
private int ValueInXml;

/// Event handler 
public event PropertyChangedEventHandler PropertyChanged;

/// Public property used in label
public int valueInXml
{get{	return ValueInXml;}set{	ValueInXml = value;
	// Notify about change and specific which property changed	OnPropertyChanged("valueInXml");}
}
/// Notify about property changes
protected void OnPropertyChanged(string name)
{PropertyChangedEventHandler handler = PropertyChanged;if (handler != null){	handler(this, new PropertyChangedEventArgs(name));}
}

```

### Data binding pro kolekci




Narozdíl od jedné proměnné, která implementuje INotifyPropertyChanged, tak pro kolekce je vyčleněno rozhraní ObservableCollection