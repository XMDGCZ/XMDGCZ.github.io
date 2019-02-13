---
layout: post
title: "login - base.php "
categories:
    -"csharp"
    -"csharp"
author: "David Malý"
--- 


## Přihlášení


Jedná se o jednoduché přihlášení, které posílá data na php soubor a ten je následovně porovnává (ověřuje) s sql databází.



**Cílem**je vytvořit přihlášení, kde klient zašle na server (php soubor) uživatelské jméno a server mu vrátí tzv. session key. Jedná se o několika - místné číslo, které se náhodně generuje. V tomto případě používám funkci rand() v php. Klient zahashované heslo ještě jednou zahashuje se session key a výsledný hash porovnává s tím samým hasham, co si zahashoval sám server na základě informací uvedených v databázi. Server vrátí odpověď zda ověření bylo úspěšné či nikoliv



**Prvním**krokem je vytvoření dvou základních formulářů. První bude vyžadovat po uživateli, aby zadal své jméno, nick nebo e-mailovou adresu a druhý bude vyžadovat heslo.

![](images/login-csharp.png)


Po zadání nicku a stistknutí tlačítka Pokračovat, se pošlou data, tedy nick na server.



**Ukázka C# kódu - část s nickem**


```

  private static readonly HttpClient client = new HttpClient(); //http klient  

var nick = nick01.Text; //proměnná naplněna výstupem Textblocku

if (nick != "") // podmínka pro ověření, zda není nick prázdný
{
var values = new Dictionary{	{ "nick"//pojmenování postu na webu, nick //proměnná }};
var content = new FormUrlEncodedContent(values);
var response = await client.PostAsync("Tady napište celou URL, kde máte uložený php soubor", content);
var responseString = await response.Content.ReadAsStringAsync(); // Do této proměnné se ukládá výstup ze server(php souboru) včetně chybif (responseString != "false") // Pokud se rovná výstup z webu odpověď false, tak napíše chybu{	Window2 win = new Window2(responseString);	win.Show(); // zobrazí další okno, tedy okno pro zadání hesla	this.Hide(); // Aktuální okno zavře}
else { MessageBox.Show("Chyba! Nespávný nick!"); }
}
else { MessageBox.Show("Nick nebyl zadán.\nMusíte zadat všechna pole!", "Chyba"); } // \n => odskočí na další řádek.
  

```


Tento C# kód zasílá na serveru v metodě POST nick a kontroluje odpověď jednoduchou podmínkou. Více o posílání dat mezi [C# a PHP](http://stackoverflow.com/questions/19189992/c-sharp-sending-data-to-php-url)



**Ukázka PHP kódu - ověření nicku**


```

  $_SESSION["uzi_nick"] = ""; //založení prázdného sessionu

$dotaz = $pdo->query("SELECT `nick` FROM `tbl` WHERE `nick` = '".$_POST["nick"]."' "); //dotaz na sql      
  foreach ($dotaz as $vysledek) {$_SESSION["uzi_nick"] = $vysledek["nick"]; //naplnění sessionu výsledkem dotazu
  }  

  if(!empty($_SESSION["uzi_nick"])) // podmínka pokud neni prázdný session (Pokud je, znamená to, že uživatel neexistuhe v db)
  {       
  $dotaz = $pdo->prepare("UPDATE `tbl` SET `datum_prihlaseni`= ? ,`session_key`= ? WHERE `nick` = '".$_POST["nick"]."'");//zaznamenání vstupu do db	$vysledek2 = $dotaz->execute(array(	  $datum, //aktuální datum. Tato proměnná je naplněna php funkcí date()	  $random_key //=> již zmiňovyný session key, tedy náhodn generované číslo			  ));	echo $random_key; //Pokud je podmínka splněna, tak se zobrazí pouze generované náhodné číslo.
  }else{echo "false"; session_destroy(); unset($_SESSION['uzi_nick']);} //Pokud podmínka neni splněna, tak se zobrazí pouze hláška false. 
  

```


Pro spojení s databází se využívá PDO [více zde](http://jecas.cz/pdo)



**V další** části klient zahashuje zahashované heslo spolu se session key, který nám vrátil web. 2x hashování je z toho důvodu, že v databázi se ukládá díky bezpečnosti hash hesla a né heslo, tak jak ho uživatel napsal. Konečné zahashované heslo se posílá na server opět metodou POST, kde ho porovnává.



**Ukázka C# kódu - část s heslem**


```

  private static readonly HttpClient client = new HttpClient(); //http klient

var pass = passwordBox01.Password;¨ //naplnění proměnné výstupu Textblocku

if (pass != "") //ošetření vstupu
{string hash_pass = CalculateMD5Hash(passwordBox01.Password); //první zahashování samotného heslostring hash_pass2 = CalculateMD5Hash(hash_pass.ToLower() + textBlock.Text); //zahashování zahashovaného heslo spolu se session key, který nám předal předchozí formulář. POZNÁMKA: MD5 JE PROLOMENÉ => NEDOPORUČUJE SE
var values = new Dictionary{	{ "pass", hash_pass2.ToLower()}, //poslání na server konečný hash hesla POZOR! je nutné převést písmena hashe z vekých na malá => může ovlivnit hash	{ "key" , textBlock.Text} // poslání session na server také session key pro ověření (může být zaslán i nick)};
var content = new FormUrlEncodedContent(values);
var response = await client.PostAsync("Tady napište celou URL, kde máte uložený php soubor", content);
var responseString = await response.Content.ReadAsStringAsync(); // Do této proměnné se ukládá výstup ze server(php souboru) včetně chyb
if (responseString != "false") // Pokud se rovná výstup z webu odpověď false, tak napíše chybu{	Window1 win = new Window1(responseString);	win.Show(); //přihlášení proběhlo úspěšně => zobrazí další okno 	this.Hide(); // Aktuální okno zavře}
else { MessageBox.Show("Chyba hesla!"); }
}
else { MessageBox.Show(" Heslo nebylo zadáno.\nMusíte zadat všechna pole!", "Chyba"); }
}
  

```


Pro tento příklad jsem použil již prolomené šífrování MD5. **Rozhodně nepoužívat**pro rozsáhlé a mimoškolní projekty! Více o šifrování [MD5 v C#](https://blogs.msdn.microsoft.com/csharpfaq/2006/10/09/how-do-i-calculate-a-md5-hash-from-a-string/)



**Ukázka PHP kódu - ověření hesla a generace session key**


```

  $_SESSION["uzi_heslo"] = ""; //založení prázdného sessionu

$dotaz = $pdo->query("SELECT * FROM `tbl` WHERE `session_key` = '".$_POST["key"]."' "); // dotaz na výpis uživatele s konkrétním session key || může být změněno na nick       
  foreach ($dotaz as $vysledek) {$_SESSION["uzi_heslo"] = $vysledek["heslo"]; // výběr hashe hesla uživatele z db
  }
  
  if (!empty($_POST["pass"])) // podmínka zda nejsou posílaná data prázdná 
  {$md5_sk_hash = $_SESSION["uzi_heslo"]; // naplnění proměnné hashe hesla z db $md5_sk_hash .= $_POST["key"]; // přidání do proměnné session key$md5_sk_hash_celek = md5($md5_sk_hash);  // zahashování celku 
if($_POST["pass"] == $md5_sk_hash_celek)// porovnání zda se hashe shodují{    $dotaz = $pdo->prepare("UPDATE `tbl` SET `sk_pass_hash`= ? WHERE `session_key` = '".$_POST["key"]."' "); 	  $vysledek2 = $dotaz->execute(array(	  $md5_sk_hash_celek               	  )); //naplnění db výsledným hashem  => možnost dalšího použití	  	 }else {echo "false"; session_destroy(); unset($_SESSION['uzi_heslo']);} //výpis hlášky pokud se ověření nezdaří
  }
  else {echo "false"; session_destroy(); unset($_SESSION['uzi_heslo']);} //výpis hlášky pokud se ověření nezdaří  
  

```


Připojování k databázi je opět přes PDO [více zde.](http://jecas.cz/pdo) Místo šifrování MD5 doporučuji SHA1 nebo SHA256. Pokud budete měnit šifrování, tak nezapomeňte změnit jak na straně klienta (C# kód), tak i na straně serveru (PHP kód).

