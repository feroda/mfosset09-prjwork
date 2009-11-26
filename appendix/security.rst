Appendice B - analisi della sicurezza di SANET
==============================================

In questa appendice si vuole effettuare una breve disamina delle problematiche di sicurezza di SANET.

SANET è un software Web il che vuol dire tutto e non vuol dire niente. 
Diciamo che dialoga all'esterno tramite il protocollo HTTP.

Oltre ad ascoltare richieste HTTP, SANET effettua disparate richieste sulla rete da cui si aspetta
determinate risposte. Tali richieste vengono fatte molto spesso tramite protocollo SNMP,
ma è altrettanto possibile che siano implementati comandi ad-hoc per interagire con qualunque altro esotico servizio.

Infine SANET dispone della CLI di configurazione che è accessibile in locale 
e per il quale si applica una politica di `trust` degli utenti del sistema.

Quindi gli attacchi esterni possono provenire da:

* richieste malformate veicolate sulle porte HTTP
* informazioni ricevute in risposta alle richieste effettuate per la rete monitorata

Mentre gli attacchi interni da:

* `code injection` in comandi impartiti nella CLI locale
* `sql injection` nel database locale

oltre ai più classici `Denial Of Service` per l'esterno e `bug` di libreria di cui segnaleremo solo un caso critico.

Attacchi esterni
----------------

Iniziamo col dire che SANET soffre ancora di problemi di performance. È un sistema `CPU-bound` 
(oltre che `Network-bound` per quello che riguarda il monitoraggio delle condizioni di rete), per cui non è difficile realizzare un attacco
Denial Of Service e fare in modo che altri utenti non possano accedere al sistema di monitoraggio e visualizzare 
lo stato della rete. 

Tolto il problema del Denial Of Service bisogna considerare il tipo di operazioni che vengono esposte via web:

* richiesta generica di un url
* salvataggio note e posizioni degli elementi nella mappa: queste operazioni scrivono dati nello RDBMS

La richiesta generica di un url potrebbe dare problemi in caso di pacchetto HTTP malformato,
oppure di corpo della richiesta forgiato ad-hoc per operazioni di code injection.

SANET è basato su Django che nelle installazioni in produzione è gestito tramite l'handler `mod_python` di Apache.
Quindi per quello che riguarda i livelli ISO/OSI fino al livello HTTP , la sicurezza è garantita dall'implementazione 
dello stack TCP/IP del sistema operativo di installazione (GNU/Linux per noi), mentre per il livello HTTP abbiamo Apache Web Server.

Il contenuto del pacchetto HTTP invece viene trasmesso a livello applicativo anche in questo caso molto robusto:

* del web server non occorre dire altro
* Django dispone di un ottimo sistema di quoting e sanitizzazione dell'input grazie alla libreria cgi di Python
* inoltre i parametri valorizzati compilando i `form` nella canonica interfaccia web, vengono serializzati tramite la libreria `Javascript Prototype` che ne garantisce il corretto quoting nell'url della richiesta in caso di una `GET`, o nell'url e nel `payload` in caso di una `POST` (operazioni HTTP più comuni). Certo, questa è una considerazione esclusivamente marginale se si pensa che in un attacco informatico di solito l'input non viene fornito tramite l'interfaccia web applicativa.

Rimane la possibilità di iniezione di codice attraverso risposte malformate alle richieste di monitoraggio di `poller`.
In questo caso le richieste SNMP sono state incapsulate e attualmente si appoggiano ai binding python di NetSNMP.
La nota positiva è che essendo incapsulati possono essere facilmente rimpiazzati da altri moduli che implementano richieste SNMP,
la nota negativa è che ogni tanto l'implementazione attuale raramente va in `Segmentation fault`. Questo problema si è verificato
per siti molto grandi, dove incide molto la latenza della rete nell'effettuazione dei controlli.

Dopo una indagine approfondita del problema non se ne è riuscita ad individuarne la causa, e il problema (che io sappia) 
non è stato sottoposto alla comunità di sviluppo per mancanza di informazioni sulla riproducibilità e la particolarità delle
reti in cui questo problema si verifica. È stato comunque realizzato un `kludge` con un processo padre che ha il solo compito
di supervisionare l'esistenza del figlio che effettua i controlli di rete e riavviarlo in caso non esista più.

Gli altri controlli di rete sono implementati da funzioni apposite e il parsing dei risultati effettuato di volta in volta
a seconda del tipo di controllo configurato. In genere il valore di ritorno viene considerato come stringa o come uno specifico
tipo di dato dipendentemente dall'operatore definito nel controllo e relativamente a questo correttamente interpretato. 
In ogni caso mai valutato. 

Il sistema non riceve trap SNMP dagli apparati.

Non si può chiudere la sezione sugli attacchi esterni senza porre in evidenza il rischio relativo alla compromissione del DNS
cui `poller` dirige numerose richieste. Se infatti venisse compromesso il DNS interrogato, si potrebbero indirizzare a piacimento
tutte queste richieste ad esempio ad un singolo host e generare un DoS. Questo considerato anche che in reti mediamente
complesse il processo dispone di circa 40 thread che fanno richieste in parallelo.


Attacchi interni
----------------

Come si accennava prima, è stata adottata una politica di sicurezza `trust` per gli utenti del sistema locale,
quindi è ovvio che se qualcuno riuscisse ad entrare nel sistema con un account autorizzato, molto probabilmente 
riuscirà a far fare a SANET quello che vorrà.

Ma l'altra domanda che ci si pone è se SANET possa o meno presentare un pericolo reale per il sistema,
o meglio, se sia possibile ottenere un accesso privilegiato sfruttando un problema di sicurezza di SANET.

Una prima considerazione da fare è che il `poller` viene eseguito come utente `root` del sistema,
perché deve poter forgiare pacchetti RAW per fare ICMP ECHO REQUEST (ping). Questo già potrebbe essere 
facilmente migliorabile nel caso si avesse a disposizione un kernel Linux che supporti le `capabilities`
e in particolare si potrebbe creare un account con la capability `CAP_NET_RAW`.

Il fatto che tutti i controlli di rete vengano eseguiti da un processo con utente `root`, 
rende il sistema nudo di fronte a un exploit del `poller`.

Inoltre è necessario porre particolare attenzione alla configurazione della access list del database:
se l'utente non privilegiato può scrivere la configurazione di SANET nel database (tramite la CLI che non
ha alcun controllo all'accesso), a questo punto l'attacker può configurare SANET , e quindi il `poller`
per i propri fini.

Le espressioni di controllo dello stato dispongono di un proprio linguaggio con un parser sviluppato ad-hoc
e quindi si può affermare che sono protette da iniezione di codice, però ci sono particolari espressioni (i.e: `wmic` ed `exec`),
che eseguono programmi esterni. È necessario verificare che le directory e i file che sono già presenti e pronti
per essere eseguiti, non siano modificabili dall'utente non privilegiato, che altrimenti troverebbe una facile scorciatoia
per eseguire codice arbitrario nel sistema.

Infine è doveroso considerare che la macchina su cui viene eseguito il processo `poller` deve, per definizione, 
raggiungere numerosi apparati di rete e server, più di quanto tipicamente possa fare un server qualsiasi nella rete del cliente:
ciò rende più appetibile questa macchina come bersaglio per chi voglia usarla come testa di ponte per altri attacchi
o attività di ingegneria sociale.

Concludendo
-----------

SANET è un sistema protetto dagli attacchi esterni, ma molto vulnerabile dagli attacchi interni.
Per questo si consiglia di ridurre i privilegi dell'utente di esecuzione del `poller` sfruttando la capability `CAP_NET_RAW`,
oppure installando il sistema di monitoraggio in una macchina virtuale eseguita con i diritti di utente non privilegiato
e con un proprio stack TCP/IP con cui produrre i pacchetti RAW di cui necessita per il corretto funzionamento.





