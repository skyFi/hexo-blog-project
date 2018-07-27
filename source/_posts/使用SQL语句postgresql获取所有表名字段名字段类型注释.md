---
title: 使用SQL语句（postgresql）获取所有表名、字段名、字段类型、注释
date: 2018-07-26 16:42:38
tags: 数据库
---

## 获取表名及注释：
```sql
SELECT
	relname AS tabname,
	CAST ( obj_description ( relfilenode, 'pg_class' ) AS VARCHAR ) AS COMMENT
FROM
	pg_class C
WHERE
	relkind = 'r'
	AND relname NOT LIKE'pg_%'
	AND relname NOT LIKE'sql_%'
ORDER BY
	relname;
```
过滤掉分表：

加条件 `and relchecks=0` 即可



## 获取字段名、类型、注释、是否为空:

```sql
SELECT
	col_description ( A.attrelid, A.attnum ) AS COMMENT,
	format_type ( A.atttypid, A.atttypmod ) AS TYPE,
	A.attname AS NAME,
	A.attnotnull AS NOTNULL
FROM
	pg_class AS C,
	pg_attribute AS A
WHERE
	C.relname = 'smartsa_training'
	AND A.attrelid = C.oid
	AND A.attnum > 0;
```