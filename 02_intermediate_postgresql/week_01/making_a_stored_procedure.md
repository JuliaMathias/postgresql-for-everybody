# Making a Stored Procedure

In this assignment you will create a table, and add a stored procedure to it.

Create this table:

```SQL
CREATE TABLE keyvalue (
  id SERIAL,
  key VARCHAR(128) UNIQUE,
  value VARCHAR(128) UNIQUE,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  PRIMARY KEY(id)
);
```

Add a stored procedure so that every time a record is updated, the updated_at variable is automatically set to the current time. This was covered in the lecture materials. The auto grader will insert some records, then update them and check to see if updated_at is updated appropriately.

## Create function

```SQL
CREATE OR REPLACE FUNCTION trigger_set_timestamp()
  RETURNS TRIGGER AS $$
  BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
  END;
  $$ language 'plpgsql';
```

## Create trigger

```SQL
CREATE TRIGGER trigger_set_timestamp
  BEFORE UPDATE ON keyvalue
  FOR EACH ROW
  EXECUTE PROCEDURE trigger_set_timestamp();
```
