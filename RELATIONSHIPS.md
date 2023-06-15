# RELATIONSHIPS

## One To One via Record Link

```sql
-- Schema

DEFINE TABLE article SCHEMAFULL;
DEFINE FIELD user ON TABLE article TYPE record(article);
    
-- Query
SELECT *
FROM article
FETCH user, user.accounts; -- get the related record
```

## Graph

Graph is powerful, but we have to make the connection after the record creation.

### One-to-One

```sql
CREATE user:seth CONTENT ...;
CREATE user_info:seth CONTENT ...;
    
RELATE user:seth->info->user_info:seth
    SET time.connected = time::now(); -- pivot value
    
SELECT
    *,
    ->info->user_info.*
FROM user:seth;
```

### One-to-Many or Many-to-Many

```sql
CREATE user:seth CONTENT ...;
CREATE article:my_first_article CONTENT ...;
CREATE article:my_second_article CONTENT ...;
    
RELATE user:seth->articles->article:my_first_article SET time.connected = time::now();
RELATE user:seth->articles->article:my_second_article SET time.connected = time::now();

SELECT
    *,
    ->articles->article.* // array of article
FROM user:seth;
```

Notes:

- You have to run `RELATE` immediately after creating the record to bind the edges.

Trick: use a simple created EVENT like this

```sql
DEFINE EVENT create_edge_on_abcyxz ON article WHEN $event = 'CREATE' THEN (
  RELATE ($value.user)->articles->($value.id) CONTENT {
    user: $value.user,
    connected_at: time::now()
  }
);
```