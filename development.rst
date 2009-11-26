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
* basava la sua implementazione su uno storage in memoria e anche qui ne é derivata una perdita di tempo
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
avrebbe consentito in modo semplice di adattare le parti scritte in Perl man mano
che la nuova implementazione in Python procedeva. 

Vediamo quali fasi si sono susseguite nello sviluppo di SANET.

Fase 1: Django + RDBMS
----------------------

**Obiettivo visualizzazione**: sfruttare le nuove tecnologie web per realizzare una 
interfaccia ricca (RIA) che potesse essere fruibile da vari browser e pertanto
non richiedesse di essere installata su ogni postazione.

Da una disamina degli `application server` disponibili per Python si è scelto il nuovo
framework Django che pure era solamente alla versione 0.96.
Si è subito deciso di adottare la versione SVN che disponeva di funzionalità molto più
avanzate della versione rilasciata e avrebbe dato anche la possibiltà di segnalare patch
e essere sincronizzati con la comunità di sviluppatori.

Fra i pregi che hanno contribuito alla scelta:

* è basato su pattern modello-vista-template (`MTV <http://docs.djangoproject.com/en/dev/faq/general/#django-appears-to-be-a-mvc-framework-but-you-call-the-controller-the-view-and-the-view-the-template-how-come-you-don-t-use-the-standard-names>`__, una interpretazione del più comune `MVC <http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller>`__ )
* è indipendente da un particolare backend DB: all'inizio si è usato MySQL ma poco dopo si è passati a PostgreSQL.
  Si voleva lasciare aperta già in principio questa possibilità date esperienze precedenti non troppo soddisfacenti
  con MySQL
* come dicono gli sviluppatori: Django è Python ! Non ci sono vincoli particolari da rispettare,
  o imponenti infrastrutture da costruire (come ad esempio accade in Zope). Inoltre il sistema è veramente molto
  duttile e si può decidere di utilizzare solamente una componente (ad esempio soltanto il modello come poi avviene con la CLI)

Lo `application server` è scelto e pertanto viene relegata ad esso 
una serie importante di problematiche di basso livello come:

* interazione con RDBMS
* interazione con socket
* parsing delle richieste HTTP
* serializzazione delle risposte HTTP
* astrazione del backend autenticazione: ogni infrastruttura di rete dispone di propri meccanismi

A questo punto si è definito il modello dei dati che verrà popolato dall'attività di monitoraggio
di `pinger` (riadattato per l'esigenza): questi saranno i dati visualizzati nell'interfaccia web.

INIL, SITEATTR, FOREST
^^^^^^^^^^^^^^^^^^^^^^

Il database è stato progettato in 3 parti logicamente suddivise:

* INIL (icon, node, interface, link): contiene le tabelle base che
  rappresentano la topologia della rete e le icone. In questa parte
  rientrano anche le configurazioni dei controlli, sebbene l'acronimo non le comprenda
* SITEATTR (siteattr): contiene una tabella con il registro globale di configurazione del
  sito (nome, ultima modifica, numero di thread, etc.)                                     
* FOREST: (tree, container, node_tree_pos): contiene le tabelle per      
  rappresentare alberi di contenitori, e per posizionare i nodi nei contenitori..  

Nella prima fase l'implementazione di INIL si è concentrata sulle 
tabelle contenenti le informazioni di configurazione e di stato dei controlli. Per quello che riguarda
i nodi e le interfacce di rete si è pensato di inserire solo le informazioni anagrafiche indispensabili
alla rappresentazione, mentre i link sono stati totalmente relegati al momento dell'implementazione futura delle mappe topologiche.

Realizzare SITEATTR è stato ovviamente banale una volta deciso di memorizzare queste informazioni
nel database è bastato dedicare una tabella con parametri e valori globali all'applicazione.

La parte FOREST è totalmente nuova rispetto a `pinger` e si è deciso di realizzarla subito.
Essa è risultata di fondamentale importanza per catalogare le risorse della rete e quindi offrire all'utente
viste specializzate dell'ambiente monitorato.

I contenitori sono strutturati ad albero in modo analogo alle directory strutturate nella gerarchia del file system. In questa metafora è come se i nodi di rete fossero i file da catalogare nelle directory. In SANET tuttavia l'espressività della categorizzazione è maggiore rispetto a quella del file system in quanto un nodo di rete può essere associato a diversi contenitori che non appartengono allo stesso albero.
Ogni albero identifica una tipologia di categorizzazione e i contenitori una specifica categoria. Alberi comunemente usati sono relativi alla dislocazione geografica degli apparati, alla gerarchia di responsabilità, o ancora alla tipologia dei nodi di rete in esso contenuti.
Classificare un nodo di rete in contenitori di alberi diversi in SANET significa applicare quei concetti di tag o categorizzazione duttile tipici delle `folksonomie del Web 2.0 <http://en.wikipedia.org/wiki/Folksonomy>`__.

Considerando che ogni pagina di SANET è la rappresentazione di ciò che è contenuto nella risorsa selezionata,
usare in modo corretto i contenitori consente di offrire ad utenti specifici cruscotti, che di solito prendono
il nome inglese di `dashboard`,  tramite i quali osservare l'andamento delle risorse di rete di propria responsabilità 
o comunque le risorse di proprio interesse.

SANET visualizza lo stato della rete
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Come risultato di questa fase viene soddisfatta la prima priorità: visualizzare lo stato della rete in modo comprensibile
all'utente medio. Una svolta epocale per i LABS: da questo momento non sarebbero stati solo i tecnici ad avere 
visibilità totale e "macchinosa" degli eventi, ma tutti avrebbero avuto un'idea delle problematiche
o quantomeno avrebbero saputo di qualcosa che non stava funzionando nel modo atteso.

...e anche di dove ciò stava accadendo: SANET infatti fin dalla prima versione 
implementa il meccanismo dei contenitori che era totalmente assente nel sistema precedente.

`Pinger` nel contempo era stato adattato all'interazione con lo RDBMS e continuava ad essere eseguito con le seguenti mansioni:

* interpretazione della configurazione per i controlli
* monitoraggio: verifica dello stato e aggiornamento misurazioni

Le caratteristiche dei controlli, il loro stato e il valore delle misurazioni venivano scritti nel database
e letti dallo application server che ne poteva così produrre la rappresentazione

TODO: immagine con DB e Django + Pinger ??

Fase 2: CLI e mappe
-------------------

Il risultato raggiunto nella rappresentazione dell'attività di monitoraggio non era nemmeno paragonabile
alla versione precedente, quindi le priorità stringenti erano soddisfatte.

A questo punto il lavoro è proseguito da un lato con il potenziamento dell'interfaccia web e quindi:

* le mappe topologiche delle reti (a cui è dedicata una sezione separata)
* interazione minimale: ricerca e gestione di appunti, note temporanee

dall'altro con **l'implementazione della `Command Line Interface` (CLI)**.

Con la CLI è stata colta l'occasione per potenziare l'espressività della tassonomia dei controlli
definibili nel sistema e implementare un'interfaccia per l'operatore di rete esperto: non a caso
l'interprete dei comandi implementato è simile a quello del sistema operativo Cisco IOS 
molto diffuso e apprezzato fra gli esperti di reti.

La CLI è realizzata interamente in Python e si appoggia allo stesso modello di dati
costruito per la parte web. Ciò è stato un notevole pregio nell'aver scelto una soluzione come Django
che implementa in modo chiaro la separazione delle componenti; oltre ovviamente al beneficio
di utilizzare software libero che ci ha consentito di copiare le funzioni di inizializzazione di Django
e riportarle nella procedura di inizializzazione della CLI.

L'interprete dei comandi è sviluppato in modo molto semplice e pratico. Anche qui si nota, come nel vecchio `pinger`,
l'approccio sistemistico fatto di funzioni piuttosto che di classi ed ereditarietà, di variabili globali invece 
di attributi statici di classe, o ancor meglio di passaggi per riferimento.

Anche l'output della CLI viene prodotto su misura e in un primo momento non si pensa alla possibilità di astrarre
il backend di output in modo da poter inizializzare lo stesso codice su backend testuale, ncurses, o grafico piuttosto che di socket di rete.

Per fortuna successivamente, appena possibile, non è stato troppo impegnativo l'intervento degli sviluppatori
per aprire questo spiraglio nella rappresentazione dei dati,
mentre purtroppo per le variabili globali o la strutturazione del codice ci si è dovuti accontentare 
dell'implementazione realizzata e che comunque, a onor del vero, funziona.

Con la CLI viene implementata nel database tutta la parte di configurazione di SANET (categorie, attributi, istanze)
e quindi ristrutturato il vecchio sistema di `template` e definizione dei controlli. 

La compatibilità è rotta, anche se la logica di fondo rimane simile. 

I sistemisti al lavoro nelle installazioni in produzione di SANET
si trovano disorientati e rallenta di molto il processo di aggiornamento delle installazioni da quella che era 
la versione 1.4 alla versione 2.0 (poi diventate 0.1.4 e 0.2.0 con il rilascio alla comunità open source).

In questa fase viene sottovalutato l'impatto di un tale aggiornamento e senza che nessuno se ne accorga,
si interrompe il dialogo fra i sistemisti e gli sviluppatori, 
facendo sì che solo dopo alcuni mesi ci si accorga del mancato avanzamento delle installazioni in produzione.

In ogni caso, è stato raggiunto un altro importante obiettivo: il potenziamento della tassonomia dei controlli. 
Ora si possono definire molti più controlli con meno sforzo.

`Pinger` è stato adattato per leggere la nuova configurazione dal database e continua la sua attività come strumento di monitoraggio e quindi di aggiornamento dello stato.

La configurazione e la rappresentazione sono in mano a SANET. Notare che non viene provvisto, e ad oggi non è ancora implementato, un modo per configurare via web i parametri dei controlli: ciò è dovuto alla consapevolezza delle complesse realtà di rete gestite dall'azienda che non si possono normalizzare con l'esposizione di interfacce cosiddette `user-friendly`.

TODO: immagine con DB e Django + CLI + Pinger

Fase 3: Poller
--------------

Entra un nuovo sviluppatore nella squadra. **Obiettivo riscrivere `pinger` in Python** e con questo:

* decurtare le ultime rimanenze di `pinger`
* aumentare l'espressività del linguaggio per la definizione delle espressioni con cui verificare lo stato ed effettuare le misurazioni
* avere un sistema più scalabile grazie al `multithread`

Se prima ci si era occupati dei meccanismi di ereditarietà fra i controlli e le categorie, ora ci si concentra sulla singola espressione da verificare. Si realizza un linguaggio con una propria grammatica, dotato di contesto e operatori con tipizzazione dinamica degli operandi. Questa nuova implementazione consente di esprimere ulteriori tipi di controlli; vegnono implementate:

* funzioni di adiacenza `bgp` ed `ospf`
* controlli su `ntp`
* interrogazione `WMI` tramite `wmic` per i `server Windows`
* esecuzione comandi esterni (e quindi integrazione `ZenPacks`, `JMX` o `plugins Nagios`)
* `wildcards` per `OID SNMP`
* operatori di `match` sottostringa

oltre al meccanismo di `escalation` che consente di ridurre al minimo il rumore per gli allarmi "a cascata".

Anche in questo caso si può sfruttare il modello di dati già usato dalla CLI e dallo application server 
e, vista l'elevata occupazione di memoria e la frequenza di operazioni di `update` nel database, 
si implementano meccanismi ad-hoc che sono più performanti di quelli offerti dal Django stesso.

Non mancano i `bug` nella libreria NetSNMP e nel suo `binding` Python: vengono segnalati, uno minore viene risolto,
per il resto si trovano `workaround`.

Frattanto prosegue lo sviluppo delle mappe e il lavoro di promozione presso i clienti dell'azienda.

La realizzazione del `poller` è la svolta finale che consente al gruppo di procedere verso il rilascio.
Ora il vecchio `pinger` è completamente sradicato e SANET lo sostituisce completamente superandone i limiti.

Un ulteriore miglioramento per la fruibilità dei dati è costituito dalla segnalazione degli allarmi tramite `feed RSS`,
o dal recupero degli stessi tramite una semplice interfaccia `XML-RPC` che ricorda l'operazione `snmpwalk`.

TODO: immagine con DB e Django + CLI + Poller

L'impulso del Master FOSSET0809
-------------------------------

Durante questo ultimo anno ho avuto modo di frequentare il Master FOSSET
su proposta e con il sovvenzionamento dell'azienda.

Ritengo opportuno dedicare una breve sezione all'influenza che questa attività
di formazione ha avuto sul processo di sviluppo di SANET.

Un apporto importante si è verificato nella filiera di sviluppo di
tutto il software aziendale grazie al corso di `Strumenti di sviluppo collaborativo`
e in particolare:

* uso degli `hooks` per i sistemi di versionamento
* strumenti di `literate programming` 

In questo modo abbiamo potuto realizzare l'infrastruttura di sviluppo presentata 
in figura :fig:`infrastruttura` TODO.

TODO: immagine ... infrastruttura di sviluppo

Per quello che riguarda il corso di `Project management` invece, abbiamo cercato di mantenere,
man mano che il team si allargava (con l'arrivo del nuovo sviluppatore) un approccio `Agile` alla risoluzione 
dei problemi, anche se non c'erano risorse per provare un vero e proprio `Scrum` come avrei voluto.

Altri tentativi di adozione sono stati:

* la tecnica del pomodoro
* `test driven development`

entrambi interessanti, ma purtroppo naufragati a causa del piccolo team autogestito
e dal fatto che la crescita deve procedere per gradi.

Alcune sperimentazioni e introduzioni importanti sono derivate dai progetti:

* **Django history** realizzato per `Fondamenti di sistemi liberi` ci ha consentito di sperimentare 
  un meccanismo versatile ed efficace per mantenere con Django
  uno storico temporale di alcune tabelle del database. L'implementazione realizzata supera alcuni
  dei limiti rilevati dall'autore Marty Alchin che purtroppo non ha più risposto ai miei messaggi. 
  In ogni caso il modulo realizzato può essere tranquillamente integrato così come è in SANET 
  per la gestione del log dei cambi di stato.

* **Syslog collector** per il corso di `Reti` è stato rilasciato come ulteriore prodotto LABS su
  http://syslog-agentx.sourceforge.net/ . 
  Esso consente a SANET (e non solo) di implementare una serie innumerevole di nuovi controlli
  dato che esporta via SNMP informazioni su match di espressioni regolari in messaggi di `syslog`.

  TODO: Syslog collector (immagine)

* Il lavoro effettuato per il supporto ai `prepared statement` in Django con `backend` PostgreSQL per il 
  corso di `Basi di dati e applicazioni web` è servito per capire che non avremmo avuto un aumento
  di prestazioni significativo con l'introduzione in SANET degli `statement` precompilati.

