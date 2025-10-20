<!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تحلیل عمیق فرآیندهای حیاتی ویندوز - راهنمای شکار تهدیدات</title>
    <style>
        body {
            font-family: 'Tahoma', 'Arial', sans-serif;
            line-height: 1.8;
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
            background-color: #f5f5f5;
        }
        .container {
            background-color: white;
            padding: 30px;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        h1 {
            color: #1a5490;
            border-bottom: 3px solid #1a5490;
            padding-bottom: 10px;
        }
        h2 {
            color: #2c3e50;
            margin-top: 30px;
            border-right: 5px solid #3498db;
            padding-right: 15px;
        }
        h3 {
            color: #34495e;
            margin-top: 25px;
        }
        .warning-box {
            background-color: #fff3cd;
            border-right: 4px solid #ffc107;
            padding: 15px;
            margin: 15px 0;
        }
        .info-box {
            background-color: #d1ecf1;
            border-right: 4px solid #17a2b8;
            padding: 15px;
            margin: 15px 0;
        }
        .danger-box {
            background-color: #f8d7da;
            border-right: 4px solid #dc3545;
            padding: 15px;
            margin: 15px 0;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin: 20px 0;
            font-size: 14px;
        }
        th {
            background-color: #1a5490;
            color: white;
            padding: 12px;
            text-align: right;
        }
        td {
            border: 1px solid #ddd;
            padding: 10px;
            text-align: right;
        }
        tr:nth-child(even) {
            background-color: #f2f2f2;
        }
        code {
            background-color: #f4f4f4;
            padding: 2px 6px;
            border-radius: 3px;
            font-family: 'Consolas', 'Monaco', monospace;
            color: #c7254e;
        }
        .process-tree {
            background-color: #f8f9fa;
            border: 1px solid #dee2e6;
            padding: 20px;
            margin: 20px 0;
            font-family: monospace;
            direction: ltr;
            text-align: left;
        }
        ul.indicators {
            list-style: none;
            padding-right: 0;
        }
        ul.indicators li:before {
            content: "⚠️ ";
            margin-left: 10px;
        }
        .mitre-tag {
            display: inline-block;
            background-color: #e74c3c;
            color: white;
            padding: 3px 8px;
            border-radius: 4px;
            font-size: 12px;
            font-weight: bold;
            margin: 0 5px;
        }
    </style>
</head>
<body>

<div class="container" dir="rtl" align="right">

<h1>🛡️ راهنامای جامع تحلیل فرآیندهای حیاتی ویندوز برای شکار تهدیدات</h1>

<div class="info-box">
    <strong>📌 درباره این مقاله:</strong><br>
    این مقاله بخشی از مجموعه راهنماهای عملیاتی <bdi dir="ltr">Threat Hunting</bdi> است که به تحلیل عمیق فرآیندهای سیستمی ویندوز می‌پردازد. مخاطبان این مقاله شامل تحلیلگران <bdi dir="ltr">SOC</bdi>، متخصصان <bdi dir="ltr">DFIR</bdi> (Digital Forensics & Incident Response) و شکارچیان تهدید هستند.<br><br>
    <strong>🎯 هدف:</strong> ارائه راهنمای عملی برای شناسایی رفتارهای مخرب در فرآیندهای حیاتی ویندوز<br>
    <strong>📊 سطح:</strong> پیشرفته (Advanced)<br>
    <strong>⏱️ زمان مطالعه:</strong> 35-40 دقیقه
</div>

<h2>📖 مقدمه: چرا شناخت رفتار نرمال حیاتی است؟</h2>

<p>یکی از اصول بنیادین در حوزه شکار تهدیدات و پاسخ به حوادث، شناخت دقیق و جامع از رفتار نرمال یک سیستم است. بدون این شناخت، تشخیص فعالیت‌های غیرعادی و مخرب تقریباً غیرممکن خواهد بود. مهاجمان پیشرفته (<bdi dir="ltr">APT Groups</bdi>) اغلب با استفاده از تکنیک‌های پیچیده‌ای مانند <bdi dir="ltr">Process Masquerading</bdi> <span class="mitre-tag">T1036</span>، <bdi dir="ltr">Process Hollowing</bdi> <span class="mitre-tag">T1055.012</span> و <bdi dir="ltr">DLL Injection</bdi> <span class="mitre-tag">T1055.001</span> تلاش می‌کنند تا فعالیت‌های مخرب خود را در پوشش فرآیندهای مشروع سیستمی پنهان کنند.</p>

<p>به همین دلیل، آشنایی با ویژگی‌های دقیق فرآیندهای سیستمی ویندوز، روابط والد-فرزندی آن‌ها، حساب‌های کاربری که تحت آن اجرا می‌شوند و زمان‌بندی شروع آن‌ها، برای هر تحلیلگر امنیتی ضروری است.</p>

<h3>🎓 آنچه در این مقاله خواهید آموخت:</h3>
<ul>
    <li>شناخت عمیق 9 فرآیند حیاتی ویندوز و رفتارهای نرمال آن‌ها</li>
    <li>علائم هشداردهنده (<bdi dir="ltr">Indicators of Compromise</bdi>) برای هر فرآیند</li>
    <li>تکنیک‌های مهاجمان برای سوء‌استفاده از این فرآیندها</li>
    <li>روش‌های شناسایی و نظارت با ابزارهای عملیاتی</li>
    <li>نقاط پایداری (<bdi dir="ltr">Persistence</bdi>) مورد استفاده بدافزارها</li>
</ul>

<h2>🗂️ جدول خلاصه فرآیندهای حیاتی ویندوز</h2>

<table>
    <thead>
        <tr>
            <th>فرآیند</th>
            <th>PID</th>
            <th>والد</th>
            <th>مسیر</th>
            <th>حساب کاربری</th>
            <th>تعداد نمونه</th>
            <th>Session</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><code>System</code></td>
            <td>4</td>
            <td>—</td>
            <td><bdi dir="ltr">ntoskrnl.exe</bdi></td>
            <td><bdi dir="ltr">Local System</bdi></td>
            <td>1</td>
            <td>—</td>
        </tr>
        <tr>
            <td><code>smss.exe</code></td>
            <td>متغیر</td>
            <td><code>System</code></td>
            <td><code>System32</code></td>
            <td><bdi dir="ltr">Local System</bdi></td>
            <td>1 (پایدار)</td>
            <td>—</td>
        </tr>
        <tr>
            <td><code>csrss.exe</code></td>
            <td>متغیر</td>
            <td>یتیم (<code>smss.exe</code>)</td>
            <td><code>System32</code></td>
            <td><bdi dir="ltr">Local System</bdi></td>
            <td>≥2</td>
            <td>0, 1, ...</td>
        </tr>
        <tr>
            <td><code>wininit.exe</code></td>
            <td>متغیر</td>
            <td>یتیم (<code>smss.exe</code>)</td>
            <td><code>System32</code></td>
            <td><bdi dir="ltr">Local System</bdi></td>
            <td>1</td>
            <td>0</td>
        </tr>
        <tr>
            <td><code>services.exe</code></td>
            <td>متغیر</td>
            <td><code>wininit.exe</code></td>
            <td><code>System32</code></td>
            <td><bdi dir="ltr">Local System</bdi></td>
            <td>1</td>
            <td>0</td>
        </tr>
        <tr>
            <td><code>svchost.exe</code></td>
            <td>متغیر</td>
            <td><code>services.exe</code></td>
            <td><code>System32</code></td>
            <td>متغیر (System/Network/Local Service)</td>
            <td>50+</td>
            <td>0</td>
        </tr>
        <tr>
            <td><code>lsass.exe</code></td>
            <td>متغیر</td>
            <td><code>wininit.exe</code></td>
            <td><code>System32</code></td>
            <td><bdi dir="ltr">Local System</bdi></td>
            <td>1</td>
            <td>0</td>
        </tr>
        <tr>
            <td><code>winlogon.exe</code></td>
            <td>متغیر</td>
            <td>یتیم (<code>smss.exe</code>)</td>
            <td><code>System32</code></td>
            <td><bdi dir="ltr">Local System</bdi></td>
            <td>≥1</td>
            <td>1, 2, ...</td>
        </tr>
        <tr>
            <td><code>explorer.exe</code></td>
            <td>متغیر</td>
            <td>یتیم (<code>userinit.exe</code>)</td>
            <td><code>%SystemRoot%</code></td>
            <td>حساب کاربر</td>
            <td>≥1</td>
            <td>1, 2, ...</td>
        </tr>
    </tbody>
</table>

<h2>🌳 نمودار درختی فرآیندهای راه‌اندازی ویندوز</h2>

<div class="process-tree">
System (PID: 4)
│
├── Memory Compression (Win10+)
│
└── smss.exe (Session Manager)
    │
    ├── [Child] smss.exe (Session 0) → Exits after spawning:
    │   ├── csrss.exe (Session 0)
    │   └── wininit.exe
    │       ├── services.exe (Service Control Manager)
    │       │   ├── svchost.exe (Instance 1 - DcomLaunch)
    │       │   ├── svchost.exe (Instance 2 - RPCSS)
    │       │   ├── svchost.exe (Instance 3 - netsvcs)
    │       │   ├── ... (50+ instances)
    │       │   ├── spoolsv.exe
    │       │   └── SearchIndexer.exe
    │       │
    │       ├── lsass.exe (Local Security Authority)
    │       │   └── lsaiso.exe (if Credential Guard enabled)
    │       │
    │       └── fontdrvhost.exe
    │
    └── [Child] smss.exe (Session 1) → Exits after spawning:
        ├── csrss.exe (Session 1)
        └── winlogon.exe
            ├── LogonUI.exe
            ├── dwm.exe (Desktop Window Manager)
            ├── fontdrvhost.exe
            └── userinit.exe → Exits after spawning:
                └── explorer.exe (User Shell)
                    ├── [User Applications]
                    ├── cmd.exe (if user launches)
                    ├── powershell.exe (if user launches)
                    └── ...
</div>

<h2>🔍 بخش اول: تحلیل عمیق فرآیندهای حیاتی ویندوز</h2>

<h3>1️⃣ فرآیند <bdi dir="ltr">System</bdi> و <bdi dir="ltr">Memory Compression</bdi></h3>

<p>فرآیند <b><bdi dir="ltr">System</bdi></b> با شناسه فرآیند (<bdi dir="ltr">PID</bdi>) ثابت <strong>4</strong>، یک فرآیند ویژه در معماری ویندوز است که میزبان تمامی ترد‌های حالت هسته (<bdi dir="ltr">Kernel Mode Threads</bdi>) می‌باشد. این فرآیند در واقع یک فرآیند واقعی در فضای کاربر (<bdi dir="ltr">User Space</bdi>) نیست، بلکه نمایشی از فعالیت‌های هسته سیستم‌عامل در ابزارهایی مانند <bdi dir="ltr">Task Manager</bdi> و <bdi dir="ltr">Process Explorer</bdi> است.</p>

<div class="info-box">
    <strong>📍 ویژگی‌های کلیدی:</strong>
    <ul>
        <li><strong>PID:</strong> همیشه 4 (ثابت)</li>
        <li><strong>والد:</strong> ندارد (ریشه درخت فرآیندها)</li>
        <li><strong>فرزند:</strong> تنها <code>smss.exe</code></li>
        <li><strong>مسیر نمایشی:</strong> <code>ntoskrnl.exe</code> (در <bdi dir="ltr">Task Manager</bdi>) یا <bdi dir="ltr">"None"</bdi> (در <bdi dir="ltr">Process Explorer</bdi>)</li>
        <li><strong>حساب کاربری:</strong> <bdi dir="ltr">Local System</bdi></li>
    </ul>
</div>

<p>یکی از فرآیندهای مرتبط که در نسخه‌های جدید ویندوز (از ویندوز 10 به بعد) اضافه شده، فرآیند <b><bdi dir="ltr">Memory Compression</bdi></b> است. این فرآیند یک فرآیند مینیمال است که وظیفه فشرده‌سازی حافظه فرآیندها در <bdi dir="ltr">RAM</bdi> را بر عهده دارد تا به جای صفحه‌بندی (<bdi dir="ltr">Paging</bdi>) داده‌ها به دیسک، عملکرد و زمان پاسخ‌دهی سیستم را بهبود بخشد. والد این فرآیند <bdi dir="ltr">System</bdi> است و هیچ فرآیند فرزندی ندارد. این فرآیند تحت حساب کاربری <bdi dir="ltr">Local System</bdi> اجرا می‌شود و تنها یک نمونه از آن در سیستم وجود دارد.</p>

<div class="warning-box">
    <strong>⚠️ هشدار برای تحلیلگران:</strong><br>
    از آنجایی که <bdi dir="ltr">Memory Compression</bdi> نسبتاً جدید است، برخی تحلیلگران ممکن است با آن آشنا نباشند و آن را به اشتباه مشکوک تلقی کنند. این فرآیند کاملاً مشروع است.
</div>

<div class="danger-box">
    <strong>🚨 علائم هشداردهنده (<bdi dir="ltr">IoCs</bdi>):</strong>
    <ul class="indicators">
        <li>وجود فرآیند فرزند غیر از <code>smss.exe</code> برای <bdi dir="ltr">System</bdi></li>
        <li><bdi dir="ltr">PID</bdi> متفاوت از 4</li>
        <li>هرگونه تلاش برای اجرای فایلی با نام <code>ntoskrnl.exe</code> از مسیرهای غیرعادی</li>
    </ul>
</div>

<h3>2️⃣ <bdi dir="ltr">Session Manager Subsystem</bdi> (<code>smss.exe</code>)</h3>

<p>فرآیند <b><code>smss.exe</code></b> اولین فرآیند حالت کاربر (<bdi dir="ltr">User Mode</bdi>) در ویندوز است که توسط فرآیند <bdi dir="ltr">System</bdi> ایجاد می‌شود. این فرآیند نقش بسیار حیاتی در راه‌اندازی سیستم دارد و مسئولیت‌های متعددی را بر عهده دارد.</p>

<div class="info-box">
    <strong>📍 وظایف کلیدی:</strong>
    <ul>
        <li>ایجاد متغیرهای محیطی (<bdi dir="ltr">Environment Variables</bdi>)</li>
        <li>ایجاد توصیفگرهای امنیتی (<bdi dir="ltr">Security Descriptors</bdi>)</li>
        <li>مقداردهی اولیه رجیستری (به ویژه هایوهای <code>HKLM\Software</code>، <code>SAM</code> و <code>Security</code>)</li>
        <li>ایجاد و مدیریت نشست‌های کاربری (<bdi dir="ltr">Sessions</bdi>)</li>
    </ul>
</div>

<p><strong>مسیر اجرایی:</strong> <code>%SystemRoot%\System32\smss.exe</code><br>
<strong>والد:</strong> فرآیند <bdi dir="ltr">System</bdi></p>

<h4>🔄 مکانیزم راه‌اندازی:</h4>

<p>نکته مهم در مورد این فرآیند این است که یک <strong>نمونه اصلی</strong> (<bdi dir="ltr">Master Instance</bdi>) از آن وجود دارد که پس از بوت سیستم باقی می‌ماند و مشخصه اصلی آن <strong>عدم وجود آرگومان خط فرمان</strong> (<bdi dir="ltr">Command Line Arguments</bdi>) است.</p>

<p>این نمونه اصلی حداقل دو نمونه فرزند از خود ایجاد می‌کند:</p>

<ol>
    <li><strong>نمونه <bdi dir="ltr">Session 0</bdi>:</strong> مسئول ایجاد فرآیندهای <code>csrss.exe</code> و <code>wininit.exe</code> (سرویس‌های سیستم)</li>
    <li><strong>نمونه <bdi dir="ltr">Session 1</bdi>:</strong> مسئول ایجاد فرآیندهای <code>csrss.exe</code> و <code>winlogon.exe</code> (اولین کاربر تعاملی)</li>
</ol>

<div class="warning-box">
    <strong>⚡ نکته مهم:</strong><br>
    پس از اتمام وظایف خود، تمامی فرآیندهای فرزند <code>smss.exe</code> خاتمه می‌یابند و تنها نمونه اصلی (بدون آرگومان) باقی می‌ماند.
</div>

<div class="danger-box">
    <strong>🚨 علائم هشداردهنده (<bdi dir="ltr">IoCs</bdi>):</strong>
    <ul class="indicators">
        <li>وجود بیش از یک نمونه پایدار از <code>smss.exe</code> بدون آرگومان</li>
        <li>والد غیر از <bdi dir="ltr">System</bdi> (PID ≠ 4)</li>
        <li>مسیر اجرایی متفاوت از <code>%SystemRoot%\System32\</code></li>
        <li>نام‌های مشابه: <code>smsss.exe</code> (با 3 حرف s)، <code>smss32.exe</code>، <code>smss.com</code></li>
        <li>اجرای تحت حساب کاربری غیر از <bdi dir="ltr">Local System</bdi></li>
    </ul>
</div>

<div class="info-box">
    <strong>🔍 روش شناسایی:</strong>
    <pre><code># PowerShell Command
Get-Process smss | Select-Object Id, Parent, Path, CommandLine

# Sysmon Event ID 1 Query
EventCode=1 Image="*\\smss.exe" ParentImage!="*\\System"</code></pre>
</div>

<h3>Client/Server Runtime Subsystem (csrss.exe)</h3>

<p>فرآیند <b>csrss.exe</b> یک فرآیند حیاتی در معماری ویندوز است که زیرسیستم ویندوز را در حالت کاربر پیاده‌سازی می‌کند. این فرآیند وظایف متعددی از جمله مدیریت فرآیندها و ترد‌ها، وارد کردن بسیاری از DLLهایی که Windows API را فراهم می‌کنند، و تسهیل خاموش شدن GUI در حین shutdown سیستم را بر عهده دارد. مسیر اجرایی آن <code>%SystemRoot%\System32\csrss.exe</code> است و تحت حساب کاربری Local System اجرا می‌شود.</p>

<p>نکته مهم در مورد این فرآیند این است که والد آن یک نمونه فرزند از <code>smss.exe</code> است که پس از ایجاد <code>csrss.exe</code> خاتمه می‌یابد، بنابراین <code>csrss.exe</code> به عنوان یک فرآیند یتیم (Orphan Process) ظاهر می‌شود. برای هر نشست کاربری (Session) یک نمونه از <code>csrss.exe</code> وجود دارد، بنابراین در یک سیستم معمولی حداقل دو نمونه از این فرآیند باید وجود داشته باشد: یکی برای Session 0 (سرویس‌های سیستم) و یکی برای Session 1 (اولین کاربر تعاملی). در صورت استفاده از Remote Desktop یا Fast User Switching، نشست‌های بیشتری ایجاد می‌شود و در نتیجه نمونه‌های بیشتری از <code>csrss.exe</code> خواهیم داشت.</p>

<p>از نظر امنیتی، <code>csrss.exe</code> هرگز نباید فرآیند فرزندی داشته باشد. اگر این فرآیند فرآیند فرزندی داشته باشد، یا والد آن یک فرآیند فعال باشد (نه یتیم)، یا مسیر اجرایی آن متفاوت از <code>System32</code> باشد، این موارد علائم جدی نفوذ هستند. مهاجمان اغلب سعی می‌کنند بدافزارهایی با نام‌های مشابه مانند <code>csrsss.exe</code> یا <code>cssrss.exe</code> ایجاد کنند یا از تکنیک Process Hollowing برای تزریق کد مخرب به فرآیند مشروع <code>csrss.exe</code> استفاده کنند.</p>

<h3>Windows Initialization Process (wininit.exe)</h3>

<p>فرآیند <b>wininit.exe</b> مسئول مقداردهی اولیه و راه‌اندازی بسیاری از اجزای حیاتی سیستم است. این فرآیند متغیرهای محیطی پیش‌فرض (USERPROFILE، ALLUSERPROFILE، PUBLIC و ProgramData) را تنظیم می‌کند، فرآیند LSASS را ایجاد کرده و کلید رمزنگاری LSA را تنظیم می‌کند، Service Control Manager را با راه‌اندازی فرآیند <code>services.exe</code> ایجاد می‌کند و دایرکتوری موقت در ریشه سیستم (<code>%SystemRoot%\Temp</code>) را ایجاد می‌کند.</p>

<p>مسیر اجرایی این فرآیند <code>%SystemRoot%\System32\wininit.exe</code> است و والد آن یک نمونه فرزند از <code>smss.exe</code> در Session 0 است که پس از ایجاد <code>wininit.exe</code> خاتمه یافته و بنابراین <code>wininit.exe</code> نیز به عنوان یک فرآیند یتیم ظاهر می‌شود. این فرآیند تحت حساب کاربری Local System اجرا می‌شود و تنها یک نمونه از آن در سیستم وجود دارد. فرآیندهای فرزند این فرآیند عبارتند از <code>services.exe</code>، <code>lsass.exe</code> و <code>fontdrvhost.exe</code>.</p>

<p>از نظر امنیتی، وجود بیش از یک نمونه از <code>wininit.exe</code>، یا داشتن والدی که یک فرآیند فعال باشد، یا مسیر اجرایی متفاوت، همگی نشانه‌های هشداردهنده هستند. همچنین، اگر فرآیندهای فرزند انتظار رفته (<code>services.exe</code>، <code>lsass.exe</code>، <code>fontdrvhost.exe</code>) وجود نداشته باشند یا فرآیندهای فرزند غیرمعمول دیگری وجود داشته باشند، این موارد نیز می‌توانند نشانگر یک نفوذ باشند.</p>

<h3>Service Control Manager (services.exe)</h3>

<p>فرآیند <b>services.exe</b> که به عنوان Service Control Manager (SCM) شناخته می‌شود، یکی از حیاتی‌ترین اجزای سیستم‌عامل ویندوز است. این فرآیند مسئول مدیریت کامل سرویس‌های سیستمی است، از جمله شروع، توقف، متوقف کردن موقت و ادامه دادن سرویس‌ها. سرویس‌ها در کلید رجیستری <code>HKLM\SYSTEM\CurrentControlSet\Services\</code> تعریف می‌شوند و SCM این تعاریف را خوانده و بر اساس تنظیمات آن‌ها عمل می‌کند.</p>

<p>مسیر اجرایی این فرآیند <code>%SystemRoot%\System32\services.exe</code> است، والد آن <code>wininit.exe</code> می‌باشد و تحت حساب کاربری Local System اجرا می‌شود. تنها یک نمونه از این فرآیند در سیستم وجود دارد. این فرآیند والد بسیاری از فرآیندهای دیگر است، از جمله تمامی نمونه‌های <code>svchost.exe</code>، <code>spoolsv.exe</code>، <code>SearchIndexer.exe</code> و هر سرویس دیگری که به صورت مستقل اجرا می‌شود.</p>

<p>یکی از نکات مهم در مورد SCM این است که این فرآیند <b>Unified Background Process Manager (UBPM)</b> را نیز پیاده‌سازی می‌کند که مسئول فعالیت‌های پس‌زمینه مانند سرویس‌ها و وظایف زمان‌بندی شده است. علاوه بر این، پس از ورود موفقیت‌آمیز تعاملی یک کاربر، SCM بوت را موفق تلقی کرده و مقدار Last Known Good control set را در <code>HKLM\SYSTEM\Select\LastKnownGood</code> به مقدار CurrentControlSet تنظیم می‌کند.</p>

<p>از نظر امنیتی، وجود بیش از یک نمونه از <code>services.exe</code>، یا والد غیر از <code>wininit.exe</code>، یا اجرای تحت حساب کاربری غیر از Local System، همگی علائم جدی نفوذ هستند. همچنین، اگر فرآیندهای فرزند غیرمعمولی که سرویس شناخته شده‌ای نیستند وجود داشته باشند، این موارد نیز باید مورد بررسی دقیق قرار گیرند.</p>

<h3>Generic Host Process for Services (svchost.exe)</h3>

<p>فرآیند <b>svchost.exe</b> یکی از شناخته‌شده‌ترین و در عین حال پیچیده‌ترین فرآیندهای ویندوز است. این فرآیند یک میزبان عمومی برای سرویس‌هایی است که به صورت DLL پیاده‌سازی شده‌اند. به دلیل کاهش مصرف منابع سیستم، ویندوز بسیاری از سرویس‌های خود را به صورت DLL پیاده‌سازی کرده و آن‌ها را در نمونه‌های مختلف <code>svchost.exe</code> اجرا می‌کند.</p>

<p>مسیر اجرایی این فرآیند <code>%SystemRoot%\System32\svchost.exe</code> است، والد آن <code>services.exe</code> می‌باشد و می‌تواند تحت حساب‌های کاربری مختلفی از جمله Local System، Network Service، Local Service و حتی حساب‌های کاربری معمولی اجرا شود. تعداد نمونه‌های این فرآیند بسیار زیاد است و معمولاً بین 10 تا 100 یا حتی بیشتر نمونه در یک سیستم معمولی وجود دارد.</p>

<p>نکته بسیار مهم در مورد <code>svchost.exe</code> این است که این فرآیند همیشه باید با پارامتر <code>-k</code> اجرا شود که Service Host Groups را در رجیستری مشخص می‌کند. گروه‌های معمول شامل DcomLaunch، RPCSS، LocalService، netsvcs، NetworkService و UnistackSvcGroup هستند. پارامتر <code>-s</code> نام سرویس خاص را مشخص می‌کند (مانند LanmanServer، WinRM یا Winmgmt) و پارامتر <code>-p</code> نشان‌دهنده اجرای سیاست (Policy Enforcement) است.</p>

<p>یکی از تغییرات مهم در ویندوز 10 نسخه 1703 این بود که مایکروسافت گروه‌بندی پیش‌فرض سرویس‌های مشابه را برای سیستم‌هایی با بیش از 3.5 گیگابایت RAM تغییر داد. در این سیستم‌ها، اکثر سرویس‌ها اکنون تحت نمونه مستقل خود از <code>svchost.exe</code> اجرا می‌شوند که منجر به وجود بیش از 50 نمونه از این فرآیند می‌شود. این تغییر برای افزایش قابلیت اطمینان و امنیت انجام شده است، زیرا مشکل در یک سرویس دیگر سرویس‌ها را تحت تأثیر قرار نمی‌دهد.</p>

<p>از نظر امنیتی، <code>svchost.exe</code> یک هدف محبوب برای مهاجمان است. علائم هشداردهنده شامل والد غیر از <code>services.exe</code>، اجرا بدون پارامتر <code>-k</code>، مسیر اجرایی مشکوک (مانند <code>svch0st.exe</code> با صفر به جای حرف o)، اجرای تحت حساب کاربری غیرمعمول، یا مصرف منابع غیرعادی (CPU یا شبکه) هستند. تحلیلگران باید با استفاده از ابزارهایی مانند Process Explorer، سرویس‌های در حال اجرا در هر نمونه <code>svchost.exe</code> را بررسی کنند و هرگونه سرویس ناشناخته یا مشکوک را شناسایی نمایند.</p>

<h3>Local Security Authority Subsystem (lsass.exe)</h3>

<p>فرآیند <b>lsass.exe</b> یکی از حساس‌ترین و حیاتی‌ترین فرآیندهای امنیتی در ویندوز است. این فرآیند مسئول مدیریت احراز هویت در سیستم است و وظایف متعددی از جمله فراخوانی بسته احراز هویت مناسب (معمولاً Kerberos برای حساب‌های دامنه یا MSV1_0 برای حساب‌های محلی)، اجرای سیاست‌های امنیتی محلی (مانند سیاست‌های رمز عبور و سیاست‌های ممیزی) و نوشتن رویدادها در لاگ امنیتی را بر عهده دارد.</p>

<p>مسیر اجرایی این فرآیند <code>%SystemRoot%\System32\lsass.exe</code> است، والد آن <code>wininit.exe</code> می‌باشد و تحت حساب کاربری Local System اجرا می‌شود. تنها یک نمونه از این فرآیند در سیستم وجود دارد و این فرآیند به ندرت فرآیند فرزندی دارد (به استثنای مواردی مانند Encrypting File System که یک استثنای شناخته شده است).</p>

<p>یکی از نکات مهم که در پوستر اصلی به آن اشاره نشده بود، وجود فرآیند <b>lsaiso.exe</b> در سیستم‌هایی است که Virtualization-Based Security (VBS) و Credential Guard فعال شده‌اند. زمانی که VBS فعال باشد، عملکرد <code>lsass.exe</code> بین دو فرآیند تقسیم می‌شود: خود <code>lsass.exe</code> و <code>lsaiso.exe</code>. اکثر عملکردها در <code>lsass.exe</code> باقی می‌مانند، اما نقش مهم ذخیره‌سازی ایمن اعتبارنامه‌های حساب به <code>lsaiso.exe</code> منتقل می‌شود. <code>lsaiso.exe</code> با استفاده از فناوری مجازی‌سازی سخت‌افزاری، در یک محیط ایزوله اجرا می‌شود که از سایر فرآیندها جدا است. زمانی که احراز هویت از راه دور مورد نیاز است، <code>lsass.exe</code> درخواست‌ها را از طریق یک کانال RPC به <code>lsaiso.exe</code> ارسال می‌کند تا کاربر را به سرویس راه دور احراز هویت کند. اگر VBS فعال نباشد، <code>lsaiso.exe</code> نباید در سیستم اجرا شود.</p>

<p>از نظر امنیتی، <code>lsass.exe</code> یک هدف اصلی برای حملات Credential Dumping است. ابزارهایی مانند Mimikatz، ProcDump و Comsvcs.dll اغلب برای استخراج اعتبارنامه‌ها از حافظه این فرآیند استفاده می‌شوند. علائم هشداردهنده شامل وجود بیش از یک نمونه از <code>lsass.exe</code>، والد غیر از <code>wininit.exe</code>، داشتن فرآیندهای فرزند غیرمعمول، یا تلاش فرآیندهای غیرمعمول برای باز کردن یک handle به <code>lsass.exe</code> با دسترسی PROCESS_VM_READ هستند. تحلیلگران باید به دقت Event ID 10 در Sysmon (ProcessAccess) را برای شناسایی چنین تلاش‌هایی نظارت کنند.</p>

<h3>Windows Logon Process (winlogon.exe)</h3>

<p>فرآیند <b>winlogon.exe</b> مسئول مدیریت تمامی جنبه‌های ورود و خروج تعاملی کاربران است. زمانی که ورود موفقیت‌آمیز است و توسط فرآیند LSASS تأیید می‌شود، <code>winlogon.exe</code> فرآیندهای فهرست شده در مقدار رجیستری "Userinit" که در <code>HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon</code> قرار دارد را راه‌اندازی می‌کند. به طور پیش‌فرض، این مقدار به <code>userinit.exe</code> اشاره دارد.</p>

<p>مسیر اجرایی این فرآیند <code>%SystemRoot%\System32\winlogon.exe</code> است، والد آن یک نمونه فرزند از <code>smss.exe</code> با شماره نشست بیشتر از صفر است که پس از ایجاد <code>winlogon.exe</code> خاتمه یافته و بنابراین <code>winlogon.exe</code> به عنوان یک فرآیند یتیم ظاهر می‌شود. این فرآیند تحت حساب کاربری Local System اجرا می‌شود و یک نمونه برای هر نشست کاربری وجود دارد. فرآیندهای فرزند معمول این فرآیند عبارتند از <code>LogonUI.exe</code>، <code>userinit.exe</code>، <code>dwm.exe</code> و <code>fontdrvhost.exe</code>.</p>

<p>یکی از نکات مهم که در پوستر اصلی به آن اشاره نشده بود، این است که <code>winlogon.exe</code> یک نقطه بحرانی برای پایداری (Persistence) بدافزارها است. مهاجمان اغلب کلیدهای رجیستری مرتبط با <code>winlogon.exe</code> مانند <code>Userinit</code>، <code>Shell</code>، <code>Notify</code> و <code>UserInitMprLogonScript</code> را تغییر می‌دهند تا بدافزار خود را در هر بار ورود کاربر اجرا کنند. علاوه بر این، مهاجمان ممکن است از Winlogon Helper DLLs که در <code>HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify</code> تعریف شده‌اند، برای اجرای کد مخرب در رویدادهای مختلف ورود استفاده کنند.</p>

<p>از نظر امنیتی، والد غیر از <code>smss.exe</code> (یتیم)، اجرای تحت حساب کاربری غیر از Local System، یا عدم وجود به عنوان والد <code>userinit.exe</code> در زمان ورود، همگی علائم هشداردهنده هستند. تحلیلگران همچنین باید کلیدهای رجیستری مرتبط با <code>winlogon.exe</code> را به دقت بررسی کنند تا از عدم وجود مکانیزم‌های پایداری مخرب اطمینان حاصل کنند.</p>

<h3>Windows Explorer (explorer.exe)</h3>

<p>فرآیند <b>explorer.exe</b> پوسته پیش‌فرض (Default Shell) ویندوز است که رابط کاربری گرافیکی اصلی را فراهم می‌کند. این فرآیند در هسته خود، به کاربران دسترسی به فایل‌ها را فراهم می‌کند، اما از نظر عملکردی، هم یک مرورگر فایل از طریق Windows Explorer است و هم یک رابط کاربری که ویژگی‌هایی مانند دسکتاپ کاربر، منوی استارت، نوار وظیفه، کنترل پنل و راه‌اندازی برنامه‌ها از طریق پسوندهای فایل و فایل‌های میانبر را فراهم می‌کند.</p>

<p>مسیر اجرایی این فرآیند <code>%SystemRoot%\explorer.exe</code> است (توجه کنید که در ریشه SystemRoot است، نه در System32)، والد آن فرآیند <code>userinit.exe</code> است که پس از راه‌اندازی <code>explorer.exe</code> خاتمه می‌یابد و بنابراین <code>explorer.exe</code> به عنوان یک فرآیند یتیم ظاهر می‌شود. این فرآیند تحت حساب کاربری کاربر وارد شده اجرا می‌شود و یک نمونه برای هر کاربر متصل به سیستم وجود دارد. این فرآیند والد بسیاری از فرآیندها است، زیرا اکثر برنامه‌هایی که کاربران به صورت تعاملی اجرا می‌کنند (مانند کلیک روی یک فایل یا یک میانبر) توسط <code>explorer.exe</code> راه‌اندازی می‌شوند.</p>

<p>یکی از نکات مهم که در پوستر اصلی به آن اشاره نشده بود، این است که پوسته پیش‌فرض در مقدار رجیستری <code>HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell</code> مشخص شده است و به طور پیش‌فرض به <code>explorer.exe</code> اشاره دارد. با این حال، ویندوز می‌تواند با رابط‌های دیگری مانند <code>cmd.exe</code> یا <code>powershell.exe</code> نیز کار کند. مهاجمان ممکن است این مقدار رجیستری را تغییر دهند تا یک پوسته مخرب را به جای <code>explorer.exe</code> اجرا کنند. همچنین، گزینه "Launch folder windows in a separate process" در ویندوز می‌تواند منجر به وجود چندین نمونه از <code>explorer.exe</code> برای هر کاربر شود.</p>

<p>از نظر امنیتی، والد غیر از <code>userinit.exe</code> (یتیم)، مسیر اجرایی در <code>System32</code> (باید در ریشه SystemRoot باشد)، یا اجرای تحت حساب Local System، همگی علائم هشداردهنده هستند. همچنین، اگر <code>explorer.exe</code> فرآیندهای فرزند غیرمعمولی مانند <code>cmd.exe</code>، <code>powershell.exe</code> یا ابزارهای سیستمی را بدون تعامل کاربر اجرا کند، این موارد باید مورد بررسی دقیق قرار گیرند زیرا ممکن است نشانه‌ای از یک نفوذ باشند.</p>

</div>
