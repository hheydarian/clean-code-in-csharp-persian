# 3
 ## Classes, Objects, and Data Structures
 
 در این فصل، به سازمان‌دهی، قالب‌بندی و مستندسازی کلاس‌ها می‌پردازیم. همچنین به نحوه‌ی نوشتن اشیاء و ساختارهای داده‌ای پاک در زبان #C می‌پردازیم که از قانون دمیتر (Law of Demeter) پیروی می‌کنند.

علاوه بر این، به اشیاء و ساختارهای داده‌ای تغییرناپذیر (immutable) نیز نگاه خواهیم انداخت و بررسی خواهیم کرد که رابط‌ها (interfaces) و کلاس‌هایی که مجموعه‌های تغییرناپذیر را تعریف می‌کنند در فضای نام (namespace)
System.Collections.Immutable قرار دارند.

موضوعات کلی که در این فصل پوشش داده می‌شوند:
+ سازمان‌دهی کلاس‌ها

+ کامنت‌گذاری برای تولید مستندات

+ انسجام (Cohesion) و کوپلینگ (Coupling)

+ قانون دمیتر (Law of Demeter)

+ اشیاء و ساختارهای داده‌ای تغییرناپذیر

مهارت‌هایی که با پیشرفت در این فصل کسب خواهید کرد:
+ چگونگی سازمان‌دهی مؤثر کلاس‌ها با استفاده از فضای نام (namespace)

+ کلاس‌هایی خواهید نوشت که کوچکتر، معنادارتر و دارای تنها یک مسئولیت (Single Responsibility) باشند

+ در هنگام نوشتن APIهای خود، می‌توانید مستندات مناسبی برای توسعه‌دهندگان ارائه دهید، با استفاده از کامنت‌هایی که ابزارهای تولید مستندات را پشتیبانی می‌کنند

+ برنامه‌هایی که می‌نویسید، به دلیل انسجام بالا و کوپلینگ پایین، به راحتی قابل توسعه و تغییر خواهند بود

+ در نهایت، می‌توانید قانون دمیتر را اعمال کرده و ساختارهای داده‌ای تغییرناپذیر بنویسید و از آن‌ها استفاده کنید

شروع کنیم
بیایید با بررسی نحوه‌ی سازمان‌دهی مؤثر کلاس‌ها با استفاده از namespaceها شروع کنیم.

## پیش‌نیازهای فنی
می‌توانید کدهای مربوط به این فصل را در گیت‌هاب مشاهده کنید:
🔗 https://github.com/PacktPublishing/Clean-Code-in-C-/tree/master/CH03

## سازمان‌دهی کلاس‌ها
یکی از ویژگی‌های بارز پروژه‌ای پاک این است که کلاس‌ها در آن به‌خوبی سازمان‌دهی شده‌اند.

از پوشه‌ها برای گروه‌بندی کلاس‌هایی که به هم مرتبط هستند، استفاده می‌شود

کلاس‌های موجود در این پوشه‌ها درون namespaceهایی قرار دارند که با نام اسمبلی (assembly) و ساختار پوشه‌ها همخوانی دارند

هر interface، class، struct و enum باید در فایل سورس جداگانه‌ی خودش و در namespace مناسب قرار گیرد.
فایل‌های سورس باید به‌صورت منطقی و سازمان‌یافته در پوشه‌های مناسب گروه‌بندی شوند
و namespace هر فایل سورس باید با نام اسمبلی و ساختار پوشه‌ها تطابق داشته باشد.

📸 تصویر زیر ساختار تمیز فایل‌ها و پوشه‌ها را نمایش می‌دهد:
<div align="center">
  
  ![Conventions-UsedThis-Book](../../assets/image/03/Table%203-1.jpeg) 
  
</div>

داشتن بیش از یک رابط (interface)، کلاس (class)، ساختار (struct)، یا شمارش (enum) در یک فایل سورس واقعی ایده بدی است. دلیل این امر آن است که حتی با وجود اینتلی‌سنس (IntelliSense) که به ما کمک می‌کند، پیدا کردن آیتم‌ها را دشوار می‌سازد.

هنگام فکر کردن به فضاهای نام (namespaces) خود، ایده خوبی است که توالی قاعده پاسکال (Pascal casing) را برای نام شرکت (company name)، نام محصول (product name)، نام فناوری (technology name)، و سپس اسامی جمع (plural names) برای کامپوننت‌ها (components) که با نقطه از هم جدا شده‌اند، دنبال کنید. برای یک مثال، به مورد زیر نگاه کنید:

FakeCompany.Product.Wpf.Feature.Subnamespace {} // Product, technology and feature specific.
// محصول، فناوری و ویژگی خاص.

دلیل شروع با نام شرکت این است که به جلوگیری از تداخل کلاس‌های فضای نام کمک می‌کند. بنابراین، اگر مایکروسافت و FakeCompany هر دو فضای نامی به نام System داشته باشند، می‌توان با استفاده از نام شرکت، سیستم (System) مورد نظر شما را از یکدیگر متمایز کرد.

در ادامه، هر آیتم کدی که قابلیت استفاده مجدد در چندین پروژه را دارد، بهتر است در مجموعه‌های جداگانه (separate assemblies) قرار داده شود تا توسط چندین پروژه قابل دسترسی باشد:

FakeCompany.Wpf.Feature.Subnamespace {} /* Technology and feature specific.
Can be used across multiple products. */
/* فناوری و ویژگی خاص. می تواند در چندین محصول استفاده شود. */

هنگام استفاده از تست‌ها (tests) در کد خود، مانند زمانی که توسعه مبتنی بر تست (Test-Driven Development (TDD)) انجام می‌دهید، همیشه بهتر است کلاس‌های تست خود را در مجموعه‌های جداگانه نگه دارید. مجموعه‌های تست باید همیشه با نام مجموعه‌ای که در حال تست آن هستند و با اضافه شدن فضای نام Tests به انتهای نام مجموعه، نام‌گذاری شوند:

FakeCompany.Core.Feature {} /* Technology agnostic and feature specific.
Can be used across multiple products. */
/* مستقل از فناوری و ویژگی خاص. می تواند در چندین محصول استفاده شود. */

هرگز نباید تست‌های مربوط به مجموعه‌های مختلف را در یک مجموعه تست قرار دهید. همیشه آن‌ها را جدا از هم نگه دارید.

علاوه بر این، فضای نام و نوع نباید از یک نام استفاده کنند زیرا این می‌تواند منجر به تداخلات کامپایلر (compiler conflicts) شود. هنگام جمع بستن فضاهای نام، می‌توانید از جمع بستن برای نام شرکت، نام محصول و مخفف‌ها (acronyms) صرف نظر کنید.

به طور خلاصه، در اینجا قوانینی وجود دارد که هنگام سازماندهی کلاس‌ها باید به خاطر بسپارید:

+ توالی قاعده پاسکال (Pascal casing) را برای نام شرکت، نام محصول، نام فناوری، و اسامی جمع برای کامپوننت‌ها که با نقطه از هم جدا شده‌اند، دنبال کنید.

+ آیتم‌های کد قابل استفاده مجدد را در مجموعه‌های جداگانه قرار دهید.

+ از یک نام برای فضای نام و نوع استفاده نکنید.

+ نام شرکت، نام محصول و مخفف‌ها را جمع نبندید.

ما به مسئولیت کلاس‌ها (responsibility of classes) می‌پردازیم.

## یک کلاس باید فقط یک مسئولیت داشته باشد (A class should have only one responsibility)
مسئولیت (Responsibility) کاری است که به کلاس محول شده است. در مجموعه اصول SOLID، حرف S مخفف اصل مسئولیت واحد (Single Responsibility Principle (SRP)) است. هنگامی که SRP در مورد یک کلاس اعمال می‌شود، بیان می‌کند که کلاس باید فقط بر روی یک جنبه از ویژگی در حال پیاده‌سازی کار کند. مسئولیت آن جنبه واحد باید به طور کامل در داخل کلاس کپسوله‌سازی (encapsulated) شود. بنابراین، هرگز نباید بیش از یک مسئولیت را به یک کلاس اعمال کنید.

بیایید به مثالی نگاه کنیم تا دلیل آن را درک کنیم:

```C#

public class MultipleResponsibilities()
{
    public string DecryptString(string text,
     SecurityAlgorithm algorithm)
    {
        // ...implementation...
    }
    public string EncryptString(string text,
     SecurityAlgorithm algorithm)
    {
        // ...implementation...
    }
    public string ReadTextFromFile(string filename)
    {
        // ...implementation...
    }
    public string SaveTextToFile(string text, string filename)
    {
        // ...implementation...
    }
}
```
همانطور که در کد بالا مشاهده می‌کنید، برای کلاس MultipleResponsibilities، ما قابلیت‌های رمزنگاری (cryptography) خود را با متدهای DecryptString و EncryptString پیاده‌سازی کرده‌ایم. ما همچنین دسترسی به فایل (file access) را با متدهای ReadTextFromFile و SaveTextToFile پیاده‌سازی کرده‌ایم. این کلاس اصل SRP را نقض می‌کند.

بنابراین ما باید این کلاس را به دو کلاس تقسیم کنیم، یکی برای رمزنگاری و دیگری برای دسترسی به فایل:

```C#

namespace FakeCompany.Core.Security
{
    public class Cryptography
    {
        public string DecryptString(string text,
         SecurityAlgorithm algorithm)
        {
            // ...implementation...
        }
        public string EncryptString(string text,
         SecurityAlgorithm algorithm)
        {
            // ...implementation...
        }
    }
}
```
همانطور که اکنون از کد بالا می‌بینیم، با انتقال متدهای EncryptString و DecryptString به کلاس Cryptography در فضای نام امنیت هسته (core security namespace)، استفاده مجدد از کد برای رمزگذاری و رمزگشایی رشته‌ها در محصولات و گروه‌های فناوری مختلف را آسان کرده‌ایم. کلاس Cryptography همچنین با SRP مطابقت دارد.

در کد زیر، می‌بینیم که پارامتر SecurityAlgorithm از کلاس Cryptography یک شمارش (enum) است و در فایل سورس خود قرار داده شده است. این به تمیز، مینیمال و سازماندهی خوب کد کمک می‌کند:

```C#

using System;
namespace FakeCompany.Core.Security
{
    [Flags]
    public enum SecurityAlgorithm
    {
        Aes,
        AesCng,
        MD5,
        SHA5
    }
}
```

اکنون، در کلاس TextFile زیر، دوباره از SRP پیروی می‌کنیم و یک کلاس قابل استفاده مجدد خوب داریم که در فضای نام سیستم فایل هسته (appropriate core filesystem namespace) قرار دارد. کلاس TextFile در محصولات و گروه‌های فناوری مختلف قابل استفاده مجدد است:

```C#

namespace FakeCompany.Core.FileSystem
{
    public class TextFile
    {
        public string ReadTextFromFile(string filename)
        {
            // ...implementation...
        }
        public string SaveTextToFile(string text, string filename)
        {
            // ...implementation...
        }
    }
}
```
ما به سازماندهی (organization) و مسئولیت (responsibility) کلاس‌ها پرداختیم. اکنون بیایید به نظردهی (commenting) در مورد کلاس‌ها برای بهره‌مندی سایر توسعه‌دهندگان نگاهی بیندازیم.
