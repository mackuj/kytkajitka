**********************
Popis software
**********************

Tato kapitola obsahuje popis softwarové části projektu. Je zde popsán:

* postup vytvoření rozhraní ke službě Twitter, 
* způsob komunikace se senzorem,
* způsob komunikace se službou Twitter,
* struktura aplikace a její jednotlivé části.


.. |_| unicode:: 0xA0
   :trim:

======================
Připojení na Twitter
======================

Nejčastějším přispěvatelem na Twitteru je uživatel, který na stránkách Twitteru nebo na mobilní aplikaci píše tweety nebo jiné příspěvky. Ale na Twitter mohou psát také automatické aplikace (náš případ). Ty však musejí být registrovány. Postup registrace je popsán v následující kapitole. 

.. _registrace-aplikace-na-twitteru:

-------------------------------
Registrace aplikace na Twitteru
-------------------------------

Pro vytvoření aplikace na Twitteru je nutné mít založený účet na Twitteru. Tento účet se musí použít k přihlášení na stránku *dev.twitter.com*. Potom je třeba na této stránce (v dolní části) najít odkaz na spravování uživatelských aplikací. Je možno také rovnou přejít na stránky *apps.twitter.com*. 

.. figure:: /images/Dev_twitter.png
   :scale: 50%
   :alt: Stránka dev.twitter.com

   Stránka *dev.twitter.com*.
 

.. figure:: /images/Prihlaseni.png
   :scale: 50%
   :alt: Přihlášení

   Přihlášení na Twitter.


.. figure:: /images/Odkaz_aplikace.jpg
   :scale: 50%
   :alt: Správa aplikací.

   Správa aplikací.


Na stránce *apps.twitter.com* je třeba kliknout na tlačítko *Create new app* a vyplnit formulář, který se objeví. 

.. figure:: /images/Formular.png
   :scale: 50%
   :alt: Formulář pro založení aplikace.

   Formulář pro založení aplikace.

 
Ve formuláři se vyplní následující pole:

* jméno aplikace (musí být jedinečné)
* krátký popis aplikace (pro budoucí uživatele)
* URL odkaz na vlastní stránky (musí se zadat odkaz na vlastní existující stránky, kde je aplikace popsána)
* adresa pro zpětné volání (zpravidla není potřeba)

Jakmile je formulář vyplněn, je nutno si přečíst pravidla použití a zatržením potvrdit svůj souhlas. Vyplněním CAPTCHA a kliknutím na tlačítko *Create your Twitter application* se vytvoří aplikace. 

Nyní je třeba autorizovat vytvořenou Twitterovou aplikaci pro náš Twitterový účet. K tomu je třeba kliknout na tlačítko *Create my access token*. Tím se vytvoří 4 přístupové tokeny (řetězce). Těmito tokeny se bude naše aplikace přihlašovat k našemu účtu na Twitteru. 

.. figure:: /images/Tokeny.jpg
   :scale: 50%
   :alt: Přístupové tokeny

   Přístupové tokeny.


Základní nastavení aplikace povoluje pouze čtení tweetů. Pokud aplikace potřebuje také zapisovat (náš případ), musí se změnit nastavení přístupového typu (*Read and Write*). 

Potom je nutno zapsat tyto 4 tokeny *Consumer Key*, *Consumer Secret*, *OAuth Access Token*, *OAuth Access Token Secret*. Tyto tokeny se použijí v aplikaci.

Podrobný postup (v angličtině) je k dispozici na :cite:`Twitapp`.

.. _komunikace-se-senzorem:

========================
Komunikace se senzorem
========================

.. index:: I2C

Pro komunikaci se senzorem po sběrnici I2C jsem využil knihovnu *smbus*. Knihovna obsahuje velké množství funkcí, z nichž jsem využil pouze funkce pro čtení a zápis hodnoty. Níže jsou uvedené obecné funkce pro čtení a zápis. 
Funkční kódy pro senzor (v programovacím jazyce Python)

.. code-block:: python

	#!/usr/bin/python
	# -*- coding: utf-8 -*-

	import smbus

	bus = smbus.SMBus(1)
	address = 0x05           # adresa senzoru

	# --------------------------------------------------
	# protokol I2C

	def i2c_write8(addr, value):
          """ Procedura která napíše na adresu addr hodnotu value. """
	  bus.write_byte(addr, value)
	  return -1

	def i2c_read16(addr):
           """ Procedura která přečte data z adresy addr a zamění byty. """
	  num = bus.read_word_data(addr, 0xFF)
	  return ((num << 8) + (num >> 8)) & 0xFFFF

	def i2c_read8(addr):
          """ Procedura která přečte z adresy addr a vrátí hodnotu. """
          num = bus.read_byte(addr)
	  return(num)

Adresy, které uvádí výrobce senzoru, jsou uvedeny v následující tabulce.


.. list-table:: Pouřité registry pro čtení a rápis
   :header-rows: 1

   * - Funkce 
     - Adresa
     - Čtení/Zápis
     - Délka dat
   * - Vlhkost 
     - 0x00
     - Čtení 
     - 2 Byty
   * - Teplota
     - 0x05
     - Čtení
     - 2 Byty
   * - Osvit Hodnota
     - 0x04
     - Čtení
     - 2 Byty
   * - Začátek Osvitu
     - 0x03
     - Zápis
     - 0 Bytů



---------------------------
Čtení teploty
---------------------------

Teplota se čte pomocí funkce *get_temp()*. Na adresu senzoru se napíše hodnota 5. Přečtená teplota je v desetinách °C.


.. code-block:: python

	def get_temp():
          """Čtení teploty """
	  i2c_write8(address, 5)
	  time.sleep(0.1)
	  t = i2c_read16(address)
	  return(t)


---------------------------
Čtení vlhkosti 
---------------------------

Na čtení vlhkosti jsem vytvořil funkci *get_mois()*. Na adresu senzoru se tentokrát napíše 0. Takto získaná vlhkost je vrácena jako proměná *m*.

.. code-block:: python

	def get_mois():
          """Čtení vlhkosti """
	  i2c_write8(address, 0)
	  time.sleep(0.1)
	  m  = i2c_read16(address)  
	  return(m)

--------------------------
Čtení osvitu
--------------------------

Čtení osvitu je složitější, neboť se musí napřed nastartovat tím, že se na adresu čidla napíše hodnotu 3 a |_| 3 |_| sekundy se čeká. Funkce *get_light()* po zapsání 4 vrátí do proměnné *l* číslo od 0 |_| do 65535.

.. code-block:: python

	def get_light():
          """Čtení osvitu - Start osvitu počkat 3s přečíst hodnotu """
	  i2c_write8(address, 3)
	  time.sleep(3.0)
	  i2c_write8(address, 4)
	  time.sleep(0.1)
	  l = i2c_read16(address)
	  return(l)

.. _namerene-hodnoty:

--------------------------
Naměřené hodnoty
--------------------------

Program má reagovat na naměřené veličiny různými typy tweetů. Abychom mohli provést kalibraci a tím lépe určili hranice jednotlivých typů tweetů, je nutno znát průběh jednotlivých veličin za delší časové období. Z toho důvodu jsem ukládal jednotlivá měření do textových souborů. Z nich jsem potom vytvořil následující grafy. 


^^^^^^^^^^^^
Teplota
^^^^^^^^^^^^

Zde lze vidět, že se teplota měnila v průběhu dne v závislosti na topení a na tom, jestli do místnosti svítilo slunce nebo ne.

.. figure:: /images/hodnoty_t.png
   :scale: 60%
   :alt: Průběh teploty.

   Průběh teploty.


^^^^^^^^^^^^
Vlhkost
^^^^^^^^^^^^

Vlhkost je snímána od okamžiku zalití rostliny. Je patrný přirozený pokles vlhkosti zapříčiněný vysycháním substrátu a spotřebou rostliny. Zvýšení vlhkosti v pravé polovině grafu je způsobeno mírným zalitím, po kterém vlhkost opět klesá. 

.. figure:: /images/hodnoty_v.png
   :scale: 60%
   :alt: Průběh vlhkosti.

   Průběh vlhkosti.


^^^^^^^^^^^^
Osvit
^^^^^^^^^^^^

Osvětlení se mění v průběhu denního cyklu. Intenzita je ovlivňována postavením květiny v |_| místnosti, ročním obdobím a počasím. Mírné kolísání k večeru je způsobeno zapínáním a |_| vypínáním umělého osvětlení v místnosti.

.. figure:: /images/hodnoty_o.png
   :scale: 60%
   :alt: Průběh osvitu.

   Průběh osvitu.

.. _komunikace-s-twitterem:

=========================
Komunikace s twitterem
=========================

Na tomto programu je vidět jednoduchý mechanismus poslání tweetu, který je použit v mém konečném programu. Ke komunikaci je použita knihovna *tweepy*. Nejdříve se musí vytvořit rozhraní pomocí přístupových klíčů, které jsem dostal při vytváření aplikace (viz kapitola :ref:`registrace-aplikace-na-twitteru`), díky tomu je možné posílat tweety, nebo je popřípadě i číst. 


.. code-block:: python

	#!/usr/bin/env python
	# -*- coding: utf-8 -*-

	import tweepy        # knihovna pro komunikaci s twitterem
        import time, sys

	CONSUMER_KEY =  'xxxxxx'
	CONSUMER_SECRET = 'yyyyyyy'
	ACCESS_KEY = 'aaaaa'
	ACCESS_SECRET = 'bbbbbbb'
        
        # prihlaseni k twitteru  
	auth = tweepy.OAuthHandler(CONSUMER_KEY, CONSUMER_SECRET)
	auth.set_access_token(ACCESS_KEY, ACCESS_SECRET)
	api = tweepy.API(auth)

	# poslani tweetu
	api.update_status(status='Test tweet z automatu!')


=============================
Struktura a funkce programu 
=============================

Program jsem rozdělil na dvě části. První část (*kytkajitka_all2.py*) má za úkol zpracování hodnot z čidel, jejich kategorizaci a ukládání průběhu veličin. Druhá část (*kytkajitka_all-tweet.py*) je periodicky spouštěna pomocí systémové služby *Cron* a má za úkol zpracovat kategorie na příslušný text a ten odeslat na Twitter. Hlídá při tom, aby se zprávy odesílaly pouze při změně kategorie a text zpráv se neopakoval. Struktura programu je znázorněna na následujícím obrázku.

.. figure:: /images/schema_programu.png
   :scale: 50%
   :alt: Schéma programu.

   Schéma programu.

.. _mereni-a-kategorie-hodnot:

----------------------------------
Měření a kategorie hodnot
----------------------------------

Měření hodnot zajišťuje program *kytkajitka_all2.py* pomocí funkcí popsaných v kapitole :ref:`komunikace-se-senzorem`. Kromě toho získané výsledky zapisuje do souboru *hodnoty.csv* a také tyto výsledky při každé změně kategorizuje. Každá kategorie má svůj rozsah hodnot. Kategorie pro každou veličinu (teplota, vlhkost, osvit) se zapisuje spolu s pořadovým číslem změny (od 1 do 3) do souboru *kategorie.txt*.  Roztřídění do kategorií je vyřešeno jednoduše pomocí konstrukce if-else. V příkladu je ukázán výpočet kategorie pro teplotu.

.. code-block:: python

	def zpracTemp(temp):
		if temp < 2:
		  katt = 1
		elif temp < 10:
		  katt = 2
		elif temp < 15:
		  katt = 3 
		elif temp < 20:
		  katt = 4
		elif temp < 22:
		  katt = 5
		elif temp < 25:
		  katt = 6
		elif temp < 30:
		  katt = 7
		else:
		  katt = 8
		return(katt)

Soubor *kategorie.txt* je pak následně otvírán v druhém programu *kytkajitka_all-tweet.py*, který provádí převod na text a zaslání tweetu.

--------------------------------
Zasílání tweetů
--------------------------------
Zasílání tweetů na Twitter zajišťuje program *kytkajitka_all-tweet.py*. Program načte soubor *kategorie.txt* a zjistí, jestli nastala změna alespoň v jedné kategorii oproti poslednímu tweetu, který je uložen v souboru *tweettime.txt*. Když došlo ke změně, vybere pro každou změněnou kategorii jeden z přednastavených textů odpovídajících výsledné kategorii. 

Podle toho, v kolika veličinách došlo ke změně, tolik zpráv bude obsahovat příslušný tweet. Výběr jednoho ze tří textů pro každou kategorii se provádí podle pořadí změny, které je uloženo v |_| souboru *kategorie.txt*. Výslednou zprávu pošle program pomocí kódu popsaného v kapitole :ref:`komunikace-s-twitterem` na Twitter a zároveň zapíše tweetované kategorie a čas do souboru *tweettime.txt*. Nakonec provede záznam do logovacího souboru *log.txt*. 

Program *kytkajitka_all-tweet.py* není spuštěný nepřetržitě, ale je spouštěn pomocí systémové služby Cron jednou za 10 minut.

------------------------------------
Kategorie hodnot a přiřazené texty
------------------------------------

V této kapitole jsou uvedeny kategorie hodnot a jim přiřazené texty, které se používají při sestavování tweetů.

^^^^^^^^^^^^^^^^^^
Kategorie teploty
^^^^^^^^^^^^^^^^^^

Pro teplotu jsem zvolil následujících osm kategorií:

* Teplota menší než 2 °C - kategorie 1 

  * text 1: Já tady mrznu!!!
  * text 2: Už mám na sobě jinovatku!!!
  * text 3: Mám omrzliny 3. stupně!!!

* Teplota 2 až 10 °C - kategorie 2

  * text 1: Je mi zzzzzzzima!!
  * text 2: Brrrrrrrrrrrr!!
  * text 3: Větší zima už snad být nemůže!!!

* Teplota 10 až 15°C - kategorie 3

  * text 1: Mohlo by se trochu přitopit.
  * text 2: Trochu chladno.
  * text 3: Už bylo tepleji.

* Teplota 15 až 20 °C - kategorie 4

  * text 1: Je tady čerstvo.
  * text 2: Žádné velké teplo tady není.
  * text 3: Radši bych ještě přitopila.

* Teplota 20 až 22 °C -kategorie 5

  * text 1: Teplota mi vyhovuje.
  * text 2: Ani teplo, ani zima.
  * text 3: Teplota je příjemná.

* Teplota 22 až 25 °C - kategorie 6

  * text 1: Je mi trochu teplo.
  * text 2: Začínám se potit.
  * text 3: Teploučko.

* Teplota 25 až 30 °C - kategorie 7

  * text 1: Je mi hic!
  * text 2: Dala bych si zmrzlinu.
  * text 3: Na takové vedro nejsem zvyklá.

* Teplota větší než 30 °C - kategorie 8

  * text 1: Úplná sahara!!!
  * text 2: Asi zvadnu horkem!!!
  * text 3: Já se snad uvařím!!!

^^^^^^^^^^^^^^^^^^^^
Kategorie vlhkosti
^^^^^^^^^^^^^^^^^^^^

Hodnoty vlhkosti [#p1]_ jsem rodělil do pěti kategorií:

* Vlhkost menší než 250 - kategorie 1

  * text 1: Usycháám!!!
  * text 2: Uschnu na troud!!!
  * text 3: Alespoň kapku vody nebo je po mně!!!

* Vlhkost 250 až 300 - kategorie 2

  * text 1: Mám velkou žízeň.
  * text 2: Napila bych se.
  * text 3: Mám sucho v puse.

* Vlhkost 300 až 400 - kategorie 3 

  * text 1: Jsem napitá tak akorát.
  * text 2: Nemám žízeň.
  * text 3: Vody bylo dost.

* Vlhkost 400 až 450 - kategorie 4

  * text 1: Chceš mě utopit?
  * text 2: Už nezalávat.
  * text 3: Čvachtá mi pod kořeny.

* Vlhkost větší než 450 - kategorie 5

  * text 1: Neumím plavat!!!
  * text 2: Mám vody po krk!!
  * text 3: Topím se!!!

^^^^^^^^^^^^^^^^^^^^^^^^^
Kategorie osvitu
^^^^^^^^^^^^^^^^^^^^^^^^^

Pro osvit jsem zvolil pouze dvě kategorie s významem den a noc:

* Osvit menší než 32767 - kategorie 1
  
  * text 1: Dobrý den.
  * text 2: To jsem se vyspala.
  * text 3: Hurá na fotosyntézu.

* Osvit větší než 32767 - kategorie 2

  * text 1: Dobrou noc.
  * text 2: Už jdu do postele
  * text 3: Už je po večerníčku.


.. rubric:: Poznámky pod čarou

.. [#p1] Vztah snímaných hodnot a skutečné vlhkosti je popsán v kapitole :ref:`mereni-vlhkosti-pudy`
