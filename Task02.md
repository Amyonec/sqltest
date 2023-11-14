
# Task02：SQL基础查询与排序
# 一、SELECT语句基础
- 1.1 从表中选取数据
- SELECT <列名>,
-  FROM <表名>;
- 1.2从表中选取符合条件的数据
- SELECT <列名>, ……
-  FROM <表名>
-  WHERE <条件表达式>;
- 1.3 相关法则
- -- 想要查询出全部列时，可以使用代表所有列的星号（*）。
- SELECT *
-   FROM <表名>;
- -- SQL语句可以使用AS关键字为列设定别名（用中文时需要双引号（“”））。
- SELECT product_id     AS id,
-       product_name   AS name,
-       purchase_price AS "进货单价"
-  FROM product;
- -- 使用DISTINCT删除product_type列中重复的数据
- SELECT DISTINCT product_type
-  FROM product;
# 二、算术运算符和比较运算符
- 2.1 算术运算符
- 2.2 比较运算符
- 2.3 常用法则
# 三、逻辑运算符
- 3.1 NOT运算符
- 3.2 AND运算符和OR运算符
- 3.3 通过括号优先处理
- 3.4 真值表
- 3.5 含有NULL时的真值
- 练习题-第一部分
- 练习题1
- 练习题2
- 练习题3
- 练习题4
# 四、对表进行聚合查询
- 4.1 聚合函数
- 4.2 使用聚合函数删除重复值
- 4.3 常用法则
# 五、对表进行分组
- 5.1 GROUP BY语句
- 5.2 聚合键中包含NULL时
- 5.3 GROUP BY书写位置
- 5.4 在WHERE子句中使用GROUP BY
- 5.5 常见错误
# 六、为聚合结果指定条件
- 6.1 用HAVING得到特定分组
- 6.2 HAVING特点
# 七、对查询结果进行排序
- 7.1 ORDER BY
- 7.2 ORDER BY中列名可使用别名
- 练习题-第二部分
- 练习题5
- 练习题6
- 练习题7
