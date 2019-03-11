# Basic Statements

In this page, I summarize concepts outlined in the COMP 421 Course that are described as *basics* of SQL. We assume the following relations for examples used in this page:

**Skaters(__sid__, sname, rating, age)**

**Participates(__sid, cid__, rank)**

**Competition(__cid__, date, type)**

## Basic form of a query
```sql
SELECT desired attributes
FROM list of relations
WHERE qualification
```
Fairly straightforward, we select all columns we want from a list of relations (tables) and may **optionally** specify some condition that filters our results. 

**Example:**
```sql
SELECT sname, rating
FROM Skaters
WHERE rating > 9 OR age < 12
```

## Relation Algebra - SQL Similarities
**Select** - <img src="https://latex.codecogs.com/gif.latex?\pi_{rating,age}" />

**Where** - <img src="https://latex.codecogs.com/gif.latex?\sigma_{rating>=10}" />

**From** - <img src="https://latex.codecogs.com/gif.latex?\sigma_{rating}[Skaters]" />

## Relational Algebra - SQL Differences

Results of SQL queries are generally **not sets** - **duplicates are allowed.**

## WHERE Clause

**attr1 op const**
i.e. An attribute compared with a constant
*e.g.* `age > 10 `

**attr1 op attr2**
i.e. An attribute compared with another
*e.g.* `age < rating`

### Operators
- **<**
- **=**
- **>**
- **<>**
- **<=**
- **>=**
- **LIKE**

**Important note:** We can use arithmetic operations before comparing.
*e.g.* `rating > 2*age` is allowed. 

### Combining Conditions (Boolean Operators)
- **AND**
- **OR**
- **NOT**

### String Selectors (the LIKE Operators)
- **%** : *any string*
- **_** : *any cahracter*

**Examples:**

*Names ending in 'y'*:
```sql
name LIKE '%y'
```
*Names that have an 'i' in the second position*:
```sql
name LIKE '_i%'
```

## DISTINCT Selection

We can select **distinct** elements using the **DISTINCT** keyword:
```sql
SELECT DISTINCT age
FROM Skaters
```

## Select *all* columns

We can select **all columns** with the star (\*) keyword:
```sql
SELECT *
FROM Skaters
WHERE rating < 9
```

## Renaming Attribute Outputs

We can rename our outputted columns using the **AS** keyword:

**Renaming a column**: 
```sql
SELECT sname, rating AS reality
FROM Skaters
```

**Renaming a column with some arithmetic**:
```sql
SELECT sname, rating*2 AS twice_rating
FROM Skaters
```

**Giving a constant a name**:
```sql
SELECT sname, '10' AS dream
FROM Skaters
```

> Note that quotation marks are **required** for constants.

## Ordering Output

We can **order** output using the **ORDER** keyword:

```sql
SELECT * 
FROM Skaters
ORDER BY age, rating
```

In the above example, we order with *priorities*, i.e. we first sort (in ascending order) by age, and **if** we find two attributes with the same age value, we order (in ascending order) by rating.

#Multirelational Queries

Multirelational Queries are the term given to Queries from **multiple tables**

## Joins using the FROM Clause:
```sql
SELECT * 
FROM Skaters, Participates
```

The above essentially selects all columns from the **cross-product** of the Skaters and Participates tables.
*Recall Cross-Product:*
![](https://puu.sh/CY4KG/c627676784.png)

```sql
SELECT sname
FROM Skaters, Participates
WHERE Skaters.sid = Participates.sid
```

## Using JOIN keyword:
```sql
SELECT sname
FROM Skaters JOIN Participates
ON Skaters.sid = Participates.sid
```

The above is equivalent to what we had previously i.e. **A Cross-Product and then a Selection on that Cross-Product**.

> Note that we use Skaters.sid, Participates.sid both here and in the previous example to disambiguate attributes from the different relations.

## Using Range Variables

In the below example, the range variables **S** and **P** are not *required*. (e.g. we could just reference the relations with their full names "Skaters" and "Participates", respectively.)

```sql
SELECT S.sname
FROM Skaters S, Participates P
WHERE S.sid = P.sid AND P.cid = 101
```

But, then consider the following example where the range variables **p1** and **p2** *are* **required**:

```sql
SELECT p1.sid, p2.sid
FROM Participates p1, Participates p2
WHERE p1.cid = p2.cid AND p1.sid < p2.sid
```

>Note that we also use `p1.sid < p2.sid` so that we don't produce duplicate pairs.

**In general**: We **require** range variables when we use the same **relation/table twice or more** in the FROM clause. THis is because, in these cases, the full relation name is no longer descriptive to which '*instance*' of the table we are referring to. 

## Unions (UNION)

Consider taking the **UNION** of two outputs. e.g. if we want to *find skaters' sids that have participated in a regional or a local competition (or both)*. 

```sql
SEELCT P.sid
FROM Participates P, Competition C
WHERE P.cid = C.cid AND
  (C.type = 'regional' OR C.type = 'local')
```

Is actually **different to**:

```sql
SELECT P.sid
FROM Participates P, Competition C
WHERE P.cid = C.cid AND C.type = 'local'
UNION
SELECT P.sid
FROM Participates P, Competition C
WHERE P.cid = C.cid AND C.type= 'regional'
```

This is because the former uses **multi-sets**. In essence, this means that if we have an sid that has participated in multiple local competitions, multiple regional competitions or multiple regional and local competitions, the sid will be returned **multiple times** by the query.

The latter uses oe of the set operators (**UNION**) which returns **sets**. This means that even if we have an sid that has participated in multiple local, regional or regional and local competitions, the sid will only be returned **once** by the query.

## Intersections (INTERSECT)

What if we want to *find skaters' sids that have participated in a regional and a local competition*. 

```sql
SELECT P.sid
FROM Participates P, Competition C
WHERE P.cid = C.cid AND C.type = 'local'
INTERSECT
SELECT P.sid
FROM Participates P, Competition C
WHERE P.cid = C.cid AND C.type = 'regional
```

We could also do this with joins:

```sql
SELECT P1.sid 
FROM Participates P1, Participates P2, Competition C1, Competition C2
WHERE (P1.cid = C1.cid AND C1.type = 'local') AND
      (P2.cid = C2.cid AND C2.type = 'regional') AND
      (P1.sid = P2.sid)
```

However the above would return duplicated sid's. 

## Differences (EXCEPT)

Finding *skaters' sids that have participated in a local but not in a regional competition*.

```sql
SELECT P.sid
FROM Participates P, Competition C
WHERE P.cid = C.cid AND C.type = 'local' 
EXCEPT
SELECT P.sid
FROM Participates P, Competition C
WHERE P.cid = C.cid AND C.type = 'regional'
```

## Multiset Semantic in SQL

Normally SQL uses **multisets**, but when we use any of the **UNION**, **INTERSECT** or **EXCEPT** keywords, it uses **sets**. 

If we want to return **multisets** with these operations we need to use the **UNION ALL**, **INTERSECT ALL**, or **EXCEPT ALL** keyphrases. 

# Nested Queries

When we use Queries inside of other queries. 

For example:

```sql 
SELECT sname
FROM Skaters
WHERE sid IN (SELECT sid
      FROM Participates
      WHERE cid = 101)
```

Or by contrast:

```sql 
SELECT sname
FROM Skaters
WHERE sid NOT IN (SELECT sid
      FROM Participates
      WHERE cid = 101)
```

Or with multiple attributes:

```sql 
...
WHERE (a1, a2) IN (SELECT a3, a4...)
```
 
 ## Non-correlated Sub-Queries
 
 A **non-correlated** sub-query is a sub-query that **does not refer to the outer/containing query**.

**Example**:

```sql
SELECT sname
FROM Skaters S
WHERE S.sid IN (SELECT P.sid
                FROM Participates P
                WHERE P.cid = 101)
```

This is a **non-correlated** sub-query because it does not depend on the **`Skaters S`** relation in the outer query. 

## Correlated Sub-Queries

By contrast, a **correlated** sub-query is a sub-query is one that **does refer to the outer/containing query.**

**Example**:

```sql

SELECT S.sname
FROM Skaters S
WHERE EXISTS (SELECT *
              FROM Participates P
              WHERE P.cid = 101 AND
                    P.sid = S.sid)
```

This is a **correlated** sub-query because the **WHERE** clause in the inner-query depends on **`S.sid`** from the **`Skaters S`** relation in the outer query. 

## Sub Query Operators

In the **WHERE** clause:

- **IN** - Found in output of sub-query.
- **NOT IN** - Not found in output of sub-query.
- **EXISTS** - There is at least a single tuple output from the sub-query.
- **NOT EXISTS** - There is no tuple output from the sub-query.

**Quantifiers**:

*Also found in the **WHERE** clause:*

- **ANY** - At least one output tuple from sub-query meets the condition specified.
- **ALL** - All tuples in output sub-query meet the condition specified.

**Examples:**

```sql
SELECT * 
FROM Skaters 
WHERE rating >= ALL (SELECT rating FROM Skaters)
```

```sql
SELECT *
FROM Skaters
WHERE rating >= ANY (SELECT rating FROM Skaters)
```

