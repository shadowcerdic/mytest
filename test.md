<div dir="rtl">
عالی. مقاله بسیار خوب و با جزئیات فنی دقیقی ارائه شده است. این متن نشان‌دهنده دانش عمیق نویسنده در مورد فرآیندهای داخلی ویندوز است. به‌عنوان یک ارزیاب، وظیفه من تقویت نقاط قوت و بهبود بخش‌هایی است که می‌توانند مقاله را برای جامعه امنیت سایبری ارزشمندتر کنند.


تحلیل و بازنویسی را در دو بخش ارائه می‌دهم:

بخش اول: تحلیل دقیق مقاله بر اساس معیارها

بخش دوم: نسخه ویرایش‌شده و بازنویسی‌شده مقاله

بخش اول: تحلیل دقیق مقاله

در اینجا، مقاله را بر اساس معیارهای مشخص‌شده به‌صورت شفاف تحلیل می‌کنم.

۱. کاربردی بودن (Applicability)

تحلیل: بسیار بالا. این مقاله مستقیماً به نیاز اصلی تحلیلگران مرکز عملیات امنیت (<bdi>SOC</bdi>)، شکارچیان تهدید (<bdi>Threat Hunters</bdi>) و تیم‌های پاسخ به حوادث (<bdi>Incident Responders</bdi>) پاسخ می‌دهد. اطلاعات ارائه‌شده در مورد روابط والد-فرزندی، حساب‌های کاربری و مسیرهای اجرایی، اساس ساخت فرضیه‌های شکار تهدید و نوشتن قواعد تشخیصی (<bdi>Detection Rules</bdi>) است. هر بخش از این مقاله می‌تواند به‌عنوان یک مرجع سریع در حین بررسی یک سیستم مشکوک استفاده شود.

۲. عملی بودن و قابلیت پیاده‌سازی (Practicality & Implementability)

تحلیل: بسیار بالا. راهکارهای پیشنهادی (که به‌صورت ضمنی در قالب علائم هشداردهنده آمده‌اند) نیازمند ابزارهای گران‌قیمت نیستند. یک تحلیلگر می‌تواند با استفاده از ابزارهای رایگان مانند <bdi>Sysinternals Process Explorer</bdi>، <bdi>Process Hacker</bdi> یا لاگ‌های جمع‌آوری‌شده توسط <bdi>Sysmon</bdi>، تمام موارد ذکرشده را بررسی کند. این مقاله بر دانش و مهارت تحلیلگر تمرکز دارد نه بر خرید یک ابزار خاص.

۳. نزدیکی به واقعیت‌های میدانی (Field Realism)

تحلیل: عالی. مقاله با واقعیت‌های میدانی کاملاً تطابق دارد. اشاره به تکنیک‌هایی مانند <bdi>Process Masquerading</bdi> (با مثال <bdi>smsss.exe</bdi>)، <bdi>Process Hollowing</bdi> (در بخش <bdi>csrss.exe</bdi>) و استخراج اطلاعات حساس از حافظه (<bdi>Credential Dumping</bdi> از <bdi>lsass.exe</bdi>) نشان می‌دهد که نویسنده با بردارهای حمله واقعی آشناست. همچنین، اشاره به جزئیات به‌روز مانند فرآیند <bdi>Memory Compression</bdi>، تغییر رفتار <bdi>svchost.exe</bdi> در نسخه‌های جدید ویندوز و وجود <bdi>lsaiso.exe</bdi> هنگام فعال بودن <bdi>Credential Guard</bdi>، ارزش مقاله را دوچندان کرده است.

۴. درستی فنی و منطقی (Technical & Logical Accuracy)

تحلیل: عالی. محتوای فنی ارائه‌شده کاملاً دقیق است. اطلاعات مربوط به شناسه‌های فرآیند (<bdi>PID</bdi>)، فرآیندهای یتیم (<bdi>Orphan Processes</bdi>)، پارامترهای خط فرمان (مانند پارامتر <bdi>-k</bdi> برای <bdi>svchost.exe</bdi>) و مسیرهای اجرایی صحیح هستند و با مستندات رسمی مایکروسافت همخوانی دارند.

۵. ارائه راهکار و ارزش افزوده (Actionability & Value-Add)

تحلیل: بسیار خوب. مقاله صرفاً به توصیف نمی‌پردازد و برای هر فرآیند، "علائم هشداردهنده" مشخصی ارائه می‌دهد. این بخش، ارزش افزوده‌ی اصلی مقاله است. با این حال، می‌توان این بخش را با ارائه فرضیه‌های مشخص برای شکار تهدید (<bdi>Hunting Hypotheses</bdi>) یا مثال‌هایی از کوئری‌های <bdi>SIEM/EDR</bdi> تقویت کرد تا مقاله بیش از پیش عملیاتی شود.

۶. ساختار و انسجام محتوا (Structure & Cohesion)

تحلیل: خوب. ساختار کلی مقاله منطقی است: یک مقدمه قوی و سپس بررسی تک‌تک فرآیندها. با این حال، مقاله فاقد یک نتیجه‌گیری مدون است که نکات کلیدی را جمع‌بندی کرده و گام‌های بعدی را برای خواننده مشخص کند. همچنین، می‌توان ساختار داخلی هر بخش را با استفاده از زیرعنوان‌ها بهبود داد تا خوانایی افزایش یابد.

۷. وضوح و خوانایی (Clarity & Readability)

تحلیل: خوب. زبان مقاله برای مخاطب متخصص کاملاً واضح است. اما می‌توان با شکستن پاراگراف‌های طولانی، استفاده از لیست‌های شماره‌گذاری‌شده یا نقطه‌ای (<bdi>bullet points</bdi>) و برجسته‌سازی (bold کردن) اصطلاحات کلیدی، متن را برای مرور سریع (اسکن کردن) مناسب‌تر کرد. مشکل اصلی، عدم رعایت قوانین قالب‌بندی متون فارسی-انگلیسی است که در نسخه ویرایش‌شده اصلاح خواهد شد.

۸. اصالت و نوآوری (Originality & Innovation)

تحلیل: خوب. اگرچه این اطلاعات در منابع مختلفی مانند پوسترهای <bdi>SANS</bdi> یا مستندات مایکروسافت وجود دارد، این مقاله با جمع‌آوری، دسته‌بندی و ارائه این اطلاعات از منظر یک تحلیلگر امنیتی، اصالت و ارزش پیدا کرده است. بخش‌های مربوط به <bdi>lsaiso.exe</bdi> و مکانیسم‌های پایداری مرتبط با <bdi>winlogon.exe</bdi> نشان‌دهنده تلاشی فراتر از بازنویسی صرف است.

۹. ارجاع‌دهی و منابع (Citations & Sources)

تحلیل: ضعیف. مقاله فاقد هرگونه ارجاع به منابع معتبر است. افزودن منابعی مانند مستندات رسمی <bdi>Microsoft Docs</bdi>، وبلاگ <bdi>Sysinternals</bdi>، یا ارجاع به تکنیک‌های متناظر در چارچوب <bdi>MITRE ATT&CK®</bdi> می‌تواند اعتبار مقاله را به‌شدت افزایش داده و راه را برای مطالعه بیشتر خوانندگان باز کند.

جمع‌بندی تحلیل: این مقاله یک محتوای فنی بسیار قوی و ارزشمند است. نقاط قوت اصلی آن دقت فنی و کاربردی بودن آن است. نقاط قابل بهبود، عمدتاً در ساختار، ارجاع‌دهی و قالب‌بندی خلاصه می‌شوند که با ویرایش قابل رفع هستند.

بخش دوم: نسخه ویرایش‌شده و بازنویسی‌شده مقاله

در این نسخه، تلاش کرده‌ام ضمن حفظ محتوای فنی ارزشمند مقاله، ساختار آن را بهبود بخشیده، خوانایی را افزایش دهم، بخش نتیجه‌گیری و منابع را اضافه کنم و قوانین نگارشی را به‌طور کامل رعایت نمایم.

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
