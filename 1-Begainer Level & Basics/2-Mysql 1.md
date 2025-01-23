## موقع للتدريب على اوامر mysql 
```
https://www.w3schools.com/MySQL/default.asp
```
----
# التعامل مع قواعد البيانات باستخدام MariaDB

```
# 1. عرض قواعد البيانات
SHOW DATABASES;

# 2. استخدام قاعدة بيانات معينة
USE database_name;

# 3. عرض الجداول داخل قاعدة بيانات
SHOW TABLES;

# 4.  عرض كل البيانات من جدول معين:
SELECT * FROM table_name;

# 5. عرض بيانات عمود معين من جدول:
SELECT column_name FROM table_name;

# 6. عرض بيانات بناءً على شرط معين:
SELECT column_name FROM table_name WHERE column_name = "row_value";

```
## جدول الأوامر الشائعة

| الأمر                | الوصف                                    |
|----------------------|------------------------------------------|
| `SHOW DATABASES;`    | عرض جميع قواعد البيانات.                 |
| `USE database_name;` | اختيار قاعدة بيانات للعمل عليها.         |
| `SHOW TABLES;`       | عرض جميع الجداول داخل قاعدة البيانات.    |
| `SELECT`             | استخراج البيانات من قاعدة البيانات.     |
| `UPDATE`             | تحديث البيانات في قاعدة البيانات.       |
| `DELETE`             | حذف البيانات من قاعدة البيانات.         |
| `INSERT INTO`        | إدخال بيانات جديدة في قاعدة البيانات.   |
| `CREATE DATABASE`    | إنشاء قاعدة بيانات جديدة.               |
| `ALTER DATABASE`     | تعديل قاعدة البيانات.                   |
| `CREATE TABLE`       | إنشاء جدول جديد.                        |
| `ALTER TABLE`        | تعديل جدول موجود.                       |
| `DROP TABLE`         | حذف جدول.                               |
| `CREATE INDEX`       | إنشاء مفتاح بحث (index).                |
| `DROP INDEX`         | حذف مفتاح البحث (index).                |
```
---
