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
        <td> <code> ARRAY[1.1,2.1,3.1]::int[] = ARRAY[1,2,3] </code> </td>
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
        <td> <code> NOT REGEXP </code> </td>
        <td> Negation of REGEXP </td>
        <td>  </td>
    </tr>
    <tr>
        <td> <code> REGEXP </code> </td>
        <td> Whether string matches regular expression </td>
        <td> <code> SELECT 'Michael!' REGEXP '.*'; </code> <br>
             > 1 <br>
             <code> SELECT 'a' REGEXP 'A', 'a' REGEXP BINARY 'A'; </code> <br> 
             > 1  0 <br> 
        </td>
    </tr>
    <tr>
        <td> <code> REGEXP_INSTR() </code> </td>
        <td> Starting index of substring matching regular expression </td>
        <td> <code> SELECT REGEXP_INSTR('dog cat dog', 'dog', 2); </code> <br>
             > 9 <br>
             <code> SELECT REGEXP_INSTR('aa aaa aaaa', 'a{4}'); </code> <br>
             > 8 <br>
        </td>
    </tr>
    <tr>
        <td> <code> REGEXP_LIKE() </code> </td>
        <td> Whether string matches regular expression </td>
        <td> <code> SELECT REGEXP_LIKE('CamelCase', 'CAMELCASE'); </code> <br> 
             > 1 <br>
             <code> SELECT REGEXP_LIKE('a', 'A'), REGEXP_LIKE('a', BINARY 'A'); </code> <br> 
             > 1  0 <br>
        </td>
    </tr>
    <tr>
        <td> <code> REGEXP_REPLACE() </code> </td>
        <td> Replace substrings matching regular expression </td>
        <td> <code> SELECT REGEXP_REPLACE('a b c', 'b', 'X'); </code> <br>
             > a X c <br>
             <code> SELECT REGEXP_REPLACE('abc def ghi', '[a-z]+', 'X', 1, 3); </code> <br>
             > abc def X <br> 
        </td>
    </tr>
    <tr>
        <td> <code> REGEXP_SUBSTR() </code> </td>
        <td> Return substring matching regular expression </td>
        <td> <code> SELECT REGEXP_SUBSTR('abc def ghi', '[a-z]+'); </code> <br>
             > abc <br>
             <code> SELECT REGEXP_SUBSTR('abc def ghi', '[a-z]+', 1, 3); </code> <br>
             > ghi <br>
        </td>
    </tr>
    <tr>
        <td> <code> RLIKE </code> </td>
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
        <td> <code> ^c% </code> </td>
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

## 行列轉換
* 列轉行
  * Postgresql
    ```
    select * from test ;
     name
    ------
     AA
     BB
     CC
 
    select string_agg(name,',') from test;
     string_agg
    ------------
     AA,BB,CC
    ```
  * SQL SERVER 2000
    ```
    create table tb(姓名 varchar(10) , 課程 varchar(10) , 分數 int) 
    insert into tb values('張三' , '語文' , 74) 
    insert into tb values('張三' , '數學' , 83) 
    insert into tb values('張三' , '物理' , 93) 
    insert into tb values('李四' , '語文' , 74) 
    insert into tb values('李四' , '數學' , 84) 
    insert into tb values('李四' , '物理' , 94) 
    go 


    --靜態SQL,指課程只有語文、數學、物理這三門課程。(以下同) 
    select 姓名 as 姓名 , 
      max(case 課程 when '語文' then 分數 else 0 end) 語文, 
      max(case 課程 when '數學' then 分數 else 0 end) 數學, 
      max(case 課程 when '物理' then 分數 else 0 end) 物理 
      cast(avg(分數*1.0) as decimal(18,2)) 平均分, 
      sum(分數) 總分 
    from tb 
    group by 姓名 


    --動態SQL,指課程不止語文、數學、物理這三門課程。(以下同) 
    declare @sql varchar(8000) 
    set @sql = 'select 姓名 ' 
    select @sql = @sql + ' , max(case 課程 when ''' + 課程 + ''' then 分數 else 0 end) [' + 課程 + ']' 
    from (select distinct 課程 from tb) as a 
    set @sql = @sql + ' , cast(avg(分數*1.0) as decimal(18,2)) 平均分 , sum(分數) 總分 from tb group by 姓名' 
    exec(@sql) 
    ```
  * SQL SERVER 2005
    ```
    --靜態SQL
    select * from (select * from tb) a pivot (max(分數) for 課程 in (語文,數學,物理)) b 
    
    select m.* , n.平均分 , n.總分 
    from (select * from (select * from tb) a pivot (max(分數) for 課程 in (語文,數學,物理)) b) m, 
    (select 姓名 , cast(avg(分數*1.0) as decimal(18,2)) 平均分 , sum(分數) 總分 from tb group by 姓名) n 
    where m.姓名 = n.姓名  
    
    
    --動態SQL
    declare @sql varchar(8000) 
    select @sql = isnull(@sql + ',' , '') + 課程 from tb group by 課程 
    exec ('select * from (select * from tb) a pivot (max(分數) for 課程 in (' + @sql + ')) b') 
    
    declare @sql varchar(8000) 
    select @sql = isnull(@sql + ',' , '') + 課程 from tb group by 課程 
    exec ('select m.* , n.平均分 , n.總分 from 
    (select * from (select * from tb) a pivot (max(分數) for 課程 in (' + @sql + ')) b) m , 
    (select 姓名 , cast(avg(分數*1.0) as decimal(18,2)) 平均分 , sum(分數) 總分 from tb group by 姓名) n 
    where m.姓名 = n.姓名') 
    ```
  * Oracle：PIVOT(oracle11g)
    * PIVOT 內需有聚集函數
    * 將 course_type 列的欄位值轉換成列名
    ```
    SELECT class_name, student_name, chinese_result, math_result, created_date
    FROM (SELECT class_name, student_name, course_type, result, created_date
          FROM class_tmp_2) T
    PIVOT(sum(result)
      FOR course_type in ('語文' AS chinese_result, '數學' AS math_result)); 
    ```
* 行轉列 
  * Postgresql
    ```
    select * from test ;
       name
    -----------
     A,B,C,D,E
 
    select regexp_split_to_table(name,',') from test;
     regexp_split_to_table
    -----------------------
     A
     B
     C
     D
     E
    ```
  * SQL SERVER 2000
    ```
    create table tb(姓名 varchar(10) , 語文 int , 數學 int , 物理 int) 
    insert into tb values('張三',74,83,93) 
    insert into tb values('李四',74,84,94) 
    go 
    
    
    --靜態SQL
    select * from 
    ( 
    select 姓名 , 課程 = '語文' , 分數 = 語文 from tb 
    union all 
    select 姓名 , 課程 = '數學' , 分數 = 數學 from tb 
    union all 
    select 姓名 , 課程 = '物理' , 分數 = 物理 from tb 
    union all 
    select 姓名 as 姓名 , 課程 = '平均分' , 分數 = cast((語文 + 數學 + 物理)*1.0/3 as decimal(18,2)) from tb 
    union all 
    select 姓名 as 姓名 , 課程 = '總分' , 分數 = 語文 + 數學 + 物理 from tb 
    ) t 
    order by 姓名 , case 課程 when '語文' then 1 when '數學' then 2 when '物理' then 3 when '平均分' then 4 when '總分' then 5 end 
    
    
    --動態SQL
    --呼叫系統表動態生態。 
    declare @sql varchar(8000) 
    select @sql = isnull(@sql + ' union all ' , '' ) + ' select 姓名 , [課程] = ' + quotename(Name , '''') + ' , [分數] = ' + quotename(Name) + ' from tb' 
    from syscolumns 
    where name! = N'姓名' and ID = object_id('tb') --表名tb，不包含列名為姓名的其它列 
    order by colid asc 
    exec(@sql + ' order by 姓名 ') 
    ```
  * SQL SERVER 2005
    ```
    --動態SQL
    select 姓名 , 課程 , 分數 from tb unpivot (分數 for 課程 in([語文] , [數學] , [物理])) t 


    --動態SQL，同SQL SERVER 2000 動態SQL。
    ```
  * Oracle：UNPIVOT(oracle11g)
    ```
    SELECT class_name, student_name, course_type, result, created_date
    FROM class_tmp
    UNPIVOT(result for course_type in (chinese_result, math_result))
    ```
<br>

## 參考資料：
* [Crosstab Query](https://stackoverflow.com/questions/3002499/postgresql-crosstab-query)
* [Generate](https://www.postgresql.org/docs/9.1/functions-srf.html)
* [PostgreSQL 正體中文使用手冊](https://docs.postgresql.tw/)
* [CTE](https://dotblogs.com.tw/wasichris/2016/11/03/151251)
* [regex](https://dataschool.com/how-to-teach-people-sql/how-regex-works-in-sql/)
* [regexp](https://dev.mysql.com/doc/refman/8.0/en/regexp.html)
* [Array Functions and Operators](https://www.postgresql.org/docs/9.1/functions-array.html)
* [SQL 橫轉豎 、豎專橫](https://www.itread01.com/content/1542125548.html)
