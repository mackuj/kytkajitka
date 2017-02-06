*************************************
Dosažené výsledky a praktické využití
*************************************

V této kapitole jsou popsány výsledky, kterých se mi podařilo dosáhnout v tomto školním projektu, jejich možné praktické využití a další možný rozvoj projektu. 

===========================
Komunikace se senzorem
===========================

Komunikace se senzorem běží stabilně a bez potíží a naměřené hodnoty jsou pro kontrolu ukládány do soubory *hodnoty.csv*, ze kterého jsou posléze vytvářeny grafy (viz kapitola :ref:`namerene-hodnoty`). Pro vytvoření grafů jsem použil program gnuplot 4.6.

========================================
Převod hodnot z čidla na vhodné texty
========================================

Převod naměřených hodnot na text tweetu je pováděn přes kategorie (viz kapitola :ref:`mereni-a-kategorie-hodnot`). Způsob, kterým se zprávy generují, zabraňuje tomu, aby byl účet na Twitteru z důvodu častého opakování stejných zpráv zablokován. Před ošetřením této chyby jsem dostával hlášení od Twitteru za spam a zasílání dalších zpráv bylo blokováno.

================================
Automatické zasílání tweetů
================================

Jeden z problémů, který jsem musel vyřešit ve své práci bylo, jak posílat automaticky tweety. Já jsem ho vyřešil pomocí rozhraní nabízeného přímo twitterem pro vytváření aplikací a pythonovskou knihovnou tweepy. Automaticky zaslané tweety jsou vidět na obrázku. Veškeré tweety jsou ke shlédnutí na Twittru na adrese https://twitter.com/kytka_jitka.

.. figure:: /images/Tweety.png
   :scale: 50%
   :alt: Stránka s Tweety.

   Stránka s Tweety.


============================
Praktické využití
============================

Praktické využití mého projektu je v tom, že skrze něj můžete sledovat svoji rostlinu a tím značně usnadnit péči o ni. Dále má praktický význam v technologii inteligentní domácnosti a mohl by, po několika úpravách, pečovat o rostlinu sám, místo toho, aby o jejím stavu pouze informoval uživatele na sociální síti.

==========================
Možný další rozvoj
==========================

Můj projekt by mohl být vylepšen o další části tak, aby byl naprosto soběstačný. To znamená, aby se dokázal sám starat o rostlinu a sám řešil problémy jako nedostatek světla, velké sucho, nízká teplota a podobně. Rozšířením projektu lze zařízení upravit tak, aby zprávy na sociální síť byly nejen zasílány, ale i příjimány. Na základě přijatých zpráv by pak bylo možné automaticky vykonávat požadované úkoly (v mém případě zalití rostliny). 






