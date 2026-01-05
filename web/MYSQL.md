
- 关系型数据库(SQL)-RDBMS关系型数据库管理系统
- 非关系型数据库(NOSQL)-NRDBMS非关系型数据库管理系统

## tables&keys

- 关系型数据库的数据存储在表里，主键唯一表示两个不同的数据，表头对应键，数据对应相应的值。
- 当一个主键无法唯一确定数据时，可以使用多个主键来唯一确认数据
- 外键，外键的值可以对应另外一个表格的主键。

## 创建数据库

- 用反引号包括住，防止关键字冲突
```MYsql
CREATE DATABASE `NAME`;
```
## 查看数据库

```MYsql
SHOW DATABASES;
```

## 删除数据库

```mysql
DROP DATABASE `NAME`
```

## 数据类型

- `INT`:整数
- `DECIMAL(m,n)`:浮点数,m：几位数，n：小数点占几位（小数部分）
- `VARCHAR(n)`：字符串，n：最大长度
- `BLOB`：大型二进制对象
- `DATE`：日期，YYYY-MM-DD
- `TIMESTAMP`：记录时间，时间戳 ,YYYY-MM-DD HH:MM:SS


## 创建表格

- 选择使用的数据库
```mysql
USE `NAME`
```
- 创建表格
```mysql
CREATE TABLE NAME(
	KEY DATATYPE,
	...
);

CREATE TABLE `student`(
	`student_id` INT PRIMARY KEY,
	`name` VARCHAR(20),
	`major` VARCHAR(20)
);
```

## 查看表格

```mysql
DESCRIBE name
```

## 删除表格

```mysQl
DROP TABLE name;
```

## 给表格添加属性

```mysql
ALTER TABLE 表格名 ADD 属性名 属性对应的数据类型;

ALTER TABLE `student` ADD gpa DECIMAL(3,2);
```

## 删除表格的属性

```mysql
ALTER TABLE 表格名 DROP COLUMN 属性名;

ALTER TABLE `student` DROP COLUMN gpa;
```

## 插入数据

- 字符串用单引号包住，而不是双引号
- 不填的属性为`NULL`
```mysql
INSERT INTO 表格名 VALUES(...)
INSERT INTO `student`(属性1，属性2，...) VALUES(...);

INSERT INTO `student` VALUES(1,'小白','历史');
INSERT INTO `student`(`name`,`major`,`student_id`) VALUES('小蓝','英语',4);
```

## 列出所有数据

```mysql
SELECT * FROM 表格名;
SELECT * FROM `student`;
```

## 属性限制

```mysql
CREATE TABLE NAME(
	KEY DATATYPE CONSTRAINTS,
	...
);
```

```MYSQL
NOT NULL 设置属性不能为空
UNIQUE 属性的值不能重复(唯一)
DEFAULT 默认值，预设值
AUTO_INCREMENT 自增
```

## 修改数据

- 语法
```mysql
UPDATE 表格名
SET 要更新的内容
WHERE 条件
```

- 例子
```mysql
UPDATE `student` 
SET `major` = '英语文学'
WHERE `major` = '英语';
-- 把所有major为英语的数据的major改成英语文学

UPDATE `student` 
SET `major` = '生化'
WHERE `major` = '生物' OR `major` = '化学';
-- 把所有major为生物或化学的数据的major改成生化

UPDATE `student` 
SET `name` = '小灰', `major` = '物理'
WHERE `student_id` = 1;
-- student_id=1的数据的name改成'小灰'，major改成'物理'

UPDATE `student` 
SET `major` = '物理';
-- 把表格中所有的数据的major都改成'物理'
```

## 删除数据

- 语法
```mysql
DELETE FROM 表格名
WHERE 条件;
```

- 例子
```mysql
DELETE FROM `student`
WHERE `student_id` = 4;
-- 删除student_id = 4的数据

DELETE FROM `student`
WHERE `name`='小黑' AND `major` = '物理';

DELETE FROM `student`
WHERE `score` < 60;

DELETE FROM `student`;
-- 删除全部数据
```

## 获得数据

- 语法
```mysql
SELECT 要取得的属性名 
FROM 表格名
ORDER BY 排序关键字1，关键字2，...
LIMIT N
WHERE 条件;
```

- 例子
```mysql
SELECT * FROM 表格名 ORDER BY 排序参考,... ASC; -- 升序,默认
SELECT * FROM 表格名 ORDER BY 排序参考,... DESC; -- 降序
SELECT * FROM 表格名 LIMIT N; -- 取得前N个数据

SELECT `name`,`major` FROM `student`;
SELECT * FROM `student` ORDER BY `score`;
SELECT * FROM `student` ORDER BY `score` DESC LIMIT 3;
SELECT * FROM `student` WHERE `major` = '英语' OR `score`>20;
SELECT * FROM `student` WHERE `major` = '英语' OR `score`<>70;
-- <>为不等于的符号
SELECT * FROM `student` WHERE `major` IN ('历史','英语','生物');
-- 检查major是否是('历史','英语','生物')中的一个
```

