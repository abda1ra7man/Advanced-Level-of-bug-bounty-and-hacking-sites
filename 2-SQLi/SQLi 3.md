
### شرح الأوامر

#### 1. الأمر الأول
```
sqlmap -u https://hossamshady11.github.io --headers="Cookie: *" -H "x-forwarded-for: *" -H "x-forwarded-Host: *"
```

##### شرح الخيارات:
- **`-u`**: تحديد الـ URL المراد اختباره.
- **`--headers="Cookie: *"`**: اختبار الكوكيز المرسلة ضمن الهيدرز.
- **`-H "x-forwarded-for: *"`**: فحص قيمة الـ `x-forwarded-for`، وهي حقل يستخدم في إرسال عنوان IP.
- **`-H "x-forwarded-Host: *"`**: فحص قيمة الـ `x-forwarded-Host`، وهي حقل يستخدم في إرسال اسم الهوست.

---

#### 2. الأمر الثاني
```bash
sqlmap -r request.txt --batch --random-agent --tamper=space2comment,randomcase --drop-set-cookie --threads 10 --technique=BEUSTQ --level 5 --risk 3 --dbs --banner --ignore-code 500
```

##### شرح الخيارات:
- **`-r request.txt`**: استخدام ملف يحتوي على الطلب (Request) لتحليل الثغرات.
- **`--batch`**: تشغيل SQLMap تلقائيًا بدون طلب تأكيدات من المستخدم.
- **`--random-agent`**: تغيير الـ User-Agent عشوائيًا لتجنب الحظر.
- **`--tamper=space2comment,randomcase`**: استخدام تقنيات التمويه:
  - `space2comment`: استبدال المسافات بتعليقات.
  - `randomcase`: تغيير الحروف الكبيرة والصغيرة بشكل عشوائي.
- **`--drop-set-cookie`**: تجاهل إعدادات الكوكيز.
- **`--threads 10`**: تحسين الأداء باستخدام 10 خيوط معالجة.
- **`--technique=BEUSTQ`**: تحديد تقنيات الاختبار المستخدمة:
  - **B**: Boolean-based blind.
  - **E**: Error-based.
  - **U**: Union query-based.
  - **S**: Stacked queries.
  - **T**: Time-based blind.
  - **Q**: Inline queries.
- **`--level 5`**: رفع مستوى التفاصيل في الاختبار.
- **`--risk 3`**: زيادة مستوى المخاطر لتحليل شامل.
- **`--dbs`**: جلب قواعد البيانات الموجودة.
- **`--banner`**: جلب معلومات بانر قاعدة البيانات.
- **`--ignore-code 500`**: تجاهل أخطاء HTTP 500.

---

#### الاستخدام:
- **الأمر الأول**: لتحليل الكوكيز والهيدرز واختبار جدران الحماية.
- **الأمر الثاني**: لتحليل شامل مع تحسين الأداء وتمويه الطلبات.
