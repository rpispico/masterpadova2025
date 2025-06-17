# FLUSSO LAVORO

## 1a Parte - Controllo as is

Con i dati scaricati da openstreetmap e geoportale regione Piemonte, andare a verificare la situazione attuale rispetto alle infrastrutture e a dati proveninenti da PAI, PGRA e informazioni incendi. 
Quindi: 

INPUT DATA:
- INFRASTRUTTURE
  - CaviMTBT_Piemonte (da OSM)
  - cabine elettriche piemonte
  - cavi AT Piemonte
  - elettrodotti
  - impianti energetici
  - power plant piemonte
  - tralicci Piemonte
- RISCHI/ESPOSIZIONE
  - Punti innesco incendi
  - Aree percorse dal fuoco
  - pai aree esondate
  - pai aree frana
  - pai aree valanga
  - pai conoidi
  - pai linee esondate (TBC)
  - pai linee valanga (tbc)
  - pai punti di frana (TBC)
  - pgra pericolosità reticolo principale
  - pgra pericolosità reticolo secondario

Qui verifica puramente quantitativa verificando quanti elementi attualmente sono all'interno delle diverse aree e tirare fuori valutazione
Questi dati come possono essere messi in relazione con dati istat? Magari si può valutare la distanza tra le diverse cabine primarie disponibili e valutare magari con Poligoni di Tyssen? Paper Bosisio. 

  ## 2a Parte - Controllo altri rischi? Vediamo come sono messo

Andare a valutare rispetto ad altri dati come il CLC del Piemonte (edizione 2018) --> come sono le aree in cui si trovano queste infrastrutture?

Altro fattore "critico" potrebbe essere il vento --> Quali dati a disposizione ci sono? Raster prodotti online da winddata --> Bassa risoluzione oppure, partendo da dati ARPA Piemonte e API tirare fuori dati "storici" e valutare come aggregarli/spazializzarli. 
Potrebbe essere una opzione/test, direttamente da POSTGIS andare a salvare tabella risultati e fare un Kriging o un IDW magari con due raster, uno su velocità media del vento e un altro per la velocità massima. 

Valutazione della canopy da dati satellitari? 

## 3a parte WebGIS per andare a creare punti con trigger automatici
Possibilità di utilizzarlo come strumento di pianificazione quick and dirty. Creazione di un punto e valutare eventuali distanze di quel punto dai rischi visti sopra.
Si potrebbe fare con dei trigger --> Punto per rischi naturali? Creazione colonne con distanza minima da quegli elementi. 
