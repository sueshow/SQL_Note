# SQL

## Crosstab Query
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

## Generate
* 範例：generate_series
```
SELECT * FROM generate_series('2008-03-01 00:00'::timestamp,
                              '2008-03-04 12:00', '10 hours');
```

```
SELECT current_date + s.a AS dates FROM generate_series(0,14,7) AS s(a);
```
<br>

## Search Argument (SARG)
* 有效的查詢參數：`=`、`>`、`<`、`>=`、`<=`、`Between`、`Like`，如`like 'T%'`符合有效SARG，但`like '%T'`就不符合
* 非有效的查詢參數：`NOT`、`!=`、`<>`、`!<`、`!>`、`NOT EXISTS`、`NOT IN`、`NOT LIKE`
* 影響效能的寫法：
  * 條件式中轉換欄位類型
  * 條件式中對欄位做運算
  * 條件式中對欄位使用函數
  * select 撈取不必要的欄位
  * 負向查詢
  * 少用 union(去重複)，可使用 union all 替代
  * 少用子查詢
  * 用 exists/not exists 取代 in/not in
  * 不須排序的資料就別用 order by
  * 善用 CTE(Common Table Expression) 改善效能
  * 避免執行不必要的查詢
<br>

## Regex
* Comparators
<table border="1" width="50%">
    <tr>
        <th width="10%">Operator</a>
        <th width="10%">Description</a>
        <th width="20%">Comparisons</a>
        <th width="10%">Output</a>
    </tr>
    <tr>
        <td rowspan="2"> ~ </td>
        <td rowspan="2"> Match, Case sensitive </td>
        <td> 'Timmy' ~ 'T%' </td>
        <td> True </td>
    </tr>
    <tr>
        <td> 'Timmy' ~ 't%' </td>
        <td> False </td>
    </tr>
    <tr>
        <td rowspan="2"> ~* </td>
        <td rowspan="2"> Match, not Case sensitive </td>
        <td> 'Timmy' ~* 'T%' </td>
        <td> True </td>
    </tr>
    <tr>
        <td> 'Timmy' ~* 't%' </td>
        <td> True </td>
    </tr>
    <tr>
        <td rowspan="2"> !~ </td>
        <td rowspan="2"> No Match, Case sensitive </td>
        <td> 'Timmy' !~ 'T%' </td>
        <td> False </td>
    </tr>
    <tr>
        <td> 'Timmy' !~ 't%' </td>
        <td> True </td>
    </tr>
    <tr>
        <td rowspan="2"> !~* </td>
        <td rowspan="2"> No Match, not Case sensitive </td>
        <td> 'Timmy' !~* 'T%' </td>
        <td> False </td>
    </tr>
    <tr>
        <td> 'Timmy' !~* 't%' </td>
        <td> False </td>
    </tr>
</table>
<br>

## 參考資料：
* [Crosstab Query](https://stackoverflow.com/questions/3002499/postgresql-crosstab-query)
* [Generate](https://www.postgresql.org/docs/9.1/functions-srf.html)
* [PostgreSQL 正體中文使用手冊](https://docs.postgresql.tw/)
* [CTE](https://dotblogs.com.tw/wasichris/2016/11/03/151251)
* [regex](https://dataschool.com/how-to-teach-people-sql/how-regex-works-in-sql/)
