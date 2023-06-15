# Scope Usage (Sign In & Sign Up)

Doc: https://surrealdb.com/docs/surrealql/statements/define

We can define the scope for sign up and sign in feature of SurrealDB.

After sign in/up, SurrealDB will return a JWT token. We can use that token to query the database (in limited permissions of course).

## Define Scope

```sql
DEFINE SCOPE account SESSION 24h
	SIGNUP ( 
        CREATE user 
        SET email = $email, 
        pass = crypto::argon2::generate($pass)
     )
    SIGNIN ( 
        SELECT * FROM user 
        WHERE email = $email AND 
        crypto::argon2::compare(pass, $pass)
    );
```

Session TTL can be:

- 5m - minute
- 6h - hour
- 1d - day
- 1w - week

TTL can be various depend on your application types. For example, banking TTL ~30m

## Sign Up (Code)

```js
const response = await dbClient.signup({
    NS: '<your namespace>',
    DB: '<your dbname>',
    SC: 'account', // scope
    email: 'admin@sethphat.com',
    pass: 'admin',
});

// if success, we'll have the JWT token in `response`
```

## Sign In (Code)

```js
const response = await dbClient.signin({
    NS: '<your namespace>',
    DB: '<your dbname>',
    SC: 'account', // scope
    email: 'admin@sethphat.com',
    pass: 'admin',
});

// if success, we'll have the JWT token in `response`
```

## Login using both Username & Email
Need to add the `$user` var, change the signup & signin a bit:

```sql
SIGNUP ( 
    CREATE user 
    SET email = $email, 
    user = $user, 
    pass = crypto::argon2::generate($pass)
) 
SIGNIN (
    SELECT * 
    FROM user
    WHERE 
        (email = $user OR username = $user) AND
        crypto::argon2::compare(pass, $pass)
)
```

```js
await dbClient.signup({
    NS: '<your namespace>',
    DB: '<your dbname>',
    SC: 'account', // scope
    email: 'admin@sethphat.com',
    user: 'admin',
    pass: 'admin',
});

await dbClient.signin({
    NS: '<your namespace>',
    DB: '<your dbname>',
    SC: 'account', // scope
    user: 'admin', // can be email or username
    pass: 'admin',
});

// if success, we'll have the JWT token in `response`
```

So we can pass any custom vars if we wanted to.

### [Bonus] Can only sign in after email verified

```sql
SIGNIN (
    SELECT * 
    FROM user
    WHERE 
        (email = $user OR username = $user) AND
        crypto::argon2::compare(pass, $pass) AND
        email_verified_at != NONE
)
```