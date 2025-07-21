# Idee e dati di lavoro Michele
## Obiettivo
Tramite dati liberamente accessibili su geoportali provare a capire se possibile declinare una nuova accezione di sicurezza energetica valutando quali reti/strutture ricadono all'interno di aree già interessate oppure in aree a rischio per particolare eventi naturali come alluvioni, incendi, valanghe e dissesti. 
A questi eventi naturali, si possono aggiungere anche informazioni relative alle isole di calore --> Regione Piemonte aveva già prodotto dati con ISTAT 2011, LST (Land Surface Temperature) e NDVI. Si può valutare un aggiornamento? In che modo? Utilizzare GEE per non andare a scaricare mille dati (ora sono disponibili Landsat 8 e 9 per LST e anche NDVI ), dati su censimento popolazione (https://www.geoportale.piemonte.it/geonetwork/srv/ita/catalog.search#/metadata/r_piemon:029976c9-3dfc-45dc-bb62-8c516a272578). MODIS / LANDSAT e/o 9 / COPERNICUS
Anche le isole di calore possono essere trattate come eventi naturali "estremi" da andare a valutare che possono andare a mettere in pericolo particolari Infrastrutture Critiche (in particolare per elettricità direi)


## Dati 
### Istat
* Sezioni censuarie con relativi dati
### Piemonte
* Incendi (https://www.geoportale.piemonte.it/geonetwork/srv/ita/catalog.search#/metadata/r_piemon:5d8d0dd5-c0ac-4403-afb9-c116ccefedc8). Formato SHP
* Osservatorio del dissesto (https://www.geoportale.piemonte.it/geonetwork/srv/ita/catalog.search#/metadata/cmto:62b371f0-8a20-4839-8eab-33de9924f417) che include dati su dissesti gravitativi (frane), misti gravitativo-idraulico (conoidi alluvionali), idraulici (aree inondate, aree inondabili e dissesti legati alla dinamica torrentizia), legati a caduta di masse nevose (valanghe). Formato WFS
* Infrastrutture acquedotti da SIRI (Sistema informativo Risorse Idriche)
* Elettrodotti Alta Tensione (Rete trasmissione nazionale di Terna) accessibile tramite BDTRE e Carta Tecnica regionale
* Impianti relativi a sottoservizi accessibili sempre tramite BDTRE Piemonte e, in particolare, all'interno della classe MANUFATTO INDUSTRIALE (MN_IND - 020201) elementri in cui il MN_IND_TY sia Cabina Trasformazione Energia, Cabina Rete Acqua, Cabina rete gas). Per reference si può fare riferimento al catalogo oggetti BDTRE (Utilizzata Versione 2.2.2 - 24 Marzo 2025)
* Dissesti PAI --> https://www.geoportale.piemonte.it/geonetwork/srv/ita/catalog.search#/metadata/r_piemon:6640f61c-74be-4ae8-964a-3b466fda813f
* IFFI idrogeo per dati aggiornati
* Corine Land cover ( https://www.geoportale.piemonte.it/geonetwork/srv/ita/catalog.search#/metadata/r_piemon:35df8a16-5d89-461f-a0f2-abc2180713d2)

Sarebbe interessante anche, in un'ottica di aree sempre più utilizzate anche per altre forme di produzione energetica (Impianti fotovoltaici, biogas?) valutare se anche queste infrastrutture siano "sicure" rispetto ai criteri puramente di confronto di cui sopra. 

### Emilia Romagna
**Parte TBC**
A questo proposito, mi piacerebbe provare a utilizzare dati telerilevati per mettere a confronto se possibile, situazioni pre-post eventi alluvionali. Come idea mi è venuta quindi quella di provare a vedere quanto possa essere possibile combinare dati ad alta risoluzione prodotti da Maxar nel suo open data program ( https://www.maxar.com/open-data/Emilia-Romagna-Italy-flooding) con le informazioni presenti nel geoportale dell'Emilia Romagna a seguito degli eventi alluvionali con perimetrazione e catalogazione dei dissesti (https://geoportale.regione.emilia-romagna.it/approfondimenti/emergenza-maggio-23/emergenza-rer-maggio-2023-servizi). 
Provando a utilizzare un recente pacchetto python sviluppato da Dr. Qiusheng Wu (https://geoai.gishub.org/) vorrei provare a valutare edifici e campi fotovoltaici (se presenti) interessati dagli eventi alluvionali.

## Flusso di lavoro

### Infrastruttura DB
Prima di tutto sto valutando se copiare dati online (magari utili anche per Matteo e Laura) oppure fare "copia" offline per non appesantire inutilmente il server con dati su dati.
1. Per reti e cabine primo controllo per vedere in quale/quali sezioni censuarie ricadono
  * Per gli elementi lineari saranno probabilmente più sezioni censuarie. Possibilità di prendere la totalità della sezione con relativo numero di abitanti oppure aggregare sotto qualche altro metodo. Questo può funzionare per acquedotto ma non rete elettriche è AT e quindi non direttamente coinvolta nella distribuzione alle comunità.  
2. Confronto con dati relativi a dissesti e incendi:
  * A che distanza sono? Sono stati coinvolti? C'è un buffer di sicurezza?
  * Aspetto da considerare penso sia anche la differenza tra le reti --> reti interrate possono magari venire toccate meno dagli incendi rispetto a quelle aeree (acquedotto vs rete elettrica AT), tuttavia se sono coinvolte in dissesti questo porta conseguenze rilevanti.
  * Creazione per ogni record di più colonne tramite trigger indicante (per le diverse tipologie di dissesto) a che distanza si trovano? --> Valido per elementi puntuali
  * Per elementi lineari possibile pensare di vedere che % della lunghezza sia all'interno aree a "rischio". Stessa cosa sempre creando più colonne a seconda del diverso record scelto

Creazione di trigger? Al momento della creazione di un elemento andare a lanciare questi controlli con postGIS per avere le informazioni:
ATTENZIONE: 
* Si dovrebbe fare copia edifici (per esempio sottostazioni elettriche) per poter fare un update andando quindi a copiare dentro. Quindi ha più senso fare un trigger oppure forse fare una vista andando a lavorare direttamente da DB può essere una soluzione migliore?
* Il numero di elementi non è poco (circa 3000) però comunque aspetti da tenere in considerazione ci sono.
