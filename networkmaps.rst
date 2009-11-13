Uno sforzo importante: le mappe
===============================

Dedichiamo un capitolo all'implementazione ed evoluzione delle mappe di rete di SANET,
dato che proprio questa evoluzione è parte consistente del project work rilasciato insieme con questo documento.

Dopo la rappresentazione dell'attività di monitoraggio in un'interfaccia web 2.0 si è deciso di implementare 
subito la visualizzazione delle mappe di rete per i contenitori.

La scelta fra SVG o Flash
-------------------------

La decisione di realizzare mappe interattive della rete ci ha subito posti
davanti alla scelta fra le tecnologie grafiche per il web: SVG o Flash.

In passato (TODO v. :ref:`unprimotentativofallito`) 
era stata implementata una versione basata su `Scalable Vector Graphic` (SVG) in modo da ottenere
un risultato più aperto rispetto all'alternativa Flash di Macromedia ora Adobe.

Da quello che è stata la nostra esperienza, SVG risulta essere lento, e,
paradossalmente, anche se è uno standard `più aperto` di Flash, Flash risulta essere più
diffuso fra le varie piattaforme e, purtroppo, la compatibilità con Internet Explorer è un fattore 
critico in questi casi. 

Inoltre, se da un lato lo standard SVG è comodo in quanto XML puro scrivibile comodamente anche tramite
il sistema di template messo a disposizione da Django, dall'altro 
abbiamo molti esempi di applicazioni web che usano Flash,
ma non abbiamo praticamente alcun caso reale in cui si usi SVG.

Per dettagli ulteriori sulla valutazione fatta si veda l'appendice :ref:`SVG_or_FLASH`.

Nel frattempo Adobe ha rilasciato il framework Flex con licenza Open Source,
il che ci avrebbe consentito di implementare applicazioni in Flash senza acquistare 
licenze per costosi ambienti di sviluppo e 
anche di poter apportare modifiche a librerie secondo le esigenze del caso.

Considerata quindi anche la possibilità di mantere `vim`, il nostro IDE preferito, 
si passa a Flash o più precisamente a Flex.

La topologia della rete
-----------------------

Per rappresentare la topologia della rete si è deciso di partire dalla libreria SpringGraph di Mark Sheperd.

A questo punto è necessaria una premessa per capire la filosofia di SANET: 
con SANET si desidera vedere tutto e solo quello che si intende monitorare, 
quindi non solo si fa a meno dell'autodiscovery dei collegamenti di rete, 
ma anche degli algoritmi di posizionamento automatico dei nodi sulla mappa. Entrambe le attività
vengono affidate al sistemista di turno che ha il compito di configurare l'installazione specifica.

Diciamo subito che per il posizionamento predefinito dei nodi di rete e dei contenitori 
si è usato, e si usa ancora oggi, `graphviz`. Le posizioni sono poi modificabili dai soliti sistemisti, 
o anche dall'utente tramite interfaccia grafica.

L'evoluzione interessante, che costituisce parte corposa in questo project work, si è verificata tra la prima 
e la seconda implementazione delle mappe di rete: vediamo ora quali erano caratteristiche e limiti della prima
versione e come sono stati superati.

Inizialmente si è creato uno strato superiore alla libreria che:

* non usasse gli algoritmi di posizionamento automatico
* posizionasse i vertici del grafo secondo le posizioni passate dal server
* rappresentasse risorse differenti: nodi e contenitori in questa fase sono entrambi vertici della mappa.
* aggiornasse periodicamente lo stato degli elementi rappresentati: dei nodi, dei contenitori e dei collegamenti.
  Lo stato dei collegamenti può essere rapprentato anche da un gradiente in caso temporaneo (o di errore di configurazione),
  in cui le verifiche effettuate sulle estremità del link restituiscano uno stato differente.

Nella prima implementazione, quella del rilascio alla CONFSL09, ogni contenitore
aveva associata un'unica mappa in cui venivano visualizzate le risorse direttamente incluse 
e i collegamenti fra esse.

Nelle figure :fig:`TODOTODOTODO` riportiamo alcuni esempi di mappe della prima implementazione.

Si volevano introdurre le seguenti funzionalità:

* rappresentare contenitori aperti e risorse contenute (clustering)
* rappresentare mappe dei contenitori adiacenti al contenitore selezionato
* visualizzare le singole interfacce di rete e il loro stato
* superare il limite del grafo semplice che non consentiva di visualizzare link multipli fra gli stessi due vertici
  (in caso di apparati con collegamenti ridondati o di contenitori con più apparati interconnessi)
* rappresentare differenti parametri: alternativamente alla rappresentazione dello stato, visualizzare il carico della rete o i valori relativi al protocollo Spanning Tree fra gli switch
* aprire e chiudere contenitori da interfaccia utente

La seconda implementazione
^^^^^^^^^^^^^^^^^^^^^^^^^^

Per superare i limiti elencati si è inizialmente provata la libreria `RaVis http://code.google.com/p/birdeye/`
che è più avanzata rispetto a SpingGraph, ma purtroppo le esigenze di personalizzazione,
con particolare riguardo al clustering, hanno fatto sì che tornassimo ad utilizzare
la precedente che conoscevamo meglio.

Quindi siamo ripartiti con una riscrittura totale dell'implmementazione precedente,
integrando ovviamente di volta in volta alcune parti che ritenevamo congrue alle nuove esigenze.

Lato server
&&&&&&&&&&&

Innanzi tutto è stata ristrutturata la parte server. 

È stata creata una nuova applicazione Django denominata `map` che si appoggia ad
un modello di dati `ibrido`. Da una parte esso include le classi `MapEnv` e `MapOpenContainer` 
che si appoggiano a tabelle nel database. Dall'altra le 
classi `MapRoot`, `OpenContainer`, `OpenNode`, `ClosedContainer`, `ClosedNode`, `ClosedIface`, `MapEdge` che non 
hanno un corrispettivo nel database, ma fungono da classi `proxy` gli oggetti di SANET e i corrispondenti `dot` di `graphviz`.

`MapEnv` contiene la lista delle mappe disponibili nel sistema con gli attributi relativi. 
Essa ha 2 campi fondamentali:

* `bound_container`: una chiave esterna che identifica a quale contenitore è associata la mappa,
  ossia relativamente a quale contenitore deve essere visualizzata. Possono esistere più mappe associate
  allo stesso contenitore. Per soddisfare l'esigenza di rappresentazione di mappe di adiacenza si è
  pensato di introdurre la possibilità di associare mappe arbitrarie ad ogni contenitore
* `open_containers`: che è un campo molti-a-molti attraverso il quale ogni mappa identifica i contenitori
  aperti da rappresentare. Le risorse visualizzate nella mappa saranno tutte quelle direttamente incluse 
  nei contenitori aperti. 
  In questo caso la tabella referenziata è proprio quella della classe `MapOpenContainer` che pertanto non ha bisogno di descrizione: il suo scopo è infatti di rappresentare la relazione molti-a-molti che esiste fra MapEnv e Container.

Di seguito il semplice schema E-R dell'applicazione `map`.

TODO: schema E-R per le mappe

Come dicevamo 
`MapRoot`, `OpenContainer`, `OpenNode`, `ClosedContainer`, `ClosedNode`, `ClosedIface`, `MapEdge` non 
  si appoggiano al database, ma fungono classi `proxy` verso due tipi di oggetti: una risorsa di SANET (contenitore, nodo, interfaccia) e il corrispondente oggetto `pydot` che è il binding python per il `dot` di `graphviz`.

`MapRoot` rappresenta la radice della mappa e fa da proxy rispetto agli oggetti di classe `MapEnv` e `pydot.Dot`.
Ottiene le informazioni dall'istanza `MapEnv` e procede ad istanziare gli altri oggetti di classi derivate da `BaseDot` (che sarebbero poi gli `Open*` e `Closed*`). Infine processa tutti i link delle interfacce istanziate nella mappa e istanzia gli oggetti di classe `Edge`. `MapRoot` fa anche qualcosa di più: crea un container radice virtuale per ogni mappa in modo da avere coerenza anche in caso di contenitori aperti appartenenti a due gerarchie di contenitori differenti. In questo modo la rappresentazione di mappe arbitrarie è ricondotta al medesimo problema di rappresentazione della mappa di un singolo contenitore con possibilità di contenitori aperti innestati.

Le classi `Open*` e `Closed*` derivano da una classe comune `BaseDot` che ha il compito di isolare questa logica
di `proxy` verso le risorse di SANET e di `pydot`.

Istanziando un oggetto di classe `Open` si avrà a che fare con una risorsa di SANET e un'istanza di `pydot.Cluster`. Mentre istanziando un oggetto di classe `Closed` si avrà a che fare con una risorsa di SANET e un'istanza di `pydot.Node`. Ciò significa che è stata completamente scorporata la natura intrinseca della risorsa di SANET dalla sua rappresentazione: se si vuole aprire un contenitore si passerà da un oggetto `ClosedContainer` a un oggetto `OpenContainer` che includeranno al proprio interno la stessa risorsa `Container` di SANET.

Allo stesso modo per i nodi: se voglio esplodere un nodo, ossia visualizzare tutte le interfacce all'interno in modo dettagliato per poter, in futuro, spostare il cavo UTP o la fibra da un'interfaccia a un'altra, posso farlo passando semplicemente da un `ClosedNode` a un `OpenNode`.

`MapEdge` è una classe che include un oggetto `pydot.Edge` e un'interfaccia sorgente e una destinazione.

Le mappe attuali utilizzano:

* `OpenContainer` per rappresentare i contenainer aperti
* `ClosedContainer` per rappresentare i container chiusi
* `ClosedNode` per rappresentare i nodi
* `MapEdge` per gli archi


Gli elementi innestati in `MapRoot` mappa vengono serializzati in un documento XML.
La prima versione prevedeva solamente elementi di tipo:

* **<node>**: vertice del grafo, poteva essere un nodo o un contenitore
* **<iface>**: elemento contenuto in **<node>** con la funzione di elencare solamente i nomi delle interfacce incluse nel nodo
* **<edge>**: conteneva attributi per identificare gli endpoint di tipo <node> e gli endpoint di tipo <iface>   

Nella nuova versione è stato introdotto il tipo <subgraph> che altro non è che la serializzazione 
di un'istanza di `OpenContainer`. In questa versione è rilevante l'innestamento degli elementi XML 
che stabiliscono in quale contenitore aperto si trovano gli oggetti rappresentati. Ciò è fondamentale
sotto 2 aspetti:

* il posizionamento di ogni elemento che deve essere calcolato relativamente al proprio contenitore aperto 
* lo spostamento di ogni elemento che deve considerare i limiti del proprio contenitore aperto. 
  Attualmente l'implementazione espande il contenitore aperto dell'elemento se esso viene spostato oltre il limite
  del lato destro e del lato inferiore dello stesso.

In figura :fig:`TODOTODOTODO` un esempio dell'XML prodotto dalla nuova implementazione.


Una nota importante sulle interfacce di rete: è l'istanza `ClosedNode` che serializza le interfacce 
contenute nel nodo. Come vedremo le interfacce sono fondamentali per superare in modo apparente il limite del grafo semplice: purtroppo spesso non sono visualizzate e rendono più corposo lo XML prodotto. 


Lato Flex
&&&&&&&&&


Quando si è andati ad intervenire sulla parte Flex,
si è cercato di evitare qualunque modifica alla libreria SpringGraph originale,
per poter installare gli aggiornamenti (di minor version) e i bugfix in modo trasparente.

Ciò non è stato possibile a causa di alcune modifiche necessarie ad implementare i meccanismi 
in oggetti derivati, quindi sono state effettuate alcune variazioni alle `signature` delle funzioni
e degli attributi che in alcuni casi sono passati ad esempio da `private` a `protected`. 
Queste personalizzazioni sono state segnalate all'autore che in un primo momento aveva risposto
dicendo che ovviamnete non c'era alcun problema, ma che lui aveva risolto in precedenza in altri modi
e avrebbe comunque verificato la nostra patch. Da quel momento in poi non si è più ricevuta alcuna risposta.




Pydot, Clustering etc. etc.
 

