<div dir="rtl">

# تحلیل جامع فرآیندهای سیستمی ویندوز: System, smss.exe و Memory Compression

## مقدمه

در معماری سیستم‌عامل ویندوز، فرآیندهای خاصی وجود دارند که درک آن‌ها برای مدیریت، بهینه‌سازی و امنیت سیستم حیاتی است. سه مورد از مهم‌ترین و در عین حال گاهی مبهم‌ترینِ این فرآیندها، «System»، «Session Manager Subsystem» یا `smss.exe` و «Memory Compression» هستند. این راهنما به تحلیل عمیق این سه فرآیند، رفتارهای عادی و مشکوک آن‌ها و روش‌های عملی برای بررسی آن‌ها می‌پردازد.

---

<h2 dir="rtl">۱. فرآیند System (PID 4): قلب تپنده کرنل</h2>

<p dir="rtl">
فرآیند <b>System</b> با شناسه فرآیند (PID) ثابت <b>4</b>، یکی از اصلی‌ترین و بنیادی‌ترین موجودیت‌ها در هسته (Kernel) ویندوز است. این فرآیند، یک فرآیند واقعی در فضای کاربر (User Space) نیست؛ بلکه در ابزارهایی مانند Task Manager، بازتابی از تمام فعالیت‌ها و تِرِدهای در حال اجرا در حالت هسته (Kernel Mode Threads) است.
</p>

<p dir="rtl">
این فرآیند در واقع نمایانگر عملکرد خودِ هسته ویندوز (فایل <code>ntoskrnl.exe</code>) بوده و مسئولیت اجرای روتین‌های وقفه (Interrupt Routines) و روندهای زمان‌بندی (Scheduling) را بر عهده دارد.
</p>

<h3 dir="rtl">ویژگی‌های کلیدی</h3>
<ul dir="rtl">
  <li><b>PID:</b> همیشه و به صورت ثابت <code>4</code> است.</li>
  <li><b>مالک (Owner):</b> تحت حساب کاربری <code>NT AUTHORITY\SYSTEM</code> اجرا می‌شود.</li>
  <li><b>والد (Parent):</b> هیچ (این فرآیند ریشه است و توسط هسته در زمان بوت ایجاد می‌شود).</li>
  <li><b>فرزند (Child):</b> در یک سیستم سالم، فرآیند System تنها و فقط یک فرآیند فرزند به نام <code>smss.exe</code> دارد.</li>
  <li><b>مسیر اجرایی:</b> فاقد مسیر اجرایی در فضای کاربر است. در Process Explorer به عنوان "None" نمایش داده می‌شود.</li>
</ul>

<h3 dir="rtl">وظایف اصلی</h3>
<ul dir="rtl">
  <li><b>مدیریت حافظه (Memory Manager Threads):</b> برای تخصیص حافظه در Kernel Pool.</li>
  <li><b>عملیات ورودی/خروجی (I/O Workers):</b> برای کنترل دیسک و شبکه.</li>
  <li><b>توزیع بار پردازشی (Balancer Threads):</b> برای متعادل‌سازی بار کاری بین هسته‌های CPU.</li>
  <li><b>ارتباطات بین پردازشی (IPC):</b> مدیریت System Calls، DPCs و Interrupts.</li>
</ul>

---

## ۲. فرآیند smss.exe: دروازه‌بان نشست‌های کاربری

فرآیند **Session Manager Subsystem** (با نام فایل **smss.exe**) یکی از حیاتی‌ترین مؤلفه‌های سیستم‌عامل ویندوز و در واقع *اولین فرآیند حالت کاربر (User Mode)* است که پس از راه‌اندازی هسته توسط فرآیند **System (PID 4)** ایجاد می‌شود.

مسیر اجرایی این فرآیند همواره در `C:\Windows\System32\smss.exe` قرار دارد و تحت حساب کاربری `NT AUTHORITY\SYSTEM` اجرا می‌شود.


### وظایف اصلی و حیاتی

- **ایجاد متغیرهای محیطی (Environment Variables):** متغیرهای سیستمی را از رجیستری می‌خواند و تنظیم می‌کند.
- **مقداردهی اولیه رجیستری:** هایوهای (Hives) حیاتی مانند `HKLM\SAM`، `HKLM\SECURITY` و `HKLM\SOFTWARE` را مقداردهی اولیه می‌کند.
- **مدیریت فایل صفحه‌بندی (Page File):** فایل `Pagefile.sys` را برای مدیریت حافظه مجازی ایجاد می‌کند.
- **بارگذاری زیرسیستم Win32:** زیرسیستم `Win32k.sys` (در حالت هسته) و `csrss.exe` (در حالت کاربر) را راه‌اندازی می‌کند.
- **ایجاد نشست‌ها (Sessions):** مهم‌ترین وظیفه آن، ایجاد نشست‌های کاربری جدید است.

### ساختار نمونه‌ها: معماری Master و Child

نحوه عملکرد `smss.exe` اغلب باعث سردرگمی می‌شود، اما ساختار مشخصی دارد:

1.  **نمونه اصلی (Master Instance):** یک نمونه اصلی و دائمی از `smss.exe` وجود دارد که پس از بوت در حافظه باقی می‌ماند. مشخصه اصلی این نمونه، *عدم وجود هرگونه آرگومان خط فرمان (Command Line Arguments)* است.
2.  **نمونه‌های فرزند (Temporary Instances):** این نمونه اصلی، به ازای هر نشست جدید، یک کپی موقتی (فرزند) از خود ایجاد می‌کند.
    - **Session 0 (نشست سیستمی):** اولین فرزند برای نشست صفر ایجاد شده و مسئول راه‌اندازی فرآیندهای `csrss.exe` و `wininit.exe` است.
    - **Session 1 (نشست کاربر):** فرزند بعدی برای نشست اولین کاربر ایجاد شده و مسئول راه‌اندازی `csrss.exe` و `winlogon.exe` مربوط به آن نشست است.
3.  **خاتمه فرزندان:** پس از اینکه این نمونه‌های موقت وظایف خود را انجام دادند، خاتمه می‌یابند. بنابراین، در یک سیستم در حال کار، تنها **یک** نمونه اصلی و پایدار از `smss.exe` باید فعال باشد.

---

## ۳. فرآیند Memory Compression: بهینه‌ساز حافظه مدرن

فرآیند **Memory Compression** یکی از قابلیت‌های نسبتاً جدید است که از ویندوز ۱۰ (Build 10586) به بعد معرفی شد. این فرآیند با هدف بهبود عملکرد سیستم، به‌خصوص در زمان کمبود حافظه فیزیکی (RAM)، طراحی شده است.

### عملکرد و هدف

به جای انتقال مستقیم داده‌های غیرفعال از RAM به دیسک (Paging)، این فرآیند ابتدا سعی می‌کند بخش‌هایی از حافظه را درون خودِ RAM فشرده‌سازی کند. این کار فضای خالی ایجاد کرده و نیاز به مراجعه به دیسکِ کُند را کاهش می‌دهد.


### ویژگی‌های کلیدی

- **والد (Parent):** این فرآیند فرزند فرآیند `System` (PID 4) محسوب می‌شود.
- **فرزند (Child):** هیچ فرآیند فرزندی ندارد.
- **مالک (Owner):** تحت حساب کاربری `NT AUTHORITY\SYSTEM` اجرا می‌شود.
- **تعداد:** همیشه تنها یک نمونه از آن در سیستم فعال است.
- **عملکرد داخلی:** بخشی از مدیریت حافظه (Memory Manager) در کرنل است و از ساختاری به نام **Store Manager** برای نگهداری صفحات فشرده استفاده می‌کند.

---

## ۴. بخش‌های کاربردی و عملیاتی

<h3 dir="rtl">راهنمای عملی تحلیل</h3>

<h4 dir="rtl">ابزارهای مجموعه Sysinternals Suite</h4>

<ul dir="rtl">
  <li><b>Process Explorer:</b> برای مشاهده دقیق سلسله‌مراتب فرآیندها (<code>System</code> → <code>smss.exe</code> & <code>Memory Compression</code>).</li>
  <li><b>Process Monitor:</b> برای رصد زنده فعالیت‌های فایل سیستم و رجیستری.</li>
  <li><b>Sysmon:</b> برای ثبت رویدادهای سیستمی و شناسایی ناهنجاری‌ها بر اساس قوانین از پیش تعیین‌شده.</li>
</ul>

#### دستورات PowerShell

- **نمایش اطلاعات فرآیندها:**

<div dir="ltr">

```powershell
# Get information about the System process
Get-Process -Id 4 | Format-List *

# Get information about the smss.exe process (should be only one)
Get-Process -Name "smss"

# Get information about the Memory Compression process
Get-Process -Name "Memory Compression"
```

</div>

- **بررسی و مدیریت وضعیت Memory Compression:**

<div dir="ltr">

```powershell
# Check if Memory Compression is enabled
Get-MMAgent

# Disable Memory Compression (requires Admin rights)
Disable-MMAgent -mc
```

</div>

#### ابزار Volatility برای تحلیل حافظه

با استفاده از Volatility و یک dump از حافظه RAM، می‌توان فرآیندهای مخفی (که توسط Rootkit از دید ابزارهای زنده پنهان شده‌اند) یا کدهای تزریق‌شده به فرآیندهای سیستمی را شناسایی کرد.

### جدول مقایسه‌ای: رفتار عادی در مقابل مشکوک

| فرآیند / ویژگی | رفتار عادی (Normal) | رفتار مشکوک (Suspicious / IoC) |
| :--- | :--- | :--- |
| **System (PID 4)** | | |
| تعداد و PID | دقیقاً یک نمونه با `PID 4`. | بیش از یک نمونه یا PID متفاوت. |
| والد | ندارد (ریشه). | داشتن یک فرآیند والد. |
| فرزند | فقط `smss.exe`. | هر فرزندی غیر از `smss.exe`. |
| فعالیت شبکه | محدود به درایورهای کرنل (مثل `HTTP.sys`). | شنود روی پورت‌های رایج (80, 443) یا ایجاد اتصالات خروجی. |
| **smss.exe** | | |
| مسیر اجرایی | همیشه در `C:\Windows\System32`. | وجود فایل در مسیر دیگر یا با نام مشابه (`smsss.exe`). |
| والد | همیشه `System (PID 4)`. | هر والد دیگری (`explorer.exe`, `services.exe`, ...). |
| تعداد نمونه‌ها | یک نمونه پایدار (بدون آرگومان خط فرمان). | بیش از یک نمونه پایدار. |
| فعالیت شبکه | ندارد. | هرگونه فعالیت شبکه‌ای یک پرچم قرمز جدی است. |
| **Memory Compression** | | |
| والد | `System (PID 4)`. | هر والدی غیر از فرآیند `System`. |
| فرزند | ندارد. | داشتن هرگونه فرآیند فرزند. |
| فعالیت شبکه/دیسک | ندارد. | مشاهده اتصالات شبکه یا خواندن/نوشتن مستقیم روی دیسک. |

### چک‌لیست امنیتی برای تحلیلگران

1.  [ ] **PID 4:** آیا فقط یک فرآیند `System` با `PID 4` وجود دارد و والد آن خالی است؟
2.  [ ] **فرزند System:** آیا تنها فرزند دائمی `System`، فرآیند `smss.exe` است؟
3.  [ ] **مشخصات smss.exe:** آیا `smss.exe` از مسیر `C:\Windows\System32` اجرا شده و والد آن `PID 4` است؟
4.  [ ] **تعداد smss.exe:** آیا فقط یک نمونه پایدار از `smss.exe` در حال اجراست؟
5.  [ ] **والد Memory Compression:** آیا والد فرآیند `Memory Compression`، فرآیند `System` است؟
6.  [ ] **اتصالات شبکه:** با `netstat -ano` یا TCPView بررسی کنید که آیا `smss.exe` یا `Memory Compression` فعالیت شبکه‌ای دارند.
7.  [ ] **ماژول‌های کرنل:** با Process Explorer، درایورهای بارگذاری‌شده در فضای `System` را بررسی و موارد بدون امضای دیجیتال را شناسایی کنید.
8.  [ ] **لاگ‌های Sysmon:** رخدادهای ایجاد فرآیند (Event ID 1) را برای شناسایی ناهنجاری‌ها در این فرآیندها تحلیل کنید.

---

## ۵. عمق فنی بیشتر و روش‌های شناسایی Rootkit

Rootkitها بدافزارهایی هستند که با دستکاری ساختارهای داخلی کرنل، خود را پنهان می‌کنند.

- **DKOM (Direct Kernel Object Manipulation):** بدافزار مستقیماً ساختار `EPROCESS` را دستکاری کرده و فرآیند مخرب خود را از لیست فرآیندهای فعال سیستم حذف می‌کند تا از دید ابزارها پنهان بماند.
- **SSDT Hooking (System Service Descriptor Table):** بدافزار با تغییر جدول فراخوانی‌های سیستمی، کنترل اجرای توابع اصلی ویندوز را به دست می‌گیرد.
- **IRP Hooking (I/O Request Packet):** بدافزار با هوک کردن توابع مدیریت ورودی/خروجی، داده‌های شبکه یا فایل سیستم را شنود یا دستکاری می‌کند.

---

## ۶. بخش‌های تکمیلی و منابع

### نگاشت به چارچوب MITRE ATT&CK®

- **[T1036] - Masquerading:** جعل نام فرآیندهای سیستمی مانند `smss.exe` به `smsss.exe` برای فریب تحلیلگران.
- **[T1068] - Exploitation for Privilege Escalation:** بهره‌برداری از آسیب‌پذیری درایورها برای اجرای کد در سطح کرنل (فرآیند `System`).
- **[T1547.006] - Boot or Logon Autostart Execution: Kernel Modules and Drivers:** بارگذاری یک درایور مخرب (Rootkit) برای تضمین پایداری.
- **[T1014] - Rootkit:** پنهان‌سازی فعالیت‌ها با دستکاری مستقیم ساختارهای کرنل.

### کوئری‌های Sysmon نمونه

برای شناسایی فعالیت‌های مشکوک، می‌توانید از قوانین زیر در پیکربندی Sysmon استفاده کنید.

<div dir="ltr">

```xml
<Sysmon schemaversion="4.81">
  <EventFiltering>
    <RuleGroup name="Suspicious System/SMSS Activity" groupRelation="or">

      <!-- Rule: Detect smss.exe running from an abnormal path -->
      <ProcessCreate onmatch="include">
        <Image condition="is">smss.exe</Image>
        <OriginalFileName condition="is">smss.exe</OriginalFileName>
        <Image condition="not end with">\\Windows\\System32\\smss.exe</Image>
      </ProcessCreate>

      <!-- Rule: Detect smss.exe with a parent other than System -->
      <ProcessCreate onmatch="include">
        <Image condition="end with">\\smss.exe</Image>
        <ParentImage condition="not is">System</ParentImage>
      </ProcessCreate>

      <!-- Rule: Detect a child process for System other than smss.exe or Memory Compression -->
      <ProcessCreate onmatch="include">
        <Rule groupRelation="and">
          <ParentImage condition="is">System</ParentImage>
          <Image condition="not end with">\\smss.exe</Image>
          <Image condition="not is">Memory Compression</Image>
        </Rule>
      </ProcessCreate>
      
      <!-- Rule: Detect network connection from smss.exe -->
      <NetworkConnect onmatch="include">
          <Image condition="end with">\\smss.exe</Image>
      </NetworkConnect>

    </RuleGroup>
  </EventFiltering>
</Sysmon>
```

</div>

### منابع بیشتر برای مطالعه

- **Microsoft Docs:** [مستندات رسمی Sysinternals Suite](https://docs.microsoft.com/en-us/sysinternals/)
- **کتاب:** *Windows Internals, Part 1 & 2*
- **وب‌سایت:** [The Volatility Foundation](https://www.volatilityfoundation.org/)

</div>
