**************************
Programovací jazyk Python
**************************

.. index:: Python

Python je víceúčelový vysokoúrovňový skriptovací programovací jazyk. Byl vytvořen v roce 1991 Guido van Rossumem. Python je dostupný na většině operačních systémů, ve většině distribucí Linuxu je Python součástí základní instalace [#p1]_.  


.. figure:: /images/Python.png
   :scale: 25%
   :alt: Logo Python

   Logo programovacího jazyka Python.



Programovací jazyk Python vyniká zejména: 

* jednoduchou syntaxí - snadno se učí, velice vhodný pro začátečníky
* univerzálností - lze použít jak pro krátké programy, tak i pro rozsáhlé aplikace
* obsáhlou nabídkou knihoven - umožňuje rychlý vývoj aplikací
* podporou objektového progamování - není vyžadováno, programy ho nemusí využívat


Jako příklad kódu v různých programovacích jazycích se používá výpis známé věty *Hello World!* (Ahoj světe!). V pythonu, jak je vidět, je to velice jednoduché.

.. code-block:: python

   # vypíše na obrazovku 
   print "Hello World!"

Python, narozdíl od jiných programovacích jazyků, vyžaduje *strukturované programování*. To znamená, že jednotlivé programové bloky musejí být náležitě odsazeny. To významně přispívá ke zpřehlednění a čitelnosti zápisu programu, jak je patrno na následující ukázce.

.. code-block:: python

   def priklad(text, podminka_text):
     """Příklad procedury pro výpis textu."""
     if podminka_text:
       print text
     else
       print "Žádný text."
     return  
   
   text = input("Zadejte text.")
   priklad(text, True)
   
   """Toto je ukázka vnořených struktur Pythonu."""


Pro projekt Kytka Jitka jsem použil Python verze 2.7. Ve všech ukázkách kódu v této dokumentaci byla použita tato verze.

Většinu svých znalostí jazyka Python využitých v tomto projektu jsem  čerpal z knihy *Začínáme programovat v jazyce Python* (:cite:`2003:Harms`).


.. rubric:: Poznámky pod čarou

.. [#p1] Python je i součástí distribuce Raspbian, která se používá v Raspberry Pi.



