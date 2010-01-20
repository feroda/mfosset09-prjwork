.. _SANET@CONFSL09:

********************************************************
Appendice E - Articolo per la CONFSL09 (versione estesa)
********************************************************

SANET - una alternativa per il monitoraggio della rete
======================================================

Luca Ferroni, `Laboratori Guglielmo Marconi S.p.A. <http://www.labs.it/>`__ , 13 giugno 2009.

Abstract
""""""""

`Le attività di gestione e supporto specializzato alle tematiche di rete presentano come denominatore comune la necessità di monitorare lo stato degli elementi che compongono e coordinano la rete siano essi apparati hardware o servizi software. Il monitoraggio delle condizioni interessanti avviene tramite i cosiddetti Network Management System (NMS) che includono componenti attive atte alla verifica continua di tali condizioni e alla segnalazione di criticità. Di norma essi offrono anche una rappresentazione grafica intuitiva della rete e dell'occupazione delle risorse nel tempo per facilitare la comprensione e il troubleshooting dei problemi.`

`SANET è un Network Management System sviluppato internamente a Laboratori Guglielmo Marconi S.p.A. (LABS) nell'ambito delle attività di assistenza, gestione e reperibilità per reti di medie dimensioni. Esso consente di avere una visibilità completa dello stato della rete, offrendo a sistemisti esperti di configurare i controlli da effettuare principalmente tramite il protocollo SNMP.`

`SANET è un'applicazione web le cui caratteristiche salienti sono:`

* `Alta configurabilità delle soglie di errore tollerabili prima di inviare gli allarmi`
* `Mappe interattive della topologia della rete configurabili per connessioni e posizionamento`
* `Catalogazione delle risorse in gerarchie arbitrarie (ad esempio per responsabilità o collocazione geografica)`
* `Grafici per la visualizzazione temporale di misure`
* `Command Line Interface per la configurazione completa del sistema`
* `Interfacce applicative XML-RPC e RSS per ottenere i risultati del monitoraggio`

`In questo articolo si intende offrire una visione d'insieme della piattaforma SANET a partire dalle sue funzionalità e la definizione delle verifiche da effettuare prevedendo limiti, soglie di tolleranza e instradamento degli allarmi. Contestualmente verrà illustrata la possibilità di storicizzare i valori per la produzione di grafici con l'occupazione delle risorse nel tempo.`

`Successivamente mostreremo la rappresentazione e la classificazione degli elementi monitorati con l'obiettivo di produrre viste mirate secondo le necessità di ogni specifica installazione. Questa parte si concluderà con la descrizione delle mappe di stato autoaggiornanti e interattive.`

`Seguirà una breve presentazione delle peculiarità della CLI per la configurazione dell'intero sistema, focalizzandosi in particolar modo sulla tassonomia studiata per consentire la propagazione delle impostazioni alle numerose risorse presenti in una rete di medie dimensioni. Verrà mostrato quindi come configurare i parametri globali del sistema, definire categorie ed istanze di nodi, interfacce, target e misure, per poi passare alla classificazione dei nodi di rete in alberi e contenitori.`

`L'articolo si concluderà con una visione architetturale delle singole componenti del sistema.`

`SANET è sviluppato grazie a strumenti Open Source e viene rilasciato alla comunità alle medesime condizioni. L'intenzione è di condividere con la comunità stessa, ciò che si è sviluppato, secondo le regole proprie del mondo Open Source. Crediamo inoltre sia possibile avviare un processo di arricchimento reciproco, favorito anche dalla localizzazione italiana di una azienda che si occupa di reti dal 1991.`

`La presentazione alla comunità avviene in occasione della III Conferenza del Software Libero Italiana.`

`L'indirizzo di riferimento del progetto è` `http://sanet.sourceforge.net/ <http://sanet.sourceforge.net>`__.

`Parole Chiave: Network Management System, NMS, Rete, monitoraggio, allarmi, grafici, mappe, topologia, controlli, RRD, SNMP`

INTRODUZIONE
------------

Ogni rete presenta la necessità di essere monitorata e gestita. Tale attività può essere espletata con personale interno alla struttura di interesse o affidandosi ad aziende specializzate. In entrambi i casi, è opportuno ricorrere a strumenti software che consentano di verificare condizioni prestabilite con periodicità e costanza e segnalare ai responsabili le situazioni problematiche. 

Security Architecture NETwork (SANET) è la soluzione che Laboratori Guglielmo Marconi (LABS) implementa per il servizio di monitoraggio. Tale piattaforma propone il monitoraggio approfondito come prima fase del processo di gestione e messa in sicurezza di una rete: in questo modo il personale specializzato ha a disposizione il maggior numero di elementi possibili per effettuare attività di troubleshooting dei problemi riscontrati e pianificare operazioni di manutenzione e miglioramento secondo necessità verificate.

La piattaforma è progettata per essere flessibile alle situazioni eterogenee che si presentano nel mondo delle reti, in particolar modo in quelle reti di dimensioni medio-grandi che portano con sé i retaggi del passato. Inoltre, come vedremo, il sistema è completamente configurabile e pertanto consente al fruitore del servizio di raggiungere il livello di controllo desiderato.

SANET è software libero ed è pubblicato all'indirizzo `http://sanet.sourceforge.net/ <http://sanet.sourceforge.net>`__ 

OBIETTIVI
---------

Con la realizzazione di SANET, Laboratori Guglielmo Marconi si propone di raccogliere la propria esperienza di gestione delle reti in una piattaforma estendibile che offra supporto trasversale all'unità sistemistica dell'azienda stessa mantenendo la professionalità e il metodo di approccio costruiti con anni di esperienza nel settore. LABS ha da sempre utilizzato strumenti autoprodotti per il monitoraggio delle reti: nel corso del tempo essi hanno testimoniato il loro valore rispetto alla granularità di configurazione e quindi la capacità di adattamento ad ogni situazione, ma d'altro canto hanno evidenziato un limite nella fruibilità dei risultati da parte del destinatario del servizio soprattutto ora che le tecnologie del web 2.0 si sono affermate e consentono di creare interfacce grafiche web avanzate.

Si è pensato quindi di cogliere l'occasione per rendere più scalabili gli strumenti adottati ed i risultati dell'attività di monitoraggio comprensibili anche ad un'utenza non specializzata, pur mantenendo alta la granulatità di configurazione del sistema che è risultata essere un punto di forza per l'incontro delle esigenze del cliente.

LABS ha da sempre utilizzato e promosso le tecnologie open source. Ora è il momento di entrare nella comunità rilasciando il risultato dei propri sforzi con l'ambizione che il prodotto realizzato possa essere utile alla comunità stessa e offra spunti per il miglioramento dell'attività di gestione e monitoraggio delle reti.

Sebbene nell'ambito open source, ad oggi, si possano trovare Network Management System (NMS) più o meno diffusi, ciò non si verificava quando LABS ha iniziato lo sviluppo di un proprio strumento che ha deciso di portare a compimento sia per motivi storici e che per rispecchiare un modus operandi che, come si è detto, ha testimoniato il suo valore nel corso degli anni.


MONITORAGGIO
------------

Nel perseguire l'obiettivo di massima configurabilità necessario per offrire un supporto adeguato alle attività di gestione delle reti e troubleshooting, SANET presenta alcune caratteristiche particolarmente avanzate che lo rendono, a nostro parere, uno strumento degno di nota, nel panorama delle soluzioni per il network management:


* **Massima flessibilità nella gestione dei controlli**. Tutti i controlli sono configurabili secondo vari parametri. Elenchiamo in particolare:

  * **frequenza**: i più importanti si eseguono tipicamente ad intervalli di qualche decina di secondi, mentre quelli più approfonditi (di solito più invasivi) si possono eseguire anche ad intervalli di molti minuti o ore
  * **tolleranza**: ossia se il fallimento di un controllo debba dare luogo ad un allarme immediato, o se debba essere solo registrato nel log e dare luogo ad allarme solo dopo un certo numero di fallimenti consecutivi.
  * **notifica**: ogni controllo viene notificato tramite email e/o SMS. Testo e soggetto delle email, così come il testo degli SMS, sono completamente personalizzabili per ciascun allarme.

* **Controllo raggiungibilità IPv4 e IPv6**. SANET permette di controllare la raggiungibilità di un host in IPv4 o in IPv6, con dimensioni del pacchetto personalizzabili (di solito impostate in modo da ottenere un datagramma IP di 1500 byte). È possibile inoltre mantenere una misura del RTT (minimo, massimo e medio) e della perdita (loss) di pacchetti, e rendere poi disponibili i dati rilevati in forma grafica. In caso di diversi indirizzi IP corrispondenti ad uno stesso nome DNS, SANET determina la raggiungibilità di almeno uno di essi, eseguendo automaticamente un rimescolamento (shuffle) dei diversi indirizzi
* **Dipendenza tra le condizioni monitorate**. Un controllo può dipendere da un altro controllo, e non verrà eseguito se il controllo dal quale dipende non dà esito positivo. Ad esempio, se il router di una sede (supponendola priva di collegamenti ridondati) è irraggiungibile, è inutile controllare gli switch e gli altri dispositivi di quella sede, perché saranno senz'altro irraggiungibili. Questo consente di ottimizzare le prestazioni, ridurre il numero di allarmi (particolarmente utile nel caso di allarmi via SMS) ed evidenziare immediatamente la reale natura del problema, senza "diluirla" tra numerosi allarmi correlati
* **Flessibilità nella individuazione delle interfacce**. Chi si occupa di network management sa che l'individuazione delle interfacce di un nodo tramite il cosiddetto ifIndex, ossia l'istanza nella tabella delle interfacce nella MIB2 (1.3.6.1.2.1.2.2.1), presenta su alcune piattaforme qualche problema, perché il numero associato ad una certa interfaccia fisica non è necessariamente costante e può variare ai reboot, ai cambi di versione del firmware, in occasione di modifiche hardware (inserimento e rimozione di moduli o schede). Analoghe problematiche si presentano con altri rami della MIB: ad esempio con le interfacce nella MIB dei bridge (1.3.6.1.2.1.17.2.15.1), con i filesystem di un server (hrStorage, 1.3.6.1.2.1.25.2.3.1), con le varie RAM di un apparato cisco con IOS (1.3.6.1.4.1.9.9.48.1.1.1), con i processi in un server (1.3.6.1.2.1.25.4.2.1) ed in tantissimi altri casi. SANET fornisce un meccanismo flessibile per individuare le istanze in tabelle generiche in base ai più svariati criteri, utilizzando poi i numeri di istanza così ottenuti nei controlli e nel monitoraggio quantitativo. È così possibile monitorare una interfaccia in base al nome, all'indirizzo IP o MAC, ad una stringa presente nella descrizione di IOS, etc. Il poller esegue automaticamente il ''walk'' per determinare inizialmente l'istanza desiderata, e memorizza quindi i risultati in una cache. In questo modo non c'è sostanziale aumento del traffico SNMP in condizioni stazionarie, pur avendo l'automatico ed immediato aggiornamento in caso di variazioni
* **Smorzamento dei controlli oscillanti (ping flap dampening)**. Può succedere che un controllo oscilli continuamente tra esiti positivi e negativi, ad esempio per via di un collegamento "quasi funzionante", di un marginale guasto hardware, etc. In queste condizioni, un sistema tradizionale produce una lunga sequenza di allarmi subito rientrati, e quindi una lunga sequenza di messaggi, particolarmente deleteria nel caso di allarmistica via SMS. SANET include la possibilità di imporre uno smorzamento a questi eventi, usando un algoritmo mutuato dal "route flap dampening" del BGP, che assegna a ciascun controllo una penalità che aumenta ad ogni cambio di stato, e si smorza esponenzialmente nel tempo (con semivita configurabile) quando non c'è cambiamento di stato. Si definiscono quindi due soglie di penalità: una (più alta) per entrare in smorzamento ed una (più bassa) per uscirne. Quando il controllo è in stato di smorzamento, continua ad essere eseguito e continuano ad essere registrati gli eventi nel log, ma i messaggi via mail o SMS non vengono più mandati finché non esce dalla condizione di smorzamento
* **Funzioni di utilità per i controlli**. SANET rende disponibili tramite la sonda poller diverse funzioni di utilità generale per i controlli, che si possono usare da sole o combinate in espressioni contenenti anche variabili SNMP presenti o passate. C'è una funzione per verificare che una interfaccia ethernet sia in full duplex provando le varie MIB standard e proprietarie dove questa informazione si può trovare, una per verificare che un server NTP (o un apparato) sia effettivamente sincronizzato. Ci sono poi funzioni per fare varie operazioni aritmetiche su tutti i valori di una certa tabella SNMP (ad esempio per verificare l'occupazione media delle CPU, senza effettivamente specificare quali e quante CPU ci sono), per verificare che una certa porta TCP sia aperta, che una certa URL sia disponibile e faccia o non faccia un match con un certo pattern, per costruire condizioni logiche in base all'esito di altri controlli, etc
* **Gestione di orari per i controlli e per i messaggi**. È possibile definire una fascia oraria per i controlli, in base all'ora (locale e con eventuale ora legale) del giorno ed al giorno della settimana. Questo consente di effettuare controlli che hanno senso solo in certi orari, ad esempio in caso di uffici che hanno l'abitudine di staccare la corrente quando chiudono, etc. È possibile anche personalizzare in base a fasce orarie i destinatari dei messaggi, ad esempio per notificare un presidio quando esso è attivo, ed un reperibile negli altri orari.

GLI ATOMI DEL MONITORAGGIO: TARGET E MISURE
-------------------------------------------

L'intero sistema di monitoraggio si basa sul controllo di informazioni ricavate da apparati, server e servizi presenti nella rete. In SANET esistono due tipi di **controlli** che prendono il nome di **target** e **misure**.

I controlli sono eseguiti dal `Poller Agent` (v. figura 13)

Target
^^^^^^

I target sono controlli **qualitativi** e costituiscono gli atomi fondamentali per la verifica delle criticità. Definire un target significa configurare il sistema affinché reperisca periodicamente un'informazione e verifichi se il valore restituito rientra in una determinata soglia con tolleranza di un certo numero di fallimenti.

L'esecuzione di tale verifica produce come output lo **STATO** del target che può essere:


* **UP**: se il valore restituito rientra nella determinata soglia
* **FAILING**: se il valore restituito non rientra nella determinata soglia, ma il numero di fallimenti è verificati è minore del numero di fallimenti tollerati
* **DOWN**: se il valore restituito non rientra nella determinata soglia, ed il numero di fallimenti verificati supera il numero di fallimenti tollerati
* **UNCHECKABLE**: se non è possibile ottenere l'informazione. Se lo si desidera, in fase di configurazione si può decidere, per ogni controllo, se questo stato è da ritenersi UP o DOWN.

[[Image:|thumb|''Figure 1: Possibili stati per i target (nell'ordine UP, FAILING, DOWN, UNCHECKABLE) '']]

Rispettivamente al verificarsi degli stati UP e DOWN vengono inviate le segnalazioni di allarme ad un indirizzo di posta elettronica, di solito un alias che poi si occupa di inoltrare la mail ai responsabili ed eventualmente in inviare le segnalazioni via SMS, o effettuare altre azioni in risposta.


Misure
^^^^^^

Le misure sono controlli **quantitativi** e risultano utili nella storicizzazione delle informazioni reperite, in particolar modo al fine di effettuare il ''troubleshooting ''del problema una volta che è giunta la segnalazione.

Le misure sono utili anche per avere la percezione dello sfruttamento di determinate risorse nel tempo. Ad esempio per prevedere, data la crescente occupazione della risorsa, che si dovrà considerare un investimento per ampliare lo spazio disco della SAN, oppure la banda passante per un determinato link.

Fondamentalmente i valori reperiti dalle misure vengono memorizzati in file Round Robin Database (RRD) che vengono rappresentati con dei grafici simili a quelli di MRTG.

[[Image:|thumb|''Figure 2: Esempio di grafico relativo a misure di utilizzo CPU e numero di connessioni'']]


IL NODO DI RETE E LA SUA CLASSIFICAZIONE
----------------------------------------

Una volta definiti gli atomi dell'attività di monitoraggio si deve procedere alla loro classificazione per fare in modo che i dati siano innanzi tutto fruibili secondo le esigenze dell'utente finale e inoltre che si eviti la ridondanza di informazioni che potrebbe causare problemi applicativi e di manutenzione futura. 

In questo capitolo verrà presentato il nodo di rete che è il nucleo di aggregazione di target e misure, per poi prendere in esame la sua classificazione e la presentazione di viste specializzate.

Il nodo di rete 
^^^^^^^^^^^^^^^^

Ora vedremo cosa è e come è costituito il ''network element ''da monitorare e come viene classificato all'interno della piattaforma. In SANET l'elemento da monitorare è qualunque oggetto dotato di uno o più indirizzi IP. Come si vede in figura 3 al nodo di rete vengono associati target, misure e interfacce di rete. A queste ultime vengono a loro volta associati ulteriori target e misure. Tale rappresentazione logica ci consente di definire controlli specifici sia per il nodo di rete che per le interfacce che ad esso afferiscono senza necessità di ridondare le informazioni. Chiarificando con alcuni esempi: per lo stesso nodo si desidererà controllare la memoria occupata o la presenza di un determinato processo; per ogni interfaccia lo stato operativo (accesa o spenta), il traffico passante, il numero di errori, ecc. 

[[Image:|thumb|''Figure 3: La rappresentazione del nodo di rete in SANET'']]Target e misure quindi costituiscono pur sempre l'atomo del monitoraggio, ma vengono a loro volta aggregati nel nodo di rete che è invece l'atomo per la classificazione delle risorse nel sistema, o più specificatamente la realizzazione delle viste.

L'interfaccia di rete e il link
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

L'interfaccia di rete, oltre ad essere punto di aggregazione per i controlli ad essa relativi, riveste un ruolo di particolare importanza perché è di fatto l'estremo di un collegamento (link). In SANET i collegamenti fra i nodi di rete sono espressi come link fra le singole interfacce. Questo consente di fatto a SANET di offrire una rappresentazione layer 2 della rete e di implementare controlli di adiacenza. Attualmente i link vengono specificati dall'operatore attraverso la CLI. Questo perché, secondo la filosofia di SANET, è compito dell'operatore conoscere le connessioni presenti nella rete di installazione ed è quindi lui stesso a dover esplicitamente definire i collegamenti che intende monitorare.

Il sito
^^^^^^^

I nodi di rete vengono innanzi tutto inclusi nel **sito** che funge da registro globale dell'installazione. È la radice dalla quale si possono raggiungere tutte le risorse del sistema. Esso dispone di parametri globali quali il nome della rete oggetto del monitoraggio, il massimo numero di thread che si vogliono utilizzare per l'esecuzione dei controlli, o ancora un indirizzo email di ''watchdog'' cui il server di monitoraggio invia segnalazioni periodiche. Quest'ultima funzionalità può risultare utile nel caso non si abbia modo di monitorare il server stesso con altre installazioni di SANET o altri strumenti.

Contenitori
^^^^^^^^^^^

Il contenitore è la risorsa demandata alla classificazione dei nodi e quindi alla creazione di diverse ''viste ''sulla rete. I contenitori sono strutturati ad albero in modo analogo alle directory strutturate nella gerarchia del file system. In questa metafora è come se i nodi di rete fossero i file da catalogare nelle directory. In SANET tuttavia l'espressività della categorizzazione è maggiore rispetto a quella del file system in quanto le gerarchie sono configurabili generando, di fatto, una foresta.

La regola generale è: ''un nodo di rete può essere associato a diversi contenitori che non appartengono allo stesso albero.''

Ogni albero identifica una tipologia di categorizzazione e i contenitori una specifica categoria. Alberi comunemente usati sono relativi alla dislocazione geografica degli apparati, alla gerarchia di responsabilità, o ancora alla tipologia dei nodi di rete in esso contenuti.

Classificare un nodo di rete in contenitori di alberi diversi in SANET significa applicare quei concetti di tag o categorizzazione duttile tipici delle folksonomie del Web 2.0. Attualmente tutta la configurazione, e quindi la definizione degli alberi e dei contenitori è effettuabile tramite CLI (v.&nbsp;par.&nbsp;6) , e quindi da parte di personale specializzato, ma la politica implementata fa si che le viste corrispondano alle esigenze del fruitore finale del servizio. 

Mappe
&&&&&

Ad ogni contenitore è associata una mappa autoaggiornante che rappresenta lo stato delle risorse e dei rispettivi collegamenti. Ai fini della rappresentazione sono stati realizzati alcuni algoritmi per la derivazione dello stato negli aggregati di target: un nodo o un'interfaccia vengono considerati in stato FAILING se almeno un controllo in essi contenuto è in stato DOWN o FAILING, oppure sono in stato DOWN se il proprio target primario (di solito rispettivamente il `ping` e l'`operstatus`) è in stato DOWN.

Le mappe sono interattive e consentono di mostrare o nascondere i nomi delle interfacce di rete e inoltre di muovere gli elementi rappresentati e salvarne la posizione.

Attualmente le mappe sono in fase di ulteriore miglioramento per consentire di esplorare vari livelli di contenitori innestati e anche di sopperire al limite nella visualizzazione che non consente di mostrare link multipli fra gli stessi nodi di rete.

[[Image:|thumb|''Figure 4: Esempio di mappa con contenitori e link multipli (stato in blu)'']] 

[[Image:|thumb|''Figure 6: Esempio di mappa con icone personalizzate'']][[Image:|thumb|''Figure 5: Esempio di mappa router e switch'']]

INTERFACCIA GRAFICA
-------------------

SANET come si è detto, evolve dall'esperienza più che decennale di Laboratori Guglielmo Marconi nella gestione di reti di medie e grandi dimensioni. In questi anni erano già stati sviluppati strumenti rudimentali per il monitoraggio della rete. Con SANET la sfida è stata quella di superare i limiti tecnici di quegli stessi strumenti con un'attenzione particolare verso la presentazione grafica dei risultati che, in questo nuovo applicativo vogliono essere comprensibili e consultabili anche da parte dei destinatari del servizio oltre che dal personale tecnico.

Di seguito elenchiamo alcune caratteristiche dell'interfaccia web realizzata.

Ogni pagina si riferisce ad una risorsa, sia essa il sito, un contenitore, un nodo, un'interfaccia di rete, un target o una misura. Tale risorsa costituisce il contesto della pagina e tutte le informazioni mostrate sono relative al suo punto di vista. 

[[Image:|thumb|''Figure 7: SANET - Struttura dell'interfaccia grafica'']]

La pagina presenta una struttura omogenea (v. fig. 7) che contiene:

* **Intestazione**: include la **barra di navigazione** che identifica il percorso effettuato per raggiungere la risorsa
* **Pannello delle risorse**: è il punto di accesso a tutte le risorse del sistema. Include la **ricerca** e il blocco **esplora risorse**, ossia una foresta che ha come radici i contenitori di primo livello di tutti gli alberi definiti
* **Corpo della pagina** (figg. 8 e 9): include i blocchi generici e specializzati per tipo di risorsa. Fra i blocchi generici abbiamo:

  * **Dettagli**: include i dettagli anagrafici della risorsa, alcune informazioni ricavate via SNMP (nel caso la risorsa sia un nodo o un'interfaccia di rete) e la visualizzazione dei target problematici
  * **Stato della risorsa**: mostra informazioni quantitative sullo stato della risorsa. Evidenzia il numero di target nei vari stati. Mostra anche il grafico del Round Trip Time (RTT) nel caso in cui la risorsa visualizzata afferisca a un nodo di rete
  * **Monitoraggio**: include l'elenco dei target con visualizzazione immediata dello stato attuale e dell'ultimo cambio di stato con rispettive date e orari
  * **Misure**: include l'elenco delle misure con possibilità di visualizzare i grafici relativi per vari intervalli temporali
  * **Log**: questo è il blocco che elenca tutti i cambi di stato per ogni target del contesto e visualizza tutti i dettagli restituiti dall'agente Poller nello svolgimento della propria attività di verifica. Il blocco rimane chiuso a meno che non lo si apra esplicitamente poiché i dettagli inclusi qui sono interessanti solo per attività di troubleshooting

Per ogni blocco in cui si fa riferimento a risorse incluse nella risorsa-contesto, si intendono tutte le risorse ad essa agganciate in modo diretto o indiretto. L'effetto che si ottiene è una visione della situazione in profondità (`deep view`).


[[Image:|thumb|''Figure 8: SANET – blocchi dettagli e stato della risorsa'']]

Ogni blocco espone in alto a destra (nello stile predefinito) un insieme di azioni effettuabili. Le azioni tipiche sono;

* **Apertura/chiusura blocco**
* **Attivazione/disattivazione schermo intero**
* **Attivazione disattivazione autoaggiornamento**

L'ultima nota è che il blocco monitoraggio espone gli stati dei controlli effettuati tramite `feed RSS` (v. fig. 9) in modo da renderli consultabili tramite un qualunque client per questo formato standard. Ce ne sono di disponibili per i desktop di nuova generazione, o client email, o plugin del browser, o semplici linee di comando. Nelle versioni più recenti è disponibile anche il ''feed'' che include esclusivamente gli stati DOWN.

[[Image:|thumb|''Figure 9: SANET - blocco monitoraggio'']]

CONFIGURAZIONE (CLI)
--------------------

Nelle installazioni di SANET in reti di medie dimensioni si raggiungono fra i 5000 e i 10000 target da verificare periodicamente. Le impostazioni dei controlli da effettuare devono essere capillari per soddisfare pienamente le esigenze del fruitore del servizio, e allo stesso tempo la piattaforma deve offrire strumenti automatici per semplificare il lavoro dell'operatore per la definizione di tutti i controlli `standard`.

La configurazione è una delle sfide più interessanti che un sistema di monitoraggio scalabile a reti di medie e grandi dimensioni propone. Nello studio e la progettazione della configurazione l'obiettivo è triplice: 

* esporre la massima granularità in particolar modo nella specifica dei parametri dei controlli, evitando di aggiungere inutile complessità alle operazioni di configurazione
* offrire al sistemista di rete un ambiente noto, simile a quello che si trova di fronte per la configurazione degli apparati di rete
* facilitare la gestione con strumenti automatici 

Abbiamo visto che in SANET al nodo di rete afferiscono in modo diretto target, misure e interfacce, e a quest'ultime sono collegati ulteriori target e misure (v. fig. 3 a pag. 6). 

In questo senso la configurazione dovrà prevedere sia la definizione dei parametri di configurazione strettamente relativi alle risorse citate, sia l'innesto di target e misure in interfacce, e infine il successivo innesto di queste ultime tre entità nei nodi.

Per soddisfare queste esigenze sono state realizzate due componenti:

* un Domain Specific Language (DSL) che offre primitive peculiari ai controlli da effettuare e variabili di ambiente per il recupero di informazioni collegate in modo più o meno indiretto alla risorsa configurata che saranno poi note al momento dell'esecuzione dei controlli stessi
* una tassonomia di oggetti che in sostanza implementa il concetto di ereditarietà applicato a categorie ed istanze. Ogni nodo, interfaccia di rete, target e misura è un'istanza che eredita le proprie impostazioni e le risorse ad essa afferenti dalla categoria cui è collegata. Di conseguenza può aggiungere, modificare o anche eliminare le caratteristiche ereditate. 

Il flusso di configurazione di SANET parte con l'importazione delle categorie di libreria fornite con l'applicativo per poi passare alla definizione delle istanze per la rete di installazione. Si preferisce identificare i nodi di rete con i nomi DNS piuttosto che con un indirizzo IP per ovvi motivi.

Qualora il sistemista lo ritenesse opportuno può definire le proprie categorie con i parametri di interesse. In figura 10 riportiamo a titolo di esempio la configurazione di una categoria per il monitoraggio di un server GNU/Linux con controlli relativi alla raggiungibilità, occupazione del file system root, percentuale di occupazione del processore, della ram e della memoria di swap, reboot del sistema, carico e tempo di inattività. Per ogni controllo viene richiesto che venga storicizzato anche il valore e presentato un grafico delle misure effettuate.

[[Image:|thumb|''Figure 10: SANET - esempio di categoria server GNU/Linux'']]


[[Image:|thumb|''Figure 11: SANET - aggiungere un nodo GNU/Linux e personalizzare i controlli'']]Possiamo quindi istanziare un nodo della categoria server-linux ed aggiungere target e misure relative ad un file system specifico per l'istanza appena creata come si vede in figura 11.

Ovviamente questa breve presentazione non rende giustizia alle potenzialità espressive della CLI di SANET. Per eventuali approfondimenti si rimanda alla guida in linea che sarà disponibile congiuntamentte alla pubblicazione del software.

Importazione ed esportazione
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Un'ultima nota per dire che la CLI dispone delle operazioni basilari <tt>show configuration</tt> e <tt>configuration terminal</tt> che consentono con banali redirezioni di input e output di esportare e la configurazione dell'intero sistema oppure di importarla su una nuova installazione, ad esempio per una migrazione di versione o di server. 

[[Image:|thumb|''Figure 12: SANET - esportazione ed importazione della configurazione'']]

ARCHITETTURA
------------

SANET è un'applicazione web e per la sua realizzazione si è scelto di utilizzare Django: un framework MVC<ref name="ftn1"><sup>Gli sviluppatori di Django preveriscono Modello-Template-Vista (MTV) invece di Modello-Vista-Controllo (MVC), ma il significato dei due termmini è sostanzialmente analogo.</sup></ref> di nuova generazione scritto in Python. L'architettura di SANET poggia su un database relazionale per la memorizzazione persistente dei dati, il quale viene gestito tramite l'Object Relational Mapper (ORM) compreso in Django stesso.

Il modello dei dati implementato tramite l'ORM espone quindi la API di interazione con il database che viene utilizzata da tutti gli atri moduli di più alto livello che sono:


* **XML / XML-RPC view**: esposizione via HTTP dei dati ricavati dal modello. Costituisce la cosiddetta Web API e pertanto viene invocata:
  * dal browser, spesso per il recupero di specifiche parti di pagina via AJAX in XHTML
  * da applicazioni che richiedono i dati in un particolare formato XML. È questo il caso delle mappe topologiche realizzate con tecnologia Adobe Flex<sup>TM</sup>
  * da applicazioni che seguono lo standard XML-RPC attraverso il quale, ad oggi, SANET espone i risultati dell'attività di monitoraggio.

* **CLI**: interfaccia dedicata alla configurazione del sistema da parte del personale specializzato. La sintassi è ispirata alle CLI esposte da altri apparati di rete. Praticamente tutte le operazioni di configurazione relative ai controlli da effettuare, alla topologia della rete, e alla classificazione dei nodi di rete, avvengono in questo modulo

* '''''Figure 13: Architettura di SANET''''''''Poller agent''': agente non interattivo che si occupa di effettuare periodicamente e ciclicamente i controlli definiti per la rete di interesse seguendo la politica definita per ogni controllo.

CONCLUSIONI
-----------

Lo sviluppo di una piattaforma per il monitoraggio della rete porta con sé la sfida di razionalizzazione di un mondo eterogeneo per definizione. Possiamo dire che LABS ha colto e superato questa sfida con successo, poiché, di fatto, questo strumento contribuisce alla propra filiera per l'offerta dei servizi di supporto alle tematiche di rete.

Anche dal punto di vista applicativo gli obiettivi sono stati raggiunti: si è mantenuta l'esperienza accumulata da LABS e i risultati dell'attività di monitoraggio sono stati esposti in un'interfaccia grafica fruibile anche da utenza non specializzata. La configurazione granulare rimane volutamente accessibile solo a personale esperto.

Dal punto di vista di arricchimento del mondo open source, bisogna chiaramente valutare il compromesso fra le funzionalità introdotte e il rumore generato dalla presenza di una nuova soluzione. LABS ha ritenuto opportuno sviluppare una nuova soluzione perché ha una propria storia nell'ambito delle reti, e nel corso degli anni ha avuto esperienze di utilizzo di vari sistemi di monitoraggio proprietari e open source, senza mai riuscire a raggiungere i risultati necessari a soddisfare pienamente tutte e sole le esigenze del cliente. Per questo motivo, dovuto anche ad un modo di operare consolidato sul campo, ha deciso di aggiungere la propria voce a questo coro.

Il rilascio open source è sicuramente un arricchimento per tutti, è aggiungere la propria voce ad un coro. Non è la velleirà di diventare la più efficace e potente soluzione per il network management, ma in primis la volontà di un'azienda che ha sempre promosso l'open source, di condividere quanto si è creato, avendo preso, modificato e rielaborato dalla ricchezza della comunità.

Per quello che riguarda gli sviluppi futuri stiamo lavorando a miglioramenti significativi per le mappe della rete e la visualizzazione grafica dei risultati, l'implementazione di meccanismi di autorizzazione (di cui sono già pensate le politiche) per esportare alcune basilari operazioni di configurazione nell'interfaccia web e infine una maggiore interazione con sistemi esterni.

E siamo sicuri .... che qualche buona idea verrà dalla comunità. 

Bibliografia
""""""""""""


* `Laboratori Guglielmo Marconi, SANET – Security Architecture NETwork - http://sanet.sourceforge.net/ <http://sanet.sourceforge.net/>`__
* `RFC 1157 – A Simple Network Management Protocol - http://www.ietf.org/rfc/rfc1157.txt <http://www.ietf.org/rfc/rfc1157.txt>`__
* `RFC 1213 – Management Information Base for Network Management of TCP/IP-based internets: MIB-II - http://www.ietf.org/rfc/rfc1213.txt <http://www.ietf.org/rfc/rfc1213.txt>`__
* `Voss, Jakob (2007). Tagging, Folksonomy & Co - Renaissance of Manual Indexing? - Proceedings of the International Symposium of Information Science: pp. 234–254 - http://arxiv.org/abs/cs/0701072 <http://arxiv.org/abs/cs/0701072>`__
* `Lawrence Journal-World, Django - The web framework for perfectionists with deadlines, 2005-2006 - http://www.djangoproject.com/ <http://www.djangoproject.com/>`__
* `Wikipedia, Model-view-controller - http://en.wikipedia.org/wiki/Model-view-controller <http://en.wikipedia.org/wiki/Model-view-controller>`__
* `ISO, Information Processing Systems - OSI Reference Model - The Basic Model, 1994 - http://www.sigcomm.org/standards/iso_stds/OSI_MODEL/ISO_IEC_7498-1.TXT <http://www.sigcomm.org/standards/iso_stds/OSI_MODEL/ISO_IEC_7498-1.TXT>`__
* `Tobi Oetiker, RRDtool, 2007 - http://oss.oetiker.ch/rrdtool/ <http://oss.oetiker.ch/rrdtool/>`__


Licenza
"""""""

Questo articolo è distribuito con licenza Creative Commons Attribution Share-Alike con attribuzione tramite riferimento a Laboratori Guglielmo Marconi e backlink a `http://www.labs.it/ <http://www.labs.it>`__


