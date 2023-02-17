# SQL

## SQL：結構化查詢語言(Structured Query Language)
* 類型：
  * Transact-SQL (又稱T-SQL)
    * 是在 Microsoft SQL Server 和 Sybase SQL Server 上的 ANSI SQL 實作
  * PL-SQL (Procedural Language-SQL)
    * 是甲骨文公司專有的 SQL 擴展語言
* 名詞：
  * DDL (Data Definition Language)：對資料庫物件 (如資料表，預存程序，函式或自訂型別等) 的新增、修改和刪除 (Create、Alter、Drop) 都使用此語法
  * DML (Data Manipulation Language)：又稱 CRUD (Create/Retrieve/Update/Delete) 功能，意指資料的新增、擷取、修改、刪除 (Select、Insert、Delete、Update) 四個功能
  * DCL (Data Control Language)：由資料庫所提供的保安功能，對於資料庫與資料庫物件的存取原則與權限 (Grant、Deny、Revoke)，都由 DCL 定義之
* 函式：
  <table border="1" width="40%">
    <tr>
        <th width="10%">函式</a>
        <th width="10%">描述</a>
        <th width="10%">函式</a>
        <th width="10%">描述</a>
    </tr>
    <tr>
        <td> AVG </td>
        <td> 平均值 </td>
        <td> COUNT </td>
        <td> 計數 (不含Null) </td>
    </tr>
    <tr>
        <td> FIRST </td>
        <td> 第一個記錄的值 </td>
        <td> SUM </td>
        <td> 求和 </td>
    </tr>
    <tr>
        <td> MAX </td>
        <td> 最大值 </td>
        <td> MIN </td>
        <td> 最小值 </td>
    </tr>
    <tr>
        <td> STDEV </td>
        <td> 樣本標準差 </td>
        <td> STDEVP </td>
        <td> 總體標準差 </td>
    </tr>
    <tr>
        <td> VAR </td>
        <td> 樣本方差 </td>
        <td> VARP </td>
        <td> 總體方差 </td>
    </tr>
    <tr>
        <td> UCASE </td>
        <td> 轉化為全大寫字母 </td>
        <td> LCASE </td>
        <td> 轉化為全小寫字母 </td>
    </tr>
    <tr>
        <td> MID </td>
        <td> 取中值 </td>
        <td> LEN </td>
        <td> 計算字串長度 </td>
    </tr>
    <tr>
        <td> INSTR </td>
        <td> 獲得子字串在母字串的起始位置 </td>
        <td> FORMAT </td>
        <td> 字串格式化 </td>
    </tr>
    <tr>
        <td> LEFT </td>
        <td> 取字串左邊子串 </td>
        <td> RIGHT </td>
        <td> 取字串右邊子串 </td>   
    </tr>
    <tr>
        <td> ROUND </td>
        <td> 數值四捨五入取整 </td>
        <td> MOD </td>
        <td> 取餘 </td>   
    </tr>
    <tr>
        <td> NOW </td>
        <td> 獲得當前時間的值 </td>
        <td> DATEDIFF </td>
        <td> 獲得兩個時間的差值 </td>   
    </tr>
  </table>
<br>


## SQL 效能調校
### Search Argument (SARG)
* 有效的查詢參數：`=`、`>`、`<`、`>=`、`<=`、`Between`、`Like`，如`like 'T%'`符合有效SARG，但`like '%T'`就不符合
* 非有效的查詢參數：`NOT`、`!=`、`<>`、`!<`、`!>`、`NOT EXISTS`、`NOT IN`、`NOT LIKE`
* 影響效能的寫法：
  * 條件式中轉換欄位類型
  * 條件式中對欄位做「運算」或「使用函數」 
  * select 撈取不必要的欄位
  * 負向查詢
  * 少用 union(去重複)，可使用 union all 替代
  * 少用子查詢
  * 用「exists/not exists」取代「in/not in」
  * 用「union/union all」取代「or」
  * 用「between」取代「in」連續數字
  * 需要查詢其他資料表資料時，使用「inner join」；不需要查詢其他資料表資料時，使用「exists/in」
  * 不須排序的資料就別用 order by
  * 善用 CTE(Common Table Expression) 改善效能
  * 避免執行不必要的查詢
  * 針對查詢條件欄位建立 Index
  * 「小表串大表」 優於 「大表串小表」

### 效能調校
* 資料庫設計與規劃
  * Primary Key 欄位的「長度儘量小」
    * 能用 small integer 就不要用 integer
    * 不要用 varchar 或 nvarchar，應該用 char 或 nchar
  * 文字資料欄位若長度固定，如：身分證字號，就不要用 varchar 或 nvarchar，應該用 char 或 nchar
  * 文字資料欄位若長度不固定，如：地址，則應該用 varchar 或 nvarchar
  * 設計欄位時，若其值可有可無，最好也給一個預設值，並設成「不允許 NULL」。因為 SQL Server 在存放和查詢有 NULL 的資料表時，會花費額外的運算動作
  * 若一個資料表的欄位過多，應「垂直切割」成兩個以上的資料表，並用同名的 Primary Key 一對多連結起來
* 適當地建立索引
  * 記得幫「Foreign Key」欄位建立索引，即使是很少被 JOIN 的資料表亦然
  * 替「常被查詢」或「排序」的欄位建立索引，如：常被當作 WHERE 子句條件的欄位
  * 用來建立索引的欄位，「長度不應過長」、「重複性應較低」，前者不要用超過 20 個位元組的欄位(如地址)，後者如姓名
  * 不宜替過多欄位建立索引，否則反而會影響到新增、修改、刪除的效能，尤其是以線上交易 (OLTP) 為主的網站資料庫
  * 若資料表存放的資料很「少」，就「不必刻意建立索引」，否則可能資料庫沿著索引樹狀結構去搜尋索引中的資料，反而比掃描整個資料表還慢
  * 若查詢時符合條件的資料很多，則透過「非叢集索引」搜尋的效能，可能反而不如整個資料表逐筆掃描
  * 建立「叢集索引」的欄位選擇至為重要，會影響到整個索引結構的效能。要用來建立「叢集索引」的欄位，務必選擇「整數」型別(鍵值會較小)、唯一、不可為 NULL
* 適當地使用索引
  * 以常數字元開頭才會使用到索引，若以萬用字元(%)開頭則不會使用索引
  * 執行完成後按 Ctrl+L，可檢視「執行計畫」，關注成本(CBO)高、statistics io 頻率高、statistics time 較長的高成本作業
  * 「負向查詢」(如NOT、!=、<>、!>、!<、NOT EXISTAS、NOT IN、NOT LIKE)常會讓「查詢最佳化程式」無法有效地使用索引，最好能用其他運算和語法改寫(非絕對)
  * 避免讓 WHERE 子句中的欄位，進行字串串接或數字運算(使用 function)，否則可能導致「查詢最佳化程式」無法直接使用索引，而改採叢集索引掃描(非絕對)
  * 資料表中的資料，會依照「叢集索引」欄位的順序存放，因此當您下 BETWEEN、GROUP BY、ORDER BY 時若有包含「叢集索引」欄位，由於資料已在資料表中排序好，因此可提升查詢速度
  * 若使用「複合索引」，要注意索引順序上的第一個欄位，才適合當作過濾條件
* 避免在 WHERE 子句中對欄位使用函數
  * 範例說明
    ```
    SELECT * FROM Orders WHERE DATEPART(yyyy, OrderDate) = 1996 AND DATEPART(mm, OrderDate)=7
    --可改成
    SELECT * FROM Orders WHERE OrderDate BETWEEN '19960701' AND '19960731'
    ```
    
    ```
    SELECT * FROM Orders WHERE SUBSTRING(CustomerID, 1, 1) = 'D'
    --可改成
    SELECT * FROM Orders WHERE CustomerID LIKE 'D%' 
    ```
* AND 與 OR 的使用
  * 在 AND 運算中，「只要有一個」條件有用到索引，即可大幅提升查詢速度
  * 在 OR 運算中，要「所有的」條件都有可用的索引，才能使用索引來提升查詢速度，可用 UNION 聯集適當地改善
* 適當地使用子查詢
  * 相較於「子查詢 (Subquery)」，較建議用 JOIN 完成的查詢，多數情況下，JOIN 的效能會比子查詢較佳
  * 子查詢類型
    * 獨立子查詢：查詢的內容可單獨執行
    * 關聯子查詢：無法單獨執行，即外層每一次查詢的動作都需要引用內層查詢的資料，或內層每一次查詢的動作都需要參考外層查詢的資料
  * ROW_NUMBER 函數加上「分群 (PARTITION BY)」等功能，執行效能極佳 
* 其他查詢技巧
  <table border="1" width="40%">
    <tr>
        <th width="5%">用途、技巧</a>
        <th width="5%">效能好的方式</a>
        <th width="10%">說明</a>
    </tr>
    <tr>
        <td> DELETE </td>
        <td> TRUNCATE </td>
        <td> 減少 rollback segments 產生可被恢復的信息 </td>
    </tr>
    <tr>
        <td> SELECT * </td>
        <td> SELECT 最小需求的資料列與欄位 </td>
        <td> ORACLE 在解析的過程中，會將「*」 依次轉換成所有的列名，這個工作是通過查詢資料字典完成的，這意味著將耗費更多的時間 </td>
    </tr>
    <tr>
        <td> DISTINCT </td>
        <td> 最高效的刪除重複記錄方法 
             ```
             DELETE FROM EMP E 
             WHERE E.ROWID > ( SELECT MIN(X.ROWID) 
                               FROM EMP X WHERE X.EMP_NO = E.EMP_NO) 
             ```
        </td>
        <td> DISTINCT、ORDER BY 語法，會讓資料庫做額外的計算 </td>
    </tr>
    <tr>
        <td> count(1) </td>
        <td> count(*) </td>
        <td> 可通過索引檢索，對索引列的計數仍舊是最快的，如 COUNT(EMPNO) </td>
    </tr>
    <tr>
        <td> UNION </td>
        <td> UNION ALL </td>
        <td> 聯集若沒有要剔除重複資料的需求，使用 UNION ALL 會比 UNION 更佳，因為後者會加入類似 DISTINCT 的演算法 </td>
    </tr>
    <tr>
        <td>  </td>
        <td> 使用 CTE 或者 EXISTS 的方式 </td>
        <td> 先縮小資料的範圍，避免大資料 JOIN 行為 </td>
    </tr>
    <tr>
        <td>  </td>
        <td> 最好明確指定該物件的「結構描述 (Schema)」 </td>
        <td> 在 SQL Server 2005 版本中，存取資料庫物件時，最好明確指定該物件的「結構描述 (Schema)」，也就是使用兩節式名稱 </td>
    </tr>
  </table>
 
* 儘可能用 Stored Procedure 取代前端應用程式直接存取資料表
  * Stored Procedure 除了經過事先編譯、效能較好以外，亦可節省 SQL 陳述式傳遞的頻寬，也方便商業邏輯的重複使用。再搭配自訂函數和 View 的使用，將來若要修改資料表結構、重新切割或反正規化時亦較方便
* 儘可能在資料來源層，就先過濾資料

### 影響效能主要因素
* 選擇「適當的索引」：索引是加速數據查詢的一種技術，適當地創建索引可以加速查詢。適當的索引通常包括主鍵、唯一索引以及常用查詢的列
* 「避免」全表掃描：如果查詢中沒有使用索引，則數據庫將進行全表掃描，將大大減慢查詢速度。因此，盡量避免使用不必要的子查詢、聯接和過濾條件
* 使用「限制」和分頁：如果結果集很大，則限制和分頁可以「減少返回的行數」，進而提高效能

<br>


## SQL 函數
### Crosstab Query
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

### Generate
* 範例：generate_series
  ```
  SELECT * FROM generate_series('2008-03-01 00:00'::timestamp,
                              '2008-03-04 12:00', '10 hours');
  ```

  ```
  SELECT current_date + s.a AS dates FROM generate_series(0,14,7) AS s(a);
  ```

### Array 
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

### Regex in My SQL
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

* SQL範例
  * RegExp_substr
    ```
    CONCAT('AA', RegExp_substr(NAME, '[0-9]{3,4}購物金'))
    →AA999購物金
    ```
  * E'(XX)|(YY)|(ZZ)'
    ```
    case when addr !~ E'(測試)|(體驗)|(NA$)'
              and length(addr)>9 then 'Y' 
         when length(addr)=9 and addr like '嘉義市%' then 'Y'
         when length(addr)=9 and addr like '新竹市%' then 'Y'
              else 'N'
    end::varchar(2) 
    → 不含「測試」、「體驗」、「NA」結束且長度大於9 標註 Y
      嘉義市、新竹市且長度等於9 標註 Y
    ```
    ```
    case when (addr ~ E'[市](\-)|(\--)' or cln_addr ~ E'[縣](\-)|(\--)' ) 
         then regexp_replace(addr, E'(\\-)|(\\--)', '') 
         else cln_addr 
    end
    ```
  * translate & replace
    ```
    translate(addr,'０１２３４５６７８９ＡＢＣＤＥＦＧＨＩＪＫＬＭＮＯＰＱＲＳＴＵＶＷＸＹＺ','0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ')
    → 全形轉換半形
    ```
  * 常用符號
    <table border="1" width="25%">
      <tr>
        <th width="5%">符號</a>
        <th width="5%">ASCII碼 [ˈæski]</a>
        <th width="5%">語法</a>
        <th width="10%">含意</a>
      </tr>
      <tr>
        <td> \t </td>
        <td> 9 </td>
        <td> CHR(9) </td>
        <td> Tab 空格鍵 </td>
      </tr>
      <tr>
        <td> \n </td>
        <td> 10 </td>
        <td> CHR(10) </td>
        <td> 換行鍵LF (Line Feed) </td>
      </tr>
      <tr>
        <td> \r </td>
        <td> 13 </td>
        <td> CHR(13) </td>
        <td> 回車CR (Carriage Return) </td>
      </tr>
      <tr>
        <td> " </td>
        <td> 34 </td>
        <td> CHR(34) </td>
        <td> Quotation mark ( " in HTML) </td>
      </tr>
      <tr>
        <td> & </td>
        <td> 38 </td>
        <td> CHR(38) </td>
        <td> Ampersand </td>
      </tr>
      <tr>
        <td> ` </td>
        <td> 39 </td>
        <td> CHR(39) </td>
        <td> Closing single quote (apostrophe) </td>
      </tr>
      <tr>
        <td> < </td>
        <td> 60 </td>
        <td> CHR(60) </td>
        <td> Less than sign (< in HTML) </td>
      </tr>
      <tr>
        <td> > </td>
        <td> 62 </td>
        <td> CHR(62) </td>
        <td> Greater than sign (> in HTML) </td>
      </tr>
    </table>

### 行列轉換
* 列轉行
  * Postgresql
    * string_agg(expression, delimiter)：直接把一個表達式變成字符串
    * array_agg(expression)：把表達式變成一個數組，一般配合 array_to_string() 使用
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
 
      ```
      create table jinbo.employee (empno smallint, ename varchar(20), job varchar(20), mgr smallint, hiredate date, sal bigint, comm bigint, deptno smallint);

      insert into jinbo.employee(empno,ename,job, mgr, hiredate, sal, comm, deptno) values (7499, 'ALLEN', 'SALEMAN', 7698, '2014-11-12', 16000, 300, 30);

      insert into jinbo.employee(empno,ename,job, mgr, hiredate, sal, comm, deptno) values (7499, 'ALLEN', 'SALEMAN', 7698, '2014-11-12', 16000, 300, 30);

      insert into jinbo.employee(empno,ename,job, mgr, hiredate, sal, comm, deptno) values (7654, 'MARTIN', 'SALEMAN', 7698, '2016-09-12', 12000, 1400, 30);

      select * from jinbo.employee;
       empno | ename  |   job   | mgr  |  hiredate  |  sal  | comm | deptno 
      -------+--------+---------+------+------------+-------+------+--------
        7499 | ALLEN  | SALEMAN | 7698 | 2014-11-12 | 16000 |  300 |     30
        7566 | JONES  | MANAGER | 7839 | 2015-12-12 | 32000 |    0 |     20
        7654 | MARTIN | SALEMAN | 7698 | 2016-09-12 | 12000 | 1400 |     30
      -----------------------------------
    
    
      --方法1
      select deptno, string_agg(ename, ',') 
      from jinbo.employee group by deptno;

      --方法2
      select deptno, array_to_string(array_agg(ename),',')
      from jinbo.employee group by deptno;
    
      --方法1和2的結果
       deptno |  string_agg  
      --------+--------------
           20 | JONES
           30 | ALLEN,MARTIN
        
        
      --方法3：按ename倒敘合併
      select deptno, string_agg(ename, ',' order by ename desc) 
      from jinbo.employee group by deptno;
    
      --方法3結果
       deptno |  string_agg  
      --------+--------------
           20 | JONES
           30 | MARTIN,ALLEN
         
         
      --方法4：按數組格式輸出       
      select deptno, array_agg(ename) 
      from jinbo.employee group by deptno;

      --方法4結果    
       deptno |   array_agg    
      --------+----------------
           20 | {JONES}
           30 | {ALLEN,MARTIN}


      --方法5-1：去重複元素
      select array_agg(distinct deptno) 
      from jinbo.employee;
    
      --方法5-1結果
      array_agg 
      -----------
       {20,30}

      --方法5-2：去重複元素，且排序
      select array_agg(distinct deptno order by deptno desc) 
      from jinbo.employee;
    
      --方法5-2結果
       array_agg 
      -----------
       {30,20}
      ˋˋˋ
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
    
    * SUTFF 函式的表達式是這樣的 STUFF(原字串, 起始位置, 移除長度, 替換字串)
    ```
    WITH SEND_MAIL AS (
     SELECT distinct UserName as rece_name
     FROM  [CIMS].[vw_MonthlyCalcUserInfo]
     WHERE MenuName like '佣獎%'
    )
    SELECT distinct STUFF((
                SELECT ',' + t1.rece_name
                FROM SEND_MAIL t1
                WHERE 1=1 
                FOR XML PATH('')), 1, LEN(','), '') AS FieldBs
    FROM SEND_MAIL t0;
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
    
      ```
      with temp as(  
        select '台灣' nation ,'台北'      city from dual union all  
        select '台灣' nation ,'台中'      city from dual union all  
        select '台灣' nation ,'高雄'      city from dual union all 
        select '日本' nation ,'北海道' city from dual union all  
        select '日本' nation ,'東京'   city from dual union all  
        select '日本' nation ,'大阪'   city from dual union all    
        select '美國' nation ,'華盛頓' city from dual union all  
        select '美國' nation ,'波士頓' city from dual union all  
        select '美國' nation ,'紐約'   city from dual   
      )  
      select nation,listagg(city,'+') within GROUP (order by city) as city
      from temp  
      group by nation
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


## Temp Table
* Temp Table 說明
  * MS SQL
    * #temp
      * 儲存於 tempdb 中
      * 只可在建立的 Session 中使用
      * 不會自動刪除，自動刪除時機為 DB Service 
      * Debug 的好幫手 
      * 建立方式一：直接 create table [table_name] 
      * 建立方式二：select * into [table_name] from [資料表] where [條件]
    * ##temp
      * 儲存於 tempdb 中 <br>
      * 可跨 Session 使用，屬於全域變數 <br>
      * 不會自動刪除，自動刪除時機為 DB Service <br>
      * 建立方式一：直接 create table [table_name] <br>
      * 建立方式二：select * into [table_name] from [資料表] where [條件]
    * @temp
      * 儲存於記憶體中 <br>
      * 只可在建立的 Session 中使用 <br>
      * 當下執行完畢即自動刪除 <br>
      * 建立方式：declare [table_name] table
    * CTE
      * 儲存於記憶體中 <br>
      * 只可在建立的 Session 中使用 <br>
      * 當下執行完畢即自動刪除 <br>
      * 建立方式：with [table-name] as <br>
      * 適合遞迴處理
  * Oracle
    * 語法
      > CREATE GLOBAL TEMPORARY TABLE [table_name] ( 
      >            [col_name1]  [data_type1]            
      >          , [col_name2]  [data_type2]       
      >          , ...                             
      >       ) ON COMMIT [Delete/Preserve] ROWS;
      
    * 類型
      * 事務級 delete      
        * Commit後，資料被刪除    
        * 不管有無 Commit，都可以直接 Drop Table  
        * Session 結束時，資料會自動刪除      
      * 會話級 preserve         
        * Commit後，資料仍保留    
        * 不管有無 Commit，若要 Drop Table 前，一定要先 Truncate Table 才可以 
        * Session 結束時，資料會自動刪除      
    * 說明
      * default：on commit delete rows
      * 所有 Session 都可見
        > select userenv('sid') from dual;
        
  * Posgret
    * 語法
      * 會話級 
        > CREATE TEMPORARY TABLE [table_name] ( 
        >           [col_name1]  [data_type1]              
        >         , [col_name2]  [data_type2]        
        >         , ...                             
        >      );         
        
      * 事務級                                
        > CREATE TEMPORARY TABLE [table_name] ( 
        >           [col_name1]  [data_type1]               
        >         , [col_name2]  [data_type2]        
        >         , ...                              
        >      ) on commit [delete/preserve/drop] rows;  
        
        * delete：結束後 truncate 資料
        * preserve：結束後保留資料
        * drop：結束後刪除 table  
    * 說明
      * 只有 Session 可見，不同 Session 可建立相同的暫存表(同結構/不同結構)
      * Session 結束後臨時表也刪除
        > select PG_BACKEND_PID();
    
* Temp Table 比較
  <table border="1" width="12%">
      <tr>
        <th width="2%"> 差異 </a>
        <th width="5%"> #temp </a>
        <th width="5%"> @temp  </a>
      </tr>
      <tr>
        <td> 儲存 </td>
        <td> tempdb </td>
        <td> 記憶體 </td>
      </tr>
      <tr>
        <td> 效能 </td>
        <td> 需花費I/O </td>
        <td> 較好 </td>
      </tr>
      <tr>
        <td> 建立 Index </td>
        <td> Y </td>
        <td> N </td>
      </tr>
      <tr>
        <td> 自動 drop table </td>
        <td> N </td>
        <td> Y </td>
      </tr>
      <tr>
        <td> 適用場景 </td>
        <td> 資料量大 </td>
        <td> 資料量小 </td>
      </tr>
      <tr>
        <td> Debug </td>
        <td> 容易 </td>
        <td> 困難 </td>
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
* [SQL 橫轉豎 、豎專橫](https://www.itread01.com/content/1542125548.html)
* [SQL Sever 直式轉成橫式](https://dotblogs.com.tw/nick0415/2018/05/22/133538)
* [PostgreSql 聚合函数string_agg与array_agg](https://blog.csdn.net/u011944141/article/details/78902678)
* [RegEx101](https://regex101.com/)
* [ASCII 控制字符](http://www.eion.com.tw/Blogger/?Pid=1128)
* [SQL 效能調校](https://sweeteason.pixnet.net/blog/post/36954495-sql-%E6%95%88%E8%83%BD%E8%AA%BF%E6%A0%A1)
