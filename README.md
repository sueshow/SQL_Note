# SQL

## Crosstab Query：
* 範例：json_object_agg In PostgreSQL  
```
CREATE TEMP TABLE t (
  section   text
, status    text
, ct        integer  -- don't use "count" as column name.
);

INSERT INTO t VALUES 
  ('A', 'Active', 1), ('A', 'Inactive', 2)
, ('B', 'Active', 4), ('B', 'Inactive', 5)
                   , ('C', 'Inactive', 7); 


SELECT section,
       (obj ->> 'Active')::int AS active,
       (obj ->> 'Inactive')::int AS inactive
FROM (SELECT section, json_object_agg(status,ct) AS obj
      FROM t
      GROUP BY section
     )X;
```
<br>

## Generate：
* 範例：generate_series
```
SELECT * FROM generate_series('2008-03-01 00:00'::timestamp,
                              '2008-03-04 12:00', '10 hours');
```

```
SELECT current_date + s.a AS dates FROM generate_series(0,14,7) AS s(a);
```
<br>

## 參考資料：
* [Crosstab Query](https://stackoverflow.com/questions/3002499/postgresql-crosstab-query)
* [Generate](https://www.postgresql.org/docs/9.1/functions-srf.html)
* [PostgreSQL 正體中文使用手冊](https://docs.postgresql.tw/)
