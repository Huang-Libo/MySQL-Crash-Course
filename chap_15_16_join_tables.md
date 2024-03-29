# chap 15~16. 联结表

## 内联结（等值联结）

显示商品表中的每个商品对应的供应商名称：  

```sql
SELECT vend_name, prod_name, prod_price
FROM vendors, products
WHERE vendors.vend_id = products.vend_id
ORDER BY vend_name, prod_name;
```

查询结果为：  

```
+-------------+----------------+------------+
| vend_name   | prod_name      | prod_price |
+-------------+----------------+------------+
| ACME        | Bird seed      |      10.00 |
| ACME        | Carrots        |       2.50 |
| ACME        | Detonator      |      13.00 |
| ACME        | Safe           |      50.00 |
| ACME        | Sling          |       4.49 |
| ACME        | TNT (1 stick)  |       2.50 |
| ACME        | TNT (5 sticks) |      10.00 |
| Anvils R Us | .5 ton anvil   |       5.99 |
| Anvils R Us | 1 ton anvil    |       9.99 |
| Anvils R Us | 2 ton anvil    |      14.99 |
| Jet Set     | JetPack 1000   |      35.00 |
| Jet Set     | JetPack 2000   |      55.00 |
| LT Supplies | Fuses          |       3.42 |
| LT Supplies | Oil can        |       8.99 |
+-------------+----------------+------------+
```

可用更明确的语法显示其为内联结：

```sql
SELECT vend_name, prod_name, prod_price
FROM vendors INNER JOIN products
ON vendors.vend_id = products.vend_id
ORDER BY vend_name, prod_name;
```

说明：`ANSI SQL` 推荐使用 `INNER JOIN` 语法。可确保不会忘记联结条件，有时候这样做也能影响性能。  

## 自联结

#### 表别名

使用表别名的作用：

1. 能缩短 SQL 语句；
2. 使得在单条 SQL 语句中可多次使用相同的表。

#### 在同一个查询中多次使用相同的表：子查询

查询某供应商生产的其他商品，如果使用子查询：  

```sql
SELECT prod_id, prod_name
FROM products
WHERE vend_id = (SELECT vend_id
                 FROM products
                 WHERE prod_id = 'DTNTR');
```

查询结果为：  

```
+---------+----------------+
| prod_id | prod_name      |
+---------+----------------+
| DTNTR   | Detonator      |
| FB      | Bird seed      |
| FC      | Carrots        |
| SAFE    | Safe           |
| SLING   | Sling          |
| TNT1    | TNT (1 stick)  |
| TNT2    | TNT (5 sticks) |
+---------+----------------+
```

#### 在同一个查询中多次使用相同的表：自联结

使用自联结时，需要用到表别名：  

```sql
SELECT p1.prod_id, p1.prod_name
FROM products AS p1, products AS p2
WHERE p1.vend_id = p2.vend_id
AND p2.prod_id = 'DTNTR';
```

查询出的结果与使用**子查询**的一致。  

#### 使用自联结还是子查询

自联结通常作为外部语句用来替代从相同表中检索数据时使用的子查询语句。有时候联结比子查询快得多，应该试一下两种方法，以确定哪一种性能更好。  

## 自然联结

无论何时对表进行联结，应该至少有一个列出现不止一个表中（被联结的列）。标准的联结返回所有数据，甚至相同的列多次出现。自然联结排除多次出现，使每个列只返回一次。**自然联结**的作用就是排除多次出现，使每个列只返回一次。  

目前我们建立的每个内部联结都是自然联结，很可能我们永远都不会用到不是自然联结的内部联结。  

书中这部分给了下面这个例子，不知是想用于说明什么：  

```sql
SELECT customers.*, 
       orders.order_num, 
       orders.order_date, 
       orderitems.prod_id, 
       orderitems.quantity, 
       orderitems.item_price
FROM customers, orders, orderitems
WHERE customers.cust_id = orders.cust_id
AND orders.order_num = orderitems.order_num
AND prod_id = 'FB';
```

查询结果为：  

```
+---------+-------------+----------------+-----------+------------+----------+--------------+--------------+-----------------+-----------+---------------------+---------+----------+------------+
| cust_id | cust_name   | cust_address   | cust_city | cust_state | cust_zip | cust_country | cust_contact | cust_email      | order_num | order_date          | prod_id | quantity | item_price |
+---------+-------------+----------------+-----------+------------+----------+--------------+--------------+-----------------+-----------+---------------------+---------+----------+------------+
|   10001 | Coyote Inc. | 200 Maple Lane | Detroit   | MI         | 44444    | USA          | Y Lee        | ylee@coyote.com |     20005 | 2005-09-01 00:00:00 | FB      |        1 |      10.00 |
|   10001 | Coyote Inc. | 200 Maple Lane | Detroit   | MI         | 44444    | USA          | Y Lee        | ylee@coyote.com |     20009 | 2005-10-08 00:00:00 | FB      |        1 |      10.00 |
+---------+-------------+----------------+-----------+------------+----------+--------------+--------------+-----------------+-----------+---------------------+---------+----------+------------+
```

## 外部联结

外部联结包含了**在相关表中没有关联行**的行。  

例如，将所有客户的订单列出来，即使未下过单的客户也需要列出，如果使用**内部联结**，我们看看有何问题：  

```sql
SELECT customers.cust_id, orders.order_num
FROM customers INNER JOIN orders
ON customers.cust_id = orders.cust_id;
```

查询结果为：  

```
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
|   10001 |     20009 |
|   10003 |     20006 |
|   10004 |     20007 |
|   10005 |     20008 |
+---------+-----------+
```

可以看出查询结果没有显示未下单的客户，因为该客户未出现在 `orders` 表中。  

如果使用**外部联结**：  

```sql
SELECT customers.cust_id, orders.order_num
FROM customers LEFT OUTER JOIN orders
ON customers.cust_id = orders.cust_id;
```

结果为：  

```
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
|   10001 |     20009 |
|   10002 |      NULL |
|   10003 |     20006 |
|   10004 |     20007 |
|   10005 |     20008 |
+---------+-----------+
```

从上述结果可以看出，未下单的客户也显示在了查询结果中。  

外部联结中的 `LEFT` 或 `RIGHT` 用于指定以哪边的表为基准，然后会在查询结果中列出该表的所有行，即使相关表中未查到符合条件的行。上述例子中是以 `customers` 表为联结的基准。  

`LEFT` 与 `RIGHT` 唯一的差别是所关联的表的顺序不同，左外部联结可以通过颠倒 `FROM` 子句中表的顺序转换为右外部联结。如：    

```sql
SELECT customers.cust_id, orders.order_num
FROM orders RIGHT OUTER JOIN customers
ON customers.cust_id = orders.cust_id;
```

查询结果与上一个例子一样。  

因此，两种类型的外部联结可互换使用，而究竟使用哪一种纯粹是根据方便而定。  

## 使用带聚集函数的联结

查询每个客户所下的订单数（使用**内部联结**）：  

```sql
SELECT customers.cust_name, 
       customers.cust_id,
       COUNT(orders.order_num) AS num_ord
FROM customers INNER JOIN orders
ON customers.cust_id = orders.cust_id
GROUP BY customers.cust_id;
```

查询结果：  

```
+----------------+---------+---------+
| cust_name      | cust_id | num_ord |
+----------------+---------+---------+
| Coyote Inc.    |   10001 |       2 |
| Wascals        |   10003 |       1 |
| Yosemite Place |   10004 |       1 |
| E Fudd         |   10005 |       1 |
+----------------+---------+---------+
```

查询每个客户所下的订单数，包括未下过单的客户（使用**左外部联结**）：  

```sql
SELECT customers.cust_name, 
       customers.cust_id,
       COUNT(orders.order_num) AS num_ord
FROM customers LEFT OUTER JOIN orders
ON customers.cust_id = orders.cust_id
GROUP BY customers.cust_id;

```

查询结果：  

```
+----------------+---------+---------+
| cust_name      | cust_id | num_ord |
+----------------+---------+---------+
| Coyote Inc.    |   10001 |       2 |
| Mouse House    |   10002 |       0 |
| Wascals        |   10003 |       1 |
| Yosemite Place |   10004 |       1 |
| E Fudd         |   10005 |       1 |
+----------------+---------+---------+
```

