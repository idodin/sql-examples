# Advanced Statements

In this page, I summarize concepts outlined in the COMP 421 Course that are described as *more advanced* concepts of SQL. We assume the following relations for examples used in this page:

**Skaters(__sid__, sname, rating, age)**

**Participates(__sid, cid__, rank)**

**Competition(__cid__, date, type)**

## Aggregation

### Counting

*Count* output tuples from statement:

**Example:**

```sql
SELECT COUNT(*)
FROM Skaters
```

```sql
SELECT COUNT(DISTINCT rating)
FROM Skaters
```

### Summing

*Sum* given attributes from statement (Needs to be numeric).

**Example:**

```sql
SELECT SUM(age)
FROM Skaters
```

### Averaging

Get *average* of given attributes from statement (Needs to be numeric).

**Example:**

```sql
SELECT AVG(age)
FROM Skaters
WHERE rating = 7
```

### Maximum

Get *maximum* attribute value from statement. (Does not need to be numeric).

**Example:**

```sql
SELECT MAX(age)
FROM Skaters
WHERE rating = 7
```

### Minimum

Get *minimum* attribute value from statement. (Does not need to be numeric).

**Example:**

```sql
SELECT MIN(age)
FROM Skaters
WHERE rating = 7
```

### Multiple Aggregators in a Single Statement

We can select **multiple** aggregators from a single statement.

**Example:**

```sql
SELECT AVG(age), COUNT(*)
FROM Skaters
WHERE rating = 7
```

## Grouping

What if we wanted to compute these values for subsets of outputs from our statement?

e.g. *Find the average age of the skaters in each rating level*

```sql
SELECT AVG(age)
FROM Skaters 
GROUP BY rating
```

## General Evaluation & Summary

```sql
SELECT target-list
FROM relation-list
(WHERE qualification)
GROUP BY grouping-list
(ORDER BY attributes from target-list)
```

- **AVG** - Compute Average.
- **SUM** - Compute Sum.
- **MAX** - Find Max.
- **MIN** - Find Min.
- **COUNT** - Count all outputted tuples. 

Use with DISTINCT to compute values for DISTINCT outputs. 

## Selections on Groups

We can perform selections on *tuples* using the **WHERE** clause. 

We can perform selections on *groups* in the same way using the **HAVING** caluse.

**Example**
```sql
SELECT rating, MIN(age)
FROM Skaters
WHERE rating > 5
GROUP BY rating
HAVING COUNT(*) >= 2
```

## Nesting Aggregate Operators

What if we wanted to compute the **minimum of average values**

For example: *find those rating for which the average age is the minimum over all ratings.*

We can **Create views** as intermediate relations to compute these:

```sql
CREATE VIEW Temp (rating, avgage)
AS SELECT rating, AVG(age) AS avgage
    FROM skaters
    GROUP BY rating

SELECT rating, avgage
FROM Temp
WHERE avgage =  (SELECT MIN(avgage) FROM Temp)
```
