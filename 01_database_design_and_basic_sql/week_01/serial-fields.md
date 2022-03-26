# SERIAL fields / Auto increment

In this assignment you will create a table, and the autograder will insert a few rows to test your schema.

Create a table named automagic with the following fields:

An id field that is an auto incrementing serial field.
A name field that allows up to 32 characters but no more This field is required. (PostgreSQL Constraints)
A height field that is a floating point number that is required.

```SQL
CREATE TABLE automagic (
  id serial,
  name char(32) NOT NULL,
  height float NOT NULL,
  primary key(id)
);
```
