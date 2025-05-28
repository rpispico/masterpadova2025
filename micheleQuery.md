# Test per utilizzo di query POSTGIS

## POLIGONO CON POLIGONO --> Distanza minima tra i due 

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

## PUNTO CON POLIGONO --> Distanza minima tra i due
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

## LINEA CON POLIGONO --> Distanza minima tra i 2 + percentuale di lunghezza elemento interessato da evento

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
