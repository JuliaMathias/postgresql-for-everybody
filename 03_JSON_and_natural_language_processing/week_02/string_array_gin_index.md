# String Array GIN Index

In this assignment, you will create a table of documents and then produce a GIN-based text[] reverse index for those documents that identifies each document which contains a particular word using SQL.

FYI: In _contrast_ with the provided sample SQL, you will map all the words in the GIN index to _lower_ case (i.e. Python, PYTHON, and python should all end up as "python" in the GIN index).

The goal of this assignment is to run these queries:

```SQL
SELECT id, doc FROM docs03 WHERE '{programmers}' <@ string_to_array(lower(doc), ' ');
EXPLAIN SELECT id, doc FROM docs03 WHERE '{programmers}' <@ string_to_array(lower(doc), ' ');
```

and (a) get the correct document(s) and (b) use the GIN index (i.e. not use a sequential scan).

```SQL
CREATE TABLE docs03 (id SERIAL, doc TEXT, PRIMARY KEY(id));

CREATE INDEX array03 ON docs03 USING gin(string_to_array(lower(doc), ' ')  array_ops);
```

If you get an **operator class** error on your **CREATE INDEX** check the instructions below for the solution.

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
pg4e=> EXPLAIN SELECT id, doc FROM docs03 WHERE '{programmers}' <@ string_to_array(lower(doc), ' ');
                                   QUERY PLAN
--------------------------------------------------------------------------------
 Seq Scan on docs03  (cost=0.00..177.24 rows=35 width=36)
 Filter: ('{programmers}'::text[] <@ string_to_array(lower(doc), ' '::text))
(2 rows)


TIME PASSES......


pg4e=> EXPLAIN SELECT id, doc FROM docs03 WHERE '{programmers}' <@ string_to_array(lower(doc), ' ');
                                        QUERY PLAN
------------------------------------------------------------------------------------------
 Bitmap Heap Scan on docs03  (cost=12.02..21.97 rows=3 width=15)
 Recheck Cond: ('{programmers}'::text[] <@ string_to_array(lower(doc), ' '::text))
   ->  Bitmap Index Scan on array03  (cost=0.00..12.02 rows=3 width=0)
   Index Cond: ('{programmers}'::text[] <@ string_to_array(lower(doc), ' '::text))
(4 rows)

pg4e=>
```
