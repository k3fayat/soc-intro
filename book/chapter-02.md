# ماژول اول — مبانی عملیات امنیت و نقش SOC

# فصل دوم — ذهنیت و روش تفکر تحلیلگر SOC

## اهداف یادگیری

در پایان این فصل، مخاطب باید بتواند:

- تفاوت بین مشاهده یک Event و تفسیر امنیتی آن را توضیح دهد.
- مفهوم **Evidence** را در تحلیل امنیتی درک کند.
- بین Fact، Observation، Hypothesis و Conclusion تفاوت بگذارد.
- یک Alert را بدون گرفتار شدن در قضاوت زودهنگام تحلیل کند.
- مفهوم **Context** را در Investigation به‌کار بگیرد.
- فعالیت‌های **Normal، Anomalous و Malicious** را از یکدیگر تفکیک کند.
- برای یک رخداد، Hypothesis ایجاد و آن را با شواهد آزمایش کند.
- از **Pivoting** برای حرکت میان منابع مختلف داده استفاده کند.
- خطاهای شناختی رایج در تحلیل امنیتی را شناسایی کند.
- برای یک Alert، Investigation منطقی و قابل دفاع انجام دهد.
- تفاوت بین «آنچه می‌دانیم» و «آنچه حدس می‌زنیم» را در مستندسازی رعایت کند.

---

# 2.1 مقدمه

در فصل قبل با SOC، نقش Tier 1 و چرخه کلی Alert آشنا شدیم.

اکنون باید به مهم‌ترین بخش کار یک تحلیلگر برسیم:

> **نحوه فکر کردن.**

ممکن است دو تحلیلگر دقیقاً یک Alert یکسان دریافت کنند، به همان SIEM دسترسی داشته باشند و همان Logها را ببینند، اما به دو نتیجه متفاوت برسند.

علت همیشه کمبود دانش فنی نیست.

گاهی تفاوت در **روش تحلیل** است.

تحلیلگر ضعیف معمولاً به دنبال یک پاسخ سریع است:

> «این حمله است یا نیست؟»

اما تحلیلگر حرفه‌ای ابتدا سؤال را دقیق‌تر می‌کند:

> «چه چیزی مشاهده شده است؟ چه شواهدی داریم؟ چه چیزهایی هنوز مشخص نیست؟ چه فرضیه‌هایی می‌توانند این رفتار را توضیح دهند و چگونه می‌توانیم آن‌ها را آزمایش کنیم؟»

این تفاوت، پایه **Analytical Thinking** در SOC است.

---

# 2.2 SOC Analyst یک Decision Maker است

تصور اشتباهی وجود دارد که تحلیلگر SOC فقط Logها را می‌خواند.

در واقع بخش مهمی از کار او **تصمیم‌گیری** است.

برای مثال، تحلیلگر ممکن است مجبور شود تصمیم بگیرد:

```text
Close
```

یا:

```text
Continue Investigation
```

یا:

```text
Escalate to Tier 2
```

یا حتی:

```text
Initiate Incident Response
```

هر تصمیم باید بر اساس شواهد و منطق قابل دفاع باشد.

بنابراین می‌توان وظیفه تحلیلی SOC Tier 1 را به شکل زیر نمایش داد:

```text
Raw Data
   ↓
Observation
   ↓
Context
   ↓
Hypothesis
   ↓
Evidence Collection
   ↓
Analysis
   ↓
Assessment
   ↓
Decision
```

این مدل در تمام فصل‌های آینده مورد استفاده قرار خواهد گرفت.

---

# 2.3 Observation چیست؟

**Observation** یعنی چیزی که مستقیماً مشاهده یا ثبت شده است.

مثلاً:

```text
10 failed login attempts
from 10.10.20.15
against user administrator
within 2 minutes
```

این یک Observation است.

اما جمله زیر Observation نیست:

> «سیستم مورد حمله Brute Force قرار گرفته است.»

این یک **Assessment یا Hypothesis** است.

تفاوت ظاهراً کوچک است، اما در Investigation بسیار مهم است.

### Observation

```text
87 failed authentication events
```

### Interpretation

```text
This may indicate brute-force activity.
```

### Conclusion

```text
The account was targeted by a brute-force attack.
```

این سه مرحله نباید بدون دلیل با یکدیگر ترکیب شوند.

---

# 2.4 Fact، Observation، Hypothesis و Conclusion

برای تحلیل حرفه‌ای، چهار مفهوم زیر را از هم جدا کنید.

## Fact

چیزی که بر اساس داده معتبر قابل اثبات است.

مثلاً:

```text
User "ali" successfully authenticated to DC01 at 03:17.
```

## Observation

چیزی که از بررسی داده‌ها مشاهده کرده‌اید.

مثلاً:

```text
14 failed authentications preceded the successful login.
```

## Hypothesis

یک توضیح احتمالی برای مشاهدات.

مثلاً:

> احتمال تلاش برای دسترسی غیرمجاز به حساب وجود دارد.

## Conclusion

نتیجه‌ای که پس از بررسی شواهد به آن رسیده‌اید.

مثلاً:

> شواهد موجود با یک Password Spraying Attack سازگار است.

اما حتی Conclusion نیز باید سطح اطمینان داشته باشد.

برای مثال:

```text
High Confidence
Medium Confidence
Low Confidence
```

---

# 2.5 چرا این تفکیک مهم است؟

فرض کنید تحلیلگر در Case بنویسد:

> «هکر وارد سیستم شده است.»

این جمله بسیار قطعی است.

اما اگر تنها داده موجود این باشد:

```text
Successful Login
```

چنین نتیجه‌ای قابل دفاع نیست.

ممکن است Login کاملاً قانونی بوده باشد.

یک مستندسازی حرفه‌ای‌تر:

> «یک Successful Authentication برای حساب `admin` از آدرس `10.10.20.15` مشاهده شد. با توجه به زمان غیرمعمول و فعالیت‌های بعدی، احتمال استفاده غیرمجاز از حساب در حال بررسی است.»

در جمله دوم:

- Fact مشخص است.
- Observation مشخص است.
- Hypothesis مشخص است.
- Conclusion زودهنگام داده نشده است.

این همان نوع نوشتاری است که در SOC حرفه‌ای ارزش دارد.

---

# 2.6 Evidence چیست؟

**Evidence** یعنی داده یا مدرکی که برای پشتیبانی یا رد یک فرضیه استفاده می‌شود.

نمونه Evidence:

- Authentication Log
- Process Creation Event
- Process Tree
- Network Connection
- DNS Query
- File Hash
- EDR Telemetry
- Firewall Log
- Email Header
- Proxy Log
- User Activity
- Configuration Record

نکته مهم:

> هر داده‌ای که می‌بینیم الزاماً Evidence مفید برای فرضیه فعلی نیست.

فرض کنید Hypothesis ما این است:

> آیا حساب کاربر `ali` مورد سوءاستفاده قرار گرفته است؟

در این حالت:

```text
Windows wallpaper change
```

احتمالاً ارزش محدودی دارد.

اما:

```text
Successful authentication from an unusual host
```

Evidence مرتبط‌تری است.

---

# 2.7 Evidence باید چه ویژگی‌هایی داشته باشد؟

Evidence خوب باید تا حد امکان:

### مرتبط باشد

به سؤال Investigation مربوط باشد.

### قابل اعتماد باشد

منبع آن معتبر باشد.

### قابل بازتولید باشد

تحلیلگر دیگری بتواند همان داده را بررسی کند.

### دارای زمان باشد

Timestamp در Investigation اهمیت بسیار زیادی دارد.

### دارای Context باشد

مثلاً:

```text
Source
Destination
User
Host
Process
Timestamp
```

---

# 2.8 اصل مهم: Evidence قبل از Interpretation

یکی از اشتباهات رایج تحلیلگران تازه‌کار این است که ابتدا نتیجه‌گیری می‌کنند و سپس به دنبال شواهد می‌گردند.

مثلاً:

> «این قطعاً Malware است.»

سپس فقط داده‌هایی را جست‌وجو می‌کنند که این فرض را تأیید کند.

این رفتار خطرناک است.

روش صحیح:

```text
Observation
      ↓
Hypothesis
      ↓
Evidence For
      +
Evidence Against
      ↓
Assessment
```

یعنی علاوه بر شواهد موافق، باید به دنبال **شواهد مخالف** نیز باشید.

---

# 2.9 Confirmation Bias

**Confirmation Bias** یکی از مهم‌ترین خطاهای شناختی در تحلیل امنیتی است.

یعنی فرد تمایل دارد اطلاعاتی را بیشتر ببیند یا جدی بگیرد که فرض اولیه او را تأیید می‌کنند.

مثلاً تحلیلگر از ابتدا فرض کرده:

> «این یک Brute Force است.»

سپس می‌بیند:

```text
100 Failed Logins
```

و نتیجه می‌گیرد:

> «دیدید؟ حمله بود.»

اما اگر بررسی کند و متوجه شود:

```text
Source IP = Vulnerability Scanner
```

و این Scanner متعلق به تیم امنیت باشد، فرض اولیه باید تغییر کند.

تحلیلگر حرفه‌ای نباید عاشق Hypothesis خودش باشد.

Hypothesis باید قابل رد شدن باشد.

---

# 2.10 فرضیه قابل آزمایش

Hypothesis خوب باید بتواند با داده آزمایش شود.

مثلاً:

> «ممکن است حساب Administrator توسط یک Password Spraying Attack هدف قرار گرفته باشد.»

اکنون می‌توان پرسید:

- آیا چند حساب مختلف هدف قرار گرفته‌اند؟
- آیا تعداد Failed Login برای هر حساب کم اما تعداد حساب‌ها زیاد است؟
- Source IP چیست؟
- آیا Authenticationها در بازه زمانی کوتاه رخ داده‌اند؟
- آیا پس از آن Successful Login وجود دارد؟

اگر پاسخ‌ها با الگوی Password Spraying سازگار نباشند، Hypothesis باید رد شود یا تغییر کند.

---

# 2.11 مثال: Brute Force یا کاربر عصبانی؟

فرض کنید:

```text
User: sara
Source IP: 10.10.20.55

Failed Login:
09:01
09:01
09:02
09:02
09:03
...
```

تحلیلگر ممکن است فوراً بگوید:

> Brute Force.

اما بررسی بیشتر نشان می‌دهد:

```text
Source Host = Laptop-Sara
```

و:

```text
Sara forgot her password
```

و:

```text
No successful login from external source
```

در این حالت احتمال حمله کاهش می‌یابد.

حال سناریو را تغییر دهیم:

```text
Source IP = Unknown-Host
Target Users = 37
Failed Attempts = 3 per user
Time = 4 minutes
```

این الگو بسیار متفاوت است.

تعداد کاربران هدف افزایش یافته و تعداد تلاش برای هر کاربر کم است.

این الگو می‌تواند با **Password Spraying** سازگار باشد.

پس:

> تعداد Eventها به‌تنهایی کافی نیست؛ **Pattern** اهمیت دارد.

---

# 2.12 Pattern چیست؟

**Pattern** یعنی الگوی تکرارشونده‌ای که از کنار هم قرار گرفتن چند Event قابل مشاهده است.

مثلاً یک Event:

```text
Failed Login
```

اطلاعات محدودی دارد.

اما:

```text
Failed Login
Failed Login
Failed Login
Successful Login
Remote Login
PowerShell
External Connection
```

یک Pattern ایجاد می‌کند.

تحلیلگر SOC باید بتواند از:

```text
Individual Event
```

به:

```text
Behavioral Pattern
```

حرکت کند.

---

# 2.13 Normal، Anomalous و Malicious

یکی از مهم‌ترین مهارت‌های SOC این است که این سه مفهوم را از هم تفکیک کنیم.

## Normal

رفتاری که با الگوی معمول سیستم، کاربر یا سازمان سازگار است.

مثلاً:

```text
Finance user
09:00
Office workstation
Normal business application
```

---

## Anomalous

رفتاری که با الگوی معمول تفاوت دارد.

مثلاً:

```text
Finance user
03:30
Unusual workstation
```

Anomaly الزاماً به معنای حمله نیست.

ممکن است:

- کاربر شیفت شب داشته باشد.
- کاربر سفر کرده باشد.
- سیستم جدیدی نصب شده باشد.
- Administrator فعالیتی انجام داده باشد.

---

## Malicious

رفتاری که شواهد کافی از Intent یا Activity مخرب ارائه می‌دهد.

مثلاً:

```text
Credential Theft
+
Malicious Process
+
C2 Connection
```

بنابراین:

```text
Anomalous ≠ Malicious
```

این یکی از مهم‌ترین اصول این فصل است.

---

# 2.14 مثال واقعی‌تر

فرض کنید یک کاربر معمولاً:

```text
08:30 → Login
17:00 → Logout
```

اما امروز:

```text
03:12 → Login
```

این Anomaly است.

اما هنوز نمی‌توان گفت Malicious است.

بررسی می‌کنیم:

```text
VPN Login
```

و متوجه می‌شویم کاربر از خانه در حال کار بوده است.

پس:

```text
Initial:
Anomalous

After Context:
Benign
```

این همان کاری است که Triage باید انجام دهد.

---

# 2.15 Baseline چیست؟

برای تشخیص Anomaly باید بدانیم Normal چیست.

این مفهوم را **Baseline** می‌نامیم.

Baseline یعنی الگوی معمول رفتار.

مثلاً:

### User Baseline

```text
Normal Login Time:
08:00 - 18:00

Normal Hosts:
WS-22

Normal Location:
Office

Normal Applications:
Office Apps
ERP
Browser
```

اگر همان کاربر:

```text
03:00
Server-01
PowerShell
```

داشته باشد، انحراف از Baseline مشخص‌تر می‌شود.

---

# 2.16 Baseline برای همه چیز یکسان نیست

Baseline ممکن است برای موارد مختلف تعریف شود:

- User
- Host
- Network
- Application
- Service
- Account
- Process
- Location
- Time

مثلاً یک Domain Controller طبیعی است که:

```text
LDAP
Kerberos
DNS
SMB
```

ترافیک داشته باشد.

اما یک Workstation معمولی ممکن است نباید چنین حجم یا نوعی از ارتباطات را داشته باشد.

پس:

> چیزی که برای یک سیستم Normal است، ممکن است برای سیستم دیگر Anomalous باشد.

---

# 2.17 Contextual Analysis

تحلیل امنیتی بدون Context ناقص است.

فرض کنید:

```text
Process:
powershell.exe
```

این به‌تنهایی اطلاعات زیادی نمی‌دهد.

اکنون Context اضافه کنیم:

```text
User = IT Admin
Host = Admin-WS01
Time = Maintenance Window
Parent = Approved Management Tool
Command = Approved Script
```

احتمال Benign بودن زیاد است.

حالا:

```text
User = Standard User
Host = Finance-PC
Time = 03:15
Parent = WINWORD.EXE
Command = Encoded PowerShell
Network = Unknown Domain
```

Context کاملاً متفاوت است.

---

# 2.18 Entity در Investigation

تحلیلگر باید بتواند موجودیت‌های مختلف را به هم متصل کند.

موجودیت‌های رایج:

```text
User
Host
IP
Domain
Process
File
Hash
URL
Application
Account
```

مثلاً:

```text
User: Ali
     |
     ↓
Host: WS-123
     |
     ↓
Process: powershell.exe
     |
     ↓
Domain: suspicious-example.com
     |
     ↓
IP: 185.x.x.x
```

این ارتباط‌ها به تحلیلگر کمک می‌کنند داستان رخداد را بسازد.

---

# 2.19 Pivoting

**Pivoting** یعنی استفاده از یک یافته برای حرکت به سمت داده مرتبط دیگر.

مثلاً Alert شامل این IP است:

```text
10.10.20.15
```

تحلیلگر آن را بررسی می‌کند و می‌فهمد:

```text
10.10.20.15 = WS-015
```

سپس Pivot می‌کند:

```text
WS-015
```

و Userهای مرتبط را بررسی می‌کند.

بعد:

```text
User → ali
```

سپس:

```text
ali → Authentication Events
```

بعد:

```text
ali → Process Events
```

و سپس:

```text
Process → Network Connections
```

در نتیجه:

```text
IP
 ↓
Host
 ↓
User
 ↓
Process
 ↓
Network
 ↓
Domain
```

این یکی از مهارت‌های اصلی SOC Analyst است.

---

# 2.20 Investigation سؤال‌محور است

یک تحلیلگر تازه‌کار ممکن است SIEM را باز کند و بدون هدف Search کند.

مثلاً:

```text
Search everything from last 24 hours.
```

این روش معمولاً نتیجه خوبی ندارد.

روش بهتر:

> ابتدا سؤال امنیتی بساز، سپس Query را طراحی کن.

مثلاً سؤال:

> «آیا این User در یک ساعت گذشته روی Host دیگری نیز Login کرده است؟»

سپس Query مناسب طراحی می‌شود.

یا:

> «آیا بعد از Login مشکوک، Process غیرمعمولی اجرا شده است؟»

یا:

> «آیا Source IP موردنظر با Hostهای دیگری نیز ارتباط داشته است؟»

این روش:

```text
Question
 ↓
Query
 ↓
Evidence
 ↓
Next Question
```

نام دارد.

---

# 2.21 Investigation Loop

Investigation معمولاً یک مسیر خطی نیست.

بهتر است آن را یک Loop بدانیم:

```text
       ┌───────────────┐
       │  Observation  │
       └───────┬───────┘
               ↓
          Hypothesis
               ↓
        Evidence Search
               ↓
          Interpretation
               ↓
        New Information
               │
               └──────────→ New Question
                              ↓
                         More Evidence
```

ممکن است یک Investigation چندین بار این چرخه را تکرار کند.

این طبیعی است.

---

# 2.22 چه زمانی Investigation را متوقف کنیم؟

یکی از مهارت‌های مهم تحلیلگر این است که بداند چه زمانی Investigation کافی است.

اگر برای هر Alert بخواهیم تمام سیستم سازمان را بررسی کنیم، SOC قادر به مدیریت حجم Alert نخواهد بود.

بنابراین باید **Scope** تعریف شود.

مثلاً:

```text
Question:
Was user Ali's account compromised?
```

ممکن است برای پاسخ اولیه بررسی کنیم:

- Authentication
- Source IP
- Host
- Post-login activity
- Related users
- Related hosts

اگر شواهد کافی برای تصمیم وجود داشته باشد، نیازی نیست کل سازمان را بررسی کنیم.

---

# 2.23 Scope چیست؟

**Scope** یعنی محدوده Investigation.

مثلاً:

```text
User:
Ali

Hosts:
WS-123
SRV-05

Time:
02:00 - 05:00

Indicators:
10.10.20.15
suspicious-domain.com
```

این محدوده باعث می‌شود Investigation کنترل‌شده باقی بماند.

اما اگر شواهد جدید نشان دهد Hostهای بیشتری درگیر هستند، Scope باید گسترش یابد.

---

# 2.24 Attack Narrative

در پایان Investigation، تحلیلگر باید بتواند یک **Attack Narrative** یا روایت فنی رخداد بسازد.

مثلاً:

```text
02:14
Multiple failed authentications against user ali

02:16
Successful authentication from WS-44

02:17
Remote connection to SRV-03

02:18
PowerShell execution observed

02:19
Outbound connection to suspicious domain
```

اکنون می‌توان یک روایت ساخت:

> در ساعت 02:14 تلاش‌های ناموفق متعددی برای احراز هویت حساب `ali` مشاهده شد. در ساعت 02:16 یک Authentication موفق از `WS-44` رخ داد. بلافاصله پس از آن یک Remote Connection به `SRV-03` و سپس اجرای PowerShell مشاهده شد. در ادامه Endpoint با یک Domain ناشناخته ارتباط برقرار کرد.

این بسیار ارزشمندتر از فهرستی از Eventهای پراکنده است.

---

# 2.25 تفاوت Data و Information

**Data** داده خام است.

مثلاً:

```text
10.10.20.15
03:14
4625
Administrator
```

اما **Information** زمانی شکل می‌گیرد که Data در Context قرار گیرد:

> در ساعت 03:14، تعداد زیادی Failed Logon برای حساب Administrator از Workstation داخلی `10.10.20.15` مشاهده شده است.

و **Intelligence** زمانی شکل می‌گیرد که این Information تحلیل شود و برای تصمیم استفاده شود.

مثلاً:

> الگوی مشاهده‌شده با Password Attack سازگار است و با توجه به موفقیت Authentication بعدی، نیاز به Investigation سطح بالاتر دارد.

بنابراین:

```text
Data
 ↓
Information
 ↓
Analysis
 ↓
Intelligence
 ↓
Decision
```

---

# 2.26 خطای شناختی: Anchoring

**Anchoring Bias** یعنی تحلیلگر بیش از حد به اولین اطلاعاتی که دریافت کرده وابسته شود.

مثلاً Alert می‌گوید:

```text
"Brute Force Detected"
```

تحلیلگر از همان ابتدا فرض می‌کند:

> Brute Force.

اما ممکن است Rule به‌درستی Tune نشده باشد.

عنوان Alert نباید جای Investigation را بگیرد.

تحلیلگر باید Alert Name را یک **Signal** بداند، نه Verdict.

---

# 2.27 خطای شناختی: Availability Bias

گاهی تحلیلگر به دلیل اینکه اخیراً حمله خاصی دیده، تصور می‌کند رخداد فعلی نیز همان حمله است.

مثلاً هفته گذشته سازمان با:

```text
Phishing
```

مواجه شده است.

این هفته نیز یک Login مشکوک مشاهده می‌شود و تحلیلگر بدون بررسی کافی فرض می‌کند:

> این هم نتیجه همان Phishing است.

ممکن است درست باشد، اما باید Evidence وجود داشته باشد.

---

# 2.28 خطای شناختی: Automation Bias

تحلیلگر ممکن است بیش از حد به خروجی ابزار اعتماد کند.

مثلاً:

```text
EDR:
Malicious = No
```

این به معنای آن نیست که:

> هیچ مشکل امنیتی وجود ندارد.

همچنین:

```text
EDR:
Malicious = Yes
```

به‌تنهایی به این معنا نیست که Incident به‌طور کامل اثبات شده است.

ابزارها باید به‌عنوان بخشی از Evidence Chain استفاده شوند.

---

# 2.29 اصل مهم: ابزار اشتباه نمی‌کند، اما ابزار هم حقیقت مطلق نیست

یک Detection ممکن است:

- درست طراحی نشده باشد؛
- Context کافی نداشته باشد؛
- داده ناقص دریافت کرده باشد؛
- با شرایط خاص سازمان سازگار نباشد.

بنابراین تحلیلگر باید بین:

```text
Tool Output
```

و:

```text
Analyst Assessment
```

تفاوت قائل شود.

---

# 2.30 Confidence

تحلیلگر باید در موارد مناسب سطح اطمینان خود را مشخص کند.

مثلاً:

### Low Confidence

شواهد محدود است.

```text
Possible suspicious activity.
```

### Medium Confidence

چند شواهد مستقل وجود دارد.

```text
Activity is likely suspicious.
```

### High Confidence

چند منبع مستقل و مرتبط یکدیگر را تأیید می‌کنند.

```text
Evidence strongly supports account compromise.
```

Confidence جایگزین Evidence نیست.

بلکه بیانگر میزان اطمینان تحلیلگر به Assessment است.

---

# 2.31 Chain of Evidence

در Investigation باید بتوانیم ارتباط بین شواهد را توضیح دهیم.

مثلاً:

```text
Evidence 1:
Failed Login

       ↓

Evidence 2:
Successful Login

       ↓

Evidence 3:
New Remote Session

       ↓

Evidence 4:
Suspicious Process

       ↓

Evidence 5:
External Connection
```

هیچ‌کدام از این Eventها به‌تنهایی ممکن است قطعی نباشند.

اما مجموع آن‌ها می‌تواند یک Pattern بسیار قوی ایجاد کند.

---

# 2.32 استقلال منابع داده

Confidence زمانی افزایش می‌یابد که Evidence از منابع مستقل مختلف به دست آید.

مثلاً:

```text
SIEM
+
EDR
+
Firewall
```

همگی یک Activity را نشان دهند.

در این حالت احتمال اینکه یک خطای واحد باعث نتیجه‌گیری شده باشد کاهش پیدا می‌کند.

برای مثال:

```text
SIEM:
PowerShell executed

EDR:
WINWORD.EXE → powershell.exe

DNS:
Suspicious domain queried

Firewall:
Outbound connection established
```

این چهار داده در کنار یکدیگر بسیار قدرتمندتر از یک Event منفرد هستند.

---

# 2.33 یک سناریوی کامل تحلیلی

فرض کنید Alert زیر را دریافت کرده‌ایم:

```text
Alert:
Suspicious PowerShell Execution

Host:
FIN-WS-22

User:
mary

Time:
02:41

Process:
powershell.exe
```

## مرحله 1 — Observation

آنچه می‌دانیم:

```text
PowerShell executed
on FIN-WS-22
by user mary
at 02:41
```

هنوز نمی‌دانیم مخرب است یا خیر.

---

## مرحله 2 — Hypothesis

فرضیه:

> PowerShell ممکن است بخشی از یک فعالیت مخرب باشد.

---

## مرحله 3 — Context

بررسی می‌کنیم:

```text
Host Role = Finance Workstation
User Role = Finance Employee
Time = 02:41
```

این موارد Suspicion را افزایش می‌دهند، اما کافی نیستند.

---

## مرحله 4 — Parent Process

می‌بینیم:

```text
WINWORD.EXE
   ↓
powershell.exe
```

Suspicion افزایش می‌یابد.

---

## مرحله 5 — Command Line

فرض کنیم:

```text
powershell.exe -EncodedCommand ...
```

اکنون Evidence قوی‌تر است.

---

## مرحله 6 — Network

بررسی می‌کنیم:

```text
FIN-WS-22
   ↓
suspicious-example.com
```

---

## مرحله 7 — DNS

می‌بینیم:

```text
02:41:12
DNS Query:
suspicious-example.com
```

---

## مرحله 8 — Assessment

اکنون چند منبع داده یکدیگر را تقویت می‌کنند:

```text
PowerShell
+
Word Parent Process
+
Encoded Command
+
Suspicious Domain
+
Unusual Time
```

در این مرحله:

```text
Assessment:
Highly Suspicious
```

می‌تواند منطقی باشد.

---

## مرحله 9 — Escalation

Tier 1 می‌تواند موارد زیر را به Tier 2 منتقل کند:

```text
Host
User
Timestamp
Process Tree
Command Line
Domain
Destination IP
Related Events
Initial Assessment
Collected Evidence
Recommended Next Steps
```

---

# 2.34 تمرین عملی 1 — Observation یا Conclusion؟

برای هر جمله مشخص کنید:

- Fact
- Observation
- Hypothesis
- Conclusion

### A

> حساب `administrator` در ساعت 03:12 یک Login موفق داشته است.

### B

> احتمال استفاده غیرمجاز از حساب وجود دارد.

### C

> حساب Administrator مورد سوءاستفاده قرار گرفته است.

### D

> پیش از Login موفق، 25 Failed Authentication از همان Source IP ثبت شده است.

هدف این تمرین، تشخیص تفاوت بین داده و تفسیر است.

---

# 2.35 تمرین عملی 2 — تشخیص Normal، Anomalous و Malicious

سناریوها را بررسی کنید.

### سناریو A

```text
User: IT-Admin
Time: 02:00
Host: Admin-WS
Activity: PowerShell
Reason: Scheduled maintenance
```

### سناریو B

```text
User: Finance
Time: 02:00
Host: Finance-PC
Activity: PowerShell
Parent: Word
Command: Encoded
```

### سناریو C

```text
User: Finance
Time: 02:00
Host: Finance-PC
Activity: PowerShell
Parent: Word
Command: Encoded
Network: Unknown external domain
EDR: Suspicious behavior detected
```

برای هر سناریو مشخص کنید:

1. Normal
2. Anomalous
3. Suspicious
4. Malicious

و مهم‌تر از آن، **دلیل تصمیم خود را بنویسید.**

---

# 2.36 تمرین عملی 3 — ساخت Hypothesis

Alert:

```text
Multiple Failed Logins

Source:
10.10.40.22

Users:
admin
user1
user2
user3
user4

Time:
10:00 - 10:04
```

حداقل سه Hypothesis بنویسید.

مثلاً یکی می‌تواند:

> Password Spraying

باشد.

اما دو Hypothesis دیگر را خودتان ایجاد کنید.

سپس برای هر Hypothesis حداقل سه Evidence مورد نیاز برای تأیید یا رد آن را بنویسید.

---

# 2.37 تمرین عملی 4 — Pivoting

داده اولیه:

```text
Source IP:
10.10.20.50
```

اطلاعات بعدی:

```text
Host:
WS-050
```

سپس:

```text
User:
reza
```

سپس:

```text
Process:
powershell.exe
```

سپس:

```text
Destination:
185.x.x.x
```

اکنون یک Investigation Tree ایجاد کنید:

```text
10.10.20.50
      ↓
   WS-050
      ↓
    Reza
      ↓
PowerShell
      ↓
185.x.x.x
```

برای هر Pivot مشخص کنید چه سؤالی باید پرسیده شود.

---

# 2.38 تمرین عملی 5 — تحلیل یک Case

سناریو:

```text
Alert:
Suspicious Login

User:
ali

Source:
10.10.50.77

Target:
DC01

Time:
03:17
```

اطلاعات اضافی:

```text
03:12 - 14 Failed Logins
03:17 - Successful Login
03:18 - Remote Login to SRV-FS01
03:19 - PowerShell Execution
03:20 - External DNS Query
```

### وظیفه

یک گزارش اولیه تهیه کنید که شامل موارد زیر باشد:

1. Observations
2. Hypothesis
3. Evidence
4. Assessment
5. Confidence
6. Scope
7. Recommended Next Action

توجه کنید که در این تمرین نباید فقط بنویسید:

> «این حمله است.»

باید نشان دهید **چرا** به این نتیجه رسیده‌اید.

---

# 2.39 تمرین آزمایشگاهی فصل

در این مرحله آزمایشگاه هنوز بسیار ساده است.

روی سیستم Windows آزمایشگاهی چند فعالیت عادی ایجاد کنید:

```text
Login
Logout
Open Application
Create File
Delete File
Run PowerShell
Run Command Prompt
Access Network Resource
```

سپس فعالیت‌هایی با الگوی متفاوت ایجاد کنید.

هدف این نیست که در این مرحله حمله واقعی اجرا کنید.

هدف این است که یاد بگیرید:

> «اگر من تحلیلگر SOC باشم، چه داده‌ای از یک فعالیت روی سیستم باقی می‌ماند؟»

برای هر فعالیت موارد زیر را ثبت کنید:

```text
Activity
Timestamp
User
Host
Process
Expected Log
Actual Log
Security Relevance
```

---

# 2.40 تمرین تحلیل بدون راهنما

در پایان فصل، سناریوی زیر را بدون راهنمایی تحلیل کنید.

```text
Alert:
Unusual Authentication Activity

User:
admin

Source IP:
10.10.20.25

Time:
02:10 - 02:20

Events:
31 Failed Logins
1 Successful Login

After Success:
Remote Session
PowerShell
DNS Query
```

اطلاعات بیشتری در اختیار شما قرار نمی‌گیرد.

به‌عنوان SOC Tier 1 Analyst باید مشخص کنید:

- اولین سؤال شما چیست؟
- چه داده‌هایی نیاز دارید؟
- چه Hypothesisهایی دارید؟
- چه شواهدی Hypothesis را تأیید می‌کند؟
- چه شواهدی آن را رد می‌کند؟
- Scope اولیه چیست؟
- چه زمانی Escalate می‌کنید؟
- چه اطلاعاتی باید در Case ثبت شود؟

---

# 2.41 Checklist مهارت فصل

پیش از عبور از این فصل، باید بتوانید موارد زیر را بدون مراجعه به متن توضیح دهید:

- [ ] تفاوت Event، Alert و Incident
- [ ] تفاوت Fact و Hypothesis
- [ ] مفهوم Evidence
- [ ] مفهوم Context
- [ ] تفاوت Normal و Anomalous
- [ ] تفاوت Anomalous و Malicious
- [ ] مفهوم Baseline
- [ ] مفهوم Pivoting
- [ ] مفهوم Investigation Loop
- [ ] مفهوم Scope
- [ ] مفهوم Confidence
- [ ] Confirmation Bias
- [ ] Anchoring Bias
- [ ] Automation Bias
- [ ] ساخت Attack Narrative

---

# 2.42 جمع‌بندی فصل

در این فصل از «ابزار» فاصله گرفتیم و روی مهم‌ترین ابزار یک SOC Analyst تمرکز کردیم:

> **ذهن تحلیلگر.**

تحلیلگر حرفه‌ای با دیدن Alert فوراً نتیجه‌گیری نمی‌کند.

او ابتدا Observation را از Interpretation جدا می‌کند، سپس Hypothesis می‌سازد و برای تأیید یا رد آن Evidence جمع‌آوری می‌کند.

فرآیند ذهنی او تقریباً چنین است:

```text
Alert
 ↓
What do I actually know?
 ↓
What am I assuming?
 ↓
What is missing?
 ↓
What hypotheses explain this?
 ↓
What evidence can prove/disprove them?
 ↓
What does the evidence tell me?
 ↓
What is the appropriate action?
```

سه اصل این فصل را باید به‌عنوان اصول پایه تحلیل SOC در ذهن نگه داشت:

### اصل اول

> **An Alert is not a Verdict.**

Alert نتیجه نهایی نیست.

### اصل دوم

> **Anomaly is not automatically Malicious.**

هر رفتار غیرعادی الزاماً مخرب نیست.

### اصل سوم

> **Good Analysis is Evidence-Driven.**

تحلیل خوب باید بر شواهد قابل بررسی تکیه کند.

در فصل بعد وارد بخش بسیار مهمی می‌شویم: **ساخت آزمایشگاه عملی SOC**. از آنجا به بعد، آموزش از حالت صرفاً مفهومی وارد محیط عملی می‌شود و آزمایشگاه مجازی را مرحله‌به‌مرحله می‌سازیم؛ به‌گونه‌ای که هر جزء آزمایشگاه دقیقاً در زمان موردنیاز به آن اضافه شود.