<div dir="rtl" align="right">

# شناخت رفتار نرمال: راهنمای جامع تحلیل فرآیندهای حیاتی ویندوز برای شکار تهدیدات

## 📋 فهرست مطالب
1. [مقدمه](#مقدمه)
2. [اهمیت شناخت رفتار نرمال](#اهمیت-شناخت-رفتار-نرمال)
3. [فرآیندهای حیاتی ویندوز](#فرآیندهای-حیاتی-ویندوز)
4. [قوانین شناسایی و چک‌لیست عملیاتی](#قوانین-شناسایی-و-چک‌لیست-عملیاتی)
5. [ابزارهای تحلیل و نظارت](#ابزارهای-تحلیل-و-نظارت)
6. [سناریوهای واقعی](#سناریوهای-واقعی)
7. [منابع و مراجع](#منابع-و-مراجع)

---

## مقدمه

در دنیای امروز امنیت سایبری، شناسایی به‌موقع تهدیدات پیشرفته (<bdi>Advanced Persistent Threats - APT</bdi>) و حملات باج‌افزاری (<bdi>Ransomware</bdi>) به یکی از چالش‌های اصلی تیم‌های <bdi>SOC</bdi> و <bdi>Incident Response</bdi> تبدیل شده است. مهاجمان مدرن از تکنیک‌های پیچیده‌ای مانند <bdi>Living off the Land (LotL)</bdi>، <bdi>Process Injection</bdi> و <bdi>Masquerading</bdi> استفاده می‌کنند تا فعالیت‌های مخرب خود را در پوشش فرآیندهای مشروع سیستم پنهان کنند.

**پایه اصلی شکار تهدیدات** (<bdi>Threat Hunting</bdi>) و تحلیل رفتاری (<bdi>Behavioral Analysis</bdi>)، شناخت دقیق و عمیق از **رفتار نرمال** سیستم‌عامل است. بدون این شناخت، تشخیص ناهنجاری‌ها تقریباً غیرممکن خواهد بود.

این مقاله به بررسی جامع فرآیندهای حیاتی ویندوز، روابط والد-فرزندی آن‌ها، حساب‌های کاربری مجاز، آرگومان‌های خط فرمان و نشانه‌های امنیتی مشکوک می‌پردازد. همچنین راهکارهای عملی برای شناسایی خودکار و پاسخ به تهدیدات ارائه می‌شود.

### اهداف این راهنما:
- **آموزش عمیق** ویژگی‌های فرآیندهای حیاتی ویندوز
- **ارائه راهکارهای عملی** برای شناسایی خودکار تهدیدات
- **تقویت مهارت‌های Threat Hunting** با دانش مستند و کاربردی
- **کاهش زمان شناسایی** (<bdi>Mean Time to Detect - MTTD</bdi>) در حوادث امنیتی

---

## اهمیت شناخت رفتار نرمال

### چرا رفتار نرمال اهمیت دارد؟

مطابق چارچوب <bdi>MITRE ATT&CK</bdi>، مهاجمان از تکنیک‌های متعددی برای پنهان‌سازی استفاده می‌کنند:

| تکنیک مهاجم | شناسه MITRE | توضیح |
|-------------|-------------|--------|
| <bdi>Process Injection</bdi> | T1055 | تزریق کد به فرآیندهای مشروع |
| <bdi>Masquerading</bdi> | T1036 | جعل هویت با نام‌های مشابه فرآیندها |
| <bdi>Process Hollowing</bdi> | T1055.012 | خالی کردن حافظه فرآیند و جایگزینی کد مخرب |
| <bdi>DLL Injection</bdi> | T1055.001 | تزریق کتابخانه‌های مخرب |
| <bdi>Credential Dumping</bdi> | T1003.001 | استخراج اعتبارنامه‌ها از حافظه <bdi>LSASS</bdi> |

**بدون شناخت رفتار نرمال:**
- فرآیند `svchost.exe` بدون پارامتر <bdi>`-k`</bdi> قابل تشخیص نیست
- فرآیند `lsass.exe` با والد غیرمعمول شناسایی نمی‌شود
- <bdi>Process Masquerading</bdi> با نام‌هایی مانند `csrsss.exe` (با سه حرف s) از دید تحلیلگران پنهان می‌ماند

---

## فرآیندهای حیاتی ویندوز

### 1. فرآیند System و Memory Compression

#### System (ntoskrnl.exe)

<table dir="rtl">
<tr>
<th>ویژگی</th>
<th>مقدار نرمال</th>
</tr>
<tr>
<td><b>نام فرآیند</b></td>
<td><code>System</code></td>
</tr>
<tr>
<td><b>شناسه فرآیند (PID)</b></td>
<td>4 (همیشه ثابت)</td>
</tr>
<tr>
<td><b>مسیر اجرایی</b></td>
<td><code>ntoskrnl.exe</code> در <bdi>Task Manager</bdi> / <code>None</code> در <bdi>Process Explorer</bdi></td>
</tr>
<tr>
<td><b>والد</b></td>
<td>هیچ (این فرآیند ریشه است)</td>
</tr>
<tr>
<td><b>فرزندان</b></td>
<td>فقط <code>smss.exe</code> و <code>Memory Compression</code></td>
</tr>
<tr>
<td><b>حساب کاربری</b></td>
<td><bdi>NT AUTHORITY\SYSTEM</bdi></td>
</tr>
<tr>
<td><b>Session ID</b></td>
<td>0</td>
</tr>
</table>

**توضیحات تکمیلی:**

فرآیند <bdi>System</bdi> در واقع یک فرآیند واقعی در فضای کاربر (<bdi>User Space</bdi>) نیست، بلکه نمایشی از فعالیت‌های هسته سیستم‌عامل (<bdi>Kernel Mode Threads</bdi>) است. این فرآیند میزبان تمامی ترد‌های حالت هسته بوده و نقش بسیار حیاتی در عملکرد سیستم دارد.

**یکی از ویژگی‌های منحصربه‌فرد:** <bdi>PID</bdi> این فرآیند همیشه 4 است و در تمام نسخه‌های ویندوز از <bdi>Windows NT</bdi> تا <bdi>Windows 11</bdi> تغییر نکرده است.

#### Memory Compression

<table dir="rtl">
<tr>
<th>ویژگی</th>
<th>مقدار نرمال</th>
</tr>
<tr>
<td><b>نام فرآیند</b></td>
<td><code>Memory Compression</code></td>
</tr>
<tr>
<td><b>مسیر اجرایی</b></td>
<td><code>%SystemRoot%\System32\ntoskrnl.exe</code></td>
</tr>
<tr>
<td><b>والد</b></td>
<td><code>System (PID 4)</code></td>
</tr>
<tr>
<td><b>فرزندان</b></td>
<td>هیچ</td>
</tr>
<tr>
<td><b>حساب کاربری</b></td>
<td><bdi>NT AUTHORITY\SYSTEM</bdi></td>
</tr>
<tr>
<td><b>تعداد نمونه</b></td>
<td>فقط یک نمونه</td>
</tr>
<tr>
<td><b>نسخه ویندوز</b></td>
<td><bdi>Windows 10+</bdi></td>
</tr>
</table>

**توضیحات تکمیلی:**

این فرآیند از <bdi>Windows 10</bdi> به بعد اضافه شده و وظیفه فشرده‌سازی حافظه فرآیندها در <bdi>RAM</bdi> را بر عهده دارد. به جای <bdi>Paging</bdi> داده‌ها به دیسک (که کند است)، ویندوز ابتدا حافظه را فشرده می‌کند تا عملکرد سیستم بهبود یابد.

**🚨 علائم هشدار امنیتی:**

| نشانه مشکوک | احتمال تهدید | اقدام توصیه‌شده |
|-------------|--------------|-----------------|
| وجود فرزند غیر از `smss.exe` و `Memory Compression` | بسیار بالا | بررسی فوری با <bdi>Process Explorer</bdi> و تحلیل <bdi>Memory Dump</bdi> |
| تغییر <bdi>PID</bdi> از 4 به عدد دیگر | بسیار بالا | احتمال <bdi>Rootkit</bdi> - نیاز به تحلیل عمیق |
| مصرف <bdi>CPU</bdi> یا <bdi>Network</bdi> غیرعادی | متوسط تا بالا | بررسی <bdi>Driver</bdi>های بارگذاری‌شده با `driverquery` |

---

### 2. Session Manager Subsystem (smss.exe)

<table dir="rtl">
<tr>
<th>ویژگی</th>
<th>مقدار نرمال</th>
</tr>
<tr>
<td><b>نام فرآیند</b></td>
<td><code>smss.exe</code></td>
</tr>
<tr>
<td><b>مسیر اجرایی</b></td>
<td><code>%SystemRoot%\System32\smss.exe</code></td>
</tr>
<tr>
<td><b>والد</b></td>
<td><code>System (PID 4)</code></td>
</tr>
<tr>
<td><b>فرزندان</b></td>
<td>• نمونه‌های فرزند <code>smss.exe</code> (موقت)<br>• <code>csrss.exe</code> (Session 0 و 1)<br>• <code>wininit.exe</code> (Session 0)<br>• <code>winlogon.exe</code> (Session 1)</td>
</tr>
<tr>
<td><b>حساب کاربری</b></td>
<td><bdi>NT AUTHORITY\SYSTEM</bdi></td>
</tr>
<tr>
<td><b>آرگومان خط فرمان</b></td>
<td>نمونه اصلی: بدون آرگومان<br>نمونه‌های فرزند: دارای آرگومان</td>
</tr>
<tr>
<td><b>تعداد نمونه پایدار</b></td>
<td>فقط یک نمونه اصلی</td>
</tr>
</table>

**توضیحات تکمیلی:**

<bdi>smss.exe</bdi> اولین فرآیند حالت کاربر (<bdi>User Mode</bdi>) در ویندوز است و نقش کلیدی در راه‌اندازی سیستم دارد. این فرآیند مسئولیت‌های متعددی دارد:

- **ایجاد متغیرهای محیطی** (<bdi>Environment Variables</bdi>) سیستم
- **مقداردهی اولیه رجیستری** (هایوهای <bdi>HKLM\Software</bdi>، <bdi>SAM</bdi>، <bdi>Security</bdi>)
- **ایجاد توصیفگرهای امنیتی** (<bdi>Security Descriptors</bdi>)
- **راه‌اندازی زیرسیستم‌های ویندوز**

**معماری اجرا:**

```
System (PID 4)
    └── smss.exe (Master Instance - بدون آرگومان)
        ├── smss.exe (Session 0 - موقت)
        │   ├── csrss.exe (Session 0)
        │   └── wininit.exe (Session 0)
        └── smss.exe (Session 1 - موقت)
            ├── csrss.exe (Session 1)
            └── winlogon.exe (Session 1)
```

پس از ایجاد فرآیندهای <bdi>csrss.exe</bdi>، <bdi>wininit.exe</bdi> و <bdi>winlogon.exe</bdi>، نمونه‌های فرزند <bdi>smss.exe</bdi> خاتمه می‌یابند و تنها نمونه اصلی باقی می‌ماند.

**🚨 علائم هشدار امنیتی:**

| نشانه مشکوک | احتمال تهدید | تکنیک MITRE | اقدام توصیه‌شده |
|-------------|--------------|-------------|-----------------|
| بیش از یک نمونه پایدار بدون آرگومان | بسیار بالا | T1036.004 | بررسی <bdi>Hash</bdi> فایل با <bdi>VirusTotal</bdi> |
| والد غیر از `System` | بسیار بالا | T1055 | احتمال <bdi>Process Injection</bdi> |
| مسیر اجرایی خارج از `System32` | بسیار بالا | T1036.005 | قرنطینه فوری و تحلیل <bdi>Malware</bdi> |
| نام‌های مشابه: `smsss.exe`, `smss32.exe` | بسیار بالا | T1036.004 | <bdi>Masquerading</bdi> - حذف فوری |

**مثال Command Line نرمال:**
```
C:\Windows\System32\smss.exe
```

**مثال Command Line مشکوک:**
```
C:\Windows\System32\smss.exe \SystemRoot\System32\csrss.exe ObjectDirectory=\Windows SharedSection=1024,20480,768
```
*(نمونه فرزند که نباید پایدار بماند)*

---

### 3. Client/Server Runtime Subsystem (csrss.exe)

<table dir="rtl">
<tr>
<th>ویژگی</th>
<th>مقدار نرمال</th>
</tr>
<tr>
<td><b>نام فرآیند</b></td>
<td><code>csrss.exe</code></td>
</tr>
<tr>
<td><b>مسیر اجرایی</b></td>
<td><code>%SystemRoot%\System32\csrss.exe</code></td>
</tr>
<tr>
<td><b>والد</b></td>
<td>یتیم (<bdi>Orphan</bdi>) - والد اصلی: نمونه فرزند <code>smss.exe</code></td>
</tr>
<tr>
<td><b>فرزندان</b></td>
<td><b>هیچ</b> (هرگز نباید فرزند داشته باشد)</td>
</tr>
<tr>
<td><b>حساب کاربری</b></td>
<td><bdi>NT AUTHORITY\SYSTEM</bdi></td>
</tr>
<tr>
<td><b>تعداد نمونه</b></td>
<td>حداقل 2 (یکی برای هر <bdi>Session</bdi>)</td>
</tr>
<tr>
<td><b>Session ID</b></td>
<td>• Session 0: سرویس‌های سیستم<br>• Session 1+: کاربران تعاملی</td>
</tr>
</table>

**توضیحات تکمیلی:**

<bdi>csrss.exe</bdi> زیرسیستم <bdi>Windows</bdi> را در حالت کاربر پیاده‌سازی می‌کند و وظایف حیاتی متعددی دارد:

- **مدیریت فرآیندها و ترد‌ها**
- **وارد کردن DLLهای Windows API**
- **مدیریت خاموش شدن GUI**
- **مدیریت کنسول (Console Windowing)**

**چرا csrss.exe یتیم است؟**

والد اصلی <bdi>csrss.exe</bdi> یک نمونه فرزند از <bdi>smss.exe</bdi> است که پس از ایجاد <bdi>csrss.exe</bdi> خاتمه می‌یابد. بنابراین در ابزارهایی مانند <bdi>Process Explorer</bdi>، <bdi>csrss.exe</bdi> بدون والد (<bdi>Non-existent Parent PID</bdi>) نمایش داده می‌شود.

**تعداد نمونه‌ها:**

| سناریو | تعداد نمونه csrss.exe |
|--------|----------------------|
| سیستم معمولی (یک کاربر) | 2 (Session 0 + Session 1) |
| استفاده از <bdi>Remote Desktop (RDP)</bdi> | 2 + تعداد کاربران RDP |
| استفاده از <bdi>Fast User Switching</bdi> | 2 + تعداد کاربران همزمان |

**🚨 علائم هشدار امنیتی:**

| نشانه مشکوک | احتمال تهدید | تکنیک MITRE | اقدام توصیه‌شده |
|-------------|--------------|-------------|-----------------|
| وجود فرآیند فرزند | بسیار بالا | T1055.012 | احتمال <bdi>Process Hollowing</bdi> |
| والد فعال (غیر یتیم) | بسیار بالا | T1055 | بررسی فوری والد |
| مسیر اجرایی خارج از `System32` | بسیار بالا | T1036.005 | قرنطینه فوری |
| نام‌های مشابه: `csrsss.exe`, `cssrss.exe` | بسیار بالا | T1036.004 | <bdi>Masquerading</bdi> |
| اجرا با حساب غیر از <bdi>SYSTEM</bdi> | بسیار بالا | T1134 | احتمال <bdi>Token Manipulation</bdi> |

**مثال Command Line نرمال:**
```
%SystemRoot%\system32\csrss.exe ObjectDirectory=\Windows SharedSection=1024,20480,768 Windows=On SubSystemType=Windows ServerDll=basesrv,1 ServerDll=winsrv:UserServerDllInitialization,3 ServerDll=sxssrv,4 ProfileControl=Off MaxRequestThreads=16
```

---

### 4. Windows Initialization Process (wininit.exe)

<table dir="rtl">
<tr>
<th>ویژگی</th>
<th>مقدار نرمال</th>
</tr>
<tr>
<td><b>نام فرآیند</b></td>
<td><code>wininit.exe</code></td>
</tr>
<tr>
<td><b>مسیر اجرایی</b></td>
<td><code>%SystemRoot%\System32\wininit.exe</code></td>
</tr>
<tr>
<td><b>والد</b></td>
<td>یتیم - والد اصلی: نمونه فرزند <code>smss.exe</code> (Session 0)</td>
</tr>
<tr>
<td><b>فرزندان</b></td>
<td>• <code>services.exe</code><br>• <code>lsass.exe</code><br>• <code>fontdrvhost.exe</code></td>
</tr>
<tr>
<td><b>حساب کاربری</b></td>
<td><bdi>NT AUTHORITY\SYSTEM</bdi></td>
</tr>
<tr>
<td><b>Session ID</b></td>
<td>0</td>
</tr>
<tr>
<td><b>تعداد نمونه</b></td>
<td>فقط یک نمونه</td>
</tr>
</table>

**توضیحات تکمیلی:**

<bdi>wininit.exe</bdi> مسئول مقداردهی اولیه و راه‌اندازی اجزای حیاتی سیستم است:

- **تنظیم متغیرهای محیطی**: <bdi>USERPROFILE</bdi>, <bdi>ALLUSERPROFILE</bdi>, <bdi>PUBLIC</bdi>, <bdi>ProgramData</bdi>
- **راه‌اندازی LSASS**: ایجاد فرآیند `lsass.exe` و تنظیم کلید رمزنگاری <bdi>LSA</bdi>
- **راه‌اندازی Service Control Manager**: ایجاد فرآیند `services.exe`
- **ایجاد دایرکتوری موقت**: `%SystemRoot%\Temp`

**🚨 علائم هشدار امنیتی:**

| نشانه مشکوک | احتمال تهدید | اقدام توصیه‌شده |
|-------------|--------------|-----------------|
| بیش از یک نمونه | بسیار بالا | بررسی فوری - احتمال <bdi>Malware</bdi> |
| والد فعال (غیر یتیم) | بسیار بالا | تحلیل <bdi>Process Tree</bdi> |
| عدم وجود فرزندان انتظار رفته | بالا | احتمال <bdi>Process Termination</bdi> توسط مهاجم |
| وجود فرزندان غیرمعمول | بالا | بررسی دقیق فرآیندهای فرزند |

---

### 5. Service Control Manager (services.exe)

<table dir="rtl">
<tr>
<th>ویژگی</th>
<th>مقدار نرمال</th>
</tr>
<tr>
<td><b>نام فرآیند</b></td>
<td><code>services.exe</code></td>
</tr>
<tr>
<td><b>مسیر اجرایی</b></td>
<td><code>%SystemRoot%\System32\services.exe</code></td>
</tr>
<tr>
<td><b>والد</b></td>
<td><code>wininit.exe</code></td>
</tr>
<tr>
<td><b>فرزندان</b></td>
<td>• تمام نمونه‌های <code>svchost.exe</code><br>• <code>spoolsv.exe</code><br>• <code>SearchIndexer.exe</code><br>• سرویس‌های مستقل دیگر</td>
</tr>
<tr>
<td><b>حساب کاربری</b></td>
<td><bdi>NT AUTHORITY\SYSTEM</bdi></td>
</tr>
<tr>
<td><b>Session ID</b></td>
<td>0</td>
</tr>
<tr>
<td><b>تعداد نمونه</b></td>
<td>فقط یک نمونه</td>
</tr>
</table>

**توضیحات تکمیلی:**

<bdi>Service Control Manager (SCM)</bdi> قلب سیستم مدیریت سرویس‌های ویندوز است. این فرآیند مسئولیت‌های حیاتی زیر را دارد:

- **شروع و توقف سرویس‌ها** (<bdi>Start, Stop, Pause, Resume</bdi>)
- **مدیریت وابستگی‌های سرویس‌ها** (<bdi>Service Dependencies</bdi>)
- **پیاده‌سازی Unified Background Process Manager (UBPM)**
- **تنظیم Last Known Good Configuration** پس از بوت موفق

**موقعیت رجیستری سرویس‌ها:**
```
HKLM\SYSTEM\CurrentControlSet\Services\
```

هر سرویس یک کلید مجزا در این مسیر دارد که شامل اطلاعات زیر است:
- `ImagePath`: مسیر فایل اجرایی
- `Start`: نوع راه‌اندازی (<bdi>Automatic, Manual, Disabled</bdi>)
- `Type`: نوع سرویس (<bdi>Win32OwnProcess, Win32ShareProcess</bdi>)
- `ServiceDll`: مسیر <bdi>DLL</bdi> برای سرویس‌های <bdi>svchost.exe</bdi>

**🚨 علائم هشدار امنیتی:**

| نشانه مشکوک | احتمال تهدید | تکنیک MITRE | اقدام توصیه‌شده |
|-------------|--------------|-------------|-----------------|
| بیش از یک نمونه | بسیار بالا | T1036 | بررسی فوری |
| والد غیر از `wininit.exe` | بسیار بالا | T1055 | احتمال <bdi>Process Injection</bdi> |
| فرزندان غیرمعمول (غیرسرویس) | بالا | T1543.003 | بررسی سرویس‌های نصب‌شده |
| تغییر در رجیستری سرویس‌ها | متوسط | T1543.003 | <bdi>Windows Service Persistence</bdi> |

**نکته مهم:** پس از ورود موفق کاربر، <bdi>SCM</bdi> بوت را موفق تلقی کرده و مقدار `HKLM\SYSTEM\Select\LastKnownGood` را به `CurrentControlSet` تنظیم می‌کند.

---

### 6. Generic Host Process for Services (svchost.exe)

<table dir="rtl">
<tr>
<th>ویژگی</th>
<th>مقدار نرمال</th>
</tr>
<tr>
<td><b>نام فرآیند</b></td>
<td><code>svchost.exe</code></td>
</tr>
<tr>
<td><b>مسیر اجرایی</b></td>
<td><code>%SystemRoot%\System32\svchost.exe</code></td>
</tr>
<tr>
<td><b>والد</b></td>
<td><code>services.exe</code></td>
</tr>
<tr>
<td><b>فرزندان</b></td>
<td>معمولاً هیچ (به جز موارد خاص)</td>
</tr>
<tr>
<td><b>حساب کاربری</b></td>
<td>• <bdi>SYSTEM</bdi><br>• <bdi>LOCAL SERVICE</bdi><br>• <bdi>NETWORK SERVICE</bdi><br>• حساب‌های کاربری دلخواه</td>
</tr>
<tr>
<td><b>آرگومان‌های الزامی</b></td>
<td><code>-k &lt;ServiceGroup&gt;</code> (همیشه)</td>
</tr>
<tr>
<td><b>تعداد نمونه</b></td>
<td><bdi>Windows 10 1703-</bdi>: 10-20<br><bdi>Windows 10 1703+</bdi>: 50-100+</td>
</tr>
</table>

**توضیحات تکمیلی:**

<bdi>svchost.exe</bdi> یک میزبان عمومی برای سرویس‌هایی است که به صورت <bdi>DLL</bdi> پیاده‌سازی شده‌اند. به دلیل کاهش مصرف منابع، ویندوز بسیاری از سرویس‌ها را به صورت <bdi>DLL</bdi> نوشته و آن‌ها را در نمونه‌های مختلف <bdi>svchost.exe</bdi> اجرا می‌کند.

**آرگومان‌های خط فرمان:**

| پارامتر | توضیح | مثال |
|---------|-------|------|
| <code>-k</code> | گروه سرویس (<bdi>Service Host Group</bdi>) - <b>الزامی</b> | <code>-k netsvcs</code> |
| <code>-s</code> | نام سرویس خاص | <code>-s LanmanServer</code> |
| <code>-p</code> | اجرای سیاست (<bdi>Policy Enforcement</bdi>) | <code>-p</code> |

**گروه‌های سرویس معمول:**

```
-k DcomLaunch          → DCOM و RPC
-k RPCSS               → RPC Endpoint Mapper
-k LocalService        → سرویس‌های محلی
-k netsvcs             → سرویس‌های شبکه
-k NetworkService      → سرویس‌های شبکه‌ای
-k UnistackSvcGroup    → سرویس‌های Networking Stack
```

**تغییرات مهم در Windows 10 1703:**

قبل از این نسخه، سرویس‌های مشابه در یک نمونه <bdi>svchost.exe</bdi> گروه‌بندی می‌شدند. از <bdi>Windows 10 1703</bdi> به بعد، در سیستم‌هایی با بیش از 3.5 گیگابایت <bdi>RAM</bdi>، هر سرویس نمونه مستقل خود را دارد. این تغییر برای:
- **افزایش قابلیت اطمینان** (مشکل در یک سرویس دیگر سرویس‌ها را تحت تأثیر قرار نمی‌دهد)
- **افزایش امنیت** (ایزوله‌سازی سرویس‌ها)
- **بهبود نظارت و عیب‌یابی**

**چگونه سرویس‌های درون svchost.exe را ببینیم؟**

**روش 1: Process Explorer**
1. دانلود <bdi>Process Explorer</bdi> از <bdi>Sysinternals</bdi>
2. پیدا کردن نمونه <bdi>svchost.exe</bdi>
3. <bdi>Double-click</bdi> → تب <bdi>Services</bdi>

**روش 2: Command Line**
```cmd
tasklist /svc /fi "imagename eq svchost.exe"
```

**روش 3: PowerShell**
```powershell
Get-Process svchost | Select-Object Id, @{Name="Services";Expression={(Get-WmiObject Win32_Service | Where-Object {$_.ProcessId -eq $_.Id}).Name}}
```

**🚨 علائم هشدار امنیتی:**

| نشانه مشکوک | احتمال تهدید | تکنیک MITRE | اقدام توصیه‌شده |
|-------------|--------------|-------------|-----------------|
| اجرا بدون پارامتر `-k` | بسیار بالا | T1036 | بررسی فوری - احتمال <bdi>Malware</bdi> |
| والد غیر از `services.exe` | بسیار بالا | T1055 | احتمال <bdi>Process Injection</bdi> |
| مسیر اجرایی مشکوک | بسیار بالا | T1036.005 | احتمال <bdi>Masquerading</bdi>: `svch0st.exe` |
| مصرف <bdi>CPU/Network</bdi> غیرعادی | متوسط | T1496 | احتمال <bdi>Cryptomining</bdi> یا <bdi>C2 Communication</bdi> |
| اتصالات شبکه به IPهای مشکوک | بالا | T1071 | بررسی <bdi>Network Connections</bdi> با `netstat` |
| سرویس‌های ناشناخته در <bdi>svchost.exe</bdi> | بالا | T1543.003 | بررسی رجیستری سرویس |

**مثال Command Line نرمال:**
```
C:\Windows\System32\svchost.exe -k netsvcs -p -s Schedule
```

**مثال Command Line مشکوک:**
```
C:\Windows\System32\svchost.exe
```
*(بدون پارامتر -k)*

---

### 7. Local Security Authority Subsystem (lsass.exe)

<table dir="rtl">
<tr>
<th>ویژگی</th>
<th>مقدار نرمال</th>
</tr>
<tr>
<td><b>نام فرآیند</b></td>
<td><code>lsass.exe</code></td>
</tr>
<tr>
<td><b>مسیر اجرایی</b></td>
<td><code>%SystemRoot%\System32\lsass.exe</code></td>
</tr>
<tr>
<td><b>والد</b></td>
<td><code>wininit.exe</code></td>
</tr>
<tr>
<td><b>فرزندان</b></td>
<td>به ندرت (استثناء: <bdi>EFS - Encrypting File System</bdi>)</td>
</tr>
<tr>
<td><b>حساب کاربری</b></td>
<td><bdi>NT AUTHORITY\SYSTEM</bdi></td>
</tr>
<tr>
<td><b>Session ID</b></td>
<td>0</td>
</tr>
<tr>
<td><b>تعداد نمونه</b></td>
<td>فقط یک نمونه</td>
</tr>
<tr>
<td><b>Protected Process</b></td>
<td>بله (<bdi>Windows 8.1+</bdi>)</td>
</tr>
</table>

**توضیحات تکمیلی:**

<bdi>LSASS (Local Security Authority Subsystem Service)</bdi> یکی از حساس‌ترین فرآیندهای امنیتی ویندوز است. این فرآیند وظایف بحرانی زیر را دارد:

- **مدیریت احراز هویت** (<bdi>Authentication</bdi>): <bdi>Kerberos</bdi> برای دامنه، <bdi>MSV1_0</bdi> برای محلی
- **اجرای سیاست‌های امنیتی محلی** (<bdi>Local Security Policy</bdi>)
- **مدیریت اعتبارنامه‌ها** (<bdi>Credential Management</bdi>)
- **نوشتن رویدادها در Security Log**

**حافظه lsass.exe چه چیزی دارد؟**

حافظه این فرآیند شامل اطلاعات حیاتی زیر است:
- <bdi>Password Hashes (NTLM, LM)</bdi>
- <bdi>Kerberos Tickets (TGT, Service Tickets)</bdi>
- <bdi>Plaintext Passwords</bdi> (در برخی پیکربندی‌ها)
- <bdi>Security Tokens</bdi>

**به همین دلیل، lsass.exe هدف اصلی حملات Credential Dumping است.**

#### lsaiso.exe و Credential Guard

در سیستم‌هایی که <bdi>Virtualization-Based Security (VBS)</bdi> و <bdi>Credential Guard</bdi> فعال است، فرآیند جدیدی به نام <bdi>lsaiso.exe</bdi> ظاهر می‌شود.

<table dir="rtl">
<tr>
<th>ویژگی</th>
<th>مقدار</th>
</tr>
<tr>
<td><b>نام فرآیند</b></td>
<td><code>lsaiso.exe</code></td>
</tr>
<tr>
<td><b>مسیر اجرایی</b></td>
<td><code>%SystemRoot%\System32\lsaiso.exe</code></td>
</tr>
<tr>
<td><b>والد</b></td>
<td><code>wininit.exe</code></td>
</tr>
<tr>
<td><b>محیط اجرا</b></td>
<td><bdi>Virtual Secure Mode (VSM)</bdi></td>
</tr>
<tr>
<td><b>وظیفه</b></td>
<td>ذخیره‌سازی ایمن اعتبارنامه‌ها</td>
</tr>
</table>

**تقسیم وظایف:**
- <bdi>lsass.exe</bdi>: مدیریت احراز هویت و سیاست‌ها
- <bdi>lsaiso.exe</bdi>: ذخیره‌سازی ایمن اعتبارنامه‌ها در محیط ایزوله

زمانی که <bdi>Credential Guard</bdi> فعال است، حتی با دسترسی <bdi>SYSTEM</bdi> و <bdi>Debug Privilege</bdi>، استخراج اعتبارنامه‌ها از <bdi>lsass.exe</bdi> غیرممکن است.

**🚨 علائم هشدار امنیتی:**

| نشانه مشکوک | احتمال تهدید | تکنیک MITRE | اقدام توصیه‌شده |
|-------------|--------------|-------------|-----------------|
| بیش از یک نمونه `lsass.exe` | بسیار بالا | T1036 | بررسی فوری - احتمال <bdi>Masquerading</bdi> |
| والد غیر از `wininit.exe` | بسیار بالا | T1055 | احتمال <bdi>Process Injection</bdi> |
| فرآیندهای فرزند غیرمعمول | بالا | T1055 | بررسی دقیق |
| دسترسی به حافظه از فرآیندهای غیرمعمول | بسیار بالا | T1003.001 | <bdi>Credential Dumping Attack</bdi> |
| وجود `lsaiso.exe` با <bdi>VBS</bdi> غیرفعال | بالا | T1036 | احتمال <bdi>Malware</bdi> |

**ابزارهای معمول حمله به lsass.exe:**

| ابزار | تکنیک | شناسایی |
|-------|--------|---------|
| <bdi>Mimikatz</bdi> | استخراج اعتبارنامه از حافظه | <bdi>Sysmon Event ID 10</bdi> |
| <bdi>ProcDump</bdi> | ایجاد <bdi>Memory Dump</bdi> | دسترسی <bdi>PROCESS_VM_READ</bdi> |
| <bdi>Comsvcs.dll</bdi> | <bdi>MiniDump</bdi> از طریق <bdi>rundll32</bdi> | فراخوانی `MiniDump` |
| <bdi>Task Manager</bdi> | ایجاد <bdi>Dump File</bdi> | دسترسی از کاربر با <bdi>Admin Rights</bdi> |

**Command Line مشکوک:**
```cmd
rundll32.exe C:\windows\System32\comsvcs.dll, MiniDump <PID> C:\temp\lsass.dmp full
```

---

### 8. Windows Logon Process (winlogon.exe)

<table dir="rtl">
<tr>
<th>ویژگی</th>
<th>مقدار نرمال</th>
</tr>
<tr>
<td><b>نام فرآیند</b></td>
<td><code>winlogon.exe</code></td>
</tr>
<tr>
<td><b>مسیر اجرایی</b></td>
<td><code>%SystemRoot%\System32\winlogon.exe</code></td>
</tr>
<tr>
<td><b>والد</b></td>
<td>یتیم - والد اصلی: نمونه فرزند <code>smss.exe</code> (Session 1+)</td>
</tr>
<tr>
<td><b>فرزندان</b></td>
<td>• <code>LogonUI.exe</code><br>• <code>userinit.exe</code><br>• <code>dwm.exe</code> (<bdi>Desktop Window Manager</bdi>)<br>• <code>fontdrvhost.exe</code></td>
</tr>
<tr>
<td><b>حساب کاربری</b></td>
<td><bdi>NT AUTHORITY\SYSTEM</bdi></td>
</tr>
<tr>
<td><b>Session ID</b></td>
<td>1+ (یکی برای هر نشست کاربری)</td>
</tr>
<tr>
<td><b>تعداد نمونه</b></td>
<td>یکی برای هر <bdi>User Session</bdi></td>
</tr>
</table>

**توضیحات تکمیلی:**

<bdi>winlogon.exe</bdi> مسئول مدیریت کامل ورود و خروج کاربران است:

- **نمایش صفحه ورود** (<bdi>Logon Screen</bdi>)
- **دریافت اعتبارنامه‌ها** و ارسال به <bdi>LSASS</bdi>
- **راه‌اندازی User Shell** (معمولاً `explorer.exe`)
- **مدیریت Lock Workstation** (<kbd>Win+L</kbd>)
- **مدیریت User Profile**

**کلیدهای رجیستری حیاتی:**

| کلید رجیستری | توضیح | مقدار پیش‌فرض |
|--------------|--------|----------------|
| <code>HKLM\...\Winlogon\Userinit</code> | فرآیند اجرا پس از ورود | <code>userinit.exe</code> |
| <code>HKLM\...\Winlogon\Shell</code> | پوسته پیش‌فرض کاربر | <code>explorer.exe</code> |
| <code>HKLM\...\Winlogon\Notify</code> | <bdi>DLLهای Notification</bdi> | - |
| <code>HKCU\...\Winlogon\Shell</code> | پوسته اختصاصی کاربر | - |

**⚠️ نقطه بحرانی برای Persistence:**

مهاجمان اغلب کلیدهای رجیستری <bdi>Winlogon</bdi> را برای پایداری (<bdi>Persistence</bdi>) تغییر می‌دهند:

**مثال حمله:**
```reg
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Userinit
مقدار: C:\Windows\System32\userinit.exe, C:\Malware\backdoor.exe
```

**🚨 علائم هشدار امنیتی:**

| نشانه مشکوک | احتمال تهدید | تکنیک MITRE | اقدام توصیه‌شده |
|-------------|--------------|-------------|-----------------|
| والد فعال (غیر یتیم) | بسیار بالا | T1055 | بررسی فوری |
| اجرا با حساب غیر از <bdi>SYSTEM</bdi> | بسیار بالا | T1134 | احتمال <bdi>Token Manipulation</bdi> |
| تغییر کلیدهای رجیستری <bdi>Winlogon</bdi> | بسیار بالا | T1547.004 | <bdi>Winlogon Helper DLL Persistence</bdi> |
| عدم وجود `userinit.exe` به عنوان فرزند | بالا | T1562 | احتمال مداخله در فرآیند ورود |

**نمونه Command Line نرمال:**
```
winlogon.exe
```

---

### 9. User Initialization (userinit.exe)

<table dir="rtl">
<tr>
<th>ویژگی</th>
<th>مقدار نرمال</th>
</tr>
<tr>
<td><b>نام فرآیند</b></td>
<td><code>userinit.exe</code></td>
</tr>
<tr>
<td><b>مسیر اجرایی</b></td>
<td><code>%SystemRoot%\System32\userinit.exe</code></td>
</tr>
<tr>
<td><b>والد</b></td>
<td><code>winlogon.exe</code></td>
</tr>
<tr>
<td><b>فرزندان</b></td>
<td><code>explorer.exe</code></td>
</tr>
<tr>
<td><b>حساب کاربری</b></td>
<td>کاربر وارد شده</td>
</tr>
<tr>
<td><b>رفتار</b></td>
<td>پس از راه‌اندازی <code>explorer.exe</code> خاتمه می‌یابد</td>
</tr>
</table>

**توضیحات تکمیلی:**

<bdi>userinit.exe</bdi> یک فرآیند کوتاه‌مدت است که وظایف زیر را انجام می‌دهد:

- **بارگذاری User Profile**
- **اجرای Login Scripts**
- **اعمال Group Policy**
- **راه‌اندازی Shell** (معمولاً `explorer.exe`)
- **خاتمه خودکار**

این فرآیند به ندرت در <bdi>Process List</bdi> دیده می‌شود زیرا بسیار سریع خاتمه می‌یابد.

---

### 10. Windows Explorer (explorer.exe)

<table dir="rtl">
<tr>
<th>ویژگی</th>
<th>مقدار نرمال</th>
</tr>
<tr>
<td><b>نام فرآیند</b></td>
<td><code>explorer.exe</code></td>
</tr>
<tr>
<td><b>مسیر اجرایی</b></td>
<td><code>%SystemRoot%\explorer.exe</code> (نه <code>System32</code>)</td>
</tr>
<tr>
<td><b>والد</b></td>
<td>یتیم - والد اصلی: <code>userinit.exe</code></td>
</tr>
<tr>
<td><b>فرزندان</b></td>
<td>اکثر برنامه‌های اجراشده توسط کاربر</td>
</tr>
<tr>
<td><b>حساب کاربری</b></td>
<td>کاربر وارد شده</td>
</tr>
<tr>
<td><b>تعداد نمونه</b></td>
<td>معمولاً 1 (ممکن است بیشتر باشد)</td>
</tr>
</table>

**توضیحات تکمیلی:**

<bdi>explorer.exe</bdi> پوسته گرافیکی پیش‌فرض ویندوز است که شامل:

- **Desktop Environment**
- **Start Menu**
- **Taskbar**
- **File Explorer**
- **System Tray**

**چرا explorer.exe والد اکثر فرآیندها است؟**

زمانی که کاربر روی یک فایل یا میانبر کلیک می‌کند، <bdi>explorer.exe</bdi> آن برنامه را راه‌اندازی می‌کند. بنابراین اکثر برنامه‌های تعاملی (مانند <bdi>browser</bdi>ها، <bdi>Office</bdi>، بازی‌ها) والد آن‌ها <bdi>explorer.exe</bdi> است.

**چند نمونه explorer.exe؟**

گزینه **"Launch folder windows in a separate process"** در ویندوز می‌تواند منجر به چندین نمونه شود.

**🚨 علائم هشدار امنیتی:**

| نشانه مشکوک | احتمال تهدید | تکنیک MITRE | اقدام توصیه‌شده |
|-------------|--------------|-------------|-----------------|
| مسیر اجرایی در `System32` | بسیار بالا | T1036.005 | باید در `%SystemRoot%` باشد |
| اجرا با حساب <bdi>SYSTEM</bdi> | بسیار بالا | T1134 | احتمال <bdi>Privilege Escalation</bdi> |
| راه‌اندازی `cmd.exe` یا `powershell.exe` بدون تعامل کاربر | بالا | T1059 | احتمال <bdi>Command Execution Attack</bdi> |
| تغییر کلید `Shell` در رجیستری | بسیار بالا | T1546.015 | <bdi>Persistence via Shell Replacement</bdi> |

**مثال حمله Persistence:**
```reg
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell
مقدار: C:\Malware\fake_explorer.exe
```

---

## قوانین شناسایی و چک‌لیست عملیاتی

### 🔍 چک‌لیست تحلیل فرآیندها

برای هر فرآیند، موارد زیر را بررسی کنید:

#### 1. **اعتبارسنجی مسیر اجرایی**
```powershell
# PowerShell: بررسی مسیر فرآیندها
Get-Process | Select-Object Name, Path | Where-Object {$_.Name -match "svchost|lsass|csrss|winlogon"}
```

#### 2. **بررسی روابط والد-فرزندی**
```powershell
# PowerShell: نمایش Process Tree
Get-CimInstance Win32_Process | Select-Object ProcessId, ParentProcessId, Name, CommandLine
```

#### 3. **بررسی حساب کاربری**
```powershell
# PowerShell: بررسی User Context
Get-Process | Select-Object Name, @{Name="Owner";Expression={(Get-WmiObject Win32_Process -Filter "ProcessId=$($_.Id)").GetOwner().User}}
```

#### 4. **بررسی آرگومان‌های خط فرمان**
```powershell
# PowerShell: بررسی Command Line Arguments
Get-CimInstance Win32_Process | Where-Object {$_.Name -eq "svchost.exe"} | Select-Object ProcessId, CommandLine
```

#### 5. **بررسی Network Connections**
```powershell
# PowerShell: بررسی اتصالات شبکه مشکوک
Get-NetTCPConnection | Where-Object {$_.State -eq "Established"} | Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, OwningProcess, @{Name="Process";Expression={(Get-Process -Id $_.OwningProcess).Name}}
```

### 📊 جدول خلاصه فرآیندهای حیاتی

| فرآیند | PID | والد | مسیر | حساب کاربری | فرزندان کلیدی |
|--------|-----|------|------|--------------|----------------|
| <code>System</code> | 4 | - | <code>ntoskrnl.exe</code> | SYSTEM | <code>smss.exe</code> |
| <code>smss.exe</code> | متغیر | System | <code>System32\smss.exe</code> | SYSTEM | <code>csrss.exe, wininit.exe, winlogon.exe</code> |
| <code>csrss.exe</code> | متغیر | یتیم | <code>System32\csrss.exe</code> | SYSTEM | هیچ |
| <code>wininit.exe</code> | متغیر | یتیم | <code>System32\wininit.exe</code> | SYSTEM | <code>services.exe, lsass.exe</code> |
| <code>services.exe</code> | متغیر | <code>wininit.exe</code> | <code>System32\services.exe</code> | SYSTEM | <code>svchost.exe, ...</code> |
| <code>svchost.exe</code> | متغیر | <code>services.exe</code> | <code>System32\svchost.exe</code> | متغیر | معمولاً هیچ |
| <code>lsass.exe</code> | متغیر | <code>wininit.exe</code> | <code>System32\lsass.exe</code> | SYSTEM | به ندرت |
| <code>winlogon.exe</code> | متغیر | یتیم | <code>System32\winlogon.exe</code> | SYSTEM | <code>userinit.exe, LogonUI.exe</code> |
| <code>explorer.exe</code> | متغیر | یتیم | <code>%SystemRoot%\explorer.exe</code> | کاربر | اکثر برنامه‌ها |

---

## ابزارهای تحلیل و نظارت

### 🛠️ ابزارهای Sysinternals

#### 1. Process Explorer
**دانلود:** https://learn.microsoft.com/en-us/sysinternals/downloads/process-explorer

**قابلیت‌ها:**
- نمایش Process Tree کامل
- بررسی DLLهای بارگذاری‌شده
- بررسی Handles و Threads
- بررسی سرویس‌های درون svchost.exe
- تشخیص Packed/Signed Binaries

**نکات مهم:**
- استفاده از <kbd>Ctrl+L</kbd> برای مشاهده DLLهای بارگذاری‌شده
- ستون <bdi>"Verified Signer"
