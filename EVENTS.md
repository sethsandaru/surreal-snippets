# EVENT

EVENT in SurrealDB is pretty cool. After a write action eg CREATE, UPDATE, DELETE, we can add an event and do
something

## Example, Record Log

```sql
DEFINE EVENT write_user_log_event ON TABLE user WHEN $event = 'UPDATE' THEN (
	CREATE event 
    SET
        user = $value.id, 
        time = time::now(), 
        before = $before
        after = $after, 
        action = 'user_updated'
);
```

You can also use `$before` & `$after`. Note for `CREATE`, `$before` is NONE

```sql
DEFINE EVENT write_email_log ON TABLE user WHEN $before.email != $after.email THEN (
	CREATE event 
    SET
        user = $value.id, 
        time = time::now(), 
        before_email = $before.email,
        after_email = $after.email, 
        action = 'user_email_updated'
);
```

## Variables

- `$event`: can be `CREATE`, `UPDATE`, `DELETE`
- `$before`: the previous record
- `$after`: the updated record