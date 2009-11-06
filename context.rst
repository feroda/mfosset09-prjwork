In quale contesto nasce l'idea di SANET ?
=========================================

In questo capitolo vedremo in quale contesto nasce SANET.
Quando si è deciso di svilupparlo, chi lo ha deciso.

Cosa si è voluto realizzare e da cosa si è partiti, 
e perché è stato avviato un nuovo progetto evitando di
adattare un programma open source esistente alle proprie esigenze.

Chi
---

LABS è un'azienda che si occupa di reti. Segue il processo di messa in funzione di una rete
a partire dalla progettazione dell'infrastruttura fisica, per poi passare al livello rete,
fino ad arrivare ai servizi.

Il cosiddetto `core business` dei LABS è l'assistenza sulle reti. Le reti hanno bisogno
di monitoraggio e manutenzione continui e in reti complesse non si può fare a meno
di avere tecnici specializzati per risolvere le più disparate problematiche che si presentano.

I problemi si possono verificare ad esempio a causa di:

* deperimento dell'hardware
* worm di rete
+ cause estemporanee (TODO: esempio ntp ?)
* retaggi del passato

Da cosa si parte
----------------

Il sistema di monitoraggio preesistente prendeva il nome di `pinger` ed era un monolitico script in Perl
che disponeva di un linguaggio per la definizione dei controlli.

Il linguaggio era abbastanza basilare, ma consentiva di definire modelli abbastanza comodi
per istruire il sistema su una serie di controlli da effettuare.

Il pregio di questo software era proprio nella definizione duttile e dettagliata dei parametri
da verificare, a quali intervalli di tempo, per quale soglia di tolleranza e a chi veicolare le segnalazioni.
Tutto ciò in un modo relativamente semplice.

I difetti invece si possono riassumere in:

* incomprensibilità dei risultati dell'attività di monitoraggio per il cliente
* interfaccia grafica pressoché inesistente
* caricamento in memoria di tutti i controlli da effettuare e riavvio del software in caso di modifica dei controlli
* mancanza di `escalation` ossia di dipendenza dei controlli e quindi flood di allarmi
* limitazioni varie dovute alla ridotta espressività del linguaggio e del parsing dello stesso
* multiprocesso, ma non multithread con conseguente sovraccarico di memoria
* parsing dei controlli non resistente ad injection


Cosa
----

Quando
------

Nel 2005 io, Luca Ferroni, venni assunto ai LABS con lo scopo di migliorare 
il sistema di monitoraggio esistente e già in uso presso questa azienda dal TODO.

Dopo aver preso parte ad alcuni lavori in essere, mi sono dedicato alla progettazione del nuovo sistema
insieme con l'Ing. Michele Bergonzoni esperto di reti dell'azienda e autore dello strumento preesistente.

Da quel momento in poi il lavoro è stato più o meno seguito da me 


Perché
------

TODO: superare i limiti di pinger mantenendo il know-how LABS
TODO: niente di simile. Prendere email segnalata da bergonz

