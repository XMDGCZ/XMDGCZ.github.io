## Datové typy

Určují, jakých hodnot smí nabývat proměnná. Dělí se na základní datové typy a na ty, které skládají vícero primitivních a vytváří tak nové.

<h3> Primitivní datové typy </h3>

Jsou takové datové typy, které **se neskládají z jiných datových typů**.  
 Primitivní datové typy, které mohou být použity v C#.

*   char    - jeden znak 
*   int     - celá čísla (32bit)
*   double  - desetinná čísla s plovoucí desetinnou čárkou (64bit)
*   decimal - celá čísla (128bit)
*   void    - žádná hodnota
*   boolean - true/false, někdy je možné reprezentovat jako bit - 0/1

Rozdíl mezi double/float a decimal

*   float/double jsou reprezentovány (uloženy) v binární podobě <h3>Referenční datové typy </h3>

**Vznikají použitím datové struktury, nebo třídy**.  
 Referenční -> používají odkaz(referenci) na jiný objekt.

Příklady:  

	<dd> Třída String: jedná se o statickou třídu, která implementuje pole (array) datového typu char.  

	<dd> Oproti struktuře String v C/C++ obohacuje String ještě o metody např. lenght;contains...  

	<td> Třída Auto může obsahovat pocetKol(int), nazevAuta(String) a jiné</td>

*Pozn. jakékoliv pole je děděné z třídy Object a tak je referenční datový typ.*</dd></dd>