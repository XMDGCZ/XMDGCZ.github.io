---
layout: post
title: "decorator.php "
categories:
    -"csharp"
    -"csharp"
author: "David Malý"
--- 


##   Decorator Pattern


Lukáš Drechsel



Decorator pattern se používá pro přidání nové funkcionality existujícímu objektu se zachováním jeho původní struktury.



Tento pattern vytváří třídu decorator, která se přiřadí k originální třídě. Následně ji přidává nové možnosti chování a operace, které lze s objektem provádět.


### Příklady použití

- Pro přidání dalšího stavu nebo chování k objektu a to dynamicky.
- Udělat změnu v některých objektech v jedné třídě, aniž by ovlivnila jiné.


### Decorator Pattern - UML Diagram a implementace


Níže se nachází UML class diagram pro implementaci designového patternu decorator:

![DecoratorPatternUML](images/decorator_uml.png)
#### 1. Component


Interface obsahující členy, který jsou implementovány třídami ConcreteClass a Decorator.


#### 2. ConcreteComponent


Třída, která implementuje Component interface.


#### 3. Decorator


Abstraktní třída, která implementuje Component interface a obsahuje reference k instanci Component. Tato třída se chová jako základní třída pro všechny dekorátory pro komponenty.


#### 4. ConcreteDecorator


Třída, která dědí z druhé třídy Decorator a poskytuje dekorátor pro komponenty.


### Zdrojový kód pro implementaci

```
public interface Component
{void Operation();
}

public class ConcreteComponent : Component
{public void Operation(){
 		Console.WriteLine("Component Operation");
 	}
}

public abstract class Decorator : Component
{private Component _component;

 	public Decorator(Component component)
 	{
 		_component = component;
 	}

 	public virtual void Operation()
 	{
 		_component.Operation();
 	}
}

public class ConcreteDecorator : Decorator
{public ConcreteDecorator(Component component) : base(component) { }

 	public override void Operation()
 	{
 		base.Operation();
 		Console.WriteLine("Override Decorator Operation");
 	}
}


```

### Vzorový kód


Představme si, že máme jednu abstraktní třídu VehicleDecorator, další třídu HondaCity a interface Vehicle. Vehicle představuje auto, který má nějaký model a cenu.



Nyní máme situaci, ve které potřebujeme přidat do již existující třídy další vlastnost objektu. V tomto případě se jedná o SpecialOffer, nějaká speciální nabídka na daný typ auta, obashující například procentuální slevu.



Nejprve si vytvoříme interface auta.


```

public interface Vehicle
{
   string Make { get; }
   string Model { get; }
   double Price { get; }
}


```


Následuje třída ConcreteComponent.


```

public class HondaCity : Vehicle
{
 	public string Make
 	{
 		get { return "HondaCity"; }
 	}

 	public string Model
 	{
 		get { return "CNG"; }
 	}

 	public double Price
 	{
 		get { return 1000000; }
 	}
}


```


Dále dekorátor abstraktní třídy.


```

public abstract class VehicleDecorator : Vehicle
{
 	private Vehicle _vehicle;

 	public VehicleDecorator(Vehicle vehicle)
 	{
 		_vehicle = vehicle;
 	}

 	public string Make
 	{
 		get { return _vehicle.Make; }
 	}

 	public string Model
 	{
 		get { return _vehicle.Model; }
 	}

 	public double Price
 	{
 		get { return _vehicle.Price; }
 	}
}


```


A samotná třída ConcreteDecorator.


```

public class SpecialOffer : VehicleDecorator
{
 	public SpecialOffer(Vehicle vehicle) : base(vehicle) { }

 	public int DiscountPercentage { get; set; }
 	public string Offer { get; set; }

 	public double Price
 	{
 		get
 		{
 			double price = base.Price;
 			int percentage = 100 - DiscountPercentage;
 			return Math.Round((price * percentage) / 100, 2);
 		}
 	}
}


```


Nakonec se napíše tato část kódu, která představuje speciální nabídku na auto se slevou 25%.


```

class Program
{
 	static void Main(string[] args)
 	{
 		// Základní auto
 		HondaCity car = new HondaCity();

 		Console.WriteLine("Honda City base price are : {0}", car.Price);

 		// Speciální nabídka
 		SpecialOffer offer = new SpecialOffer(car);
 		offer.DiscountPercentage = 25;
 		offer.Offer = "25 % discount";

 		Console.WriteLine("{1} @ Diwali Special Offer and price are : {0} ", offer.Price, offer.Offer);

 		Console.ReadKey();

 	}
}


```

### Zdroje


[dotnettricks.com](http://www.dotnettricks.com/learn/designpatterns/decorator-design-pattern-dotnet)



[dofactory.com](http://www.dofactory.com/net/decorator-design-pattern)



[codeproject.com](https://www.codeproject.com/Articles/479635/UnderstandingplusandplusImplementingplusDecoratorp)

