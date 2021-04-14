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

## Array 
* Operators
<table border="1" width="20%">
    <tr>
        <th width="2%">Operator</a>
        <th width="8%">Description</a>
        <th width="10%">Example</a>
        <th width="10%">Result</a>
    </tr>
    <tr>
        <td> <code> = </code> </td>
        <td> equal </td>
        <td> ARRAY[1.1,2.1,3.1]::int[] = ARRAY[1,2,3] </td>
        <td> true </td>
    </tr>
    <tr>
        <td> <code> < </code> </td>
        <td> less than </td>
        <td> <code> ARRAY[1,2,3] < ARRAY[1,2,4] </code> </td>
        <td> true </td>
    </tr>
    <tr>
        <td> <code> > </code> </td>
        <td> greater than </td>
        <td> <code> ARRAY[1,4,3] > ARRAY[1,2,4] </code> </td>
        <td> true </td>
    </tr>
    <tr>
        <td> <code> @> </code> </td>
        <td> contains </td>
        <td> <code> ARRAY[1,4,3] @> ARRAY[3,1] </code> </td>
        <td> true </td>
    </tr>
    <tr>
        <td> <code> @> </code> </td>
        <td> contains </td>
        <td> <code> ARRAY[1,4,3] @> ARRAY[3,1] </code> </td>
        <td> true </td>
    </tr>
    <tr>
        <td> <code> <@ </code> </td>
        <td> is contained by </td>
        <td> <code> ARRAY[2,7] <@ ARRAY[1,7,4,2,6] </code> </td>
        <td> true </td>
    </tr>
    <tr>
        <td> <code> && </code> </td>
        <td> overlap (have elements in common) </td>
        <td> <code> ARRAY[1,4,3] && ARRAY[2,1] </code> </td>
        <td> true </td>
    </tr>
    <tr>
        <td rowspan="4"> <code> || </code> </td>
        <td rowspan="2"> array-to-array concatenation </td>
        <td> <code> ARRAY[1,2,3] || ARRAY[4,5,6] </code> </td>
        <td> {1,2,3,4,5,6} </td>
    </tr>
    <tr>
        <td> <code> ARRAY[1,2,3] || ARRAY[[4,5,6],[7,8,9]] </code> </td>
        <td> {{1,2,3},{4,5,6},{7,8,9}} </td>
    </tr>
    <tr>
        <td> element-to-array concatenation </td>
        <td> <code> 3 || ARRAY[4,5,6] </code> </td>
        <td> {3,4,5,6} </td>
    </tr>
    <tr>
        <td> array-to-element concatenation </td>
        <td> <code> ARRAY[4,5,6] || 7 </code> </td>
        <td> {4,5,6,7} </td>
    </tr>
</table>

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
<table border="1" width="20%">
    <tr>
        <th width="2%">Name</a>
        <th width="8%">Description</a>
        <th width="10%">Example</a>
    </tr>
    <tr>
        <td> NOT REGEXP </td>
        <td> Negation of REGEXP </td>
        <td>  </td>
    </tr>
    <tr>
        <td> REGEXP </td>
        <td> Whether string matches regular expression </td>
        <td> <code> 
             SELECT 'Michael!' REGEXP '.*'; <br>
             > 1 <br>
             SELECT 'a' REGEXP 'A', 'a' REGEXP BINARY 'A'; <br> 
             > 1  0 <br>
             </code>
        </td>
    </tr>
    <tr>
        <td> REGEXP_INSTR() </td>
        <td> Starting index of substring matching regular expression </td>
        <td> <code>
             SELECT REGEXP_INSTR('dog cat dog', 'dog', 2); <br>
             > 9 <br>
             SELECT REGEXP_INSTR('aa aaa aaaa', 'a{4}'); <br>
             > 8 <br>
             </code>
        </td>
    </tr>
    <tr>
        <td> REGEXP_LIKE() </td>
        <td> Whether string matches regular expression </td>
        <td> <code>
             SELECT REGEXP_LIKE('CamelCase', 'CAMELCASE'); <br> 
             > 1 <br>
             SELECT REGEXP_LIKE('a', 'A'), REGEXP_LIKE('a', BINARY 'A'); <br> 
             > 1  0 <br>
             </code>
        </td>
    </tr>
    <tr>
        <td> REGEXP_REPLACE() </td>
        <td> Replace substrings matching regular expression </td>
        <td> <code>
             SELECT REGEXP_REPLACE('a b c', 'b', 'X'); <br>
             > a X c <br>
             SELECT REGEXP_REPLACE('abc def ghi', '[a-z]+', 'X', 1, 3); <br>
             > abc def X <br> 
             </code>
        </td>
    </tr>
    <tr>
        <td> REGEXP_SUBSTR() </td>
        <td> Return substring matching regular expression </td>
        <td> <code>
             SELECT REGEXP_SUBSTR('abc def ghi', '[a-z]+'); <br>
             > abc <br>
             SELECT REGEXP_SUBSTR('abc def ghi', '[a-z]+', 1, 3); <br>
             > ghi <br>
             </code>
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
<table border="1" width="35%">
    <tr>
        <th width="2%">Metacharacter</a>
        <th width="15%">Description</a>
        <th width="15%">Example</a>
        <th width="3%">Example Matches</a>
    </tr>
    <tr>
        <td> <code> ^ </code> </td>
        <td> Start the match at the beginning of a stringe </td>
        <td> </code> ^c% </code> </td>
        <td> cat, car, chain </td>
    </tr>
    <tr>
        <td> <code> | </code> </td>
        <td> Alternation (either of two alternatives) </td>
        <td> <code> c(a|o)% </code> </td>
        <td> can, corn, cop </td>
    </tr>
    <tr>
        <td> <code> () </code> </td>
        <td> Group items in a single logical item </td>
        <td> <code> c(a|o)% </code> </td>
        <td> can, corn, cop </td>
    </tr>
    <tr>
        <td> <code> _ </code> </td>
        <td> Any single character (using LIKE and SIMILAR TO) </td>
        <td> <code> c_ </code> </td>
        <td> co, fico, pico </td>
    </tr>
    <tr>
        <td> <code> % </code> </td>
        <td> Any string (using LIKE and SIMILAR TO) </td>
        <td> <code> c% </code> </td>
        <td> chart, articulation, crate </td>
    </tr>
    <tr>
        <td> <code> . </code> </td>
        <td> Any single character (using POSIX) </td>
        <td> <code> c. </code> </td>
        <td> co, fico, pico </td>
    </tr>
    <tr>
        <td> <code> .*  </code> </td>
        <td> Any string (using POSIX) </td>
        <td> <code> c.* </code> </td>
        <td> chart, articulation, crate </td>
    </tr>
    <tr>
        <td> <code> + </code> </td>
        <td> Repetition of the previous item one or more times </td>
        <td> <code> co+ </code> </td>
        <td> coo, cool </td>
    </tr>
</table>
<br>

* POSIX comparators
<table border="1" width="30%">
    <tr>
        <th width="5%">Operator</a>
        <th width="10%">Description</a>
        <th width="10%">Comparisons</a>
        <th width="5%">Output</a>
    </tr>
    <tr>
        <td rowspan="2"> <code> ~ </code> </td>
        <td rowspan="2"> Match, Case sensitive </td>
        <td> <code> 'Timmy' ~ 'T%' </code> </td>
        <td> True </td>
    </tr>
    <tr>
        <td> <code> 'Timmy' ~ 't%' </code> </td>
        <td> False </td>
    </tr>
    <tr>
        <td rowspan="2"> <code> ~* </code> </td>
        <td rowspan="2"> Match, not Case sensitive </td>
        <td> <code> 'Timmy' ~* 'T%' </code> </td>
        <td> True </td>
    </tr>
    <tr>
        <td> <code> 'Timmy' ~* 't%' </code> </td>
        <td> True </td>
    </tr>
    <tr>
        <td rowspan="2"> <code> !~ </code> </td>
        <td rowspan="2"> No Match, Case sensitive </td>
        <td> <code> 'Timmy' !~ 'T%' </code> </td>
        <td> False </td>
    </tr>
    <tr>
        <td> <code> 'Timmy' !~ 't%' </code> </td>
        <td> True </td>
    </tr>
    <tr>
        <td rowspan="2"> <code> !~* </code> </td>
        <td rowspan="2"> No Match, not Case sensitive </td>
        <td> <code> 'Timmy' !~* 'T%' </code> </td>
        <td> False </td>
    </tr>
    <tr>
        <td> <code> 'Timmy' !~* 't%' </code> </td>
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
* [Array Functions and Operators](https://www.postgresql.org/docs/9.1/functions-array.html)
