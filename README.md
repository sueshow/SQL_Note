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
     )X
```
<br>

## 參考資料：
* https://stackoverflow.com/questions/3002499/postgresql-crosstab-query
