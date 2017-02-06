*************************************
Postup práce
*************************************


Na projektu jsem pracoval přibližně od srpna roku 2015. Nejprve jsem zprovoznil počítač Raspberry Pi a seznamoval se se základy jazyka Python. Současně jsem zjišťoval možnosti komunikace s Twitterem a hledal vhodné čidlo. Poté, co se mi podařilo objevit pythonovskou knihovnu Tweepy, založil jsem si uživatelský účet na Twitteru a pokusil se vygenerovat první tweet. 

Dlouho se mi nedařilo nalézt vhodný senzor vlhkosti. Naprostá většina senzorů na internetu je odporového typu, který trpí korozí a negativně ovlivňuje půdu rostliny. U litevského výrobce jsem objevil senzor pracující na kapacitním principu (viz kapitola :ref:`senzor`) a kromě vlhkosti umožňuje i měření teploty a osvitu. Po obdržení senzoru jsem začal zkoušet komunikaci. Pro spojení Raspberry Pi a senzoru jsem vytvořil kabel a propojovací desku. Když se mi podařilo přečíst první hodnoty ze senzoru, vytvořil jsem jednoduchý program, který zapisoval hodnoty do souboru. Po vykreslení grafů jsem zjistil, že hodnoty osvitu neodpovídaly skutečnému osvětlení, vlhkost i teplota byly v pořádku. Dlouho jsem nemohl odhalit příčinu. Problém jsem vyřešil zvýšením napájecího napětí čidla z 3.3V na 5V úpravou propojovací desky. 

Po zprovoznění správné komunikace se senzorem jsem měl pocit, že práce je téměř hotová, neboť zasílání tweetů mi už také fungovalo. Přesto jsem však ještě strávil mnoho času sestavováním tweetů z naměřených hodnot a psaním dokumentace.

