********************
Popis hardware
********************

V této kapitole je popsán hardware, který jsem použil ve svém projektu. Zároveň jsou zde popsány principy funkce použitého senzoru.

======================
Raspberry Pi
======================

.. index:: Raspberry Pi, GPIO

.. |_| unicode:: 0xA0
   :trim:

Raspberry Pi je malý počítač s ARM procesorem, který byl vyvinut se záměrem vzbudit u dětí a |_| mládeže zájem o programování. Krátce po jeho uvedení na trh se však vytvořila velká komunita uživatelů, kteří Raspberry Pi používají jako univerzální počítač pro nejrůznější projekty. K jednoduché komunikaci slouží GPIO [#p3]_ sběrnice, jejíž součástí je i I2C [#p4]_ sběrnice, na kterou se dá připojit velké množství doplňků. Dále obsahuje HDMI konektor k připojení na monitor nebo televizi a také USB a ethernetový adaptér. Pevný disk je nahrazen SD kartou, na které je uložen jak operační systém, tak uživatelská data. V mém projektu je využito Raspberry Pi Model B.

.. tabularcolumns:: |p{2.5cm}|p{7cm}|p{5cm}| 

.. list-table:: Technické parametry Raspberry Pi model B
   :header-rows: 1

   * - Parametr
     - Hodnota
     - Poznámka  
   * - Rozměry
     - 85.60 mm × 56.5 mm
     - 
   * - Paměť RAM 
     - 512 MB
     - Sdíleno s grafickou kartou 
   * - Procesor
     - ARM 11 
     - ARM1176JZF-S (1 jádro)
   * - Frekvence
     - 700 MHz
     - 
   * - Porty 
     - 2x USB, 1x ethernet, 1x HDMI, 1x 3,5 mm audio, 1x composite video
     - 
   * - Napájení
     - 5 V, 700 mA
     - konektor micro USB



.. figure:: /images/Raspberry_Pi_popis.jpg
   :scale: 100%
   :alt: Obrázek Raspberry pi.

   Minipočitač Raspberry Pi použitý v projektu Kytka Jitka.

.. _senzor:

======================
Senzor
======================

.. index:: Senzor

.. index:: I2C

Vybral jsem si senzor *Soil moisture senzor*, protože umožňuje měřit všechny tři veličiny (vlhkost, teplotu a osvit). Navíc umožňuje přenos naměřených hodnot po sběrnici I2C, což zjednodušuje připojení k Raspberry Pi [#p1]_. Podrobný popis, schéma zapojení a vzorové příklady komunikace s čidlem jsou uvedeny na stránce :cite:`I2Csenzor`. 

.. figure:: /images/senzor.JPG
   :scale: 50%
   :alt: Obrázek senzoru

   Použitý senzor vlhkosti, teploty a osvětlení.

.. _mereni-vlhkosti-pudy:

----------------------------
Měření vlhkosti půdy
----------------------------

Měření půdní vlhkosti je velice složité a náročné. Existuje mnoho způsobů rozdělení, například: 

* destruktivní a nedestruktivní (podle toho, jestli je nutné odebrat půdní vzorek)
* přímé a nepřímé (podle toho, jestli se měří přímo vlhkost nebo veličina na vlhkosti závislá. Nepřímé metody vyžadují kalibraci)

Senzor, který jsem použil v tomto projektu, používá nedestruktivní, nepřímou metodu měření. Jedná se o metodu označovanou jako Frequency Domain (FD) Capacitance, která spočívá v |_| tom, že elektrická kapacita kondenzátoru, kde je jako dielektrikum použito půdy, je závislá na půdní vlhkosti. 

Procesor v senzoru nabije kondenzátor (dvě kovové destičky a půda okolo) a měří, jak dlouho to trvalo. Čas závisí na vlastnostech dielektrika, tedy vlhkosti půdy. Čím je čas potřebný k |_| nabití kondenzátoru kratší, tím je půda sušší. Měření je však ovlivňováno i jinými faktory jako například teplotou nebo složením půdy. Hodnoty vracené čidlem jsou relativní (bezrozměrné). Proto pro zjištění skutečné vlhkosti půdy je nezbytná kalibrace čidla. 

Protože pro potřeby projektu není důležitá skutečná hodnota vlhkosti půdy, provedl jsem pouze přibližnou kalibraci zrakem a hmatem. Jako vlhkou jsem vyhodnotil půdu při hodnotách 450 až 470, jako suchou půdu při hodnotách 300 a nižších. 


----------------------------
Měření teploty
----------------------------

Teplota se měří pomocí termistoru [#p2]_, který je umístěn na dolním konci čidla. Procesor v čidle provádí převod naměřené hodnoty odporu přímo na stupně Celsia. Hodnotu získanou z čidla je nutno podělit deseti, protože čidlo předává hodnotu teploty v desetinách °C (například teplotu 22,4 předává čidlo jako hodnotu 224).


----------------------------
Měření osvětlení
----------------------------

Osvit se měří pomocí LED [#p5]_ čidla. Měření osvitu neprobíhá nepřetržitě, ale je nutno ho programově spustit. Výsledný osvit je možno získat po uplynutí zadané doby (nejméně 3 sekundy). Čidlo vrací hodnoty 0 až 65535, nižší hodnoty při vyšším osvitu.




======================
Propojení
======================

K propojení čidla s Raspberry Pi jsem použil plochý kabel se šesti vodiči, na který jsem připevnil samořezné konektory. Na čidlo jsem připájel šestipólový konektor s kolíčky, do kterého se zasunuje konektor na plochém kabelu. Čidlo s kabelem je vidět na následujícím obrázku.
 

.. figure:: /images/Propojeni1.JPG
   :scale: 65%
   :alt: Obrazek Propojeni

   Propojeni čidla s Raspberry Pi


.. figure:: /images/schema_kabelu.png
   :scale: 50%
   :alt: Schema kabelu

   Schéma propojovacího kabelu.


K propojení kabelu s Raspberry Pi jsem vyrobil propojovací desku, která vytváří propojení příslušných signálů na konektoru Raspberry Pi se signály z čidla. Čidlo ke své činnosti potřebuje napájení (VCC, GND) [#p6]_ a signály sběrnice I2C (SCL, SDA) [#p7]_. Z toho důvodu, že Raspberry Pi má vnější signály přivedeny na 26 bitový konektor, na kterém jsou potřebné signály umístěny jinak než na čidle, musel jsem je propojit pomocí samopájitelného drátu. Propojovací deska je přímo zasunuta do konektoru Raspberry Pi a zajišťuje tak spolehlivé propojení signálů. 



 
.. figure:: /images/Propojeni2.JPG
   :scale: 40%
   :alt: Obrazek Propojeni

   Propojovací deska - strana konektorů.



.. figure:: /images/Propojeni3.JPG
   :scale: 40%
   :alt: Obrazek Propojeni

   Propojovací deska - strana drátů.

Propojovací desku jsem zhotovil z části univerzálního plošného spoje a dvou konektorů. Ty jsem propojil pomocí páječky a samopájitelného drátu. Schéma propojení je na následujícím obrázku.


.. figure:: /images/schema_desky.png
   :scale: 60%
   :alt: Schema desky

   Schéma propojovací desky.






.. rubric:: Poznámky pod čarou

.. [#p1] Ke zpracování signálů z čidla není potřeba Raspberry Pi rozšiřovat o analogově číslicový převodník, protože je už obsažen v elektronice čidla.

.. [#p2] Odpor který mění svoji velikost na základě změny teploty.

.. [#p3] GPIO nebo General Purpose Input/Output jsou piny, tedy vývody, které lze programovat pomocí softwaru. Do těchto pinů lze posílat elektrický signál nebo jej z nich naopak přijímat, tím lze pracovat s připojeným hardware na nejnižší úrovni.

.. [#p4] I2C je sériová počítačová sběrnice vyvinutá firmou Phillips pro připojování periferií k základní desce.

.. [#p5] Light Emitting Diode - elektronická součástka vyzařující světlo. 

.. [#p6] VCC - napájecí napětí, GND - společný vodič (zem)

.. [#p7] SCL - hodinový signál, SDA - sériová data

