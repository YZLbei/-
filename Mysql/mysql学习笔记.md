# 一、select

## 去重

```sql
select distinct cust_country from customers;
```

## limit

```sql
select `cust_id` from customers limit 2,2;
```

第一个数是开始的行数，第二个数是大小，也就是返回的是2-4



## 排序

**ORDER BY**

### 按一个列排序

```sql
select * from customers order by cust_state;
```

### 按多个列排序

order by 后面加，

![image-20220508200606835](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220508200606835.png)

只有price有多个相同值时，才会按照name排序

### 指定排序方向

#### DESC

```sql
SELECT cust_name,cust_contact,cust_zip FROM customers ORDER BY cust_zip DESC;
```

注意Desc只作用于前边的列

#### 多个列上降序排序

每个列都要写DESC

```sql
SELECT prod_id,prod_price,prod_name FROM products  ORDER BY prod_price DESC,prod_name DESC
```



## 小技巧

使用limit和order by找最大值

```sql
SELECT `prod_price` FROM `products` ORDER BY `prod_price` DESC LIMIT 1
```

1. order by 在from之后
2. limit在最后
3. 要desc



# 二、过滤

![image-20220509211637202](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220509211637202.png)

## **使用引号**

- 字符串使用引号
- 数值不使用引号

## 不匹配

<>

## 范围值

```sql
SELECT prod_name,prod_price FROM products WHERE `prod_price` BETWEEN 5 AND 10
```

## 空值

```sql
SELECT prod_name FROM `products` WHERE `prod_price` IS NULL
```

**注意**

匹配和不匹配不会返回null值的行



# 操作符

## And和Or

and优先级高于or

要利用（）明确优先级

```sql
SELECT prod_name,prod_price FROM products WHERE (`vend_id`=1002 OR `vend_id`=1003)AND `prod_price`<=10
```

## IN

in功能相当于or

- in表示范围
- in的取值在（）中，由，分隔

```sql
SELECT prod_name,prod_price FROM products WHERE `vend_id` IN (1002 ,1003)AND `prod_price`<=10
```

## NOT操作符



# 通配符

## like操作符

表示后面的是通配符匹配

## %

表示任意字符出现任意次数

表示0个，1个或者多个字符

```sql
SELECT prod_name,prod_price FROM products WHERE `prod_name` LIKE 'jet%'
```

### 注意尾空格

尾空格通配符不会匹配

### 注意NULL

%几乎可以匹配任何东西

但是不能匹配NULL，不能用NULL命名

## _

下划线只匹配单个字符而不是多个字符

不能匹配0个字符



# 正则表达式

## 基本字符匹配

```sql
SELECT `prod_name` FROM `products` WHERE `prod_name` REGEXP '1000' ORDER BY `prod_name`
```



REGEXP后面是正则表达式处理



## .

表示匹配任意一个字符

```sql
SELECT `prod_name` FROM `products` WHERE `prod_name` REGEXP '.000' ORDER BY `prod_name`
```

![image-20220510212400568](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220510212400568.png)

## LIKE与REGEXP的区别

- LIKE是匹配整个列
- REGEXP是匹配列值

REGEXP不区分大小写，要区分大小写要用BINARY ，在正则表达式前加上BINARY



## OR匹配

不是用or操作符

而是用|

```sql
SELECT `prod_name` FROM `products` WHERE `prod_name` REGEXP '1000|2000' ORDER BY `prod_name`
```



## 匹配几个字符之一

用**[]**，表示匹配[]中字符的一个

```sql
SELECT `prod_name` FROM `products` WHERE `prod_name` REGEXP '[123] ton' ORDER BY `prod_name`
```

表示123中选一个

![image-20220510213327479](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220510213327479.png)



[123]相当于[1|2|3]（可以这样写）

[]主要作用是确定OR查找范围

如果把'[123] ton' 写成'1|2|3 ton' 结果就是错误的



### ^

可以用^表示匹配指定字符集之外的，如'[^^123] ton'



## 注意

匹配时空格是有实际意义的

## 匹配范围(-)

```sql
SELECT `prod_name` FROM `products` WHERE `prod_name` REGEXP '[1-3] ton' ORDER BY `prod_name`
```

## 匹配特殊字符

匹配特殊字符用\\

**转义字符**

```sql
select prod_name from `products` where prod_name REGEXP '\\.'
```

## 匹配字符类

使用预定的字符集

![image-20220511194421736](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511194421736.png)

要注意字符集[]是一体的，在字符集外还应该有[]，如[[:dight:]]

## 匹配多个实例（限定出现次数）

![image-20220511194751531](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511194751531.png)



```sql
select prod_name from `products` where prod_name REGEXP '\\([[:digit:]] sticks?\\)'
```

![image-20220511195019844](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511195019844.png)

**注意**

- 是[[:digit:]] 
- 要写s，s？表示s是0个或者1个

### 匹配连着四个数字

```sql
select prod_name from `products` where prod_name REGEXP '[[:digit:]]{4}'
```

![image-20220511195152618](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511195152618.png)

表示连着出现4个数字

## 定位符

![image-20220511195612030](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511195612030.png)

### ^

表示以^后的开始

```sql
select prod_name from `products` where prod_name REGEXP '^[[:digit:]\\.]'
```

![image-20220511195409408](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511195409408.png)

#### ^的两种用法

- 在【】里表示否定
- 在外面或者后面接字符，表示文本的开始

# 创建计算字段

- 当存储在表中的数据不是程序需要的，我们需要的是从数据库检索出转换、计算或格式化的数据
- 而不是从数据库检索出数据，再由客户端重新格式化
- 在服务器上完成效率更高，要快的多

## 何为计算字段

1. 计算字段就是在数据库中的一种字段，数据库进行格式转换、计算或者格式化后的字段
2. 不是真正存在于数据库表中的，而是运行时在select语句内创建的
3. 对于客户机，区分不出计算字段和表中实际存在的字段

## 拼接字段

使用**Concat（）**拼接

注意多数的SQL使用+或者||拼接

```sql
SELECT CONCAT(vend_name ,'(',`vend_country`,')') FROM vendors
```

![image-20220511200851578](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511200851578.png)

### RTRIM()

去掉值右边的所有空格

```sql
select concat(RTrim(vend_name) ,'(',rtrim(`vend_country`),')') from vendors
```

![image-20220511201316097](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511201316097.png)

LTRIM去掉左侧的所有空格

TRIM去掉两侧的所有空格



### 取别名

未命名的列不能应用于客户机中，因为没法引用

所以要起别名

#### Alias

```sql
select concat(RTrim(vend_name) ,'(',rtrim(`vend_country`),')') as vend_title from vendors 
```

![image-20220511201623110](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511201623110.png)

## 算数运算

```sql
SELECT prod_id ,quantity*`item_price`  AS price FROM `orderitems` WHERE `order_num`= 20005
```

![image-20220511204419872](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511204419872.png)

​	

# 使用数据处理函数

## 文本处理函数

### Upper（变为大写）

```sql
SELECT vend_name,UPPER(vend_name) AS name_upcase FROM vendors ORDER BY vend_name
```

![image-20220511205010495](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511205010495.png)

![image-20220511205021257](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511205021257.png)

## 日期和时间处理函数

比较重要

![image-20220511205449466](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511205449466.png)

**注意**

- mysql中的日期格式一定是**yyyy-mm-dd**，不论是插入还是where

- 用where比较时，要用Date函数

  用Date（）函数相当于只比较日期；

  因为date类型可能有具体的时间，不使用Date()函数，可能会匹配失败

```sql
SELECT `cust_id` ,order_num FROM orders WHERE DATE(`order_date`) = '2005-09-01'
```

![image-20220511210226569](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511210226569.png)

- 如果只用日期，用**Date()**函数
- 否则用**Time（）**函数

### 检测9月的所有日期

```sql
SELECT `cust_id` ,order_num FROM orders WHERE DATE(`order_date`) BETWEEN '2005-09-01 ' AND '2005-09-30'
```

![image-20220511210539222](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511210539222.png)

或者用年和月

```sql
SELECT `cust_id` ,order_num FROM orders WHERE YEAR(`order_date`)=2005 AND MONTH(`order_date`)=9
```

## 数值处理函数

![image-20220511210732148](https://buketyzl.oss-cn-qingdao.aliyuncs.com/image-20220511210732148.png)
