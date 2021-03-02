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

## Regex in My SQL
* Regular Expressions
<table border="1" width="30%">
    <tr>
        <th width="5%">Name</a>
        <th width="10%">Description</a>
        <th width="15%">Example</a>
    </tr>
    <tr>
        <td> NOT REGEXP </td>
        <td> Negation of REGEXP </td>
        <td>  </td>
    </tr>
    <tr>
        <td> REGEXP </td>
        <td> Whether string matches regular expression </td>
        <td> SELECT 'Michael!' REGEXP '.*'; <br>
             > 1 <br>
             SELECT 'a' REGEXP 'A', 'a' REGEXP BINARY 'A'; <br> 
             > 1  0 <br>
        </td>
    </tr>
    <tr>
        <td> REGEXP_INSTR() </td>
        <td> Starting index of substring matching regular expression </td>
        <td> SELECT REGEXP_INSTR('dog cat dog', 'dog', 2); <br>
             > 9 <br>
             SELECT REGEXP_INSTR('aa aaa aaaa', 'a{4}'); <br>
             > 8 <br>
        </td>
    </tr>
    <tr>
        <td> REGEXP_LIKE() </td>
        <td> Whether string matches regular expression </td>
        <td> SELECT REGEXP_LIKE('CamelCase', 'CAMELCASE'); <br> 
             > 1 <br>
             SELECT REGEXP_LIKE('a', 'A'), REGEXP_LIKE('a', BINARY 'A'); <br> 
             > 1  0 <br>
        </td>
    </tr>
    <tr>
        <td> REGEXP_REPLACE() </td>
        <td> Replace substrings matching regular expression </td>
        <td> SELECT REGEXP_REPLACE('a b c', 'b', 'X'); <br>
             > a X c <br>
             SELECT REGEXP_REPLACE('abc def ghi', '[a-z]+', 'X', 1, 3); <br>
             > abc def X <br> 
        </td>
    </tr>
    <tr>
        <td> REGEXP_SUBSTR() </td>
        <td> Return substring matching regular expression </td>
        <td> SELECT REGEXP_SUBSTR('abc def ghi', '[a-z]+'); <br>
             > abc <br>
             SELECT REGEXP_SUBSTR('abc def ghi', '[a-z]+', 1, 3); <br>
             > ghi <br>
        </td>
    </tr>
    <tr>
        <td> RLIKE </td>
        <td> Whether string matches regular expression </td>
        <td>  </td>
    </tr>
</table>
<br>


* Metacharacters
<table border="1" width="40%">
    <tr>
        <th width="5%">Metacharacter</a>
        <th width="10%">Description</a>
        <th width="20%">Example</a>
        <th width="5%">Example Matches</a>
    </tr>
    <tr>
        <td> ^ </td>
        <td> Start the match at the beginning of a stringe </td>
        <td> ^c% </td>
        <td> cat, car, chain </td>
    </tr>
    <tr>
        <td> | </td>
        <td> Alternation (either of two alternatives) </td>
        <td> c(a|o)% </td>
        <td> can, corn, cop </td>
    </tr>
    <tr>
        <td> () </td>
        <td> Group items in a single logical item </td>
        <td> c(a|o)% </td>
        <td> can, corn, cop </td>
    </tr>
    <tr>
        <td> _ </td>
        <td> Any single character (using LIKE and SIMILAR TO) </td>
        <td> c_ </td>
        <td> co, fico, pico </td>
    </tr>
    <tr>
        <td> % </td>
        <td> Any string (using LIKE and SIMILAR TO) </td>
        <td> c% </td>
        <td> chart, articulation, crate </td>
    </tr>
    <tr>
        <td> . </td>
        <td> Any single character (using POSIX) </td>
        <td> c. </td>
        <td> co, fico, pico </td>
    </tr>
    <tr>
        <td> .* </td>
        <td> Any string (using POSIX) </td>
        <td> c.* </td>
        <td> chart, articulation, crate </td>
    </tr>
    <tr>
        <td> + </td>
        <td> Repetition of the previous item one or more times </td>
        <td> co+ </td>
        <td> coo, cool </td>
    </tr>
</table>
<br>

* POSIX comparators
<table border="1" width="40%">
    <tr>
        <th width="5%">Operator</a>
        <th width="10%">Description</a>
        <th width="20%">Comparisons</a>
        <th width="5%">Output</a>
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
* [regexp](https://dev.mysql.com/doc/refman/8.0/en/regexp.html)
