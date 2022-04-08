# Unesco Heritage Sites Many-to-One

In this assignment you will read some Unesco Heritage Site data in comma-separated-values (CSV) format and produce properly normalized tables as specified below.

Here is the structure of the tables you will need for this assignment:

```SQL
DROP TABLE unesco_raw;
CREATE TABLE unesco_raw
 (name TEXT, description TEXT, justification TEXT, year INTEGER,
    longitude FLOAT, latitude FLOAT, area_hectares FLOAT,
    category TEXT, category_id INTEGER, state TEXT, state_id INTEGER,
    region TEXT, region_id INTEGER, iso TEXT, iso_id INTEGER);
```

```SQL
CREATE TABLE category (
  id SERIAL,
  name VARCHAR(128) UNIQUE,
  PRIMARY KEY(id)
);
```

To load the [CSV data for this assignment](https://www.pg4e.com/tools/sql/whc-sites-2018-small.csv?PHPSESSID=cd4eab8c59ab80d96e8a3f7754171f01%22) use the following *copy* command. Adding *HEADER* causes the CSV loader to skip the first line in the CSV file. The *\copy* command must be one long line.

```bash
\copy unesco_raw(name,description,justification,year,longitude,latitude,area_hectares,category,state,region,iso) FROM 'whc-sites-2018-small.csv' WITH DELIMITER ',' CSV HEADER;
```

Normalize the data in the *unesco_raw* table by adding the entries to each of the lookup tables (category, etc.) and then adding the foreign key columns to the *unesco_raw* table. Then make a new table called *unesco* that removes all of the un-normalized redundant text columns like *category*.

If you run the program multiple times in testing or with different files, make sure to empty out the data before each run.

The autograder will look at the *unesco* table.

To grade this assignment, the program will run a query like this on your database and look for the data it expects to see:

```SQL
SELECT unesco.name, year, category.name, state.name, region.name, iso.name
  FROM unesco
  JOIN category ON unesco.category_id = category.id
  JOIN iso ON unesco.iso_id = iso.id
  JOIN state ON unesco.state_id = state.id
  JOIN region ON unesco.region_id = region.id
  ORDER BY region.name, unesco.name
  LIMIT 3;
```

The expected result of this query on your database is:

| Name                           | Year | Category | State        | Region | iso |
| ------------------------------ | ---- | -------- | ------------ | ------ | --- |
| Khomani Cultural Landscape     | 2017 | Cultural | South Africa | Africa | za  |
| Aapravasi Ghat                 | 2006 | Cultural | Mauritius    | Africa | mu  |
| Air and T n r Natural Reserves | 1991 | Natural  | Niger        | Africa | ne  |

## Solution

```SQL
DROP TABLE unesco_raw;

CREATE TABLE unesco_raw(
  name TEXT,
  description TEXT,
  justification TEXT,
  year INTEGER,
  longitude FLOAT,
  latitude FLOAT,
  area_hectares FLOAT,
  category TEXT,
  category_id INTEGER,
  state TEXT,
  state_id INTEGER,
  region TEXT,
  region_id INTEGER,
  iso TEXT,
  iso_id INTEGER
);

CREATE TABLE unesco (
  id SERIAL,
  name TEXT,
  description TEXT,
  justification TEXT,
  year INTEGER,
  longitude FLOAT,
  latitude FLOAT,
  area_hectares FLOAT,
  category_id INTEGER,
  state_id INTEGER,
  region_id INTEGER,
  iso_id INTEGER,
  PRIMARY KEY(id)
);

CREATE TABLE category (
  id SERIAL,
  name VARCHAR(128) UNIQUE,
  PRIMARY KEY(id)
);

CREATE TABLE state (
  id SERIAL,
  name VARCHAR(128) UNIQUE,
  PRIMARY KEY(id)
);

CREATE TABLE region (
  id SERIAL,
  name VARCHAR(128) UNIQUE,
  PRIMARY KEY(id)
);

CREATE TABLE iso (
  id SERIAL,
  name VARCHAR(128) UNIQUE,
  PRIMARY KEY(id)
);
```

```bash
\copy unesco_raw(name,description,justification,year,longitude,latitude,area_hectares,category,state,region,iso) FROM 'whc-sites-2018-small.csv' WITH DELIMITER ',' CSV HEADER;
```

```SQL
INSERT INTO category (name) SELECT DISTINCT category FROM unesco_raw;
```

```SQL
INSERT INTO state (name) SELECT DISTINCT state FROM unesco_raw;
```

```SQL
INSERT INTO region (name) SELECT DISTINCT region FROM unesco_raw;
```

```SQL
INSERT INTO iso (name) SELECT DISTINCT iso FROM unesco_raw;
```

```SQL
UPDATE unesco_raw SET category_id = (SELECT category.id FROM category WHERE category.name = unesco_raw.category);
```

```SQL
UPDATE unesco_raw SET state_id = (SELECT state.id FROM state WHERE state.name = unesco_raw.state);
```

```SQL
UPDATE unesco_raw SET region_id = (SELECT region.id FROM region WHERE region.name = unesco_raw.region);
```

```SQL
UPDATE unesco_raw SET iso_id = (SELECT iso.id FROM iso WHERE iso.name = unesco_raw.iso);
```

```SQL
INSERT INTO unesco (name, description, justification, year, longitude, latitude, area_hectares, category_id, state_id, region_id, iso_id)
SELECT name, description, justification, year, longitude, latitude, area_hectares, category_id, state_id, region_id, iso_id
FROM unesco_raw;
```
