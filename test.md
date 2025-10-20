
<body>

<div class="container" dir="rtl" align="right">

<h1>🛡️ راهنمای جامع تحلیل فرآیندهای حیاتی ویندوز برای شکار تهدیدات</h1>

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

<h3>3️⃣ Client/Server Runtime Subsystem (csrss.exe)</h3>

<p>فرآیند <b>csrss.exe</b> یک فرآیند حیاتی در معماری ویندوز است که زیرسیستم ویندوز را در حالت کاربر پیاده‌سازی می‌کند. این فرآیند وظایف متعددی از جمله مدیریت فرآیندها و ترد‌ها، وارد کردن بسیاری از DLLهایی که Windows API را فراهم می‌کنند، و تسهیل خاموش شدن GUI در حین shutdown سیستم را بر عهده دارد. مسیر اجرایی آن <code>%SystemRoot%\System32\csrss.exe</code> است و تحت حساب کاربری Local System اجرا می‌شود.</p>

<p>نکته مهم در مورد این فرآیند این است که والد آن یک نمونه فرزند از <code>smss.exe</code> است که پس از ایجاد <code>csrss.exe</code> خاتمه می‌یابد، بنابراین <code>csrss.exe</code> به عنوان یک فرآیند یتیم (Orphan Process) ظاهر می‌شود. برای هر نشست کاربری (Session) یک نمونه از <code>csrss.exe</code> وجود دارد، بنابراین در یک سیستم معمولی حداقل دو نمونه از این فرآیند باید وجود داشته باشد: یکی برای Session 0 (سرویس‌های سیستم) و یکی برای Session 1 (اولین کاربر تعاملی). در صورت استفاده از Remote Desktop یا Fast User Switching، نشست‌های بیشتری ایجاد می‌شود و در نتیجه نمونه‌های بیشتری از <code>csrss.exe</code> خواهیم داشت.</p>

<p>از نظر امنیتی، <code>csrss.exe</code> هرگز نباید فرآیند فرزندی داشته باشد. اگر این فرآیند فرآیند فرزندی داشته باشد، یا والد آن یک فرآیند فعال باشد (نه یتیم)، یا مسیر اجرایی آن متفاوت از <code>System32</code> باشد، این موارد علائم جدی نفوذ هستند. مهاجمان اغلب سعی می‌کنند بدافزارهایی با نام‌های مشابه مانند <code>csrsss.exe</code> یا <code>cssrss.exe</code> ایجاد کنند یا از تکنیک Process Hollowing برای تزریق کد مخرب به فرآیند مشروع <code>csrss.exe</code> استفاده کنند.</p>

<div class="danger-box">
    <strong>🚨 علائم هشداردهنده (<bdi dir="ltr">IoCs</bdi>):</strong>
    <ul class="indicators">
        <li>وجود فرآیند فرزند برای <code>csrss.exe</code></li>
        <li>والد فعال (غیر از نمونه فرزند <code>smss.exe</code> که خاتمه یافته)</li>
        <li>مسیر اجرایی متفاوت از <code>%SystemRoot%\System32\</code></li>
        <li>نام‌های مشابه: <code>csrsss.exe</code>، <code>cssrss.exe</code>، <code>csrss32.exe</code></li>
        <li>اجرای تحت حساب کاربری غیر از <bdi dir="ltr">Local System</bdi></li>
    </ul>
</div>

<h3>4️⃣ Windows Initialization Process (wininit.exe)</h3>

<p>فرآیند <b>wininit.exe</b> مسئول مقداردهی اولیه و راه‌اندازی بسیاری از اجزای حیاتی سیستم است. این فرآیند متغیرهای محیطی پیش‌فرض (USERPROFILE، ALLUSERPROFILE، PUBLIC و ProgramData) را تنظیم می‌کند، فرآیند LSASS را ایجاد کرده و کلید رمزنگاری LSA را تنظیم می‌کند، Service Control Manager را با راه‌اندازی فرآیند <code>services.exe</code> ایجاد می‌کند و دایرکتوری موقت در ریشه سیستم (<code>%SystemRoot%\Temp</code>) را ایجاد می‌کند.</p>

<p>مسیر اجرایی این فرآیند <code>%SystemRoot%\System32\wininit.exe</code> است و والد آن یک نمونه فرزند از <code>smss.exe</code> در Session 0 است که پس از ایجاد <code>wininit.exe</code> خاتمه یافته و بنابراین <code>wininit.exe</code> نیز به عنوان یک فرآیند یتیم ظاهر می‌شود. این فرآیند تحت حساب کاربری Local System اجرا می‌شود و تنها یک نمونه از آن در سیستم وجود دارد. فرآیندهای فرزند این فرآیند عبارتند از <code>services.exe</code>، <code>lsass.exe</code> و <code>fontdrvhost.exe</code>.</p>

<p>از نظر امنیتی، وجود بیش از یک نمونه از <code>wininit.exe</code>، یا داشتن والدی که یک فرآیند فعال باشد، یا مسیر اجرایی متفاوت، همگی نشانه‌های هشداردهنده هستند. همچنین، اگر فرآیندهای فرزند انتظار رفته (<code>services.exe</code>، <code>lsass.exe</code>، <code>fontdrvhost.exe</code>) وجود نداشته باشند یا فرآیندهای فرزند غیرمعمول دیگری وجود داشته باشند، این موارد نیز می‌توانند نشانگر یک نفوذ باشند.</p>

<div class="danger-box">
    <strong>🚨 علائم هشداردهنده (<bdi dir="ltr">IoCs</bdi>):</strong>
    <ul class="indicators">
        <li>وجود بیش از یک نمونه از <code>wininit.exe</code></li>
        <li>والد فعال (غیر از نمونه فرزند <code>smss.exe</code> که خاتمه یافته)</li>
        <li>مسیر اجرایی متفاوت از <code>%SystemRoot%\System32\</code></li>
        <li>عدم وجود فرآیندهای فرزند انتظار رفته (<code>services.exe</code>، <code>lsass.exe</code>، <code>fontdrvhost.exe</code>)</li>
        <li>وجود فرآیندهای فرزند غیرمعمول</li>
    </ul>
</div>

<h3>5️⃣ Service Control Manager (services.exe)</h3>

<p>فرآیند <b>services.exe</b> که به عنوان Service Control Manager (SCM) شناخته می‌شود، یکی از حیاتی‌ترین اجزای سیستم‌عامل ویندوز است. این فرآیند مسئول مدیریت کامل سرویس‌های سیستمی است، از جمله شروع، توقف، متوقف کردن موقت و ادامه دادن سرویس‌ها. سرویس‌ها در کلید رجیستری <code>HKLM\SYSTEM\CurrentControlSet\Services\</code> تعریف می‌شوند و SCM این تعاریف را خوانده و بر اساس تنظیمات آن‌ها عمل می‌کند.</p>

<p>مسیر اجرایی این فرآیند <code>%SystemRoot%\System32\services.exe</code> است، والد آن <code>wininit.exe</code> می‌باشد و تحت حساب کاربری Local System اجرا می‌شود. تنها یک نمونه از این فرآیند در سیستم وجود دارد. این فرآیند والد بسیاری از فرآیندهای دیگر است، از جمله تمامی نمونه‌های <code>svchost.exe</code>، <code>spoolsv.exe</code>، <code>SearchIndexer.exe</code> و هر سرویس دیگری که به صورت مستقل اجرا می‌شود.</p>

<p>یکی از نکات مهم در مورد SCM این است که این فرآیند <b>Unified Background Process Manager (UBPM)</b> را نیز پیاده‌سازی می‌کند که مسئول فعالیت‌های پس‌زمینه مانند سرویس‌ها و وظایف زمان‌بندی شده است. علاوه بر این، پس از ورود موفقیت‌آمیز تعاملی یک کاربر، SCM بوت را موفق تلقی کرده و مقدار Last Known Good control set را در <code>HKLM\SYSTEM\Select\LastKnownGood</code> به مقدار CurrentControlSet تنظیم می‌کند.</p>

<p>از نظر امنیتی، وجود بیش از یک نمونه از <code>services.exe</code>، یا والد غیر از <code>wininit.exe</code>، یا اجرای تحت حساب کاربری غیر از Local System، همگی علائم جدی نفوذ هستند. همچنین، اگر فرآیندهای فرزند غیرمعمولی که سرویس شناخته شده‌ای نیستند وجود داشته باشند، این موارد نیز باید مورد بررسی دقیق قرار گیرند.</p>

<div class="danger-box">
    <strong>🚨 علائم هشداردهنده (<bdi dir="ltr">IoCs</bdi>):</strong>
    <ul class="indicators">
        <li>وجود بیش از یک نمونه از <code>services.exe</code></li>
        <li>والد غیر از <code>wininit.exe</code></li>
        <li>اجرای تحت حساب کاربری غیر از <bdi dir="ltr">Local System</bdi></li>
        <li>مسیر اجرایی متفاوت از <code>%SystemRoot%\System32\</code></li>
        <li>وجود فرآیندهای فرزند غیرمعمول که سرویس شناخته شده‌ای نیستند</li>
    </ul>
</div>

<h3>6️⃣ Generic Host Process for Services (svchost.exe)</h3>

<p>فرآیند <b>svchost.exe</b> یکی از شناخته‌شده‌ترین و در عین حال پیچیده‌ترین فرآیندهای ویندوز است. این فرآیند یک میزبان عمومی برای سرویس‌هایی است که به صورت DLL پیاده‌سازی شده‌اند. به دلیل کاهش مصرف منابع سیستم، ویندوز بسیاری از سرویس‌های خود را به صورت DLL پیاده‌سازی کرده و آن‌ها را در نمونه‌های مختلف <code>svchost.exe</code> اجرا می‌کند.</p>

<p>مسیر اجرایی این فرآیند <code>%SystemRoot%\System32\svchost.exe</code> است، والد آن <code>services.exe</code> می‌باشد و می‌تواند تحت حساب‌های کاربری مختلفی از جمله Local System، Network Service، Local Service و حتی حساب‌های کاربری معمولی اجرا شود. تعداد نمونه‌های این فرآیند بسیار زیاد است و معمولاً بین 10 تا 100 یا حتی بیشتر نمونه در یک سیستم معمولی وجود دارد.</p>

<p>نکته بسیار مهم در مورد <code>svchost.exe</code> این است که این فرآیند همیشه باید با پارامتر <code>-k</code> اجرا شود که Service Host Groups را در رجیستری مشخص می‌کند. گروه‌های معمول شامل DcomLaunch، RPCSS، LocalService، netsvcs، NetworkService و UnistackSvcGroup هستند. پارامتر <code>-s</code> نام سرویس خاص را مشخص می‌کند (مانند LanmanServer، WinRM یا Winmgmt) و پارامتر <code>-p</code> نشان‌دهنده اجرای سیاست (Policy Enforcement) است.</p>

<p>یکی از تغییرات مهم در ویندوز 10 نسخه 1703 این بود که مایکروسافت گروه‌بندی پیش‌فرض سرویس‌های مشابه را برای سیستم‌هایی با بیش از 3.5 گیگابایت RAM تغییر داد. در این سیستم‌ها، اکثر سرویس‌ها اکنون تحت نمونه مستقل خود از <code>svchost.exe</code> اجرا می‌شوند که منجر به وجود بیش از 50 نمونه از این فرآیند می‌شود. این تغییر برای افزایش قابلیت اطمینان و امنیت انجام شده است، زیرا مشکل در یک سرویس دیگر سرویس‌ها را تحت تأثیر قرار نمی‌دهد.</p>

<p>از نظر امنیتی، <code>svchost.exe</code> یک هدف محبوب برای مهاجمان است. علائم هشداردهنده شامل والد غیر از <code>services.exe</code>، اجرا بدون پارامتر <code>-k</code>، مسیر اجرایی مشکوک (مانند <code>svch0st.exe</code> با صفر به جای حرف o)، اجرای تحت حساب کاربری غیرمعمول، یا مصرف منابع غیرعادی (CPU یا شبکه) هستند. تحلیلگران باید با استفاده از ابزارهایی مانند Process Explorer، سرویس‌های در حال اجرا در هر نمونه <code>svchost.exe</code> را بررسی کنند و هرگونه سرویس ناشناخته یا مشکوک را شناسایی نمایند.</p>

<div class="danger-box">
    <strong>🚨 علائم هشداردهنده (<bdi dir="ltr">IoCs</bdi>):</strong>
    <ul class="indicators">
        <li>والد غیر از <code>services.exe</code></li>
        <li>اجرای بدون پارامتر <code>-k</code></li>
        <li>مسیر اجرایی مشکوک (مانند <code>svch0st.exe</code> با صفر به جای حرف o)</li>
        <li>اجرای تحت حساب کاربری غیرمعمول</li>
        <li>مصرف منابع غیرعادی (CPU یا شبکه)</li>
        <li>وجود سرویس‌های ناشناخته یا مشکوک در نمونه‌های <code>svchost.exe</code></li>
    </ul>
</div>

<div class="info-box">
    <strong>🔍 روش شناسایی:</strong>
    <pre><code># PowerShell Command to list services in each svchost.exe instance
Get-WmiObject Win32_Service | Where-Object {$_.PathName -like "*svchost.exe*"} | Select-Object Name, DisplayName, State, PathName, ProcessId

# Sysmon Event ID 1 Query
EventCode=1 Image="*\\svchost.exe" ParentImage!="*\\services.exe"</code></pre>
</div>

<h3>7️⃣ Local Security Authority Subsystem (lsass.exe)</h3>

<p>فرآیند <b>lsass.exe</b> یکی از حساس‌ترین و حیاتی‌ترین فرآیندهای امنیتی در ویندوز است. این فرآیند مسئول مدیریت احراز هویت در سیستم است و وظایف متعددی از جمله فراخوانی بسته احراز هویت مناسب (معمولاً Kerberos برای حساب‌های دامنه یا MSV1_0 برای حساب‌های محلی)، اجرای سیاست‌های امنیتی محلی (مانند سیاست‌های رمز عبور و سیاست‌های ممیزی) و نوشتن رویدادها در لاگ امنیتی را بر عهده دارد.</p>

<p>مسیر اجرایی این فرآیند <code>%SystemRoot%\System32\lsass.exe</code> است، والد آن <code>wininit.exe</code> می‌باشد و تحت حساب کاربری Local System اجرا می‌شود. تنها یک نمونه از این فرآیند در سیستم وجود دارد و این فرآیند به ندرت فرآیند فرزندی دارد (به استثنای مواردی مانند Encrypting File System که یک استثنای شناخته شده است).</p>

<p>یکی از نکات مهم که در پوستر اصلی به آن اشاره نشده بود، وجود فرآیند <b>lsaiso.exe</b> در سیستم‌هایی است که Virtualization-Based Security (VBS) و Credential Guard فعال شده‌اند. زمانی که VBS فعال باشد، عملکرد <code>lsass.exe</code> بین دو فرآیند تقسیم می‌شود: خود <code>lsass.exe</code> و <code>lsaiso.exe</code>. اکثر عملکردها در <code>lsass.exe</code> باقی می‌مانند، اما نقش مهم ذخیره‌سازی ایمن اعتبارنامه‌های حساب به <code>lsaiso.exe</code> منتقل می‌شود. <code>lsaiso.exe</code> با استفاده از فناوری مجازی‌سازی سخت‌افزاری، در یک محیط ایزوله اجرا می‌شود که از سایر فرآیندها جدا است. زمانی که احراز هویت از راه دور مورد نیاز است، <code>lsass.exe</code> درخواست‌ها را از طریق یک کانال RPC به <code>lsaiso.exe</code> ارسال می‌کند تا کاربر را به سرویس راه دور احراز هویت کند. اگر VBS فعال نباشد، <code>lsaiso.exe</code> نباید در سیستم اجرا شود.</p>

<p>از نظر امنیتی، <code>lsass.exe</code> یک هدف اصلی برای حملات Credential Dumping است. ابزارهایی مانند Mimikatz، ProcDump و Comsvcs.dll اغلب برای استخراج اعتبارنامه‌ها از حافظه این فرآیند استفاده می‌شوند. علائم هشداردهنده شامل وجود بیش از یک نمونه از <code>lsass.exe</code>، والد غیر از <code>wininit.exe</code>، داشتن فرآیندهای فرزند غیرمعمول، یا تلاش فرآیندهای غیرمعمول برای باز کردن یک handle به <code>lsass.exe</code> با دسترسی PROCESS_VM_READ هستند. تحلیلگران باید به دقت Event ID 10 در Sysmon (ProcessAccess) را برای شناسایی چنین تلاش‌هایی نظارت کنند.</p>

<div class="danger-box">
    <strong>🚨 علائم هشداردهنده (<bdi dir="ltr">IoCs</bdi>):</strong>
    <ul class="indicators">
        <li>وجود بیش از یک نمونه از <code>lsass.exe</code></li>
        <li>والد غیر از <code>wininit.exe</code></li>
        <li>وجود فرآیندهای فرزند غیرمعمول</li>
        <li>تلاش فرآیندهای غیرمعمول برای باز کردن handle به <code>lsass.exe</code> با دسترسی PROCESS_VM_READ</li>
        <li>وجود <code>lsaiso.exe</code> در سیستم‌هایی که VBS فعال نیست</li>
    </ul>
</div>

<div class="info-box">
    <strong>🔍 روش شناسایی:</strong>
    <pre><code># PowerShell Command to check access to lsass.exe
Get-Process lsass | Select-Object Id, ProcessName, @{Name="Accessed by"; Expression={(Get-Process -Module $_.Modules).ProcessName | Sort-Object -Unique}}

# Sysmon Event ID 10 Query
EventCode=10 TargetImage="*\\lsass.exe" GrantedAccess="0x1010" OR GrantedAccess="0x1410"</code></pre>
</div>

<h3>8️⃣ Windows Logon Process (winlogon.exe)</h3>

<p>فرآیند <b>winlogon.exe</b> مسئول مدیریت تمامی جنبه‌های ورود و خروج تعاملی کاربران است. زمانی که ورود موفقیت‌آمیز است و توسط فرآیند LSASS تأیید می‌شود، <code>winlogon.exe</code> فرآیندهای فهرست شده در مقدار رجیستری "Userinit" که در <code>HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon</code> قرار دارد را راه‌اندازی می‌کند. به طور پیش‌فرض، این مقدار به <code>userinit.exe</code> اشاره دارد.</p>

<p>مسیر اجرایی این فرآیند <code>%SystemRoot%\System32\winlogon.exe</code> است، والد آن یک نمونه فرزند از <code>smss.exe</code> با شماره نشست بیشتر از صفر است که پس از ایجاد <code>winlogon.exe</code> خاتمه یافته و بنابراین <code>winlogon.exe</code> به عنوان یک فرآیند یتیم ظاهر می‌شود. این فرآیند تحت حساب کاربری Local System اجرا می‌شود و یک نمونه برای هر نشست کاربری وجود دارد. فرآیندهای فرزند معمول این فرآیند عبارتند از <code>LogonUI.exe</code>، <code>userinit.exe</code>، <code>dwm.exe</code> و <code>fontdrvhost.exe</code>.</p>

<p>یکی از نکات مهم که در پوستر اصلی به آن اشاره نشده بود، این است که <code>winlogon.exe</code> یک نقطه بحرانی برای پایداری (Persistence) بدافزارها است. مهاجمان اغلب کلیدهای رجیستری مرتبط با <code>winlogon.exe</code> مانند <code>Userinit</code>، <code>Shell</code>، <code>Notify</code> و <code>UserInitMprLogonScript</code> را تغییر می‌دهند تا بدافزار خود را در هر بار ورود کاربر اجرا کنند. علاوه بر این، مهاجمان ممکن است از Winlogon Helper DLLs که در <code>HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify</code> تعریف شده‌اند، برای اجرای کد مخرب در رویدادهای مختلف ورود استفاده کنند.</p>

<p>از نظر امنیتی، والد غیر از <code>smss.exe</code> (یتیم)، اجرای تحت حساب کاربری غیر از Local System، یا عدم وجود به عنوان والد <code>userinit.exe</code> در زمان ورود، همگی علائم هشداردهنده هستند. تحلیلگران همچنین باید کلیدهای رجیستری مرتبط با <code>winlogon.exe</code> را به دقت بررسی کنند تا از عدم وجود مکانیزم‌های پایداری مخرب اطمینان حاصل کنند.</p>

<div class="danger-box">
    <strong>🚨 علائم هشداردهنده (<bdi dir="ltr">IoCs</bdi>):</strong>
    <ul class="indicators">
        <li>والد غیر از <code>smss.exe</code> (یتیم)</li>
        <li>اجرای تحت حساب کاربری غیر از <bdi dir="ltr">Local System</bdi></li>
        <li>عدم وجود به عنوان والد <code>userinit.exe</code> در زمان ورود</li>
        <li>تغییرات مشکوک در کلیدهای رجیستری مرتبط با <code>winlogon.exe</code></li>
        <li>وجود Winlogon Helper DLLs مشکوک</li>
    </ul>
</div>

<div class="info-box">
    <strong>🔍 روش شناسایی:</strong>
    <pre><code># PowerShell Command to check winlogon registry keys
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" | Select-Object Userinit, Shell, Notify, UserInitMprLogonScript

# Sysmon Event ID 1 Query
EventCode=1 Image="*\\winlogon.exe" ParentImage!="*\\smss.exe"</code></pre>
</div>

<h3>9️⃣ Windows Explorer (explorer.exe)</h3>

<p>فرآیند <b>explorer.exe</b> پوسته پیش‌فرض (Default Shell) ویندوز است که رابط کاربری گرافیکی اصلی را فراهم می‌کند. این فرآیند در هسته خود، به کاربران دسترسی به فایل‌ها را فراهم می‌کند، اما از نظر عملکردی، هم یک مرورگر فایل از طریق Windows Explorer است و هم یک رابط کاربری که ویژگی‌هایی مانند دسکتاپ کاربر، منوی استارت، نوار وظیفه، کنترل پنل و راه‌اندازی برنامه‌ها از طریق پسوندهای فایل و فایل‌های میانبر را فراهم می‌کند.</p>

<p>مسیر اجرایی این فرآیند <code>%SystemRoot%\explorer.exe</code> است (توجه کنید که در ریشه SystemRoot است، نه در System32)، والد آن فرآیند <code>userinit.exe</code> است که پس از راه‌اندازی <code>explorer.exe</code> خاتمه می‌یابد و بنابراین <code>explorer.exe</code> به عنوان یک فرآیند یتیم ظاهر می‌شود. این فرآیند تحت حساب کاربری کاربر وارد شده اجرا می‌شود و یک نمونه برای هر کاربر متصل به سیستم وجود دارد. این فرآیند والد بسیاری از فرآیندها است، زیرا اکثر برنامه‌هایی که کاربران به صورت تعاملی اجرا می‌کنند (مانند کلیک روی یک فایل یا یک میانبر) توسط <code>explorer.exe</code> راه‌اندازی می‌شوند.</p>

<p>یکی از نکات مهم که در پوستر اصلی به آن اشاره نشده بود، این است که پوسته پیش‌فرض در مقدار رجیستری <code>HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell</code> مشخص شده است و به طور پیش‌فرض به <code>explorer.exe</code> اشاره دارد. با این حال، ویندوز می‌تواند با رابط‌های دیگری مانند <code>cmd.exe</code> یا <code>powershell.exe</code> نیز کار کند. مهاجمان ممکن است این مقدار رجیستری را تغییر دهند تا یک پوسته مخرب را به جای <code>explorer.exe</code> اجرا کنند. همچنین، گزینه "Launch folder windows in a separate process" در ویندوز می‌تواند منجر به وجود چندین نمونه از <code>explorer.exe</code> برای هر کاربر شود.</p>

<p>از نظر امنیتی، والد غیر از <code>userinit.exe</code> (یتیم)، مسیر اجرایی در <code>System32</code> (باید در ریشه SystemRoot باشد)، یا اجرای تحت حساب Local System، همگی علائم هشداردهنده هستند. همچنین، اگر <code>explorer.exe</code> فرآیندهای فرزند غیرمعمولی مانند <code>cmd.exe</code>، <code>powershell.exe</code> یا ابزارهای سیستمی را بدون تعامل کاربر اجرا کند، این موارد باید مورد بررسی دقیق قرار گیرند زیرا ممکن است نشانه‌ای از یک نفوذ باشند.</p>

<div class="danger-box">
    <strong>🚨 علائم هشداردهنده (<bdi dir="ltr">IoCs</bdi>):</strong>
    <ul class="indicators">
        <li>والد غیر از <code>userinit.exe</code> (یتیم)</li>
        <li>مسیر اجرایی در <code>System32</code> (باید در ریشه SystemRoot باشد)</li>
        <li>اجرای تحت حساب <bdi dir="ltr">Local System</bdi></li>
        <li>اجرای فرآیندهای فرزند غیرمعمول بدون تعامل کاربر</li>
        <li>تغییر در مقدار رجیستری Shell به غیر از <code>explorer.exe</code></li>
    </ul>
</div>

<div class="info-box">
    <strong>🔍 روش شناسایی:</strong>
    <pre><code># PowerShell Command to check explorer.exe processes
Get-Process explorer | Select-Object Id, Path, Parent, StartTime

# Sysmon Event ID 1 Query
EventCode=1 Image="*\\explorer.exe" ParentImage!="*\\userinit.exe"</code></pre>
</div>

<h2>🔬 بخش دوم: تکنیک‌های پیشرفته شکار تهدیدات</h2>

<h3>🎯 تکنیک 1: تحلیل روابط والد-فرزندی غیرعادی</h3>

<p>یکی از مؤثرترین روش‌ها برای شناسایی فعالیت‌های مخرب، تحلیل روابط والد-فرزندی فرآیندها است. مهاجمان اغلب از فرآیندهای مشروع برای اجرای بدافزارهای خود استفاده می‌کنند، اما روابط غیرعادی بین فرآیندها می‌تواند نشانه‌ای از فعالیت مخرب باشد.</p>

<div class="technique-box">
    <h4>🔍 روابط غیرعادی که باید بررسی شوند:</h4>
    <ul>
        <li><code>svchost.exe</code> که فرآیندهای تعاملی مانند <code>cmd.exe</code> یا <code>powershell.exe</code> را اجرا می‌کند</li>
        <li><code>winword.exe</code> یا <code>excel.exe</code> که فرآیندهای سیستمی مانند <code>cmd.exe</code> را اجرا می‌کنند</li>
        <li><code>lsass.exe</code> که فرآیندهای فرزند غیرمعمول دارد</li>
        <li><code>csrss.exe</code> یا <code>smss.exe</code> که فرآیندهای فرزند دارند</li>
        <li><code>explorer.exe</code> که فرآیندهای سیستمی را بدون تعامل کاربر اجرا می‌کند</li>
    </ul>
</div>

<div class="info-box">
    <strong>🔍 روش شناسایی:</strong>
    <pre><code># PowerShell Command to find unusual parent-child relationships
Get-WmiObject Win32_Process | Select-Object Name, ParentProcessId, ProcessId, CommandLine | 
Where-Object {$_.Name -in @("cmd.exe","powershell.exe","wscript.exe","cscript.exe") -and 
(Get-WmiObject Win32_Process -Filter "ProcessId=$($_.ParentProcessId)").Name -in @("svchost.exe","winword.exe","excel.exe")}

# Sysmon Event ID 1 Query
EventCode=1 Image IN ("*\\cmd.exe","*\\powershell.exe","*\\wscript.exe","*\\cscript.exe") 
ParentImage IN ("*\\svchost.exe","*\\winword.exe","*\\excel.exe")</code></pre>
</div>

<h3>🎯 تکنیک 2: شناسایی Process Masquerading</h3>

<p>تکنیک Process Masquerading <span class="mitre-tag">T1036</span> یکی از روش‌های رایج مهاجمان برای پنهان‌سازی بدافزارها است. در این تکنیک، مهاجم نام یک فرآیند مشروع را برای بدافزار خود استفاده می‌کند تا از شناسایی فرار کند.</p>

<div class="technique-box">
    <h4>🔍 نشانه‌های Process Masquerading:</h4>
    <ul>
        <li>نام فرآیند با نام فایل مطابقت ندارد</li>
        <li>مسیر فایل با مسیر مورد انتظار برای آن فرآیند مطابقت ندارد</li>
        <li>چندین نمونه از فرآیندهایی که معمولاً تنها یک نمونه دارند (مانند <code>lsass.exe</code> یا <code>wininit.exe</code>)</li>
        <li>فرآیندهای سیستمی که از مسیرهای غیرعادی اجرا می‌شوند</li>
    </ul>
</div>

<div class="info-box">
    <strong>🔍 روش شناسایی:</strong>
    <pre><code># PowerShell Command to detect process masquerading
Get-Process | Where-Object {$_.ProcessName -ne $_.MainModule.ModuleName.Replace(".exe","")} | 
Select-Object ProcessName, Id, Path, @{Name="OriginalFileName"; Expression={$_.MainModule.FileName}}

# Sysmon Event ID 1 Query
EventCode=1 OriginalFileName!=Image</code></pre>
</div>

<h3>🎯 تکنیک 3: نظارت بر دسترسی به فرآیندهای حساس</h3>

<p>دسترسی غیرمجاز به فرآیندهای حساس مانند <code>lsass.exe</code> می‌تواند نشانه‌ای از تلاش برای استخراج اعتبارنامه‌ها باشد. نظارت بر این دسترسی‌ها می‌تواند به شناسایی حملات Credential Dumping کمک کند.</p>

<div class="technique-box">
    <h4>🔍 دسترسی‌های مشکوک که باید بررسی شوند:</h4>
    <ul>
        <li>دسترسی با مجوز PROCESS_VM_READ به <code>lsass.exe</code></li>
        <li>دسترسی با مجوز PROCESS_CREATE_PROCESS به فرآیندهای سیستمی</li>
        <li>دسترسی با مجوز PROCESS_WRITE_MEMORY به فرآیندهای سیستمی</li>
        <li>تلاش برای تزریق DLL به فرآیندهای سیستمی</li>
    </ul>
</div>

<div class="info-box">
    <strong>🔍 روش شناسایی:</strong>
    <pre><code># Sysmon Event ID 10 Query for suspicious access to lsass.exe
EventCode=10 TargetImage="*\\lsass.exe" GrantedAccess IN ("0x1010","0x1410","0x1438","0x1F0FFF")

# Sysmon Event ID 7 Query for image loads in sensitive processes
EventCode=7 ImageLoaded IN ("*\\samlib.dll","*\\vaultcli.dll","*\\mimilib.dll") 
TargetImage IN ("*\\lsass.exe","*\\svchost.exe","*\\services.exe")</code></pre>
</div>

<h3>🎯 تکنیک 4: تحلیل خطوط فرمان غیرعادی</h3>

<p>خطوط فرمان غیرعادی می‌توانند نشانه‌ای از فعالیت مخرب باشند. مهاجمان اغلب از پارامترهای خاص برای دور زدن دفاع‌ها یا اجرای کد مخرب استفاده می‌کنند.</p>

<div class="technique-box">
    <h4>🔍 خطوط فرمان مشکوک که باید بررسی شوند:</h4>
    <ul>
        <li><code>svchost.exe</code> بدون پارامتر <code>-k</code></li>
        <li><code>rundll32.exe</code> با پارامترهای غیرعادی</li>
        <li><code>regsvr32.exe</code> با اسکریپت‌های از راه دور</li>
        <li><code>certutil.exe</code> برای دانلود فایل</li>
        <li>پاورشل با پارامترهای انکود شده یا bypass</li>
    </ul>
</div>

<div class="info-box">
    <strong>🔍 روش شناسایی:</strong>
    <pre><code># Sysmon Event ID 1 Query for suspicious command lines
EventCode=1 Image="*\\svchost.exe" CommandLine NOT LIKE "*-k*"

EventCode=1 Image="*\\rundll32.exe" CommandLine LIKE "*javascript*" OR CommandLine LIKE "*comspec*"

EventCode=1 Image="*\\regsvr32.exe" CommandLine LIKE "*http*" OR CommandLine LIKE "*ftp*"

EventCode=1 Image="*\\certutil.exe" CommandLine LIKE "*urlcache*" OR CommandLine LIKE "*verifyctl*"

EventCode=1 Image="*\\powershell.exe" CommandLine LIKE "*-enc*" OR CommandLine LIKE "*-nop*" OR CommandLine LIKE "*-w hidden*"</code></pre>
</div>

<h2>🛠️ بخش سوم: ابزارها و منابع برای تحلیل فرآیندها</h2>

<h3>🔧 ابزارهای داخلی ویندوز</h3>

<div class="tool-box">
    <h4>📋 Task Manager</h4>
    <p>ابزار اصلی ویندوز برای مشاهده فرآیندهای در حال اجرا. نسخه‌های جدید ویندوز قابلیت‌های بیشتری برای تحلیل فرآیندها ارائه می‌دهند.</p>
    
    <h4>📋 Process Explorer</h4>
    <p>ابزار پیشرفته‌تر از Sysinternals که اطلاعات دقیق‌تری در مورد فرآیندها، DLLهای بارگذاری شده، اتصالات شبکه و handleها ارائه می‌دهد.</p>
    
    <h4>📋 Process Monitor</h4>
    <p>ابزاری برای نظارت بر فعالیت‌های فایل، رجیستری، فرآیند و شبکه در سیستم عامل.</p>
    
    <h4>📋 Sysmon</h4>
    <p>ابزار نظارتی که رویدادهای دقیق مربوط به فرآیندها، اتصالات شبکه و تغییرات فایل را در لاگ ویندوز ثبت می‌کند.</p>
</div>

<h3>🔧 ابزارهای شخص ثالث</h3>

<div class="tool-box">
    <h4>📋 Autoruns for Windows</h4>
    <p>ابزاری برای مشاهده و مدیریت برنامه‌هایی که با راه‌اندازی ویندوز اجرا می‌شوند، از جمله سرویس‌ها، درایورها و برنامه‌های راه‌انداز.</p>
    
    <h4>📋 Process Hacker</h4>
    <p>ابزار متن-باز و قدرتمند برای نظارت و مدیریت فرآیندها، سرویس‌ها و اتصالات شبکه.</p>
    
    <h4>📋 OSQuery</h4>
    <p>ابزاری برای نظارت بر سیستم عامل با استفاده از کوئری‌های SQL که امکان تحلیل پیشرفته فرآیندها را فراهم می‌کند.</p>
</div>

<h3>📚 منابع آموزشی و مرجع</h3>

<div class="info-box">
    <h4>📖 منابع آنلاین:</h4>
    <ul>
        <li><bdi dir="ltr">Windows Internals</bdi> - کتاب مرجع برای درک عمیق معماری ویندوز</li>
        <li><bdi dir="ltr">MITRE ATT&CK</bdi> - چارچوب جامع تکنیک‌های مورد استفاده مهاجمان</li>
        <li><bdi dir="ltr">Sysinternals Documentation</bdi> - مستندات کامل ابزارهای Sysinternals</li>
        <li><bdi dir="ltr">Windows Security Documentation</bdi> - مستندات رسمی مایکروسافت در مورد امنیت ویندوز</li>
    </ul>
</div>

<h2>📝 بخش چهارم: بهترین شیوه‌ها و توصیه‌ها</h2>

<h3>🎯 شیوه‌های نظارت بر فرآیندها</h3>

<div class="info-box">
    <h4>📋 بهترین شیوه‌ها:</h4>
    <ul>
        <li>استفاده از Sysmon برای ثبت رویدادهای فرآیند و دسترسی به فرآیندها</li>
        <li>ایجاد خط‌مشی‌های مشخص برای نظارت بر فرآیندهای حساس</li>
        <li>پیکربندی هشدارها برای فعالیت‌های غیرعادی</li>
        <li>انجام تحلیل خط‌مشی (Baseline Analysis) برای شناسایی رفتار نرمال سیستم</li>
        <li>به‌روز نگه داشتن دانش در مورد تکنیک‌های جدید مهاجمان</li>
    </ul>
</div>

<h3>🎯 شیوه‌های پاسخ به حوادث</h3>

<div class="info-box">
    <h4>📋 اقدامات توصیه شده:</h4>
    <ul>
        <li>ایزوله کردن سیستم‌های آلوده به سرعت</li>
        <li>جمع‌آوری حافظه و تصاویر دیسک برای تحلیل عمیق‌تر</li>
        <li>تحلیل لاگ‌های Sysmon برای شناسایی فعالیت‌های مخرب</li>
        <li>بررسی روابط والد-فرزندی فرآیندهای مشکوک</li>
        <li>تحلیل خطوط فرمان و پارامترهای استفاده شده</li>
        <li>بررسی نقاط پایداری (Persistence) در سیستم</li>
    </ul>
</div>

<h2>🎯 نتیجه‌گیری</h2>

<p>تحلیل فرآیندهای حیاتی ویندوز یکی از مهارت‌های بنیادین برای هر تحلیلگر امنیتی است. شناخت دقیق رفتار نرمال این فرآیندها، روابط والد-فرزندی آن‌ها و ویژگی‌های کلیدی، به ما امکان می‌دهد تا فعالیت‌های غیرعادی و مخرب را به سرعت شناسایی کنیم.</p>

<p>مهاجمان پیشرفته به طور مداوم در حال توسعه تکنیک‌های جدید برای پنهان‌سازی فعالیت‌های خود در پوشش فرآیندهای مشروع هستند. بنابراین، تحلیلگران امنیتی باید دانش خود را به‌روز نگه دارند و از ابزارهای پیشرفته برای نظارت بر فرآیندها استفاده کنند.</p>

<p>با استفاده از تکنیک‌ها و ابزارهای معرفی شده در این مقاله، تحلیلگران می‌توانند توانایی خود را در شناسایی و تحلیل فعالیت‌های مخرب به طور قابل توجهی بهبود بخشند و در نتیجه، امنیت سازمان‌های خود را افزایش دهند.</p>

<div class="warning-box">
    <strong>⚠️ نکته نهایی:</strong><br>
    هیچ ابزار یا تکنیکی به تنهایی کافی نیست. شکار تهدیدات موفق نیازمند ترکیبی از دانش فنی عمیق، ابزارهای مناسب و رویکرد تحلیلی است که به طور مداوم بهبود می‌یابد.
</div>

<div class="footer">
    <p>© 2023 - راهنمای تحلیل فرآیندهای حیاتی ویندوز برای شکار تهدیدات</p>
    <p>این مقاله برای اهداف آموزشی تهیه شده است و استفاده از آن با ذکر منبع بلامانع است.</p>
</div>

</div>

</body>
</html>
