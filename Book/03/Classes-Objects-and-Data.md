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

## نظردهی برای تولید مستندات (Commenting for documentation generation)
مستندسازی کد منبع (documenting your source code) شما همیشه ایده خوبی است، چه یک پروژه داخلی باشد و چه یک نرم‌افزار خارجی که توسط توسعه‌دهندگان دیگر استفاده خواهد شد. پروژه‌های داخلی به دلیل جابجایی توسعه‌دهنده (developer turnover) و اغلب مستندات ضعیف یا عدم وجود آن برای کمک به توسعه‌دهندگان جدید برای آشنا شدن با کار (get up to speed)، آسیب می‌بینند. بسیاری از APIهای شخص ثالث (third-party APIs) موفق به شروع به کار نمی‌شوند یا پذیرش آن‌ها کندتر از حد انتظار است، اغلب به دلیل اینکه استفاده‌کنندگان از APIها به دلیل وضعیت ضعیف مستندات توسعه‌دهنده (developer documentation) از روی ناامیدی آن‌ها را رها می‌کنند.

همیشه ایده خوبی است که اعلامیه‌های حق تکثیر (copyright notices) را در بالای هر فایل کد منبع قرار دهید و در مورد فضاهای نام (namespaces)، رابط‌ها (interfaces)، کلاس‌ها (classes)، شمارش‌ها (enums)، ساختارها (structs)، متدها (methods) و ویژگی‌های (properties) خود نظر بنویسید. نظرات حق تکثیر شما باید اولین مورد در فایل سورس باشد، بالای عبارات using و به شکل یک نظر چند خطی (multiline comment) باشد که با /* شروع و با */ به پایان می‌رسد:

/**************************************************************************
 ********
 * Copyright 2019 PacktPub
 *
 * Permission is hereby granted, free of charge, to any person obtaining a
 copy of
 * this software and associated documentation files (the "Software"), to
 [ 67 ]
Classes, Objects, and Data Structures
 Chapter 3
 deal in
 * the Software without restriction, including without limitation the
 rights to use,
 * copy, modify, merge, publish, distribute, sublicense, and/or sell copies
 of the
 * Software, and to permit persons to whom the Software is furnished to do
 so,
 * subject to the following conditions:
 *
 * The above copyright notice and this permission notice shall be included
 in all
 * copies or substantial portions of the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS
 OR
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL
 THE
 * AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 * LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
 FROM,
 * OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS
 IN THE
 * SOFTWARE.
 ***************************************************************************
 ******/
```C#

using System;
/// <summary>
/// The CH3.Core.Security namespace contains fundamental types used
/// for the purpose of implementing application security.
/// </summary>


 namespace CH3.Core.Security
 {
    /// <summary>
    /// Encrypts and decrypts provided strings based on the selected
    /// algorithm.
    /// </summary>


    public class Cryptography
    {
        /// <summary>
        /// Decrypts a string using the selected algorithm.
        /// </summary>

        /// <param name="text">The string to be decrypted.</param>
        /// <param name="algorithm">The cryptographic algorithm used to decrypt the string.</param>
        /// <returns>Decrypted string</returns>
 [ 68 ]
Classes, Objects, and Data Structures
 Chapter 3
        public string DecryptString(string text,
         SecurityAlgorithm algorithm)
        {
            // ...implementation...
            throw new NotImplementedException();
        }
        /// <summary>
        /// Encrypts a string using the selected algorithm.
        /// </summary>

        /// <param name="text">The string to encrypt.</param>
        /// <param name="algorithm">The cryptographic algorithm used to encrypt the string.</param>
        /// <returns>Encrypted string</returns>
        public string EncryptString(string text,
         SecurityAlgorithm algorithm)
        {
            // ...implementation...
            throw new NotImplementedException();
        }
    }
 }
```
نمونه کد بالا مثالی از یک فضای نام و کلاس مستند شده به همراه متدهای مستند شده را ارائه می‌دهد. خواهید دید که نظرات مستندات (documentation comments) برای فضای نام و اعضای موجود در آن با سه اسلش (///) شروع می‌شوند و مستقیماً بالای آیتم مورد نظر قرار می‌گیرند. هنگامی که شما سه اسلش را تایپ می‌کنید، ویژوال استودیو به صورت خودکار تگ‌های XML را بر اساس خط پایین ایجاد می‌کند.

به عنوان مثال، در کد بالا، فضای نام و کلاس فقط یک تگ <summary> دارند، اما هر دو متد شامل یک summary، چند تگ param برای پارامترها و یک تگ returns برای مقدار بازگشتی هستند.

جدول زیر تگ‌های مختلف XML را که می‌توانید در نظرات مستندات خود استفاده کنید، شامل می‌شود. (توجه: جدول در متن اصلی ارائه نشده است.)

<div align="center">
  
  ![Conventions-UsedThis-Book](../../assets/image/03/Table%203-2.jpeg) 
  
</div>

از جدول قبلی ، مشخص است که شما فضای زیادی برای مستندسازی کد منبع خود دارید. بنابراین ایده خوبی است که از تگ‌های موجود برای مستندسازی کد خود به بهترین نحو استفاده کنید. هرچه مستندات بهتر باشد، برای سایر توسعه‌دهندگان سریع‌تر و آسان‌تر خواهد بود که با استفاده از کد آشنا شوند.

اکنون زمان آن است که به همبستگی (cohesion) و جفت‌شدگی (coupling) نگاهی بیندازیم.

## همبستگی و جفت‌شدگی (Cohesion and coupling)
در یک مجموعه (assembly) C# که به خوبی طراحی شده است، کد به درستی با هم گروه‌بندی می‌شود. به این همبستگی بالا (high cohesion) می‌گویند. همبستگی پایین (low cohesion) زمانی است که کدی را دارید که با هم گروه‌بندی شده‌اند اما به یکدیگر تعلق ندارند.

شما می‌خواهید کلاس‌های مرتبط تا حد امکان مستقل (independent) از هم باشند. هرچه یک کلاس به کلاس دیگری وابسته باشد، جفت‌شدگی (coupling) بالاتر است. به این جفت‌شدگی محکم (tight coupling) می‌گویند. هرچه کلاس‌ها از یکدیگر مستقل‌تر باشند، جفت‌شدگی پایین‌تر است. به این جفت‌شدگی سست (low coupling) می‌گویند.

بنابراین، در یک کلاس با تعریف خوب، شما به همبستگی بالا (high cohesion) و جفت‌شدگی پایین (low coupling) نیاز دارید. اکنون به مثال‌هایی از جفت‌شدگی محکم و سپس جفت‌شدگی سست نگاهی می‌اندازیم.


## یک مثال از جفت‌شدگی محکم (An example of tight coupling)
در نمونه کد زیر، کلاس TightCouplingA کپسوله‌سازی (encapsulation) را می‌شکند و متغیر _name را به صورت مستقیم قابل دسترسی می‌کند. متغیر _name باید private باشد و فقط توسط ویژگی‌ها یا متدهای داخل کلاس محصور کننده خود تغییر یابد. ویژگی Name متدهای get و set را برای اعتبارسنجی متغیر _name فراهم می‌کند، اما این کاملاً بی‌معنی است زیرا می‌توان از این بررسی‌ها صرف‌نظر کرد و ویژگی‌ها را فراخوانی نکرد:

```C#

using System.Diagnostics;
namespace CH3.Coupling
{
    public class TightCouplingA
    {
        public string _name;
        public string Name
        {
            get
            {
            }
            set
            {
                if (!_name.Equals(string.Empty))
                    return _name;
                else
                    return "String is empty!";
                if (value.Equals(string.Empty))
                    Debug.WriteLine("String is empty!");
            }
        }
    }
}
```
از طرف دیگر، در کد زیر، کلاس TightCouplingB یک نمونه از TightCouplingA ایجاد می‌کند. سپس با دسترسی مستقیم به متغیر عضو _name و تنظیم آن روی null، و سپس دسترسی مستقیم برای چاپ مقدار آن در پنجره خروجی دیباگ، جفت‌شدگی محکمی (tight coupling) بین دو کلاس ایجاد می‌کند:

```C#

using System.Diagnostics;
namespace CH3.Coupling
{
    public class TightCouplingB
    {
        public TightCouplingB()
        {
            TightCouplingA tca = new TightCouplingA();
            tca._name = null;
            Debug.WriteLine("Name is " + tca._name);
        }
    }
}
```


            
اکنون بیایید به همان مثال ساده با استفاده از جفت‌شدگی سست (low coupling) نگاه کنیم.

## یک مثال از جفت‌شدگی سست (An example of low coupling)
در این مثال، ما دو کلاس داریم، LooseCouplingA و LooseCouplingB.
LooseCouplingA یک متغیر نمونه private به نام _name اعلام می‌کند، و این متغیر از طریق یک ویژگی public تنظیم می‌شود.
LooseCouplingB یک نمونه از LooseCouplingA ایجاد می‌کند و مقدار Name را می‌گیرد و تنظیم می‌کند (gets and sets). از آنجا که عضو داده _name نمی‌تواند مستقیماً تنظیم شود، بررسی‌های مربوط به تنظیم و گرفتن مقدار آن عضو داده انجام می‌شود.
و بنابراین ما مثالی از جفت‌شدگی سست داریم. بیایید نگاهی به دو کلاس LooseCouplingA و LooseCouplingB بیندازیم که این را در عمل نشان می‌دهند:

```C#

using System.Diagnostics;
namespace CH3.Coupling
{
    public class LooseCouplingA
    {
        private string _name;
        private readonly string _stringIsEmpty = "String is empty";
        public string Name
        {
            get
            {
                if (_name.Equals(string.Empty))
                    return _stringIsEmpty;
                else
                    return _name;
            }
            set
            {
                if (value.Equals(string.Empty))
                    Debug.WriteLine("Exception: String length must be
                     greater than zero.");
            }
        }
    }
}
```
در کلاس LooseCouplingA، فیلد _name را به عنوان private اعلام می‌کنیم و از تغییر مستقیم داده‌ها جلوگیری می‌کنیم. داده‌های _name توسط ویژگی Name به صورت غیرمستقیم قابل دسترسی می‌شوند:

```C#

using System.Diagnostics;
namespace CH3.Coupling
{
    public class LooseCouplingB
    {
        public LooseCouplingB()
        {
            LooseCouplingA lca = new LooseCouplingA();
            lca = null;
            Debug.WriteLine($"Name is {lca.Name}");
        }
    }
}
```
کلاس LooseCouplingB قادر به دسترسی مستقیم به متغیر _name کلاس LooseCouplingA نیست و بنابراین عضو داده را از طریق یک ویژگی تغییر می‌دهد.

خُب، ما به جفت‌شدگی نگاه کردیم و اکنون می‌دانیم که چگونه از کد با جفت‌شدگی محکم اجتناب کرده و کد با جفت‌شدگی سست را پیاده‌سازی کنیم. بنابراین اکنون، زمان آن است که به برخی از مثال‌های همبستگی پایین (low cohesion) و همبستگی بالا (high cohesion) نگاه کنیم.
