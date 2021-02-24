+ 展示mysql版本状态信息
```sql
status;
```

+ 显示所有schema
```sql
show schemas;
show databases;
```

+ 切换schema
```sql
use XXX；
```

+ 显示表中的索引
```sql
show index from XXX;
``` 

+ 查看表结构描述
```sql
desc XXX;
show create table XXX; // 这个命令可以展示详细的建表语句 包括存储引擎的选择等
``` 

+ 创建表
```sql
create table t_user(id int(10) primary key,name varchar(2),age int) engine = InnoDB;
```

+ 删除索引
```sql
drop index XXX on [TABLE_NAME];
```

+ 命令行下执行sql文件
```sql
mysql -u root -p < sakila-data.sql
```