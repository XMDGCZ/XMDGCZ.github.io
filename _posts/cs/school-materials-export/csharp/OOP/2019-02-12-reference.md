## Reference objektu

Když programátor vytvoří proměnnou, je pro ni vymezena paměť, programátor poté pracuje pouze s ukazatelem na tuto paměť, který se předává napříč programem (odkazuje na paměť, kde jsou data proměnné).

 Instance třídy je chápána jako proměnná a pracuje se pouze s ukazatelem na paměť, kde je uložena.

### Předání instance třídy metodě

V objektově orientovaném programovacím jazyku je několik způsobů, jak předat data metodě.  

*   Jako primitivní datový typ
*   Jako primitivní pole
*   Jako jednoduchý objekt např. String
*   Složitý objekt např. instance třídy Osoba
*   Nějaká forma seznamu např. List
U prvních dvou případů platí, že se vytvoří lokální kopie daného parametru a s ní daná metoda pracuje, poté se zahodí.   

Ve zbylých případech se předává pouze odkaz na vytvořenou instanci objektu, se kterou je možné dále pracovat a ovlivnit tak i celý objekt.  

Jednoduchý program, který vytvoří pole a vypíše jeho hodnoty

    <code class="language-C# "> using System.Collections.Generic;

    // Vytvoření nové instance třídy List obalující datový typ int
    List<int> list = new List<int>();

    // Naplnění hodnotami
    list.Add(1);
    list.Add(2);

    // Vypsání seznamu hodnot
    foreach (int one in list) Debug.WriteLine(one);

    // Záměna 1. a 2. prvku v seznamu 
    switchFunction(list);

    // Vypsání seznamu hodnot
    Debug.WriteLine("");
    foreach (int one in list) Debug.WriteLine(one);

    public static void switchFunction(List<int> list)
    {
        int t = list[0];
        list[0] = list[1];
        list[1] = t;
    }
    </int></int></int></code>

Výstup je:

    <code class="language-C# ">1
    2

    2
    1
    </code>

**Jinak řečeno, ovlivníme-li hodnotu u referenčního datového typu, ovlivníme ji pro celý program, ne jen pro konkrétní metodu.**  