<div dir="rtl" style="text-align: justify; line-height: 1.8; font-family: 'Vazirmatn', sans-serif;">

  <h3>فرآیند System و Memory Compression در ویندوز</h3>

  <p>
    فرآیند <code>System</code> با شناسه فرآیند (PID) ثابت <bdi>4</bdi>، یکی از فرآیندهای ویژه در معماری سیستم‌عامل ویندوز است. این فرآیند میزبان تمام تردهای حالت هسته (<bdi>Kernel Mode Threads</bdi>) بوده و برخلاف سایر فرآیندها، در فضای کاربر (<bdi>User Space</bdi>) اجرا نمی‌شود. 
    در واقع، آنچه در ابزارهایی مانند <bdi>Task Manager</bdi> یا <bdi>Process Explorer</bdi> مشاهده می‌شود، تنها نمایش‌دهندهٔ فعالیت‌های سطح هسته است، نه یک فرآیند مستقل کاربری.
  </p>

  <p>
    مسیر اجرایی این فرآیند بسته به ابزار متفاوت است: در <bdi>Task Manager</bdi> و <bdi>Process Hacker</bdi> با نام <code>ntoskrnl.exe</code> نمایش داده می‌شود، در حالی که در <bdi>Process Explorer</bdi> مقدار آن «None» است. 
    این فرآیند تنها یک فرآیند فرزند دارد که همان <code>smss.exe</code> است. در نتیجه، مشاهدهٔ هرگونه فرآیند فرزند دیگر می‌تواند نشانه‌ای از نفوذ یا فعالیت غیرعادی در سیستم باشد.
  </p>

  <h4>Memory Compression — فشرده‌سازی حافظه در ویندوز</h4>

  <p>
    در نسخه‌های جدید ویندوز (از ویندوز ۱۰ به بعد)، مؤلفه‌ای با نام <b>Memory Compression</b> معرفی شده است که نقش آن بهینه‌سازی مصرف حافظهٔ رم است. 
    این مؤلفه داده‌های کمتر استفاده‌شدهٔ فرآیندها را به‌صورت فشرده در حافظه نگه می‌دارد تا از انتقال مکرر آن‌ها به دیسک (Paging) جلوگیری شود. 
    در نتیجه، زمان پاسخ‌دهی سیستم و عملکرد کلی بهبود می‌یابد.
  </p>

  <p>
    این فرآیند بخشی از گروه <bdi>System and Compressed Memory</bdi> بوده و به‌صورت داخلی تحت والد <code>System</code> اجرا می‌شود. 
    حساب کاربری آن <bdi>Local System</bdi> است و تنها یک نمونه از آن در سیستم وجود دارد. 
    از آنجا که این مؤلفه نسبتاً جدید است، گاهی تحلیل‌گران ممکن است آن را اشتباه به‌عنوان یک فرآیند ناشناخته یا بدافزار در نظر بگیرند.
  </p>

  <p>
    درک صحیح نقش و ساختار این فرآیندها، به‌ویژه برای تیم‌های امنیتی و تحلیل‌گران <bdi>Incident Response</bdi>، اهمیت زیادی دارد؛ زیرا رفتار نرمال <code>System</code> و وابستگانش معیار اصلی در تشخیص فعالیت‌های مشکوک است.
  </p>

  <p style="font-size: 0.9em; color: #555;">
    <b>منبع:</b> 
    <a href="https://learn.microsoft.com/en-us/windows/client-management/memory-compression" target="_blank">
      Microsoft Docs – Memory Compression in Windows
    </a>
  </p>

</div>

<style>
  code {
    direction: ltr;
    font-family: monospace;
    background-color: #f4f4f4;
    padding: 2px 4px;
    border-radius: 4px;
  }
  bdi {
    direction: ltr;
    unicode-bidi: isolate;
  }
  h3, h4 {
    color: #333;
    margin-top: 1.5em;
  }
</style>
