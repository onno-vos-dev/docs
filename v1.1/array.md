---
title: ARRAY
summary: The ARRAY data type stores one-dimensional, 1-indexed, homogeneous arrays of any non-array data types
toc: true
---

<span class="version-tag">New in v1.1:</span>The `ARRAY` data type stores one-dimensional, 1-indexed, homogeneous arrays of any non-array [data type](data-types.html).

The `ARRAY` data type is useful for ensuring compatibility with ORMs and other tools. However, if such compatibility is not a concern, it's more flexible to design your schema with normalized tables.


{{site.data.alerts.callout_info}} CockroachDB does not support nested arrays, creating database indexes on arrays, and ordering by arrays.{{site.data.alerts.end}}

## Syntax

A value of data type `ARRAY` can be expressed in the following ways:


- Appending square brackets (`[]`) to any non-array [data type](data-types.html).
- Adding the term `ARRAY` to any non-array [data type](data-types.html).

## Size

The size of an `ARRAY` value is variable, but it's recommended to keep values under 1 MB to ensure performance. Above that threshold, [write amplification](https://en.wikipedia.org/wiki/Write_amplification) and other considerations may cause significant performance degradation.  

## Examples

### Creating an array column by appending square brackets

~~~ sql
> CREATE TABLE a (b STRING[]);

> INSERT INTO a VALUES (ARRAY['sky', 'road', 'car']);

> SELECT * FROM a;
~~~
~~~
+----------------------+
|          b           |
+----------------------+
| {"sky","road","car"} |
+----------------------+
(1 row)
~~~

### Creating an array column by adding the term `ARRAY`

~~~ sql
> CREATE TABLE c (d INT ARRAY);

> INSERT INTO c VALUES (ARRAY[10,20,30]);

> SELECT * FROM c;
~~~
~~~
+------------+
|     d      |
+------------+
| {10,20,30} |
+------------+
(1 row)
~~~

### Accessing an array element using array index
{{site.data.alerts.callout_info}} Arrays in CockroachDB are 1-indexed. {{site.data.alerts.end}}

~~~ sql
> SELECT * FROM c;
~~~
~~~
+------------+
|     d      |
+------------+
| {10,20,30} |
+------------+
(1 row)
~~~

~~~ sql
> SELECT d[2] FROM c;
~~~
~~~
+------+
| d[2] |
+------+
|   20 |
+------+
(1 row)
~~~

### Appending an element to an array

#### Using the `array_append` function

~~~ sql
> SELECT * FROM c;
~~~
~~~
+------------+
|     d      |
+------------+
| {10,20,30} |
+------------+
(1 row)
~~~
~~~ sql
> UPDATE c SET d = array_append(d, 40) WHERE d[3] = 30;

> SELECT * FROM c;
~~~
~~~
+---------------+
|       d       |
+---------------+
| {10,20,30,40} |
+---------------+
(1 row)
~~~

#### Using the append (`||`) operator

~~~ sql
> SELECT * FROM c;
~~~
~~~
+---------------+
|       d       |
+---------------+
| {10,20,30,40} |
+---------------+
(1 row)
~~~
~~~ sql
> UPDATE c SET d = d || 50 WHERE d[4] = 40;

> SELECT * FROM c;
~~~
~~~
+------------------+
|        d         |
+------------------+
| {10,20,30,40,50} |
+------------------+
(1 row)
~~~


## See Also

[Data Types](data-types.html)
