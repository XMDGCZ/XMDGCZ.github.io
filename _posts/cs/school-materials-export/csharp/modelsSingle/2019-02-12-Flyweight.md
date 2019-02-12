##  Flyweight Design Pattern

Marek Bastl

 Flyweight je jedním ze základních návrhových vzorů programů. Patří do skupiny strukturních vzorů, které umožňují řízení počtu instancí objektů. Vzor je vhodný pro situace, kdy vzniká mnoho malých objektů, u kterých je možné podstatnou část jejich stavu sdílet nebo ho nahradit výpočtem.

### Smysl patternu

<table class="table-basic center-text">
    <tr class="mdl-color--primary white-text">
        <th>Nabízí</th>
    </tr>
    <tr>
        <td>Používá sdídelní pro effektivní podporu velkého množství objektů</td>
    </tr>
    <tr>
        <td>The Motif GUI strategie nahrazuje náročné widgety pomocí jednoduchých gadgetů</td>
    </tr>
</table>

### Problémy

Systém umožnuje navrhování objektů až na nejnižší úroveň, v tomto poskytuje značnou flexibilitu. Problém nastává v případě náročnosti na výkon a využití paměti, kde je velmi náročný.

### Popis

Flyweight popisuje způsob, jak sdílet objekty, tak aby je bylo možné použít bez nepřiměřených nákladů. Každý objekt je rozdělen na dva stavy. Stav závislý (vnější) a stav nezávislý (vnitřní). Tento stav je uložen v objektu Flyweight při jeho volání.

### Ukázka

**Krok 1**

Vytvoření abstartní Flyweight třídy Soldier.

<code class="language-C# ">public abstract class Soldier
    {
              public WeaponType Weapon{get;set;}
              public abstract void RenderSoldier(string StrPriName, string Color);
    }</code>

**Krok 2**

Vytvoření třídy GunSoldier a SwordSoldier (zbraně).

<code class="language-C# ">public class GunSoldier:Soldier
    {
              public GunSoldier()
              {
                        this.Weapon = WeaponType.Gun;
              }
              public override void RenderSoldier(string StrPriName, string Color)
              {
                        HttpContext.Current.Response.Write("Gun Character " + StrPriName + " Rendered with "+Color+" Color");
              }
    }
    public class SwordSoldier:Soldier
    {
              public SwordSoldier()
              {
                        this.Weapon = WeaponType.Sword;
              }
              public override void RenderSoldier(string StrPriName, string Color)
              {
                        HttpContext.Current.Response.Write("Sword Character " + StrPriName + " Rendered with " + Color + " Color");
              }
    }</code>

**Krok 3**

Vytvoření třídy SoldierFactory.

<code class="language-C# ">public class SoldierFactory
    {
              Dictionary<string, soldier=""> SoldierCollection;
              public SoldierFactory()
              {
                        SoldierCollection = new Dictionary<string, soldier="">();
              }
              public Soldier GetSoldier(string SoldierIndex)
              {
                        if(!SoldierCollection.ContainsKey(SoldierIndex))
                        {
                                  HttpContext.Current.Response.Write("Objet created - ");
                                  switch(SoldierIndex)
                                  {
                                            case "0":
                                            SoldierCollection.Add(SoldierIndex, new GunSoldier());
                                            break;
                                            case "1":
                                            SoldierCollection.Add(SoldierIndex, new SwordSoldier());
                                            break;
                                  }
                        }
                        else
                        {
                                  HttpContext.Current.Response.Write("Objet reused - ");
                        }
                        return SoldierCollection[SoldierIndex];
              }
    }</string,></string,></code>

**Krok 4**

Uživatelský kód

<code class="language-C# ">this.CreateSoldier(DdlCharacterType1, TxtCharacterName1, TxtColor1);
    Response.Write("  
");
    this.CreateSoldier(DdlCharacterType2, TxtCharacterName2, TxtColor2);
    Response.Write("  
");
    this.CreateSoldier(DdlCharacterType3, TxtCharacterName3, TxtColor3);
    private void CreateSoldier(DropDownList DdlCharacterType, TextBox TxtCharacterName, TextBox TxtColor)
    {
              Soldier soldier = factory.GetSoldier(DdlCharacterType.SelectedValue);
              soldier.RenderSoldier(TxtCharacterName.Text,TxtColor.Text);
    }</code>

### Struktura

Flyweights jsou uloženy v repozitáři Factory. Klient se omezuje na vytváření Flyweights přímo a proto musí využít třídu Factory. Veškeré sdílené atributy musí být poskytnuty klientem. 

#### Class diagram

![Struktura](images/Flyweight.png)