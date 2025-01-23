# PHP

- دي لغة بتربط قواعد البيانات بالجزء اللي بيظهر للمستخدم، وده مهم جدًا لأي حد مهتم بالاختراق.
- لازم نفهم الأساسيات ونعرف ازاي نربط الجزء اللي بيظهر للمستخدم بقاعدة البيانات عشان نقدر نكتشف الثغرات الموجودة ونعمل مراجعة للكود.
-------
# إعداد خادم PHP

- أول خطوة إننا نجهز خادم PHP عشان الموقع يشتغل.
- على نظام Linux ممكن نستخدم Apache أو Nginx.
- الأوامر المطلوبة لتشغيل Apache:
  1. تثبيت Apache: `sudo apt install apache2`
  2. تشغيل الخدمة: `sudo systemctl start apache2`
  3. تفعيل الخدمة: `sudo systemctl enable apache2`
  4. تثبيت إضافات PHP: `sudo apt install php libapache2-mod-php`
-------
# إنشاء ملف PHP

- بعد تثبيت خادم Apache، تروح على المسار `/var/www/html`.
```
cd /var/www/html
```
- تبدأ بإنشاء ملف اسمه `index.php`.
```
sudo nano index.php
```
- ممكن تبدأ بكتابة كود بسيط زي ده:

```php
<!DOCTYPE html>
<html>
<body>
<?php
echo "My first PHP script!";
?>
</body>
</html>
```
- هتوصل للصفحة الي انتا عملتها باللينك ده :

```
http://your_ipaddress/index.php
      OR
http://localhost/index.php

```

----
# توضيح الفرق بين الحروف الكبيرة والصغيرة في PHP واستخدام النقطة

- الكود ده بيعرف متغير اسمه `$color` وبيحط قيمته "red".
- **PHP بتفرق بين الحروف الكبيرة والصغيرة في أسماء المتغيرات**.
- النقطة (`.`) بتستخدم لدمج النصوص مع المتغيرات أو النصوص الأخرى.

## مثال:
  ```php
  <?php
  $color = "red";
  echo "My car is " . $color . "<br>"; // هيطبع: My car is red
  echo "My house is " . $COLOR . "<br>"; // مش هيطبع حاجة لأن $COLOR غير معرف
  echo "My boat is " . $coLOR . "<br>"; // مش هيطبع حاجة لأن $coLOR غير معرف
  ?>
```
---------
# مفهوم الدوال في PHP

- **الدوال**: طريقة لإعادة استخدام الكود عن طريق تعريف وظيفة (Function) معينة وتنفيذها عند الحاجة.

## مثال:
  ```php
  <?php
  function myMessage($message) {
      echo $message;
  }

  myMessage("Hello world!"); // استدعاء الدالة
  ?>
```
# إرسال بيانات من HTML إلى PHP

- **الفورم** بيتم استخدامه لإرسال البيانات إلى ملف PHP لمعالجتها.

## الكود:
  ```html
  <form action="file.php" method="POST">
      <input name="username" placeholder="username">
      <input name="password" placeholder="password">
      <input type="submit">
  </form>
```
# استقبال بيانات الفورم في PHP

- الكود ده بيستقبل البيانات اللي تم إرسالها من الفورم اللي بتستخدم طريقة `POST`.

## الكود:
  ```php
<?php

$usr = $_POST["username"];
$pass = $_POST["password"];

echo "usernmae = " . $usr . "<br>";
echo "password = " . $pass . "<br>";
echo "<script>alert(2)</script>";
?>
```
---
# قراءة الملفات في PHP

- الكود ده بيقرأ محتوى ملف نصي ويعرضه على المتصفح.

## الكود:
```php
<?php
$filename = "webdictionary.txt"; // اسم الملف
$myfile = fopen($filename, "r"); // فتح الملف في وضع القراءة

echo fread($myfile, filesize($filename)); // قراءة محتوى الملف
fclose($myfile); // إغلاق الملف
?>
```

# تخزين بيانات المستخدم في ملف نصي باستخدام PHP

## الكود:
```php
<?php
$username = $_POST['user']; // استقبال اسم المستخدم من الفورم
$password = $_POST['pass']; // استقبال كلمة المرور من الفورم

$i = fopen("test.txt", "a"); // فتح الملف في وضع الإضافة (Append)
$data = "username: " . $username . " and password is: " . $password . "\n"; // تجهيز البيانات
$r = fwrite($i, $data); // كتابة البيانات في الملف
fclose($i); // إغلاق الملف
?>
```
