# postgresql2

# PostgreSQL

SQL is a communication language. It’s how we interface or interact with databases that support it. PostgreSQL is a database that uses SQL. There are many other databases that support SQL. As an example, MSSQL.

When designing a database, there are a few questions that are important to ask.

1. What kind of thing are we storing?
2. What properties does this thing have?
3. What type of data does each of those properties contain?

```sql
CREATE TABLE cities (
  name VARCHAR(50), -- variable length character
  country VARCHAR(50),
  population INTEGER, -- number without a decimal
  area INTEGER
);
```

In SQL, ******************Keywords****************** tell the database we want to do something and are always written in all caps. ************************Identifiers************************ are always written in all lower case and they point to things we want to act on. In the example above, `CREATE TABLE` are keywords and `cities` is an identifier.

```sql
-- Single
INSERT INTO cities (name, country, population, area) 
VALUES ('Tokyo', 'Japan', 38505000, 8223);
-- Multiple
INSERT INTO cities (name, country, population, area) 
VALUES 
  ('Delhi', 'India', 28125000, 2240),
  ('Shanghai', 'China', 22125000, 4015),
  ('Sao Paulo', 'Brazil', 20935000, 3043);
```

```sql
// Gets all columns from cities
SELECT * FROM cities;
// Gets name, population from cities
SELECT name, population FROM cities;
// Calculates population density
SELECT name, population / area AS density from cities;
```

The population divided by population is an example of an operation. You can perform operations on different datatype than integers.

```sql
SELECT name || country FROM cities; // joins two VARCHARS
SELECT name || ', ' || country FROM cities;
SELECT name ||', ' || country AS location FROM cities;
SELECT CONCAT(name, ', ', country) AS location FROM cities; // same thing
SELECT LOWER(name) AS lower, UPPER(country) as upper FROM cities; // lower, upper
```

You can also limit result sets by using `WHERE`.

```sql
SELECT name, area FROM cities WHERE area > 4000;
SELECT name, area FROM cities WHERE area = 8223;
SELECT name, area FROM cities WHERE area != 8223;
SELECT name, area FROM cities WHERE area BETWEEN 2000 AND 4000;
SELECT name, area FROM cities WHERE name IN ('Delhi', 'Shanghai');
SELECT name, area FROM cities WHERE name NOT IN ('Delhi', 'Shanghai');
```

```sql
UPDATE cities SET population = 39505000 WHERE name = 'Tokyo'
```

## Relationships

### One-to-Many / Many-to-One

One record in table refers to many records in another table. “Has many”. “Has one”.

### One-To-One

Where one record in a table belongs to one record in another table

### Many-to-Many

When many records in one table belong to many records in another table.

### Primary Keys and Foreign Keys

**Primary keys** uniquely identify rows in a table. A **foreign key** identifies records in another table.

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY, -- generate values for us, creates index
  username VARCHAR(50)
)

INSERT INTO users (username)
VALUES ('monahan93'), ('pfeffer'), ('si93onis'), ('99stroman');
```

```sql
CREATE TABLE photos (
  id SERIAL PRIMARY KEY,
  url VARCHAR(200),
  user_id INTEGER REFERENCES users(id) -- foreign key
);

INSERT INTO photos (url, user_id)
VALUES ('http://one.jpg', 4);

INSERT INTO photos (url, user_id)
VALUES 
  ('http://two.jpg', 1),
  ('http://a.jpg', 1),
  ('http://as.jpg', 1),
  ('http://r.jpg', 2),
  ('http://gg.jpg', 3),
  ('http://rr.jpg', 4);

SELECT * FROM photos WHERE user_id = 4;

SELECT username, url 
FROM users
JOIN photos ON photos.user_id = users.id
WHERE users.id = 4;
```

If you insert a photo for a user that does not exist, the `REFERENCES` keyword will reject the operation. This behavior creates ****************data consistency****************. It means we don’t have to worry about unexpected behavior with our data and that we can always expect the same behavior. To avoid this, we can pass `NULL` as the user id. This allows for bypassing the constraint.

```sql
DROP TABLE photos; -- delete a table

CREATE TABLE photos (
  id SERIAL PRIMARY KEY,
  url VARCHAR(200),
  -- causes photos to delete when referenced user is deleted
  user_id INTEGER REFERENCES users(id) ON DELETE CASCADE
);

-- related photos will be deleted
DELETE FROM users WHERE id = 1;

CREATE TABLE photos (
  id SERIAL PRIMARY KEY,
  url VARCHAR(200),
  -- causes photos associated user_id key to be set to null
  user_id INTEGER REFERENCES users(id) ON DELETE SET NULL
);

-- related photos will have user_id set to null
DELETE FROM users WHERE id = 1;
```

Joins are used to merge data from multiple tables, and aggregation is used to assimilate values from multiple rows.

```jsx
SELECT contents, username, photo_id
FROM comments
JOIN users ON users.id = comments.user_id

SELECT *
FROM comments
JOIN photos ON photos.id = comments.photo_id
JOIN users ON users.id = comments.user_id
```
