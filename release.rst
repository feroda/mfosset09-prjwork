Rilasciare SANET alla comunità
==============================

Il processo di rilascio è una fase cruciale nella vita di un software libero.
È il momento in cui il software esce dalla sua nicchia ed entra a far parte della comunità.

Tecnicamente non si può dire che è il momento in cui il software libero nasce perché 
un software è libero a prescindere dalla sua distribuzione, ma si può tranquillamente dire 
che il rilascio è il momento in cui il software entra a far parte della comunità del software libero.

Per uno sviluppatore vuol dire mettersi in gioco, vuol dire trovare il tempo per spiegare 
ed ascoltare. Per un'azienda vuol dire pubblicità, e vuol dire anche costi.

Un'azienda che non ha bisogno di pubblicità non è interessata a rilasciare il software.
Anche se un po' di pubblicità non guasta mai, bisogna considerare i costi ad essa collegata.

Rilasciare un software libero per un'azienda, significa porsi degli interrogativi e fare 
delle scelte che altrimenti non avrebbe dovuto fare, significa un impegno a documentare adeguatamente
il software, significa creare dei canali di comunicazione con la comunità, significa 
porre attenzione alle richieste della comunità.

Da tutto ciò quindi si deduce che non c'è solo un discorso di pubblicità, ma anche di qualità
del software e di apertura mentale di chi crede che il confronto sia in ogni caso costruttivo. 

È per questo che secondo me bisogna credere nelle aziende che rilasciano i
propri prodotti e pongono attenzione alla comunità, mentre bisogna andare cauti con chi sostiene di
offrire soluzioni open source, ma che in realtà non giocano la propria parte secondo queste regole.

Le scelte principali
--------------------

Riassumiamo le scelte fondamentali che LABS ha preso per essere pronta al rilascio di SANET:

* **Rilasciare tutto, o tenere un piccolo sottoinsieme di funzionalità "separate"**:
  non è raro trovare prodotti liberi di aziende che mantengono separate una serie di funzionalità
  per la versione `enterprise` con licenza meno aperta. Questo approccio può derivare da una serie di
  considerazioni quali vantaggio competitivo, collaborazioni esterne, o il meno pulito di posizionare
  i cosiddetti `specchi per allodole`. LABS ha deciso di rilasciare tutto il software realizzato e anche 
  una corposa libreria di controlli. Questa è senza dubbio una scelta di grande apertura che sottolinea
  la confidenza nel proprio operato e la consapevolezza che la ricchezza di un software simile consiste
  soprattutto nel servizio di personalizzazione e assistenza h24 che si può offrire a strutture complesse
  che necessitano di esperti sistemisti di rete... e la professionalità non si fa 
  con qualche riga di codice in più
* **Quale licenza applicare**: dato che SANET è un'applicazione web, abbiamo optato per la AGPLv3.
  L'intenzione è di evitare il `problema Google`: un'azienda potente che inglobi SANET fornendo
  un servizio di rete basato su di esso, ma senza essere obbligata a restituire il codice alla comunità
  e tantomeno a noi
* **Dove sviluppare il codice** o meglio dove gestire lo sviluppo del codice e la comunità degli utenti: 
  la risposta è stata `Sourceforge.net <http://sanet.sourceforge.net>`__. Il portale di sviluppo software open source per definizione.
  Sourceforge.net offre il sistema di versionamento Subversion (e ora anche GIT) e la recente possibilità di installare applicazioni esterne quali TRAC, MediaWiki o Wordpress, ci hanno convinto che sarebbe stata una scelta appropriata.
  Infatti, in particolare grazie a TRAC, abbiamo potuto portare avanti il progetto senza cambiare il nostro modo di lavorare
* **Quando uscire con la prima release**: una decisione non banale. Abbiamo deciso di uscire non appena
  ci fossimo liberati totalmente del vecchio `pinger`. In aiuto ci è venuta anche l'occasione della ConfSL09: un motivo in più per accellerare i lavori e rilasciare entro il 13 giugno 2009. 


Il primo rilascio alla ConfSL09
-------------------------------

**Sabato 13 giugno 2009 è il primo rilascio ufficiale di SANET**.

Momento storico per i LABS e in particolare per me che per qualche anno
avevo sviluppato software libero con il sogno, o l'ambizione di raggiungere questo obiettivo.

Cosa viene fatto per il rilascio di questa prima versione ?

Il minimo indispensabile:

* viene inserito un file LICENSE con la licenza AGPLv3
* viene creato un tarball dell'ultima versione stabile
* viene rilasciato il pdf dell'articolo per la CONFSL09 come guida base per l'utente 
* viene creata la home page del progetto: http://sanet.sourceforge.net
* viene attivato e impostato l'ambiente TRAC con le prossime milestone
* viene creato e registrato il canale irc #sanet nella rete freenode

La strada è tracciata. SANET è su Sourceforge.net. 

Ora inizia un percorso: dalla cattedrale si va verso il bazaar.
Riportiamo a questo proposito la figura :ref:`from-tc-to-tb` riferita allo studio 
`From the Cathedral to the Bazaar: An Empirical Study of the Lifecycle of Volunteer Community Projects` 
di `Andrea Capiluppi e Martin Michlmayr`. 

.. _from-tc-to-tb:

.. figure:: _static/DallaCattedraleAlBazaar.png
	:align: center 
	:scale: 80

	From the Cathedral to the Bazaar 

Nello studio i due autori spiegano come ogni progetto disseminato nel bazaar parta da una prima fase
caratterizzata dall'approccio a cattedrale.

Cosa c'è nella cattedrale ?

* Rappresentazione della rete

  * Nodi
  * Interfacce
  * Controlli

    * Target
    * Misure

  * Sito
  * Contenitori

* Poller (agente di controllo)

  * Legge la configurazione
  * Esegue i controlli
  * Agisce al verificarsi di determinate condizioni

* Logica dei controlli 

  * Target UP
  * Target DOWN
  * Target FAILING
  * Target UNCHECKABLE
  * Target INACTIVE (trasparente)
  * 2 limiti:

    * Valore
    * Tolleranza temporale

* Libreria dei controlli

  * Nodo

    * Raggiungibilità (MTU configurabile)
    * Occupazione CPU, FS, RAM, VMEM
    * Reboot
    * Presenza di un processo
    * Raggiungiblità TCP
    * Sincronizzazione con server NTP
    * Adiacenza BGP, OSPF
    * Match di un URL con una espressione regolare
    * WMI

  * Interfaccia (supporta variazione di ifIndex)

    * Stato
    * Numero di errori
    * Pacchetti non unicast ricevuti
    * Full duplex
    * Traffico (supporta contatori a 32 e 64 bit)
    * STP
    * Variazione di stato
    * Variazione costo root bridge
    * Variazione porta root bridge

* CLI per la configurazione

  * Creazione e gestione di categorie di nodi, interfacce, controlli
  * Creazione e gestione di nodi, interfacce e controlli
  * Creazione e gestione di alberi e contenitori
  * Quando controllare
  * Quando e a chi mandare la segnalazione
  * Sospendere il controllo di un nodo
  * Snmpwalk integrato

* Interfaccia web

  * Visualizzazione dello stato e delle misure
  * Feed RSS
  * Mappe


Andando verso il bazaar...
--------------------------

Alla ConfSL09 il rilascio è stato annunciato come `Open Source Prerelease`
a causa della mancanza di un'adeguata documentazione e dell'esternazione 
del repository Subversion per lo sviluppo.

Ci siamo subito concentrati nel colmare queste lacune e quindi:

* la documentazione è stata completata e tradotta in inglese
* abbiamo trasferito su Sourceforge tutto il repository Subversion con la storia dello sviluppo,
  rimediando ad alcuni `errori di giovinezza`: abbiamo eliminato alcune password che erano state 
  inserite in passato e la licenza è stata applicata in modo retroattivo
* abbiamo riportato nel TRAC di Sourceforge i bug applicativi

Fatto il nuovo `tarball` con i primi `bugfix`, ci siamo anche confrontati internamente
sullo stato del software: quello che noi consideravamo versione 2.x
non poteva essere considerato alla stessa stregua dalla comunità del software libero.

Perciò abbiamo deciso di effettuare il `downgrade` di versione dalla 2.3 alla 0.2.3:
SANET è funzionante, ma è ancora in evoluzione e soprattutto non ha ancora la `confezione`
necessaria per essere almeno 1.0.

Il secondo rilascio al termine del Master FOSSET0809
----------------------------------------------------

Siamo a inizio novembre e SANET è andato molto avanti rispetto al rilascio di giugno.
C'è stato tutto il lavoro sulle mappe (fino ad agosto), ma non solo. Il `poller` integra molti più controlli,
ed è stato realizzato un modulo per la reportistica.

In questo periodo la crescita della comunità non è stata fra le priorità LABS
che ha preferito spingere sulle nuove funzionalità.

A cinque mesi dal rilascio si contano:

* 120 download dell'applicazione e 56 dell'articolo realizzato per la ConfSL09
* un canale IRC frequentato solo da sistemisti LABS
* un repository Subversion che è molto più avanti del tarball

Colgo l'occasione col dire che io, lo sviluppatore principale del progetto,
ho interrotto il rapporto di lavoro dipendente con i LABS il 30 settembre.
Questo aspetto è molto importante e darà adito ad alcune riflessioni che però lascio alla sezione 
:ref:`retrospective-and-future`.

Di cosa ha bisogno SANET ora ?

Ho pensato di curare il rilascio di questa nuova versione, la 0.3.9.

I cambiamenti sono stati molti e ci avviciniamo alla 0.4.
È giunto il momento di realizzare la procedura di installazione che si occupi
di verificare se tutte le dipendenze del sistema sono soddisfatte.

È abbastanza frequente che si verifichino errori a causa di vecchie librerie,
o mancanza di alcuni prerequisiti.

L'evoluzione naturale del rilascio del software sarebbe di ampliare il bacino
di utenza e ampliare i canali di comunicazione con la comunità. Per fare questo
è innanzi tutto importante pacchettizzare il software per una distribuzione.
Un'altra idea sarebbe di aprire un blog specializzato. 

Al momento, considerata l'evoluzione dei rapporti, non si è pensato di
proseguire riguardo a questi ultimi due passi. Si intende discuterne con LABS 
che detiene il diritto di paternità del software e quindi l'interesse nella diffusione
dell'implementazione.

In questa fase abbiamo quindi congelato lo sviluppo e creato il file standard `setup.py <http://docs.python.org/distutils/setupscript.html>`__
per la distribuzione di applicativi python. Lo script verifica le dipendenze
e installa il software. Inoltre è stato adottato `pip <http://pip.openplans.org/>`__ per la generazione 
dell'elenco di librerie python richieste con le rispettive versioni.
Per quello che riguarda la verifica delle dipendenze fra applicativi i manutentori di moduli python
suggeriscono di occuparsene usando i gestori pacchetti delle distribuzioni specifiche.

Abbiamo cercato di andare oltre, per verificare le dipendenze rispetto ad applicativi
esterni. Sicuramente lo strumento per eccellenza a questo fine sono gli `autotools`, ma 
l'idea è giunta tardi e, alla data di stesura di questo documento, non c'è stato modo di provarli.
È stato invece realizzato lo script ad-hoc `install_requirements.sh` che verifica la presenza di corrette librerie
NET-SNMP e PostgreSQL che sono elementi cruciali del sistema.
La sua esecuzione è stata integrata nel `setup.py` per mantenere comunque la procedura standard 
di installazione pacchetti python.
Ripeto che il prossimo passo sarà ripassare gli `autotools` e provare con quelli.

Il senso di creare la 0.3.9 (alpha 1) è quello di avere margine per alcune modifiche grafiche
nell'integrazione nell'interfaccia del modulo dei report che necessita di alcune migliorie prima della 0.4.

