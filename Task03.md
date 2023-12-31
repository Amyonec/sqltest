# 本笔记为阿里云天池龙珠计划SQL训练营的学习内容，链接为：https://tianchi.aliyun.com/specials/promotion/aicampsql；
# Task03：复杂查询方法-视图、子查询、函数等
# 3.1 视图
- 3.1.1 什么是视图
- 视图是一个虚拟的表，不同于直接操作数据表，视图是依据SELECT语句来创建的（会在下面具体介绍），所以操作视图时会根据创建视图的SELECT语句生成一张虚拟表，然后在这张虚拟表上做SQL操作。
- 3.1.2 视图与表有什么区别
- 用一句话非常凝练的概括了视图与表的区别—“是否保存了实际的数据”。所以视图并不是数据库真实存储的数据表，它可以看作是一个窗口，通过这个窗口我们可以看到数据库表中真实存在的数据。所以我们要区别视图和数据表的本质，即视图是基于真实表的一张虚拟的表，其数据来源均建立在真实表的基础上。
- 3.1.3 为什么会存在视图
- 那既然已经有数据表了，为什么还需要视图呢？主要有以下几点原因：
- 通过定义视图可以将频繁使用的SELECT语句保存以提高效率。
- 通过定义视图可以使用户看到的数据更加清晰。
- 通过定义视图可以不对外公开数据表全部字段，增强数据的保密性。
- 通过定义视图可以降低数据的冗余。
- 3.1.4 如何创建视图
- CREATE VIEW <视图名称>(<列名1>,<列名2>,...) AS <SELECT语句>
- 其中SELECT 语句需要书写在 AS 关键字之后。 SELECT 语句中列的排列顺序和视图中列的排列顺序相同， SELECT 语句中的第 1 列就是视图中的第 1 列， SELECT 语句中的第 2 列就是视图中的第 2 列，以此类推。而且视图的列名是在视图名称之后的列表中定义的。
- 需要注意的是视图名在数据库中需要是唯一的，不能与其他视图和表重名。
- 视图不仅可以基于真实表，我们也可以在视图的基础上继续创建视图。
- 虽然在视图上继续创建视图的语法没有错误，但是我们还是应该尽量避免这种操作。这是因为对多数 DBMS 来说， 多重视图会降低 SQL 的性能。
- 需要注意的是在一般的DBMS中定义视图时不能使用ORDER BY语句。
- 为了学习多表视图，我们在product表和shop_product表的基础上创建视图。
- CREATE VIEW view_shop_product(product_type, sale_price, shop_name)
- AS
- SELECT product_type, sale_price, shop_name
-  FROM product,
-       shop_product
- WHERE product.product_id = shop_product.product_id;
- 我们可以在这个视图的基础上进行查询
- SELECT sale_price, shop_name
-  FROM view_shop_product
- WHERE product_type = '衣服';
- 3.1.5 如何修改视图结构
- ALTER VIEW <视图名> AS <SELECT语句>
- 其中视图名在数据库中需要是唯一的，不能与其他视图和表重名。
- 当然也可以通过将当前视图删除然后重新创建的方式达到修改的效果。
- 我们修改上方的productSum视图
- ALTER VIEW productSum
-    AS
-        SELECT product_type, sale_price
-          FROM Product
-         WHERE regist_date > '2009-09-11';
- 3.1.6 如何更新视图内容
- 因为视图是一个虚拟表，所以对视图的操作就是对底层基础表的操作，所以在修改时只有满足底层基本表的定义才能成功修改。
- 对于一个视图来说，如果包含以下结构的任意一种都是不可以被更新的：
- 聚合函数 SUM()、MIN()、MAX()、COUNT() 等。
- DISTINCT 关键字。
- GROUP BY 子句。
- HAVING 子句。
- UNION 或 UNION ALL 运算符。
- FROM 子句中包含多个表。
- 视图归根结底还是从表派生出来的，因此，如果原表可以更新，那么 视图中的数据也可以更新。反之亦然，如果视图发生了改变，而原表没有进行相应更新的话，就无法保证数据的一致性了。
- 因为我们刚刚修改的productSum视图不包括以上的限制条件，我们来尝试更新一下视图
- UPDATE productsum
-   SET sale_price = '5000'
- WHERE product_type = '办公用品';
- 刚才修改视图的时候是设置product_type='办公用品'的商品的sale_price=5000，为什么原表的数据只有一条做了修改呢？
- 还是因为视图的定义，视图只是原表的一个窗口，所以它修改也只能修改透过窗口能看到的内容。
- 注意：这里虽然修改成功了，但是并不推荐这种使用方式。而且我们在创建视图时也尽量使用限制不允许通过视图来修改表
- 3.1.7 如何删除视图
- DROP VIEW <视图名1> [ , <视图名2> …]

# 3.2 子查询
- SELECT stu_name
- FROM (
-         SELECT stu_name, COUNT(*) AS stu_cnt
-          FROM students_info
-          GROUP BY stu_age)
-      AS studentSum;
- 3.2.1 什么是子查询
- 子查询指一个查询语句嵌套在另一个查询语句内部的查询，这个特性从 MySQL 4.1 开始引入，在 SELECT 子句中先计算子查询，子查询结果作为外层另一个查询的过滤条件，查询可以基于一个表或者多个表。
- 3.2.2 子查询和视图的关系
- 子查询就是将用来定义视图的 SELECT 语句直接用于 FROM 子句当中。其中AS studentSum可以看作是子查询的名称，而且由于子查询是一次性的，所以子查询不会像视图那样保存在存储介质中， 而是在 SELECT 语句执行之后就消失了。
- 3.2.3 嵌套子查询
- 与在视图上再定义视图类似，子查询也没有具体的限制，例如我们可以这样
- SELECT product_type, cnt_product
- FROM (SELECT *
-        FROM (SELECT product_type, 
-                      COUNT(*) AS cnt_product
-                FROM product 
-               GROUP BY product_type) AS productsum
-       WHERE cnt_product = 4) AS productsum2;
-   其中最内层的子查询我们将其命名为productSum，这条语句根据product_type分组并查询个数，第二层查询中将个数为4的商品查询出来，最外层查询product_type和cnt_product两列。
- 虽然嵌套子查询可以查询出结果，但是随着子查询嵌套的层数的叠加，SQL语句不仅会难以理解而且执行效率也会很差，所以要尽量避免这样的使用。
- 3.2.4 标量子查询
- 标量就是单一的意思，那么标量子查询也就是单一的子查询，那什么叫做单一的子查询呢？
- 所谓单一就是要求我们执行的SQL语句只能返回一个值，也就是要返回表中具体的某一行的某一列。例如我们有下面这样一张表
- 那么我们执行一次标量子查询后是要返回类似于，“0004”，“菜刀”这样的结果。
- 3.2.5 标量子查询有什么用
- 我们现在已经知道标量子查询可以返回一个值了，那么它有什么作用呢？
- 直接这样想可能会有些困难，让我们看几个具体的需求：
- 1.查询出销售单价高于平均销售单价的商品
- 2.查询出注册日期最晚的那个商品
- 3.2.6 关联子查询
- 关联子查询既然包含关联两个字那么一定意味着查询与子查询之间存在着联系。这种联系是如何建立起来的呢？
- SELECT product_type, product_name, sale_price
-  FROM product AS p1
- WHERE sale_price > (SELECT AVG(sale_price)
-                       FROM product AS p2
-                      WHERE p1.product_type = p2.product_type
-   GROUP BY product_type);
-   关联子查询中我们将外面的product表标记为p1，将内部的product设置为p2，而且通过WHERE语句连接了两个查询。
- 小结
- 视图和子查询是数据库操作中较为基础的内容，对于一些复杂的查询需要使用子查询加一些条件语句组合才能得到正确的结果。但是无论如何对于一个SQL语句来说都不应该设计的层数非常深且特别复杂，不仅可读性差而且执行效率也难以保证，所以尽量有简洁的语句来完成需要的功能。
# 练习题-第一部分
- 3.1创建出满足下述三个条件的视图（视图名称为 ViewPractice5_1）。使用 product（商品）表作为参照表，假设表中包含初始状态的 8 行数据。
- 条件 1：销售单价大于等于 1000 日元。
- 条件 2：登记日期是 2009 年 9 月 20 日。
- 条件 3：包含商品名称、销售单价和登记日期三列。
- ALTER VIEW ViewPractice5_1
-    AS
-    SELECT product_name,sale_price,regist_date
-    FROM product
-    WHERE sale_price>=1000 and regist_date = '2009-9-20';
- 3.2 向习题一中创建的视图 ViewPractice5_1 中插入如下数据，会得到什么样的结果呢？
- INSERT INTO ViewPractice5_1 VALUES (' 刀子 ', 300, '2009-11-02');
- 结果会报错，因为不满足原始表格非空条件
- 3.3请根据如下结果编写 SELECT 语句，其中 sale_price_all 列为全部商品的平均销售单价。
- SELECT product_id,product_name,product_type,sale_price,(SELECT AVG(sale_price)
          FROM product) AS avg_sale_price
- FROM product
- 3.4根据习题一中的条件编写一条 SQL 语句，创建一幅包含如下数据的视图（名称为AvgPriceByType）。
- ALTER VIEW AvgPriceByType
-    AS
-    SELECT product_id,product_name,product_type,sale_price,(SELECT AVG(sale_price) FROM product P2 WHERE P1.product_type =P2.product_type GROUP BY P1.product_type) AS avg_sale_price
-    FROM product P1
# 3.3 各种各样的函数
- 3.3.1 算数函数
- (+ - * /四则运算)
- ABS – 绝对值 ABS( 数值 ) ABS 函数用于计算一个数字的绝对值，表示一个数到原点的距离。当 ABS 函数的参数为NULL时，返回值也是NULL。
- MOD – 求余数 MOD( 被除数，除数 ) MOD 是计算除法余数（求余）的函数，是 modulo 的缩写。小数没有余数的概念，只能对整数列求余数。注意：主流的 DBMS 都支持 MOD 函数，只有SQL Server 不支持该函数，其使用%符号来计算余数。
- ROUND – 四舍五入 ROUND( 对象数值，保留小数的位数 ) ROUND 函数用来进行四舍五入操作。注意：当参数 保留小数的位数 为变量时，可能会遇到错误，请谨慎使用变量。
- 3.3.2 字符串函数
- CONCAT – 拼接 CONCAT(str1, str2, str3) MySQL中使用 CONCAT 函数进行拼接。
- LENGTH – 字符串长度 LENGTH( 字符串 )
- LOWER – 小写转换 LOWER 函数只能针对英文字母使用，它会将参数中的字符串全都转换为小写。该函数不适用于英文字母以外的场合，不影响原本就是小写的字符。
- 类似的， UPPER 函数用于大写转换。
- REPLACE – 字符串的替换 REPLACE( 对象字符串，替换前的字符串，替换后的字符串 )
- SUBSTRING – 字符串的截取 SUBSTRING （对象字符串 FROM 截取的起始位置 FOR 截取的字符数） 使用 SUBSTRING 函数 可以截取出字符串中的一部分字符串。截取的起始位置从字符串最左侧开始计算，索引值起始为1。
- （扩展内容）SUBSTRING_INDEX – 字符串按索引截取 SUBSTRING_INDEX (原始字符串， 分隔符，n)  该函数用来获取原始字符串按照分隔符分割后，第 n 个分隔符之前（或之后）的子字符串，支持正向和反向索引，索引起始值分别为 1 和 -1。
- 获取第1个元素比较容易，获取第2个元素/第n个元素可以采用二次拆分的写法。SELECT SUBSTRING_INDEX(SUBSTRING_INDEX('www.mysql.com', '.', 2), '.', -1);
- 3.3.3 日期函数
- 不同DBMS的日期函数语法各有不同，本课程介绍一些被标准 SQL 承认的可以应用于绝大多数 DBMS 的函数。特定DBMS的日期函数查阅文档即可。
- CURRENT_DATE – 获取当前日期 SELECT CURRENT_DATE;
- CURRENT_TIME – 当前时间 SELECT CURRENT_TIME;
- CURRENT_TIMESTAMP – 当前日期和时间 SELECT CURRENT_TIMESTAMP;
- EXTRACT – 截取日期元素 EXTRACT(日期元素 FROM 日期) 使用 EXTRACT 函数可以截取出日期数据中的一部分，例如“年”“月”，或者“小时”“秒”等。该函数的返回值并不是日期类型而是数值类型
- 3.3.4 转换函数
- “转换”这个词的含义非常广泛，在 SQL 中主要有两层意思：一是数据类型的转换，简称为类型转换，在英语中称为cast；另一层意思是值的转换。
- CAST – 类型转换 CAST（转换前的值 AS 想要转换的数据类型）
- COALESCE – 将NULL转换为其他值 COALESCE(数据1，数据2，数据3……) COALESCE 是 SQL 特有的函数。该函数会返回可变参数 A 中左侧开始第 1个不是NULL的值。参数个数是可变的，因此可以根据需要无限增加。在 SQL 语句中将 NULL 转换为其他值时就会用到转换函数。
# 3.4 谓词
- 3.4.1 什么是谓词
- 谓词就是返回值为真值的函数。包括TRUE / FALSE / UNKNOWN。
- 谓词主要有以下几个：
- LIKE
- BETWEEN
- IS NULL、IS NOT NULL
- IN
- EXISTS
- 3.4.2 LIKE谓词 – 用于字符串的部分一致查询
- 当需要进行字符串的部分一致查询时需要使用该谓词。
- 部分一致大体可以分为前方一致、中间一致和后方一致三种类型。
- 前方一致即作为查询条件的字符串（这里是“ddd”）与查询对象字符串起始部分相同。
- SELECT *
- FROM samplelike
- WHERE strcol LIKE 'ddd%';
- 其中的%是代表“零个或多个任意字符串”的特殊符号，本例中代表“以ddd开头的所有字符串”。
- 中间一致即查询对象字符串中含有作为查询条件的字符串，无论该字符串出现在对象字符串的最后还是中间都没有关系。
- SELECT *
- FROM samplelike
- WHERE strcol LIKE '%ddd%';
- 后方一致即作为查询条件的字符串（这里是“ddd”）与查询对象字符串的末尾部分相同
- SELECT *
- FROM samplelike
- WHERE strcol LIKE '%ddd';
- 综合如上三种类型的查询可以看出，查询条件最宽松，也就是能够取得最多记录的是中间一致。这是因为它同时包含前方一致和后方一致的查询结果。
- _下划线匹配任意 1 个字符
- 使用 _（下划线）来代替 %，与 % 不同的是，它代表了“任意 1 个字符”。
- 3.4.3 BETWEEN谓词 – 用于范围查询
- 使用 BETWEEN 可以进行范围查询。该谓词与其他谓词或者函数的不同之处在于它使用了 3 个参数。
- -- 选取销售单价为100～ 1000元的商品
- SELECT product_name, sale_price
- FROM product
- WHERE sale_price BETWEEN 100 AND 1000;
- BETWEEN 的特点就是结果中会包含 100 和 1000 这两个临界值，也就是闭区间。如果不想让结果中包含临界值，那就必须使用 < 和 >。
- SELECT product_name, sale_price
- FROM product
- WHERE sale_price > 100
- AND sale_price < 1000;
- 3.4.4 IS NULL、 IS NOT NULL – 用于判断是否为NULL
- 为了选取出某些值为 NULL 的列的数据，不能使用 =，而只能使用特定的谓词IS NULL。
- 与此相反，想要选取 NULL 以外的数据时，需要使用IS NOT NULL。
- 3.4.5 IN谓词 – OR的简便用法
- - 多个查询条件取并集时可以选择使用or语句。
- -- 通过OR指定多个进货单价进行查询
- SELECT product_name, purchase_price
- FROM product
- WHERE purchase_price = 320
- OR purchase_price = 500
- OR purchase_price = 5000;
- 虽然上述方法没有问题，但还是存在一点不足之处，那就是随着希望选取的对象越来越多， SQL 语句也会越来越长，阅读起来也会越来越困难。这时， 我们就可以使用IN 谓词
`IN(值1, 值2, 值3, …)来替换上述 SQL 语句。
- SELECT product_name, purchase_price
- FROM product
- WHERE purchase_price IN (320, 500, 5000);
- 反之，希望选取出“进货单价不是 320 元、 500 元、 5000 元”的商品时，可以使用否定形式NOT IN来实现
- 需要注意的是，在使用IN 和 NOT IN 时是无法选取出NULL数据的。
- 实际结果也是如此，上述两组结果中都不包含进货单价为 NULL 的叉子和圆珠笔。 NULL 只能使用 IS NULL 和 IS NOT NULL 来进行判断。
- 3.4.6 使用子查询作为IN谓词的参数
- IN和子查询 IN 谓词（NOT IN 谓词）具有其他谓词所没有的用法，那就是可以使用子查询作为其参数。我们已经在 5-2 节中学习过了，子查询就是 SQL内部生成的表，因此也可以说“能够将表作为 IN 的参数”。同理，我们还可以说“能够将视图作为 IN 的参数”
- 或者，你会疑惑既然 in 谓词也能实现，那为什么还要使用子查询呢？这里给出两点原因：
- ①：实际生活中，某个门店的在售商品是不断变化的，使用 in 谓词就需要经常更新 sql 语句，降低了效率，提高了维护成本；
- ②：实际上，某个门店的在售商品可能有成百上千个，手工维护在售商品编号真是个大工程。
- 使用子查询即可保持 sql 语句不变，极大提高了程序的可维护性，这是系统开发中需要重点考虑的内容。
- 3.4.7 EXIST 谓词
- EXIST 谓词的用法理解起来有些难度。
- ① EXIST 的使用方法与之前的都不相同
- ② 语法理解起来比较困难
- ③ 实际上即使不使用 EXIST，基本上也都可以使用 IN（或者 NOT IN）来代替
- 这么说的话，还有学习 EXIST 谓词的必要吗？答案是肯定的，因为一旦能够熟练使用 EXIST 谓词，就能体会到它极大的便利性。
- EXIST谓词的使用方法
- 谓词的作用就是 “判断是否存在满足某种条件的记录”。
- 如果存在这样的记录就返回真（TRUE），如果不存在就返回假（FALSE）。
- EXIST（存在）谓词的主语是“记录”。
- SELECT product_name, sale_price
-  FROM product AS p
-  WHERE EXISTS (SELECT *
-                 FROM shopproduct AS sp
-                WHERE sp.shop_id = '000C'
-                  AND sp.product_id = p.product_id);
- EXIST的参数
- 之前我们学过的谓词，基本上都是像“列 LIKE 字符串”或者“ 列 BETWEEN 值 1 AND 值 2”这样需要指定 2 个以上的参数，而 EXIST 的左侧并没有任何参数。因为 EXIST 是只有 1 个参数的谓词。 所以，EXIST 只需要在右侧书写 1 个参数，该参数通常都会是一个子查询。
- 子查询中的SELECT *
- 由于 EXIST 只关心记录是否存在，因此返回哪些列都没有关系。 EXIST 只会判断是否存在满足子查询中 WHERE 子句指定的条件“商店编号（shop_id）为 '000C'，商品（product）表和商店商品（shopproduct）表中商品编号（product_id）相同”的记录，只有存在这样的记录时才返回真（TRUE）。
- 大家可以把在 EXIST 的子查询中书写 SELECT * 当作 SQL 的一种习惯。
- 使用NOT EXIST替换NOT IN
- 就像 EXIST 可以用来替换 IN 一样， NOT IN 也可以用NOT EXIST来替换。
- NOT EXIST 与 EXIST 相反，当“不存在”满足子查询中指定条件的记录时返回真（TRUE）。           
# 3.5 CASE 表达式
- 3.5.1 什么是 CASE 表达式？
- CASE 表达式是函数的一种。是 SQL 中数一数二的重要功能，有必要好好学习一下。
- CASE 表达式是在区分情况时使用的，这种情况的区分在编程中通常称为（条件）分支。
- CASE表达式的语法分为简单CASE表达式和搜索CASE表达式两种。由于搜索CASE表达式包含简单CASE表达式的全部功能。
- CASE WHEN <求值表达式> THEN <表达式>
-     WHEN <求值表达式> THEN <表达式>
-     WHEN <求值表达式> THEN <表达式>
-     .
-     .
-     .
- ELSE <表达式>
- END
- 上述语句执行时，依次判断 when 表达式是否为真值，是则执行 THEN 后的语句，如果所有的 when 表达式均为假，则执行 ELSE 后的语句。
- 无论多么庞大的 CASE 表达式，最后也只会返回一个值。
- 3.5.2 CASE表达式的使用方法
- 假设现在 要实现如下结果：
- A ：衣服
- B ：办公用品
- C ：厨房用具
- 因为表中的记录并不包含“A ： ”或者“B ： ”这样的字符串，所以需要在 SQL 中进行添加。并将“A ： ”“B ： ”“C ： ”与记录结合起来。
- 应用场景1：根据不同分支得到不同列值
- SELECT  product_name,
-        CASE WHEN product_type = '衣服' THEN CONCAT('A ： ',product_type)
-             WHEN product_type = '办公用品'  THEN CONCAT('B ： ',product_type)
-             WHEN product_type = '厨房用具'  THEN CONCAT('C ： ',product_type)
-             ELSE NULL
-        END AS abc_product_type
-  FROM  product;
-  ELSE 子句也可以省略不写，这时会被默认为 ELSE NULL。但为了防止有人漏读，还是希望大家能够显示地写出 ELSE 子句。
- 此外， CASE 表达式最后的“END”是不能省略的，请大家特别注意不要遗漏。忘记书写 END 会发生语法错误，这也是初学时最容易犯的错误。
- 应用场景2：实现列方向上的聚合
- 通常我们使用如下代码实现行的方向上不同种类的聚合（这里是 sum）
- SELECT product_type,
-       SUM(sale_price) AS sum_price
-  FROM product
- GROUP BY product_type;
- 假如要在列的方向上展示不同种类额聚合值，该如何写呢？聚合函数 + CASE WHEN 表达式即可实现该效果
- -- 对按照商品种类计算出的销售单价合计值进行行列转换
- SELECT SUM(CASE WHEN product_type = '衣服' THEN sale_price ELSE 0 END) AS sum_price_clothes,
-       SUM(CASE WHEN product_type = '厨房用具' THEN sale_price ELSE 0 END) AS sum_price_kitchen,
-       SUM(CASE WHEN product_type = '办公用品' THEN sale_price ELSE 0 END) AS sum_price_office
-  FROM product;
-  （扩展内容）应用场景3：实现行转列
-  -- CASE WHEN 实现数字列 score 行转列
- SELECT name,
-       SUM(CASE WHEN subject = '语文' THEN score ELSE null END) as chinese,
-       SUM(CASE WHEN subject = '数学' THEN score ELSE null END) as math,
-       SUM(CASE WHEN subject = '外语' THEN score ELSE null END) as english
-  FROM score
- GROUP BY name;
- 总结：
- 当待转换列为数字时，可以使用SUM AVG MAX MIN等聚合函数；
- 当待转换列为文本时，可以使用MAX MIN等聚合函数
# 练习题-第二部分
- 3.5 运算或者函数中含有 NULL 时，结果全都会变为NULL ？（判断题）
- 是的，运算或者函数中含有 NULL 时，结果全都会变为NULL
- 3.6 对本章中使用的 product（商品）表执行如下 2 条 SELECT 语句，能够得到什么样的结果呢？
- SELECT product_name, purchase_price
-  FROM product
- WHERE purchase_price NOT IN (500, 2800, 5000);
- purchase_price列中不含(500, 2800, 5000）这几个数据的值,谓语无法与NULL进行比较
- SELECT product_name, purchase_price
-  FROM product
- WHERE purchase_price NOT IN (500, 2800, 5000, NULL);
- 返回0条记录，因为NOT IN的参数不能包含NULL，否则结果为NULL
- 3.7按照销售单价（ sale_price）对练习 6.1 中的 product（商品）表中的商品进行如下分类。
- 低档商品：销售单价在1000日元以下（T恤衫、办公用品、叉子、擦菜板、 圆珠笔）
- 中档商品：销售单价在1001日元以上3000日元以下（菜刀）
- 高档商品：销售单价在3001日元以上（运动T恤、高压锅）
- 请编写出统计上述商品种类中所包含的商品数量的 SELECT 语句，结果如下所示。
- SELECT
- SUM(CASE WHEN sale_price <=1000 THEN 1 ELSE 0 END) AS low_price
- SUM(CASE WHEN sale_price >1000 AND sale_price <=3000 HEN 1 ELSE 0 END) AS mid_price
- SUM(CASE WHEN sale_price >3000 HEN 1 ELSE 0 END) AS high_price
- FROM product;


