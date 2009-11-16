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
  In questo modo avremmo evitato il `problema Google`: un'azienda potente che inglobasse SANET fornendo
  un servizio di rete basato su di esso, ma senza essere obbligata a restituire il codice alla comunità
  e tantomeno a noi
* **Dove sviluppare il codice** o meglio dove gestire lo sviluppo del codice e la comunità degli utenti: 
  la risposta è stata Sourceforge.net . Il portale di sviluppo software open source per definizione.
  Sourceforge.net offre il sistema di versionamento SVN e la recente possibilità di installare applicazioni esterne quali TRAC, MediaWiki o Wordpress, ci hanno convinto che sarebbe stata una scelta appropriata.
  Infatti, in particolare grazie a TRAC, avremmo potuto prosegure senza traumi con il modo di lavorare
  e il dialogo con i sistemisti LABS
* **Quando uscire con la prima release**: una decisione non banale. Abbiamo deciso di uscire non appena
  ci fossimo liberati totalmente del vecchio `pinger`. In aiuto ci è venuta anche l'occasione della ConfSL09: un motivo in più per accellerare i lavori e rilasciare entro il 12 giugno 2009. 


Il primo rilascio alla ConfSL09
-------------------------------

Il 12 giugno 2009 è il primo rilascio di SANET.
SANET esce già nella sua versione 2.0 

 
TODO: immagine dalla cattedrale al bazaar

From the Cathedral to the Bazaar: An
Empirical Study of the Lifecycle of Volunteer
           Community Projects
        Andrea Capiluppi, and Martin Michlmayr

Impostato ambiente TRAC
Creato canale irc #sanet su freenode

Documentazione e altro
----------------------

Rilasciata documentazione completa in inglese
Spostata tutta la storia di SVN su sourceforge
con problemi di ... oscuramento password vecchie,
applicazione licenza retroattiva


Il secondo rilascio al termine del Master FOSSET0809
----------------------------------------------------

TODO ?Di cosa abbiamo bisogno....?!?

La versione SVN troppo avanti rispetto al rilascio
Congelare la release e fare il tarball
Creare setup.py per l'installazione
Estrapolare Django.

