<div dir="rtl" align="right">

عذرخواهی می‌کنم، حق با شماست. در پاسخ قبلی مطالب را به اشتباه ترکیب کردم.

در ادامه، متن کامل و بهبودیافته **منحصراً برای فرآیند `smss.exe`** در فرمت Markdown آماده شده است. این نسخه برای استفاده در GitHub کاملاً مناسب بوده و تمام جزئیات فنی و کاربردی درخواست‌شده را در بر می‌گیرد.

</div>

---

<div dir="rtl">

# تحلیل جامع فرآیند smss.exe: دروازه‌بان نشست‌های ویندوز

## مقدمه

فرآیند **Session Manager Subsystem** (با نام فایل **smss.exe**) یکی از حیاتی‌ترین مؤلفه‌های سیستم‌عامل ویندوز است. این فرآیند به عنوان *اولین فرآیند حالت کاربر (User Mode)* پس از راه‌اندازی هسته (Kernel) توسط فرآیند **System (PID 4)** ایجاد می‌شود و نقشی کلیدی در راه‌اندازی سیستم و آماده‌سازی محیط برای نشست‌های (Sessions) کاربری ایفا می‌کند.

این راهنما به تحلیل عمیق این فرآیند، رفتارهای عادی و مشکوک آن، و روش‌های عملی برای بررسی آن از دیدگاه امنیتی می‌پردازد.

---

## ۱. عملکرد و وظایف اصلی

مسیر اجرایی `smss.exe` همواره در `C:\Windows\System32\smss.exe` قرار دارد و تحت حساب کاربری `NT AUTHORITY\SYSTEM` اجرا می‌شود.

این فرآیند مسئولیت‌های بنیادین متعددی را در مراحل اولیه بوت بر عهده دارد:

-   **ایجاد متغیرهای محیطی (Environment Variables):** متغیرهای سیستمی را با خواندن مقادیر از کلید رجیستری `HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Environment` تنظیم می‌کند.
-   **مقداردهی اولیه رجیستری:** هایوهای (Hives) حیاتی رجیستری مانند `HKLM\SAM`، `HKLM\SECURITY` و `HKLM\SOFTWARE` را بارگذاری و آماده می‌کند.
-   **مدیریت فایل صفحه‌بندی (Page File):** فایل `Pagefile.sys` را برای مدیریت حافظه مجازی ایجاد و مقداردهی می‌کند.
-   **بارگذاری زیرسیستم Win32:** زیرسیستم `Win32k.sys` (در حالت هسته) و فرآیند `csrss.exe` (در حالت کاربر) را راه‌اندازی می‌کند تا محیط گرافیکی و APIهای ویندوز فراهم شوند.
-   **اجرای وظایف پیش‌راه‌اندازی:** دستورات موجود در مقدار `BootExecute` در رجیستری (مانند `autochk.exe` برای بررسی سلامت دیسک) را قبل از بوت کامل اجرا می‌کند. این بخش یکی از نقاط مورد علاقه بدافزارها برای دستیابی به پایداری (Persistence) است.

<p align="center">
  <img src="https://i.imgur.com/T0bS18K.png" alt="smss.exe Process Hierarchy" width="700"/>
  <br>
  <small>نمودار سلسله‌مراتبی ایجاد فرآیندها توسط smss.exe</small>
</p>

## ۲. ساختار نمونه‌ها: معماری "Master" و "Child"

نحوه عملکرد `smss.exe` اغلب باعث سردرگمی می‌شود، اما ساختار کاملاً مشخصی دارد:

1.  **نمونه اصلی (Master Instance):** یک نمونه اصلی و دائمی از `smss.exe` وجود دارد که پس از بوت در حافظه باقی می‌ماند. مشخصه اصلی این نمونه، *عدم وجود هرگونه آرگومان خط فرمان (Command Line Arguments)* است.

2.  **نمونه‌های فرزند (Temporary Instances):** این نمونه اصلی، به ازای هر نشست جدید، یک کپی موقتی (فرزند) از خود ایجاد می‌کند.
    -   **Session 0 (نشست سیستمی):** اولین فرزند برای نشست ایزوله صفر ایجاد شده و مسئول راه‌اندازی فرآیندهای سیستمی حیاتی یعنی **`wininit.exe`** و **`csrss.exe`** (مربوط به نشست صفر) است.
    -   **Session 1 (نشست کاربر):** با لاگین اولین کاربر، فرزند بعدی برای نشست او (معمولاً Session 1) ایجاد شده و مسئول راه‌اندازی **`winlogon.exe`** (فرآیند لاگین کاربر) و **`csrss.exe`** مربوط به آن نشست است.

3.  **خاتمه فرزندان:** پس از اینکه این نمونه‌های موقت وظایف خود را انجام دادند، بلافاصله خاتمه می‌یابند. بنابراین، در یک سیستم در حال کار، تنها **یک** نمونه اصلی و پایدار از `smss.exe` باید فعال باشد.

---

## ۳. تحلیل امنیتی و راهنمای عملی

### جدول مقایسه‌ای: رفتار عادی در مقابل مشکوک

| ویژگی | رفتار عادی (Normal) | رفتار مشکوک (Suspicious / IoC) |
| :--- | :--- | :--- |
| **مسیر اجرایی** | همیشه در `C:\Windows\System32\` | وجود در مسیرهای دیگر مانند `Temp`, `Downloads` یا حتی `System32` با نام مشابه. |
| **نام فایل** | `smss.exe` | جعل نام (Masquerading) مانند `smsss.exe`, `sms.exe` یا `smss32.exe`. |
| **فرآیند والد** | همیشه **System (PID 4)** | هر والد دیگری مانند `explorer.exe`, `svchost.exe` یا `powershell.exe`. |
| **تعداد نمونه‌ها** | یک نمونه پایدار (بدون آرگومان خط فرمان). | وجود **بیش از یک** نمونه پایدار. |
| **آرگومان خط فرمان** | نمونه اصلی فاقد آرگومان است. | وجود آرگومان‌های مشکوک در نمونه اصلی. |
| **فعالیت شبکه** | به هیچ‌وجه نباید فعالیت شبکه‌ای داشته باشد. | هرگونه اتصال ورودی یا خروجی یک **پرچم قرمز جدی** است. |
| **امضای دیجیتال** | دارای امضای معتبر "Microsoft Windows Publisher". | فاقد امضا یا با امضای نامعتبر. |
| **حساب کاربری** | `NT AUTHORITY\SYSTEM` | اجرا تحت حساب کاربری دیگر. |

### دستورات کاربردی PowerShell

برای بررسی سریع و خودکار ویژگی‌های `smss.exe` می‌توان از دستورات زیر استفاده کرد.

<div dir="ltr">

```powershell
# Get the smss.exe process object
$smss = Get-Process -Name smss -ErrorAction SilentlyContinue

if ($smss) {
    # Check for multiple instances
    if ($smss.Count -gt 1) {
        Write-Warning "More than one smss.exe instance found!"
    }

    # Verify path and parent process
    foreach ($instance in $smss) {
        $parent = (Get-CimInstance -ClassName Win32_Process -Filter "ProcessId = $($instance.Id)").ParentProcessId
        Write-Host "Instance PID: $($instance.Id)"
        Write-Host "  Path: $($instance.Path)"
        Write-Host "  Parent PID: $parent"

        # Validate Path
        if ($instance.Path -ne "C:\Windows\System32\smss.exe") {
            Write-Error "Suspicious Path: $($instance.Path)"
        }
        # Validate Parent
        if ($parent -ne 4) {
            $parentProcess = Get-Process -Id $parent -ErrorAction SilentlyContinue
            Write-Error "Incorrect Parent Process: $($parentProcess.Name)"
        }
    }
} else {
    Write-Host "smss.exe is not running."
}
```

</div>

### چک‌لیست امنیتی برای تحلیلگران

1.  [ ] **تایید مسیر:** آیا مسیر فایل `C:\Windows\System32\smss.exe` است؟
2.  [ ] **تایید والد:** آیا فرآیند والد آن `System (PID 4)` است؟ (با استفاده از Process Explorer یا دستور بالا)
3.  [ ] **تایید تعداد:** آیا فقط یک نمونه پایدار از آن در حال اجرا است؟
4.  [ ] **بررسی نام:** آیا نام فایل دقیقاً `smss.exe` است و نه یک نام مشابه؟
5.  [ ] **بررسی شبکه:** با ابزاری مانند TCPView یا `netstat -ano`، اطمینان حاصل کنید که این فرآیند هیچ‌گونه اتصال شبکه‌ای ندارد.
6.  [ ] **بررسی امضای دیجیتال:** در تب Properties فایل، امضای دیجیتال آن را بررسی کنید.
7.  [ ] **بررسی رجیستری برای پایداری:** مقدار کلید `HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\BootExecute` را بررسی کنید. به صورت پیش‌فرض، این مقدار باید فقط شامل `autocheck autochk *` باشد. وجود ورودی‌های دیگر می‌تواند نشانه‌ی بدافزار باشد.

---

## ۴. بخش‌های تکمیلی

### نگاشت به چارچوب MITRE ATT&CK®

-   **[T1036.005] - Masquerading: Match Legitimate Name or Location:** مهاجمان با ایجاد بدافزارهایی با نام `smss.exe` در مسیرهای غیر استاندارد یا با نام‌های مشابه مانند `smsss.exe` سعی در فریب تحلیلگران دارند.
-   **[T1547.001] - Boot or Logon Autostart Execution: Registry Run Keys / Startup Folder:** اگرچه `smss.exe` به طور مستقیم از Run Keys استفاده نمی‌کند، اما مهاجمان می‌توانند مقدار رجیستری `BootExecute` را که توسط `smss.exe` پردازش می‌شود، برای اجرای اسکریپت یا بدافزار خود در مراحل اولیه بوت دستکاری کنند.
-   **[T1204.002] - User Execution: Malicious File:** کاربری ممکن است فریب خورده و یک فایل مخرب که خود را `smss.exe` جا زده است را از یک مسیر نامعتبر (مثلاً پوشه دانلود) اجرا کند.

### کوئری‌های Sysmon نمونه

برای شناسایی فعالیت‌های مشکوک مرتبط با `smss.exe`، می‌توانید از قوانین زیر در پیکربندی Sysmon استفاده کنید.

<div dir="ltr">

```xml
<Sysmon schemaversion="4.81">
  <EventFiltering>
    <RuleGroup name="Suspicious SMSS.exe Activity" groupRelation="or">

      <!-- Rule 1: Detect smss.exe running from an abnormal path -->
      <ProcessCreate onmatch="include">
        <Image condition="is">smss.exe</Image>
        <OriginalFileName condition="is">smss.exe</OriginalFileName>
        <!-- Standard path on 64-bit systems. Adapt for 32-bit if needed. -->
        <Image condition="not image">C:\Windows\System32\smss.exe</Image>
      </ProcessCreate>

      <!-- Rule 2: Detect smss.exe with a parent other than System -->
      <ProcessCreate onmatch="include">
        <Image condition="end with">\smss.exe</Image>
        <ParentImage condition="not is">System</ParentImage>
      </ProcessCreate>
      
      <!-- Rule 3: Detect network connection initiated by smss.exe -->
      <NetworkConnect onmatch="include">
          <Image condition="end with">\smss.exe</Image>
      </NetworkConnect>

    </RuleGroup>
  </EventFiltering>
</Sysmon>
```

</div>

### منابع بیشتر برای مطالعه

-   **مستندات مایکروسافت:** [Windows Internals, Part 1, 7th Edition](https://docs.microsoft.com/en-us/sysinternals/resources/windows-internals)
-   **وبلاگ Sysinternals:** [The Case of the Hung Sysinternals Utilities](https://techcommunity.microsoft.com/t5/windows-blog-archive/the-case-of-the-hung-sysinternals-utilities/ba-p/723559) (توضیح نقش `smss.exe` در ایجاد نشست‌ها)
-   **چارچوب MITRE ATT&CK®:** [mitre.org](https://attack.mitre.org/)

</div>
