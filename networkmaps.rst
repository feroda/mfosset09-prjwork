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
Gli elementi che sono presenti nella mappa vengono definiti in un documento XML.
La prima versione prevedeva solamente elementi di tipo:

* **<node>**: vertice del grafo, poteva essere un nodo o un contenitore
* **<iface>**: elemento contenuto in **<node>** con la funzione di elencare solamente i nomi delle interfacce incluse nel nodo
* **<edge>**: conteneva attributi per identificare gli endpoint di tipo <node> e gli endpoint di tipo <iface>   

Nella nuova versione è stato introdotto il tipo <subgraph> che identifica il cosiddetto `cluster`
ossia il contenitore aperto. In questa versione è rilevante l'innestamento degli elementi XML 
che stabiliscono in quale contenitore aperto si trovano gli oggetti rappresentati. Ciò è fondamentale
sotto 2 aspetti:

* il posizionamento di ogni elemento che deve essere calcolato relativamente al proprio contenitore aperto 
* lo spostamento di ogni elemento che deve considerare i limiti del proprio contenitore aperto. 
  Attualmente l'implementazione espande il contenitore aperto dell'elemento se esso viene spostato oltre il limite
  del lato destro e del lato inferiore dello stesso.




Lato Flex
&&&&&&&&&


Quando si è andati ad intervenire sulla parte Flex,
si è cercato di evitare qualunque modifica alla libreria originale,
per poter installare gli aggiornamenti (di minor version) e i bugfix in modo trasparente.

Ciò non è stato possibile a causa di alcune modifiche necessarie ad implementare i meccanismi 
in oggetti derivati, quindi sono state effettuate alcune variazioni alle `signature` delle funzioni
e degli attributi che in alcuni casi sono passati ad esempio da `private` a `protected`. 
Queste personalizzazioni sono state segnalate all'autore che in un primo momento aveva risposto
dicendo che ovviamnete non c'era alcun problema, ma che lui aveva risolto in precedenza in altri modi
e avrebbe comunque verificato la nostra patch. Da quel momento in poi non si è più ricevuta alcuna risposta.




Pydot, Clustering etc. etc.
 

