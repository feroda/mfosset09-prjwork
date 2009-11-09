Le fasi di sviluppo
===================

Evolvere un programma che porta con se i retaggi di un passato decennale
oltre che l'approccio prettamente pratico del sistemista non è un percorso semplice,
soprattutto se si intende reingegnerizzare il tutto in modo da minimizzare la rottura
con il passato e farne un software manutenibile in cui sia chiara la separazione delle componenti.

Si decide di partire con lo sviluppo di `SANET`.

Un primo tentativo fallito
--------------------------

In principio si è approcciato il problema in modo abbastanza superficiale:
presa coscienza delle parti da sostituire, si è scelto il linguaggio Python già
in uso nell'azienda per altri progetti e si è demandata la prima stesura del nuovo codice
ad un collaboratore temporaneo.

Questo approccio si è dimostrato fallimentare perché:

* aveva l'ambizione di sostituire in breve tempo tutta la vecchia piattaforma
* non si appoggiava ad alcun framework che potesse velocizzare lo sviluppo focalizzando l'attenzione
  sulle peculiarità di SANET
* basava la sua implementazione su uno storage in memoria e anche qui perdita di tempo
  ed energie in problemi di concorrenza e persistenza delle informazioni
* si voleva implementare una politica di ereditarietà molto complessa fra istanze madri, categorie,
  e istanze collegate: in pratica si stava riscrivendo un linguaggio a oggetti

e inoltre non possiamo trascurare che dal punto implementativo queste sfide erano state
sottovalutate dall'azienda che non ha dedicato risorse umane per seguire costantemente
il collaboratore temporaneo che si è trovato probabilmente smarrito con in mano un linguaggio
di programmazione che non conosceva e problematiche che non aveva mai affrontato dal punto
di vista pratico.

Fase 0: una migrazione graduale
-------------------------------

Una volta che la collaborazione temporanea è terminata, le persone coinvolte nel progetto
si sono confrontate e, considerata la situazione, si è scelto di far tesoro dell'esperienza
fatta, salvare il salvabile (che comunque non era trascurabile) e partire per una migrazione
più graduale.

Si è pensato di appoggiarsi ad un RDBMS per la memorizzazione persistente dei dati, la gestione
dell'atomicità e dell'isolamento delle interazioni, la coerenza dei dati (in quest'ordine 
proprietà DAIC di solito note come ACID !).

Inoltre avvalersi di un backend DB che sfruttasse il linguaggio SQL per l'interazione,
avrebbe consentito l'implementazione sia nella nuova implementazione in Python che nella vecchia 
basata su Perl.

Vediamo quali fasi si sono susseguite nello sviluppo di SANET.

Fase 1: Django + RDBMS
----------------------

Obiettivo visualizzazione. Sfruttare le nuove tecnologie web per realizzare una 
interfaccia ricca (RIA) che potesse essere fruibile da vari browser e pertanto
non richiedesse di essere installata su ogni postazione.

Da una disamina degli `application server` disponibili per Python si è scelto il nuovo
framework Django che pure era solamente alla versione 0.96.
Si è subito deciso di adottare la versione SVN che disponeva di funzionalità molto più
avanzate della versione rilasciata e avrebbe dato anche la possibiltà di segnalare patch
e essere sincronizzati con la comunità di sviluppatori.

Fra i pregi che hanno contribuito alla scelta:

* basato su pattern modello-vista-template (MVT, simile al più comune MVC)
* indipendente da un particolare backend DB: all'inizio si è usato MySQL ma poco dopo si è passati a PostgreSQL.
  Si voleva lasciare aperta già in principio questa possibilità date esperienze precedenti non troppo soddisfacenti
  con MySQL
* come dicono gli sviluppatori: Django è Python ! Non ci sarebbero stati vincoli particolari da rispettare,
  o imponenti infrastrutture da costruire (come ad esempio accade in Zope). Inoltre il sistema è veramente molto
  duttile e si può decidere di utilizzare solamente una componente (ad esempio il modello come poi avviene con la CLI)

Lo application server è scelto e quindi vengono ad esso relegate tutta una serie di problematiche di basso livello
come:

* interazione con RDBMS
* interazione con socket
* parsing delle richieste HTTP
* serializzazione delle risposte HTTP
* astrazione del backend autenticazione: ogni infrastruttura di rete dispone di propri meccanismi

A questo punto si è definito il modello dei dati che verrà popolato dall'attività di monitoraggio
di `pinger`: questi saranno i dati visualizzati nell'interfaccia web.

INIL, STATE, TREE
^^^^^^^^^^^^^^^^^

Il database è stato progettato in 3 parti logicamente suddivise:

TODO: recuperare materiale da wiki
TODO: mettere schema E-R ?

Evidenziare che la parte "tree" è nuova rispetto a pinger.

SANET visualizza lo stato della rete
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Come risultato di questa fase viene soddisfatta la prima priorità: visualizzare lo stato della rete in modo comprensibile
all'utente medio. Una svolta epocale per i LABS: da quel momento non sarebbero stati solo i tecnici ad avere 
visibilità totale e `macchinosa` degli eventi, ma tutti avrebbero avuto un'idea delle problematiche
o quantomeno avrebbero saputo di qualcosa che non stava funzionando nel modo atteso.

...e anche di dove ciò stava accadendo: SANET infatti implementava il meccanismo dei contenitori
che era totalmente assente nel sistema precedente.

`Pinger` nel contempo era stato adattato all'interazione con lo RDBMS e continuava ad essere eseguito con le seguenti mansioni:

* interpretazione della configurazione per i controlli
* monitoraggio: verifica dello stato e aggiornamento misurazioni

Le caratteristiche dei controlli, il loro stato e il valore delle misurazioni venivano scritti nel database
e letti dall'application server che ne poteva così produrre la rappresentazione

TODO: immagine con DB e Django + Pinger

Fase 2: CLI e mappe
-------------------

Il risultato raggiunto nella rappresentazione dell'attività di monitoraggio non era nemmeno paragonabile
alla versione precedente, quindi le priorità stringenti erano soddisfatte.

A questo punto il lavoro è proseguito da un lato con il potenziamento dell'interfaccia web e quindi:

* le mappe topologiche delle reti (a cui è dedicata una sezione separata)
* interazione minimale: ricerca e gestione di appunti, note temporanee

dall'altro con l'implementazione della `Command Line Interface` (CLI).

Con la CLI è stata colta l'occasione per potenziare l'espressività della tassonomia dei controlli
definibili nel sistema e implementare un'interfaccia per l'operatore di rete esperto: non a caso
l'interprete dei comandi che è stato implementato è simile a quello del sistema operativo Cisco IOS 
molto diffuso e apprezzato fra gli esperti di reti.

La CLI è realizzata interamente in Python e si appoggia allo stesso modello di dati
costruito per la parte web. Ciò è stato un notevole pregio nell'aver scelto una soluzione come Django
che implementa in modo chiaro la separazione delle componenti; oltre ovviamente al beneficio
di utilizzare software libero che ci ha consentito di copiare le funzioni di inizializzazione di Django
e riportarle nella procedura di inizializzazione della CLI.

L'interprete dei comandi è sviluppato in modo molto semplice e pratico. Anche qui si nota, come nel vecchio pinger,
l'approccio sistemistico fatto di funzioni piuttosto che di classi ed ereditarietà, di variabili globali invece 
di attributi statici di classe, o ancor meglio un passaggi per riferimento.

Anche l'output della CLI viene prodotto su misura e in un primo momento non si pensa alla possibilità di astrarre
backend di output in modo da poter inizializzare lo stesso codice su backend testuale, ncurses, o grafico piuttosto che di socket di rete.

Per fortuna successivamente, appena possibile, non è stato troppo impegnativo l'intervento degli sviluppatori
per aprire questo spiraglio nella rappresentazione dell'output,
mentre purtroppo per le variabili globali o la strutturazione del codice ci si è dovuti accontentare 
dell'implementazione realizzata e che comunque, a onor del vero, funziona.

Con la CLI viene implementata nel database tutta la parte di configurazione di SANET (categorie, attributi, istanze)
e quindi ristrutturato il vecchio sistema di template e definizione dei controlli: la compatibilità è rotta,
anche se la logica di fondo rimane simile. I sistemisti al lavoro nelle installazioni in produzione di SANET
si trovano disorientati e rallenta di molto il processo di aggiornamento delle installazioni da quella che era 
la versione 1.4 alla versione 2.0 (poi diventate 0.1.4 e 0.2.0 con il rilascio alla comunità open source).

In questa fase viene sottovalutato l'impatto di un tale aggiornamento e si interrompe il dialogo fra i sistemisti
e gli sviluppatori, facendo sì che solo dopo alcuni mesi ci si accorga del mancato avanzamento delle installazioni
in produzione.

In ogni caso, è stato raggiunto un altro importante obiettivo: il potenziamento della tassonomia dei controlli. Ora si possono definire molti più controlli con meno sforzo.

`Pinger` è stato adattato per leggere la nuova configurazione dal database e continua la sua attività come strumento di monitoraggio e quindi di aggiornamento dello stato.

La configurazione e la rappresentazione sono in mano a SANET. Notare che non viene provvisto, e ad oggi non è ancora implementato, un modo per configurare via web i parametri dei controlli: ciò è dovuto dalla consapevolezza delle complesse realtà di rete gestite dall'azienda che non si possono normalizzare con l'esposizione di interfacce cosiddette `user-friendly`.

TODO: immagine con DB e Django + CLI + Pinger

Fase 3: Poller
--------------

Diego Billi entra nella squadra. Obiettivo riscrivere pinger in python

TODO: immagine con DB e Django + CLI + Poller


Uno sforzo importante: le mappe
-------------------------------

Framework Flex rilasciato in Open Source da Adobe.
Questo ci ha consentito di non comprare costose licenze che per implementare .... 
Di rimanere svincolati. E di poter applicare delle modifiche a librerie cui ci siamo appoggiati

Grafi semplici

Pydot, Clustering etc. etc.
 
L'impulso del master FOSSET
---------------------------

TODO: immagine ... infrastruttura di sviluppo

Hooks 
Literate programming

Syslog collector (immagine)

Test Driven Development


Il rilascio...
