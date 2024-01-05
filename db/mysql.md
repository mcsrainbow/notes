## MySQL

### Frequently

```sql
-- duplicate table
CREATE TABLE table_name_20240105 LIKE table_name;
INSERT INTO table_name_20240105 SELECT * FROM table_name;

-- update column
UPDATE table_name SET column_a = 'A' WHERE column_b = 'B';

-- create database and user
CREATE DATABASE db_name CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
CREATE USER 'user_name'@'%';
GRANT ALL ON db_name.* TO 'user_name'@'%';
ALTER USER 'user_name'@'%' IDENTIFIED BY 'password';

-- create index
CREATE INDEX index_name ON table_name (column1, column2, ...);
CREATE INDEX idx_lastname ON customers (last_name);
```

```bash
# backup database to sql file
mysqldump --user=user_name --password=password --host=host_name db_name > db_name.$(date +%Y%m%d%H%M).sql
mysqldump --user=user_name --password=password --host=host_name --all-databases > databases.$(date +%Y%m%d%H%M).sql
mysqldump --user=user_name --password=password --host=host_name db_name table_name > db_name.table_name.$(date +%Y%m%d%H%M).sql

docker exec mysql_container mysqldump --user=user_name --password=password --host=host_name db_name > db_name.$(date +%Y%m%d%H%M).sql

# restore from backup sql file
mysql --user=user_name --password=password --host=host_name < path_to_backup.sql

mysql> USE db_name;
mysql> SOURCE path_to_backup.sql;

# reset password
mysqld_safe --skip-grant-tables
mysql> UPDATE mysql.user SET password=PASSWORD('new_pass') WHERE user='root';
```

### Browsing

```sql
SHOW DATABASES;
SHOW TABLES;
SHOW COLUMNS FROM table_1;
DESCRIBE table_1;
SHOW CREATE TABLE table_1;
SHOW PROCESSLIST;
KILL process_number;
```

### Database

```sql
CREATE DATABASE db_name;
CREATE DATABASE db_name CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
USE db_name;
DROP DATABASE db_name;
ALTER DATABASE db_name CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```

### Users and Privileges

```sql
CREATE USER 'user'@'localhost';
GRANT ALL PRIVILEGES ON db_name.* TO 'user'@'localhost';
GRANT SELECT, INSERT, DELETE ON db_name.* TO 'user'@'localhost';
ALTER USER 'user'@'localhost' IDENTIFIED BY 'password';

REVOKE ALL PRIVILEGES ON db_name.* FROM 'user'@'host';
REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'user'@'host';

FLUSH PRIVILEGES;
```

```sql
SET PASSWORD = PASSWORD('new_pass');
SET PASSWORD FOR 'user'@'host' = PASSWORD('new_pass');
```

```sql
DROP USER 'user'@'host';
```

### Table

#### Create

```sql
CREATE TABLE table_1 (column_1 type_1, column_2 type_2);
CREATE TABLE table_1 (column_1 type_1, column_2 type_2, INDEX (column));
CREATE TABLE table_1 (column_1 type_1, column_2 type_2, PRIMARY KEY (column_1));
CREATE TABLE table_1 (column_1 type_1, column_2 type_2, PRIMARY KEY (column_1,column_2));
```

```sql
CREATE TABLE table_1 (fk_column_1 type_1, column_2 type_2, ...,
  FOREIGN KEY (fk_column_1) REFERENCES table_2 (t2_column_a))
    [ON UPDATE|ON DELETE] [CASCADE|SET NULL]
```

```sql
CREATE TABLE table_1 (fk_column_1 type_1, fk_column_2 type_2, ...,
  FOREIGN KEY (fk_column_1, fk_column_2) REFERENCES table_2 (t2_column_a, t2_column_b))
```

```sql
CREATE TABLE table_1 IF NOT EXISTS;
```

```sql
CREATE TEMPORARY TABLE table;
```

#### Drop

```sql
DROP TABLE table_1;
DROP TABLE IF EXISTS table_1;
DROP TABLE table_1, table_2, ...
```

#### Alter

```sql
ALTER TABLE table_1 MODIFY column_1 type_1;
ALTER TABLE table_1 MODIFY column_1 type_1 NOT NULL ...
ALTER TABLE table_1 CHANGE old_name_column_1 new_name_column_1 type_1;
ALTER TABLE table_1 CHANGE old_name_column_1 new_name_column_1 type_1 NOT NULL ...
ALTER TABLE table_1 ALTER column_1 SET DEFAULT ...
ALTER TABLE table_1 ALTER column_1 DROP DEFAULT;
ALTER TABLE table_1 ADD new_name_column_1 type_1;
ALTER TABLE table_1 ADD new_name_column_1 type_1 FIRST;
ALTER TABLE table_1 ADD new_name_column_1 type_1 AFTER another_column;
ALTER TABLE table_1 DROP column_1;
ALTER TABLE table_1 ADD INDEX (column);
```

#### Column Order

```sql
ALTER TABLE table_1 MODIFY column_1 type_1 FIRST;
ALTER TABLE table_1 MODIFY column_1 type_1 AFTER another_column;
ALTER TABLE table_1 CHANGE old_name_column_1 new_name_column_1 type_1 FIRST;
ALTER TABLE table_1 CHANGE old_name_column_1 new_name_column_1 type_1 AFTER another_column;
```

#### Keys

```sql
CREATE TABLE table_1 (..., PRIMARY KEY (column_1, column_2));
CREATE TABLE table_1 (..., FOREIGN KEY (column_1, column_2) REFERENCES table_2 (t2_column_1, t2_column_2));
```

### Conditions

```sql
column_1 = value_1
column_1 <> value_1
column_1 LIKE 'value _ %'
column_1 IS NULL
column_1 IS NOT NULL
column_1 IS IN (value_1, value_2)
column_1 IS NOT IN (value_1, value_2)
condition_1 AND condition_2
condition_1 OR condition_2
```

### Select

```sql
SELECT * FROM table_1;
SELECT * FROM table_1, table_2;
SELECT column_1, column_2 FROM table_1, table_2;
SELECT ... FROM ... WHERE condition;
SELECT ... FROM ... WHERE condition GROUP BY column;
SELECT ... FROM ... WHERE condition_1 GROUP BY column HAVING condition_2;
SELECT ... FROM ... WHERE condition ORDER BY column_1, column_2;
SELECT ... FROM ... WHERE condition ORDER BY column_1, column_2 DESC;
SELECT ... FROM ... WHERE condition LIMIT 10;
SELECT DISTINCT column_1 FROM ...
SELECT DISTINCT column_1, column_2 FROM ...
```

```sql
SELECT ... FROM t1 JOIN t2 ON t1.id1 = t2.id2 WHERE condition;
SELECT ... FROM t1 LEFT JOIN t2 ON t1.id1 = t2.id2 WHERE condition;
SELECT ... FROM t1 JOIN (t2 JOIN t3 ON ...) ON ...
```

### Insert

```sql
INSERT INTO table_1 (column_1, column_2) VALUES (value_1, value_2);
```

### Delete

```sql
DELETE FROM table_1;
TRUNCATE table_1;
DELETE FROM table_1 WHERE condition;
DELETE FROM table_1, table_2 WHERE table_1.id1 = table_2.id2 AND condition;
```

### Update

```sql
UPDATE table_1 SET column_1=new_value_1 WHERE condition;
UPDATE table_1, table_2 SET column_1=new_value_1, column_2=new_value_2, ... WHERE table_1.id1 = table_2.id2 AND condition;
```
