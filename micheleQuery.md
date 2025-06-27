# Test per utilizzo di query POSTGIS

## SQL 

## CAMBIO SCHEMA A UNA TABELLA

``` sql
ALTER TABLE lavoro.punti_innesco_incendi
	SET SCHEMA inputdata;
``` 


### POLIGONO CON POLIGONO --> Distanza minima tra i due 

``` sql
SELECT a.id AS id_cabine,
    a.geom,
    a.fid AS fid_cabine,
    a.uuid AS uuid_cabine,
    b.id AS id_sql_incendio,
    b.id_incendi AS codice_univoco_incendio,
    b.cod_ince AS codice_incendio,
    st_distance(a.geom, b.geom) AS distanza_minima
   FROM lavoro.impianti_energetici a
     JOIN LATERAL ( SELECT b_1.id,
            b_1.geom,
            b_1.id_incendi,
            b_1.cod_ince
           FROM lavoro.aree_percorse_incendi b_1
          ORDER BY (a.geom <-> b_1.geom)
         LIMIT 1) b ON true -- Voglio prendere solo il primo
  ORDER BY (st_distance(a.geom, b.geom));
```
* LATERAL JOIN: sottoquery correlata, cioè per ogni riga di riferimento esegue sottoquery su tabella di confronto (in questo caso tra cabine e aree percorse da fuoco).
* ORDER BY a.geom <-> b.geom: usa l'operatore di distanza spaziale più veloce (basato su bounding boxes con l'indice GiST).
* ST_Distance(a.geom, b.geom): calcola la distanza geometrica esatta tra i due poligoni.

⚠️ Qui ho messo tutto nello stesso SR. Qualora così non fosse devo usare ST_Transform(). Per esempio:
``` sql
ST_Distance(ST_Transform(a.geom, 4326), ST_Transform(b.geom, 32632)) AS distanza_minima
```

### PUNTO CON POLIGONO --> Distanza minima tra i due
Non cambia praticamente nulla con quanto sopra
``` sql
 SELECT a.id AS id_cabine,
    a.geom,
    a.fid AS fid_cabine,
    a.uuid AS uuid_cabine,
    b.id AS id_sql_incendio,
    b.id_incendi AS codice_univoco_incendio,
    b.cod_ince AS codice_incendio,
    st_distance(a.geom, b.geom) AS distanza_minima
   FROM lavoro.impianti_energetici a
     JOIN LATERAL ( SELECT b_1.id,
            b_1.geom,
            b_1.id_incendi,
            b_1.cod_ince
           FROM lavoro.punti_innesco_incendi b_1
          ORDER BY (a.geom <-> b_1.geom)
         LIMIT 1) b ON true
  ORDER BY (st_distance(a.geom, b.geom));
```

### LINEA CON POLIGONO --> Distanza minima tra i 2 + percentuale di lunghezza elemento interessato da evento

``` sql
 SELECT a.fid AS id_sql_elettrodotto,
    a.geom,
    a.gid AS gid_elettrodotto,
    b.id AS id_sql_incendio,
    b.id_incendi AS codice_univoco_incendio,
    b.cod_ince AS codice_incendio,
    st_distance(a.geom, b.geom) AS distanza_minima,
    st_length(COALESCE(b.intersezione, st_geomfromtext('LINESTRING EMPTY'::text, st_srid(a.geom)))) AS lunghezza_sovrapposta,
        CASE
            WHEN st_length(a.geom) = 0::double precision THEN 0::double precision
            ELSE st_length(COALESCE(b.intersezione, st_geomfromtext('LINESTRING EMPTY'::text, st_srid(a.geom)))) / st_length(a.geom) * 100::double precision
        END AS percentuale_sovrapposta
   FROM lavoro.elettrodotti a
     JOIN LATERAL ( SELECT b_1.id,
            b_1.geom,
            b_1.id_incendi,
            b_1.cod_ince,
            st_intersection(a.geom, b_1.geom) AS intersezione
           FROM lavoro.aree_percorse_incendi b_1
          ORDER BY (a.geom <-> b_1.geom)
         LIMIT 1) b ON true;
```
* ST_GeomFromText('LINESTRING EMPTY', ST_SRID(...)) per evitare NULL e fornire una geometria vuota valida (di tipo linea). :: è il parser come text. ST_GeomFromText (*Constructs a PostGIS ST_Geometry object from the OGC Well-Known text representation.*)
* il CASE e il COALESCE mi consentono di andare a ovviare nel caso ci siano problemi. In questo caso, in particolare, nel caso ci siano valori nulli (nessuna sovrapposizione). 

## Intersezione Punto con Poligono esportando informazioni tra i due campi

``` sql
select e.id, e.descr_uso, i.cod_ince  --campi da mantenere
from istat.cens_edifici_ppr e, piemonte.areepercorseincendi i
where e.comuni = 'Mompantero' --filtro per limitare i risultati
and ST_Transform(e.geom,32632) && i.geom  -- && serve per vedere se le bounding box si intersecano.. verifica speditiva
and st_isvalid(e.geom)
and st_isvalid(i.geom)
and ST_Within(ST_Transform(e.geom,32632),i.geom) -- Il within, invece, guarda effettivamente se il punto è all'interno del poligono 
order by e.id;
``` 

| Funzione        | Cosa fa?                                                                 | Tipo di confronto                  | Precisione                                   |
| --------------- | ------------------------------------------------------------------------ | ---------------------------------- | -------------------------------------------- |
| **`&&`**        | Verifica se le **bounding box** delle due geometrie si sovrappongono.    | Sovrapposizione delle bounding box | Controllo rapido, ma non preciso             |
| **`ST_Within`** | Verifica se una geometria è **completamente dentro** un'altra geometria. | Contenimento geometrico            | Più preciso, verifica la geometria effettiva |



## OPENSTREET MAP - OVERPASS TURBO API 

### Ricerca ed estrazione cabine primarie 

In questo caso, con query cabine, lui si prende tutto quanto, anche quelle secondarie. L'export poi è stato fatto in geojson tramite esporta. 

```java
[out:json][timeout:300];

// Decido l'estensione --> in questo caso vado con regione Piemonte 
area["name"="Piemonte"]["boundary"="administrative"]["admin_level"="4"]->.regione;  // Tutte queste sono chiavi che identificano il piemonte da DB OSM

// Trova tutte le way con codice-valore che mi servono nell'area
way['power'='substation'](area.regione);  // Tiro fuori tutto quanto indipendentemente da cosa si tratta
//way['power'='substation']['substation'='distribution'](area.regione); // filtro su CP Terna AT/MT di distribuzione poi
//way['power'='substation']['substation'='traction'](area.regione);    // filtro su CP per ferrovie
//way['power'='substation']['substation'='industrial'](area.regione);    // filtro su CP industriali e.g. acciaierie in cui arriva direttamente AT

out body;

>;

out skel qt;

```

### Estrazione Cavi AT
```java
[out:json][timeout:300];

// Cerca regione Piemonte
area["name"="Piemonte"]["boundary"="administrative"]["admin_level"="4"]->.regione;

// Trova tutte le way con codice-valore che mi servono nell'area
way['power'='line'](area.regione);

out body;

>;

out skel qt;
```


### Estrazione Cavi Minor Line (MT-BT??)
```java
[out:json][timeout:300];

// Cerca regione Piemonte
area["name"="Piemonte"]["boundary"="administrative"]["admin_level"="4"]->.regione;

// Trova tutte le way con codice-valore che mi servono nell'area
way['power'='minor_line'](area.regione);

out body;

>;

out skel qt;
```
### Ricerca ed estrazione impianti trattamento acque reflue

```java
[out:json][timeout:300];

// Cerca regione Piemonte
area["name"="Piemonte"]["boundary"="administrative"]["admin_level"="4"]->.regione;

// Trova tutte le way con codice-valore che mi servono nell'area
way['man_made'='wastewater_plant'](area.regione);

out body;

>;

out skel qt;
```

### Ricerca Power Plant	

```java

[out:json][timeout:300];

// Cerca regione Piemonte
area["name"="Piemonte"]["boundary"="administrative"]["admin_level"="4"]->.regione;

// Trova tutte le way con codice-valore che mi servono nell'area
way['power'='plant'](area.regione);

out body;

>;

out skel qt;
```



### Ricerca Tralicci

```java

[out:json][timeout:300];

// Cerca regione Piemonte
area["name"="Piemonte"]["boundary"="administrative"]["admin_level"="4"]->.regione;

// Trova tutte le way con codice-valore che mi servono nell'area
node['power'='tower'](area.regione);

out body;

>;

out skel qt;
```


## Ricerca dighe

```java
[out:json][timeout:300];

// Cerca regione Piemonte
area["name"="Piemonte"]["boundary"="administrative"]["admin_level"="4"]->.regione;

// Trova tutte le way con codice-valore che mi servono nell'area
way['waterway'='dam'](area.regione);

out body;

>;

out skel qt;
```
## GDAL 
Usando OSGeo4W Shell posso direttamente lanciare comandi di gdal shell. In questo modo ho fatto queste attività piuttosto che utilizzare script più complicati. 

* Cambio working directory
```console
cd C:\Users\.....   -- in cui vado a inserire il mio percorso
```

* Creazione file txt comprendente tutti i nomi dei file tif del DSM/DTM scaricate da portale ministero
```console
dir /b /s *.tif > tiff_list.txt
```
Breakdown del comando:
dir - Comando per listare file e cartelle

/b - Opzione "bare format"
Mostra solo i nomi dei file (senza date, dimensioni, attributi)
Output pulito, un file per riga

/s - Opzione "subdirectories"

Cerca ricorsivamente in tutte le sottocartelle
Non solo nella cartella corrente, ma anche in quelle annidate

*.tif - Pattern di ricerca

Trova tutti i file con estensione .tif
L'asterisco * è un wildcard che significa "qualsiasi nome"

'>' - Operatore di redirezione

Invece di mostrare l'output a schermo, lo scrive in un file

tiff_list.txt - File di destinazione

Crea (o sovrascrive) questo file di testo con la lista

* Merge dei file tiff
```console
gdal_merge -o mosaic_dsm_first.tif --optfile tiff_list.txt
```

Breakdown del comando:
gdal_merge - Utility GDAL per unire/mosaicare raster

Parte del pacchetto GDAL tools
Equivalente Python: gdal_merge.py

-o mosaic_dsm_first.tif - Output file

-o specifica il nome del file di output
mosaic_dsm_first.tif sarà il file risultante
Dal nome sembra essere un DSM (Digital Surface Model)

--optfile tiff_list.txt - Input da file di lista

--optfile legge la lista dei file da un file di testo
Usa il file tiff_list.txt creato dal comando precedente
Alternativa a specificare tutti i file manualmente
