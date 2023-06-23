# PERMISSIONS

SurrealDB has the built-in PERMISSIONS check. Available for scoped user (need auth)

## Notes
- If you don't specify the action (select, create,...), by default it is allowed.
- `NONE` means forbid the action
- We can use relationship-access to check, do any check. 
  - But aware of the performance. Especially multi-level graph-access.

## Basic

```sql
DEFINE TABLE article SCHEMAFULL
  PERMISSIONS
    FOR delete NONE, -- can't delete
    FOR select, create, update WHERE $auth.id = user; -- can view, create and update our own articles

DEFINE FIELD user ON TABLE article TYPE record(article);
```

## Permission On Fields

Available as well

```sql
DEFINE FIELD deleted_at ON TABLE company_note
  TYPE datetime
  VALUE time::now()
  PERMISSIONS
    FOR update, delete NONE; -- can't update or delete this field
```

## Need to log in to view the data

```sql
DEFINE TABLE tags SCHEMAFULL
  PERMISSIONS
    FOR select $auth.id != NONE;
```