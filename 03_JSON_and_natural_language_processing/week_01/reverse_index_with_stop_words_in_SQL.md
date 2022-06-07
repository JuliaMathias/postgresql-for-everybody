# Reverse Index (with stop words) in SQL

In this assignment, you will create a table of documents and then produce a reverse index for those documents that identifies each document which contains a particular word using SQL.

FYI: In contrast with the provided sample SQL, you will map all the words in the reverse index to lower case (i.e. Python, PYTHON, and python should all end up as "python" in the inverted index).

```SQL
CREATE TABLE docs02 (id SERIAL, doc TEXT, PRIMARY KEY(id));

CREATE TABLE invert02 (
  keyword TEXT,
  doc_id INTEGER REFERENCES docs02(id) ON DELETE CASCADE
);
```

Here are the one-line documents that you are to insert into docs01:

```SQL
INSERT INTO docs02 (doc) VALUES
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

Here are your stop words:

```SQL
CREATE TABLE stop_words (word TEXT unique);

INSERT INTO stop_words (word) VALUES
('i'), ('a'), ('about'), ('an'), ('are'), ('as'), ('at'), ('be'),
('by'), ('com'), ('for'), ('from'), ('how'), ('in'), ('is'), ('it'), ('of'),
('on'), ('or'), ('that'), ('the'), ('this'), ('to'), ('was'), ('what'),
('when'), ('where'), ('who'), ('will'), ('with');
```

Here is a sample for the first few expected rows of your reverse index:

```SQL
SELECT keyword, doc_id FROM invert02 ORDER BY keyword, doc_id LIMIT 10;

keyword    |  doc_id
-----------+--------
actual     |    1
all        |    6
allow      |    10
and        |    1
and        |    5
and        |    6
and        |    8
any        |    3
basic      |    1
because    |    6
```

## To-do

```SQL
INSERT INTO invert02 (doc_id, keyword)
SELECT DISTINCT id, s.keyword AS keyword
FROM docs02 AS D, unnest(string_to_array(lower(D.doc), ' ')) s(keyword)
WHERE s.keyword NOT IN (SELECT word FROM stop_words)
ORDER BY id;
```
