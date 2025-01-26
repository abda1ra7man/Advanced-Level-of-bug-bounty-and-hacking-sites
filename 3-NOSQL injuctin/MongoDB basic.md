# MongoDB Commands Summary 

#### **Introduction**
MongoDB هو نوع من قواعد البيانات NoSQL اللي بيستخدم **BSON** لتخزين البيانات بدل الطرق التقليدية زي الجداول في قواعد البيانات العلاقية.

---

#### **تنصيب MongoDB على Linux**
1. تحميل MongoDB:
   ```bash
   curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.4.7.tgz
   ```
2. فك الضغط وتحديد المسار:
   ```bash
   tar xvf mongodb-linux-x86_64-3.4.7.tgz
   mv mongodb-linux-x86_64-3.4.7 mongodb
   export PATH=$PATH:$HOME/mongodb/mongodb/bin
   ```
3. تشغيل MongoDB:
   ```bash
   ./mongod --dbpath $HOME/mongodb/data &
   ```

---

#### **أوامر MongoDB الأساسية**

1. **عرض قواعد البيانات:**
   ```bash
   show dbs
   ```

2. **إنشاء قاعدة بيانات:**
   ```bash
   use database_name
   ```

3. **إنشاء Collection (جدول):**
   ```bash
   db.createCollection("collection_name")
   ```

4. **إضافة مستند (Document):**
   - مستند واحد:
     ```bash
     db.collection_name.insertOne({
       key1: "value1",
       key2: "value2"
     })
     ```
   - مجموعة مستندات:
     ```bash
     db.collection_name.insertMany([
       { key1: "value1" },
       { key2: "value2" }
     ])
     ```

---

#### **تحديث البيانات**
1. تحديث مستند واحد:
   ```bash
   db.collection_name.updateOne(
     { key: "value" },
     { $set: { key: "new_value" } }
   )
   ```

2. تحديث مجموعة مستندات:
   ```bash
   db.collection_name.updateMany(
     { key: "value" },
     { $set: { key: "new_value" } }
   )
   ```

---

#### **حذف البيانات**
1. حذف مستند واحد:
   ```bash
   db.collection_name.deleteOne({ key: "value" })
   ```

2. حذف مجموعة مستندات:
   ```bash
   db.collection_name.deleteMany({ key: "value" })
   ```

3. حذف Collection كامل:
   ```bash
   db.collection_name.drop()
   ```

---

#### **البحث في البيانات**
1. جلب كل المستندات:
   ```bash
   db.collection_name.find()
   ```

2. البحث بشرط معين:
   ```bash
   db.collection_name.find({ key: "value" })
   ```

3. البحث بشرط Regex:
   ```bash
   db.collection_name.find({ key: { $regex: /^pattern$/ } })
   ```

4. البحث بشرط متعدد:
   ```bash
   db.collection_name.find({ $and: [ { key1: "value1" }, { key2: "value2" } ] })
   ```

---

#### **ملاحظات إضافية**
- MongoDB بيشتغل على **المنفذ الافتراضي 27017/TCP**.
- يمكنك عرض المجموعات باستخدام:
   ```bash
   show collections
   ```

---


