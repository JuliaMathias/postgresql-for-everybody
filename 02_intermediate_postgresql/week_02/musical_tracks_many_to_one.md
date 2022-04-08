# Musical Tracks Many-to-One

This application will read an iTunes library in comma-separated-values (CSV) format and produce properly normalized tables as specified below. Once you have placed the proper data in the tables, press the button below to check your answer.

Here is the structure of the tables you will need for this assignment:

```SQL
CREATE TABLE album (
  id SERIAL,
  title VARCHAR(128) UNIQUE,
  PRIMARY KEY(id)
);
```

```SQL
CREATE TABLE track (
    id SERIAL,
    title VARCHAR(128),
    len INTEGER, rating INTEGER, count INTEGER,
    album_id INTEGER REFERENCES album(id) ON DELETE CASCADE,
    UNIQUE(title, album_id),
    PRIMARY KEY(id)
);
```

```SQL
DROP TABLE IF EXISTS track_raw;
```

```SQL
CREATE TABLE track_raw
 (title TEXT, artist TEXT, album TEXT, album_id INTEGER,
  count INTEGER, rating INTEGER, len INTEGER);
```

We will ignore the artist field for this assignment and focus on the many-to-one relationship between tracks and albums.
If you run the program multiple times in testing or with different files, make sure to empty out the data before each run.

Load this CSV data file into the track_raw table using the \copy command. Then write SQL commands to insert all of the distinct albums into the album table (creating their primary keys) and then set the album_id in the track_raw table using an SQL query like:

```SQL
UPDATE track_raw SET album_id = (SELECT album.id FROM album WHERE album.title = track_raw.album);
```

Then use a INSERT ... SELECT statement to copy the corresponding data from the track_raw table to the track table, effectively dropping the artist and album text fields.

To grade this assignment, the auto-grader will run a query like this on your database and look for the data it expects to see:

```SQL
SELECT track.title, album.title
    FROM track
    JOIN album ON track.album_id = album.id
    ORDER BY track.title LIMIT 3;
```

The expected result of this query on your database is:

| track                      | album                              |
| -------------------------- | ---------------------------------- |
| A Boy Named Sue (live)     | The Legend Of Johnny Cash          |
| A Brief History of Packets | Computing Conversations            |
| Águas De Março             | Natural Wonders Music Sampler 1999 |

## Solution

```bash
\copy track_raw(title, artist, album, count, rating, len) FROM 'library.csv' DELIMITER ',' CSV;
```

```SQL
INSERT INTO album (title) SELECT DISTINCT album FROM track_raw;
```

```SQL
UPDATE track_raw SET album_id = (SELECT album.id FROM album WHERE album.title = track_raw.album);
```

```SQL
INSERT INTO track (title, album_id, count, rating, len)
SELECT title, album_id, count, rating, len
FROM track_raw;
```
