<div dir="rtl">

<div dir="rtl" align="right">

<h2>راهنمای تحلیلگر: کالبدشکافی فرآیندهای حیاتی ویندوز برای شکار تهدید</h2>

<h3>مقدمه: چرا شناخت رفتار نرمال، اولین گام در دفاع سایبری است؟</h3>

<p>یکی از اصول بنیادین در حوزه‌هایی چون **شکار تهدیدات (<bdi>Threat Hunting</bdi>)** و **پاسخ به حوادث (<bdi>Incident Response</bdi>)**، شناخت دقیق و جامع از رفتار نرمال یک سیستم‌عامل است. بدون این خط‌پایه (<bdi>Baseline</bdi>)، تشخیص فعالیت‌های غیرعادی و مخرب تقریباً غیرممکن خواهد بود. مهاجمان پیشرفته با بهره‌گیری از تکنیک‌های پیچیده‌ای نظیر <bdi>Process Masquerading</bdi>، <bdi>Process Hollowing</bdi> و <bdi>DLL Injection</bdi>، تلاش می‌کنند تا فعالیت‌های خود را در پوشش فرآیندهای مشروع سیستمی پنهان سازند. به همین دلیل، تسلط بر ویژگی‌های دقیق فرآیندهای سیستمی ویندوز—از جمله روابط والد-فرزندی، زمینه‌های اجرایی (<bdi>User Context</bdi>) و آرگومان‌های خط فرمان—برای هر تحلیلگر امنیتی یک ضرورت است.</p>

<hr>

<h4>۱. فرآیند <bdi>System</bdi> و <bdi>Memory Compression</bdi></h4>
<ul>
<li><strong>ویژگی‌های کلیدی <bdi>System</bdi>:</strong>
<ul>
<li><strong>شناسه فرآیند (<bdi>PID</bdi>):</strong> همیشه <bdi>4</bdi> است.</li>
<li><strong>نقش:</strong> میزبان ترد‌های حالت هسته (<bdi>Kernel Mode Threads</bdi>). این فرآیند یک نمایش مجازی از فعالیت‌های هسته (<bdi>ntoskrnl.exe</bdi>) است و در فضای کاربر (<bdi>User Space</bdi>) اجرا نمی‌شود.</li>
<li><strong>والد:</strong> ندارد (ریشه درخت فرآیندهاست).</li>
<li><strong>فرزندان مشروع:</strong> تنها یک فرزند به‌نام <code><bdi>smss.exe</bdi></code> دارد.</li>
</ul>
</li>
<li><strong>ویژگی‌های کلیدی <bdi>Memory Compression</bdi>:</strong>
<ul>
<li><strong>نقش:</strong> فشرده‌سازی صفحات حافظه در <bdi>RAM</bdi> برای بهبود عملکرد (معرفی‌شده در ویندوز 10).</li>
<li><strong>والد:</strong> فرآیند <bdi>System</bdi>.</li>
<li><strong>فرزندان:</strong> هیچ فرزندی ندارد.</li>
<li><strong>حساب کاربری:</strong> <bdi>NT AUTHORITY\SYSTEM</bdi>.</li>
</ul>
</li>
<li><strong>⚠️ نقاط قابل توجه برای شکار تهدید:</strong>
<ul>
<li>هر فرآیند فرزندی برای <bdi>System</bdi> به‌جز <code><bdi>smss.exe</bdi></code>، به‌شدت مشکوک است و می‌تواند نشانه‌ای از تزریق کد به هسته (<bdi>Kernel-mode Rootkit</bdi>) باشد.</li>
<li>فرآیند <bdi>Memory Compression</bdi> نسبتاً جدید است؛ عدم آشنایی تحلیلگران با آن ممکن است منجر به هشدارهای نادرست (<bdi>False Positives</bdi>) شود.</li>
</ul>
</li>
</ul>

<h4>۲. زیرسیستم مدیر نشست (<bdi>smss.exe</bdi>)</h4>
<ul>
<li><strong>ویژگی‌های کلیدی:</strong>
<ul>
<li><strong>نقش:</strong> اولین فرآیند حالت کاربر که مسئول راه‌اندازی نشست‌های کاربری (<bdi>Sessions</bdi>) و ایجاد فرآیندهای حیاتی دیگر است.</li>
<li><strong>مسیر:</strong> <code><bdi>%SystemRoot%\System32\smss.exe</bdi></code></li>
<li><strong>والد:</strong> فرآیند <bdi>System (PID 4)</bdi>.</li>
<li><strong>فرزندان:</strong> نمونه اصلی (<bdi>Master</bdi>) که باقی می‌ماند، نمونه‌های موقتی برای هر نشست ایجاد می‌کند که فرآیندهای <code><bdi>wininit.exe</bdi></code> (برای <bdi>Session 0</bdi>)، <code><bdi>winlogon.exe</bdi></code> (برای نشست‌های کاربری) و <code><bdi>csrss.exe</bdi></code> را ایجاد کرده و سپس خاتمه می‌یابند.</li>
</ul>
</li>
<li><strong>⚠️ نقاط قابل توجه برای شکار تهدید:</strong>
<ul>
<li>وجود بیش از یک نمونه پایدار از <code><bdi>smss.exe</bdi></code>.</li>
<li>والد فرآیندی غیر از <bdi>System (PID 4)</bdi>.</li>
<li>اجرا از مسیری غیر از <code><bdi>System32</bdi></code>.</li>
<li>بدافزارهایی که با نام‌های مشابه مانند <code><bdi>smsss.exe</bdi></code> خود را پنهان می‌کنند (<bdi>Masquerading</bdi>).</li>
</ul>
</li>
</ul>

<h4>۳. زیرسیستم کلاینت/سرور (<bdi>csrss.exe</bdi>)</h4>
<ul>
<li><strong>ویژگی‌های کلیدی:</strong>
<ul>
<li><strong>نقش:</strong> مدیریت کنسول ویندوز، ایجاد و حذف ترد‌ها و پشتیبانی از زیرسیستم گرافیکی.</li>
<li><strong>مسیر:</strong> <code><bdi>%SystemRoot%\System32\csrss.exe</bdi></code></li>
<li><strong>والد:</strong> توسط یک نمونه موقتی از <code><bdi>smss.exe</bdi></code> ایجاد شده و سپس یتیم (<bdi>Orphaned</bdi>) می‌شود.</li>
<li><strong>تعداد نمونه‌ها:</strong> حداقل دو نمونه (یکی برای <bdi>Session 0</bdi> و دیگری برای اولین کاربر).</li>
</ul>
</li>
<li><strong>⚠️ نقاط قابل توجه برای شکار تهدید:</strong>
<ul>
<li>این فرآیند **هرگز نباید فرزندی داشته باشد**. وجود هرگونه فرزند یک نشانه قطعی از فعالیت مخرب است.</li>
<li>داشتن یک والد فعال (غیر یتیم بودن).</li>
<li>تزریق کد از طریق تکنیک <bdi>Process Hollowing</bdi> به این فرآیند مشروع.</li>
</ul>
</li>
</ul>

<h4>۴. فرآیند مقداردهی اولیه ویندوز (<bdi>wininit.exe</bdi>)</h4>
<ul>
<li><strong>ویژگی‌های کلیدی:</strong>
<ul>
<li><strong>نقش:</strong> راه‌اندازی سرویس‌های حیاتی مانند مدیر کنترل سرویس (<bdi>SCM</bdi>)، <bdi>LSASS</bdi> و <bdi>Local Session Manager</bdi>.</li>
<li><strong>مسیر:</strong> <code><bdi>%SystemRoot%\System32\wininit.exe</bdi></code></li>
<li><strong>والد:</strong> توسط یک نمونه موقتی از <code><bdi>smss.exe</bdi></code> ایجاد شده و سپس یتیم می‌شود.</li>
<li><strong>فرزندان مشروع:</strong> <code><bdi>services.exe</bdi></code>, <code><bdi>lsass.exe</bdi></code> و <code><bdi>fontdrvhost.exe</bdi></code>.</li>
</ul>
</li>
<li><strong>⚠️ نقاط قابل توجه برای شکار تهدید:</strong>
<ul>
<li>وجود بیش از یک نمونه از <code><bdi>wininit.exe</bdi></code>.</li>
<li>داشتن فرزندی غیر از سه فرآیند ذکر شده.</li>
<li>اجرا با والدی غیر از یک <code><bdi>smss.exe</bdi></code> خاتمه‌یافته.</li>
</ul>
</li>
</ul>

<h4>۵. مدیر کنترل سرویس (<bdi>services.exe</bdi>)</h4>
<ul>
<li><strong>ویژگی‌های کلیدی:</strong>
<ul>
<li><strong>نقش:</strong> مدیریت (شروع، توقف، ...) تمام سرویس‌های سیستمی.</li>
<li><strong>مسیر:</strong> <code><bdi>%SystemRoot%\System32\services.exe</bdi></code></li>
<li><strong>والد:</strong> <code><bdi>wininit.exe</bdi></code>.</li>
<li><strong>فرزندان:</strong> والد تمام نمونه‌های <code><bdi>svchost.exe</bdi></code> و سایر سرویس‌هایی است که در فرآیند خودشان اجرا می‌شوند (مانند <code><bdi>spoolsv.exe</bdi></code>).</li>
</ul>
</li>
<li><strong>⚠️ نقاط قابل توجه برای شکار تهدید:</strong>
<ul>
<li>والد غیر از <code><bdi>wininit.exe</bdi></code>.</li>
<li>وجود بیش از یک نمونه در سیستم.</li>
<li>اجرای یک فرآیند غیرمنتظره به‌عنوان فرزند (مثلاً <code><bdi>cmd.exe</bdi></code> یا <code><bdi>powershell.exe</bdi></code>)، که می‌تواند نشان‌دهنده اجرای یک سرویس مخرب باشد.</li>
</ul>
</li>
</ul>

<h4>۶. فرآیند میزبان عمومی برای سرویس‌ها (<bdi>svchost.exe</bdi>)</h4>
<ul>
<li><strong>ویژگی‌های کلیدی:</strong>
<ul>
<li><strong>نقش:</strong> میزبانی سرویس‌های مبتنی بر <bdi>DLL</bdi> برای صرفه‌جویی در منابع.</li>
<li><strong>مسیر:</strong> <code><bdi>%SystemRoot%\System32\svchost.exe</bdi></code></li>
<li><strong>والد:</strong> همیشه <code><bdi>services.exe</bdi></code> است.</li>
<li><strong>خط فرمان:</strong> همیشه باید با پارامتر <code><bdi>-k</bdi></code> (برای تعیین گروه سرویس) اجرا شود.</li>
<li><strong>تعداد:</strong> بسیار زیاد (ده‌ها نمونه در یک سیستم مدرن طبیعی است).</li>
</ul>
</li>
<li><strong>⚠️ نقاط قابل توجه برای شکار تهدید:</strong>
<ul>
<li>والد غیر از <code><bdi>services.exe</bdi></code> (این یک نشانه بسیار قوی از نفوذ است).</li>
<li>اجرا بدون پارامتر خط فرمان <code><bdi>-k</bdi></code>.</li>
<li>اجرا از مسیری مشکوک یا با نامی مشابه (مثلاً <code><bdi>svch0st.exe</bdi></code>).</li>
<li>اتصالات شبکه غیرعادی یا مصرف بالای پردازنده (<bdi>CPU</bdi>) که با سرویس‌های میزبانی‌شده توسط آن همخوانی ندارد.</li>
</ul>
</li>
</ul>

<h4>۷. زیرسیستم مرجع امنیت محلی (<bdi>lsass.exe</bdi>)</h4>
<ul>
<li><strong>ویژگی‌های کلیدی:</strong>
<ul>
<li><strong>نقش:</strong> مسئول اجرای سیاست‌های امنیتی و مدیریت احراز هویت کاربران. حافظه این فرآیند حاوی اطلاعات حساس کاربری (مانند هش‌ها و تیکت‌های <bdi>Kerberos</bdi>) است.</li>
<li><strong>مسیر:</strong> <code><bdi>%SystemRoot%\System32\lsass.exe</bdi></code></li>
<li><strong>والد:</strong> <code><bdi>wininit.exe</bdi></code>.</li>
<li><strong>نکته:</strong> در سیستم‌های با <bdi>Credential Guard</bdi> فعال، فرآیند <code><bdi>lsaiso.exe</bdi></code> نیز اجرا می‌شود تا اعتبارنامه‌ها را در محیطی ایزوله محافظت کند.</li>
</ul>
</li>
<li><strong>⚠️ نقاط قابل توجه برای شکار تهدید:</strong>
<ul>
<li><strong>هدف اصلی حملات <bdi>Credential Dumping</bdi>!</strong></li>
<li>هرگونه تلاش برای دسترسی به حافظه این فرآیند (مثلاً توسط <code><bdi>rundll32.exe</bdi></code> یا <code><bdi>taskmgr.exe</bdi></code>) بسیار مشکوک است (قابل ردگیری با <bdi>Sysmon Event ID 10</bdi>).</li>
<li>وجود بیش از یک نمونه یا والدی غیر از <code><bdi>wininit.exe</bdi></code>.</li>
<li>نام‌های مشابه مانند <code><bdi>lsasss.exe</bdi></code>.</li>
</ul>
</li>
</ul>

<h4>۸. فرآیند ورود به ویندوز (<bdi>winlogon.exe</bdi>)</h4>
<ul>
<li><strong>ویژگی‌های کلیدی:</strong>
<ul>
<li><strong>نقش:</strong> مدیریت ورود و خروج تعاملی کاربران.</li>
<li><strong>مسیر:</strong> <code><bdi>%SystemRoot%\System32\winlogon.exe</bdi></code></li>
<li><strong>والد:</strong> توسط یک نمونه موقتی از <code><bdi>smss.exe</bdi></code> ایجاد شده و سپس یتیم می‌شود.</li>
<li><strong>فرزند مشروع اصلی:</strong> <code><bdi>userinit.exe</bdi></code>.</li>
</ul>
</li>
<li><strong>⚠️ نقاط قابل توجه برای شکار تهدید:</strong>
<ul>
<li><strong>نقطه محبوب برای مکانیزم‌های پایداری (<bdi>Persistence</bdi>).</strong></li>
<li>مقادیر رجیستری زیر باید به‌دقت بررسی شوند:
<ul>
<li><code><bdi>HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\Userinit</bdi></code></li>
<li><code><bdi>HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell</bdi></code></li>
</ul>
</li>
<li>مهاجمان ممکن است یک <bdi>DLL</bdi> مخرب را به‌عنوان <bdi>Winlogon Helper DLL</bdi> ثبت کنند.</li>
</ul>
</li>
</ul>

<h4>۹. ویندوز اکسپلورر (<bdi>explorer.exe</bdi>)</h4>
<ul>
<li><strong>ویژگی‌های کلیدی:</strong>
<ul>
<li><strong>نقش:</strong> پوسته (<bdi>Shell</bdi>) پیش‌فرض ویندوز که رابط کاربری گرافیکی (دسکتاپ، منوی استارت و ...) را فراهم می‌کند.</li>
<li><strong>مسیر:</strong> <code><bdi>%SystemRoot%\explorer.exe</bdi></code> (<strong>نکته مهم:</strong> در ریشه <bdi>SystemRoot</bdi> است، نه <bdi>System32</bdi>).</li>
<li><strong>والد:</strong> توسط <code><bdi>userinit.exe</bdi></code> ایجاد شده و سپس یتیم می‌شود.</li>
<li><strong>حساب کاربری:</strong> تحت حساب کاربری وارد شده اجرا می‌شود (نه <bdi>SYSTEM</bdi>).</li>
</ul>
</li>
<li><strong>⚠️ نقاط قابل توجه برای شکار تهدید:</strong>
<ul>
<li>اجرا از مسیری غیر از <code><bdi>%SystemRoot%</bdi></code>، به‌ویژه از <code><bdi>System32</bdi></code>.</li>
<li>اجرا تحت حساب کاربری <bdi>SYSTEM</bdi>.</li>
<li>داشتن والدی غیر از یک <code><bdi>userinit.exe</bdi></code> خاتمه‌یافته.</li>
<li>اجرای ابزارهای خط فرمان مانند <code><bdi>cmd.exe</bdi></code> یا <code><bdi>powershell.exe</bdi></code> به عنوان فرزند، بدون تعامل کاربر.</li>
</ul>
</li>
</ul>

<hr>

<h3>نتیجه‌گیری: از دانش تا اقدام</h3>
<p>
این کالبدشکافی نشان می‌دهد که هر فرآیند سیستمی ویندوز دارای یک "اثر انگشت" رفتاری مشخص است. مهاجمانی که تلاش می‌کنند در این چارچوب مشروع پنهان شوند، ناگزیر ناهنجاری‌هایی (<bdi>Anomalies</bdi>) از خود به جای می‌گذارند. وظیفه یک تحلیلگر هوشیار، شناخت عمیق این الگوهای نرمال و ساخت فرضیه‌های شکار برای یافتن این انحرافات است. استفاده از این دانش به‌عنوان پایه، اثربخشی ابزارهایی مانند <bdi>SIEM</bdi> و <bdi>EDR</bdi> را به میزان چشمگیری افزایش می‌دهد و دفاع را از حالت واکنشی به حالت فعال و پیش‌دستانه تغییر می‌دهد.
</p>

<h3>منابع پیشنهادی برای مطالعه بیشتر</h3>
<ul>
<li><a href="https://docs.microsoft.com/en-us/sysinternals/">مجموعه ابزارهای <bdi>Sysinternals</bdi></a></li>
<li><a href="https://attack.mitre.org/">چارچوب <bdi>MITRE ATT&CK®</bdi></a> (به‌ویژه تاکتیک‌های <bdi>Execution</bdi>, <bdi>Persistence</bdi> و <bdi>Defense Evasion</bdi>)</li>
<li>پوستر <bdi>SANS Hunt Evil</bdi></li>
<li>کتاب <bdi>Windows Internals</bdi> نوشته Pavel Yosifovich, Mark Russinovich, et al.</li>
</ul>

</div>
</div>
