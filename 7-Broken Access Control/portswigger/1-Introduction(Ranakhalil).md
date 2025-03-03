## 🔑 مصطلحات هامة: المصادقة (Authentication)

المصادقة هي عملية **التحقق من هوية المستخدم** والتأكد من أنه هو الشخص الذي يدّعي أنه هو.

على سبيل المثال، عندما تريد أليس تسجيل الدخول إلى حسابها على تطبيق معين، فإنها تحتاج إلى إدخال **اسم المستخدم وكلمة المرور** في نموذج تسجيل الدخول. إذا كانت البيانات صحيحة، يتم التحقق منها، ويسمح لها بالدخول.

📌 **ملاحظة**: المصادقة تختلف عن **التفويض (Authorization)**، حيث أن المصادقة تتحقق من "من أنت؟"، بينما التفويض يحدد "ما الذي يُسمح لك بفعله؟".

![image](https://github.com/user-attachments/assets/25cb2ee1-41c2-4357-b107-8b889dd3bc85)

---

## 🍪 مصطلحات هامة: إدارة الجلسات (Session Management)

إدارة الجلسات هي العملية التي يتم من خلالها **تمييز الطلبات التي يرسلها كل مستخدم بعد تسجيل الدخول**، وذلك لضمان استمرار الجلسة دون الحاجة لإعادة المصادقة في كل مرة.

📌 **كيف يتم ذلك؟**
- عند تسجيل الدخول، يقوم الخادم بإرسال **رمز الجلسة (Session Token)** إلى المتصفح في صورة **Cookie**.
- يتم إرسال هذا الرمز مع كل طلب جديد، مما يسمح للخادم بتمييز المستخدمين ومتابعة جلساتهم.

🔍 **مثال عملي**:
عند تسجيل دخول أليس إلى التطبيق، يحصل متصفحها على **رمز الجلسة**، والذي يتم تمريره مع كل طلب جديد، بحيث يعرف الخادم أن هذه الطلبات تخص أليس وليس مستخدمًا آخر.

⚠️ **تحذير**: إذا لم تتم إدارة الجلسات بشكل آمن، فقد يؤدي ذلك إلى **اختطاف الجلسات (Session Hijacking)**، حيث يتمكن المهاجم من سرقة الرمز واستخدامه للوصول إلى حساب المستخدم.

```http
session=666Agif8klerj.fwoiejf
```
---
## 🔐 أنواع التحكم في الوصول (Access Control)

هناك **3 أنواع رئيسية** للتحكم في الوصول:

### 1️⃣ التحكم الرأسي (Vertical Access Control)
🔹 يُستخدم لمنع المستخدمين العاديين من الوصول إلى وظائف متاحة فقط للمسؤولين أو المستخدمين ذوي الصلاحيات الأعلى.

📌 **مثال**:
- يستطيع **المسؤول (Admin)** إدارة المستخدمين، بينما لا يمكن للمستخدم العادي فعل ذلك.

---

### 2️⃣ التحكم الأفقي (Horizontal Access Control)
🔹 يمنع المستخدمين الذين لديهم نفس الامتيازات من الوصول إلى بيانات بعضهم البعض.

📌 **مثال**:
- لا ينبغي أن يتمكن مستخدم من رؤية أو تعديل بيانات مستخدم آخر بنفس الصلاحيات.

---

### 3️⃣ التحكم المعتمد على السياق (Context-Dependent Access Control)
🔹 يعتمد على حالة التطبيق أو تفاعل المستخدم مع النظام لتحديد إمكانية الوصول إلى وظائف معينة.

📌 **مثال**:
- لا يمكن للمستخدم حذف حساب آخر إلا بعد **تأكيد العملية** من خلال رسالة تأكيد.

⚠️ **تحذير**: عدم تطبيق تحكم سليم في الوصول قد يؤدي إلى **ثغرات أمنية خطيرة** مثل **Broken Access Control**، والتي تُعتبر من أخطر ثغرات تطبيقات الويب.

---
## 🔴 ثغرة Broken Access Control

تحدث ثغرات **التحكم المكسور في الوصول (Broken Access Control)** عندما يتمكن المستخدمون من تنفيذ إجراءات أو الوصول إلى بيانات خارج نطاق الصلاحيات المخصصة لهم.  
يمكن أن يؤدي ذلك إلى:
- كشف معلومات حساسة.
- الوصول غير المصرح به للموارد.
- تعديل أو حذف البيانات دون إذن.

---

## 🔥 تصعيد الامتيازات الأفقي (Horizontal Privilege Escalation)

يحدث **تصعيد الامتيازات الأفقي** عندما يتمكن المهاجم من الوصول إلى **موارد مستخدم آخر** بنفس مستوى الصلاحيات، بسبب ضعف في التحكم بالوصول.

### 📌 مثال عملي:
1. أليس لديها حساب بنكي على موقع vulnerable-website.com ويمكنها الوصول إلى بيانات حسابها عبر الرابط:

https://vulnerable-website.com/myaccount?id=123

2. المهاجم، الذي لديه أيضًا حساب على الموقع، يغيّر رقم **id** في الرابط إلى رقم مستخدم آخر:

https://vulnerable-website.com/myaccount?id=124

3. إذا لم يكن هناك **تحقق مناسب من الصلاحيات**، سيتمكن المهاجم من الاطلاع على بيانات حساب أليس.

---

## ⚠️ كيف يتم منع هذه الثغرات؟
- التحقق من صلاحيات كل مستخدم قبل تنفيذ أي طلب.
- عدم الاعتماد على **معرفات URL فقط** لتحديد الوصول إلى البيانات.
- استخدام **Token-Based Authentication** بدلاً من تمرير المعرفات في الروابط.
---

## 🔥 تصعيد الامتيازات الرأسي (Vertical Privilege Escalation)

يحدث **تصعيد الامتيازات الرأسي** عندما يتمكن المهاجم من الوصول إلى **وظائف إدارية أو ذات امتيازات عالية** داخل التطبيق، رغم أنه لا يملك الصلاحيات المطلوبة.

### 📌 مثال عملي:
1. **المسؤول (Administrator)** لديه صلاحية الوصول إلى **لوحة التحكم (Admin Panel)** عبر الرابط:

https://vulnerable-website.com/login/home.jsp?admin=true

2. **المهاجم (Attacker)**، الذي لديه حساب مستخدم عادي، يحاول تعديل **معلمة (Parameter) URL** من:

https://vulnerable-website.com/login/home.jsp?admin=false

إلى:

https://vulnerable-website.com/login/home.jsp?admin=true


3. إذا لم يقم التطبيق بالتحقق من الصلاحيات **بشكل صحيح**، سيتمكن المهاجم من الوصول إلى **لوحة التحكم** والقيام بإجراءات خطيرة مثل:
- عرض بيانات المستخدمين.
- حذف أو إضافة مستخدمين جدد.
- تنفيذ عمليات إدارية لم يكن مسموحًا له بها.

---

## ⚠️ كيف يتم منع هذه الثغرات؟
✅ **عدم الاعتماد على بيانات URL أو المعلمات (Parameters) في تحديد الصلاحيات.**  
✅ **استخدام آليات تحقق قوية على مستوى الخادم (Server-Side Validation).**  
✅ **التحقق من أدوار المستخدم (User Roles) في كل طلب يتم إرساله إلى الخادم.**  
✅ **استخدام **JWT أو OAuth** لضمان أمان عملية المصادقة.**  
✅ **إجراء اختبارات أمنية دورية على التطبيق لكشف هذه الثغرات.**  

## ⚠️ ثغرات التحكم في الوصول في العمليات متعددة الخطوات

تحدث هذه الثغرات عندما يتم تطبيق **قواعد التحكم في الوصول** على بعض الخطوات في العملية، ولكن يتم **تجاهلها في خطوات أخرى**، مما يسمح للمهاجم بتجاوز القيود وتنفيذ إجراءات غير مصرح بها.

---

## 🔥 مثال عملي على حذف المستخدمين

1️⃣ **الخطوة الأولى:**  
   - عند محاولة حذف مستخدم، يتم إرسال طلب إلى:
```http
/confirm
```
   - يعرض التطبيق رسالة تأكيد للمستخدم قبل الحذف.

2️⃣ **الخطوة الثانية:**  
   - بعد الضغط على "نعم"، يتم إرسال طلب إلى:
```http
 /delete
```
   - يقوم التطبيق بحذف المستخدم المحدد.

### 📌 أين تكمن المشكلة؟
إذا لم يتم التحقق من صلاحيات المستخدم عند إرسال طلب `/delete`، يمكن للمهاجم **تخطي خطوة التأكيد** وإرسال الطلب مباشرة، حتى لو لم يكن لديه الصلاحيات المناسبة.

---

## ✅ كيف يتم منع هذه الثغرات؟
- **التحقق من الصلاحيات في كل خطوة، وليس فقط في الواجهة الأمامية.**  
- **عدم الاعتماد على الخطوات التسلسلية كآلية وحيدة للتحكم في الوصول.**  
- **استخدام رموز تحقق (CSRF Tokens) لضمان أن الطلبات تأتي من جلسة مستخدم موثوقة.**  
- **تنفيذ التحقق من صلاحيات المستخدم على مستوى الخادم (Server-Side Validation).**  
---

## 🔥 أمثلة على ثغرات التحكم في الوصول مع نماذج للطلبات (Requests & URLs)

### 1️⃣ تجاوز التحقق من التحكم في الوصول عبر تعديل المعلمات في الـ URL أو الصفحة
**📌 المشكلة**: يمكن للمهاجم تعديل معلمات الطلب يدويًا للوصول إلى موارد غير مسموح بها.  
**📌 مثال عملي**:
```http
GET https://vulnerable-website.com/user/profile?id=123  # الطلب الصحيح
GET https://vulnerable-website.com/user/profile?id=456  # المهاجم يعدّل ID لرؤية بيانات مستخدم آخر
```

---

### 2️⃣ الوصول إلى API مع نقص في التحكم في الوصول على طرق HTTP (POST, PUT, DELETE)
**📌 المشكلة**: إذا لم يتم فرض صلاحيات على API، فقد يتمكن المهاجم من تنفيذ عمليات غير مصرح بها.  
**📌 مثال عملي**:
```http
DELETE https://vulnerable-website.com/api/deleteUser?id=123  # يجب أن يكون محصورًا على المسؤولين فقط!
```
- إذا لم يكن هناك تحقق مناسب، يمكن لأي مستخدم حذف أي حساب!

---

### 3️⃣ التلاعب بالـ Metadata مثل تعديل أو إعادة إرسال JWT أو Cookies
**📌 المشكلة**: يمكن للمهاجم تعديل أو إعادة استخدام **رموز المصادقة (JWTs) أو ملفات الكوكيز** للوصول غير المصرح به.  
**📌 مثال عملي**:
- **كوكيز غير آمنة يمكن تعديلها**:
```http
Cookie: role=user  # مهاجم يعدلها إلى
Cookie: role=admin  # إذا لم يتم التحقق من صلاحيات الكوكيز، يحصل على صلاحيات مسؤول!
```
- **JWT يتم تعديله يدويًا بعد فك التشفير**:
```json
{
  "alg": "none",
  "typ": "JWT"
}
```
إذا لم يقم الخادم بالتحقق من التوقيع، يمكن استغلال ذلك للوصول كمسؤول.

---

### 4️⃣ استغلال أخطاء CORS للوصول إلى API من نطاق غير موثوق
**📌 المشكلة**: إذا لم يتم تكوين سياسة **CORS** بشكل صحيح، يمكن لمهاجم تنفيذ طلبات من مواقع غير موثوقة.  
**📌 مثال عملي**:
```http
Origin: http://malicious-site.com
```
- إذا كان الخادم يرد بـ:
```http
Access-Control-Allow-Origin: *
```
فهذا يعني أن **أي موقع** يمكنه تنفيذ طلبات على الـ API، مما يعرض البيانات للخطر.

---

### 5️⃣ التنقل القسري إلى صفحات محمية دون مصادقة (Force Browsing)
**📌 المشكلة**: يمكن للمهاجم الوصول إلى صفحات لا يجب أن تكون متاحة بدون تسجيل الدخول.  
**📌 مثال عملي**:
```http
GET https://vulnerable-website.com/admin/dashboard
```
- إذا لم يكن هناك تحقق في **الخادم**، يمكن للمهاجم الوصول إلى الصفحة حتى بدون تسجيل الدخول!

---

## ✅ كيف يتم منع هذه الثغرات؟
- **التحقق من صلاحيات المستخدم على مستوى الخادم، وليس فقط في الواجهة الأمامية.**  
- **عدم الاعتماد على معرفات (IDs) يتم تمريرها عبر الـ URL دون تحقق.**  
- **فرض سياسات CORS صارمة بحيث لا يتم قبول الطلبات إلا من النطاقات الموثوقة.**  
- **استخدام توقيع رقمي (JWT Signature) لمنع التلاعب بالرموز.**  
- **إعادة توجيه المستخدمين غير المصادقين عند محاولة الوصول إلى صفحات محمية.**  
---

## 🔥 تحليل ثغرة التحكم في الوصول في `deleteOrder`

### ⚠️ أين تكمن المشكلة؟
1. **عدم التحقق من هوية المستخدم الحالي**  
   - الكود يحصل على الطلب (`order`) مباشرة من `orderRepository` بدون التحقق مما إذا كان المستخدم الذي ينفذ العملية **هو مالك الطلب**.
   - يمكن لمستخدم غير مصرح له حذف طلب **يخص مستخدمًا آخر**.

2. **لا يوجد تحقق من صلاحيات المستخدم**  
   - السطر:
     ```java
     orderRepository.delete(order);
     ```
     يتم تنفيذه مباشرة **بدون أي تحقق مما إذا كان للمستخدم الصلاحية لحذف الطلب**.

---

### 🛑 كيف يمكن استغلال الثغرة؟
✅ إذا كان التطبيق يسمح للمستخدم بتمرير `id` للطلب يدويًا، يمكن للمهاجم استدعاء:
```java
deleteOrder(12345);  // محاولة حذف طلب لا يخصه
```
إذا كان الطلب `12345` يخص مستخدمًا آخر، **فسيتم حذفه دون مشاكل**، مما يؤدي إلى **خرق خطير في الأمان**.

---

## ✅ كيف يمكن إصلاح الثغرة؟
### 🔒 الحل: التحقق من الصلاحيات قبل الحذف
```java
public boolean deleteOrder(Long id, User currentUser) {
    Order order = orderRepository.getOne(id);
    if (order == null) {
        log.info("Order not found");
        return false;
    }

    User orderOwner = order.getUser();
    if (!orderOwner.getId().equals(currentUser.getId())) {
        log.warn("Unauthorized delete attempt by user {}", currentUser.getId());
        return false;
    }

    orderRepository.delete(order);
    log.info("Deleted order for user {}", orderOwner.getId());
    return true;
}
```

---

## 📌 التعديلات والإصلاحات:
✔️ **إضافة تحقق مما إذا كان المستخدم الحالي هو مالك الطلب (`orderOwner.getId().equals(currentUser.getId())`).**  
✔️ **إضافة رسالة تحذير (`log.warn`) في حال محاولة مستخدم غير مصرح له حذف طلب لا يخصه.**  
✔️ **إضافة `User currentUser` كمعامل، بحيث يتم التحقق من هوية المستخدم الحالي بدلاً من السماح بأي حذف عشوائي.**  

---

## 🔎 كيف تجد ثغرات التحكم في الوصول؟

لكشف ثغرات التحكم في الوصول، يعتمد الأمر على **منظور الاختبار** الذي تتبعه. يمكن تقسيم هذا النوع من الاختبارات إلى نوعين رئيسيين:  

---

### 1️⃣ الاختبار الأسود (Black Box Testing)

- **تحديد التطبيق**: يجب عليك فهم كيف يتفاعل التطبيق مع **النظام الأساسي**.
- **تحليل آلية التحكم في الوصول**: افهم كيف يتم تنفيذ **التحكم في الوصول** لكل مستوى صلاحيات.
- **التلاعب بالمعلمات**: جرب التلاعب بالمعلمات في الطلبات التي قد تؤثر على قرارات التحكم في الوصول.
- **التدقيق باستخدام الأدوات**: استخدم الأدوات مثل **Authorize** لتسريع عملية الاختبار.

📌 **مثال عملي**:
- جرب تغيير معلمات URL أو المعلمات في POST أو PUT.
- جرب الوصول إلى صفحات غير مصرح بها وتحقق من سلوك النظام.

---

### 2️⃣ الاختبار الأبيض (White Box Testing)

- في هذا النوع من الاختبار، يتم إتاحة الكود والمعلومات الداخلية عن التطبيق، مما يسمح بالتحقق المباشر من طرق التحكم في الوصول داخل الكود نفسه.
---
## 📝 White-Box Testing (الاختبار الأبيض)

في **الاختبار الأبيض**، يتم فحص الكود البرمجي للتطبيق لتحديد كيفية تنفيذ التحكم في الوصول. يتضمن هذا النوع من الاختبار التحقق من النواحي الداخلية للنظام.

---

### ⚠️ المشاكل الشائعة التي يتم اكتشافها خلال هذا الاختبار:
1. **النظام يفتح افتراضيًا (System Defaults to Open)**:
   - يتم تقديم بعض الوظائف والموارد بدون **تحقق مناسب من الوصول**.
  
2. **وجود أو عدم وجود فحوصات تحكم في الوصول على الوظائف أو الموارد**:
   - عدم وجود **تحقق من الوصول** للوظائف المهمة قد يترك النظام عرضة لهجمات.
  
3. **قواعد تحكم الوصول المفقودة للطرق مثل POST و PUT و DELETE في الـ API**:
   - إذا لم يتم فرض تحكم في الوصول على هذه الطرق، يمكن للمتسلل إرسال طلبات غير مصرح بها لتعديل أو حذف البيانات.

4. **الاعتماد الكامل على المدخلات من جانب العميل لاتخاذ قرارات التحكم في الوصول**:
   - هذا يجعل التطبيق عرضة للهجمات، لأن المهاجم يمكنه تعديل المدخلات في المتصفح للتلاعب بالنظام.

5. **التحقق من الثغرات في التحكم في الوصول في التطبيق أثناء التشغيل**:
   - اختبار التطبيق أثناء تشغيله يساعد في التحقق من مدى فاعلية تنفيذ التحكم في الوصول.

---
# automation tool for  testing Broken Access Control 

![image](https://github.com/user-attachments/assets/80c4fd36-1021-414d-9a45-781e98485999)
