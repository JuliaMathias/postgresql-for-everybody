# Generating Text

In this assignment you will create a table named bigtext with a single TEXT column named content. Insert 100000 records with numbers starting at 100000 and going through 199999 into the table as shown below:

```bash
This is record number 100000 of quite a few text records.
This is record number 100001 of quite a few text records.
...
This is record number 199998 of quite a few text records.
This is record number 199999 of quite a few text records.
```

## Solution

```SQL
CREATE TABLE bigtext (content TEXT);

```

```SQL
INSERT INTO bigtext (content)
SELECT
  'This is record number' || generate_series(100000, 199999) || 'of quite a few text records.';
```

```SQL

```
