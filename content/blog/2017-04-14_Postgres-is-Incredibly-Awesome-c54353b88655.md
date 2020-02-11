---
slug: postgres-is-incredibly-awesome
date: 2017-04-14T23:56:10.071Z
title: "Postgres is Incredibly Awesome"
template: "post"
draft: false
description: "I suspect that a large percentage of users come to Postgres for PostGIS. You get spatial data and operations wrapped up in a familiar SQL syntax. This enables a developer familiar with relational…"
category: ""
tags: [Programming,Google Cloud Platform,Postgres,Startup,Sql]
---

### Postgres is Incredibly Awesome

I suspect that a large percentage of users come to Postgres for PostGIS. You get spatial data and operations wrapped up in a familiar SQL syntax. This enables a developer familiar with relational databases to quickly begin dealing with spatial data. PostGIS plays well with other popular open source tools such as GDAL (OGR), GeoServer, and QGIS as well as commercial offerings from ESRI and Safe Software (FME); to name a few. Add on the fact that the Django ORM can handle [spatial data and operations](https://docs.djangoproject.com/en/1.10/ref/contrib/gis/) and you are enabling developers to do some serious geospatial processing.

That being said, there _are_ other features of Postgres that should not be overlooked. In particular: crosstabs, array columns, and builtin JSON support. While Postgres is first and foremost a _relational_ database; all of these features, in their own way, allow the designer to de-normalize the database to some degree.

#### [Crosstabs](https://www.postgresql.org/docs/9.6/static/tablefunc.html)

The _tablefunc_ module includes various functions that return tables. In particular, the _crosstab_ function:

> Produces a “pivot table” containing row names plus N value columns, where N is determined by the row type specified in the calling query.

If this doesn’t make much sense, don’t worry… it’s not very clear what this means from this simple explanation. Rather than expanding on this here, see this [blog post](http://imasdemase.com/en/programacion-2/tablas-cruzadas-en-postgresql-pivotmytable/) for a practical explanation and an excellent helper function.

#### [Arrays](https://www.postgresql.org/docs/9.6/static/arrays.html)

> PostgreSQL allows columns of a table to be defined as variable-length multidimensional arrays. Arrays of any built-in or user-defined base type, enum type, or composite type can be created.

This feature allows the designer to create a column that can store a variable amount of data; something that might normally be handled with a many-to-many relationship defined through additional tables, for example. The array column can be indexed, so queries to find overlapping rows can be quite fast. For example, counting all “employees” on the “test” project might look something like this:

```
SELECT COUNT(*) FROM employees WHERE projects && ‘{test}’;
```

instead of this:

```
SELECT COUNT(*)  
FROM   
 employees,   
 employees_projects,  
 projects  
WHERE   
 employees.id = employees_projects.employee_id AND  
 employees_projects.project_id = projects.id AND  
 projects.name = 'test';
```

#### [JSON](https://www.postgresql.org/docs/9.6/static/datatype-json.html)

> JSON data types are for storing JSON (JavaScript Object Notation) data, as specified in [RFC 7159](http://rfc7159.net/rfc7159). Such data can also be stored as text, but the JSON data types have the advantage of enforcing that each stored value is valid according to the JSON rules. There are also assorted JSON-specific functions and operators available for data stored in these data types; see [Section 9.15](https://www.postgresql.org/docs/9.6/static/functions-json.html).

JSON columns can also be GIN indexed, which makes “existence and containment” queries quite efficient. One practical application of this is the ability to store “similar” data in the same table. That is, common fields can be extracted into regular columns (with indexes, etc.) with the remainder stored in a catch-all JSON column. In the extreme case, you could make a table with a single column containing a JSON document for each row… like a poor man’s MongoDB?
