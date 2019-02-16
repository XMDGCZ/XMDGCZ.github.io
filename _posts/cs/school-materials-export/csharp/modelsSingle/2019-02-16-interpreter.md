---
layout: post
title: "Interpreter "
categories:
    - "csharp"
    - "modelsSingle"
sort_index: 9
author: "David Malý"
--- 


##   Design Patterns - Interpreter


Marek Moravčík



Interpreter slouží k překladu gramatiky z libovolného „jazyka“ do obecné formy. Vytváří reprezentaci pro části gramatiky a je zároveň schopen větu nebo frázi interpretovat skrze definice vytvořené uživatelem.



Pozn.    *Jazykem je chápán jakýkoliv obor slov, který obsahuje nějakou strukturu (gramatiku).*


### Kdy použít Interpreter?


Používá se v případech, kdy je třeba interpretovat gramatiku některého jazyka. Interpreter lze použít v případě, že je možné definovat pravidla gramatiky, podle kterých se jazyk bude řídit.


### Použití
![UML diagram](images/InterpreterUML.gif)

**Client** reprezentuje rozhraní skrze které se vytváří abstraktní strom expresí a volá samotnou interpretaci věty



**AbstractExpression** definuje interface použitý pro interpretaci



**TerminalExpression** vyjadřuje konečný výraz



**NonterminalExpression** vyjadřuje složený výraz, pro který obsahuju další definice vyjadřující části výrazu



**Context** obsahuje globální informaci s kterou se pracuje

**C#**




```

Context context = new Context();
 // Abstraktní strom výrazů
ArrayList list = new ArrayList();
list.Add(new TerminalExpression());
list.Add(new NonterminalExpression());


// Interpretace
foreach (AbstractExpression exp in list)
{exp.Interpret(context);
}

```

```
// Abstraktní třída
abstract class AbstractExpression
  {
    public abstract void Interpret(Context context);
  }

  // Třída konečného výrazu
  class TerminalExpression : AbstractExpression
  {
    public override void Interpret(Context context)
    {	// Udělej něco pro konečný výraz
    }
  }

  // Třída složeného výrazu
  class NonterminalExpression : AbstractExpression
  {
    public override void Interpret(Context context)
    {	// Udělej něco pro složený výraz
    }
  }

```

### Ukázka


Překlad římských číslic na arabské






```

string roman = "MMXVIII";
Context context = new Context { Input = roman };


```


List výrazů


```
 List tree = new List(); tree.Add(new ThousandExpression()); tree.Add(new HundredExpression()); tree.Add(new TenExpression()); tree.Add(new OneExpression());

```


Interpretace zvoleného čísla


```
 foreach (Expression expression in tree)
{expression.Interpret(context);
}

 Console.WriteLine("{0} = {1}", roman, context.Output);

 // Počkej na uživatele
Console.ReadKey();


```


Definice třídy s textem


```
 class Context
{public string Input { get; set; }public int Output { get; set; }
}


```


Definice abstraktní třídy výrazu, který bude interpretován


```

 abstract class Expression
{public void Interpret(Context context){	 if (context.Input.Length == 0)		 return;
	 if (context.Input.StartsWith(Nine()))	 {		 context.Output += (9 * Multiplier());		 context.Input = context.Input.Substring(2);	}	else if (context.Input.StartsWith(Four()))	 {		 context.Output += (4 * Multiplier());		 context.Input = context.Input.Substring(2);	}	else if (context.Input.StartsWith(Five()))	 {		 context.Output += (5 * Multiplier());		 context.Input = context.Input.Substring(1);	}
	while (context.Input.StartsWith(One()))	{		 context.Output += (1 * Multiplier());		 context.Input = context.Input.Substring(1);	}}
 public abstract string One();public abstract string Four();public abstract string Five();public abstract string Nine();public abstract int Multiplier();
}

```


Třídy pro číslice Tisíc, sto, deset a jedna


```
  // Tisíc je reprezentováno písmenem M
class ThousandExpression : Expression
{public override string One() { return "M"; }public override string Four() { return " "; }public override string Five() { return " "; }public override string Nine() { return " "; }public override int Multiplier() { return 1000; }
}

// Sto je reprezentováno písmeny C, CD, D or CM
class HundredExpression : Expression
{public override string One() { return "C"; }public override string Four() { return "CD"; }public override string Five() { return "D"; }public override string Nine() { return "CM"; }public override int Multiplier() { return 100; }
}

// Deset je reprezentováno písmeny X, XL, L and XC
class TenExpression : Expression
{public override string One() { return "X"; }public override string Four() { return "XL"; }public override string Five() { return "L"; }public override string Nine() { return "XC"; }public override int Multiplier() { return 10; }
}


// Jedna je reprezentováno písmeny I, II, III, IV, V, VI, VI, VII, VIII, IX
class OneExpression : Expression
{public override string One() { return "I"; }public override string Four() { return "IV"; }public override string Five() { return "V"; }public override string Nine() { return "IX"; }public override int Multiplier() { return 1; }
}


```


**Output:**

![Output](images/InterpreterUkazka.png)