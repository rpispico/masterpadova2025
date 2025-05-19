# Amministrazione Database







## Creazione db

```sql
CREATE DATABASE masterpadova
    WITH
    OWNER = XXXX
    ENCODING = 'UTF8'
    LOCALE_PROVIDER = 'libc'
    CONNECTION LIMIT = -1
    IS_TEMPLATE = False;

COMMENT ON DATABASE masterpadova
    IS 'project work 2025';
```

## Estensioni

```sql
CREATE EXTENSION postgis;
```

## Gruppi di utenti

```sql

```

## Utenti

```sql

```

## Schemi

```sql
CREATE SCHEMA IF NOT EXISTS istat
    AUTHORIZATION XXXX;

COMMENT ON SCHEMA istat
    IS 'dati istat 2021';

ALTER DEFAULT PRIVILEGES FOR ROLE XXXX IN SCHEMA istat
GRANT ALL ON TABLES TO "YYYY";

ALTER DEFAULT PRIVILEGES FOR ROLE XXXX IN SCHEMA istat
GRANT ALL ON SEQUENCES TO "YYYY";

CREATE SCHEMA lavoro
    AUTHORIZATION XXXX;

COMMENT ON SCHEMA lavoro
    IS 'schema di appoggio dati';

```



## Nomi tavola in minuscolo

```sql
-- rinominare tavole in minuscolo 
select 
'ALTER TABLE IF EXISTS "' || table_schema || '"."' || table_name || '" RENAME TO "' 
|| lower(table_name) || '";'
from information_schema.tables
	where table_schema = 'istat'
	and table_schema not in ('pg_catalog','information_schema','public')
	and table_name != lower(table_name)
order by table_schema, table_name
```



## Nomi colonna in minuscolo

```sql
select 

-- ALTER TABLE IF EXISTS istat.asc1_r01_21     RENAME "COD_REG" TO cod_reg;
	
'ALTER TABLE IF EXISTS "' || table_schema || '"."' || table_name || '" RENAME "' 
|| column_name || '" TO "' || lower(column_name) || '";'
from information_schema.columns
	where table_schema = 'istat'
	and table_schema not in ('pg_catalog','information_schema','public')
	and column_name != lower(column_name)
order by table_schema, table_name, column_name

```

## Nomi colonna in integer

```sql
select 
'ALTER TABLE istat.r01_indicatori_2021_sezioni ALTER COLUMN "' || column_name || 
'" TYPE integer USING ("' ||   column_name ||       '"::integer);'
from information_schema.columns
	where table_schema = 'istat'
	and table_schema not in ('pg_catalog','information_schema','public')
	and table_name = 'r01_indicatori_2021_sezioni'
order by table_schema, table_name, column_name
```



## Amministrazione tavole

```sql
select 
	'GRANT USAGE ON SCHEMA  "' || schema_name || '" TO "YYYY";',
	'GRANT ALL ON SCHEMA  "' || schema_name || '" TO "ZZZZ";',
'ALTER SCHEMA   "' || schema_name || '" OWNER TO "YYYY";'
from information_schema.schemata
	where catalog_name = 'masterpadova'
	and schema_name not in ('pg_catalog','information_schema','public')
order by schema_name


-- ownership delle tavole
select 'ALTER TABLE "' || table_schema || '"."' || table_name || '" OWNER TO "YYYY";'
from information_schema.tables
	where table_catalog = 'masterpadova'
	and table_schema not in ('pg_catalog','information_schema','public')
order by table_schema, table_name

-- ownership delle viste
select 'ALTER TABLE "' || table_schema || '"."' || table_name || '" OWNER TO "YYYY";'
from information_schema.views
	where table_catalog = 'masterpadova'
	and table_schema not in ('pg_catalog','information_schema','public')
order by table_schema, table_name

-- select tavole
select 'GRANT SELECT ON TABLE "' || table_schema || '"."' || table_name || '" TO "YYYY";'
from information_schema.tables
	where table_catalog = 'masterpadova'
	and table_schema not in ('pg_catalog','information_schema','public')
order by table_schema, table_name
	
select 'GRANT ALL ON TABLE "' || table_schema || '"."' || table_name || '" TO "ZZZZ";'
from information_schema.tables
	where table_catalog = 'masterpadova'
	and table_schema not in ('pg_catalog','information_schema','public')
order by table_schema, table_name

select 'GRANT ALL ON TABLE "' || table_schema || '"."' || table_name || '" TO "YYYY";'
from information_schema.tables
	where table_catalog = 'masterpadova'
	and table_schema not in ('pg_catalog','information_schema','public')
order by table_schema, table_name

-- usage sequence
select 'GRANT USAGE ON SEQUENCE "' || sequence_schema || '"."' || sequence_name || '" TO "YYYY";' 
from information_schema.sequences
	where sequence_catalog = 'masterpadova'
	and sequence_schema not in ('pg_catalog','information_schema','public')
order by sequence_schema, sequence_name

-- usage sequence
select 'GRANT USAGE ON SEQUENCE "' || sequence_schema || '"."' || sequence_name || '" TO "ZZZZ";' 
from information_schema.sequences
	where sequence_catalog = 'masterpadova'
	and sequence_schema not in ('pg_catalog','information_schema','public')
order by sequence_schema, sequence_name

```

## Commenti tavola censimento

```sql
COMMENT ON COLUMN istat.cens21.COD_REG IS 'Codice che identifica univocamente la regione nell’ambito del territorio nazionale';
--COMMENT ON COLUMN istat.cens21.REGIONE IS 'Denominazione della regione';
--COMMENT ON COLUMN istat.cens21.CODPRO IS 'Codice che identifica univocamente la provincia nell’ambito del territorio nazionale';
COMMENT ON COLUMN istat.cens21.PROVINCIA IS 'Denominazione della provincia';
--COMMENT ON COLUMN istat.cens21.CODCOM IS 'Codice che identifica univocamente il comune nell’ambito del territorio provinciale';
--COMMENT ON COLUMN istat.cens21.COMUNE IS 'Denominazione del comune';
COMMENT ON COLUMN istat.cens21.PRO_COM IS 'Codice che identifica univocamente il comune nell’ambito del territorio nazionale';
COMMENT ON COLUMN istat.cens21.SEZ21_ID IS 'Codice che identifica univocamente la sezione di censimento 2021 nell’ambito del territorio nazionale. ';
COMMENT ON COLUMN istat.cens21.COM_ASC1 IS 'Codice della sub-area amministrativa di primo livello';
COMMENT ON COLUMN istat.cens21.COM_ASC2 IS 'Codice della sub-area amministrativa di secondo livello (ove presente)';
COMMENT ON COLUMN istat.cens21.COM_ASC3 IS 'Codice della sub-area amministrativa di terzo livello  (ove presente)';
--COMMENT ON COLUMN istat.cens21.P1 IS 'Popolazione residente - totale';
COMMENT ON COLUMN istat.cens21.P2 IS 'Popolazione residente - maschi';
COMMENT ON COLUMN istat.cens21.P3 IS 'Popolazione residente - femmine';
--COMMENT ON COLUMN istat.cens21.P14 IS 'Popolazione residente - età < 5 anni';
--COMMENT ON COLUMN istat.cens21.P15 IS 'Popolazione residente - età 5 - 9 anni';
--COMMENT ON COLUMN istat.cens21.P16 IS 'Popolazione residente - età 10 - 14 anni';
--COMMENT ON COLUMN istat.cens21.P17 IS 'Popolazione residente - età 15 - 19 anni';
--COMMENT ON COLUMN istat.cens21.P18 IS 'Popolazione residente - età 20 - 24 anni';
--COMMENT ON COLUMN istat.cens21.P19 IS 'Popolazione residente - età 25 - 29 anni';
--COMMENT ON COLUMN istat.cens21.P20 IS 'Popolazione residente - età 30 - 34 anni';
--COMMENT ON COLUMN istat.cens21.P21 IS 'Popolazione residente - età 35 - 39 anni';
--COMMENT ON COLUMN istat.cens21.P22 IS 'Popolazione residente - età 40 - 44 anni';
--COMMENT ON COLUMN istat.cens21.P23 IS 'Popolazione residente - età 45 - 49 anni';
--COMMENT ON COLUMN istat.cens21.P24 IS 'Popolazione residente - età 50 - 54 anni';
--COMMENT ON COLUMN istat.cens21.P25 IS 'Popolazione residente - età 55 - 59 anni';
--COMMENT ON COLUMN istat.cens21.P26 IS 'Popolazione residente - età 60 - 64 anni';
--COMMENT ON COLUMN istat.cens21.P27 IS 'Popolazione residente - età 65 - 69 anni';
--COMMENT ON COLUMN istat.cens21.P28 IS 'Popolazione residente - età 70 - 74 anni';
--COMMENT ON COLUMN istat.cens21.P29 IS 'Popolazione residente - età > 74 anni';
COMMENT ON COLUMN istat.cens21.P30 IS 'Popolazione residente - maschi - età < 5 anni';
COMMENT ON COLUMN istat.cens21.P31 IS 'Popolazione residente - maschi - età 5 - 9 anni';
COMMENT ON COLUMN istat.cens21.P32 IS 'Popolazione residente - maschi - età 10 - 14 anni';
COMMENT ON COLUMN istat.cens21.P33 IS 'Popolazione residente - maschi - età 15 - 19 anni';
COMMENT ON COLUMN istat.cens21.P34 IS 'Popolazione residente - maschi - età 20 - 24 anni';
COMMENT ON COLUMN istat.cens21.P35 IS 'Popolazione residente - maschi - età 25 - 29 anni';
COMMENT ON COLUMN istat.cens21.P36 IS 'Popolazione residente - maschi - età 30 - 34 anni';
COMMENT ON COLUMN istat.cens21.P37 IS 'Popolazione residente - maschi - età 35 - 39 anni';
COMMENT ON COLUMN istat.cens21.P38 IS 'Popolazione residente - maschi - età 40 - 44 anni';
COMMENT ON COLUMN istat.cens21.P39 IS 'Popolazione residente - maschi - età 45 - 49 anni';
COMMENT ON COLUMN istat.cens21.P40 IS 'Popolazione residente - maschi - età 50 - 54 anni';
COMMENT ON COLUMN istat.cens21.P41 IS 'Popolazione residente - maschi - età 55 - 59 anni';
COMMENT ON COLUMN istat.cens21.P42 IS 'Popolazione residente - maschi - età 60 - 64 anni';
COMMENT ON COLUMN istat.cens21.P43 IS 'Popolazione residente - maschi - età 65 - 69 anni';
COMMENT ON COLUMN istat.cens21.P44 IS 'Popolazione residente - maschi - età 70 - 74 anni';
COMMENT ON COLUMN istat.cens21.P45 IS 'Popolazione residente - maschi - età > 74 anni';
COMMENT ON COLUMN istat.cens21.P67 IS 'Popolazione residente - femmine - età < 5 anni';
COMMENT ON COLUMN istat.cens21.P68 IS 'Popolazione residente - femmine - età 5 - 9 anni';
COMMENT ON COLUMN istat.cens21.P69 IS 'Popolazione residente - femmine - età 10 - 14 anni';
COMMENT ON COLUMN istat.cens21.P70 IS 'Popolazione residente - femmine - età 15 - 19 anni';
COMMENT ON COLUMN istat.cens21.P71 IS 'Popolazione residente - femmine - età 20 - 24 anni';
COMMENT ON COLUMN istat.cens21.P72 IS 'Popolazione residente - femmine - età 25 - 29 anni';
COMMENT ON COLUMN istat.cens21.P73 IS 'Popolazione residente - femmine - età 30 - 34 anni';
COMMENT ON COLUMN istat.cens21.P74 IS 'Popolazione residente - femmine - età 35 - 39 anni';
COMMENT ON COLUMN istat.cens21.P75 IS 'Popolazione residente - femmine - età 40 - 44 anni';
COMMENT ON COLUMN istat.cens21.P76 IS 'Popolazione residente - femmine - età 45 - 49 anni';
COMMENT ON COLUMN istat.cens21.P77 IS 'Popolazione residente - femmine - età 50 - 54 anni';
COMMENT ON COLUMN istat.cens21.P78 IS 'Popolazione residente - femmine - età 55 - 59 anni';
COMMENT ON COLUMN istat.cens21.P79 IS 'Popolazione residente - femmine - età 60 - 64 anni';
COMMENT ON COLUMN istat.cens21.P80 IS 'Popolazione residente - femmine - età 65 - 69 anni';
COMMENT ON COLUMN istat.cens21.P81 IS 'Popolazione residente - femmine - età 70 - 74 anni';
COMMENT ON COLUMN istat.cens21.P82 IS 'Popolazione residente - femmine - età > 74 anni';
COMMENT ON COLUMN istat.cens21.P83 IS 'Popolazione residente - totale di 9 anni e più';
COMMENT ON COLUMN istat.cens21.P84 IS 'Popolazione residente - maschi di 9 anni e più';
COMMENT ON COLUMN istat.cens21.P85 IS 'Popolazione residente - femmine di 9 anni e più';
--COMMENT ON COLUMN istat.cens21.P86 IS 'Popolazione residente - totale di 9 anni e più senza titolo di studio';
--COMMENT ON COLUMN istat.cens21.P87 IS 'Popolazione residente - totale di 9 anni e più con licenza elementare';
--COMMENT ON COLUMN istat.cens21.P88 IS 'Popolazione residente - totale di 9 anni e più con licenza media';
--COMMENT ON COLUMN istat.cens21.P89 IS 'Popolazione residente - totale di 9 anni e più con Diploma di scuola secondaria superiore incluse le Qualifiche professionali';
--COMMENT ON COLUMN istat.cens21.P90 IS 'Popolazione residente - totale di 9 anni e più con titoli terziari di primo o secondo livello (inclusi i dottori di ricerca)';
COMMENT ON COLUMN istat.cens21.P91 IS 'Popolazione residente - maschi di 9 anni e più senza titolo di studio';
COMMENT ON COLUMN istat.cens21.P92 IS 'Popolazione residente - maschi di 9 anni e più con licenza elementare';
COMMENT ON COLUMN istat.cens21.P93 IS 'Popolazione residente - maschi di 9 anni e più con licenza media';
COMMENT ON COLUMN istat.cens21.P94 IS 'Popolazione residente - maschi di 9 anni e più con Diploma di scuola secondaria superiore incluse le Qualifiche professionali';
COMMENT ON COLUMN istat.cens21.P95 IS 'Popolazione residente - maschi di 9 anni e più con titoli terziari di primo o secondo livello (inclusi i dottori di ricerca)';
COMMENT ON COLUMN istat.cens21.P96 IS 'Popolazione residente - femmine di 9 anni e più senza titolo di studio';
COMMENT ON COLUMN istat.cens21.P97 IS 'Popolazione residente - femmine di 9 anni e più con licenza elementare';
COMMENT ON COLUMN istat.cens21.P98 IS 'Popolazione residente - femmine di 9 anni e più con licenza media';
COMMENT ON COLUMN istat.cens21.P99 IS 'Popolazione residente - femmine di 9 anni e più con Diploma di scuola secondaria superiore incluse le Qualifiche professionali';
COMMENT ON COLUMN istat.cens21.P100 IS 'Popolazione residente - femmine di 9 anni e più con titoli terziari di primo o secondo livello (inclusi i dottori di ricerca)';
--COMMENT ON COLUMN istat.cens21.P101 IS 'Popolazione residente - totale occupati di 15-64 anni';
COMMENT ON COLUMN istat.cens21.P102 IS 'Popolazione residente - maschi occupati di 15-64 anni ';
COMMENT ON COLUMN istat.cens21.P103 IS 'Popolazione residente - femmine occupate di 15-64 anni ';
--COMMENT ON COLUMN istat.cens21.IT1 IS 'Popolazione residente - italiani - età 0 - 14 anni';
--COMMENT ON COLUMN istat.cens21.IT2 IS 'Popolazione residente - italiani - età 15 - 64 anni';
--COMMENT ON COLUMN istat.cens21.IT3 IS 'Popolazione residente - italiani - età 65 anni e più ';
COMMENT ON COLUMN istat.cens21.IT4 IS 'Popolazione residente - maschi italiani - età 0 - 14 anni';
COMMENT ON COLUMN istat.cens21.IT5 IS 'Popolazione residente - maschi  italiani - età 15 - 64 anni';
COMMENT ON COLUMN istat.cens21.IT6 IS 'Popolazione residente - maschi  italiani - età 65 anni e più ';
COMMENT ON COLUMN istat.cens21.IT7 IS 'Popolazione residente - femmine italiane - età 0 - 14 anni';
COMMENT ON COLUMN istat.cens21.IT8 IS 'Popolazione residente - femmine italiane - età 15 - 64 anni';
COMMENT ON COLUMN istat.cens21.IT9 IS 'Popolazione residente - femmine italiane -età 65 anni e più ';
--COMMENT ON COLUMN istat.cens21.IT10 IS 'Popolazione residente - occupati italiani di 15-64 anni';
COMMENT ON COLUMN istat.cens21.IT11 IS 'Popolazione residente - occupati italiani maschi di 15-64 anni';
COMMENT ON COLUMN istat.cens21.IT12 IS 'Popolazione residente - occupate italiane femmine di 15-64 anni';
--COMMENT ON COLUMN istat.cens21.ST1 IS 'Stranieri e apolidi residenti in Italia - totale';
COMMENT ON COLUMN istat.cens21.ST2 IS 'Stranieri e apolidi residenti in Italia - maschi';
COMMENT ON COLUMN istat.cens21.ST2_B IS 'Stranieri e apolidi residenti in Italia - femmine';
--COMMENT ON COLUMN istat.cens21.ST3 IS 'Stranieri e apolidi residenti in Italia - età 0 - 29 anni';
--COMMENT ON COLUMN istat.cens21.ST4 IS 'Stranieri e apolidi residenti in Italia - età 30 - 54 anni';
--COMMENT ON COLUMN istat.cens21.ST5 IS 'Stranieri e apolidi residenti in Italia - età 55 anni e più';
--COMMENT ON COLUMN istat.cens21.ST16 IS 'Stranieri residenti in Italia - totale UE';
COMMENT ON COLUMN istat.cens21.ST17 IS 'Stranieri residenti in Italia - maschi UE';
COMMENT ON COLUMN istat.cens21.ST18 IS 'Stranieri residenti in Italia - femmine UE';
--COMMENT ON COLUMN istat.cens21.ST19 IS 'Stranieri residenti in Italia - totale EXTRA UE';
COMMENT ON COLUMN istat.cens21.ST20 IS 'Stranieri residenti in Italia - maschi EXTRA UE';
COMMENT ON COLUMN istat.cens21.ST21 IS 'Stranieri residenti in Italia - femmine EXTRA UE';
--COMMENT ON COLUMN istat.cens21.ST22 IS 'Stranieri e apolidi residenti in Italia - età 0 - 14 anni';
--COMMENT ON COLUMN istat.cens21.ST23 IS 'Stranieri e apolidi residenti in Italia - età 15 - 64 anni';
--COMMENT ON COLUMN istat.cens21.ST24 IS 'Stranieri e apolidi residenti in Italia - età 65 anni e più';
COMMENT ON COLUMN istat.cens21.ST25 IS 'Stranieri e apolidi residenti in Italia - maschi - età 0 - 14 anni';
COMMENT ON COLUMN istat.cens21.ST26 IS 'Stranieri e apolidi residenti in Italia - maschi - età 15 - 64 anni';
COMMENT ON COLUMN istat.cens21.ST27 IS 'Stranieri e apolidi residenti in Italia - maschi - età 65 anni e più';
COMMENT ON COLUMN istat.cens21.ST28 IS 'Stranieri e apolidi residenti in Italia - femmine - età 0 - 14 anni';
COMMENT ON COLUMN istat.cens21.ST29 IS 'Stranieri e apolidi residenti in Italia - femmine - età 15 - 64 anni';
COMMENT ON COLUMN istat.cens21.ST30 IS 'Stranieri e apolidi residenti in Italia - femmine - età 65 anni e più';
--COMMENT ON COLUMN istat.cens21.ST31 IS 'Stranieri e apolidi residenti in Italia - occupati - età 15 - 64 anni';
COMMENT ON COLUMN istat.cens21.ST32 IS 'Stranieri e apolidi residenti in Italia - occupati maschi - età 15 - 64 anni';
COMMENT ON COLUMN istat.cens21.ST33 IS 'Stranieri e apolidi residenti in Italia - occupate femmine - età 15 - 64 anni';
--COMMENT ON COLUMN istat.cens21.PF1 IS 'Famiglie residenti - totale';
--COMMENT ON COLUMN istat.cens21.PF3 IS 'Famiglie residenti - 1 componente';
--COMMENT ON COLUMN istat.cens21.PF4 IS 'Famiglie residenti - 2 componenti';
--COMMENT ON COLUMN istat.cens21.PF5 IS 'Famiglie residenti - 3 componenti';
--COMMENT ON COLUMN istat.cens21.PF6 IS 'Famiglie residenti - 4 componenti';
--COMMENT ON COLUMN istat.cens21.PF7 IS 'Famiglie residenti - 5 componenti';
--COMMENT ON COLUMN istat.cens21.PF8 IS 'Famiglie residenti - 6 e oltre componenti';
COMMENT ON COLUMN istat.cens21.A2 IS 'Abitazioni occupate da almeno una persona residente';
--COMMENT ON COLUMN istat.cens21.A3 IS 'Abitazioni vuote e abitazioni occupate solo da persone non residenti';
--COMMENT ON COLUMN istat.cens21.A8 IS 'Abitazioni totali';
--COMMENT ON COLUMN istat.cens21.E3 IS 'Edifici ad uso residenziale';


COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.CODREG IS 'Codice che identifica univocamente la regione nell’ambito del territorio nazionale';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.REGIONE IS 'Denominazione della regione';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.CODPRO IS 'Codice che identifica univocamente la provincia nell’ambito del territorio nazionale';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.PROVINCIA IS 'Denominazione della provincia';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.CODCOM IS 'Codice che identifica univocamente il comune nell’ambito del territorio provinciale';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.COMUNE IS 'Denominazione del comune';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.PROCOM IS 'Codice che identifica univocamente il comune nell’ambito del territorio nazionale';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.SEZ21_ID IS 'Codice che identifica univocamente la sezione di censimento 2021 nell’ambito del territorio nazionale. ';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.COM_ASC1 IS 'Codice della sub-area amministrativa di primo livello';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.COM_ASC2 IS 'Codice della sub-area amministrativa di secondo livello (ove presente)';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.COM_ASC3 IS 'Codice della sub-area amministrativa di terzo livello  (ove presente)';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P1 IS 'Popolazione residente - totale';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P2 IS 'Popolazione residente - maschi';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P3 IS 'Popolazione residente - femmine';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P14 IS 'Popolazione residente - età < 5 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P15 IS 'Popolazione residente - età 5 - 9 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P16 IS 'Popolazione residente - età 10 - 14 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P17 IS 'Popolazione residente - età 15 - 19 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P18 IS 'Popolazione residente - età 20 - 24 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P19 IS 'Popolazione residente - età 25 - 29 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P20 IS 'Popolazione residente - età 30 - 34 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P21 IS 'Popolazione residente - età 35 - 39 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P22 IS 'Popolazione residente - età 40 - 44 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P23 IS 'Popolazione residente - età 45 - 49 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P24 IS 'Popolazione residente - età 50 - 54 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P25 IS 'Popolazione residente - età 55 - 59 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P26 IS 'Popolazione residente - età 60 - 64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P27 IS 'Popolazione residente - età 65 - 69 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P28 IS 'Popolazione residente - età 70 - 74 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P29 IS 'Popolazione residente - età > 74 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P30 IS 'Popolazione residente - maschi - età < 5 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P31 IS 'Popolazione residente - maschi - età 5 - 9 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P32 IS 'Popolazione residente - maschi - età 10 - 14 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P33 IS 'Popolazione residente - maschi - età 15 - 19 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P34 IS 'Popolazione residente - maschi - età 20 - 24 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P35 IS 'Popolazione residente - maschi - età 25 - 29 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P36 IS 'Popolazione residente - maschi - età 30 - 34 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P37 IS 'Popolazione residente - maschi - età 35 - 39 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P38 IS 'Popolazione residente - maschi - età 40 - 44 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P39 IS 'Popolazione residente - maschi - età 45 - 49 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P40 IS 'Popolazione residente - maschi - età 50 - 54 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P41 IS 'Popolazione residente - maschi - età 55 - 59 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P42 IS 'Popolazione residente - maschi - età 60 - 64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P43 IS 'Popolazione residente - maschi - età 65 - 69 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P44 IS 'Popolazione residente - maschi - età 70 - 74 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P45 IS 'Popolazione residente - maschi - età > 74 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P67 IS 'Popolazione residente - femmine - età < 5 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P68 IS 'Popolazione residente - femmine - età 5 - 9 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P69 IS 'Popolazione residente - femmine - età 10 - 14 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P70 IS 'Popolazione residente - femmine - età 15 - 19 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P71 IS 'Popolazione residente - femmine - età 20 - 24 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P72 IS 'Popolazione residente - femmine - età 25 - 29 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P73 IS 'Popolazione residente - femmine - età 30 - 34 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P74 IS 'Popolazione residente - femmine - età 35 - 39 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P75 IS 'Popolazione residente - femmine - età 40 - 44 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P76 IS 'Popolazione residente - femmine - età 45 - 49 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P77 IS 'Popolazione residente - femmine - età 50 - 54 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P78 IS 'Popolazione residente - femmine - età 55 - 59 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P79 IS 'Popolazione residente - femmine - età 60 - 64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P80 IS 'Popolazione residente - femmine - età 65 - 69 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P81 IS 'Popolazione residente - femmine - età 70 - 74 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P82 IS 'Popolazione residente - femmine - età > 74 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P83 IS 'Popolazione residente - totale di 9 anni e più';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P84 IS 'Popolazione residente - maschi di 9 anni e più';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P85 IS 'Popolazione residente - femmine di 9 anni e più';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P86 IS 'Popolazione residente - totale di 9 anni e più senza titolo di studio';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P87 IS 'Popolazione residente - totale di 9 anni e più con licenza elementare';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P88 IS 'Popolazione residente - totale di 9 anni e più con licenza media';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P89 IS 'Popolazione residente - totale di 9 anni e più con Diploma di scuola secondaria superiore incluse le Qualifiche professionali';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P90 IS 'Popolazione residente - totale di 9 anni e più con titoli terziari di primo o secondo livello (inclusi i dottori di ricerca)';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P91 IS 'Popolazione residente - maschi di 9 anni e più senza titolo di studio';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P92 IS 'Popolazione residente - maschi di 9 anni e più con licenza elementare';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P93 IS 'Popolazione residente - maschi di 9 anni e più con licenza media';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P94 IS 'Popolazione residente - maschi di 9 anni e più con Diploma di scuola secondaria superiore incluse le Qualifiche professionali';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P95 IS 'Popolazione residente - maschi di 9 anni e più con titoli terziari di primo o secondo livello (inclusi i dottori di ricerca)';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P96 IS 'Popolazione residente - femmine di 9 anni e più senza titolo di studio';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P97 IS 'Popolazione residente - femmine di 9 anni e più con licenza elementare';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P98 IS 'Popolazione residente - femmine di 9 anni e più con licenza media';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P99 IS 'Popolazione residente - femmine di 9 anni e più con Diploma di scuola secondaria superiore incluse le Qualifiche professionali';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P100 IS 'Popolazione residente - femmine di 9 anni e più con titoli terziari di primo o secondo livello (inclusi i dottori di ricerca)';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P101 IS 'Popolazione residente - totale occupati di 15-64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P102 IS 'Popolazione residente - maschi occupati di 15-64 anni ';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.P103 IS 'Popolazione residente - femmine occupate di 15-64 anni ';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.IT1 IS 'Popolazione residente - italiani - età 0 - 14 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.IT2 IS 'Popolazione residente - italiani - età 15 - 64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.IT3 IS 'Popolazione residente - italiani - età 65 anni e più ';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.IT4 IS 'Popolazione residente - maschi italiani - età 0 - 14 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.IT5 IS 'Popolazione residente - maschi  italiani - età 15 - 64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.IT6 IS 'Popolazione residente - maschi  italiani - età 65 anni e più ';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.IT7 IS 'Popolazione residente - femmine italiane - età 0 - 14 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.IT8 IS 'Popolazione residente - femmine italiane - età 15 - 64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.IT9 IS 'Popolazione residente - femmine italiane -età 65 anni e più ';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.IT10 IS 'Popolazione residente - occupati italiani di 15-64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.IT11 IS 'Popolazione residente - occupati italiani maschi di 15-64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.IT12 IS 'Popolazione residente - occupate italiane femmine di 15-64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST1 IS 'Stranieri e apolidi residenti in Italia - totale';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST2 IS 'Stranieri e apolidi residenti in Italia - maschi';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST2_B IS 'Stranieri e apolidi residenti in Italia - femmine';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST3 IS 'Stranieri e apolidi residenti in Italia - età 0 - 29 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST4 IS 'Stranieri e apolidi residenti in Italia - età 30 - 54 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST5 IS 'Stranieri e apolidi residenti in Italia - età 55 anni e più';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST16 IS 'Stranieri residenti in Italia - totale UE';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST17 IS 'Stranieri residenti in Italia - maschi UE';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST18 IS 'Stranieri residenti in Italia - femmine UE';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST19 IS 'Stranieri residenti in Italia - totale EXTRA UE';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST20 IS 'Stranieri residenti in Italia - maschi EXTRA UE';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST21 IS 'Stranieri residenti in Italia - femmine EXTRA UE';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST22 IS 'Stranieri e apolidi residenti in Italia - età 0 - 14 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST23 IS 'Stranieri e apolidi residenti in Italia - età 15 - 64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST24 IS 'Stranieri e apolidi residenti in Italia - età 65 anni e più';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST25 IS 'Stranieri e apolidi residenti in Italia - maschi - età 0 - 14 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST26 IS 'Stranieri e apolidi residenti in Italia - maschi - età 15 - 64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST27 IS 'Stranieri e apolidi residenti in Italia - maschi - età 65 anni e più';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST28 IS 'Stranieri e apolidi residenti in Italia - femmine - età 0 - 14 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST29 IS 'Stranieri e apolidi residenti in Italia - femmine - età 15 - 64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST30 IS 'Stranieri e apolidi residenti in Italia - femmine - età 65 anni e più';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST31 IS 'Stranieri e apolidi residenti in Italia - occupati - età 15 - 64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST32 IS 'Stranieri e apolidi residenti in Italia - occupati maschi - età 15 - 64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.ST33 IS 'Stranieri e apolidi residenti in Italia - occupate femmine - età 15 - 64 anni';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.PF1 IS 'Famiglie residenti - totale';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.PF3 IS 'Famiglie residenti - 1 componente';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.PF4 IS 'Famiglie residenti - 2 componenti';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.PF5 IS 'Famiglie residenti - 3 componenti';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.PF6 IS 'Famiglie residenti - 4 componenti';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.PF7 IS 'Famiglie residenti - 5 componenti';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.PF8 IS 'Famiglie residenti - 6 e oltre componenti';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.A2 IS 'Abitazioni occupate da almeno una persona residente';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.A3 IS 'Abitazioni vuote e abitazioni occupate solo da persone non residenti';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.A8 IS 'Abitazioni totali';
COMMENT ON COLUMN istat.r01_indicatori_2021_sezioni.E3 IS 'Edifici ad uso residenziale';

```
