---
layout: post
title: "interface_student.php "
categories:
    -"csharp"
    -"csharp"
author: "David Malý"
--- 


## Implementace interface


Martin Zbořil



Implementace slouží jako příklad použití interface. Ukázka je založená na téma ohodnocení studenta ve škole. Kde se, dle studentových výkonů(známek) určuje počet hodin, které se musí učit a počet hodin, které může využít k hraní na PC.



Jako první samotná definice interface IPersonBehavior. Interface obsahuje atributy Study a Play a metoda GetResult().


```

interface IPersonBehavior
{int Study { get; set; }int Play { get; set; }string GetResult();
}

```


Třídy, které implementují interface.



**GoodPersonMark**



Třída, která vystihuje chování žáka, když má dobré známky. Či-li žák musí studovat 1 hodinu a hrát 5 hodin. Metoda **GetResult()** vrací výsledek s informací, kolik musí hrát a studovat.


```

class GoodPersonMark : IPersonBehavior
{private int _study = 1;
public int Study{	get { return _study; }	set { _study = value; }}
private int _play = 5;
public int Play{	get { return _play; }	set { _play = value; }}
public string GetResult(){	return "Můžeš hrát: " + _play + " hodin" + " Studovat: " + _study + " hodin";}
}

```


**MediumPersonMark**



Třída, která vystihuje chování žáka, když má průměrné známky. Či-li žák musí studovat 3 hodiny, a hrát může taky 3 hodiny. Metoda **GetResult()** vrací výsledek s informací, kolik musí hrát a studovat.


```

class MediumPersonMark : IPersonBehavior
{private int _study = 3;
public int Study{	get { return _study; }	set { _study = value; }}private int _play = 3;
public int Play{	get { return _play; }
	set { _play = value; }}public string GetResult(){	return "Můžeš hrát: " + _play + " hodin" + " Studovat: " + _study + " hodin";
}
}

```


**BadPersonMark**



Třída, která vystihuje chování žáka, když má špatné známky. Či-li žák musí studovat 5 hodiny, a hrát 1 hodinu. Metoda **GetResult()** vrací výsledek s informací, kolik musí hrát a studovat.


```

class BadPersonMark : IPersonBehavior{	private int _study = 5;
	public int Study	{		get { return _study; }		set { _study = value; }	}
	private int _play = 1;
	public int Play	{		get { return _play; }		set { _play = value; }	}		public string GetResult()	{		return "Můžeš hrát: " + _play + " hodin" + " Studovat: " + _study + " hodin";	}}

```


**Person**



Třída, která vystihuje osobu(žáka). Obsahuje atributy Name a PersonRatingStrategy. Atribut **PersonRatingStrategy** je datový typ rozhraní(interface) a metoda **GetReward()**, vrací výsledek na základě tohoto atributu.


```

			class Person
			    {
			        public Person(int mark, string name, IPersonBehavior personratingstrategy)
			        {
			            this._mark = mark;
			            this._name = name;
			            this._personratingstrategy = personratingstrategy;
			        }
			        private string _name;
			        public string Name
			        {
			            get { return _name; }
			            set { _name = value; }
			        }
			        private IPersonBehavior _personratingstrategy;
			        public IPersonBehavior PersonRatingStrategy
			        {
			            get { return _personratingstrategy; }
			            set { _personratingstrategy = value; }
			        }
			    	public string GetRating()
			        {
			            return _personratingstrategy.GetResult();
			        }			    }
			
		
```


**Person**





Instance třídy Person a vypsání ohodnocení.<br><br>
```

			///Špatné chování
				Person person = new Person("Albert", new BadPersonMark());
	            Console.WriteLine(person.Name + " " + person.GetRating());
	            ///Průměrné chování
	            Person person1 = new Person("Petr", new MediumPersonMark());
	            Console.WriteLine(person1.Name + " " + person1.GetRating());
	            ///Výborné chování
	            Person person2 = new Person("Jana", new GoodPersonMark());
	            Console.WriteLine(person2.Name + " " + person2.GetRating());
			
		
```
