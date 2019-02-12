## Abstraktní třída

 Abstraktní třída je nástrojem abstrakce systému. Určuje identitu objektů, předepisuje atributy a metody, které mohou ostatní objekty dědit. 

 Podobně jako Interface může definovat pouze hlavičky metod bez implementace těla. Nejčastěji se využívá její vlastnosti kombinování abstraktních metod (pouze hlavičky) a reálných (včetně těla), čímž umožňuje redukovat opakování kódu a udržovat strukturu systému. 

[Abstract class in Java](http://stackoverflow.com/q/1320745/3864686)

### Abstraktní třída a dědičnost

 Jako nástroj abstrakce systému nemůže být abstraktní třída instancována, je nutné ji jinou třídou **konkretizovat** (podědit její vlastnosti a implementovat funkčnost). 

 Umožňuje použití **abstraktních metod** - pouze hlavičky metod bez implementace těla. Subclass (třídy, které realizují abstraktní nástroje, nebo dědí jiné třídy) musí abstraktní metody implementovat (stejně jako u Interface). 

 Třída konkretizuje abstraktní třídu. Dědí její atributy a metody. 

![](images/AbstractInTheory.png)

 Třída ConcretizationOfAbstractClass podědila Field Attribute a metodu Method(). K tomu rozšířila abstrakci o vlastní metodu NewMethod. 

### Konkrétní příklad - Zvířata

 Na obrázku je vymodelovaná situace, kde abstraktní třída zaštiťuje všechna zvířata (stejně jako v reálném světě, zvíře není žádná existující entita). A dva konkrétní zástupci zvířat. 

![](images/AbstractionInPractise.png)

 DogMaster a CatStupido implementují vše z abstraktního předka - Animal. Animal je abstraktní třída, nelze vytvořit její instanci. 

 Definice Animal: * Deklaruje proměnnou (Field) Name, která je public * DateOfBirth, která je děditelná pouze v rámci stejné Assemly jako je abstraktní třída * Implementuje metodu AgeText(), která je společná pro všechny podtřídy (subclasses) * Předepisuje Abstraktní metodu DoSound(), kterou musí každá podtřída implementovat 

    <code class="language-C# ">public abstract class Animal
    {
        // Public field Name
        public string Name;

        // Could be inherited only by classes in same assembly
        protected internal DateTime DateOfBirth = new DateTime();

        // Implements age info, which is same for all animals
        public string AgeText()
        {
            return DateOfBirth.ToString(CultureInfo.CurrentCulture);
        }

        // Abstract method which must be implemented by all subclasses
        public abstract string DoSound();
    }
    </code>

 Definice CatStupido implementuje vše z Abstraktní třídy pomocí vlastních metod a mňouká k tomu.  
 CatStupido musí po svém implementovat abstraktní metodu DoSound() poděděnou z abstraktní třídy. 

    <code class="language-C# ">class CatStupido : Animal
    {
        // Not inherited methods

        /// Just get Name and add "Meow""
        public string GetNameWithCat()
        {
            // Inherited Name attribute from Base class
            return Name + "Meow";
        }

        /// Get name of cat and meow
        public string GetAgeWithMeow()
        {
            return AgeText() + " Meow";
        }

        public override string DoSound()
        {
            return "Meow";
        }
    }
    </code>

 Definice DogMaster spoléhá na dědičnost a implementuje pomocí vlastních metod zděděné zdroje.  
 DogMaster musí po svém implementovat abstraktní metodu DoSound() poděděnou z abstraktní třídy. 

    <code class="language-C# ">class DogMaster : Animal
    {
        /// Barks by age (only years)
        /// return: Formatted string of Barks according to age
        public string BarkHowOldAreYou()
        {
            StringBuilder outputToPrintStringBuilder = new StringBuilder();

            outputToPrintStringBuilder.Append( AgeText() ).Append(Environment.NewLine);

            for(int i =0; i < (datetime.now.year="" -="" dateofbirth.year);="" i="" ++)="" outputtoprintstringbuilder.append("bark").append(environment.newline);="" return="" outputtoprintstringbuilder.tostring();="" }="" public="" override="" string="" dosound()="" {="" return="" "le="" wuf";="" }="" }=""></code>

### Vícenásobná dědičnost

 V Českém i jiném jazyce, může být slovo podřazené nadřazené jinému např. hudební nástroj -> strunný nástroj -> kytara -> elektrická kytara -> Gibson Fender. Jedná se o postupnou konkretizaci abstraktních pojmů až k samotné konkrétní entitě. 

 Následující diagram modeluje tuto situace v menším měřítku: Hudební nástroj -> strunný nástroj -> elektrická a basová kytara. 

![](images/InstrumentsClassDiagram.png)

 Princip dědičnosti je zde zřejmý na první pohled (Strunný nástroj dědí z Hudebního nástroje a konkrétní typy strunných nástrojů, kytara, basa, dědí ze strunného nástroje). 

 Za povšimnutí stojí, že jak třída Instrument, tak i Strunný nástroj jsou abstraktní třídy - neexistuje jejich reálná entita.** Pokud je třída abstraktní a dědí z jiné abstraktní třídy, tak nemusí implementovat žádné její abstraktní metody.** To je starost konkrétních tříd - BassGuitar a ElectricGuitar implementují abstraktní metodu Play() (označena hvězdičkou). 