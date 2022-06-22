# GIN ts_vector Index

In this assignment, you will create a table of documents and then produce a GIN-based **ts_vector** index on the documents.

The goal of this assignment is to run these queries:

```SQL
SELECT id, doc FROM docs03 WHERE to_tsquery('english', 'programmers') @@ to_tsvector('english', doc);
EXPLAIN SELECT id, doc FROM docs03 WHERE to_tsquery('english', 'programmers') @@ to_tsvector('english', doc);
```

and (a) get the correct document(s) and (b) use the GIN index (i.e. not use a sequential scan).

```SQL
CREATE TABLE docs03 (id SERIAL, doc TEXT, PRIMARY KEY(id));

CREATE INDEX fulltext03 ON docs03 USING gin(to_tsvector('english', doc));
```

If you already have the **docs03** filled with the correct rows, you can just add the new index to the table.

Here are the one-line documents that you are to insert into **docs03**:

```SQL
INSERT INTO docs03 (doc) VALUES
('C PHP Ruby Basic Perl JavaScript and many more The actual'),
('hardware inside the Central Processing Unit CPU does not understand'),
('any of these highlevel languages'),
('The CPU understands a language we call machine'),
('language Machine language is very simple and frankly very'),
('tiresome to write because it is represented all in zeros and ones'),
('Machine language seems quite simple on the surface given that there are'),
('only zeros and ones but its syntax is even more complex and far more'),
('intricate than Python So very few programmers ever write machine'),
('language Instead we build various translators to allow programmers to');
```

You should also insert a number of filler rows into the table to make sure PostgreSQL uses its index:

```SQL
INSERT INTO docs03 (doc) SELECT 'Neon ' || generate_series(10000,20000);
```

It might take from a few seconds to a minute or two before PostgreSQL catches up with its indexing. The autograder won't work if the index is incomplete. If the **EXPLAIN** command says that it is using **Seq Scan** - the index has not completed yet. Run the above **EXPLAIN** multiple times if necessary until you verify that PostgreSQL has finished making the index:

```SQL
pg4e=> EXPLAIN SELECT id, doc FROM docs03 WHERE to_tsquery('english', 'instructions') @@ to_tsvector('english', doc);
                                       QUERY PLAN
-----------------------------------------------------------------------------------------
 Bitmap Heap Scan on docs03  (cost=208.27..268.71 rows=35 width=36)
   Recheck Cond: ('''instruct'''::tsquery @@ to_tsvector('english'::regconfig, doc))
   ->  Bitmap Index Scan on fulltext03  (cost=0.00..208.26 rows=35 width=0)
         Index Cond: ('''instruct'''::tsquery @@ to_tsvector('english'::regconfig, doc))
(4 rows)

pg4e=>
```

## References

Here are some links on monitoring the building of indexes:

- [PostgreSQL Wiki: Index Maintenance](https://wiki.postgresql.org/wiki/Index_Maintenance)
- [Stackoverflow: Monitoring Progress of Index Construction in PostgreSQL](https://dba.stackexchange.com/a/161992/206399)
