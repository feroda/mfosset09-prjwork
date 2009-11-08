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

...e anche dove ciò stava accadendo: SANET infatti implementava il meccanismo dei contenitori.



TODO: pinger configura e scrive su db

Fase 2: CLI
--------------

Ridefinizione configurazione, nuova parte di db scrivibile tramite cli simile a Cisco IOS.

Pinger effettua solamente i controlli

Fase 3: Poller
--------------

Diego Billi entra nella squadra. Obiettivo riscrivere pinger in python

TODO: immagini ... da pinger a db con visualizzazione, a poller


TODO: immagine nuova architettura (da documento sviluppo R1.4 credo o giu' di li)

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
