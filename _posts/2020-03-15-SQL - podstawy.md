---
layout: post
title: "SQL: Podstawy"
categories: SQL
author: "Michał"
---
Wszystkie przykłady opisane na podstawie SQLite

Główny podział poleceń w SQL:

- SELECT
- INSERT
- UPDATE
- DELETE
- funkcje agregujące
- JOIN
- Subqueries



## Tworzenie nowej tabeli

```sql
"""CREATE TABLE "{table_name}" (
	"offer_id"	INTEGER NOT NULL,
	"city"	TEXT,
	"region"	TEXT,
	"model"	TEXT,
	"year"	INTEGER,
	"mileage"	INTEGER,
	"fuel_type"	TEXT,
	"displacement"	INTEGER,
	"price"	INTEGER,
	"currency"	TEXT,
	"pub_date"	TEXT,
	"duration"	INTEGER,
	"end_price"	INTEGER
    );""".format(table_name=table_name)
```

### Kopiowanie danych do kolejnej tabeli
```sql
INSERT INTO new_table (offer_id,city,region,model)
SELECT offer_id,city,region,model FROM otomoto_20200101
UNION
SELECT offer_id,city,region,model FROM otomoto_20200102
```

### Kopiowanie zawartości kolumny

Z jednej tabeli do drugiej jeżeli Id jest takie samo

```sql
UPDATE mtable 
SET pub_date = (
SELECT pub_date FROM otomoto_20200102
WHERE (mtable.offer_id) = (otomoto_20200102.offer_id))
WHERE pub_date is null AND ((offer_id) IN (SELECT offer_id FROM otomoto_20200102));
```

## Zapisywanie długich zapytań 
W pythonie powinno się to robić od trzech apostrofów, cudzysłowów, można wtedy dowolnie łamać tekst wewnątrz.
```sq
create_users = """
    INSERT INTO
    cars (offer_id,city,region,model,year,mileage,fuel_type,displacement,price,currency,pub_date,duration,end_price)
    VALUES
    (6069449316,'Prudnik','Opolskie','Toyota Yaris II',2009,153000,'Diesel',-1,12999,'PLN','2019-12-31',7,12999),
    (6068202189,'Włocławek','Kujawsko-pomorskie','Toyota Yaris II',2008,110000,'Benzyna',1298,17600,'PLN','2019-12-31',21,16900),
    (6067206317,'Łódź','Łódzkie','Toyota Yaris II',2010,167938,'Diesel',1364,13999,'PLN','2019-12-31',31,13900),
    (6069421596,'Katowice','Śląskie','Toyota Yaris II',2008,214548,'Benzyna+LPG',1298,12000,'PLN','2019-12-31',31,12000),
    (6068568066,'Katowice','Śląskie','Toyota Yaris II',2007,38000,'Benzyna',1298,19300,'PLN','2019-12-31',12,18500);
    """
```