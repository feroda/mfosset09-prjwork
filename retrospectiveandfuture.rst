Verifica e prospettive
======================

Indubbiamente SANET esiste, funziona e tecnicamente si può dire che è 
uno degli strumenti più avanzati nel panorama dei Network Management System
Open Source.

Sicuramente può e deve ancora migliorare sotto l'aspetto della comunità,
delle performance, della distribuzione degli agenti, della personalizzazione
degli eventi.

Potrebbe anche interagire con sistemi di ticketing per la gestione
del workflow di risoluzione degli allarmi.

In questo capitolo si intende proporre una verifica 
e analizzare alcuni possibili prospettive future 
relativamente al processo di sviluppo e rilascio e agli attori
attualmente interessati al buon funzionamento e al miglioramento di SANET.

Verifica
--------

Guardarsi alle spalle dopo qualche anno di lavoro non è semplice.
Neanche le dinamiche che hanno portato all'evoluzione di SANET non sono semplici,
come non sono semplici le dinamiche della comunità Open Source.

Vivendo dall'interno questo processo ci si rende conto che alla fine
le uniche cose che contano veramente sono le priorità aziendali.
Chi è nel mondo dell'Open Source da anni non può non sapere che è questa 
la regola che vale per i prodotti di successo. È il trionfo di un sano liberismo
in cui le priorità aziendali sono anche quelle che portano a un miglioramento sociale
e comunitario.

Purtroppo le priorità aziendali, accompagnate da un contesto sociale in cui la 
mancanza di tempo e la fretta predominano, a volte portano a compiere scelte
affrettate e si perdono inevitabilmente occasioni di crescita derivanti da un confronto
più approfondito con lo stato dell'arte e la comunità del software libero.

Nel complesso io credo che la forte esperienza dei LABS nella gestione delle reti e il 
precedente sistema di monitoraggio `pinger`, abbiano giocato un ruolo fondamentale nella 
decisione di non puntare ad integrarsi a soluzioni esistenti. 

Questa scelta potrebbe essere criticata anche considerando che LABS storicamente non è un'azienda
di sviluppo software e quindi sarebbe stato più produttivo avvalersi della forza della comunità
invece che creare e mantenere un nuovo programma. 
Allo stesso modo potrei dire che con una unità di sviluppatori più corposa si sarebbe potuto indagare meglio 
sulle peculiarità degli altri sistemi di monitoraggio perché si sa ... 
la barriera di entrata è più alta se si deve entrare in un progetto avviato.
Soprattutto se ci si deve entrare con un know-how importante da integrare.

Considerati questi aspetti, sebbene io stesso fossi promotore di un approccio più aperto
e più integrato con la comunità, comprendo le scelte che l'azienda ha fatto.

Ora che il prodotto è maturo per un uso interno, credo che sia il momento di aprirsi in modo 
più deciso alla comunità. In questo modo ci si potrebbe avvalere anche di collaboratori esterni interessati allo sviluppo
di SANET che potrebbero apportare contributi in documentazione, codice, pubblicità o nuove idee senza gravare sui costi aziendali.

Riguardo alla tecnologia usata ritengo che siano state fatte scelte appropriate: 

* Python è un linguaggio di programmazione versatile e veloce che consente programmazione funzionale, ad oggetti e a metaclassi
* Django è un framework web che non snatura python e ne sfrutta ottimamente le potenzialità
* PostgreSQL è potente e comunque la scelta di appoggiarsi ad un RDBMS è di indubbia utilità
* Flash, per quanto riproducibile ottimamente solo con il plugin proprietario di Adobe è stupefacente
* RRD per le serie temporali e il consolidamento dei dati
* RSS + XML-RPC e folksonomie sono tecnicismi del Web 2.0 che adattano SANET anche alla consultazione tramite strumenti esterni

Prospettive
-----------

.. ref future 

Un altro punto su cui bisogna porre molta attenzione sono secondo me le prospettive si aprono a partire da uno strumento
open source creato a cattedrale da un'azienda e rilasciato alla comunità.

Cosa succederà ?

Il rilascio sicuramente è ancora una scommessa, per l'azienda e per me che sono stato uno dei principali autori di SANET.
Date le logiche aziendali che hanno condotto fin qui lo sviluppo, 
non mi sembra che sia fra le priorità dell'azienda far crescere la comunità di sviluppo intorno a SANET.

Ma il punto è un altro: SANET nasce come prodotto all'interno di LABS. Effettuando il rilascio alla comunità LABS,
pur non rinunciando al diritto (inalienabile) di paternità dell'opera, di fatto pone il prodotto al centro dell'interesse:
per accedere al prodotto, alla conoscenza e alle funzionalità da esso implementate non si deve più necessariamente
passare attraverso LABS.

È in questo, come si è ricordato più volte, che si denota una grande apertura di mentalità dell'azienda.

Ora, l'interesse è catalizzato dal prodotto e di fatto, ogni attore del mercato può `dargli una spinta` verso il miglioramento.

Ad esempio io ora , in questo momento, nella scrittura di questo progetto finale di Master, sto scrivendo una documentazione
sull'evoluzione di SANET, e ho realizzato la procedura di installazione del software pur non essendo in questo momento alle
dipendenze dell'azienda.

Poniamo caso che l'azienda `beFair <http://www.befair.it>` da me fondata nutra interesse per l'attività di monitoraggio delle reti,
o individui un'esigenza di un cliente che possa essere soddisfatta con degli aggiornamenti su SANET, ebbene, sarebbe interesse
dell'azienda beFair e dell'azienda LABS discutere del miglioramento ed integrarlo nella versione ufficiale di SANET.

Questo è il punto fondamentale e la grande apertura del rilascio alla comunità: siamo tutti curiosi di vedere 
come si evolverà questa situazione.


