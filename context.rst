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
* cause estemporanee (TODO: esempio ntp ?)
* retaggi del passato
* worm/attacchi nella rete

Da cosa si parte
----------------

Il sistema di monitoraggio preesistente prendeva il nome di `pinger` ed era un monolitico script in Perl
che disponeva di un linguaggio per la definizione dei controlli.

Il linguaggio era abbastanza basilare, ma consentiva di definire modelli abbastanza comodi
per istruire il sistema su una serie di controlli da effettuare.

Il pregio di questo software consisteva proprio nella definizione duttile e dettagliata dei parametri
da verificare per ogni controllo: a quali intervalli di tempo effettuarli, 
per quale soglia di tolleranza e a chi veicolare le segnalazioni.
Tutto ciò in un modo relativamente semplice.

I difetti invece si possono riassumere in:

* incomprensibilità dei risultati dell'attività di monitoraggio per il cliente
* interfaccia grafica pressoché inesistente
* caricamento in memoria di tutti i controlli da effettuare e riavvio del software in caso di modifica dei controlli
* mancanza di `escalation` ossia di dipendenza dei controlli e quindi flood di allarmi per un unico problema
* limitazioni varie dovute alla ridotta espressività del linguaggio e del parsing dello stesso
* multiprocesso, ma non multithread con conseguente sovraccarico di memoria
* parsing dei controlli non resistente ad injection

È da notare che il tutto si basava su GNU/Linux e strumenti open source 
e anche la realizzazione stessa rimaneva open: i clienti più smaliziati potevano 
anche andare a curiosare nel codice e suggerire modifiche oltre ovviamente a definire i propri controlli.

Tuttavia non si può dire che questa situazione fosse ben definita, dato che, di fatto `pinger` era da sempre 
installato su appliance LABS e LABS stesso non ha mai esplicitato la licenza dello strumento in quanto
era funzionale al servizio di assistenza delle reti che diveniva oggetto principale del contratto con il cliente.

Da questa considerazione si può desumere che LABS è sempre stato autore più o meno consapevole di software open,
ma non aveva ancora tenuto in considerazione la possibilità di condividere apertamente la propria
conoscenza, di creare una comunità intorno all'attività di monitoraggio delle reti che ha da sempre costituito
un punto di eccellenza per l'azienda.

Ciò ovviamente era anche dovuto all'assenza di un settore di sviluppo software e quindi al modo `artigianale`
in cui veniva sviluppato `pinger`. Per capire basta sapere che il versionamento del software avveniva tramite
`diff` e `patch` ... che, a onor del vero, lo stesso Linus Torvalds ritiene un meccanismo ben più evoluto di CVS
(si veda il video `Linus Torvalds on Git <http://www.youtube.com/>`__), ma che comunque rasenta la definizione di
versionamento.

In ultimo è necessario osservare che LABS già aveva avviato una piccola divisione di sviluppo software
per portare avanti altri progetti.

L'esigenza
----------

Le doti del sistema di monitoraggio in uso erano confermate dai casi di successo riscontrati
presso i clienti LABS che tipicamente hanno sempre presentato
infrastrutture di rete mediamente o molto complesse.

Da qui si è deciso di intraprendere una nuova strada, o meglio, di consolidare la strada già intrapresa:
reingegnerizzare il sistema di monitoraggio valorizzandone i pregi, superandone i limiti e colmandone le lacune.

Si sa che nello sviluppo software "l'appetito vien mangiando" e, anche se non si aveva l'ambizione
di realizzare il software di monitoraggio perfetto, 
si è capito che ristrutturando quello che già c'era a disposizione 
si sarebbe potuto:

* soddisfare nuove esigenze 
* ottenere uno strumento più facile da mantenere ed estendere
* potenziare l'espressività e la tassonomia dei controlli da effettuare
* adattarsi o integrare nuove tecnologie

Non a caso la soddisfazione di nuove esigenze del cliente è stato elencato al primo posto:
questa è la motivazione che spinge le aziende a investire e innovare.

Anche nel caso dei LABS, sebbene le motivazioni fossero molteplici, il momento dell'investimento,
il cosiddetto `trigger` è stata la richiesta sempre più insistente di un'interfaccia grafica
comprensibile: il cliente lamentava di non essere consapevole in alcun modo della situazione della propria rete
e che le informazioni estremamente dettagliate e tecniche fornite dalla scarna interfaccia di `pinger`
non fossero per lui di alcuna utilità.

Quindi la decisione è di aprire gli orizzonti, coinvolgere il gruppo di sviluppatori, 
e progettare un software per il monitoraggio delle reti che superasse i limiti di `pinger`
(con priorità per i limiti di rappresentazione grafica dei risultati) e mantenesse il know-how dell'azienda.

Trattandosi questa volta di un software vero e proprio più che di un insieme di script
si inizia a valutare la possibilità di rilasciare il lavoro alla comunità.
In azienda il terreno è fertile: si comprende l'importanza di condividere conoscenza,
la ricchezza del servizio, della cultura e della prestazione professionale
invece che il valore artificioso della licenza centellinata
e pure si coglie marginalmente il vantaggio reciproco (per l'azienda a la comunità) che ne sarebbe potuto derivare.

La strada del software libero non è già tracciata, ma inizia a diffondersi nell'aria.
Continuando con contratti sul servizio di assistenza alle reti, tra l'altro, 
non sarebbe sussistito alcun obbligo di rilascio del software: 
infatti tale software non sarebbe stato ceduto al cliente, ma sarebbe rimasto
uno strumento di proprietà dell'azienda per offrire il servizio concordato.

Inoltre la presentazione dei risultati sarebbe avvenuta tramite interfaccia web
e quindi anche in questo caso si sarebbe rimasti nel pieno della legittimità
a meno di integrare software basato su licenza `Affero GPLv3 <http://www.fsf.org>`__ o
con simili requisiti a tutela di libertà per l'utente.

Quando
------

Il lavoro di analisi, definizione obiettivi e progettazione della nuova architettura è
iniziato nel 2005. 

I più diffusi sistemi open source esistenti per il monitoraggio delle reti erano già
conosciuti dagli esperti di rete LABS e non si è ritenuto opportuno fare un'approfondita valutazione
di integrabilità con l'esistente.

In quel periodo `ZenOSS <http://zenoss.org>`__ , uno dei progetti open source più frequentati 
su `SourceForge.net <http://sourceforge.net>`__ e più attivi al momento della scrittura di questo documento, 
non era ancora stato avviato.

Quindi, considerando anche l'ampia base di controlli sviluppati dall'azienda, i casi di successo riscontrati
e le modalità operative assodate, ci si è diretti verso lo sviluppo di una nuova soluzione,
accettando in questo modo di non approfittare di comunità già consolidate sui temi del network management.

Questa scelta ha ovviamente avuto un impatto decisivo sull'impostazione dello sviluppo della nuova piattaforma
che voleva a questo punto evolvere dalla precedente senza rompere con il passato per continuare appunto,
a consolidare la strada già intrapresa.

Si presentava lo scenario di rimpiazzare in corsa: non interrompere il servizio, ma rimpiazzare gradualmente parti
di software migliorandone gradualmente la qualità complessiva.


