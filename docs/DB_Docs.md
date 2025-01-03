### SQL Syntax Document for System Task Queries Across Various Dialects

This document provides syntax examples for performing common system task queries across different SQL dialects
including MSSQL (SSMS 2017), PostgreSQL, SQLite, MariaDB (MySQL), MongoDB, and GraphQL. The tasks include querying
columns from tables, retrieving primary keys (PK) and foreign keys (FK), managing users and grants, and fetching
column values.

---

### MSSQL (SQL Server Management Studio 2017)

#### 1. **Getting Columns from a Table**
```sql
-- Select all columns from a table
SELECT * FROM YourTableName;

-- Select specific columns
SELECT Column1, Column2 FROM YourTableName;
```

#### 2. **Getting PK/FK Information**
```sql
-- System catalog view to find primary keys
SELECT tc.table_name, tc.column_name, tc.constraint_name
FROM sys.tables t
JOIN sys.index_columns ic ON t.object_id = ic.object_id
JOIN sys.key_columns kc ON ic.object_id = kc.object_id AND ic.column_id = kc.column_id
JOIN sys.key_restrictions kr ON kc.object_id = kr.object_id AND kc.column_id = kr.column_id
JOIN sys.tables pkTable ON kc.parent_object_id = pkTable.object_id
WHERE pkTable.name = 'YourTableName';

-- System catalog view to find foreign keys
SELECT
    fk.name AS ForeignKeyName,
    OBJECT_NAME(fk.parent_object_id) AS TableName,
    COL_NAME(fh.parent_object_id, fh.column_id) AS ColumnName,
    OBJECT_NAME(fk.referenced_object_id) AS ReferencedTableName,
    COL_NAME(fgh.referenced_object_id, fgh.column_id) AS ReferencedColumnName
FROM sys.foreign_keys AS fk
JOIN sys.foreign_key_columns AS fh ON fk.object_id = fh.constraint_object_id
JOIN sys.foreign_key_columns AS fgh ON fh.parent_object_id = fgh.constraint_object_id
WHERE fk.parent_object_id = OBJECT_ID('YourTableName');
```

#### 3. **Users and Grants**
```sql
-- List all users
SELECT * FROM sys.database_principals WHERE type IN ('S', 'U');

-- List user permissions on a specific table
USE YourDatabaseName;
GO
SELECT user_name(grantee_principal_id) AS [User],
       permission_name AS [Permission]
FROM INFORMATION_SCHEMA.TABLES
JOIN INFORMATION_SCHEMA.TABLE_PRIVILEGES ON INFORMATION_SCHEMA.TABLES.TABLE_NAME =
INFORMATION_SCHEMA.TABLE_PRIVILEGES.TABLE_NAME
WHERE TABLE_CATALOG = 'YourDatabaseName' AND TABLE_NAME = 'YourTableName';
```

#### 4. **Column Values**
```sql
-- Fetch specific column values
SELECT Column1, Column2 FROM YourTableName WHERE SomeCondition = 'SomeValue';
```

---

### PostgreSQL

#### 1. **Getting Columns from a Table**
```sql
-- Select all columns
SELECT * FROM your_table_name;

-- Select specific columns
SELECT column1, column2 FROM your_table_name;
```

#### 2. **Getting PK/FK Information**
```sql
-- Primary Keys
SELECT tc.table_name, tc.column_name
FROM information_schema.table_constraints tc
JOIN information_schema.key_column_usage kcu ON tc.constraint_name = kcu.constraint_name
WHERE tc.constraint_type = 'PRIMARY KEY' AND tc.table_name = 'your_table_name';

-- Foreign Keys
SELECT
    tc.table_name,
    kcu.column_name,
    ccu.table_name AS foreign_table_name,
    ccu.column_name AS foreign_column_name
FROM information_schema.table_constraints tc
JOIN information_schema.key_column_usage kcu ON tc.constraint_name = kcu.constraint_name
JOIN information_schema.constraint_column_usage ccu ON ccu.constraint_name = tc.constraint_name
WHERE tc.constraint_type = 'FOREIGN KEY' AND tc.table_name = 'your_table_name';
```

#### 3. **Users and Grants**
```sql
-- List all users
SELECT usename FROM pg_user;

-- User privileges on a specific table
SELECT rolname AS User, perm TYPE | GRANT TYPE
FROM pg_roles JOIN pg_user_tables ON pg_roles.oid = pg_user_tables.roleid
WHERE pg_user_tables.tablename = 'your_table_name';
```

#### 4. **Column Values**
```sql
-- Fetch specific column values
SELECT column1, column2 FROM your_table_name WHERE some_condition = 'some_value';
```

---

### SQLite

#### 1. **Getting Columns from a Table**
```sql
-- Select all columns
SELECT * FROM your_table_name;

-- Select specific columns
SELECT column1, column2 FROM your_table_name;
```

#### 2. **Getting PK/FK Information**
SQLite does not have explicit system tables for PK/FK like other databases, but you can infer this through schema
inspection:
```sql
-- Check PK constraint on a table (if explicitly defined)
PRAGMA table_info(your_table_name);
```

#### 3. **Users and Grants**
SQLite does not support traditional user management or grants like other SQL databases. However, you can manage
permissions via file ownership:
```sql
-- Check file owner (simulating user management)
SELECT name FROM sqlite_master WHERE type='table' AND name='your_table_name';
```

#### 4. **Column Values**
```sql
-- Fetch specific column values
SELECT column1, column2 FROM your_table_name WHERE some_condition = 'some_value';
```

---

### MySQL

#### 1. **Getting Columns from a Table**
```sql
-- Select all columns
SELECT * FROM your_table_name;

-- Select specific columns
SELECT column1, column2 FROM your_table_name;
```

#### 2. **Getting PK/FK Information**
```sql
-- Primary Keys
SELECT TABLE_NAME, COLUMN_NAME
FROM INFORMATION_SCHEMA.KEY_COLUMN
WHERE TABLE_SCHEMA = 'your_database_name' AND CONSTRAINT_NAME = 'PRIMARY';

-- Foreign Keys
SELECT
    TABLE_NAME,
    COLUMN_NAME,
    CONSTRAINT_NAME,
    REFERENCED_TABLE_NAME,
    REFERENCED_COLUMN_NAME
FROM INFORMATION_SCHEMA.KEY_COLUMN
WHERE TABLE_SCHEMA = 'your_database_name' AND CONSTRAINT_NAME LIKE '%FOREIGN%';
```

#### 3. **Users and Grants**
```sql
-- List all users
SELECT user, host FROM mysql.user;

-- User privileges on a specific table
SELECT user, table_name, grant_priv FROM mysql.user WHERE user = 'your_user';
```

#### 4. **Column Values**
```sql
-- Fetch specific column values
SELECT column1, column2 FROM your_table_name WHERE some_condition = 'some_value';
```

---

### Microsoft SQL Server (T-SQL)

#### 1. **Getting Columns from a Table**
```sql
-- Select all columns
SELECT * FROM your_table_name;

-- Select specific columns
SELECT column1, column2 FROM your_table_name;
```

#### 2. **Getting PK/FK Information**
```sql
-- Primary Keys
SELECT
    t.name AS TableName,
    c.name AS ColumnName
FROM sys.tables t
JOIN sys.index_columns ic ON t.object_id = ic.object_id
JOIN sys.columns c ON ic.object_id = c.object_id AND ic.column_id = c.column_id
WHERE dc.is_primary_key = 1 AND t.name = 'YourTableName';

-- Foreign Keys
SELECT
    OBJECT_NAME(parent_object_id) AS TableName,
    COLUMN_NAME(parent_object_id, parent_column_id) AS ColumnName,
    OBJECT_NAME(referenced_object_id) AS ReferencedTableName,
    COLUMN_NAME(referenced_object_id, referenced_column_id) AS ReferencedColumnName
FROM sys.foreign_keys
JOIN sys.columns ON foreign_key_columns.parent_object_id = columns.object_id AND
foreign_key_columns.parent_column_id = columns.column_id
WHERE parent_object_id = OBJECT_ID('YourTableName');
```

#### 3. **Users and Grants**
```sql
-- List all users
SELECT user_name(user_id) AS [User], type AS [Type] FROM sys.database_principals;

-- User privileges on a specific table
SELECT user_name(grantee_principal_id) AS User,
       permission_name AS Permission
FROM sys.database_permissions dp
JOIN sys.database_principals dp_user ON dp.grantee_principal_id = dp_user.user_id
WHERE dp.major_id = OBJECT_ID('YourTableName');
```

#### 4. **Column Values**
```sql
-- Fetch specific column values
SELECT Column1, Column2 FROM YourTableName WHERE SomeCondition = 'SomeValue';
```

---

### PostgreSQL with JSON (psycopg2 Python Example)

#### 1. **Getting Columns from a Table**
```python
import psycopg2

conn = psycopg2.connect("dbname=your_db user=your_user password=your_password host=localhost")
cur = conn.cursor()

cur.execute("SELECT * FROM your_table_name LIMIT 10")
rows = cur.fetchall()
for row in rows:
    print(row)

cur.close()
conn.close()
```

#### 2. **Getting PK/FK Information**
```python
cur.execute("""
SELECT
    tc.table_name AS TableName,
    tc.column_name AS ColumnName
FROM
    information_schema.table_constraints tc
JOIN
    information_schema.constraint_column_usage ccu ON tc.constraint_name = ccu.constraint_name
JOIN
    information_schema.key_column_usage kcu ON tc.constraint_name = kcu.constraint_name
WHERE
    tc.constraint_type = 'PRIMARY KEY' AND tc.table_name = 'YourTableName'
""")
for row in cur:
    print(row)
```

#### 3. **Users and Grants**
```python
cur.execute("""
SELECT
    usename AS User,
    usetype AS Type
FROM
    pg_user
""")
for row in cur:
    print(row)
```

#### 4. **Column Values**
```python
cur.execute("SELECT column1, column2 FROM your_table_name WHERE some_condition = 'some_value' LIMIT 10")
rows = cur.fetchall()
for row in rows:
    print(row)
```
