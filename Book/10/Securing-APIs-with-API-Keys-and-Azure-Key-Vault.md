# فصل دهم: 🔒 امن‌سازی APIها با کلیدهای API و Azure Key Vault

در این فصل، می‌خواهیم ببینیم چگونه می‌توانیم اسرار و اطلاعات حساس را در **Azure Key Vault** نگه‌داری کنیم. همچنین بررسی خواهیم کرد که چگونه می‌توانیم با استفاده از **کلیدهای API**، کلیدهای خود را با **احراز هویت** و **مجوز مبتنی بر نقش (Role-Based Authorization)** امن کنیم. برای کسب تجربه عملی در زمینه امنیت API، یک **API کامل و کاربردی در حوزه FinTech** خواهیم ساخت.

API ما داده‌های API شخص ثالث را با استفاده از یک **کلید خصوصی** (که در Azure Key Vault محفوظ است) استخراج خواهد کرد. سپس API خود را با دو کلید API امن خواهیم کرد؛ یک کلید برای استفاده داخلی و کلید دوم برای کاربران خارجی.

موضوعاتی که در این فصل پوشش داده می‌شوند:

1. دسترسی به **Morningstar API**
2. ذخیره کلید Morningstar API در **Azure Key Vault**
3. ایجاد برنامه وب **dividend calendar** با **ASP.NET Core** در Azure
4. انتشار برنامه وب خود
5. استفاده از کلید API برای امن‌سازی API تقویم سود سهام
6. تست امنیت کلید API
7. افزودن کدهای تقویم سود سهام
8. محدودسازی (Throttling) استفاده از API

---

### 🎯 اهداف فصل

با مطالعه این فصل، شما با اصول طراحی خوب API آشنا خواهید شد و مهارت‌های لازم برای ارتقای توانایی‌های خود در کار با API را کسب خواهید کرد. این فصل به شما کمک می‌کند تا مهارت‌های زیر را بیاموزید:

* امن‌سازی یک API با **کلید API مشتری (Client API Key)** 🔑
* ذخیره و بازیابی اسرار با استفاده از **Azure Key Vault**
* اجرای دستورات API با **Postman** برای ارسال و دریافت داده
* درخواست و استفاده از APIهای شخص ثالث در **RapidAPI.com**
* محدودسازی استفاده از API (**API Throttling**)
* نوشتن **FinTech APIs** که از داده‌های مالی آنلاین استفاده می‌کنند 💹

---

### ⚙️ پیش‌نیازهای فنی

برای بهره‌مندی کامل از این فصل، مطمئن شوید که پیش‌نیازهای فنی زیر را آماده کرده‌اید:

* **Visual Studio 2019 Community Edition** یا نسخه بالاتر
* کلید شخصی **Morningstar API** از [RapidAPI](https://rapidapi.com/integraatio/api/morningstar1)
* **RestSharp** ([http://restsharp.org/](http://restsharp.org/))
* **Swashbuckle.AspNetCore 5** یا بالاتر
* **Postman** ([https://www.postman.com/](https://www.postman.com/))
* **Swagger** ([https://swagger.io](https://swagger.io))

---

### 🚀 شروع پروژه API – تقویم سود سهام

بهترین روش یادگیری، **یادگیری با عمل کردن** است. بنابراین، ما یک API کاربردی می‌سازیم و آن را امن می‌کنیم. این API ممکن است کامل نباشد و جا برای بهبود داشته باشد، اما شما می‌توانید این بهبودها را خودتان پیاده‌سازی کرده و پروژه را توسعه دهید. هدف اصلی این است که یک API کاملاً عملی داشته باشیم که **یک کار انجام دهد:** بازگرداندن داده‌های مالی شامل **تمام سود سهام شرکت‌ها در سال جاری**.

API تقویم سود سهام ما، که در این فصل خواهیم ساخت، **با کلید API احراز هویت** می‌شود. بسته به کلید استفاده شده، **Authorization** تعیین می‌کند که کاربر داخلی است یا خارجی. کنترلر متد مناسب را با توجه به نوع کاربر اجرا می‌کند. در این فصل تنها **متد کاربران داخلی** پیاده‌سازی خواهد شد، اما شما می‌توانید **متد کاربران خارجی** را نیز به عنوان تمرین خودتان بسازید.

**متد داخلی** یک کلید API را از Azure Key Vault استخراج می‌کند و **چندین فراخوانی API به API شخص ثالث** انجام می‌دهد. داده‌ها در **فرمت JSON** بازگردانی شده، به اشیاء تبدیل (Deserialize) می‌شوند و سپس برای استخراج **پرداخت سود سهام آینده** پردازش می‌شوند و به یک **لیست سود سهام** اضافه می‌شوند. این لیست در نهایت به **فرمت JSON** به فراخواننده بازگردانده می‌شود.

نتیجه نهایی، یک فایل JSON است که شامل **تمام پرداخت‌های برنامه‌ریزی‌شده سود سهام برای سال جاری** است. کاربر نهایی می‌تواند این داده‌ها را به لیستی از سود سهام تبدیل کند و با استفاده از **LINQ** آن را پرس‌وجو کند.

---

### 🏗️ ساختار پروژه

پروژه‌ای که در این فصل خواهیم ساخت، یک **Web API** است که **JSON پردازش‌شده** از APIهای مالی شخص ثالث را بازمی‌گرداند. پروژه، **لیستی از شرکت‌ها از یک بورس مشخص** دریافت می‌کند و سپس برای هر شرکت، داده‌های **سود سهام** را دریافت می‌کند. داده‌های سود سهام برای سال جاری پردازش می‌شوند و در نهایت به **فراخواننده API** در قالب **JSON** بازگردانده می‌شوند.

کاربر نهایی می‌تواند داده‌های JSON را به **اشیاء C#** تبدیل کند و روی این اشیاء **پرس‌وجوهای LINQ** انجام دهد، مثلاً دریافت پرداخت‌های سود سهام **ماه بعد** یا **پرداخت‌های ماه جاری**.

APIهایی که استفاده خواهیم کرد، بخشی از **Morningstar API** هستند که از طریق **RapidAPI.com** در دسترس‌اند. شما می‌توانید برای دریافت **کلید API رایگان Morningstar** ثبت‌نام کنید. ما API خود را با یک **سیستم ورود (Login)** امن خواهیم کرد، جایی که کاربران با **ایمیل و رمز عبور** وارد می‌شوند.

برای ارسال **درخواست‌های POST و GET** به API تقویم سود سهام، به **Postman** نیز نیاز خواهیم داشت.

---

### 🧩 جزئیات پروژه

راه‌حل ما شامل یک پروژه خواهد بود: یک **ASP.NET Core Application** که **Target Framework** آن **.NET Core 3.1** یا بالاتر است.

در ادامه، نحوه **دسترسی به Morningstar API** را بررسی خواهیم کرد. ✅
### 🌐 دسترسی به Morningstar API

به آدرس [https://rapidapi.com/integraatio/api/morningstar1](https://rapidapi.com/integraatio/api/morningstar1) بروید و **کلید دسترسی API** خود را درخواست کنید. این API یک **Freemium API** است، به این معنا که شما مجاز هستید تعداد مشخصی فراخوانی را به صورت رایگان برای مدت محدودی انجام دهید و پس از آن نیاز به پرداخت هزینه دارید. کمی زمان بگذارید و مستندات API را مطالعه کنید. به **پلن‌های قیمتی** توجه کنید و پس از دریافت کلید، آن را **محرمانه نگه دارید** 🔑.

APIهایی که ما به آن‌ها علاقه‌مندیم به شرح زیر هستند:

* **GET /companies/list-by-exchange:** این API لیستی از کشورها برای بورس مشخص‌شده را باز می‌گرداند.
* **GET /dividends:** این API تمام اطلاعات **پرداخت سود سهام تاریخی و فعلی** برای شرکت مشخص‌شده را دریافت می‌کند.

قسمت اول درخواست API، **HTTP verb GET** است که برای بازیابی یک منبع استفاده می‌شود.
قسمت دوم، منبعی است که می‌خواهیم دریافت کنیم. در این مثال، منبع **/companies/list-by-exchange** است. همان‌طور که در مورد دوم لیست بالا می‌بینیم، ما منبع **/dividends** را دریافت می‌کنیم.

می‌توانید هر API را در مرورگر امتحان کنید و داده بازگشتی را مشاهده کنید. توصیه می‌کنم قبل از ادامه، این کار را انجام دهید. این کار به شما کمک می‌کند تا با روند کاری که در پیش داریم، آشنا شوید. جریان اصلی کاری ما به این صورت است:

1. دریافت لیست شرکت‌هایی که به بورس مشخصی تعلق دارند
2. حلقه زدن روی هر شرکت برای دریافت داده‌های سود سهام
3. اگر داده سود سهام دارای تاریخ پرداخت آینده باشد، به **تقویم سود سهام** اضافه می‌شود؛ در غیر این صورت، نادیده گرفته می‌شود.

مهم نیست یک شرکت چند داده سود سهام دارد؛ ما تنها به **اولین رکورد** که **به‌روزترین** است، علاقه‌مندیم.

حالا که کلید API خود را دارید (فرض بر این است که مراحل را دنبال کرده‌اید)، می‌توانیم **ساخت API خود را شروع کنیم** 🚀.

---

### 🔐 ذخیره کلید Morningstar API در Azure Key Vault

ما از **Azure Key Vault** و **Managed Service Identity (MSI)** در یک برنامه وب **ASP.NET Core** استفاده خواهیم کرد. بنابراین، قبل از ادامه، به یک **اشتراک Azure** نیاز دارید. برای مشتریان جدید، یک پیشنهاد رایگان ۱۲ ماهه در دسترس است: [Azure Free](http://azure.microsoft.com/en-us/free)

به عنوان توسعه‌دهندگان وب، **نگهداری اسرار در کد** کار مناسبی نیست، زیرا کد می‌تواند **مهندسی معکوس** شود. اگر کد منبع باز باشد، خطر **بارگذاری کلیدهای شخصی یا سازمانی در سیستم کنترل نسخه عمومی** وجود دارد.

راه حل این مشکل، **ذخیره امن اسرار** است؛ اما این خود یک **چالش** ایجاد می‌کند: برای دسترسی به کلیدهای محرمانه، نیاز به **احراز هویت** داریم. چگونه می‌توانیم این چالش را حل کنیم؟

با **فعال‌سازی MSI** برای سرویس Azure خود می‌توانیم این مشکل را حل کنیم. در نتیجه، **یک Service Principal توسط Azure** تولید می‌شود. این Service Principal توسط برنامه‌هایی که توسط کاربر توسعه داده شده‌اند، برای دسترسی به منابع Microsoft Azure استفاده می‌شود. برای Service Principal می‌توان از **گواهی دیجیتال (Certificate)** یا **نام کاربری و رمز عبور** همراه با **هر نقش دلخواه** که مجموعه مجوزهای لازم را دارد، استفاده کرد.

کسی که کنترل حساب Azure را دارد، مشخص می‌کند هر سرویس چه وظایف خاصی را می‌تواند انجام دهد. معمولاً بهتر است از **محدودیت کامل** شروع کنید و تنها وقتی نیاز بود قابلیت‌ها را اضافه کنید.

**نمودار زیر** روابط بین برنامه‌های وب ASP.NET Core، MSI و سرویس Azure ما را نشان می‌دهد:

<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-1.jpeg) 
</div>

### 🏢 استفاده از Azure AD و MSI برای احراز هویت

**Azure Active Directory (Azure AD)** توسط **MSI** برای تزریق **Service Principal** به نمونه سرویس استفاده می‌شود. یک منبع Azure به نام **Local Metadata Service** برای دریافت **Access Token** استفاده می‌شود و برای احراز هویت دسترسی سرویس به **Azure Key Vault** کاربرد دارد.

کد ما سپس با **Local Metadata Service** که روی منبع Azure در دسترس است، تماس می‌گیرد تا **Access Token** را دریافت کند. این Access Token که از **Local MSI Endpoint** استخراج شده، توسط کد ما برای **احراز هویت در سرویس Azure Key Vault** استفاده می‌شود 🔑.

---

### 🔹 ورود به Azure و ایجاد Resource Group

Azure CLI را باز کرده و دستور زیر را برای ورود به Azure تایپ کنید:

```bash
az login
```

پس از ورود موفق، می‌توانیم یک **Resource Group** ایجاد کنیم. Resource Groupها **کانتینرهای منطقی** هستند که منابع Azure در آن‌ها مستقر و مدیریت می‌شوند.

برای ایجاد یک Resource Group در **East US**، دستور زیر را اجرا کنید:

```bash
az group create --name "<YourResourceGroupName>" --location "East US"
```

این **Resource Group** را در طول فصل برای تمام مراحل استفاده خواهیم کرد.

---

### 🔑 ایجاد Key Vault

برای ایجاد یک **Key Vault**، اطلاعات زیر مورد نیاز است:

1. **نام Key Vault:** رشته‌ای بین ۳ تا ۲۴ کاراکتر، فقط شامل اعداد ۰-۹، حروف کوچک و بزرگ a-z، A-Z و خط تیره (-)
2. **نام Resource Group**
3. **مکان (Location):** مثلا East US یا West US

در Azure CLI، دستور زیر را وارد کنید:

```bash
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

در این مرحله، فقط **حساب Azure شما** مجاز به انجام عملیات روی Key Vault است. در صورت نیاز می‌توانید حساب‌های دیگر را اضافه کنید.

---

### 🔹 افزودن کلید Morningstar API به Key Vault

کلیدی که باید به پروژه اضافه کنیم، **MorningstarApiKey** است. برای افزودن این کلید به Key Vault، دستور زیر را اجرا کنید:

```bash
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "MorningstarApiKey" --value "<YourMorningstarApiKey>"
```

اکنون Key Vault شما، **کلید Morningstar API** را ذخیره کرده است. برای بررسی درست ذخیره شدن مقدار، دستور زیر را اجرا کنید:

```bash
az keyvault secret show --name "MorningstarApiKey" --vault-name "<YourKeyVaultName>"
```

اگر همه چیز درست باشد، مقدار کلید در **کنسول نمایش داده می‌شود** و نام و مقدار کلید ذخیره‌شده را خواهید دید ✅.

---

### 🌐 ایجاد برنامه وب ASP.NET Core – تقویم سود سهام در Azure

برای تکمیل این مرحله از پروژه، به **Visual Studio 2019** با **ASP.NET و Web Development Workload** نصب‌شده نیاز دارید.

۱. ایجاد یک **ASP.NET Core Web Application جدید**:

<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-2.jpeg) 
</div>

۲. مطمئن شوید که **API** انتخاب شده و گزینه **No Authentication** تنظیم شده باشد 🔧
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-3.jpeg) 
</div>

۳. یک **API نمونه پیش‌بینی وضعیت هوا** تعریف شده است و در مرورگر، **کد JSON** زیر را نمایش می‌دهد:

```json
[
  {"date":"2020-04-13T20:02:22.8144942+01:00","temperatureC":0,"temperatureF":32,"summary":"Balmy"},
  {"date":"2020-04-14T20:02:22.8234349+01:00","temperatureC":13,"temperatureF":55,"summary":"Warm"},
  {"date":"2020-04-15T20:02:22.8234571+01:00","temperatureC":3,"temperatureF":37,"summary":"Scorching"},
  {"date":"2020-04-16T20:02:22.8234587+01:00","temperatureC":-2,"temperatureF":29,"summary":"Sweltering"},
  {"date":"2020-04-17T20:02:22.8234602+01:00","temperatureC":-13,"temperatureF":9,"summary":"Cool"}
]
```

---

### ☁️ انتشار برنامه وب ما در Azure

قبل از اینکه بتوانیم برنامه‌های وب خود را منتشر کنیم، ابتدا باید یک **Azure App Service جدید** ایجاد کنیم تا برنامه را روی آن منتشر کنیم.
برای این کار، نیاز به یک **Resource Group** برای نگهداری App Service داریم و همچنین به یک **Hosting Plan جدید** که شامل **مکان، اندازه و ویژگی‌های سرور وب** باشد، نیاز داریم.

مراحل مورد نیاز به شرح زیر است:

۱. مطمئن شوید که از **Visual Studio** به **حساب Azure خود وارد شده‌اید**.
برای ایجاد App Service، روی پروژه‌ای که تازه ایجاد کرده‌اید **راست‌کلیک** کرده و گزینه **Publish** را از منو انتخاب کنید.
این کار، پنجره **Pick a publish target** را نمایش می‌دهد، همان‌طور که در تصویر زیر مشاهده می‌کنید:

<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-4.jpeg) 
</div>

۲. گزینه **App Service | Create New** را انتخاب کرده و روی **Create Profile** کلیک کنید.
سپس یک **Hosting Plan جدید** ایجاد کنید، همان‌طور که در مثال زیر نشان داده شده است:
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-5.jpeg) 
</div>

۳. **Resource Group** را انتخاب کنید. همچنین توصیه می‌شود که **تنظیمات Application Insights** را نیز پیکربندی کنید 🔍
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-6.jpeg) 
</div>

۴. روی **Create** کلیک کنید تا App Service شما ایجاد شود. پس از ایجاد، صفحه **Publish** شما باید به این شکل باشد:
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-7.jpeg) 
</div>

۵. در این مرحله، می‌توانید روی **Site URL** کلیک کنید. این کار، آدرس سایت شما را در مرورگر باز می‌کند.
اگر سرویس شما به‌درستی پیکربندی و در حال اجرا باشد، مرورگر باید صفحه زیر را نمایش دهد:
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-8.jpeg) 
</div>

۶. بیایید **API خود را منتشر کنیم**. روی دکمه **Publish** کلیک کنید. وقتی وب‌سایت اجرا شد، ابتدا یک **صفحه خطا** نمایش داده می‌شود.

آدرس URL را به شکل زیر تغییر دهید:

```
https://dividend-calendar.azurewebsites.net/weatherforecast
```

اکنون صفحه وب باید **کد JSON API پیش‌بینی وضعیت هوا** را نمایش دهد:

```json
[
  {"date":"2020-04-13T19:36:26.9794202+00:00","temperatureC":40,"temperatureF":103,"summary":"Hot"},
  {"date":"2020-04-14T19:36:26.9797346+00:00","temperatureC":7,"temperatureF":44,"summary":"Bracing"},
  {"date":"2020-04-15T19:36:26.9797374+00:00","temperatureC":8,"temperatureF":46,"summary":"Scorching"},
  {"date":"2020-04-16T19:36:26.9797389+00:00","temperatureC":11,"temperatureF":51,"summary":"Freezing"},
  {"date":"2020-04-17T19:36:26.9797403+00:00","temperatureC":3,"temperatureF":37,"summary":"Hot"}
]
```

خدمات ما اکنون **زنده و فعال** است ✅.
اگر وارد **Azure Portal** شوید و به **Resource Group** مربوط به **Hosting Plan** خود بروید، چهار منبع خواهید دید:

1. **App Service:** dividend-calendar
2. **Application Insights:** dividend-calendar
3. **App Service Plan:** DividendCalendarHostingPlan
4. **Key Vault:** هر نامی که برای Key Vault خود انتخاب کرده‌اید. در مثال من، نام آن **Keys-APIs** است، همان‌طور که در تصویر نشان داده شده است.
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-9.jpeg) 
</div>

اگر از **صفحه اصلی Azure Portal** ([https://portal.azure.com/#home](https://portal.azure.com/#home)) روی **App Service** خود کلیک کنید، خواهید دید که می‌توانید:

* به سرویس خود دسترسی داشته باشید و آن را مرور کنید 🌐
* سرویس را **متوقف (Stop)** کنید ⏹️
* سرویس را **راه‌اندازی مجدد (Restart)** کنید 🔄
* سرویس را **حذف (Delete)** کنید 🗑️
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-10.jpeg) 
</div>

### 🗂️ آماده‌سازی پروژه و شروع ساخت تقویم سود سهام

حالا که پروژه ما با **Application Insights** آماده است و **کلید Morningstar API** به‌صورت امن ذخیره شده، می‌توانیم **ساخت تقویم سود سهام** را آغاز کنیم.

---

### 🔑 استفاده از کلید API برای امن‌سازی Dividend Calendar API

برای امن‌سازی دسترسی به **Dividend Calendar API**، از **کلید API مشتری (Client API Key)** استفاده خواهیم کرد. روش‌های مختلفی برای اشتراک‌گذاری کلیدهای مشتری با کاربران وجود دارد، اما ما اینجا به آن نمی‌پردازیم؛ شما می‌توانید استراتژی خود را داشته باشید. تمرکز ما روی **فعال‌سازی دسترسی احراز هویت‌شده و مجاز به API** است.

برای ساده‌تر کردن کار، از **الگوی Repository** استفاده می‌کنیم.

* این الگو کمک می‌کند برنامه ما از **منبع داده زیرین جدا شود**.
* نگهداری و تغییر منبع داده بدون تأثیر بر برنامه را آسان‌تر می‌کند.

در پروژه ما، کلیدها در یک کلاس تعریف خواهند شد، اما در یک پروژه تجاری، بهتر است کلیدها در **Data Store** مانند **Cosmos DB، SQL Server یا Azure Key Vault** ذخیره شوند. استفاده از **Repository Pattern** به شما این امکان را می‌دهد که **کنترل کامل روی منبع داده داشته باشید**.

---

### 🏗️ راه‌اندازی Repository

۱. یک **پوشه جدید با نام Repository** به پروژه اضافه کنید.
۲. یک **Interface** به نام `IRepository` و یک کلاس به نام `InMemoryRepository` که این Interface را پیاده‌سازی می‌کند، اضافه کنید.

**کد Interface:**

```csharp
using CH09_DividendCalendar.Security.Authentication;
using System.Threading.Tasks;

namespace CH09_DividendCalendar.Repository
{
    public interface IRepository
    {
        Task<ApiKey> GetApiKey(string providedApiKey);
    }
}
```

* این Interface یک متد برای **دریافت کلید API** تعریف می‌کند.
* کلاس `ApiKey` بعداً تعریف خواهد شد.

---

### ⚙️ پیاده‌سازی InMemoryRepository

**Usingها:**

```csharp
using CH09_DividendCalendar.Security.Authentication;
using CH09_DividendCalendar.Security.Authorisation;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
```

* **Namespaceهای Security** زمانی ایجاد خواهند شد که کلاس‌های **Authentication و Authorization** اضافه شوند.

**کلاس Repository:**

```csharp
public class InMemoryRepository : IRepository
{
    private readonly IDictionary<string, ApiKey> _apiKeys;

    public Task<ApiKey> GetApiKey(string providedApiKey)
    {
        _apiKeys.TryGetValue(providedApiKey, out var key);
        return Task.FromResult(key);
    }

    public InMemoryRepository()
    {
        var existingApiKeys = new List<ApiKey>
        {
            new ApiKey(1, "Internal", "C5BFF7F0-B4DF-475E-A331F737424F013C", new DateTime(2019, 01, 01),
                new List<string> { Roles.Internal }),
            new ApiKey(2, "External", "9218FACE-3EAC-6574C3F0-08357FEDABE9", new DateTime(2020, 4, 15),
                new List<string> { Roles.External })
        };
        _apiKeys = existingApiKeys.ToDictionary(x => x.Key, x => x);
    }
}
```

* **کلاس InMemoryRepository** متد `GetApiKey()` را پیاده‌سازی می‌کند و یک **Dictionary از کلیدهای API** برمی‌گرداند.
* این کلیدها در متغیر `_apiKeys` ذخیره می‌شوند.
* **Constructor** یک کلید داخلی برای استفاده داخلی و یک کلید خارجی برای استفاده خارجی ایجاد می‌کند و آن‌ها را به **Dictionary** تبدیل می‌کند.

---

### 📡 استفاده از X-Api-Key

ما از **HTTP Header** به نام `X-Api-Key` استفاده خواهیم کرد که **کلید API مشتری** را نگه‌داری کرده و برای **احراز هویت و مجوز** به API ارسال می‌شود.

۱. یک پوشه جدید به نام **Shared** بسازید و فایلی به نام `ApiKeyConstants` اضافه کنید.
۲. کد زیر را وارد کنید:

```csharp
namespace CH09_DividendCalendar.Shared
{
    public struct ApiKeyConstants
    {
        public const string HeaderName = "X-Api-Key";
        public const string MorningstarApiKeyUrl =
            "https://<YOUR_KEY_VAULT_NAME>.vault.azure.net/secrets/MorningstarApiKey";
    }
}
```

* این فایل شامل دو **ثابت (Constant)** است:

  1. **HeaderName:** برای تعیین هویت کاربر
  2. **MorningstarApiKeyUrl:** آدرس کلید Morningstar API در Azure Key Vault

---

### 📄 تنظیم JSON Naming Policy

از آنجا که با داده‌های JSON کار می‌کنیم، نیاز به **تنظیم سیاست نام‌گذاری JSON** داریم.

۱. یک پوشه به نام **Json** بسازید و کلاس `DefaultJsonSerializerOptions` را اضافه کنید:

```csharp
using System.Text.Json;

namespace CH09_DividendCalendar.Json
{
    public static class DefaultJsonSerializerOptions
    {
        public static JsonSerializerOptions Options => new JsonSerializerOptions
        {
            PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
            IgnoreNullValues = true
        };
    }
}
```

* این کلاس **نام‌گذاری camelCase** را اعمال می‌کند و مقادیر Null را نادیده می‌گیرد.

---

اکنون آماده‌ایم تا **احراز هویت و مجوز** را به API خود اضافه کنیم ✅.

### 🔒 راه‌اندازی Authentication و Authorization

حال به سراغ ساخت **کلاس‌های امنیتی برای احراز هویت و مجوز** می‌رویم. قبل از شروع، بهتر است تفاوت بین **Authentication** و **Authorization** را روشن کنیم:

* **Authentication (احراز هویت):** بررسی اینکه آیا کاربر مجاز به دسترسی به API ما هست یا نه.
* **Authorization (مجوز):** تعیین سطح دسترسی و حقوق کاربر پس از ورود به API.

---

### 🛡️ افزودن Authentication

۱. یک پوشه به نام **Security** به پروژه اضافه کنید.
۲. درون این پوشه، دو پوشه جدید به نام‌های **Authentication** و **Authorisation** ایجاد کنید.
۳. ابتدا کلاس‌های **Authentication** را اضافه می‌کنیم. اولین کلاس `ApiKey` است.

**ویژگی‌های کلاس ApiKey:**

```csharp
public int Id { get; }
public string Owner { get; }
public string Key { get; }
public DateTime Created { get; }
public IReadOnlyCollection<string> Roles { get; }
```

* این ویژگی‌ها اطلاعات مربوط به **کلید API و مالک آن** را ذخیره می‌کنند.
* مقداردهی از طریق **Constructor** انجام می‌شود:

```csharp
public ApiKey(int id, string owner, string key, DateTime created,
    IReadOnlyCollection<string> roles)
{
    Id = id;
    Owner = owner ?? throw new ArgumentNullException(nameof(owner));
    Key = key ?? throw new ArgumentNullException(nameof(key));
    Created = created;
    Roles = roles ?? throw new ArgumentNullException(nameof(roles));
}
```

---

### ⚠️ کلاس UnauthorizedProblemDetails

در صورت عدم موفقیت در احراز هویت، کاربر با **Error 403 Unauthorized** مواجه می‌شود.

```csharp
public class UnauthorizedProblemDetails : ProblemDetails
{
    public UnauthorizedProblemDetails(string details = null)
    {
        Title = "Forbidden";
        Detail = details;
        Status = 403;
        Type = "https://httpstatuses.com/403";
    }
}
```

* این کلاس از `Microsoft.AspNetCore.Mvc.ProblemDetails` ارث‌بری می‌کند و جزئیات خطا را ارائه می‌دهد.

---

### 🧩 افزودن AuthenticationBuilderExtensions

```csharp
public static class AuthenticationBuilderExtensions
{
    public static AuthenticationBuilder AddApiKeySupport(
        this AuthenticationBuilder authenticationBuilder,
        Action<ApiKeyAuthenticationOptions> options
    )
    {
        return authenticationBuilder
            .AddScheme<ApiKeyAuthenticationOptions, ApiKeyAuthenticationHandler>(
                ApiKeyAuthenticationOptions.DefaultScheme, options);
    }
}
```

* این متد، **پشتیبانی از کلید API** را به سرویس احراز هویت اضافه می‌کند.
* در متد `ConfigureServices` کلاس `Startup` فراخوانی خواهد شد.

---

### ⚙️ کلاس ApiKeyAuthenticationOptions

```csharp
public class ApiKeyAuthenticationOptions : AuthenticationSchemeOptions
{
    public const string DefaultScheme = "API Key";
    public string Scheme => DefaultScheme;
    public string AuthenticationType = DefaultScheme;
}
```

* این کلاس از `AuthenticationSchemeOptions` ارث‌بری می‌کند و **Default Scheme** را روی **API Key Authentication** تنظیم می‌کند.

---

### 🔑 کلاس ApiKeyAuthenticationHandler

این کلاس **اصلی برای اعتبارسنجی کلید API** و اطمینان از دسترسی مشتری به API است:

```csharp
public class ApiKeyAuthenticationHandler : AuthenticationHandler<ApiKeyAuthenticationOptions>
{
    private const string ProblemDetailsContentType = "application/problem+json";
    private readonly IRepository _repository;
}
```

* از `AuthenticationHandler` ارث‌بری می‌کند و از `ApiKeyAuthenticationOptions` استفاده می‌کند.
* نوع محتوا برای جزئیات خطا `application/problem+json` است.
* `_repository` به عنوان محل نگهداری کلیدهای API تعریف شده است.

---

### 🔧 Constructor

```csharp
public ApiKeyAuthenticationHandler(
    IOptionsMonitor<ApiKeyAuthenticationOptions> options,
    ILoggerFactory logger,
    UrlEncoder encoder,
    ISystemClock clock,
    IRepository repository
) : base(options, logger, encoder, clock)
{
    _repository = repository ?? throw new ArgumentNullException(nameof(repository));
}
```

* پارامترها به کلاس پایه ارسال می‌شوند.
* اگر repository خالی باشد، استثنای NullArgument پرتاب می‌شود.

---

### ⚡ متدهای HandleChallengeAsync و HandleForbiddenAsync

```csharp
protected override async Task HandleChallengeAsync(AuthenticationProperties properties)
{
    Response.StatusCode = 401;
    Response.ContentType = ProblemDetailsContentType;
    var problemDetails = new UnauthorizedProblemDetails();
    await Response.WriteAsync(JsonSerializer.Serialize(problemDetails, DefaultJsonSerializerOptions.Options));
}

protected override async Task HandleForbiddenAsync(AuthenticationProperties properties)
{
    Response.StatusCode = 403;
    Response.ContentType = ProblemDetailsContentType;
    var problemDetails = new ForbiddenProblemDetails();
    await Response.WriteAsync(JsonSerializer.Serialize(problemDetails, DefaultJsonSerializerOptions.Options));
}
```

* `HandleChallengeAsync()` وقتی احراز هویت کاربر موفق نباشد، **Error 401 Unauthorized** برمی‌گرداند.
* `HandleForbiddenAsync()` وقتی بررسی مجوز کاربر شکست بخورد، **Error 403 Forbidden** برمی‌گرداند.

---

### 🔍 متد HandleAuthenticateAsync

```csharp
protected override async Task<AuthenticateResult> HandleAuthenticateAsync()
{
    if (!Request.Headers.TryGetValue(ApiKeyConstants.HeaderName, out var apiKeyHeaderValues))
        return AuthenticateResult.NoResult();

    var providedApiKey = apiKeyHeaderValues.FirstOrDefault();
    if (apiKeyHeaderValues.Count == 0 || string.IsNullOrWhiteSpace(providedApiKey))
        return AuthenticateResult.NoResult();

    var existingApiKey = await _repository.GetApiKey(providedApiKey);
    if (existingApiKey != null) {
        var claims = new List<Claim> { new Claim(ClaimTypes.Name, existingApiKey.Owner) };
        claims.AddRange(existingApiKey.Roles.Select(role => new Claim(ClaimTypes.Role, role)));
        var identity = new ClaimsIdentity(claims, Options.AuthenticationType);
        var identities = new List<ClaimsIdentity> { identity };
        var principal = new ClaimsPrincipal(identities);
        var ticket = new AuthenticationTicket(principal, Options.Scheme);
        return AuthenticateResult.Success(ticket);
    }
    return AuthenticateResult.Fail("Invalid API Key provided.");
}
```

* ابتدا بررسی می‌کند که **Header موجود است یا نه**.
* اگر Header یا مقدار آن موجود نباشد، `AuthenticateResult.NoResult()` برمی‌گرداند.
* کلید سمت سرور از **Repository** دریافت می‌شود و اعتبارسنجی انجام می‌شود.
* اگر کلید معتبر باشد، **Claims** برای کاربر تنظیم و `AuthenticateResult.Success()` برگردانده می‌شود.
* در غیر این صورت، **پیغام خطای Invalid API Key** برگردانده می‌شود.

---

✅ با این کار **احراز هویت (Authentication)** کامل شد. مرحله بعدی، **پیاده‌سازی Authorization** است.
### 🛡️ افزودن Authorization

کلاس‌های **Authorization** را در پوشه **Authorisation** اضافه می‌کنیم.

---

### 🔑 تعریف نقش‌ها (Roles)

```csharp
public struct Roles
{
    public const string Internal = "Internal";
    public const string External = "External";
}
```

* API ما هم برای **کاربران داخلی** و هم **کاربران خارجی** طراحی شده است.
* اما در نسخه حداقلی (MVP)، فقط کد **کاربران داخلی** پیاده‌سازی خواهد شد.

---

### 📜 تعریف سیاست‌ها (Policies)

```csharp
public struct Policies
{
    public const string Internal = nameof(Internal);
    public const string External = nameof(External);
}
```

* دو سیاست برای **کاربران داخلی و خارجی** تعریف شده است.

---

### ⚠️ کلاس ForbiddenProblemDetails

```csharp
public class ForbiddenProblemDetails : ProblemDetails
{
    public ForbiddenProblemDetails(string details = null)
    {
        Title = "Forbidden";
        Detail = details;
        Status = 403;
        Type = "https://httpstatuses.com/403";
    }
}
```

* این کلاس **جزئیات مشکل Forbidden** را ارائه می‌دهد اگر کاربر احراز هویت‌شده **دسترسی لازم را نداشته باشد**.
* می‌توانید یک رشته به Constructor ارسال کنید تا اطلاعات بیشتری ارائه شود.

---

### 🧩 Authorization Handlers

#### ExternalAuthorisationHandler

```csharp
public class ExternalAuthorisationHandler : AuthorizationHandler<ExternalRequirement>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context,
        ExternalRequirement requirement
    )
    {
        if (context.User.IsInRole(Roles.External))
            context.Succeed(requirement);
        return Task.CompletedTask;
    }
}

public class ExternalRequirement : IAuthorizationRequirement
{
}
```

* کلاس `ExternalRequirement` خالی است و Interface `IAuthorizationRequirement` را پیاده‌سازی می‌کند.
* Handler بررسی می‌کند که آیا کاربر نقش **External** دارد یا خیر و مجوز را اعمال می‌کند.

---

#### InternalAuthorisationHandler

```csharp
public class InternalAuthorisationHandler : AuthorizationHandler<InternalRequirement>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context,
        InternalRequirement requirement
    )
    {
        if (context.User.IsInRole(Roles.Internal))
            context.Succeed(requirement);
        return Task.CompletedTask;
    }
}

public class InternalRequirement : IAuthorizationRequirement
{
}
```

* Handler داخلی مجوز کاربران داخلی را بررسی می‌کند.
* اگر کاربر نقش **Internal** داشته باشد، دسترسی داده می‌شود؛ در غیر این صورت، رد می‌شود.

---

### ⚙️ بروزرسانی کلاس Startup

#### متد Configure

```csharp
public void Configure(IApplicationBuilder app, IHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

* اگر در حالت توسعه باشیم، صفحه استثناها به صفحه توسعه‌دهنده تغییر می‌کند.
* از Routing برای تطبیق **URIs با Actions کنترلرها** استفاده می‌شود.
* سپس احراز هویت و مجوز فعال می‌شود و در نهایت Endpoints از کنترلرها map می‌شوند.

---

#### متد ConfigureServices

```csharp
services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme = ApiKeyAuthenticationOptions.DefaultScheme;
    options.DefaultChallengeScheme = ApiKeyAuthenticationOptions.DefaultScheme;
})
.AddApiKeySupport(options => { });

services.AddAuthorization(options =>
{
    options.AddPolicy(Policies.Internal, policy =>
        policy.Requirements.Add(new InternalRequirement()));
    options.AddPolicy(Policies.External, policy =>
        policy.Requirements.Add(new ExternalRequirement()));
});
```

* **Default Scheme** روی **API Key Authentication** تنظیم شده است.
* `AddApiKeySupport()` از **Extension Method** اضافه شده در `AuthenticationBuilderExtensions` استفاده می‌کند.
* سپس **سیاست‌های داخلی و خارجی** اضافه شده و با `InternalRequirement` و `ExternalRequirement` پیوند داده می‌شوند.

---

✅ حالا تمام کلاس‌های **امنیت API Key** اضافه شدند و می‌توانیم با **Postman** صحت عملکرد **Authentication و Authorization** را تست کنیم.
### 🧪 تست امنیت API Key

در این بخش، قصد داریم **احراز هویت و مجوز API Key** خود را با **Postman** تست کنیم.

---

### ۱️⃣ ایجاد کنترلر DividendCalendar

به پوشه **Controllers** یک کلاس به نام `DividendCalendar` اضافه کنید و آن را به شکل زیر بروزرسانی کنید:

```csharp
[ApiController]
[Route("api/[controller]")]
public class DividendCalendar : ControllerBase
{
    [Authorize(Policy = Policies.Internal)]
    [HttpGet("internal")]
    public IActionResult GetDividendCalendar()
    {
        var message = $"Hello from {nameof(GetDividendCalendar)}.";
        return new ObjectResult(message);
    }

    [Authorize(Policy = Policies.External)]
    [HttpGet("external")]
    public IActionResult External()
    {
        var message = "External access is currently unavailable.";
        return new ObjectResult(message);
    }
}
```

* این کلاس **تمام عملکردهای API Dividend Calendar** را شامل می‌شود.
* حتی اگر در نسخه اولیه (MVP) **کاربران خارجی فعال نباشند**، همچنان می‌توانیم احراز هویت و مجوزهای داخلی و خارجی را تست کنیم.

---

### ۲️⃣ تست با Postman

۱. **Postman** را باز کنید و یک درخواست جدید **GET** بسازید.
2\. URL زیر را وارد کنید:

```
https://localhost:44325/api/dividendcalendar/internal
```

۳. روی **Send** کلیک کنید.

* این درخواست، **احراز هویت و مجوز داخلی** را تست می‌کند.
* اگر کلید API معتبر باشد، پیام زیر برگردانده می‌شود:

```
Hello from GetDividendCalendar.
```

* در غیر این صورت، پیام خطای 401 یا 403 نمایش داده می‌شود.

---

با این کار، **امنیت API Key** ما در محیط توسعه تست شده و آماده مرحله بعدی می‌باشد.
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-11.jpeg) 
</div>

### 🔐 تست امنیت API بدون کلید

همان‌طور که مشاهده می‌کنید، **بدون قرار دادن API Key** در درخواست API، پاسخ **401 Unauthorized** دریافت می‌شود و JSON خطا مطابق با کلاس **ForbiddenProblemDetails** نمایش داده می‌شود.

---

### ۱️⃣ افزودن هدر X-Api-Key

* در Postman به بخش **Headers** بروید و هدر زیر را اضافه کنید:

```
Key: X-Api-Key
Value: C5BFF7F0-B4DF-475E-A331-F737424F013C
```

---

### ۲️⃣ ارسال درخواست

* پس از افزودن هدر، روی **Send** کلیک کنید.
* این بار، با وجود **API Key معتبر**، پاسخ موفقیت‌آمیز داخلی دریافت می‌کنید:

```
Hello from GetDividendCalendar.
```

✅ بدین ترتیب، **احراز هویت و مجوز کاربران داخلی** با موفقیت تست شد.
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-12.jpeg) 
</div>

### ✅ دریافت وضعیت 200 OK

* حالا وضعیت پاسخ **200 OK** خواهید داشت.
* این یعنی **درخواست API با موفقیت انجام شده است**.
* نتیجه درخواست را می‌توانید در **Body** مشاهده کنید.
* **کاربران داخلی** پیام زیر را خواهند دید:

```
Hello from GetDividendCalendar.
```

---

### 🔄 تست مسیر خارجی

* درخواست را دوباره اجرا کنید، اما این بار **URL را از internal به external تغییر دهید**.
* URL جدید باید به شکل زیر باشد:

```
https://localhost:44325/api/dividendcalendar/external
```

* این کار امکان بررسی **دسترسی و مجوز کاربران خارجی** را فراهم می‌کند.
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-13.jpeg) 
</div>

### ⛔ دریافت وضعیت 403 Forbidden

* اکنون باید وضعیت پاسخ **403 Forbidden** را دریافت کنید و **JSON خطا** مطابق با ForbiddenProblemDetails نمایش داده شود.
* دلیل این امر این است که **API Key معتبر است**، اما مسیر مربوط به **کاربران خارجی** است و **کاربر خارجی دسترسی به API داخلی ندارد**.

---

### ۱️⃣ تغییر مقدار هدر X-Api-Key

* مقدار هدر **X-Api-Key** را به شکل زیر تغییر دهید:

```
Value: 9218FACE-3EAC-6574-C3F0-08357FEDABE9
```

---

### ۲️⃣ ارسال مجدد درخواست

* پس از تغییر مقدار هدر، روی **Send** کلیک کنید.
* حالا درخواست با **API Key خارجی معتبر** ارسال شده و باید بتوانید **دسترسی خارجی را با موفقیت بررسی کنید**.
<div align="center">
    
![Conventions-UsedThis-Book](../../assets/image/10/Table%2010-14.jpeg) 
</div>

### ✅ مشاهده وضعیت 200 OK

* اکنون وضعیت پاسخ **200 OK** را مشاهده خواهید کرد و در بخش body پیام زیر نمایش داده می‌شود:

```
External access is currently unavailable
```

* خبر خوب! سیستم **امنیت مبتنی بر نقش** ما با استفاده از **API key authentication و authorization** تست شده و کار می‌کند.
* قبل از اینکه حتی **FinTech API** واقعی خود را اضافه کنیم، **API Key امنیتی** را پیاده‌سازی و آزمایش کرده‌ایم. بنابراین امنیت API را **قبل از نوشتن حتی یک خط کد از API واقعی** رعایت کرده‌ایم.
* اکنون می‌توانیم با اطمینان شروع کنیم به ساخت **Dividend Calendar API**، زیرا می‌دانیم امنیت آن برقرار است. 🛡️

---

## 📝 افزودن کد Dividend Calendar

* API داخلی ما تنها یک هدف دارد: ساخت یک **آرایه از dividend ها** که قرار است در سال جاری پرداخت شوند.
* شما می‌توانید پروژه را توسعه دهید تا **JSON را در یک فایل یا پایگاه داده ذخیره کنید** و به این ترتیب تنها **یک بار در ماه به API داخلی فراخوانی بزنید** تا هزینه‌ها کاهش یابد.
* نقش خارجی می‌تواند به داده‌ها از **فایل یا پایگاه داده** هر زمان که نیاز است دسترسی پیدا کند.

---

### ۱️⃣ ایجاد مدل Dividend

* مدل داخلی ما قبلاً کنترلر دارد و امنیت آن از دسترسی غیرمجاز جلوگیری می‌کند.
* اکنون باید **JSON dividend calendar** را تولید کنیم تا متد ما بازگرداند.

```csharp
public class Dividend
{
    public string Mic { get; set; }
    public string Ticker { get; set; }
    public string CompanyName { get; set; }
    public float DividendYield { get; set; }
    public float Amount { get; set; }
    public DateTime? ExDividendDate { get; set; }
    public DateTime? DeclarationDate { get; set; }
    public DateTime? RecordDate { get; set; }
    public DateTime? PaymentDate { get; set; }
    public string DividendType { get; set; }
    public string CurrencyCode { get; set; }
}
```

* این فیلدها شامل موارد زیر هستند:

  * **Mic:** کد شناسایی بازار (ISO 10383 Market Identification Code)
  * **Ticker:** نماد سهام
  * **CompanyName:** نام شرکت
  * **DividendYield:** نسبت سود سالانه شرکت به قیمت سهم
  * **Amount:** مبلغ پرداختی به ازای هر سهم
  * **ExDividendDate:** آخرین تاریخ خرید سهم برای دریافت سود بعدی
  * **DeclarationDate:** تاریخ اعلام پرداخت سود توسط شرکت
  * **RecordDate:** تاریخی که شرکت بررسی می‌کند چه کسانی حق دریافت سود دارند
  * **PaymentDate:** تاریخ دریافت سود توسط سهامداران
  * **DividendType:** نوع سود (مثلاً Cash, Stock, Property و…)
  * **CurrencyCode:** واحد پول پرداخت

---

### ۲️⃣ ایجاد مدل Company

```csharp
public class Company
{
    public string MIC { get; set; }
    public string Currency { get; set; }
    public string Ticker { get; set; }
    public string SecurityId { get; set; }
    public string CompanyName { get; set; }
}
```

* تفاوت بین **CurrencyCode** در Dividend و **Currency** در Company به دلیل فرآیند **Object Mapping JSON** است تا از بروز استثناهای قالب‌بندی جلوگیری شود.

---

### ۳️⃣ ایجاد مدل Companies

```csharp
public class Companies
{
    public int Total { get; set; }
    public int Offset { get; set; }
    public List<Company> Results { get; set; }
    public string ResponseStatus { get; set; }
}
```

* توضیح فیلدها:

  * **Total:** تعداد کل رکوردهای بازگشتی از API
  * **Offset:** جابجایی رکورد
  * **Results:** لیست شرکت‌ها
  * **ResponseStatus:** اطلاعات وضعیت پاسخ، مخصوصاً در صورت بروز خطا

---

### ۴️⃣ ایجاد مدل Dividends

```csharp
public class Dividends
{
    public int Total { get; set; }
    public int Offset { get; set; }
    public List<Dictionary<string, string>> Results { get; set; }
    public ResponseStatus ResponseStatus { get; set; }
}
```

* فیلدها مشابه کلاس قبلی هستند، به جز **Results** که شامل لیست پرداخت‌های dividend برای هر شرکت مشخص است.

---

### ۵️⃣ ایجاد مدل ResponseStatus

```csharp
public class ResponseStatus
{
    public string ErrorCode { get; set; }
    public string Message { get; set; }
    public string StackTrace { get; set; }
    public List<Dictionary<string, string>> Errors { get; set; }
    public List<Dictionary<string, string>> Meta { get; set; }
}
```

* توضیح فیلدها:

  * **ErrorCode:** شماره خطا
  * **Message:** پیام خطا
  * **StackTrace:** اطلاعات دیباگ خطا
  * **Errors:** لیست خطاها
  * **Meta:** لیست متادیتای خطا

---

✅ تا این مرحله، تمام مدل‌های لازم برای **API Dividend Calendar** آماده شده‌اند و می‌توانیم به نوشتن منطق پردازش داده‌ها و بازگرداندن JSON ادامه دهیم.

### 🛠️ پیاده‌سازی فراخوانی‌های API برای ساخت Dividend Calendar

حال که تمام **مدل‌های لازم** آماده شده‌اند، می‌توانیم شروع کنیم به **فراخوانی‌های API** برای ساخت **تقویم پرداخت dividend**.

---

### ۱️⃣ متد FormatStringDate()

```csharp
private DateTime? FormatStringDate(string date)
{
    return string.IsNullOrEmpty(date) ? (DateTime?)null : DateTime.Parse(date);
}
```

* این متد یک **رشته تاریخ** می‌گیرد.
* اگر رشته **null یا خالی** باشد، مقدار null باز می‌گرداند.
* در غیر این صورت، رشته را به **DateTime nullable** تبدیل کرده و باز می‌گرداند. 🗓️

---

### ۲️⃣ متد GetMorningstarApiKey()

```csharp
private async Task<string> GetMorningstarApiKey()
{
    try
    {
        AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
        KeyVaultClient keyVaultClient = new KeyVaultClient(
            new KeyVaultClient.AuthenticationCallback(
                azureServiceTokenProvider.KeyVaultTokenCallback
            )
        );

        var secret = await keyVaultClient.GetSecretAsync(ApiKeyConstants.MorningstarApiKeyUrl)
                                         .ConfigureAwait(false);
        return secret.Value;
    }
    catch (KeyVaultErrorException keyVaultException)
    {
        return keyVaultException.Message;
    }
}
```

* این متد **AzureServiceTokenProvider** را ایجاد می‌کند.
* سپس یک **KeyVaultClient** می‌سازد که عملیات رمزنگاری انجام می‌دهد.
* با فراخوانی **GetSecretAsync**، API Key مربوط به Morningstar از **Azure Key Vault** دریافت می‌شود.
* در صورت بروز خطا، پیام خطا بازگردانده می‌شود. 🔑

---

### ۳️⃣ متد GetCompanies()

```csharp
private Companies GetCompanies(string mic)
{
    var client = new RestClient($"https://morningstar1.p.rapidapi.com/companies/list-by-exchange?Mic={mic}");
    var request = new RestRequest(Method.GET);
    request.AddHeader("x-rapidapi-host", "morningstar1.p.rapidapi.com");
    request.AddHeader("x-rapidapi-key", GetMorningstarApiKey().Result);
    request.AddHeader("accept", "string");
    IRestResponse response = client.Execute(request);
    return JsonConvert.DeserializeObject<Companies>(response.Content);
}
```

* این متد یک **REST client** می‌سازد که لیست شرکت‌های موجود در بورس مشخص شده را باز می‌گرداند.
* نوع درخواست **GET** است و هدرهای **x-rapidapi-host، x-rapidapi-key و accept** اضافه می‌شوند.
* سپس JSON بازگشتی به مدل **Companies** دسیریالایز می‌شود. 🏢

---

### ۴️⃣ متد GetDividends()

```csharp
private Dividends GetDividends(string mic, string ticker)
{
    var client = new RestClient($"https://morningstar1.p.rapidapi.com/dividends?Ticker={ticker}&Mic={mic}");
    var request = new RestRequest(Method.GET);
    request.AddHeader("x-rapidapi-host", "morningstar1.p.rapidapi.com");
    request.AddHeader("x-rapidapi-key", GetMorningstarApiKey().Result);
    request.AddHeader("accept", "string");
    IRestResponse response = client.Execute(request);
    return JsonConvert.DeserializeObject<Dividends>(response.Content);
}
```

* مشابه **GetCompanies()** است، اما این بار **dividends** هر شرکت مشخص را باز می‌گرداند. 💰

---

### ۵️⃣ متد BuildDividendCalendar()

```csharp
private List<Dividend> BuildDividendCalendar()
{
    const string MIC = "XLON";
    var thisYearsDividends = new List<Dividend>();
    var companies = GetCompanies(MIC);

    foreach (var company in companies.Results) {
        var dividends = GetDividends(MIC, company.Ticker);
        if (dividends.Results == null)
            continue;

        var currentDividend = dividends.Results.FirstOrDefault();
        if (currentDividend == null || currentDividend["payableDt"] == null)
            continue;

        var dateDiff = DateTime.Compare(
            DateTime.Parse(currentDividend["payableDt"]),
            new DateTime(DateTime.Now.Year - 1, 12, 31)
        );

        if (dateDiff > 0) {
            var payableDate = DateTime.Parse(currentDividend["payableDt"]);
            var dividend = new Dividend() {
                Mic = MIC,
                Ticker = company.Ticker,
                CompanyName = company.CompanyName,
                ExDividendDate = FormatStringDate(currentDividend["exDividendDt"]),
                DeclarationDate = FormatStringDate(currentDividend["declarationDt"]),
                RecordDate = FormatStringDate(currentDividend["recordDt"]),
                PaymentDate = FormatStringDate(currentDividend["payableDt"]),
                Amount = float.Parse(currentDividend["amount"])
            };
            thisYearsDividends.Add(dividend);
        }
    }

    return thisYearsDividends;
}
```

* در این نسخه، MIC **سخت‌کد شده به "XLON"** (بورس لندن) است.
* در آینده می‌توانیم این متد و **endpoint** عمومی را برای دریافت MIC به عنوان پارامتر به‌روزرسانی کنیم.
* ابتدا لیست **شرکت‌ها** از Morningstar API دریافت می‌شود.
* سپس برای هر شرکت، **dividendهای آن شرکت** گرفته می‌شود و بررسی می‌کنیم که تاریخ پرداخت بعد از **31 دسامبر سال قبل** باشد.
* در نهایت، لیست **dividendهای امسال** ساخته می‌شود و بازگردانده می‌شود. 🔄

---

### ۶️⃣ به‌روزرسانی GetDividendCalendar()

```csharp
[Authorize(Policy = Policies.Internal)]
[HttpGet("internal")]
public IActionResult GetDividendCalendar()
{
    return new ObjectResult(JsonConvert.SerializeObject(BuildDividendCalendar()));
}
```

* این متد **لیست dividendهای امسال** را به صورت JSON سریالایز شده باز می‌گرداند.
* اگر پروژه را در **Postman** با **internal x-api-key** اجرا کنید، پس از حدود **20 دقیقه** JSON زیر برگردانده می‌شود:

```json
[{"Mic":"XLON","Ticker":"ABDP","CompanyName":"AB Dynamics PLC","DividendYield":0.0,"Amount":0.0279,...}]
```

* ⚠️ این کوئری **زمان زیادی** می‌برد (\~20 دقیقه) و نتایج **در طول سال تغییر می‌کند**.
* پیشنهاد: می‌توان API را **ماهانه اجرا** کرد و JSON را در **فایل یا پایگاه داده** ذخیره نمود. سپس endpoint خارجی از این داده ذخیره شده استفاده کند. 🗄️

### ⏱️ محدودسازی (Throttle) API

هنگامی که APIها را در دسترس قرار می‌دهید، **باید آن‌ها را محدود کنید (Throttle)**.
روش‌های مختلفی برای این کار وجود دارد؛ مثل **محدود کردن تعداد کاربران همزمان** یا **تعداد فراخوانی‌ها در یک بازه زمانی مشخص**.

در این بخش، ما می‌خواهیم API خود را طوری محدود کنیم که **فقط یک بار در ماه، روز 25 اجرا شود**.

---

### ۱️⃣ اضافه کردن کلید به appsettings.json

در فایل **appsettings.json**، خط زیر را اضافه کنید:

```json
"MorningstarNextRunDate": null,
```

* این مقدار، **تاریخ اجرای بعدی API** را ذخیره خواهد کرد. 📅

---

### ۲️⃣ کلاس AppSettings

در ریشه پروژه، کلاس زیر را اضافه کنید:

```csharp
public class AppSettings
{
    public DateTime? MorningstarNextRunDate { get; set; }
}
```

* این **property**، مقدار مربوط به کلید **MorningstarNextRunDate** را نگه می‌دارد.

---

### ۳️⃣ متد AddOrUpdateAppSetting()

```csharp
public static void AddOrUpdateAppSetting<T>(string sectionPathKey, T value)
{
    try
    {
        var filePath = Path.Combine(AppContext.BaseDirectory, "appsettings.json");
        string json = File.ReadAllText(filePath);
        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(json);
        SetValueRecursively(sectionPathKey, jsonObj, value);
        string output = Newtonsoft.Json.JsonConvert.SerializeObject(jsonObj, Newtonsoft.Json.Formatting.Indented);
        File.WriteAllText(filePath, output);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error writing app settings | {0}", ex.Message);
    }
}
```

* این متد **appsettings.json** را می‌خواند، JSON را دسیریالایز می‌کند، مقدار مورد نظر را تنظیم می‌کند و دوباره JSON را در فایل ذخیره می‌کند. 📝

---

### ۴️⃣ متد SetValueRecursively()

```csharp
private static void SetValueRecursively<T>(string sectionPathKey, dynamic jsonObj, T value)
{
    var remainingSections = sectionPathKey.Split(":", 2);
    var currentSection = remainingSections[0];
    if (remainingSections.Length > 1)
    {
        var nextSection = remainingSections[1];
        SetValueRecursively(nextSection, jsonObj[currentSection], value);
    }
    else
    {
        jsonObj[currentSection] = value;
    }
}
```

* این متد، مسیر کلید را به صورت **بازگشتی** دنبال می‌کند و در نهایت مقدار مورد نظر را تنظیم می‌کند. 🔄

---

### ۵️⃣ تعریف ThrottleMonthDay

```csharp
public const int ThrottleMonthDay = 25;
```

* این مقدار برای بررسی **روز اجرای ماهانه API** استفاده می‌شود.

---

### ۶️⃣ متد ThrottleMessage()

```csharp
private string ThrottleMessage()
{
    return "This API call can only be made once on the 25th of each month.";
}
```

* این متد پیام هشدار محدودیت را برمی‌گرداند. 🚫

---

### ۷️⃣ دسترسی به AppSettings در Controller

```csharp
public DividendCalendarController(IOptions<AppSettings> appSettings)
{
    _appSettings = appSettings.Value;
}
```

* این **constructor**، امکان دسترسی به مقادیر **appsettings.json** را فراهم می‌کند.

---

### ۸️⃣ پیکربندی در Startup

```csharp
var appSettingsSection = Configuration.GetSection("AppSettings");
services.Configure<AppSettings>(appSettingsSection);
```

* این خطوط اجازه می‌دهند **AppSettings** به صورت **Dependency Injection** در controller در دسترس باشد.

---

### ۹️⃣ متد SetMorningstarNextRunDate()

```csharp
private DateTime? SetMorningstarNextRunDate()
{
    int month;
    if (DateTime.Now.Day < 25)
        month = DateTime.Now.Month;
    else
        month = DateTime.Now.AddMonths(1).Month;

    var date = new DateTime(DateTime.Now.Year, month, ApiKeyConstants.ThrottleMonthDay);

    AppSettings.AddOrUpdateAppSetting<DateTime?>("MorningstarNextRunDate", date);

    return date;
}
```

* بررسی می‌کند که روز **فعلی کمتر از 25** باشد یا نه.
* سپس **تاریخ اجرای بعدی** را محاسبه و در **appsettings.json** ذخیره می‌کند.

---

### 🔟 متد CanExecuteApiRequest()

```csharp
private bool CanExecuteApiRequest()
{
    DateTime? nextRunDate = _appSettings.MorningstarNextRunDate;
    if (!nextRunDate.HasValue)
        nextRunDate = SetMorningstarNextRunDate();

    if (DateTime.Now.Day == ApiKeyConstants.ThrottleMonthDay)
    {
        if (nextRunDate.Value.Month == DateTime.Now.Month)
        {
            SetMorningstarNextRunDate();
            return true;
        }
        else
        {
            return false;
        }
    }
    else
    {
        return false;
    }
}
```

* بررسی می‌کند که آیا **API در روز 25 ماه جاری** قابل اجرا است یا خیر.
* اگر شرط‌ها برقرار باشند، **true** برمی‌گردد، در غیر این صورت **false**. ✅

---

### ۱۱️⃣ به‌روزرسانی GetDividendCalendar()

```csharp
[Authorize(Policy = Policies.Internal)]
[HttpGet("internal")]
public IActionResult GetDividendCalendar()
{
    if (CanExecuteApiRequest())
        return new ObjectResult(JsonConvert.SerializeObject(BuildDividendCalendar()));
    else
        return new ObjectResult(ThrottleMessage());
}
```

* اکنون وقتی کاربر داخلی API را فراخوانی می‌کند، **اعتبارسنجی محدودیت** انجام می‌شود.
* اگر شرایط اجرا فراهم باشد، JSON تقویم dividend بازگردانده می‌شود، در غیر این صورت **پیام محدودیت** نمایش داده می‌شود. ⏳

---

### ✅ جمع‌بندی

* پروژه کامل شد، اما **جا برای بهبود و توسعه** وجود دارد.
* مرحله بعدی: **مستندسازی API** و **استقرار آن** همراه با **لاگ‌گیری و مانیتورینگ**.
* **Logging** برای ذخیره جزئیات استثناها و پیگیری استفاده از API مفید است.
* **Monitoring** برای بررسی سلامت API و دریافت هشدار در صورت بروز مشکل کاربرد دارد.

این تمرین می‌تواند تجربه بسیار خوبی برای توسعه و بهبود مهارت‌های شما باشد. 🌟

### 📚 خلاصه فصل ۱۰ – امنیت API با API Key و Azure Key Vault

در این فصل، شما با یک **API شخص ثالث** ثبت‌نام کردید و **کلید API مخصوص خودتان** را دریافت کردید. این کلید در **Azure Key Vault** ذخیره شد تا از دسترسی کاربران غیرمجاز محافظت شود. 🔑

سپس یک برنامه **ASP.NET Core** ایجاد کرده و آن را در **Azure** منتشر کردید. بعد از آن، به **امن‌سازی برنامه وب** با استفاده از **احراز هویت (Authentication)** و **مجوزدهی مبتنی بر نقش (Role-based Authorization)** پرداختید.

---

### 🔒 امنیت و مجوزدهی

* **Authorization** که تنظیم شد، با استفاده از **API Key** انجام می‌شود.

* در این پروژه، از دو **API Key** استفاده شد:

  1. یکی برای **کاربران داخلی**
  2. یکی برای **کاربران خارجی**

* **تست امنیت API و API Key** با **Postman** انجام شد.

  * Postman یک ابزار بسیار کاربردی برای **تست درخواست‌ها و پاسخ‌های HTTP** است. 🛠️

---

### 📆 API Dividend Calendar

* سپس کد **Dividend Calendar API** اضافه شد و **دسترسی داخلی و خارجی** بر اساس **API Key** فعال شد.
* پروژه چندین **فراخوانی API** انجام داد تا **لیستی از شرکت‌هایی که قصد پرداخت سود سهام دارند** ایجاد شود.
* در نهایت، داده‌ها به **JSON** سریالایز شده و به کاربر بازگردانده شدند.
* پروژه طوری محدود شد که **فقط یک بار در ماه اجرا شود**.

---

### 💡 نتیجه

با طی کردن این فصل:

* شما یک **FinTech API** ایجاد کرده‌اید که **هر ماه یک بار اجرا می‌شود**.
* این API **اطلاعات پرداخت سود سهام سال جاری** را ارائه می‌دهد.
* کاربران می‌توانند این داده‌ها را **دسیریالایز کرده** و با **LINQ**، داده‌های مورد نظر خود را استخراج کنند.

---

### 🔧 فصل بعدی

در فصل بعدی، از **PostSharp** برای پیاده‌سازی **برنامه‌نویسی مبتنی بر Aspect (AOP)** استفاده می‌کنیم.
با این فریمورک AOP، یاد می‌گیریم که چگونه **عملکردهای مشترک** مثل **مدیریت استثنا، لاگ‌گیری، امنیت و تراکنش‌ها** را در برنامه‌هایمان مدیریت کنیم.

---

### ❓ سوالات تمرینی

1. چه URLهایی منابع خوبی برای **میزبانی API خود** و **دسترسی به API شخص ثالث** هستند؟
2. دو بخش لازم برای **امن‌سازی API** چیست؟
3. **Claims** چیست و چرا باید از آن‌ها استفاده کنید؟
4. از **Postman** برای چه کاری استفاده می‌کنید؟
5. چرا باید از **Repository Pattern** برای ذخیره داده‌ها استفاده کنید؟

---

### 📖 مطالعه بیشتر

* [Microsoft: Web API Security Guide](https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/individual-accounts-in-web-api) – راهنمای جامع امنیت وب API
* [ASP.NET Membership Database](https://docs.microsoft.com/en-us/aspnet/web-forms/overview/older-versions-security/membership/creating-the-membership-schema-in-sqlserver-vb) – ایجاد پایگاه داده عضویت
* [ISO 10383 MIC](https://www.iso20022.org/10383/iso-10383-market-identifier-codes) – درباره ISO 10383 MIC
* [Adding Key Vault via Visual Studio](https://docs.microsoft.com/en-gb/azure/key-vault/vs-key-vault-addconnected-service)
* [Azure CLI MSI Installer](https://aka.ms/installazurecliwindows)
* [Azure Service-to-Service Authentication](https://docs.microsoft.com/en-us/azure/key-vault/service-to-service-authentication)
* [Azure Free Subscription](https://azure.microsoft.com/en-gb/free/?WT.mc_id=A261C142F)
* [Azure Key Vault Basics](https://docs.microsoft.com/en-us/azure/key-vault/basic-concepts)
* [Creating a .NET Core App in Azure](https://docs.microsoft.com/en-us/azure/app-service/app-service-webget-started-dotnet)
* [Azure App Service Plans Overview](https://docs.microsoft.com/en-gb/azure/app-service/overview-hostingplans)
* [Tutorial: Using Azure Key Vault with Web App](https://docs.microsoft.com/en-us/azure/key-vault/tutorial-net-createvault-azure-web-app)

