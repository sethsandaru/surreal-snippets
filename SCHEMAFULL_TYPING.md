# SCHEMAFULL Notes

Tables that defined in `SCHEMAFULL` won't be able to have dynamic columns/values.

Everything is strictly defined (just like traditional DBMS SQL). 
You must `DEFINE FIELD` in order to put data into the field.

## Object Typing

You must define the properties in order to store them.

```sql
DEFINE FIELD object_field ON TABLE user TYPE object;

// fixed body
DEFINE FIELD object_field.name ON TABLE user TYPE string;
DEFINE FIELD object_field.price ON TABLE user TYPE decimal;
DEFINE FIELD object_field.currency_code ON TABLE user TYPE string;

// accepts any body
DEFINE FIELD properties ON TABLE user FLEXIBLE TYPE object;
```

## Array Typing

```sql
-- array of string or int
DEFINE FIELD tags TYPE array;
DEFINE FIELD tags.* TYPE string;

DEFINE FIELD ages TYPE array;
DEFINE FIELD ages.* TYPE int;
    
-- array of objects
DEFINE FIELD addresses TYPE array;
DEFINE FIELD addresses.*.address TYPE string;
DEFINE FIELD addresses.*.phone TYPE string;
```