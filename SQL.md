# SQL相关问题记录

---

## MySQL

### ***清空表SQL***

``` sql
TRUNCATE TABLE tableName;
```

### ***MySQL新增字段***

``` sql
ALTER TABLE tableName ADD COLUMN columnName VARCHAR(20) DEFAULT '' NOT NULL COMMENT '类型' AFTER anotherColumnName;
```

### ***建表SQL***

```sql
CREATE TABLE `tableName` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '主键id',
  `remark` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT '备注',
  `add_date` datetime NOT NULL COMMENT '新增时间',
  `last_update_date` datetime NOT NULL COMMENT '最后修改时间',
  `add_user_id` bigint(20) NOT NULL DEFAULT '0' COMMENT '添加人用户Id',
  `data_version` bigint(20) NOT NULL DEFAULT '1' COMMENT '数据版本号',
  `last_update_user_id` bigint(20) NOT NULL DEFAULT '0' COMMENT '最后更新人用户Id',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=15 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### ***MySQL新建唯一索引时是不区分大小写的，如果需要区分需要添加唯一索引，binary是为了英文区分大小写，因为mysql的唯一索引不区分大小写***

```sql
CREATE TABLE
Student
(
    id bigint NOT NULL AUTO_INCREMENT COMMENT '主键',
    no VARCHAR(100) COMMENT '学号',
    //加唯一索引，binary是为了英文区分大小写，因为mysql的唯一索引不区分大小写
    name VARCHAR(100) binary NOT NULL unique  COMMENT '姓名'
    createdOn DATETIME COMMENT '创建时间',
    PRIMARY KEY (id)
)
ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='学生表'
```

## SQLServer

### ***时间戳格式转换***

```sql
SELECT CONVERT(BIGINT,version) FROM TableName;
```

### ***查询SqlServer索引***

```sql
SELECT
索引名称=a.name,
表名=c.name,
索引字段名=d.name,
索引字段位置=d.colid
FROM sysindexes a
JOIN sysindexkeys b ON a.id = b.id AND a.indid = b.indid
JOIN sysobjects c ON b.id = c.id
JOIN syscolumns d ON b.id = d.id AND b.colid = d.colid
WHERE a.indid NOT IN (0,255)
-- and   c.xtype='U'   and   c.status>0 --查所有用户表
AND   c.name='tableName' --查指定表
ORDER BY   c.name,a.name,d.name
```

### ***数据库表查询***

```sql
SELECT  
d.name 表名,
a.colorder 字段序号,
a.name 字段名,
b.name 类型,
a.length 占用字节数,
COLUMNPROPERTY(a.id,a.name,'PRECISION') AS 长度,
ISNULL(e.text,'') 默认值,
ISNULL(g.[value],'') AS 字段说明
FROM syscolumns a
LEFT JOIN systypes b ON a.xtype=b.xusertype
INNER JOIN sysobjects d ON a.id=d.id AND d.xtype='U' AND d.name <>'dtproperties'
LEFT JOIN syscomments e ON a.cdefault=e.id
LEFT JOIN sys.extended_properties g  ON a.id=g.major_id AND a.colid = g.minor_id
WHERE d.name='tableName'
ORDER BY a.id,a.colorder
```
