# Idee e dati di lavoro Michele
## Obiettivo
Tramite dati liberamente accessibili su geoportali provare a capire se possibile declinare una nuova accezione di sicurezza energetica valutando quali reti/strutture ricadono all'interno di aree già interessate oppure in aree a rischio per particolare eventi naturali come alluvioni, incendi, valanghe e dissesti. 

## Dati 
### Istat
* Sezioni censuarie con relativi dati
### Piemonte
* Incendi (https://www.geoportale.piemonte.it/geonetwork/srv/ita/catalog.search#/metadata/r_piemon:5d8d0dd5-c0ac-4403-afb9-c116ccefedc8). Formato SHP
* Osservatorio del dissesto (https://www.geoportale.piemonte.it/geonetwork/srv/ita/catalog.search#/metadata/cmto:62b371f0-8a20-4839-8eab-33de9924f417) che include dati su dissesti gravitativi (frane), misti gravitativo-idraulico (conoidi alluvionali), idraulici (aree inondate, aree inondabili e dissesti legati alla dinamica torrentizia), legati a caduta di masse nevose (valanghe). Formato WFS
* Infrastrutture acquedotti da SIRI (Sistema informativo Risorse Idriche)
* Elettrodotti Alta Tensione (Rete trasmissione nazionale di Terna) accessibile tramite BDTRE e Carta Tecnica regionale
* Impianti relativi a sottoservizi accessibili sempre tramite BDTRE Piemonte e, in particolare, all'interno della classe MANUFATTO INDUSTRIALE (MN_IND - 020201) elementri in cui il MN_IND_TY sia Cabina Trasformazione Energia, Cabina Rete Acqua, Cabina rete gas). Per reference si può fare riferimento al catalogo oggetti BDTRE (Utilizzata Versione 2.2.2 - 24 Marzo 2025)

Sarebbe interessante anche, in un'ottica di aree sempre più utilizzate anche per altre forme di produzione energetica (Impianti fotovoltaici, biogas?) valutare se anche queste infrastrutture siano "sicure" rispetto ai criteri puramente di confronto di cui sopra. 

### Emilia Romagna
**Parte TBC**
A questo proposito, mi piacerebbe provare a utilizzare dati telerilevati per mettere a confronto se possibile, situazioni pre-post eventi alluvionali. Come idea mi è venuta quindi quella di provare a vedere quanto possa essere possibile combinare dati ad alta risoluzione prodotti da Maxar nel suo open data program ( https://www.maxar.com/open-data/Emilia-Romagna-Italy-flooding) con le informazioni presenti nel geoportale dell'Emilia Romagna a seguito degli eventi alluvionali con perimetrazione e catalogazione dei dissesti (https://geoportale.regione.emilia-romagna.it/approfondimenti/emergenza-maggio-23/emergenza-rer-maggio-2023-servizi). 
Provando a utilizzare un recente pacchetto python sviluppato da Dr. Qiusheng Wu (https://geoai.gishub.org/) vorrei provare a valutare edifici e campi fotovoltaici (se presenti) interessati dagli eventi alluvionali.

## Flusso di lavoro

### Infrastruttura DB
Prima di tutto sto valutando se copiare dati online (magari utili anche per Matteo e Laura) oppure fare "copia" offline per non appesantire inutilmente il server con dati su dati.
1. Per reti e cabine primo controllo per vedere in quale/quali sezioni censuarie ricadono
  1. Per gli elementi lineari saranno probabilmente più sezioni censuarie. Possibilità di prendere la totalità della sezione con relativo numero di abitanti oppure aggregare sotto qualche altro metodo. Questo può funzionare per acquedotto ma non rete elettriche è AT e quindi non direttamente coinvolta nella distribuzione alle comunità.  
