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

In passato (v. :ref:`first-attempt-failed`) 
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

Per dettagli ulteriori sulla valutazione fatta si veda l'appendice :ref:`SVG-or-FLASH`.

Nel frattempo Adobe ha rilasciato il `framework Flex` con licenza Open Source,
il che ci avrebbe consentito di implementare applicazioni in Flash senza acquistare 
licenze per costosi ambienti di sviluppo e 
anche di poter apportare modifiche a librerie secondo le esigenze del caso.

Considerata quindi anche la possibilità di mantere `vim`, il nostro IDE preferito, 
si passa a Flash o più precisamente a Flex.

La topologia della rete
-----------------------

Per rappresentare la topologia della rete si è deciso di partire dalla libreria `SpringGraph` di Mark Sheperd.

A questo punto è necessaria una premessa per capire la filosofia di SANET: 
con SANET si desidera vedere tutto e solo quello che si intende monitorare, 
quindi non solo si fa a meno dell'`autodiscovery` dei collegamenti di rete, 
ma anche degli algoritmi di posizionamento automatico dei nodi sulla mappa. Entrambe le attività
vengono affidate al sistemista di turno che ha il compito di configurare l'installazione specifica.

Per il posizionamento predefinito dei nodi di rete e dei contenitori 
si è usato, e si usa ancora oggi, `graphviz`. 
Le posizioni sono poi modificabili dai soliti sistemisti, o anche dall'utente tramite interfaccia grafica.

L'evoluzione interessante, che costituisce parte corposa in questo project work, si è verificata tra la prima 
e la seconda implementazione delle mappe di rete: vediamo ora quali erano caratteristiche e limiti della prima
versione e come sono stati superati.

Inizialmente era stato creato uno strato superiore alla libreria che:

* non usasse gli algoritmi di posizionamento automatico
* posizionasse i vertici del grafo secondo le posizioni passate dal server
* rappresentasse risorse differenti: nodi e contenitori in questa fase sono entrambi vertici della mappa
* aggiornasse periodicamente lo stato degli elementi rappresentati: dei nodi, dei contenitori e dei collegamenti.
  Lo stato dei collegamenti poteva (e può ancor oggi) essere rapprentato anche da un gradiente (trattasi di una 
  situazione temporanea o di un errore di configurazione),
  in cui le verifiche effettuate sulle estremità del link restituiscano uno stato differente.

Nella prima implementazione, quella del rilascio alla CONFSL09, ogni contenitore
aveva associata un'unica mappa in cui venivano visualizzate le risorse direttamente incluse 
e i collegamenti fra esse.

Nelle figure :fig:`TODOTODOTODO` riportiamo alcuni esempi di mappe della prima implementazione.

Nella nuova versione abbiamo voluto introdurre le seguenti funzionalità:

* rappresentare contenitori aperti e risorse contenute (clustering)
* rappresentare mappe dei contenitori adiacenti al contenitore selezionato
* visualizzare le singole interfacce di rete e il loro stato
* superare il limite del grafo semplice che non consentiva di visualizzare link multipli fra gli stessi due vertici
  (in caso di apparati con collegamenti ridondati o di contenitori con più apparati interconnessi)
* rappresentare differenti parametri: alternativamente alla rappresentazione dello stato, 
  visualizzare il carico della rete o i valori relativi al protocollo Spanning Tree fra gli switch
* aprire e chiudere contenitori da interfaccia utente

La seconda implementazione
^^^^^^^^^^^^^^^^^^^^^^^^^^

Per superare i limiti elencati si è inizialmente provata la libreria `RaVis <http://code.google.com/p/birdeye/>`__
che è più avanzata rispetto a `SpingGraph`, ma purtroppo le esigenze di personalizzazione,
con particolare riguardo al `clustering`, hanno fatto sì che tornassimo ad utilizzare
la soluzione precedente già conosciuta.

Quindi, seppur basandoci sulla stessa libreria, 
siamo ripartiti con una riscrittura totale dell'implementazione precedente,
integrando ovviamente di volta in volta alcune parti che ritenevamo congrue alle nuove esigenze.

Lato server
&&&&&&&&&&&

Innanzi tutto è stata ristrutturata la parte server. 

È stata creata una nuova applicazione Django denominata `map` che si appoggia ad
un modello di dati ibrido: da una parte esso include le classi `MapEnv` e `MapOpenContainer` 
che si appoggiano a tabelle nel database, dall'altra le 
classi `MapRoot`, `OpenContainer`, `OpenNode`, `ClosedContainer`, `ClosedNode`, `ClosedIface`, `MapEdge` che non 
hanno un corrispettivo nel database, ma fungono da classi `proxy` per gli oggetti di SANET e i corrispondenti `dot` di `graphviz`.

`MapEnv` contiene la lista delle mappe disponibili nel sistema con gli attributi relativi. 

Essa ha 2 campi fondamentali:

* `bound_container`: una chiave esterna che identifica a quale contenitore è associata la mappa,
  ossia relativamente a quale contenitore deve essere visualizzata. Possono esistere più mappe associate
  allo stesso contenitore. Per soddisfare l'esigenza di rappresentazione di mappe di adiacenza si è
  pensato di introdurre la possibilità di associare mappe arbitrarie ad ogni contenitore
* `open_containers`: che è un campo molti-a-molti attraverso il quale ogni mappa identifica i contenitori
  aperti da rappresentare. Le risorse visualizzate nella mappa saranno tutte quelle direttamente incluse 
  nei contenitori aperti oggetto di questa relazione. 
  In questo caso la tabella referenziata è proprio quella della classe `MapOpenContainer` che pertanto non ha bisogno di descrizione: 
  il suo scopo è infatti di rappresentare la relazione molti-a-molti che esiste fra `MapEnv` e `Container`.

Di seguito il semplice schema E-R dell'applicazione `map`.

TODO: schema E-R per le mappe

Come dicevamo 
`MapRoot`, `OpenContainer`, `OpenNode`, `ClosedContainer`, `ClosedNode`, `ClosedIface`, `MapEdge` non 
si appoggiano al database, ma fungono da classi `proxy` verso due tipi di oggetti: una risorsa di SANET (contenitore, nodo, interfaccia) e il corrispondente oggetto `pydot` che è il binding python per il `dot` di `graphviz`.

`MapRoot` rappresenta la radice della mappa e fa da `proxy` rispetto agli oggetti di classe `MapEnv` e `pydot.Dot`.
Ottiene le informazioni dall'istanza `MapEnv` e procede ad istanziare gli altri oggetti di classi derivate da `BaseDot` (che sarebbero poi gli `Open*` e `Closed*`). Infine processa tutti i link delle interfacce istanziate nella mappa e istanzia gli oggetti di classe `Edge`. `MapRoot` fa anche qualcosa di più: crea un container radice virtuale per ogni mappa in modo da avere coerenza anche in caso di contenitori aperti appartenenti a due gerarchie di contenitori differenti. In questo modo la rappresentazione di mappe arbitrarie è ricondotta al medesimo problema di rappresentazione della mappa di un singolo contenitore con possibilità di contenitori aperti innestati.

Le classi `Open*` e `Closed*` derivano da una classe comune `BaseDot` che ha il compito di isolare proprio la logica
di `proxy` verso le risorse di SANET e di `pydot`.

Istanziando un oggetto di classe `Open` si avrà a che fare con una risorsa di SANET e un'istanza di `pydot.Cluster`. Mentre istanziando un oggetto di classe `Closed` si avrà a che fare con una risorsa di SANET e un'istanza di `pydot.Node`. Ciò significa che è stata completamente scorporata la natura intrinseca della risorsa di SANET dalla sua rappresentazione: se si vuole aprire un contenitore si passerà da un oggetto `ClosedContainer` a un oggetto `OpenContainer` che avrà fra i suoi attributi la medesima risorsa di class `Container`.

Allo stesso modo per i nodi: se si desidera esplodere un nodo, ossia visualizzare tutte le interfacce all'interno in modo dettagliato per poter, in futuro, spostare il cavo UTP o la fibra da un'interfaccia a un'altra, è possibile farlo passando semplicemente da un `ClosedNode` a un `OpenNode`.

`MapEdge` è una classe che include un oggetto `pydot.Edge` più un'interfaccia sorgente e una destinazione.

Le mappe attuali utilizzano:

* `OpenContainer` per rappresentare i contenainer aperti
* `ClosedContainer` per rappresentare i container chiusi
* `ClosedNode` per rappresentare i nodi
* `MapEdge` per gli archi


La gerarchia di `MapRoot` viene serializzata in un documento XML.
La prima versione prevedeva solamente elementi di tipo:

* **<node>**: vertice del grafo, poteva essere un nodo o un contenitore
* **<iface>**: elemento contenuto in **<node>** con la funzione di elencare solamente i nomi delle interfacce incluse nel nodo
* **<edge>**: conteneva attributi per identificare gli endpoint di tipo <node> e gli endpoint di tipo <iface>   

Nella nuova versione è stato introdotto il tipo **<subgraph>**  che altro non è che la serializzazione 
di un'istanza di `OpenContainer`. In questa versione è rilevante l'innestamento degli elementi XML 
che stabiliscono in quale contenitore aperto si trovano gli oggetti rappresentati. Ciò è fondamentale
sotto 2 aspetti:

* il posizionamento di ogni elemento che deve essere calcolato relativamente al proprio contenitore aperto 
* lo spostamento di ogni elemento che deve considerare i limiti del proprio contenitore aperto. 
  Attualmente l'implementazione espande il contenitore aperto dell'elemento se esso viene spostato oltre il limite
  del lato destro e del lato inferiore dello stesso.

In :ref:`map-xml` riportiamo un esempio di XML prodotto dalla nuova versione di SANET per rappresentare una mappa con contenitori aperti:

Una nota importante sulle interfacce di rete: è l'istanza `ClosedNode` che serializza le interfacce 
contenute nel nodo. Come vedremo le interfacce sono fondamentali per superare in modo apparente il limite del grafo semplice: purtroppo spesso non sono visualizzate e rendono più corposo lo XML prodotto. 

Lato Flex
&&&&&&&&&

Quando si è andati ad intervenire sulla parte Flex,
abbiamo cercato di evitare qualunque modifica alla libreria SpringGraph originale,
per poter installare gli aggiornamenti (di minor version) e i bugfix in modo trasparente.

Ciò non è stato possibile a causa di alcune modifiche necessarie ad implementare i meccanismi 
in oggetti derivati, quindi sono state effettuate alcune variazioni alle `signature` delle funzioni
e degli attributi che in alcuni casi sono passati ad esempio da `private` a `protected`. 
Queste personalizzazioni sono state segnalate all'autore che in un primo momento aveva risposto
dicendo che non era contrario alle modifiche, ma lui aveva risolto in precedenza in altri modi
e avrebbe comunque verificato la nostra patch. Da quel momento in poi non si è più ricevuta alcuna risposta.

Molti sforzi sono stati dedicati all'implementazione delle mappe lato Flex.
Flex è un framework molto potente, ma molto complesso.

Di seguito spieghiamo la logica che risiede dietro i file più significativi:

* `SANETMap.mxml`: questo il file applicazione. Il main.
  Contiene l'interfaccia grafica applicativa con i controller grafici e le funzioni (astratte) che servono a recuperare i valori desiderati (**valori di stato, spanning tree, carico di rete**). In questa nuova versione
  sono stati introdotti 3 slider per: **zoom, curvatura degli archi e trasparenza dello sfondo dei contenitori aperti**.
  È presente un **check selezionabile per la visualizzazione delle interfacce di rete e dei loro nomi**. 
  Infine un bottone per il **salvataggio permanente delle posizioni dei nodi**.
* `MapInfoProxy.as`: proxy del tipo di richiesta specifica. Tutti gli `handler` di invio e ricezione si registrano in questa classe. Il controller
  grafico che seleziona il tipo di valore da visualizzare attiva il canale specifico di richiesta/ricezione.
* `NetGraph.as`: struttura del grafo di cui poi VisualNetGraph.as conterrà la rappresentazione. Include il un metodo statico per costruire il grafo dai dati XML ricevuti. 
* `VisualNetGraph.as`: rappresentazione grafica della mappa. Qui vengono innestati i contenitori aperti (a partire dal contenitore radice virtuale) e le risorse in essi incluse. Inoltre vengono ospitati gli archi che devono poter connettere risorse presenti anche in container aperti differenti e disattivato l'algoritmo automatico di posizionamento.
* `SANETEdge.as`: rappresentazione degli archi. Qui si gestisce la direzione e il gradiente. 2 trucchi sono stati usati per superare le limitazioni del grafo semplice: il primo riguarda la direzione e il secondo i link multipli. La direzione viene passata nell'elemento XML <edge> e viene ricalcolata proprio in questo file. Invece l'intuizione che sta dietro al superamento dei link multipli fra due vertici, consiste nella consapevolezza che le interfacce di rete odierne (`wired`) se connesse, interconnettono sempre e solo 2 endopoint. In questo caso va benissimo il grafo semplice. Quindi nelle nuove mappe l'arco non è più costruito fra 2 nodi o 2 container chiusi, ma fra le interfacce che essi stessi contengono siano esse visibili o invisibili.
* `NestedItem.as`: implementa la logica dell'innestamento degli elementi XML spiegata sopra.
* `SANETViewFactory.as`: in fase di rendering, istanzia l'oggetto `Visual*` specifico relativamente ai dati XML di un elemento di `NetGraph.as` dato in input
* `VisualBaseNetGraphElement.mxml`: classe base per gli elementi della mappa. Definisce il canvas e gli handler per gli eventi nell'interazione con l'utente (mouseover, mouseout, doubleClick, ...) 
* `VisualCloseContainer.as`: rappresentazione di contenitore chiuso. È usato anche per la rappresentazione dei nodi chiusi. Dal punto di vista dell'interfaccia sono icone che hanno un'etichetta, un url, stessi handler per la gestione di eventi e conoscenza delle interfacce di rete incluse (`VisualIface.as`)
* `VisualOpenContainer.as`: rappresentazione di container aperto. Un riquadro con posizionamento diverso dell'etichetta, bordi colorati con il proprio stato (derivante dalle risorse incluse nello stesso) e conoscenza degli oggetti in esso contenute (`VisualOpenContainer.as`, `VisualCloseContainer.as`)
* `VisualIface.as`: rappresentazione grafica delle interfacce di rete. Esse sono cerchi posizionati al centro del proprio `VisualCloseContainer.as`. Di esse non è possibile salvare le posizioni. È molto interessante poter visualizzare lo stato di una singola interfaccia di rete.

Purtroppo al momento della scrittura di questo documento manca l'implementazione lato server del recupero dei valori quali carico di rete e stato dello spanning tree e quindi, sebbene l'implementazione lato client sia praticamente pronta, tali parametri non possono essere rappresentati nella versione 0.4 di SANET.

`SANETMap.swf` è il file compilato dell'applicazione Flash, occupa circa 400KB
ed è l'unico che viene scaricato dal browser (e messo in cache) per la visualizzazione delle mappe in SANET.

Un'ultima nota su come ancora una volta il software libero ci è stato di aiuto 
e in particolare il modulo di terze parti Flex Thunderbolt
che consente di effettuare il log dei messaggi nell'estensione FireBug di Firefox 
(estensione fondamentale per ogni sviluppatore web).

 
Interazione fra Javascript e Flex
&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&

A corredare il tutto non poteva mancare un modulo per interagire con le mappe direttamente da Javascript
e viceversa. Questo è il modulo Flex Ajax Bridge provvisto da Adobe stessa (file `bridge/FABridge.as`) ed è 
stato fondamentale per dare coerenza nel tooltip informativo delle risorse e nel menu contestuale.

TODO: immagine mappe nuove
