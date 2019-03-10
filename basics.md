# Basic Statements

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

## Using the FROM Clause:
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

## Using JOINs:
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
