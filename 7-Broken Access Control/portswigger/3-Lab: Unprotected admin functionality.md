```markdown
# 🔹 **تخطي الحماية باستخدام ملف robots.txt** 🔹

## 📌 **الهدف:**  
استغلال ملف `robots.txt` للوصول إلى لوحة الإدارة وحذف المستخدم **Carlos**.

---

## **📍 الخطوات:**

### 1️⃣ **استكشاف ملف robots.txt**
- افتح **المعمل** في المتصفح.
- في شريط العنوان، أضف `/robots.txt` لنهاية الـ URL، ليصبح بالشكل التالي:
  
  ```
  https://your-lab-url/robots.txt
  ```

- سيظهر لك محتوى الملف، وابحث عن سطر **Disallow**، والذي يحتوي على المسار المخفي للوحة الإدارة، مثال:

  ```
  User-agent: *
  Disallow: /administrator-panel
  ```

---

### 2️⃣ **الوصول إلى لوحة الإدارة**
- استبدل `/robots.txt` في شريط العنوان بـ **المسار الموجود في Disallow**:

  ```
  https://your-lab-url/administrator-panel
  ```

- الآن، إذا كان الوصول غير محمي، ستفتح لك لوحة تحكم المسؤول مباشرة.

---

### 3️⃣ **حذف المستخدم Carlos**
- بمجرد الدخول للوحة التحكم، ابحث عن قائمة المستخدمين.
- ابحث عن **Carlos** واضغط على **حذفه**.

---

## ✅ **الملخص:**
1. استعرض `robots.txt` لمعرفة المسارات المخفية.
2. استخدم المسار للوصول إلى لوحة الإدارة.
3. احذف المستخدم المستهدف من داخل اللوحة.

---

💡 **نصيحة أمنية:**  
ملف `robots.txt` ليس وسيلة لحماية الصفحات الحساسة، بل هو مجرد توجيه لعناكب البحث. لذا، لا تعتمد عليه لإخفاء مسارات حساسة! 🚨
```

---
في سيناريو حقيقي لو **ملف `robots.txt` مش موجود** أو مش بيحتوي على أي مسارات حساسة، فيه طرق تانية تقدر تستخدمها علشان تكتشف مسار لوحة الإدارة:  

---

## 🔹 **1️⃣ التخمين الشائع (Common Directory Enumeration)**
المسارات الخاصة بلوحات الإدارة بتكون متشابهة في مواقع كتير، جرب تكتب بعض المسارات الشائعة بعد اسم الدومين:

```
https://target-site.com/admin
https://target-site.com/administrator
https://target-site.com/admin-panel
https://target-site.com/dashboard
https://target-site.com/wp-admin   (لو الموقع WordPress)
https://target-site.com/cpanel
https://target-site.com/login
```

📌 **الأدوات اللي ممكن تساعدك في التخمين:**
- `ffuf`  
- `dirb`  
- `gobuster`  

مثال باستخدام `gobuster`:
```bash
gobuster dir -u https://target-site.com -w /usr/share/wordlists/dirb/common.txt
```
ده بيجرب مسارات من قائمة جاهزة على الموقع وبيحدد اللي موجود.

---

## 🔹 **2️⃣ استغلال رسائل الخطأ (Error-based Information Disclosure)**
- لو جربت تدخل مسار عشوائي زي `/random-admin-panel` وشفت رسالة خطأ زي:
  
  ```
  403 Forbidden - You don't have permission to access this resource.
  ```
  فده معناه إن المسار موجود لكنه محمي.
  
- لو دخلت `/admin` وظهرت رسالة زي:
  
  ```
  404 Not Found
  ```
  فده معناه إن المسار **غير موجود أصلاً**.

- أحيانًا ممكن الـ **JavaScript files** تكشف مسارات مخفية عن طريق فحص كود الموقع (`CTRL + U` أو DevTools > Sources).

---

## 🔹 **3️⃣ البحث في كود الموقع (Inspect & Fuzzing)**
1. **افتح "View Page Source" أو DevTools** (`CTRL + U` أو `F12`).
2. دور على كلمات زي:
   - `admin`
   - `dashboard`
   - `login`
   - `redirect`
   - `panel`
3. ممكن تلاقي مسار مذكور في JavaScript أو في إحدى **الـ API requests**.

---

## 🔹 **4️⃣ تحليل الروابط الداخلية (Internal Links)**
- بعض المواقع بتحتوي على روابط مخفية داخل الـ HTML. ممكن تستخدم `wget` أو `curl` لاستكشاف الروابط:
```bash
wget -r -np -l 2 https://target-site.com
```

---

## 🔹 **5️⃣ البحث في محركات البحث (Google Dorking)**
ممكن يكون المسار متاح للعامة عن طريق البحث في جوجل باستخدام:
```
site:target-site.com intitle:"admin"
site:target-site.com inurl:"admin"
site:target-site.com filetype:txt
```
ده ممكن يظهر ملفات تكشف مسارات مخفية.

---

## ✅ **الملخص:**
1. **التخمين** باستخدام المسارات الشائعة.
2. **استخدام أدوات كشف المسارات** زي `gobuster` أو `dirb`.
3. **تحليل الأخطاء** لو الموقع بيكشف معلومات إضافية.
4. **فحص كود الصفحة والـ JavaScript** لاستخراج المسارات المخفية.
5. **استخدام Google Dorking** للبحث عن الملفات أو الروابط المكشوفة.

🕵️‍♂️ **نصيحة:** دايماً استخدم الطرق دي في بيئات اختبارية أو بعد إذن قانوني، لأن استكشاف المسارات المخفية بدون تصريح ممكن يعتبر هجوم على الموقع. 🚨
