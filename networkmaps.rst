Uno sforzo importante: le mappe
===============================

Dopo la rappresentazione dell'attività di monitoraggio in un'interfaccia web 2.0 si è deciso di implementare 
subito la visualizzazione delle mappe di rete per i contenitori.

La scelta fra SVG o Flash
-------------------------

In passato era stata implementata una versione basata su `Scalable Vector Graphic` (SVG) in modo da ottenere
un risultato più aperto rispetto all'alternativa Flash di Macromedia ora Adobe.

Se da un lato lo standard SVG è comodo in quanto XML puro scrivibile comodamente anche tramite
il sistema di template messo a disposizione da Django, dall'altro si verificano molte mancanze
e quindi si è valutato il passaggio da SVG a Flash.

Da quello che è stata l'esperienza fatta, SVG risulta essere lento, e,
paradossalmente, anche se è uno standard `più aperto` di Flash, Flash risulta essere più
compatibile fra varie piattaforme e, purtroppo, la compatibilità con Internet Explorer è un fattore 
critico in questi casi. 

Inoltre abbiamo molti esempi di applicazioni web che usano Flash,
ma non abbiamo praticamente alcun caso reale in cui si usi SVG.

Per un dettaglio della valutazione fatta si veda l'appendice :ref:`SVG_or_FLASH`.

Nel frattempo Adobe ha rilasciato il framework Flex con licenza Open Source,
il che ci avrebbe consentito di implementare applicazioni in Flash senza acquistare 
licenze per ambienti di sviluppo e anche di poter apportare modifiche a librerie secondo le esigenze del caso.

Considerato quindi che si può anche continuare ad usare `vim`, il nostro IDE preferito, 
si passa a Flash o più precisamente a Flex.

La topologia della rete
-----------------------

TODO: vedere se portarlo in una sezione fra i 2 rilasci...

Per rappresentare la topologia della rete si è deciso di partire dalla libreria SpringGraph di Mark Sheperd.
A questo punto bisogna tenere presente la filosofia di SANET: LABS vuole vedere tutto e solo quello 
che intende monitorare, quindi non solo si fa a meno dell'autodiscovery dei collegamenti di rete, 
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
  in cui le verifiche effettuate sulle estremità del link restituiscano uno stato differente

Nella prima implementazione, quella del rilascio alla CONFSL09, ogni contenitore
aveva associata un'unica mappa in cui venivano visualizzate le risorse direttamente incluse e i collegamenti fra esse.

Si volevano superare i seguenti limiti:

* rappresentazione di contenitori aperti e risorse contenute (clustering)
* rappresentazione della mappa dei contenitori adiacenti al contenitore selezionato
* visualizzare lo stato delle singole interfacce
* superare il limite del grafo semplice che non consentiva la visualizzazione di link multipli fra gli stessi due vertici
* rappresentazione di differenti parametri: alternativamente alla rappresentazione dello stato, visualizzare il carico della rete o lo stato del protocollo Spanning Tree fra gli switch

* possibilità di aprire e chiudere contenitori da interfaccia utente


Grafi semplici

Pydot, Clustering etc. etc.
 

