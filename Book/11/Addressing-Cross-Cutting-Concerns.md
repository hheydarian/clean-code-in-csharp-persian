# فصل یازدهم:🎯 **پرداختن به مسائل فراگیر (Cross-Cutting Concerns)**

وقتی که می‌خواهید **کد تمیز (Clean Code)** بنویسید، دو نوع **مسئله (Concern)** وجود دارد که باید به آن‌ها توجه کنید:

1. **مسائل اصلی (Core Concerns):** دلایل ایجاد نرم‌افزار و علت توسعه آن هستند.
2. **مسائل فراگیر (Cross-Cutting Concerns):** مسائلی که بخشی از نیازمندی‌های کسب‌وکار نیستند اما در تمام بخش‌های کد باید به آن‌ها توجه شود و با مسائل اصلی در ارتباط هستند.

این موضوع را می‌توان به شکل زیر در نمودار نشان داد: 📊

<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/11/Table%2011-1.jpeg) 
</div>

این فصل بر **مسائل فراگیر (Cross-Cutting Concerns)** تمرکز دارد و هدف ما ساخت یک **کتابخانه کلاس قابل استفاده مجدد (Reusable Class Library)** است که می‌توانید آن را به دلخواه خود تغییر دهید یا گسترش دهید.

**مسائل فراگیر** شامل موارد زیر هستند:

* مدیریت پیکربندی (Configuration Management) ⚙️
* ثبت وقایع (Logging) 📝
* حسابرسی (Auditing) 📊
* امنیت (Security) 🔒
* اعتبارسنجی (Validation) ✔️
* مدیریت استثناها (Exception Handling) ⚠️
* ابزارسنجی (Instrumentation) 🛠️
* تراکنش‌ها (Transactions) 💳
* استفاده بهینه از منابع (Resource Pooling) 🏗️
* کشینگ (Caching) 🗄️
* چندنخی و همزمانی (Threading and Concurrency) 🧵

برای ساخت **کتابخانه قابل استفاده مجدد**، از **الگوی دکوراتور (Decorator Pattern)** و **چارچوب PostSharp Aspect** استفاده خواهیم کرد که در زمان کامپایل به پروژه تزریق می‌شود.

همچنین با مطالعه این فصل خواهید دید که **برنامه‌نویسی مبتنی بر Attribute** چگونه باعث می‌شود:

* حجم کدهای تکراری (Boilerplate Code) کاهش یابد،
* کد کوتاه‌تر، خواناتر و راحت‌تر برای نگهداری و توسعه باشد.

در نتیجه، تنها **کدهای ضروری کسب‌وکار** در متدهای شما باقی می‌ماند و کدهای تکراری مدیریت می‌شوند.

بسیاری از این ایده‌ها را قبلاً هم مطرح کرده‌ایم، اما دوباره ذکر می‌کنیم زیرا **مسائل فراگیر** هستند.

در این فصل، موضوعات زیر را پوشش می‌دهیم:

1. الگوی دکوراتور (Decorator Pattern) 🏷️
2. الگوی پراکسی (Proxy Pattern) 🛡️
3. برنامه‌نویسی مبتنی بر جنبه (Aspect-Oriented Programming – AOP) با PostSharp ⚙️
4. پروژه – کتابخانه قابل استفاده مجدد برای مسائل فراگیر

**در پایان این فصل، شما توانایی‌های زیر را کسب خواهید کرد:**

* پیاده‌سازی الگوی دکوراتور.
* پیاده‌سازی الگوی پراکسی.
* اعمال AOP با استفاده از PostSharp.
* ساخت کتابخانه AOP قابل استفاده مجدد که مسائل فراگیر شما را مدیریت کند.

**پیش‌نیازهای فنی:**
برای بهره‌برداری کامل از این فصل، به **Visual Studio 2019** و **PostSharp** نیاز دارید.
برای دریافت فایل‌های کد این فصل، به لینک زیر مراجعه کنید:
[https://github.com/PacktPublishing/Clean-Code-in-C-/tree/master/CH11](https://github.com/PacktPublishing/Clean-Code-in-C-/tree/master/CH11)

بیایید با **الگوی دکوراتور** شروع کنیم:

---

### الگوی دکوراتور (Decorator Pattern) 🏷️

الگوی طراحی دکوراتور یک **الگوی ساختاری (Structural Pattern)** است که برای **افزودن قابلیت‌های جدید به یک شیء موجود بدون تغییر ساختار آن** استفاده می‌شود.
در این الگو، **کلاس اصلی در کلاس دکوراتور قرار می‌گیرد** و رفتارها و عملیات جدید در زمان اجرا به شیء اضافه می‌شوند.
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/11/Table%2011-2.jpeg) 
</div>

رابطه **Component** و اعضایی که دارد، توسط کلاس‌های **ConcreteComponent** و **Decorator** پیاده‌سازی می‌شود.

* کلاس **ConcreteComponent** رابط **Component** را پیاده‌سازی می‌کند.
* کلاس **Decorator** یک کلاس **انتزاعی (Abstract Class)** است که رابط **Component** را پیاده‌سازی کرده و **ارجاع به یک نمونه از Component** را نگه می‌دارد.
* کلاس **Decorator** به‌عنوان کلاس پایه برای دکوراتورها عمل می‌کند.
* کلاس **ConcreteDecorator** از کلاس **Decorator** ارث‌بری می‌کند و یک دکوراتور برای Componentها فراهم می‌کند.

---

حال یک مثال می‌نویسیم که یک **عملیات را در یک بلاک try/catch** قرار می‌دهد.
در هر دو بخش **try** و **catch**، یک رشته به کنسول چاپ خواهد شد.

1️⃣ یک **Console Application** در .NET 4.8 به نام **CH10\_AddressingCrossCuttingConcerns** بسازید.
2️⃣ یک فولدر به نام **DecoratorPattern** اضافه کنید.
3️⃣ یک **interface** جدید به نام **IComponent** اضافه کنید:

```csharp
public interface IComponent {
   void Operation();
}
```

برای ساده نگه‌داشتن مثال، این interface فقط یک عملیات از نوع void دارد.

---

حالا که interface آماده است، یک **کلاس انتزاعی** اضافه می‌کنیم که آن را پیاده‌سازی کند:

```csharp
public abstract class Decorator : IComponent {
    private IComponent _component;

    public Decorator(IComponent component) {
        _component = component;
    }

    public virtual void Operation() {
        _component.Operation();
    }
}
```

* متغیر عضو **\_component** که شیء IComponent را نگه می‌دارد، از طریق **سازنده (Constructor)** مقداردهی می‌شود.
* متد **Operation()** به صورت **virtual** تعریف شده تا در کلاس‌های مشتق شده بتوان آن را بازنویسی کرد.

---

حال کلاس **ConcreteComponent** را می‌سازیم:

```csharp
public class ConcreteComponent : IComponent {
    public void Operation() {
        throw new NotImplementedException();
    }
}
```

همان‌طور که می‌بینید، این کلاس فقط یک عملیات دارد که **NotImplementedException** پرتاب می‌کند.

---

در ادامه کلاس **ConcreteDecorator** را می‌نویسیم:

```csharp
public class ConcreteDecorator : Decorator {
    public ConcreteDecorator(IComponent component) : base(component) { }

    public override void Operation() {
        try {
            Console.WriteLine("Operation: try block.");
            base.Operation();
        } catch(Exception ex)  {
            Console.WriteLine("Operation: catch block.");
            Console.WriteLine(ex.Message);
        }
    }
}
```

* کلاس **ConcreteDecorator** از کلاس **Decorator** ارث‌بری می‌کند.
* سازنده، یک پارامتر **IComponent** دریافت کرده و آن را به سازنده پایه می‌دهد تا **متغیر عضو** مقداردهی شود.
* در متد **Operation()** بازنویسی شده، یک **بلاک try/catch** داریم:

  * در **try**، پیامی به کنسول نوشته می‌شود و متد Operation() کلاس پایه اجرا می‌شود.
  * در **catch**، در صورت بروز استثنا، پیام خطا همراه با متن Exception چاپ می‌شود.

---

قبل از استفاده از کد، کلاس **Program** را به‌روزرسانی می‌کنیم. متد **DecoratorPatternExample()** را اضافه کنید:

```csharp
private static void DecoratorPatternExample() {
    var concreteComponent = new ConcreteComponent();
    var concreteDecorator = new ConcreteDecorator(concreteComponent);
    concreteDecorator.Operation();
}
```

* در این متد، یک **ConcreteComponent** ایجاد می‌کنیم.
* سپس آن را به سازنده یک **ConcreteDecorator** جدید می‌دهیم.
* در نهایت متد **Operation()** را روی ConcreteDecorator فراخوانی می‌کنیم.

---

دو خط زیر را به **Main()** اضافه کنید:

```csharp
DecoratorPatternExample();
Console.ReadKey();
```

* این دو خط، مثال ما را اجرا می‌کنند و سپس منتظر می‌مانند تا کاربر یک کلید فشار دهد.
* با اجرای برنامه، خروجی مشابه تصویر نمونه نمایش داده می‌شود. 📺
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/11/Table%2011-3.jpeg) 
</div>

با این، مرور ما بر **الگوی دکوراتور (Decorator Pattern)** به پایان رسید ✅.
حالا زمان آن است که به **الگوی پراکسی (Proxy Pattern)** بپردازیم.

---

### الگوی پراکسی (Proxy Pattern) 🛡️

الگوی پراکسی یک **الگوی طراحی ساختاری (Structural Design Pattern)** است که **اشیایی را فراهم می‌کند که به‌عنوان جایگزین برای اشیاء واقعی سرویس (Service Objects) مورد استفاده توسط کلاینت‌ها عمل می‌کنند**.

* پروکسی‌ها درخواست‌های کلاینت را دریافت می‌کنند، کار مورد نیاز را انجام می‌دهند و سپس درخواست را به اشیاء سرویس منتقل می‌کنند.
* اشیاء پروکسی **قابل تعویض با سرویس‌ها هستند** زیرا همان **رابط‌ها (Interfaces)** را دارند.
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/11/Table%2011-4.jpeg) 
</div>

یک نمونه‌ای از زمانی که می‌خواهید **الگوی پراکسی (Proxy Pattern)** را استفاده کنید، زمانی است که:

* یک کلاس دارید که نمی‌خواهید تغییر کند،
* اما نیاز دارید رفتارهای اضافی به آن اضافه شود.

پروکسی‌ها کارها را به اشیاء دیگر واگذار می‌کنند. مگر اینکه پروکسی از سرویس مشتق شده باشد، متدهای پروکسی در نهایت باید به یک **شیء Service** ارجاع دهند.

---

ما یک پیاده‌سازی ساده از **الگوی پراکسی** را بررسی می‌کنیم:

1️⃣ یک فولدر به نام **ProxyPattern** در ریشه پروژه فصل ۱۱ اضافه کنید.
2️⃣ یک **interface** به نام **IService** با یک متد برای مدیریت درخواست بسازید:

```csharp
public interface IService {
    void Request();
}
```

* متد **Request()** کاری را انجام می‌دهد که درخواست را پردازش می‌کند.
* هم پروکسی و هم سرویس این رابط را پیاده‌سازی می‌کنند تا متد **Request()** را استفاده کنند.

---

3️⃣ کلاس **Service** را اضافه کرده و رابط **IService** را پیاده‌سازی کنید:

```csharp
public class Service : IService {
    public void Request() {
        Console.WriteLine("Service: Request();");
    }
}
```

* کلاس **Service** متد واقعی **Request()** را پیاده‌سازی می‌کند.
* این متد توسط کلاس **Proxy** فراخوانی خواهد شد.

---

4️⃣ حال، کلاس **Proxy** را می‌نویسیم:

```csharp
public class Proxy : IService {
    private IService _service;

    public Proxy(IService service) {
        _service = service;
    }

    public void Request() {
        Console.WriteLine("Proxy: Request();");
        _service.Request();
    }
}
```

* کلاس **Proxy** رابط **IService** را پیاده‌سازی می‌کند و یک سازنده دارد که **یک پارامتر IService** می‌گیرد.
* متد **Request()** توسط کلاینت فراخوانی می‌شود.
* **Proxy.Request()** کارهای مورد نیاز خود را انجام داده و سپس مسئول فراخوانی **\_service.Request()** است.

---

5️⃣ برای دیدن عملکرد، کلاس **Program** را به‌روزرسانی می‌کنیم:

```csharp
private static void ProxyPatternExample() {
    Console.WriteLine("### Calling the Service directly. ###");
    var service = new Service();
    service.Request();

    Console.WriteLine("## Calling the Service via a Proxy. ###");
    new Proxy(service).Request();
}
```

* این متد ابتدا **Request()** سرویس را به صورت مستقیم اجرا می‌کند.
* سپس همان متد را از طریق **Proxy** اجرا می‌کند.

با اجرای پروژه، خروجی مشابه نمونه زیر مشاهده خواهد شد: 📺
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/11/Table%2011-5.jpeg) 
</div>

حال که با **الگوی دکوراتور** و **الگوی پراکسی** آشنا شدید، بیایید نگاهی به **برنامه‌نویسی مبتنی بر جنبه (AOP) با PostSharp** بیندازیم. ⚙️

---

### برنامه‌نویسی مبتنی بر جنبه (AOP) با PostSharp 🧩

* **AOP** می‌تواند با **OOP** استفاده شود.
* یک **Aspect** یک **Attribute** است که به کلاس‌ها، متدها، پارامترها و Properties اعمال می‌شود و در **زمان کامپایل** کد را به کلاس، متد، پارامتر یا Property مورد نظر تزریق می‌کند.
* این روش اجازه می‌دهد **مسائل فراگیر (Cross-Cutting Concerns)** از کد کسب‌وکار جدا شده و به یک **کتابخانه کلاس** منتقل شوند.
* این مسائل به عنوان Attributes اضافه می‌شوند و **کامپایلر کد لازم را در زمان اجرا تزریق می‌کند**، که باعث می‌شود کد کسب‌وکار شما **کوچک، خوانا و ساده برای نگهداری و توسعه** باشد.

---

می‌توانید **PostSharp** را از لینک زیر دانلود کنید:
[https://www.postsharp.net/download](https://www.postsharp.net/download)

**نحوه کار AOP با PostSharp:**
1️⃣ بسته PostSharp را به پروژه اضافه کنید.
2️⃣ کد خود را با Attributes نشانه‌گذاری کنید.
3️⃣ کامپایلر C# کد را به باینری تبدیل می‌کند و PostSharp باینری را تحلیل کرده و پیاده‌سازی Aspectها را تزریق می‌کند.

* اگرچه باینری‌ها در زمان کامپایل تغییر می‌کنند، **کد منبع پروژه بدون تغییر باقی می‌ماند**.
* این باعث می‌شود که کد شما **تمیز، ساده و قابل نگهداری** باشد و استفاده مجدد و گسترش آن در آینده آسان‌تر شود.

---

PostSharp الگوهای آماده خوبی برای شما ارائه می‌دهد:

* **MVVM**
* **Caching**
* **Multi-threading**
* **Logging و Architecture Validation**
* و بسیاری دیگر

اگر هیچکدام نیاز شما را برآورده نکرد، می‌توانید **الگوهای خود را با توسعه Framework جنبه‌ها یا معماری** خودکار کنید.

---

### توسعه جنبه‌ها و چارچوب معماری

* با **Aspect Framework**، شما جنبه ساده یا ترکیبی خود را توسعه داده، آن را به کد اعمال می‌کنید و استفاده آن را اعتبارسنجی می‌کنید.
* با **Architectural Framework**، محدودیت‌های معماری سفارشی خود را ایجاد می‌کنید.

**نکته:** هنگام نوشتن Aspect و Attributes باید بسته **PostSharp.Redist NuGet** را اضافه کنید.

* اگر Attributes و Aspectها کار نکردند، روی پروژه راست‌کلیک کرده و **Add PostSharp to Project** را انتخاب کنید.

---

### توسعه یک جنبه ساده

* جنبه ما ساده خواهد بود و شامل یک **Transformation** است.
* جنبه را از کلاس پایه **Primitive Aspect** مشتق می‌کنیم.
* برخی متدها را که به عنوان **Advice** شناخته می‌شوند، بازنویسی می‌کنیم.
* برای ساخت جنبه ترکیبی می‌توانید به لینک زیر مراجعه کنید:
  [https://doc.postsharp.net/complex-aspects](https://doc.postsharp.net/complex-aspects)

---

### تزریق رفتار قبل و بعد از اجرای متد

جنبه **OnMethodBoundaryAspect** الگوی دکوراتور را پیاده‌سازی می‌کند.

* با این جنبه، می‌توانید منطق را **قبل و بعد از اجرای متد هدف** اجرا کنید.

**جدول متدهای Advice در OnMethodBoundaryAspect:**

| Advice                           | متد                                                                       | توضیح |
| -------------------------------- | ------------------------------------------------------------------------- | ----- |
| OnEntry(MethodExecutionArgs)     | قبل از اجرای متد و قبل از هر کد کاربر اجرا می‌شود                         |       |
| OnSuccess(MethodExecutionArgs)   | بعد از اجرای موفق متد، بدون استثنا، اجرا می‌شود                           |       |
| OnException(MethodExecutionArgs) | در صورت بروز Exception بعد از کد کاربر اجرا می‌شود، معادل catch است       |       |
| OnExit(MethodExecutionArgs)      | هنگام خروج متد، چه موفق و چه با Exception، اجرا می‌شود، معادل finally است |       |

---

### نمونه جنبه Logging

1️⃣ ابتدا PostSharp را به پروژه اضافه کنید.
2️⃣ یک فولدر به نام **Aspects** بسازید و یک کلاس جدید به نام **LoggingAspect** اضافه کنید:

```csharp
[PSerializable]
public class LoggingAspect : OnMethodBoundaryAspect { }
```

* `[PSerializable]` باعث می‌شود PostSharp یک Serializer برای **PortableFormatter** ایجاد کند.

---

### بازنویسی متدهای Advice

```csharp
public override void OnEntry(MethodExecutionArgs args) {
    Console.WriteLine("The {0} method has been entered.", args.Method.Name);
}

public override void OnSuccess(MethodExecutionArgs args) {
    Console.WriteLine("The {0} method executed successfully.", args.Method.Name);
}

public override void OnExit(MethodExecutionArgs args) {
    Console.WriteLine("The {0} method has exited.", args.Method.Name);
}

public override void OnException(MethodExecutionArgs args) {
    Console.WriteLine("An exception was thrown in {0}.", args.Method.Name);
}
```

* **OnEntry()** قبل از اجرای هر کد کاربر اجرا می‌شود.
* **OnSuccess()** بعد از اجرای موفق کد کاربر اجرا می‌شود.
* **OnExit()** هنگام خروج متد اجرا می‌شود و معادل finally است.
* **OnException()** در صورت بروز Exception اجرا می‌شود و معادل catch است.

---

### استفاده از LoggingAspect

1️⃣ متد موفق:

```csharp
[LoggingAspect]
private static void SuccessfulMethod() {
    Console.WriteLine("Hello World, I am a success!");
}
```

2️⃣ متد ناموفق:

```csharp
[LoggingAspect]
private static void FailedMethod() {
    Console.WriteLine("Hello World, I am a failure!");
    var x = 1;
    var y = 0;
    var z = x / y;
}
```

* **SuccessfulMethod()** پیام موفقیت را چاپ می‌کند.
* **FailedMethod()** پیام خطا چاپ کرده و عملیات **تقسیم بر صفر** انجام می‌دهد که باعث **DivideByZeroException** می‌شود.

هر دو متد را از **Main()** فراخوانی کنید و پروژه را اجرا کنید. خروجی مشابه نمونه زیر خواهد بود: 📺
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/11/Table%2011-6.jpeg) 
</div>

در این مرحله، **Debugger** باعث خروج برنامه خواهد شد. ✅
همان‌طور که می‌بینید، **ایجاد جنبه‌های PostSharp خودتان** برای برآورده کردن نیازهایتان، فرآیندی ساده است.

حالا به سراغ **افزودن محدودیت‌های معماری (Architectural Constraints)** می‌رویم.

---

### توسعه چارچوب معماری 🏗️

* **محدودیت معماری** یعنی پذیرش الگوهای طراحی سفارشی که باید در تمام ماژول‌ها رعایت شوند.
* ما یک **Scalar Constraint** ایجاد می‌کنیم که **یک عنصر کد را اعتبارسنجی کند**.
* Scalar Constraint ما به نام **BusinessRulePatternValidation**، بررسی می‌کند که هر کلاس مشتق شده از **BusinessRule** باید یک **کلاس داخلی به نام Factory** داشته باشد.

---

ابتدا کلاس **BusinessRulePatternValidation** را اضافه کنید:

```csharp
[MulticastAttributeUsage(MulticastTargets.Class, Inheritance = MulticastInheritance.Strict)]
public class BusinessRulePatternValidation : ScalarConstraint { }
```

* `[MulticastAttributeUsage]` مشخص می‌کند که این جنبه اعتبارسنجی فقط روی کلاس‌ها و با رعایت ارث‌بری کار می‌کند.

---

متد **ValidateCode()** را بازنویسی می‌کنیم:

```csharp
public override void CodeValidation(object target)  {
    var targetType = (Type)target;
    if (targetType.GetNestedType("Factory") == null) {
        Message.Write(
            targetType, SeverityType.Warning,
            "10",
            "You must include a 'Factory' as a nested type for {0}.",
            targetType.DeclaringType,
            targetType.Name);
    }
}
```

* این متد بررسی می‌کند که آیا کلاس هدف، نوع داخلی **Factory** دارد یا خیر.
* اگر **Factory** موجود نباشد، پیام هشدار در پنجره خروجی چاپ می‌شود.

---

کلاس **BusinessRule** را اضافه کنید:

```csharp
[BusinessRulePatternValidation]
public class BusinessRule  { }
```

* کلاس **BusinessRule** خالی است و **Factory** ندارد.
* Attribute **BusinessRulePatternValidation** به آن اختصاص داده شده است، که یک **محدودیت معماری** است.
* پروژه را بسازید و پیام هشدار در پنجره خروجی مشاهده خواهد شد.

---

### پروژه – کتابخانه قابل استفاده مجدد برای مسائل فراگیر

در این بخش، یک **کتابخانه قابل استفاده مجدد** برای مدیریت **مسائل فراگیر** می‌سازیم.

* این کتابخانه عملکرد محدودی دارد اما دانش لازم برای گسترش آن را به شما می‌دهد.
* کتابخانه‌ای که می‌سازید، **.NET Standard Library** خواهد بود تا در برنامه‌های **.NET Framework و .NET Core** قابل استفاده باشد.
* همچنین یک **Console Application** در .NET Framework ایجاد می‌کنیم تا کتابخانه را در عمل ببینیم.

---

### شروع پروژه

1️⃣ یک **.NET Standard Class Library** به نام **CrossCuttingConcerns** بسازید.
2️⃣ یک **.NET Framework Console Application** به نام **TestHarness** به پروژه اضافه کنید.

* در این کتابخانه، قابلیت‌های قابل استفاده مجدد برای مدیریت مسائل مختلف اضافه می‌کنیم، از جمله **Caching**.

---

### افزودن مسأله کشینگ (Caching) 🗄️

* **Caching** تکنیکی برای ذخیره‌سازی و افزایش عملکرد هنگام دسترسی به منابع مختلف است.
* کش می‌تواند **حافظه، فایل‌سیستم یا دیتابیس** باشد.
* برای ساده بودن، ما **Memory Caching** استفاده می‌کنیم.

---

1️⃣ فولدری به نام **Caching** در پروژه **CrossCuttingConcerns** بسازید.
2️⃣ یک کلاس به نام **MemoryCache** اضافه کنید.
3️⃣ پکیج‌های NuGet زیر را اضافه کنید:

* PostSharp
* PostSharp.Patterns.Common
* PostSharp.Patterns.Diagnostics
* System.Runtime.Caching

---

کلاس **MemoryCache** را به صورت زیر به‌روزرسانی کنید:

```csharp
public static class MemoryCache {
    public static T GetItem<T>(string itemName, TimeSpan timeInCache, Func<T> itemCacheFunction) {
        var cache = System.Runtime.Caching.MemoryCache.Default;
        var cachedItem = (T) cache[itemName];
        if (cachedItem != null) return cachedItem;

        var policy = new CacheItemPolicy { AbsoluteExpiration = DateTimeOffset.Now.Add(timeInCache) };
        cachedItem = itemCacheFunction();
        cache.Set(itemName, cachedItem, policy);
        return cachedItem;
    }
}
```

* **GetItem()** نام آیتم کش، مدت زمان نگهداری در کش و تابعی برای ایجاد آیتم در صورت نبود آن در کش را می‌گیرد.

---

### افزودن کلاس تست

1️⃣ در پروژه **TestHarness** یک کلاس جدید به نام **TestClass** اضافه کنید.
2️⃣ متدهای **GetCachedItem()** و **GetMessage()** را اضافه کنید:

```csharp
public string GetCachedItem() {
    return MemoryCache.GetItem<string>("Message", TimeSpan.FromSeconds(30), GetMessage);
}

private string GetMessage() {
    return "Hello, world of cache!";
}
```

* **GetCachedItem()** آیتمی به نام `"Message"` را از کش می‌گیرد.
* اگر در کش موجود نباشد، **GetMessage()** آن را برای ۳۰ ثانیه در کش قرار می‌دهد.

---

### به‌روزرسانی متد Main()

```csharp
var harness = new TestClass();
Console.WriteLine(harness.GetCachedItem());
Console.WriteLine(harness.GetCachedItem());
Thread.Sleep(TimeSpan.FromSeconds(1));
Console.WriteLine(harness.GetCachedItem());
```

* اولین فراخوانی **GetCachedItem()** آیتم را در کش ذخیره و بازمی‌گرداند.
* فراخوانی دوم، آیتم را از کش دریافت و بازمی‌گرداند.
* **Thread.Sleep** باعث منقضی شدن کش نمی‌شود چون زمان هنوز کمتر از ۳۰ ثانیه است، اما برای شبیه‌سازی عملیات بعدی مفید است.
* آخرین فراخوانی، آیتم را دوباره از کش دریافت و بازمی‌گرداند.
### افزودن قابلیت لاگ‌گیری در فایل 📄

در پروژه ما، فرآیندهای **Logging، Auditing و Instrumentation** خروجی خود را به **یک فایل متنی** می‌فرستند.
برای این کار نیاز به یک کلاس داریم که:

1️⃣ بررسی کند فایل مورد نظر وجود دارد یا خیر و در صورت عدم وجود، آن را ایجاد کند.
2️⃣ خروجی را به فایل اضافه کرده و ذخیره کند.

---

1️⃣ یک فولدر به نام **FileSystem** در کتابخانه کلاس ایجاد کنید.
2️⃣ یک کلاس به نام **LogFile** بسازید و آن را **public static** تعریف کنید.
3️⃣ متغیرهای عضو زیر را اضافه کنید:

```csharp
private static string _location = string.Empty;
private static string _filename = string.Empty;
private static string _file = string.Empty;
```

* `_location` مسیر فولدر **Entry Assembly** را نگه می‌دارد.
* `_filename` نام فایل به همراه پسوند را نگه می‌دارد.
* `_file` مسیر کامل فایل را نگه می‌دارد.

---

#### افزودن فولدر Logs در زمان اجرا

```csharp
private static void AddDirectory() {
    if (!Directory.Exists(_location))
        Directory.CreateDirectory("Logs");
}
```

* این متد بررسی می‌کند که مسیر وجود دارد یا خیر، و در صورت عدم وجود، فولدر ایجاد می‌کند.

---

#### افزودن فایل در صورت عدم وجود

```csharp
private static void AddFile() {
    _file = Path.Combine(_location, _filename);
    if (File.Exists(_file)) return;
    using (File.Create($"Logs\\{_filename}")) { }
}
```

* مسیر و نام فایل ترکیب می‌شوند.
* اگر فایل وجود داشته باشد، متد خارج می‌شود. در غیر این صورت، فایل ایجاد می‌شود.
* استفاده از **using** از بروز **IOException** در ایجاد اولین رکورد جلوگیری می‌کند.

---

#### متد ذخیره داده در فایل

```csharp
public static void AppendTextToFile(string filename, string text) {
    _location = $"{Path.GetDirectoryName(Assembly.GetEntryAssembly()?.Location)}\\Logs";
    _filename = filename;
    AddDirectory();
    AddFile();
    File.AppendAllText(_file, text);
}
```

* این متد نام فایل و متن را دریافت می‌کند و مسیر فولدر **Entry Assembly** را تنظیم می‌کند.
* سپس اطمینان حاصل می‌کند که فولدر و فایل موجود هستند و متن را به فایل اضافه می‌کند.

---

### افزودن مسأله Logging 🖥️

* اکثر برنامه‌ها به **نوعی لاگ‌گیری** نیاز دارند: کنسول، فایل، Event Log و دیتابیس.
* در پروژه ما، تنها به **کنسول و فایل متنی** می‌پردازیم.

1️⃣ فولدری به نام **Logging** در کتابخانه کلاس ایجاد کنید.
2️⃣ یک کلاس به نام **ConsoleLoggingAspect** اضافه کنید و کد زیر را وارد کنید:

```csharp
[PSerializable]
public class ConsoleLoggingAspect : OnMethodBoundaryAspect { }
```

* `[PSerializable]` به PostSharp می‌گوید که یک **Serializer** برای PortableFormatter تولید کند.
* کلاس **ConsoleLoggingAspect** از **OnMethodBoundaryAspect** ارث‌بری می‌کند.

---

### بازنویسی متدهای Advice برای لاگ‌گیری در کنسول

```csharp
public override void OnEntry(MethodExecutionArgs args) {
    Console.WriteLine($"Method: {args.Method.Name}, OnEntry().");
}

public override void OnExit(MethodExecutionArgs args) {
    Console.WriteLine($"Method: {args.Method.Name}, OnExit().");
}

public override void OnSuccess(MethodExecutionArgs args) {
    Console.WriteLine($"Method: {args.Method.Name}, OnSuccess().");
}

public override void OnException(MethodExecutionArgs args) {
    Console.WriteLine($"An exception was thrown in {args.Method.Name}. {args}");
}
```

* **OnEntry()** قبل از اجرای بدنه متد اجرا می‌شود و نام متد و نوع Advice را چاپ می‌کند.
* **OnExit()** بعد از اتمام متد اجرا می‌شود.
* **OnSuccess()** پس از اجرای موفقیت‌آمیز بدون Exception اجرا می‌شود.
* **OnException()** هنگام بروز Exception اجرا می‌شود.

---

### لاگ‌گیری در فایل متنی 📝

* یک کلاس به نام **TextFileLoggingAspect** اضافه کنید.
* این کلاس مشابه **ConsoleLoggingAspect** است، با این تفاوت که:

  * متدهای **OnEntry()، OnExit() و OnSuccess()** از متد **LogFile.AppendTextToFile()** برای افزودن خروجی به فایل **Log.txt** استفاده می‌کنند.
  * متد **OnException()** نیز خروجی را به **Exception.log** اضافه می‌کند.

مثال **OnEntry()** در **TextFileLoggingAspect**:

```csharp
public override void OnEntry(MethodExecutionArgs args) {
    LogFile.AppendTextToFile("Log.txt", $"\nMethod: {args.Method.Name}, OnEntry().");
}
```

---

با این کار قابلیت‌های **لاگ‌گیری در فایل و کنسول** آماده است.
در مرحله بعد، به **مسأله Exceptions** می‌پردازیم. ⚡
### افزودن مسأله **Exception-Handling** ⚠️

در نرم‌افزار، تجربه **Exceptions** توسط کاربران اجتناب‌ناپذیر است.
پس باید روشی برای **لاگ‌گیری آن‌ها** داشته باشیم.

* روش معمول، ذخیره خطا در **فایلی روی سیستم کاربر** است، مثل `Exception.log`.
* ما در این بخش همین کار را انجام می‌دهیم.

---

1️⃣ یک فولدر به نام **Exceptions** در کتابخانه کلاس ایجاد کنید.
2️⃣ یک فایل به نام **ExceptionAspect** بسازید و کد زیر را وارد کنید:

```csharp
[PSerializable]
public class ExceptionAspect : OnExceptionAspect {
    public string Message { get; set; }
    public Type ExceptionType { get; set; }
    public FlowBehavior Behavior { get; set; }

    public override void OnException(MethodExecutionArgs args) {
        var message = args.Exception != null ? args.Exception.Message : "Unknown error occured.";
        LogFile.AppendTextToFile(
            "Exceptions.log", $"\n{DateTime.Now}: Method: {args.Method}, Exception: {message}"
        );
        args.FlowBehavior = FlowBehavior.Continue;
    }

    public override Type GetExceptionType(System.Reflection.MethodBase targetMethod) {
        return ExceptionType;
    }
}
```

* کلاس **ExceptionAspect** ارث‌بری می‌کند از **OnExceptionAspect** و دارای سه ویژگی است:
  1️⃣ `Message`: پیام خطا
  2️⃣ `ExceptionType`: نوع Exception رخ داده
  3️⃣ `FlowBehavior`: تعیین می‌کند بعد از مدیریت Exception، اجرای برنامه ادامه یابد یا متوقف شود.

* **OnException()** ابتدا پیام خطا را می‌سازد، سپس با **LogFile.AppendTextToFile()** آن را در فایل ذخیره می‌کند.

* **GetExceptionType()** نوع Exception رخ داده را برمی‌گرداند.

* برای استفاده، کافی است **\[ExceptionAspect]** را به متد خود اضافه کنید.

---

### افزودن مسأله **Security** 🔒

* نیازهای امنیتی بستگی به پروژه دارند.
* رایج‌ترین موارد: **Authentication و Authorization** برای دسترسی کاربران به بخش‌های مختلف سیستم.
* در این بخش از **Decorator Pattern** برای ایجاد یک **Secure Component** با **Role-Based Methods** استفاده می‌کنیم.

> امنیت موضوع بزرگی است و فراتر از این کتاب می‌باشد.
> منابع پیشنهادی:
>
> * [Security in .NET](https://docs.microsoft.com/en-us/dotnet/standard/security/)
> * [OAuth 2.0 in .NET](https://oauth.net/code/dotnet/)

* ما در این فصل فقط **امنیت سفارشی خودمان** را با Decorator Pattern اضافه می‌کنیم.

---

### ایجاد Component امن

1️⃣ فولدری به نام **Security** بسازید.
2️⃣ یک Interface به نام **ISecureComponent** اضافه کنید:

```csharp
public interface ISecureComponent {
    void AddData(dynamic data);
    int EditData(dynamic data);
    int DeleteData(dynamic data);
    dynamic GetData(dynamic data);
}
```

* چهار متد بالا واضح هستند.
* `dynamic` یعنی هر نوع داده‌ای می‌تواند ورودی یا خروجی باشد.

---

### ایجاد کلاس پایه DecoratorBase

```csharp
public abstract class DecoratorBase : ISecureComponent {
    private readonly ISecureComponent _secureComponent;

    public DecoratorBase(ISecureComponent secureComponent) {
        _secureComponent = secureComponent;
    }

    public virtual void AddData(dynamic data) {
        _secureComponent.AddData(data);
    }

    public virtual int EditData(dynamic data) {
        return _secureComponent.EditData(data);
    }

    public virtual int DeleteData(dynamic data) {
        return _secureComponent.DeleteData(data);
    }

    public virtual dynamic GetData(dynamic data) {
        return _secureComponent.GetData(data);
    }
}
```

* این کلاس متدهای Interface را پیاده‌سازی می‌کند و قابلیت **Override** در کلاس‌های مشتق را فراهم می‌کند.

---

### کلاس ConcreteSecureComponent

* کلاس واقعی که کار امن را انجام می‌دهد.
* هر متد پیام مربوطه را در **Console** چاپ می‌کند.
* `DeleteData()` و `EditData()` مقدار ۱ برمی‌گردانند و `GetData()` مقدار `"Hi!"` را برمی‌گرداند.

---

### مدیریت دسترسی کاربران

```csharp
public readonly struct Credentials {
    public static string Role { get; private set; }

    public Credentials(string username, string password) {
        switch (username) {
            case "System" when password == "Administrator":
                Role = "Administrator";
                break;
            case "End" when password == "User":
                Role = "Restricted";
                break;
            default:
                Role = "Imposter";
                break;
        }
    }
}
```

* `struct` نام کاربری و رمز عبور را می‌گیرد و **Role مناسب** را اختصاص می‌دهد.
* کاربران Restricted دسترسی کمتری نسبت به Administrator دارند.

---

### کلاس ConcreteDecorator برای امنیت

```csharp
public class ConcreteDecorator : DecoratorBase {
    public ConcreteDecorator(ISecureComponent secureComponent) : base(secureComponent) { }

    public override void AddData(dynamic data) {
        if (Credentials.Role.Contains("Administrator") || Credentials.Role.Contains("Restricted")) {
            base.AddData((object)data);
        } else {
            throw new UnauthorizedAccessException("Unauthorized");
        }
    }

    public override int EditData(dynamic data) {
        if (Credentials.Role.Contains("Administrator")) {
            return base.EditData(data);
        }
        throw new UnauthorizedAccessException("Unauthorized");
    }

    public override int DeleteData(dynamic data) {
        if (Credentials.Role.Contains("Administrator")) {
            return base.DeleteData(data);
        }
        throw new UnauthorizedAccessException("Unauthorized");
    }

    public override dynamic GetData(dynamic data) {
        if (Credentials.Role.Contains("Administrator") || Credentials.Role.Contains("Restricted")) {
            return base.GetData(data);
        }
        throw new UnauthorizedAccessException("Unauthorized");
    }
}
```

* **ConcreteDecorator** بررسی می‌کند که کاربر **در Role مجاز** قرار دارد یا خیر.
* فقط کاربران Administrator و Restricted می‌توانند متدها را اجرا کنند.

---

### آماده‌سازی برای اجرای امنیت

```csharp
private static readonly ConcreteDecorator ConcreteDecorator = new ConcreteDecorator(
    new ConcreteSecureComponent()
);

private static void Main(string[] _) {
    new Credentials("End", "User");
    DoSecureWork();
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
}
```

* یک **ConcreteDecorator** ساخته و **ConcreteSecureComponent** را به آن پاس می‌دهیم.
* این شیء در متدهای داده‌ای ما استفاده می‌شود.
* سپس با وارد کردن **نام کاربری و رمز عبور**، کار امن آغاز می‌شود.
ما نام کاربری و رمز عبور را به **struct Credentials** اختصاص می‌دهیم. این کار باعث می‌شود **Role** تنظیم شود. سپس متد **DoSecureWork()** را فراخوانی می‌کنیم.

* متد **DoSecureWork()** مسئول فراخوانی همه متدهای داده‌ای است.
* در انتها، برنامه منتظر می‌ماند تا کاربر یک کلید فشار دهد و سپس خارج می‌شود.

---

### تعریف متد DoSecureWork()

```csharp
private static void DoSecureWork() {
    AddData();
    EditData();
    DeleteData();
    GetData();
}
```

* این متد، همه متدهای داده‌ای را فراخوانی می‌کند که در نهایت به **ConcreteDecorator** منتقل می‌شوند.

---

### تعریف متد AddData() با ExceptionAspect ⚠️

```csharp
[ExceptionAspect(consoleOutput: true)]
private static void AddData() {
    ConcreteDecorator.AddData("Hello, world!");
}
```

* **\[ExceptionAspect]** به **AddData()** اعمال شده است.
* این اطمینان می‌دهد که هر خطایی در **Exceptions.log** ثبت می‌شود.
* پارامتر `consoleOutput: true` باعث می‌شود پیام خطا در **کنسول** هم نمایش داده شود.
* خود متد، متد **AddData()** را روی **ConcreteDecorator** فراخوانی می‌کند.

---

### سایر متدهای داده‌ای

* بقیه متدها (**EditData()**, **DeleteData()**, **GetData()**) را به همان روش تعریف کنید، با اعمال **ExceptionAspect** و فراخوانی متدهای متناظر در **ConcreteDecorator**.

---

پس از اجرای برنامه، باید خروجی مشابه تصویر زیر را مشاهده کنید:

✅ متدها اجرا می‌شوند
✅ پیام‌ها در کنسول چاپ می‌شوند
✅ هر خطایی در **Exceptions.log** ثبت می‌شود

<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/11/Table%2011-7.jpeg) 
</div>

اکنون ما یک **شیء مبتنی بر نقش** داریم که همراه با **مدیریت استثناها** کار می‌کند. گام بعدی ما، پیاده‌سازی **Validation Concern** یا بررسی اعتبار داده‌ها است. ✅

---

## افزودن **Validation Concern** 🔍

تمام داده‌های وارد شده توسط کاربر باید **اعتبارسنجی** شوند، چرا که ممکن است **خطرناک، ناقص یا با فرمت اشتباه** باشند. هدف این است که اطمینان حاصل کنیم داده‌ها **پاک و ایمن** هستند.

برای نمونه‌ی ما، **اعتبارسنجی null** را پیاده‌سازی می‌کنیم.

### 1️⃣ افزودن کلاس AllowNullAttribute

* یک پوشه به نام **Validation** به پروژه اضافه کنید.
* سپس کلاس زیر را اضافه کنید:

```csharp
[AttributeUsage(AttributeTargets.Parameter | AttributeTargets.ReturnValue |
 AttributeTargets.Property)]
public class AllowNullAttribute : Attribute { }
```

* این **Attribute** اجازه می‌دهد که پارامترها، مقادیر بازگشتی و Properties بتوانند **null** باشند.

---

### 2️⃣ افزودن enum ValidationFlags

* در فایلی با همان نام، enum زیر را اضافه کنید:

```csharp
[Flags]
public enum ValidationFlags {
    Properties = 1,
    Methods = 2,
    Arguments = 4,
    OutValues = 8,
    ReturnValues = 16,
    NonPublic = 32,
    AllPublicArguments = Properties | Methods | Arguments,
    AllPublic = AllPublicArguments | OutValues | ReturnValues,
    All = AllPublic | NonPublic
}
```

* این **Flags** مشخص می‌کنند که **Aspect** بر روی چه اجزایی اعمال شود.

---

### 3️⃣ افزودن کلاس ReflectionExtensions

```csharp
public static class ReflectionExtensions {
    private static bool IsCustomAttributeDefined<T>(this ICustomAttributeProvider value) where T : Attribute {
        return value.IsDefined(typeof(T), false);
    }

    public static bool AllowsNull(this ICustomAttributeProvider value) {
        return value.IsCustomAttributeDefined<AllowNullAttribute>();
    }

    public static bool MayNotBeNull(this ParameterInfo arg) {
        return !arg.AllowsNull() && !arg.IsOptional && !arg.ParameterType.IsValueType;
    }
}
```

* **IsCustomAttributeDefined()**: بررسی می‌کند که آیا Attribute مورد نظر روی عضو اعمال شده است یا نه.
* **AllowsNull()**: بررسی می‌کند که آیا \[AllowNull] اعمال شده است یا خیر.
* **MayNotBeNull()**: بررسی می‌کند که null مجاز است یا خیر، پارامتر اختیاری است یا نه، و نوع داده پارامتر چیست. نتیجه **Boolean** بازگردانده می‌شود.

---

### 4️⃣ افزودن کلاس DisallowNonNullAspect

```csharp
[PSerializable]
public class DisallowNonNullAspect : OnMethodBoundaryAspect {
    private int[] _inputArgumentsToValidate;
    private int[] _outputArgumentsToValidate;
    private string[] _parameterNames;
    private bool _validateReturnValue;
    private string _memberName;
    private bool _isProperty;

    public DisallowNonNullAspect() : this(ValidationFlags.AllPublic) { }

    public DisallowNonNullAspect(ValidationFlags validationFlags) {
        ValidationFlags = validationFlags;
    }

    public ValidationFlags ValidationFlags { get; set; }
}
```

* کلاس **DisallowNonNullAspect** از **OnMethodBoundaryAspect** ارث‌بری می‌کند.
* متغیرها برای نگهداری پارامترهای ورودی، خروجی، نام پارامترها، بررسی مقادیر بازگشتی و نام عضو استفاده می‌شوند.
* سازنده پیش‌فرض برای اعمال Validator بر تمام اعضای عمومی است.

---

### 5️⃣ Override متد CompileTimeValidate

```csharp
public override bool CompileTimeValidate(MethodBase method) {
    var methodInformation = MethodInformation.GetMethodInformation(method);
    var parameters = method.GetParameters();

    if (!ValidationFlags.HasFlag(ValidationFlags.NonPublic) && !methodInformation.IsPublic) return false;
    if (!ValidationFlags.HasFlag(ValidationFlags.Properties) && methodInformation.IsProperty) return false;
    if (!ValidationFlags.HasFlag(ValidationFlags.Methods) && !methodInformation.IsProperty) return false;

    _parameterNames = parameters.Select(p => p.Name).ToArray();
    _memberName = methodInformation.Name;
    _isProperty = methodInformation.IsProperty;

    var argumentsToValidate = parameters.Where(p => p.MayNotBeNull()).ToArray();
    _inputArgumentsToValidate = ValidationFlags.HasFlag(ValidationFlags.Arguments) ?
                                argumentsToValidate.Where(p => !p.IsOut).Select(p => p.Position).ToArray() :
                                new int[0];

    _outputArgumentsToValidate = ValidationFlags.HasFlag(ValidationFlags.OutValues) ?
                                 argumentsToValidate.Where(p => p.ParameterType.IsByRef).Select(p => p.Position).ToArray() :
                                 new int[0];

    if (!methodInformation.IsConstructor) {
        _validateReturnValue = ValidationFlags.HasFlag(ValidationFlags.ReturnValues) &&
                               methodInformation.ReturnParameter.MayNotBeNull();
    }

    var validationRequired = _validateReturnValue || _inputArgumentsToValidate.Length > 0 || _outputArgumentsToValidate.Length > 0;
    return validationRequired;
}
```

* این متد تضمین می‌کند که **Aspect** در زمان **Compile-Time** بر عضو مناسب اعمال شده باشد.

---

### 6️⃣ Override متد OnEntry

```csharp
public override void OnEntry(MethodExecutionArgs args) {
    foreach (var argumentPosition in _inputArgumentsToValidate) {
        if (args.Arguments[argumentPosition] != null) continue;
        var parameterName = _parameterNames[argumentPosition];
        if (_isProperty) {
            throw new ArgumentNullException(parameterName, $"Cannot set the value of property '{_memberName}' to null.");
        } else {
            throw new ArgumentNullException(parameterName);
        }
    }
}
```

* بررسی می‌کند که پارامترهای ورودی **null** نباشند و در صورت وجود، **ArgumentNullException** پرتاب می‌شود.

---

### 7️⃣ Override متد OnSuccess

```csharp
public override void OnSuccess(MethodExecutionArgs args) {
    foreach (var argumentPosition in _outputArgumentsToValidate) {
        if (args.Arguments[argumentPosition] != null) continue;
        var parameterName = _parameterNames[argumentPosition];
        throw new InvalidOperationException($"Out parameter '{parameterName}' is null.");
    }

    if (!_validateReturnValue || args.ReturnValue != null) return;

    if (_isProperty) {
        throw new InvalidOperationException($"Return value of property '{_memberName}' is null.");
    }
    throw new InvalidOperationException($"Return value of method '{_memberName}' is null.");
}
```

* پارامترهای خروجی را بررسی می‌کند و در صورت **null** بودن، **InvalidOperationException** پرتاب می‌شود.

---

### 8️⃣ افزودن کلاس MethodInformation

* برای استخراج اطلاعات متدها و سازنده‌ها:

```csharp
private class MethodInformation {
    public string Name { get; private set; }
    public bool IsProperty { get; private set; }
    public bool IsPublic { get; private set; }
    public bool IsConstructor { get; private set; }
    public ParameterInfo ReturnParameter { get; private set; }

    private MethodInformation(ConstructorInfo constructor) : this((MethodBase)constructor) {
        IsConstructor = true;
        Name = constructor.Name;
    }

    private MethodInformation(MethodInfo method) : this((MethodBase)method) {
        IsConstructor = false;
        Name = method.Name;
        if (method.IsSpecialName && (Name.StartsWith("set_") || Name.StartsWith("get_"))) {
            Name = Name.Substring(4);
            IsProperty = true;
        }
        ReturnParameter = method.ReturnParameter;
    }

    private MethodInformation(MethodBase method) {
        IsPublic = method.IsPublic;
    }

    private static MethodInformation CreateInstance(MethodInfo method) {
        return new MethodInformation(method);
    }

    public static MethodInformation GetMethodInformation(MethodBase methodBase) {
        var ctor = methodBase as ConstructorInfo;
        if (ctor != null) return new MethodInformation(ctor);
        var method = methodBase as MethodInfo;
        return method == null ? null : CreateInstance(method);
    }
}
```

---

📌 اکنون **Validation Aspect** آماده است و می‌توانید:

* `[AllowNull]` برای مجاز کردن **null**
* `[DisallowNonNullAspect]` برای جلوگیری از **null**

گام بعدی ما اضافه کردن **Transaction Concern** خواهد بود.

اکنون کتابخانه ما شامل چند **Cross-Cutting Concern** آماده و قابل استفاده است. بیایید بخش‌هایی که اضافه کرده‌ایم را به طور خلاصه مرور کنیم: ✅

---

## 1️⃣ **Transaction Concern** 💳

* **هدف:** تضمین اجرای کامل یا rollback تراکنش‌ها.
* **پیاده‌سازی:**

```csharp
[PSerializable]
[AttributeUsage(AttributeTargets.Method)]
public sealed class RequiresTransactionAspect : OnMethodBoundaryAspect {
    public override void OnEntry(MethodExecutionArgs args) {
        var transactionScope = new TransactionScope(TransactionScopeOption.Required);
        args.MethodExecutionTag = transactionScope;
    }
    public override void OnSuccess(MethodExecutionArgs args) {
        var transactionScope = (TransactionScope)args.MethodExecutionTag;
        transactionScope.Complete();
    }
    public override void OnExit(MethodExecutionArgs args) {
        var transactionScope = (TransactionScope)args.MethodExecutionTag;
        transactionScope.Dispose();
    }
}
```

* `[RequiresTransactionAspect]` روی متد قرار می‌گیرد تا تراکنش آغاز شود، در صورت موفقیت کامل شود و در نهایت Dispose شود.
* برای ثبت خطاهای تراکنش، می‌توانید از `[ExceptionAspect]` نیز استفاده کنید.

---

## 2️⃣ **Resource Pool Concern** 🏊‍♂️

* **هدف:** بهبود عملکرد با ایجاد و استفاده مجدد از اشیاء گران‌قیمت.
* **کلاس ResourcePool<T>**:

```csharp
public class ResourcePool<T> {
    private readonly ConcurrentBag<T> _resources;
    private readonly Func<T> _resourceGenerator;

    public ResourcePool(Func<T> resourceGenerator) {
        _resourceGenerator = resourceGenerator ?? throw new ArgumentNullException(nameof(resourceGenerator));
        _resources = new ConcurrentBag<T>();
    }

    public T Get() => _resources.TryTake(out T item) ? item : _resourceGenerator();
    public void Return(T item) => _resources.Add(item);
}
```

* **استفاده:**

```csharp
var pool = new ResourcePool<Course>(() => new Course());
var course = pool.Get();
pool.Return(course);
```

---

## 3️⃣ **Configuration Settings Concern** ⚙️

* **هدف:** مرکزی کردن تنظیمات برنامه (App.config یا Web.config).
* **کلاس Settings:**

```csharp
public static class Settings {
    public static string GetAppSetting(string key) {
        return System.Configuration.ConfigurationManager.AppSettings[key];
    }
    public static void SetAppSettings(this string key, string value) {
        System.Configuration.ConfigurationManager.AppSettings[key] = value;
    }
}
```

* با **import static** می‌توان از کلاس بدون پیش‌وند استفاده کرد:

```csharp
Console.WriteLine(GetAppSetting("Greeting"));
"Greeting".SetAppSettings("Goodbye!");
Console.WriteLine(GetAppSetting("Greeting"));
```

---

## 4️⃣ **Instrumentation Concern** ⏱️

* **هدف:** پروفایل و اندازه‌گیری زمان اجرای متدها.
* **کلاس InstrumentationAspect:**

```csharp
[PSerializable]
[AttributeUsage(AttributeTargets.Method)]
public class InstrumentationAspect : OnMethodBoundaryAspect {
    public override void OnEntry(MethodExecutionArgs args) {
        LogFile.AppendTextToFile("Profile.log", $"\nMethod: {args.Method.Name}, Start Time: {DateTime.Now}");
        args.MethodExecutionTag = Stopwatch.StartNew();
    }
    public override void OnException(MethodExecutionArgs args) {
        LogFile.AppendTextToFile("Exception.log", $"\n{DateTime.Now}: {args.Exception.Source} {args.Exception.Message}");
    }
    public override void OnExit(MethodExecutionArgs args) {
        var stopwatch = (Stopwatch)args.MethodExecutionTag;
        stopwatch.Stop();
        LogFile.AppendTextToFile("Profile.log", $"\nMethod: {args.Method.Name}, Stop Time: {DateTime.Now}, Duration: {stopwatch.Elapsed}");
    }
}
```

* شروع و پایان متد ثبت شده و مدت زمان اجرای آن به **Profile.log** نوشته می‌شود.
* در صورت وقوع استثناء، خطا در **Exception.log** ذخیره می‌شود.

---

### ✅ نتیجه

حالا شما یک **کتابخانه کامل از Cross-Cutting Concerns** دارید که شامل موارد زیر است:

1. **Caching** – ذخیره موقت داده‌ها
2. **File Logging & Console Logging** – لاگ‌گیری
3. **Exception Handling** – مدیریت استثناء‌ها
4. **Security** – امنیت مبتنی بر نقش
5. **Validation** – اعتبارسنجی داده‌ها
6. **Transactions** – تراکنش‌ها
7. **Resource Pooling** – استفاده مجدد از منابع
8. **Configuration** – مدیریت تنظیمات
9. **Instrumentation** – پروفایلینگ متدها

تمام این موارد با **AOP و Decorator Pattern** پیاده‌سازی شده‌اند و شما می‌توانید به راحتی در پروژه‌های خود از آن‌ها استفاده کنید.

### خلاصه فصل

در این فصل، ما موارد زیر را یاد گرفتیم:

1. **الگوی دکوراتور (Decorator Pattern)**

   * امکان اضافه کردن رفتار جدید به اشیاء بدون تغییر کلاس اصلی.

2. **الگوی پراکسی (Proxy Pattern)**

   * ایجاد شیء جایگزین برای سرویس واقعی.
   * پراکسی درخواست‌های مشتری را دریافت و پردازش کرده و به سرویس اصلی منتقل می‌کند.
   * پراکسی و سرویس یک رابط (interface) مشترک دارند، بنابراین قابل جایگزینی هستند.

3. **برنامه‌نویسی مبتنی بر جنبه (AOP) با PostSharp**

   * **Aspect** و **Attribute** برای تزریق خودکار کد در زمان کامپایل استفاده می‌شوند.
   * امکان مدیریت **Cross-Cutting Concerns** مانند:

     * **Logging** (لاگ‌گیری)
     * **Auditing** (ممیزی)
     * **Security** (امنیت)
     * **Validation** (اعتبارسنجی)
     * **Exception Handling** (مدیریت استثناء‌ها)
     * **Instrumentation** (پروفایلینگ متدها)
     * **Transactions** (تراکنش‌ها)
     * **Resource Pooling** (استفاده مجدد از منابع)
     * **Caching** (ذخیره موقت داده‌ها)
     * **Threading & Concurrency** (چندنخی و همزمانی)

4. **گسترش فریمورک Aspect**

   * ساخت **Aspect** سفارشی و اعمال آن روی متدها یا کلاس‌ها.
   * استفاده از PostSharp و الگوی دکوراتور برای مدیریت Concerns به‌صورت تمیز و قابل نگهداری.

---

### سوالات مرور

1. **Cross-Cutting Concern چیست و AOP مخفف چیست؟**

   * **Cross-Cutting Concern:** مسائلی که بر بخش‌های مختلف برنامه اثر می‌گذارند و نمی‌توان آن‌ها را در یک ماژول خاص محدود کرد (مثل لاگ، امنیت، تراکنش).
   * **AOP:** **Aspect-Oriented Programming** یا برنامه‌نویسی مبتنی بر جنبه.

2. **Aspect چیست و چگونه آن را اعمال می‌کنید؟**

   * **Aspect:** واحدی از رفتار که می‌تواند به روش‌های مختلف برنامه اضافه شود (مثلاً Logging).
   * اعمال از طریق افزودن **Attribute** روی کلاس، متد، پارامتر یا property انجام می‌شود.

3. **Attribute چیست و چگونه آن را اعمال می‌کنید؟**

   * **Attribute:** Metadata یا داده‌های توصیفی برای کد.
   * با قرار دادن `[AttributeName]` روی کلاس یا متد اعمال می‌شود.

4. **Aspects و Attributes چگونه با هم کار می‌کنند؟**

   * Attribute جنبه (Aspect) را مشخص می‌کند.
   * PostSharp در زمان کامپایل کد مربوط به Aspect را در محل مورد نظر تزریق می‌کند.

5. **فرآیند ساخت (Build Process) با Aspects چگونه کار می‌کند؟**

   * کامپایلر کد را به باینری تبدیل می‌کند.
   * PostSharp باینری را تحلیل کرده و کد Aspect را تزریق می‌کند.
   * نتیجه: کد اصلی دست‌نخورده باقی می‌ماند، اما رفتارهای اضافی در زمان اجرا اعمال می‌شوند.

---

### مطالعه بیشتر

* صفحه اصلی PostSharp: [https://www.postsharp.net/](https://www.postsharp.net/)

