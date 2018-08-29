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
SELECT a.attnum
      ,a.attname                            AS name
      ,format_type(a.atttypid, a.atttypmod) AS typ
      ,a.attnotnull                         AS notnull
      ,coalesce(p.indisprimary, FALSE)      AS primary_key
      ,f.adsrc                              AS default_val
      ,d.description                        AS col_comment
FROM   pg_attribute    a
LEFT   JOIN pg_index   p ON p.indrelid = a.attrelid AND a.attnum = ANY(p.indkey)
LEFT   JOIN pg_description d ON d.objoid  = a.attrelid AND d.objsubid = a.attnum
LEFT   JOIN pg_attrdef f ON f.adrelid = a.attrelid  AND f.adnum = a.attnum
WHERE  a.attnum > 0
AND    NOT a.attisdropped
AND    a.attrelid = 'public.smartsa_training'::regclass  -- table may be schema-qualified
ORDER  BY a.attnum;

```