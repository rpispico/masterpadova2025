# Statistiche

## Layer di appoggio

```sql
CREATE TABLE IF NOT EXISTS lavoro.ante_evento
(
    even_id serial NOT NULL ,
    pid integer,
    geom geometry(MultiPolygon,32632),
    tipo_diss character varying(100) COLLATE pg_catalog."default",
    leg_reg character varying(5) COLLATE pg_catalog."default",
    istat character varying(6) COLLATE pg_catalog."default",
    pubblica character(1) COLLATE pg_catalog."default" DEFAULT 'N'::bpchar,
    even_tipo_id integer,
    even_data timestamp without time zone,
    even_codice character varying(50) COLLATE pg_catalog."default",
    even_nome character varying(200) COLLATE pg_catalog."default",
    even_note character varying(2000) COLLATE pg_catalog."default",
    autorecrea character varying(100) COLLATE pg_catalog."default" DEFAULT "current_user"(),
    datacrea date DEFAULT now(),
    datamodifica timestamp without time zone DEFAULT now(),
    autoremodifica character varying(100) COLLATE pg_catalog."default" DEFAULT "current_user"(),
    even_popolazione integer,
    even_valore_economico_omi bigint,
    CONSTRAINT ante_evento_pk_id PRIMARY KEY (even_id)
)

TABLESPACE pg_default;

ALTER TABLE IF EXISTS lavoro.ante_evento
    OWNER to "PW2025_ADMIN";

REVOKE ALL ON TABLE lavoro.ante_evento FROM "PW2025_R";

GRANT SELECT ON TABLE lavoro.ante_evento TO "PW2025_R";

GRANT ALL ON TABLE lavoro.ante_evento TO "PW2025_RW";

COMMENT ON TABLE lavoro.ante_evento
    IS 'strato informativo geometrico delle aree da indagare';

COMMENT ON COLUMN lavoro.ante_evento.even_tipo_id
    IS 'tipo evento: terremoto, temporale, evento naturale, evento antropico....
';

COMMENT ON COLUMN lavoro.ante_evento.even_note
    IS 'descrizione estesa dell''evento';
-- Index: ante_evento_geom

-- DROP INDEX IF EXISTS anterlive2023.ante_evento_geom;

CREATE INDEX IF NOT EXISTS ante_evento_geom
    ON lavoro.ante_evento USING gist
    (geom)
    TABLESPACE pg_default;
```



## Poligoni con poligoni

```sql
with pop as 
(
select g.sez21_id, g.p1 numabit_sez, 
g.p1 * (st_area(st_makevalid(st_intersection(e.geom,g.geom)))/st_area(g.geom) ) numabit_prop,
st_area(st_makevalid(st_intersection(e.geom,g.geom))) int_area,
st_area(g.geom) as tot_area
from lavoro.ante_evento e, istat.v_sezcens2021 g
where e.even_id = 1
and g.p1 > 0
and e.geom && g.geom
and	st_isvalid(e.geom)
and st_isvalid(g.geom)
and st_intersects(e.geom,g.geom)
)
select pop.sez21_id, sum(numabit_prop)::integer 
from pop 
group by sez21_id
order by sez21_id

with pop as 
(
select e.even_id, g.sez21_id, g.p1 numabit_sez, 
g.p1 * (st_area(st_makevalid(st_intersection(e.geom,g.geom)))/st_area(g.geom) ) numabit_prop,
st_area(st_makevalid(st_intersection(e.geom,g.geom))) int_area
from lavoro.ante_evento e, istat.v_sezcens2021 g
where e.even_id = 1
and g.p1 > 0
and e.geom && g.geom
and	st_isvalid(e.geom)
and st_isvalid(g.geom)
and st_intersects(e.geom,g.geom)
)
select pop.even_id, sum(numabit_prop)::integer 
from pop 
group by even_id
```

## Poligoni e linee

```sql
with strade as 
(
select g.fid, el_str_cf,
st_length(st_makevalid(st_intersection(e.geom,g.geom))) int_lun,
st_length(g.geom) as tot_lun
from lavoro.ante_evento e, piemonte.v_el_str_tp_str g
where e.even_id = 1
and e.geom && g.geom
and	st_isvalid(e.geom)
and st_isvalid(g.geom)
and st_intersects(e.geom,g.geom)
)
select strade.el_str_cf, sum(int_lun)/1000::integer as lun_km
from strade 
group by el_str_cf
order by el_str_cf
```
