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

  ## 2a Parte - Controllo altri rischi? Area studio ristretta

Prendo gli elettrodotti e faccio un merge di tutti e tre (i due da OSM e quello BDTRE) per poi fare un clip su Provincia di Torino, Alta Val Susa e Val Chisone. Dopo, faccio un buffer di 5/10 metri per lato per fare un corridoio intorno alle campate. Questo buffer lo uso per clippare il corine land cover da geoportale piemonte e vedere quale sia l'uso del suolo in un quel corridoio.. 
Secondo step è andare a vedere e analizzare solamente dove uso del suolo comprende copertura arborea... Scarico plugin Group Stats di qgis che mi crea una sorta di pivot molto utile per avere una idea delle numeriche.
Non ci sono molte centraline con anemomentro.. Forse meglio fare in Val di Susa. 



  
### Uso Suolo Piemonte
Andare a valutare rispetto ad altri dati come il CLC del Piemonte (edizione 2018) --> come sono le aree in cui si trovano queste infrastrutture?
https://www.geoportale.piemonte.it/geonetwork/srv/ita/catalog.search;jsessionid=1907C77A00157E43A595F9A5DD20956C.clu001node01_tc1-catalogo-gnosreg?node=srv#/metadata/r_piemon:35df8a16-5d89-461f-a0f2-abc2180713d2
Si può usare questo dato di maggiore dettaglio rispetto al CLC Copernicus. Estrazione dei dati 2023 solo per aree provincia di Cuneo

### Vento
Altro fattore "critico" potrebbe essere il vento --> Quali dati a disposizione ci sono? Raster prodotti online da winddata --> Bassa risoluzione oppure, partendo da dati ARPA Piemonte e API tirare fuori dati "storici" e valutare come aggregarli/spazializzarli. 
Potrebbe essere una opzione/test, direttamente da POSTGIS andare a salvare tabella risultati e fare un Kriging o un IDW magari con due raster, uno su velocità media del vento e un altro per la velocità massima. 
Estrazione stazioni meteo con anemometri e provincia di Cuneo

### Valutazione della canopy da dati satellitari
Utilizzo dati provenienti da questo studio https://nlang.users.earthengine.app/view/global-canopy-height-2020. Possibilità di avere dati 10x10 m con standard deviation relativamente all'altezza della canopy.
https://www.research-collection.ethz.ch/handle/20.500.11850/609802. 
Scaricato due geotif che coprono area del piemonte (tranne Lingua a est) 3x3 gradi e successivamente mergiati e poi clippati su estensione regione Piemonte. 


## 3a parte WebGIS per andare a creare punti con trigger automatici
Possibilità di utilizzarlo come strumento di pianificazione quick and dirty. Creazione di un punto e valutare eventuali distanze di quel punto dai rischi visti sopra.
Si potrebbe fare con dei trigger --> Punto per rischi naturali? Creazione colonne con distanza minima da quegli elementi. 
