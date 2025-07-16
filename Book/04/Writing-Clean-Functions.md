# *۴*
## نوشتن توابع تمیز
توابع تمیز (Clean functions) متدهایی هستند که کوچک بوده (دارای دو یا کمتر آرگومان هستند) و از تکرار (duplication) اجتناب می‌کنند. متد ایده‌آل هیچ پارامتری ندارد و وضعیت برنامه (program's state) را تغییر نمی‌دهد. متدهای کوچک کمتر مستعد استثناها (exceptions) هستند، بنابراین شما کدی بسیار مستحکم‌تر (robust) خواهید نوشت که در بلندمدت به نفع شما خواهد بود، زیرا باگ‌های کمتری برای رفع کردن خواهید داشت.

برنامه‌نویسی تابعی (Functional programming) یک متدولوژی کدنویسی نرم‌افزار است که محاسبات را به عنوان ارزیابی ریاضیاتی محاسبات (mathematical evaluation of computations) در نظر می‌گیرد. این فصل مزایای در نظر گرفتن محاسبات به عنوان ارزیابی توابع ریاضیاتی را به شما آموزش می‌دهد تا از تغییر وضعیت یک شیء (object's state) جلوگیری کنید.

متدهای بزرگ (که با نام توابع نیز شناخته می‌شوند) می‌توانند برای خواندن دست و پا گیر و مستعد خطا باشند، بنابراین نوشتن متدهای کوچک مزایای خود را دارد. از این رو، به بررسی چگونگی تقسیم متدهای بزرگ به متدهای کوچک‌تر خواهیم پرداخت. در این فصل، برنامه‌نویسی تابعی (functional programming) در C# و نحوه نوشتن متدهای کوچک و تمیز را پوشش خواهیم داد.

سازنده‌ها (Constructors) و متدهایی با پارامترهای متعدد می‌توانند کار کردن با آن‌ها را بسیار دشوار سازند، بنابراین باید به دنبال راه‌هایی برای مدیریت و ارسال پارامترهای متعدد باشیم، و همچنین نحوه اجتناب از استفاده بیش از دو پارامتر را بررسی کنیم. دلیل اصلی برای کاهش تعداد پارامترها این است که آن‌ها می‌توانند خوانایی کد را سخت کنند، منبع آزار برای برنامه‌نویسان دیگر باشند، و در صورت زیاد بودن باعث استرس بصری (visual stress) شوند. آن‌ها همچنین می‌توانند نشانه‌ای باشند که متد در تلاش برای انجام کارهای بیش از حد است، یا اینکه باید بازسازی کد (refactoring) خود را در نظر بگیرید.

در این فصل، موضوعات زیر را پوشش خواهیم داد:

درک برنامه‌نویسی تابعی (functional programming)

کوچک نگه داشتن متدها (Keeping methods small)

اجتناب از تکرار (duplication)

اجتناب از پارامترهای متعدد (multiple parameters)


در پایان این فصل، شما مهارت‌های لازم برای انجام موارد زیر را خواهید داشت:

توصیف اینکه برنامه‌نویسی تابعی (functional programming) چیست

ارائه مثال‌های موجود از برنامه‌نویسی تابعی (functional programming) در زبان برنامه‌نویسی C#

نوشتن کد تابعی C#

اجتناب از نوشتن متدهایی با بیش از دو آرگومان

نوشتن اشیاء و ساختارهای داده تغییرناپذیر (immutable)

کوچک نگه داشتن متدهای خود

نوشتن کدی که به اصل تک‌مسئولیتی (Single Responsibility Principle - SRP) پایبند باشد

بیایید شروع کنیم!

## درک برنامه‌نویسی تابعی (Understanding functional programming)
تنها چیزی که برنامه‌نویسی تابعی (functional programming) را از سایر روش‌های برنامه‌نویسی متمایز می‌کند این است که توابع داده یا وضعیت (state) را تغییر نمی‌دهند. شما از برنامه‌نویسی تابعی (functional programming) در سناریوهایی مانند یادگیری عمیق (deep learning)، یادگیری ماشین (machine learning)، و هوش مصنوعی (artificial intelligence) استفاده خواهید کرد، زمانی که لازم است مجموعه‌های مختلفی از عملیات را روی یک مجموعه داده یکسان انجام دهید.

سینتکس LINQ در .NET Framework نمونه‌ای از برنامه‌نویسی تابعی (functional programming) است. بنابراین، اگر کنجکاو هستید که برنامه‌نویسی تابعی (functional programming) چگونه به نظر می‌رسد، و اگر قبلاً از LINQ استفاده کرده‌اید، پس با برنامه‌نویسی تابعی (functional programming) مواجه شده‌اید و باید بدانید که چگونه است.

از آنجایی که برنامه‌نویسی تابعی (functional programming) یک موضوع عمیق است و کتاب‌ها، دوره‌ها و ویدئوهای زیادی در این زمینه وجود دارد، ما در این فصل تنها به طور مختصر با بررسی توابع خالص (pure functions) و داده‌های تغییرناپذیر (immutable data) به این موضوع خواهیم پرداخت.

یک تابع خالص (pure function) تنها به عملیات روی داده‌هایی که به آن پاس داده می‌شوند، محدود است. در نتیجه، متد قابل پیش‌بینی (predictable) است و از تولید اثرات جانبی (side effects) جلوگیری می‌کند. این به برنامه‌نویسان کمک می‌کند زیرا استدلال و تست چنین متدهایی آسان‌تر است.


پس از اینکه یک شیء داده تغییرناپذیر (immutable data object) یا ساختار داده (data structure) مقداردهی اولیه شد، مقادیر داده موجود در آن اصلاح نخواهند شد. از آنجایی که داده فقط تنظیم می‌شود و تغییر نمی‌کند، می‌توانید به راحتی در مورد اینکه داده چیست، چگونه تنظیم می‌شود و با ورودی‌های داده شده، نتیجه هر عملیاتی چه خواهد بود، استدلال کنید. داده‌های تغییرناپذیر نیز برای تست آسان‌تر هستند، زیرا می‌دانید ورودی‌های شما چه هستند و چه خروجی‌هایی انتظار می‌رود. این کار نوشتن سناریوهای تست (test cases) را بسیار آسان‌تر می‌کند، زیرا نیازی به در نظر گرفتن موارد زیادی مانند وضعیت شیء (object state) ندارید. مزیت اشیاء و ساختارهای تغییرناپذیر این است که آن‌ها Thread-safe هستند. اشیاء و ساختارهای Thread-safe برای اشیاء انتقال داده (data transfer objects - DTOs) که می‌توانند بین Threadها پاس داده شوند، مناسب هستند.

اما ساختارها (structs) همچنان می‌توانند تغییرپذیر (mutable) باشند اگر حاوی انواع مرجع (reference types) باشند. یک راه حل برای این مشکل، تغییرناپذیر ساختن نوع مرجع خواهد بود. C# 7.2 پشتیبانی از readonly struct و ImmutableStruct را اضافه کرد. بنابراین، حتی اگر ساختارهای ما حاوی انواع مرجع باشند، اکنون می‌توانیم از این ساختارهای جدید C# 7.2 برای ساختن ساختارهایی با انواع مرجع تغییرناپذیر استفاده کنیم.

حالا، بیایید به یک مثال از تابع خالص (pure function) نگاهی بیندازیم. تنها راه برای تنظیم خصوصیات یک شیء، از طریق سازنده (constructor) در زمان ساخت است. این کلاس، یک کلاس Player است که تنها وظیفه‌اش نگهداری نام بازیکن و بالاترین امتیاز اوست. یک متد ارائه شده است که بالاترین امتیاز بازیکن را به‌روزرسانی می‌کند:

```csharp
public class Player
{
    public string PlayerName { get; }
    public long HighScore { get; }

    public Player(string playerName, long highScore)
    {
        PlayerName = playerName;
        HighScore = highScore;
    }

    public Player UpdateHighScore(long highScore)
    {
        return new Player(PlayerName, highScore);
    }
}
```

توجه داشته باشید که متد UpdateHighScore خصوصیت HighScore را به‌روزرسانی نمی‌کند. در عوض، با پاس دادن متغیر PlayerName که از قبل در کلاس تنظیم شده است، و highScore که پارامتر متد است، یک کلاس Player جدید را نمونه‌سازی کرده و برمی‌گرداند. اکنون یک مثال بسیار ساده از نحوه برنامه‌نویسی نرم‌افزار خود بدون تغییر وضعیت آن را مشاهده کرده‌اید.



برنامه‌نویسی تابعی (Functional programming) موضوع بسیار گسترده‌ای است و نیاز به تغییر طرز فکر دارد که می‌تواند برای برنامه‌نویسان رویه محور (procedural) و شیءگرا (object-oriented) بسیار دشوار باشد. از آنجایی که پرداختن عمیق به موضوع برنامه‌نویسی تابعی (functional programming) خارج از محدوده این کتاب است، شما به طور فعال تشویق می‌شوید که منابع برنامه‌نویسی تابعی (functional programming) ارائه شده توسط PacktPub را برای خودتان بررسی کنید.

Packt برخی کتاب‌ها و ویدئوهای بسیار خوبی دارد که در آموزش سطوح بالای برنامه‌نویسی تابعی (functional programming) تخصص دارند. لینک‌های برخی از منابع برنامه‌نویسی تابعی (functional programming) Packt را در انتهای این فصل، در بخش مطالعه بیشتر (Further reading) خواهید یافت.

قبل از اینکه ادامه دهیم، به چند مثال LINQ نگاهی خواهیم انداخت، زیرا LINQ نمونه‌ای از برنامه‌نویسی تابعی (functional programming) در C# است. داشتن یک مجموعه داده مثالی خوب خواهد بود. کد زیر لیستی از فروشندگان و محصولات را می‌سازد. با نوشتن ساختار Product شروع می‌کنیم:

```csharp
public struct Product
{
    public string Vendor { get; }
    public string ProductName { get; }

    public Product(string vendor, string productName)
    {
        Vendor = vendor;
        ProductName = productName;
    }
}
```

اکنون که ساختار خود را داریم، مقداری داده نمونه را درون متد GetProducts() اضافه خواهیم کرد:

```csharp
public static List<Product> GetProducts()
{
    return new List<Products>
    {
        new Product("Microsoft", "Microsoft Office"),
        new Product("Oracle", "Oracle Database"),
        new Product("IBM", "IBM DB2 Express"),
        new Product("IBM", "IBM DB2 Express"),
        new Product("Microsoft", "SQL Server 2017 Express"),
        new Product("Microsoft", "Visual Studio 2019 Community Edition"),
        new Product("Oracle", "Oracle JDeveloper"),
        new Product("Microsoft", "Azure"),
        new Product("Microsoft", "Azure"),
        new Product("Microsoft", "Azure Stack"),
        new Product("Google", "Google Cloud Platform"),
        new Product("Amazon", "Amazon Web Services")
    };
}
```



در نهایت، می‌توانیم شروع به استفاده از LINQ روی لیست خود کنیم. در مثال قبلی، یک لیست متمایز از محصولات را به دست می‌آوریم که بر اساس نام فروشنده مرتب شده‌اند، و نتایج را چاپ می‌کنیم:

```csharp
class Program
{
    static void Main(string[] args)
    {
        var vendors = (from p in GetProducts()
                       select p.Vendor)
                      .Distinct()
                      .OrderBy(x => x);

        foreach(var vendor in vendors)
            Console.WriteLine(vendor);

        Console.ReadKey();
    }
}
```

در اینجا، ما با فراخوانی GetProducts() و انتخاب تنها ستون Vendor، لیستی از فروشندگان را به دست می‌آوریم. سپس، با فراخوانی متد Distinct()، لیست را فیلتر می‌کنیم تا هر فروشنده فقط یک بار در آن گنجانده شود. لیست فروشندگان سپس با فراخوانی OrderBy(x => x)، که در آن x نام فروشنده است، به ترتیب حروف الفبا مرتب می‌شود. پس از به دست آوردن لیست مرتب شده از فروشندگان متمایز، سپس در لیست حلقه می‌زنیم و نام فروشنده را چاپ می‌کنیم. در نهایت، منتظر می‌مانیم تا کاربر برای خروج از برنامه، هر کلیدی را فشار دهد.

یکی از مزایای برنامه‌نویسی تابعی (functional programming) این است که متدهای شما بسیار کوچک‌تر از متدهای موجود در سایر انواع برنامه‌نویسی هستند. در ادامه، به این موضوع خواهیم پرداخت که چرا کوچک نگه داشتن متدها خوب است، و همچنین تکنیک‌هایی که می‌توانیم از آن‌ها استفاده کنیم، از جمله برنامه‌نویسی تابعی (functional programming).

## کوچک نگه داشتن متدها (Keeping methods small)
هنگام برنامه‌نویسی کد تمیز و خوانا، مهم است که متدها را کوچک نگه داریم. ترجیحاً، در دنیای C#، بهتر است متدها را زیر ۱۰ خط نگه داریم. طول ایده‌آل بیش از ۴ خط نیست. یک راه خوب برای کوچک نگه داشتن متدها این است که در نظر بگیرید آیا باید خطاها را گرفتار کنید (trapping for errors) یا آن‌ها را بیشتر در پشته فراخوانی (call stack) بالا ببرید (bubbling them further up). با برنامه‌نویسی دفاعی (defensive programming)، ممکن است کمی بیش از حد دفاعی شوید، و این می‌تواند به میزان کدی که می‌نویسید، اضافه کند. علاوه بر این، متدهایی که خطاها را می‌گیرند، طولانی‌تر از متدهایی خواهند بود که این کار را نمی‌کنند.


بیایید کد زیر را در نظر بگیریم که می‌تواند یک ArgumentNullException را پرتاب کند:

```csharp
public UpdateView(MyEntities context, DataItem dataItem)
{
    InitializeComponent();
    try
    {
        DataContext = this;
        _dataItem = dataItem;
        _context = context;
        nameTextBox.Text = _dataItem.Name;
        DescriptionTextBox.Text = _dataItem.Description;
    }
    catch (Exception ex)
    {
        Debug.WriteLine(ex);
        throw;
    }
}
```

در کد بالا، به وضوح می‌توان دید که دو مکان وجود دارد که ممکن است یک ArgumentNullException در آن‌ها ایجاد شود. اولین خط کدی که به طور بالقوه می‌تواند یک ArgumentNullException را ایجاد کند، nameTextBox.Text = _dataItem.Name; است؛ دومین خط کدی که ممکن است به طور بالقوه همان استثنا را ایجاد کند، DescriptionTextBox.Text = _dataItem.Description; است. می‌توانیم ببینیم که هندلر استثنا (exception handler)، استثنا را هنگام وقوع می‌گیرد، آن را در کنسول می‌نویسد، و سپس به سادگی آن را دوباره به پشته (stack) پرتاب می‌کند.

توجه داشته باشید که از دیدگاه خوانایی انسانی، ۸ خط کد بلوک try/catch را تشکیل می‌دهند.

شما می‌توانید مدیریت استثنای try/catch را به طور کامل با یک خط متن با نوشتن اعتبارسنج آرگومان (argument validator) خودتان جایگزین کنید. برای توضیح این موضوع، یک مثال ارائه خواهیم داد.

بیایید با نگاهی به کلاس ArgumentValidator شروع کنیم. هدف این کلاس، پرتاب یک ArgumentNullException با نام متدی است که حاوی آرگومان null است:

```csharp
using System;

namespace CH04.Validators
{
    internal static class ArgumentValidator
    {
        public static void NotNull(
            string name,
            [ValidatedNotNull] object value
        )
        {
            if (value == null)
                throw new ArgumentNullException(name);
        }
    }

    [AttributeUsage(
        AttributeTargets.All,
        Inherited = false,
        AllowMultiple = true)
    ]
    internal sealed class ValidatedNotNullAttribute : Attribute
    {
    }
}
```

اکنون که کلاس اعتبارسنجی null (null validation) خود را داریم، می‌توانیم روش جدید اعتبارسنجی پارامترها برای مقادیر null را در متدهای خود انجام دهیم. پس، بیایید به یک مثال ساده نگاه کنیم:

```csharp
public ItemsUpdateView(
    Entities context,
    ItemsView itemView
)
{
    InitializeComponent();
    ArgumentValidator.NotNull("ItemsUpdateView", itemView);
    // ### implementation omitted ###
}
```

همانطور که به وضوح می‌توانید ببینید، ما تمام بلوک try catch را با یک خط کد در بالای متد جایگزین کرده‌ایم. هنگامی که این اعتبارسنجی یک آرگومان null را تشخیص دهد، یک ArgumentNullException پرتاب می‌شود و از ادامه اجرای کد جلوگیری می‌کند. این کار خوانایی کد را بسیار آسان‌تر می‌کند و همچنین به اشکال‌زدایی (debugging) کمک می‌کند.

حالا، به فرمت‌بندی توابع با تورفتگی (indentation) نگاهی خواهیم انداخت تا خواندن آن‌ها آسان باشد.

## تورفتگی کد (Indenting code)
یک متد بسیار طولانی در بهترین حالت هم خواندن و دنبال کردنش دشوار است، به خصوص وقتی مجبور باشید بارها در متد اسکرول کنید تا به انتهای آن برسید. اما انجام این کار با متدهایی که به درستی با سطوح صحیح تورفتگی (indentation) فرمت‌بندی نشده‌اند، می‌تواند یک کابوس واقعی باشد.


اگر تا به حال با کد متدی مواجه شدید که به درستی فرمت‌بندی نشده است، به عنوان یک برنامه‌نویس حرفه‌ای، مسئولیت خودتان بدانید که قبل از انجام هر کار دیگری، کد را مرتب کنید. هر کدی که بین آکولادها ({}) قرار می‌گیرد، به عنوان یک بلوک کد (code block) شناخته می‌شود. کد درون یک بلوک کد (code block) باید به اندازه یک سطح تورفتگی (indented) داشته باشد. بلوک‌های کد (code blocks) درون بلوک‌های کد (code blocks) نیز باید به اندازه یک سطح تورفتگی (indented) داشته باشند، همانطور که در مثال زیر نشان داده شده است:

```csharp
public Student Find(List<Student> list, int id)
{
    Student r = null;
    foreach (var i in list)
    {
        if (i.Id == id)
            r = i;
    }
    return r;
}
```

مثال بالا تورفتگی بد (bad indentation) و همچنین برنامه‌نویسی حلقه بد (bad loop programming) را نشان می‌دهد. در اینجا، می‌توانید ببینید که لیستی از دانشجویان در حال جستجو است تا دانشجویی با ID مشخص شده که به عنوان پارامتر پاس داده شده، پیدا و برگردانده شود. آنچه برخی برنامه‌نویسان را آزار می‌دهد و عملکرد برنامه را کاهش می‌دهد این است که حلقه در کد بالا، حتی پس از پیدا شدن دانشجو، ادامه می‌یابد. ما می‌توانیم تورفتگی (indentation) و عملکرد کد بالا را به صورت زیر بهبود ببخشیم:

```csharp
public Student Find(List<Student> list, int id)
{
    Student r = null;
    foreach (var i in list)
    {
        if (i.Id == id)
        {
            r = i;
            break;
        }
    }
    return r;
}
```

در کد بالا، ما فرمت‌بندی را بهبود بخشیده‌ایم و اطمینان حاصل کرده‌ایم که کد به درستی تورفتگی (indented) دارد. ما یک break به حلقه for اضافه کرده‌ایم تا حلقه foreach هنگام پیدا شدن تطابق، پایان یابد.

اکنون کد نه تنها خواناتر است، بلکه عملکرد بسیار بهتری نیز دارد. تصور کنید کد در حال اجرا بر روی یک دانشگاه با ۷۳,۰۰۰ دانشجو در پردیس و از طریق آموزش از راه دور است. در نظر بگیرید که اگر دانشجویی که با ID مطابقت دارد، اولین نفر در لیست باشد، بدون دستور break، کد باید ۷۲,۹۹۹ محاسبه غیرضروری را اجرا کند. می‌توانید ببینید که دستور break چه تفاوتی در عملکرد کد بالا ایجاد می‌کند.


ما مقدار بازگشتی را در مکان اصلی خود نگه داشته‌ایم، زیرا کامپایلر ممکن است شکایت کند که همه مسیرهای کد یک مقدار را برنمی‌گردانند. این همچنین دلیلی است که ما دستور break را اضافه کردیم. واضح است که تورفتگی مناسب (proper indentation) خوانایی کد را بهبود می‌بخشد، بنابراین به درک برنامه‌نویس از آن کمک می‌کند. این به برنامه‌نویس امکان می‌دهد هر تغییری را که لازم می‌داند، اعمال کند.

# اجتناب از تکرار (Avoiding duplication)
کد می‌تواند یا DRY باشد یا WET. کد WET مخفف Write Every Time (هر بار بنویس) است و نقطه مقابل DRY است که مخفف Don't Repeat Yourself (خودت را تکرار نکن) می‌باشد. مشکل کد WET این است که کاندیدای عالی برای باگ‌ها (bugs) است. فرض کنید تیم تست یا یک مشتری باگی را پیدا کرده و به شما گزارش می‌دهد. شما باگ را رفع می‌کنید و آن را ارسال می‌کنید، اما این باگ هر بار که آن کد در برنامه کامپیوتری شما با آن مواجه شود، دوباره به سراغتان می‌آید.

اکنون، ما کد WET خود را با حذف تکرار (duplication)، DRY می‌کنیم. یک راه برای انجام این کار، استخراج کد (extracting the code) و قرار دادن آن در یک متد و سپس متمرکز کردن متد (centralizing the method) به گونه‌ای است که برای تمام بخش‌های برنامه کامپیوتری که به آن نیاز دارند، قابل دسترسی باشد.

وقت یک مثال است. تصور کنید مجموعه‌ای از آیتم‌های هزینه دارید که شامل خصوصیات Name و Amount هستند. حالا، در نظر بگیرید که باید مقدار decimal Amount را برای یک آیتم هزینه بر اساس Name به دست آورید.

فرض کنید مجبور بودید این کار را ۱۰۰ بار انجام دهید. برای این کار، می‌توانید کد زیر را بنویسید:

```csharp
var amount = ViewModel
    .ExpenseLines
    .Where(e => e.Name.Equals("Life Insurance"))
    .FirstOrDefault()
    .Amount;
```

هیچ دلیلی وجود ندارد که نتوانید همان کد را ۱۰۰ بار بنویسید. اما راهی وجود دارد که آن را فقط یک بار بنویسید، بنابراین اندازه پایگاه کد (codebase) خود را کاهش داده و بهره‌وری بیشتری داشته باشید. بیایید نگاهی بیندازیم که چگونه می‌توانیم این کار را انجام دهیم:

```csharp
public decimal GetValueByName(string name)
{
    return ViewModel
        .ExpenseLines
        .Where(e => e.Name.Equals(name))
        .FirstOrDefault()
        .Amount;
}
```


هیچ دلیلی وجود ندارد که نتوانید همان کد را ۱۰۰ بار بنویسید. اما راهی وجود دارد که آن را فقط یک بار بنویسید، بنابراین اندازه پایگاه کد (codebase) خود را کاهش داده و بهره‌وری بیشتری داشته باشید. بیایید نگاهی بیندازیم که چگونه می‌توانیم این کار را انجام دهیم:

public decimal GetValueByName(string name)
{
    return ViewModel
        .ExpenseLines
        .Where(e => e.Name.Equals(name))
        .FirstOrDefault()
        .Amount;
}

برای استخراج مقدار مورد نیاز از مجموعه ExpenseLines در ViewModel خود، تنها کاری که باید انجام دهید این است که نام مقداری را که نیاز دارید به متد GetValueName(string name) پاس دهید، همانطور که در کد زیر نشان داده شده است:

```csharp
var amount = GetValueByName("Life Insurance");
```

آن یک خط کد بسیار خوانا است، و خطوط کد برای به دست آوردن مقدار در یک متد واحد قرار دارند. بنابراین، اگر متد به هر دلیلی (مانند رفع باگ) نیاز به تغییر داشته باشد، شما تنها باید کد را در یک مکان اصلاح کنید.

گام منطقی بعدی برای نوشتن توابع خوب، داشتن کمترین تعداد پارامتر ممکن است. در بخش بعدی، به این موضوع خواهیم پرداخت که چرا نباید بیش از دو پارامتر داشته باشیم، و همچنین چگونه حتی اگر به تعداد بیشتری نیاز داریم، فقط با تعداد کمی پارامتر کار کنیم.

# اجتناب از پارامترهای متعدد (Avoiding multiple parameters)
متدهای نیلادیک (Niladic) نوع ایده‌آل متدها در C# هستند. چنین متدهایی هیچ پارامتری (که به آن‌ها آرگومان (arguments) نیز گفته می‌شود) ندارند. متدهای مونادیک (Monadic) تنها یک پارامتر دارند. متدهای دیادیک (Dyadic) دو پارامتر دارند. متدهای تریادیک (Triadic) سه پارامتر دارند. متدهایی که بیش از سه پارامتر دارند، به عنوان متدهای پلیادیک (Polyadic) شناخته می‌شوند. شما باید هدف خود را بر روی حداقل نگه داشتن تعداد پارامترها (ترجیحاً کمتر از سه) قرار دهید.

در دنیای ایده‌آل برنامه‌نویسی C#، باید تمام تلاش خود را برای اجتناب از متدهای تریادیک (triadic) و پلیادیک (polyadic) به کار بگیرید. دلیل این امر نه به خاطر برنامه‌نویسی بد، بلکه به این خاطر است که کد شما را خواناتر و قابل فهم‌تر می‌کند. متدهایی با پارامترهای زیاد می‌توانند باعث استرس بصری (visual stress) برای برنامه‌نویسان شوند و همچنین می‌توانند منبع آزار باشند. IntelliSense نیز با اضافه کردن پارامترهای بیشتر، می‌تواند برای خواندن و درک دشوار شود.

بیایید به یک مثال بد از یک متد پلیادیک (polyadic) نگاه کنیم که اطلاعات حساب کاربری را به‌روزرسانی می‌کند:

```csharp
public void UpdateUserInfo(int id, string username, string firstName,
string lastName, string addressLine1, string addressLine2, string
addressLine3, string addressLine3, string addressLine4, string city, string
postcode, string region, string country, string homePhone, string
workPhone, string mobilePhone, string personalEmail, string workEmail,
string notes)
{
    // ### implementation omitted ###
}
```

همانطور که متد UpdateUserInfo نشان می‌دهد، کد برای خواندن وحشتناک است. چگونه می‌توانیم متد را طوری اصلاح کنیم که از یک متد پلیادیک (polyadic) به یک متد مونادیک (monadic) تبدیل شود؟ پاسخ ساده است – ما یک شیء UserInfo را به آن پاس می‌دهیم. ابتدا، قبل از اینکه متد را اصلاح کنیم، بیایید نگاهی به کلاس UserInfo خود بیندازیم:

```csharp
public class UserInfo
{
    public int Id { get; set; }
    public string Username { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string AddressLine1 { get; set; }
    public string AddressLine2 { get; set; }
    public string AddressLine3 { get; set; }
    public string AddressLine4 { get; set; }
    public string City { get; set; }
    public string Region { get; set; }
    public string Country { get; set; }
    public string HomePhone { get; set; }
    public string WorkPhone { get; set; }
    public string MobilePhone { get; set; }
    public string PersonalEmail { get; set; }
    public string WorkEmail { get; set; }
    public string Notes { get; set; }
}
```

اکنون ما کلاسی داریم که تمام اطلاعات مورد نیاز برای پاس دادن به متد UpdateUserInfo را شامل می‌شود. متد UpdateUserInfo اکنون می‌تواند از یک متد پلیادیک (polyadic) به یک متد مونادیک (monadic) تبدیل شود، به صورت زیر:

```csharp
public void UpdateUserInfo(UserInfo userInfo)
{
    // ### implementation omitted ###
}
```

کد بالا چقدر بهتر به نظر می‌رسد؟ کوچک‌تر و بسیار خواناتر است. قاعده کلی (rule of thumb) باید این باشد که کمتر از سه پارامتر داشته باشیم، و در حالت ایده‌آل هیچ پارامتری نداشته باشیم. اگر کلاس شما از اصل تک‌مسئولیتی (SRP) پیروی می‌کند، آنگاه پیاده‌سازی الگوی شیء پارامتر (parameter object pattern) را در نظر بگیرید، همانطور که در اینجا انجام داده‌ایم.

# پیاده‌سازی SRP (Implementing SRP)
تمام اشیاء و متدهایی که می‌نویسید، باید حداکثر یک مسئولیت داشته باشند و نه بیشتر. اشیاء می‌توانند متدهای متعددی داشته باشند، اما آن متدها، در مجموع، باید همگی در راستای هدف واحد شیئی که به آن تعلق دارند، کار کنند. متدها می‌توانند چندین متد را فراخوانی کنند که هر کدام کارهای متفاوتی انجام می‌دهند. اما خود متد باید فقط یک کار را انجام دهد.

متدی که بیش از حد می‌داند و انجام می‌دهد، به عنوان متد خدا (God method) شناخته می‌شود. و به همین ترتیب، شیئی که بیش از حد می‌داند و انجام می‌دهد، به عنوان شیء خدا (God object) شناخته می‌شود. اشیاء خدا (God objects) و متدهای خدا (God methods) خواندن، نگهداری و اشکال‌زدایی آن‌ها دشوار است. چنین اشیاء و متدهایی اغلب می‌توانند همان باگ را بارها تکرار کنند. افرادی که در هنر برنامه‌نویسی مهارت دارند، از اشیاء خدا (God objects) و متدهای خدا (God methods) اجتناب می‌کنند. بیایید به متدی نگاه کنیم که بیش از یک کار را انجام می‌دهد:

```csharp
public void SrpBrokenMethod(string folder, string filename, string text,
emailFrom, password, emailTo, subject, message, mediaType)
{
    var file = $"{folder}{filename}";
    File.WriteAllText(file, text);
    MailMessage message = new MailMessage();
    SmtpClient smtp = new SmtpClient();
    message.From = new MailAddress(emailFrom);
    message.To.Add(new MailAddress(emailTo));
    message.Subject = subject;
    message.IsBodyHtml = true;
    message.Body = message;
    Attachment emailAttachment = new Attachment(file);
    emailAttachment.ContentDisposition.Inline = false;
    emailAttachment.ContentDisposition.DispositionType =
    DispositionTypeNames.Attachment;
    emailAttachment.ContentType.MediaType = mediaType;
    emailAttachment.ContentType.Name = Path.GetFileName(filename);
    message.Attachments.Add(emailAttachment);
    smtp.Port = 587;
    smtp.Host = "smtp.gmail.com";
    smtp.EnableSsl = true;
    smtp.UseDefaultCredentials = false;
    smtp.Credentials = new NetworkCredential(emailFrom, password);
    smtp.DeliveryMethod = SmtpDeliveryMethod.Network;
    smtp.Send(message);
}
```

(SrpBrokenMethod) به وضوح بیش از یک کار را انجام می‌دهد، بنابراین SRP را نقض می‌کند. اکنون ما این متد را به تعدادی متد کوچک‌تر تقسیم خواهیم کرد که فقط یک کار را انجام می‌دهند. همچنین به موضوع ماهیت پلیادیک (polyadic) متد، که در آن بیش از دو پارامتر دارد، خواهیم پرداخت.

قبل از اینکه شروع به تقسیم متد به متدهای کوچک‌تر کنیم که تنها یک کار را انجام می‌دهند، باید به تمام عملیاتی که متد در حال انجام آن‌هاست، نگاه کنیم. متد با نوشتن متن در یک فایل شروع می‌شود. سپس یک پیام ایمیل ایجاد می‌کند، یک پیوست به آن اختصاص می‌دهد، و در نهایت ایمیل را ارسال می‌کند. بنابراین، برای این کار، ما به متدهایی برای موارد زیر نیاز داریم:

نوشتن متن در فایل

ایجاد یک پیام ایمیل

اضافه کردن یک پیوست ایمیل

ارسال ایمیل

با نگاهی به متد فعلی، ما چهار پارامتر داریم که برای نوشتن متن در یک فایل به آن پاس داده می‌شوند: یکی برای پوشه، یکی برای نام فایل، یکی برای متن، و یکی برای نوع رسانه (media type). پوشه و نام فایل را می‌توان در یک پارامتر واحد به نام filename ترکیب کرد. اگر filename و folder دو متغیر جداگانه باشند که در کد فراخواننده استفاده می‌شوند، می‌توانند به عنوان یک رشته درون‌یابی شده (interpolated string) واحد، مانند $"‌{folder}{filename}"، به متد پاس داده شوند.

در مورد نوع رسانه، این می‌تواند به صورت خصوصی در یک ساختار (struct) در زمان ساخت تنظیم شود. می‌توانیم از آن ساختار برای تنظیم خصوصیات مورد نیاز خود استفاده کنیم تا بتوانیم ساختار را با سه خصوصیت به عنوان یک پارامتر واحد پاس دهیم. بیایید نگاهی به کدی بیندازیم که این کار را انجام می‌دهد:

```csharp
public struct TextFileData
{
    public string FileName { get; private set; }
    public string Text { get; private set; }
    public MimeType MimeType { get; }

    public TextFileData(string filename, string text)
    {
        Text = text;
        MimeType = MimeType.TextPlain;
        FileName = $"{filename}-{GetFileTimestamp()}";
    }

    public void SaveTextFile()
    {
        File.WriteAllText(FileName, Text);
    }

    private static string GetFileTimestamp()
    {
        var year = DateTime.Now.Year;
        var month = DateTime.Now.Month;
        var day = DateTime.Now.Day;
        var hour = DateTime.Now.Hour;
        var minutes = DateTime.Now.Minute;
        var seconds = DateTime.Now.Second;
        var milliseconds = DateTime.Now.Millisecond;
        return
            $"{year}{month}{day}@{hour}{minutes}{seconds}{milliseconds}";
    }
}
```

سازنده TextFileData اطمینان حاصل می‌کند که مقدار FileName با فراخوانی متد GetFileTimestamp() و افزودن آن به انتهای FileName، منحصر به فرد باشد. برای ذخیره فایل متنی، متد SaveTextFile() را فراخوانی می‌کنیم. توجه داشته باشید که MimeType به صورت داخلی تنظیم شده و روی MimeType.TextPlain قرار گرفته است. می‌توانستیم به سادگی MimeType را به صورت سخت‌کد شده به شکل MimeType = "text/plain"; تنظیم کنیم، اما مزیت استفاده از Enum این است که کد قابل استفاده مجدد است، با این مزیت اضافی که نیازی نیست متن مربوط به یک MimeType خاص را به خاطر بسپارید یا آن را در اینترنت جستجو کنید. حالا، Enum را کدنویسی کرده و یک توضیح (description) به مقدار Enum اضافه می‌کنیم:

```csharp
[Flags]
public enum MimeType
{
    [Description("text/plain")]
    TextPlain
}
```

خب، ما Enum خود را داریم، اما اکنون به راهی برای استخراج توضیح (description) نیاز داریم تا بتوان آن را به راحتی به یک متغیر اختصاص داد. بنابراین، یک کلاس توسعه (extension class) ایجاد خواهیم کرد که به ما امکان می‌دهد توضیح یک Enum را به دست آوریم. این کار به ما امکان می‌دهد MimeType را به صورت زیر تنظیم کنیم:

```csharp
MimeType = MimeType.TextPlain;
```

بدون متد توسعه (extension method)، مقدار MimeType برابر با 0 خواهد بود. اما با متد توسعه (extension method)، مقدار MimeType برابر با "text/plain" است. اکنون می‌توانید این توسعه (extension) را در پروژه‌های دیگر خود نیز استفاده مجدد کرده و آن را بر اساس نیاز خود توسعه دهید.

کلاس بعدی که خواهیم نوشت، کلاس Smtp است که مسئولیت آن ارسال ایمیل از طریق پروتکل Smtp است:

```csharp
public class Smtp
{
    private readonly SmtpClient _smtp;

    public Smtp(Credential credential)
    {
        _smtp = new SmtpClient
        {
            Port = 587,
            Host = "smtp.gmail.com",
            EnableSsl = true,
            UseDefaultCredentials = false,
            Credentials = new NetworkCredential(
                credential.EmailAddress, credential.Password),
            DeliveryMethod = SmtpDeliveryMethod.Network
        };
    }

    public void SendMessage(MailMessage mailMessage)
    {
        _smtp.Send(mailMessage);
    }
}
```

کلاس Smtp دارای یک سازنده (constructor) است که یک پارامتر از نوع Credential می‌گیرد. این اعتبارنامه (credential) برای ورود به سرور ایمیل استفاده می‌شود. سرور در سازنده پیکربندی شده است. هنگامی که متد SendMessage(MailMessage mailMessage) فراخوانی می‌شود، پیام ارسال می‌گردد.

بیایید یک کلاس DemoWorker بنویسیم که کار را به متدهای مختلف تقسیم می‌کند:

```csharp
public class DemoWorker
{
    TextFileData _textFileData;

    public void DoWork()
    {
        SaveTextFile();
        SendEmail();
    }

    public void SendEmail()
    {
        Smtp smtp = new Smtp(new Credential("fakegmail@gmail.com",
            "fakeP@55w0rd"));
        smtp.SendMessage(GetMailMessage());
    }

    private MailMessage GetMailMessage()
    {
        var msg = new MailMessage();
        msg.From = new MailAddress("fakegmail@gmail.com");
        msg.To.Add(new MailAddress("fakehotmail@hotmail.com"));
        msg.Subject = "Some subject";
        msg.IsBodyHtml = true;
        msg.Body = "Hello World!";
        msg.Attachments.Add(GetAttachment());
        return msg;
    }

    private Attachment GetAttachment()
    {
        var attachment = new Attachment(_textFileData.FileName);
        attachment.ContentDisposition.Inline = false;
        attachment.ContentDisposition.DispositionType =
            DispositionTypeNames.Attachment;
        attachment.ContentType.MediaType =
            MimeType.TextPlain.Description();
        attachment.ContentType.Name =
            Path.GetFileName(_textFileData.FileName);
        return attachment;
    }

    private void SaveTextFile()
    {
        _textFileData = new TextFileData(
            $"{Environment.SpecialFolder.MyDocuments}attachment",
            "Here is some demo text!"
        );
        _textFileData.SaveTextFile();
    }
}
```

کلاس DemoWorker نسخه بسیار تمیزتری از ارسال یک پیام ایمیل را نشان می‌دهد. متد اصلی مسئول ذخیره یک پیوست و ارسال آن به عنوان پیوست از طریق ایمیل، DoWork() نام دارد. این متد تنها شامل دو خط کد است. خط اول متد SaveTextFile() را فراخوانی می‌کند، در حالی که خط دوم متد SendEmail() را فراخوانی می‌کند.

متد SaveTextFile() یک ساختار (struct) جدید TextFileData ایجاد می‌کند و نام فایل و مقداری متن را به آن پاس می‌دهد. سپس متد SaveTextFile() را در ساختار TextFileData فراخوانی می‌کند که مسئول ذخیره متن در فایل مشخص شده است.

متد SendEmail() یک کلاس Smtp جدید ایجاد می‌کند. کلاس Smtp دارای یک پارامتر Credential است، در حالی که کلاس Credential دو پارامتر رشته‌ای برای آدرس ایمیل و رمز عبور دارد. ایمیل و رمز عبور برای ورود به سرور SMTP استفاده می‌شوند. پس از ایجاد سرور SMTP، متد SendMessage(MailMessage mailMessage) فراخوانی می‌شود.

این متد نیاز دارد که یک شیء MailMessage به آن پاس داده شود. بنابراین، ما متدی به نام GetMailMethod() داریم که یک شیء MailMessage را می‌سازد و سپس آن را به متد SendMessage(MailMessage mailMessage) پاس می‌دهد. GetMailMethod() با فراخوانی متد GetAttachment()، یک پیوست به MailMessage اضافه می‌کند.


همانطور که از این اصلاحات می‌بینید، کد ما اکنون فشرده‌تر و خواناتر است. این کلید کد باکیفیت است که اصلاح و نگهداری آن آسان باشد: باید خواندن و درک آن آسان باشد. به همین دلیل مهم است که متدهای شما کوچک و تمیز باشند و کمترین تعداد پارامتر ممکن را داشته باشند.

آیا متد شما SRP را نقض می‌کند؟ اگر چنین است، باید در نظر بگیرید که متد را به تعداد مسئولیت‌هایی که دارد، به متدهای کوچک‌تر تقسیم کنید. و این فصل در مورد نوشتن توابع تمیز به پایان می‌رسد. اکنون زمان آن است که آنچه آموخته‌اید را خلاصه کرده و دانش خود را محک بزنید.

# خلاصه (Summary)
در این فصل، دیدید که چگونه برنامه‌نویسی تابعی (functional programming) می‌تواند با عدم تغییر وضعیت (state)، ایمنی کد شما را بهبود بخشد، که می‌تواند منجر به باگ‌ها، به ویژه در برنامه‌های چندریسمانی (multithreaded) شود. با کوچک نگه داشتن متدها با نام‌های معنادار و حداکثر دو پارامتر، دیدید که کد شما چقدر تمیزتر و خواناتر می‌شود. همچنین دیدید که چگونه می‌توانیم تکرار (duplication) را در کد خود حذف کنیم و مزایای انجام این کار را مشاهده کردید. کدی که خواندنش آسان است، نگهداری و توسعه آن آسان‌تر از کدی است که خواندن و رمزگشایی آن دشوار است!

اکنون به سراغ موضوع مدیریت استثنا (exception handling) خواهیم رفت. در فصل بعدی، یاد خواهید گرفت که چگونه از مدیریت استثنا (exception handling) به طور مناسب استفاده کنید، استثناهای سفارشی C# خود را بنویسید که اطلاعات معناداری ارائه می‌دهند، و کدی بنویسید که از ایجاد NullPointerExceptions جلوگیری می‌کند.

# سوالات (Questions)
به متدی که هیچ پارامتری ندارد چه می‌گویند؟

به متدی که یک پارامتر دارد چه می‌گویند؟

به متدی که دو پارامتر دارد چه می‌گویند؟

به متدی که سه پارامتر دارد چه می‌گویند؟

به متدی که بیش از سه پارامتر دارد چه می‌گویند؟

از کدام دو نوع متد باید اجتناب کرد و چرا؟

به زبان ساده، برنامه‌نویسی تابعی (functional programming) چیست؟

برخی از مزایای برنامه‌نویسی تابعی (functional programming) چیست؟

یک عیب برنامه‌نویسی تابعی (functional programming) را نام ببرید.

کد WET چیست و چرا باید از آن اجتناب کرد؟

# مطالعه بیشتر (Further reading)
در اینجا منابع اضافی برای شما آورده شده است تا بتوانید عمیق‌تر به قلمرو برنامه‌نویسی تابعی C# بپردازید:

Functional C# اثر Wisnu Anggoro: https://www.packtpub.com/application-development/functional-c. این کتاب به برنامه‌نویسی تابعی C# اختصاص دارد و اگر می‌خواهید بیشتر بدانید، جای خوبی برای شروع است.

Functional Programming in C# اثر Jovan Poppavic (MSFT): https://www.codeproject.com/Articles/375166/Functional-programming-in-Csharp. این یک مقاله عمیق در مورد برنامه‌نویسی تابعی C# است. حاوی نمودار است و دارای امتیاز ۵ ستاره است.
