# ماژول اول — مبانی عملیات امنیت و نقش SOC

# فصل اول — آشنایی با Security Operations

## اهداف یادگیری

در پایان این فصل، انتظار می‌رود مخاطب بتواند:

- مفهوم **Security Operations** را به‌صورت عملیاتی توضیح دهد.
- نقش **SOC (Security Operations Center)** را در معماری امنیت سازمان درک کند.
- اجزای اصلی یک SOC را شناسایی کند.
- تفاوت نقش‌های **Tier 1، Tier 2 و Tier 3** را توضیح دهد.
- تفاوت SOC با NOC، CSIRT و تیم Incident Response را تشخیص دهد.
- جریان یک Alert از لحظه تولید تا Closure یا Escalation را توضیح دهد.
- وظایف روزانه یک SOC Tier 1 Analyst را بشناسد.
- با مفهوم **Handover** و اهمیت آن در محیط عملیاتی SOC آشنا شود.
- شاخص‌های پایه عملکرد SOC را بشناسد.
- اولین مدل ذهنی صحیح برای کار در یک SOC ایجاد کند.

---

# 1.1 مقدمه

امنیت سایبری در یک سازمان را نمی‌توان تنها با نصب Firewall، Antivirus یا یک محصول امنیتی تأمین کرد.

هر سازمانی که از شبکه، سیستم‌عامل، سرویس‌های ابری، حساب‌های کاربری، نرم‌افزارهای سازمانی و تجهیزات مختلف استفاده می‌کند، دائماً در حال تولید داده‌های امنیتی است.

برای مثال:

- یک کاربر وارد سیستم می‌شود.
- یک کاربر رمز عبور اشتباه وارد می‌کند.
- یک Administrator یک حساب جدید ایجاد می‌کند.
- یک Process روی یک سیستم اجرا می‌شود.
- یک سیستم به یک IP خارجی متصل می‌شود.
- یک DNS Query به یک Domain ارسال می‌شود.
- یک Firewall یک Connection را Block می‌کند.
- یک Endpoint یک فایل مشکوک را شناسایی می‌کند.
- یک کاربر از یک سیستم جدید Login می‌کند.

هرکدام از این فعالیت‌ها ممکن است یک **Event** ایجاد کنند.

اما یک Event لزوماً یک رخداد امنیتی نیست.

برای مثال:

```text
User: ali
Host: PC-023
Event: Successful Logon
Time: 09:12
Source IP: 10.10.20.45
```

این Event به‌تنهایی احتمالاً کاملاً عادی است.

اما اگر همان کاربر در ساعت 03:17 صبح از یک سیستم ناشناخته در یک Subnet غیرمعمول Login کند، شرایط متفاوت می‌شود:

```text
User: ali
Host: SRV-DC01
Event: Successful Logon
Time: 03:17
Source IP: 10.10.50.77
```

اکنون باید سؤال‌هایی مطرح شوند:

- آیا علی در این ساعت مشغول به کار بوده است؟
- آیا این حساب یک حساب عادی است؟
- آیا `10.10.50.77` یک سیستم مجاز است؟
- آیا این Login با فعالیت‌های دیگری همراه بوده است؟
- آیا Login قبل از آن چندین Failed Logon وجود داشته؟
- بعد از Login چه Processهایی اجرا شده‌اند؟
- آیا همان حساب روی سیستم دیگری نیز Login کرده است؟

اینجاست که مفهوم **Security Operations** اهمیت پیدا می‌کند.

Security Operations یعنی تبدیل داده‌های امنیتی پراکنده به **اطلاعات قابل تحلیل و تصمیم عملیاتی**.

---

# 1.2 Security Operations چیست؟

**Security Operations** یا عملیات امنیت، مجموعه‌ای از فرآیندها، افراد، فناوری‌ها و فعالیت‌هایی است که برای **پایش، شناسایی، تحلیل و مدیریت تهدیدها و رخدادهای امنیتی** در یک سازمان انجام می‌شوند.

به‌صورت ساده:

> Security Operations یعنی سازمان به‌صورت مستمر وضعیت امنیتی محیط خود را مشاهده کند، فعالیت‌های مشکوک را شناسایی کند و در برابر آن‌ها واکنش مناسب نشان دهد.

چهار مفهوم در این تعریف بسیار مهم هستند:

### Monitoring — پایش

یعنی مشاهده مداوم فعالیت‌های محیط.

### Detection — تشخیص

یعنی شناسایی فعالیتی که ممکن است نشان‌دهنده یک تهدید باشد.

### Investigation — بررسی

یعنی جمع‌آوری و تحلیل شواهد برای مشخص کردن اینکه واقعاً چه اتفاقی افتاده است.

### Response — واکنش

یعنی انجام اقدام مناسب برای کنترل یا مدیریت رخداد.

بنابراین Security Operations فقط Monitoring نیست.

اگر یک سازمان میلیون‌ها Log جمع‌آوری کند ولی کسی آن‌ها را تحلیل نکند، عملاً Security Operations مؤثری ایجاد نشده است.

---

# 1.3 چرا Security Operations ضروری است؟

فرض کنیم یک سازمان دارای تجهیزات زیر است:

```text
500 Workstation
50 Server
20 Network Device
2 Firewall
1 Email Gateway
1 VPN Gateway
```

هرکدام از این سیستم‌ها می‌توانند تعداد زیادی Event تولید کنند.

حتی یک سیستم ساده Windows ممکن است در طول روز هزاران Event تولید کند.

اگر همه این Eventها بدون دسته‌بندی در اختیار یک انسان قرار گیرند، بررسی تک‌تک آن‌ها غیرممکن است.

بنابراین سازمان نیاز دارد:

```text
Raw Events
     ↓
Collection
     ↓
Normalization
     ↓
Detection
     ↓
Alert
     ↓
Triage
     ↓
Investigation
     ↓
Decision
     ↓
Response / Escalation
```

این زنجیره، یکی از مهم‌ترین مدل‌های ذهنی این دوره است.

تحلیلگر SOC باید بتواند در هر مرحله بداند:

> داده از کجا آمده، اکنون در چه مرحله‌ای قرار دارد و تصمیم بعدی چیست؟

---

# 1.4 SOC چیست؟

**SOC** مخفف:

> Security Operations Center

به معنی:

> مرکز عملیات امنیت

است.

SOC یک واحد سازمانی و عملیاتی است که وظیفه آن پایش و مدیریت وضعیت امنیتی سازمان است.

یک اشتباه رایج این است که SOC را صرفاً یک اتاق پر از مانیتور تصور کنیم.

در واقع SOC ترکیبی از موارد زیر است:

```text
People
+
Process
+
Technology
+
Data
```

یا به شکل مفهومی:

```text
                SOC
                 |
       ┌─────────┼─────────┐
       │         │         │
     People    Process  Technology
                           |
                         Data
```

اگر یکی از این اجزا به‌درستی طراحی نشده باشد، SOC ضعیف خواهد بود.

---

# 1.5 اجزای اصلی SOC

## 1.5.1 People

افراد مهم‌ترین جزء SOC هستند.

نمونه نقش‌ها:

- SOC Analyst
- Incident Responder
- Threat Hunter
- Detection Engineer
- SOC Manager
- Security Engineer
- Threat Intelligence Analyst

در این دوره تمرکز اصلی ما روی نقش:

> **SOC Tier 1 Analyst**

خواهد بود.

---

## 1.5.2 Process

هیچ SOC حرفه‌ای صرفاً با ابزار کار نمی‌کند.

برای مثال اگر Alert زیر ایجاد شود:

```text
Possible Brute Force Attack
Source IP: 10.10.10.45
Target: DC01
Failed Attempts: 183
```

تحلیلگر باید بداند:

1. چه اطلاعاتی بررسی شود؟
2. چه زمانی Alert مهم تلقی شود؟
3. چه کسی مسئول بررسی باشد؟
4. چه زمانی Escalate شود؟
5. چه اطلاعاتی در Case ثبت شود؟
6. چه زمانی Incident بسته شود؟

این دستورالعمل‌ها بخشی از **Process** هستند.

---

## 1.5.3 Technology

SOC از مجموعه‌ای از فناوری‌ها استفاده می‌کند.

برای مثال:

- SIEM
- EDR
- NDR
- Firewall
- IDS/IPS
- Email Security
- Vulnerability Management
- Threat Intelligence Platform
- SOAR
- Ticketing / Case Management

هر ابزار مسئله خاصی را حل می‌کند.

یک نکته مهم:

> تحلیلگر SOC نباید ابزارمحور باشد؛ باید مسئله‌محور باشد.

یعنی ابتدا باید بداند چه سؤالی دارد و سپس مشخص کند کدام ابزار یا Data Source می‌تواند پاسخ آن سؤال را فراهم کند.

---

# 1.6 SIEM چیست؟

یکی از مهم‌ترین فناوری‌های مورد استفاده در SOC، **SIEM** است.

SIEM مخفف:

> Security Information and Event Management

است.

SIEM معمولاً وظایفی مانند موارد زیر را انجام می‌دهد:

- جمع‌آوری Log
- ذخیره‌سازی داده
- Parse کردن داده
- Normalization
- Search
- Correlation
- Detection
- Alerting
- Dashboarding

برای مثال، یک سازمان ممکن است Logهای زیر را به SIEM ارسال کند:

```text
Windows
Linux
Firewall
VPN
DNS
Proxy
EDR
Active Directory
Email Gateway
Cloud Services
```

SIEM این داده‌ها را در یک محیط قابل جست‌وجو در اختیار تیم امنیت قرار می‌دهد.

اما باید به یک نکته مهم توجه کرد:

> SIEM خودش تحلیلگر SOC نیست.

SIEM می‌تواند یک Alert ایجاد کند، اما انسان باید Context آن را بررسی کند.

---

# 1.7 Alert چیست؟

**Alert** پیامی است که نشان می‌دهد یک Rule، Detection یا سیستم تشخیص، فعالیتی را قابل توجه یا مشکوک تشخیص داده است.

مثلاً:

```text
ALERT

Name:
Multiple Failed Logins

Source:
10.10.20.15

Target:
DC01

User:
administrator

Attempts:
250

Time Window:
5 minutes
```

اما این Alert هنوز Incident قطعی نیست.

تحلیلگر باید آن را بررسی کند.

این فرآیند:

> **Alert Triage**

نام دارد.

---

# 1.8 Triage چیست؟

**Triage** یعنی ارزیابی و اولویت‌بندی اولیه یک Alert یا رخداد.

تحلیلگر Tier 1 معمولاً ابتدا می‌خواهد به چند سؤال پاسخ دهد:

### سؤال اول: چه اتفاقی افتاده است؟

مثلاً:

> تعداد زیادی Failed Login مشاهده شده است.

### سؤال دوم: چه کسی درگیر است؟

```text
User = administrator
```

### سؤال سوم: کجا رخ داده است؟

```text
Target = DC01
```

### سؤال چهارم: از کجا آمده است؟

```text
Source = 10.10.20.15
```

### سؤال پنجم: چه زمانی اتفاق افتاده است؟

```text
14:02 - 14:07
```

### سؤال ششم: آیا این رفتار عادی است؟

مثلاً ممکن است IP متعلق به یک Vulnerability Scanner سازمان باشد.

در این حالت:

```text
Many Login Attempts
```

ممکن است مخرب نباشد.

اما اگر IP متعلق به یک Workstation معمولی باشد، وضعیت متفاوت است.

---

# 1.9 Event، Alert و Incident

این سه اصطلاح باید از همین ابتدا کاملاً از یکدیگر تفکیک شوند.

## Event

یک فعالیت ثبت‌شده در سیستم.

مثلاً:

```text
User logged in successfully.
```

## Alert

سیستم تشخیص داده که یک یا چند Event ممکن است اهمیت امنیتی داشته باشند.

مثلاً:

```text
20 Failed Logins from the same IP.
```

## Incident

پس از بررسی مشخص شده که یک رخداد امنیتی واقعی یا محتمل با اهمیت عملیاتی وجود دارد.

مثلاً:

```text
Compromised user account
```

رابطه ساده:

```text
Event
  ↓
Detection
  ↓
Alert
  ↓
Triage
  ↓
Investigation
  ↓
Incident
```

اما این مسیر همیشه به Incident ختم نمی‌شود.

ممکن است:

```text
Alert
  ↓
Triage
  ↓
False Positive
  ↓
Close
```

یا:

```text
Alert
  ↓
Triage
  ↓
Benign Activity
  ↓
Close
```

این تفاوت برای تحلیلگر Tier 1 حیاتی است.

---

# 1.10 False Positive

**False Positive** یعنی سیستم یک فعالیت را به‌عنوان تهدید تشخیص داده، اما بررسی نشان می‌دهد تهدید واقعی وجود ندارد.

مثلاً Detection زیر را در نظر بگیرید:

```text
Alert:
Multiple Port Scanning Attempts
```

تحلیلگر بررسی می‌کند و متوجه می‌شود Source IP متعلق به تیم IT و سیستم موردنظر یک Vulnerability Scanner مجاز است.

پس:

```text
Detection = Triggered
Threat = No
```

این یک False Positive است.

اما False Positive لزوماً به معنای «بد بودن Detection» نیست.

Detection ممکن است از نظر منطقی درست عمل کرده باشد، اما Context سازمانی را نداند.

---

# 1.11 True Positive

**True Positive** زمانی است که Detection فعالیتی را مشکوک یا مخرب تشخیص دهد و Investigation نیز این موضوع را تأیید کند.

مثلاً:

```text
Alert:
Suspicious PowerShell

↓
Investigation

PowerShell spawned by Word
↓
Encoded Command
↓
External Network Connection
↓
Suspicious Domain
```

در چنین شرایطی احتمال یک حمله واقعی افزایش می‌یابد.

---

# 1.12 Benign Positive

یک مفهوم دیگر **Benign Positive** است.

در این حالت Alert واقعاً با فعالیتی منطبق است که Detection برای آن طراحی شده، اما فعالیت **مجاز و غیرمخرب** است.

مثلاً:

```text
Alert:
PowerShell Execution
```

تحلیلگر بررسی می‌کند:

```text
User = IT_Admin
Host = Admin-WS01
Time = Approved Maintenance Window
Command = Approved Maintenance Script
```

Detection درست عمل کرده است.

PowerShell واقعاً اجرا شده است.

اما فعالیت مخرب نیست.

پس:

```text
Detection = Correct
Activity = Benign
```

این وضعیت با False Positive از نظر مفهومی تفاوت دارد و تحلیلگر باید این تفاوت را در ذهن داشته باشد.

---

# 1.13 ساختار تیم SOC

ساختار SOC در سازمان‌های مختلف متفاوت است، اما یک مدل رایج به شکل زیر است:

```text
                    SOC Manager
                         |
          ┌──────────────┼──────────────┐
          │              │              │
       Tier 1          Tier 2         Tier 3
       Analyst         Analyst        Analyst
          │              │              │
      Monitoring    Investigation    Advanced
       & Triage      & Response      Analysis
```

البته در سازمان‌های کوچک ممکن است یک نفر چند نقش را همزمان انجام دهد.

---

# 1.14 SOC Tier 1 Analyst

Tier 1 معمولاً اولین سطح تحلیل عملیاتی است.

وظایف رایج عبارت‌اند از:

- Monitoring
- دریافت Alert
- Triage
- بررسی اولیه
- جمع‌آوری Context
- بررسی IOC
- جست‌وجوی Logهای مرتبط
- تعیین اولیه Severity
- False Positive Identification
- ایجاد و به‌روزرسانی Case
- Escalation
- Handover

Tier 1 معمولاً قرار نیست تمام Incidentهای پیچیده را به‌تنهایی حل کند.

اما باید بتواند **اطلاعات کافی و باکیفیت برای تحلیل سطح بالاتر فراهم کند.**

این نکته بسیار مهم است.

یک Escalation ضعیف:

```text
"User may be hacked. Please investigate."
```

ارزش محدودی دارد.

اما یک Escalation حرفه‌ای می‌تواند چنین ساختاری داشته باشد:

```text
Incident:
Suspected Account Compromise

User:
ali

Source IP:
10.10.50.77

Target:
DC01

First observed:
03:17 UTC

Authentication:
Successful

Previous activity:
14 failed logins within 3 minutes

Post-authentication activity:
Remote logon to SRV-FS01

Relevant indicators:
10.10.50.77
User: ali

Assessment:
Suspicious / High Confidence

Reason:
Successful authentication followed by anomalous
remote activity after multiple failed attempts.

Recommended escalation:
Tier 2 Investigation
```

این تفاوت، تفاوت بین «گزارش یک هشدار» و «تحلیل امنیتی» است.

---

# 1.15 Tier 2

Tier 2 معمولاً مسئول Investigation عمیق‌تر است.

نمونه فعالیت‌ها:

- Incident Investigation
- Advanced Log Analysis
- Endpoint Investigation
- Timeline Reconstruction
- Correlation
- Malware Investigation مقدماتی
- تعیین Scope
- بررسی Lateral Movement
- تحلیل تکنیک‌های مهاجم
- هماهنگی با Incident Response

Tier 2 معمولاً Alert را از Tier 1 دریافت می‌کند، اما این به معنای جدایی کامل این دو سطح نیست.

در برخی سازمان‌ها Tier 1 و Tier 2 مرز کاملاً مشخصی ندارند.

---

# 1.16 Tier 3

Tier 3 معمولاً با پیچیده‌ترین مسائل سروکار دارد.

نمونه فعالیت‌ها:

- Advanced Threat Hunting
- Detection Engineering
- Malware Analysis
- Advanced Incident Response
- Reverse Engineering
- Forensics
- Advanced Threat Research

Tier 3 ممکن است برای ایجاد Detection جدید یا تحلیل یک حمله پیچیده وارد عمل شود.

---

# 1.17 SOC در مقابل NOC

**NOC** مخفف:

> Network Operations Center

است.

هدف اصلی NOC معمولاً حفظ Availability و عملکرد صحیح زیرساخت شبکه و سرویس‌هاست.

در مقابل، SOC تمرکز امنیتی دارد.

مثلاً:

```text
Router CPU = 95%
```

ممکن است مسئله اصلی NOC باشد.

اما:

```text
Large outbound traffic from internal server
```

می‌تواند موضوع SOC باشد.

البته این مرز مطلق نیست.

یک Incident امنیتی ممکن است باعث مشکل Availability شود و در نتیجه SOC و NOC باید با یکدیگر همکاری کنند.

---

# 1.18 SOC در مقابل CSIRT

**CSIRT** مخفف:

> Computer Security Incident Response Team

است.

CSIRT بیشتر روی **Incident Response** تمرکز دارد.

SOC معمولاً به‌صورت مداوم Monitoring و Detection انجام می‌دهد.

یک مدل ساده:

```text
SOC
 |
 | Detect
 | Triage
 | Investigate
 |
 ↓
Potential Incident
 |
 ↓
CSIRT / Incident Response
 |
 ↓
Containment
Eradication
Recovery
```

در عمل، ساختار سازمان‌ها متفاوت است و ممکن است SOC و CSIRT در یک واحد ادغام شده باشند.

---

# 1.19 چرخه کاری یک Alert

یکی از مهم‌ترین مهارت‌های تحلیلگر Tier 1، شناخت چرخه عمر Alert است.

یک مدل ساده:

```text
        Alert Generated
               |
               ↓
           Assignment
               |
               ↓
             Triage
               |
       ┌───────┴────────┐
       ↓                ↓
    Benign          Suspicious
       |                |
       ↓                ↓
     Close          Investigation
                         |
                  ┌──────┴──────┐
                  ↓             ↓
              Escalate        Close
                  |
                  ↓
             Tier 2 / IR
```

این فرآیند در هر سازمان ممکن است با ابزار و اصطلاحات متفاوت اجرا شود، اما منطق کلی مشابه است.

---

# 1.20 Alert Assignment

در SOC ممکن است روزانه تعداد زیادی Alert ایجاد شود.

بنابراین Alert باید به تحلیلگر یا Queue مناسب اختصاص پیدا کند.

برای مثال:

```text
Queue: Authentication
Queue: Endpoint
Queue: Network
Queue: Email
Queue: Cloud
```

یا ممکن است همه Alertها در یک Queue قرار گیرند و براساس Severity اولویت‌بندی شوند.

تحلیلگر باید قبل از شروع Investigation بداند:

- Alert به چه کسی اختصاص داده شده؟
- SLA آن چیست؟
- Priority چیست؟
- آیا Incident مشابهی قبلاً باز شده است؟
- آیا Alert بخشی از یک Incident بزرگ‌تر است؟

---

# 1.21 SLA در SOC

**SLA** مخفف:

> Service Level Agreement

است.

در SOC ممکن است برای Alertهای مختلف زمان پاسخ متفاوت تعیین شود.

مثلاً:

```text
Critical:
Initial response < 15 min

High:
Initial response < 30 min

Medium:
Initial response < 2 hours

Low:
Initial response < 8 hours
```

مقادیر واقعی کاملاً وابسته به سازمان هستند.

تحلیلگر باید SLA را به‌عنوان بخشی از فرآیند عملیاتی بشناسد.

یک Alert ممکن است از نظر فنی جالب باشد، اما اگر Critical باشد، نمی‌توان آن را ساعت‌ها بدون بررسی رها کرد.

---

# 1.22 Severity، Priority و Risk

این سه مفهوم نباید با یکدیگر یکی فرض شوند.

## Severity

شدت بالقوه رخداد.

مثلاً:

```text
Critical
High
Medium
Low
```

## Priority

اولویت انجام کار.

Priority می‌تواند علاوه بر Severity به عوامل دیگری وابسته باشد.

## Risk

میزان ریسک کلی که از ترکیب احتمال و Impact حاصل می‌شود.

برای مثال:

```text
Alert:
Multiple Failed Logins

Severity:
Medium
```

اما اگر Target:

```text
Domain Controller
```

باشد، ممکن است Priority افزایش پیدا کند.

یا اگر حساب هدف:

```text
Domain Administrator
```

باشد، اهمیت آن بسیار بیشتر می‌شود.

بنابراین:

> Severity یک Alert به‌تنهایی برای تصمیم‌گیری کافی نیست.

---

# 1.23 Context؛ تفاوت تحلیلگر خوب و ضعیف

فرض کنید Alert زیر ایجاد شده است:

```text
Outbound Connection
Destination IP: 185.x.x.x
Host: SRV01
Port: 443
```

آیا این حمله است؟

نمی‌دانیم.

اگر بدانیم:

```text
SRV01 = Web Server
Destination = Approved CDN
```

احتمالاً فعالیت عادی است.

اما اگر:

```text
SRV01 = Domain Controller
Destination = Unknown Internet IP
Connection = Repeated every 60 seconds
```

اهمیت بسیار بیشتری پیدا می‌کند.

بنابراین یک Alert بدون Context ارزش تحلیلی محدودی دارد.

Context می‌تواند شامل موارد زیر باشد:

- Asset
- User
- IP
- Hostname
- Role
- Time
- Location
- Process
- Previous Activity
- Related Events
- Threat Intelligence
- Business Importance

---

# 1.24 Asset Criticality

همه سیستم‌ها ارزش یکسانی ندارند.

برای مثال:

```text
Employee Laptop
```

با:

```text
Domain Controller
```

از نظر امنیتی یکسان نیست.

همچنین:

```text
Test Server
```

با:

```text
Payment Database
```

اهمیت یکسانی ندارد.

بنابراین SOC باید از **Asset Criticality** آگاه باشد.

در یک Investigation، تحلیلگر باید بپرسد:

> این سیستم چه نقشی در سازمان دارد؟

این سؤال می‌تواند Priority یک Incident را به‌شدت تغییر دهد.

---

# 1.25 Handover

SOC معمولاً به‌صورت 24×7 فعالیت می‌کند.

بنابراین ممکن است تحلیل یک Incident در یک شیفت شروع و در شیفت بعد ادامه پیدا کند.

در این شرایط **Handover** اهمیت زیادی دارد.

Handover یعنی انتقال اطلاعات عملیاتی از یک تحلیلگر یا شیفت به تحلیلگر یا شیفت بعد.

یک Handover خوب باید حداقل شامل موارد زیر باشد:

```text
Incident ID
Current Status
What Happened
What Was Investigated
Evidence
Current Assessment
Pending Actions
Priority
Next Recommended Action
```

یک Handover ضعیف:

```text
"Still investigating suspicious login."
```

یک Handover بهتر:

```text
Incident: INC-2026-0142

User:
ali

Issue:
Suspicious successful authentication to DC01.

Investigated:
- 14 failed logins before successful login
- Source IP: 10.10.50.77
- Source host identified as WS-77
- Successful remote authentication to SRV-FS01
- No confirmed malicious process yet

Current assessment:
Suspicious, not yet confirmed compromise.

Pending:
- Review EDR telemetry on WS-77
- Check additional authentication events
- Verify user activity with IT

Priority:
High

Next action:
Continue endpoint investigation.
```

این نوع مستندسازی باعث می‌شود تحلیلگر بعدی مجبور نباشد Investigation را از صفر شروع کند.

---

# 1.26 یک روز کاری تحلیلگر Tier 1

برای درک بهتر نقش Tier 1، یک روز فرضی را بررسی کنیم.

ساعت 08:00:

تحلیلگر وارد شیفت می‌شود.

اولین کار:

### بررسی Handover

```text
Open Incidents
Pending Investigations
Critical Alerts
System Issues
```

ساعت 08:15:

Alert جدید:

```text
Multiple Failed Authentication
```

تحلیلگر:

1. Alert را باز می‌کند.
2. Source IP را بررسی می‌کند.
3. User را بررسی می‌کند.
4. Target را بررسی می‌کند.
5. Time Range را بررسی می‌کند.
6. Logهای مرتبط را Search می‌کند.
7. متوجه می‌شود IP متعلق به Vulnerability Scanner است.
8. Change Ticket مربوطه را پیدا می‌کند.
9. Alert را Benign تشخیص می‌دهد.
10. Case را مستند و Close می‌کند.

ساعت 09:00:

Alert دیگری:

```text
Suspicious PowerShell Execution
```

تحلیلگر:

```text
Host → WS-123
User → Ali
Parent Process → WINWORD.EXE
Command Line → Encoded PowerShell
Network Connection → Unknown Domain
```

اکنون موضوع جدی‌تر است.

تحلیلگر:

- Endpoint Telemetry را بررسی می‌کند.
- Domain را بررسی می‌کند.
- Process Tree را بررسی می‌کند.
- IOCها را استخراج می‌کند.
- یافته‌ها را ثبت می‌کند.
- Incident را به Tier 2 Escalate می‌کند.

این همان نوع فعالیتی است که در ادامه کتاب به‌صورت عملی تمرین خواهیم کرد.

---

# 1.27 اشتباهات رایج تحلیلگر تازه‌کار

## اشتباه اول: هر Alert را Incident فرض کردن

Alert فقط یک Signal است.

باید بررسی شود.

---

## اشتباه دوم: اعتماد کامل به Severity

ممکن است Detection اشتباه Configure شده باشد.

Severity باید با Context بررسی شود.

---

## اشتباه سوم: جست‌وجوی فقط یک Log

یک Event معمولاً داستان کامل را نشان نمی‌دهد.

تحلیلگر باید بتواند:

```text
Pivot
```

کند.

یعنی از یک داده به داده مرتبط دیگر حرکت کند.

مثلاً:

```text
User
 ↓
Host
 ↓
Process
 ↓
Network Connection
 ↓
Domain
 ↓
Other Hosts
```

---

## اشتباه چهارم: نادیده گرفتن زمان

زمان یکی از مهم‌ترین فیلدهای Investigation است.

تحلیلگر باید بتواند:

```text
Before
During
After
```

را بررسی کند.

---

## اشتباه پنجم: مستندسازی ضعیف

اگر تحلیلگر نتیجه‌گیری خود را ثبت نکند، تحلیل انجام‌شده ممکن است برای تیم هیچ ارزش عملیاتی نداشته باشد.

---

# 1.28 مدل ذهنی پیشنهادی برای SOC Analyst

از این فصل به بعد، هنگام مشاهده هر Alert، ابتدا این چارچوب را در ذهن داشته باشید:

```text
1. What?
   چه اتفاقی افتاده؟

2. Who?
   چه کاربر یا موجودیتی درگیر است؟

3. Where?
   روی چه سیستم یا شبکه‌ای رخ داده؟

4. When?
   چه زمانی رخ داده؟

5. From Where?
   Source چیست؟

6. What Else?
   چه فعالیت‌های مرتبط دیگری وجود دارد؟

7. Is It Normal?
   آیا این رفتار عادی است؟

8. Is It Malicious?
   آیا شواهدی از رفتار مخرب وجود دارد؟

9. What Is The Impact?
   اگر مخرب باشد، چه چیزی در معرض خطر است؟

10. What Next?
    اقدام بعدی چیست؟
```

این چارچوب ساده، پایه بسیاری از Investigationهای آینده خواهد بود.

---

# 1.29 سناریوی عملی فصل

## سناریو

در SIEM سازمان Alert زیر ایجاد شده است:

```text
Alert:
Multiple Failed Logins

User:
administrator

Source IP:
10.10.20.45

Target:
DC01

Attempts:
87

Time:
10:02 - 10:05
```

اطلاعات اولیه:

```text
10.10.20.45 = WS-045
WS-045 = Employee Workstation
User of WS-045 = sara
administrator = Privileged Account
```

### مرحله اول: سؤال

آیا این Alert الزاماً یک حمله Brute Force است؟

خیر.

هنوز شواهد کافی نداریم.

---

### مرحله دوم: سؤال

چه اطلاعاتی باید جمع‌آوری شود؟

حداقل:

- Login Events
- Source Host
- Source User
- Target Account
- Target Host
- Authentication Type
- Time Range
- Successful Login
- Process Activity
- Network Activity

---

### مرحله سوم: Hypothesis

یک Hypothesis اولیه:

> احتمال تلاش برای دسترسی غیرمجاز به حساب Administrator وجود دارد.

اما این فقط Hypothesis است.

نباید آن را به‌عنوان Fact ثبت کرد.

---

### مرحله چهارم: Investigation

فرض کنیم در Logها موارد زیر پیدا می‌شود:

```text
10:02:11 Failed Login
10:02:13 Failed Login
10:02:14 Failed Login
...
10:04:59 Failed Login
```

سپس:

```text
10:05:01 Successful Login
```

و بعد:

```text
10:05:13 Remote Logon to SRV-FS01
```

در این مرحله اهمیت Incident به‌شدت افزایش پیدا می‌کند.

اکنون یک تحلیلگر حرفه‌ای باید سؤال‌های جدیدی بپرسد:

- Successful Login با چه Authentication Type انجام شده؟
- آیا Credential معتبر بوده؟
- چه کسی پشت WS-045 بوده؟
- آیا Sara واقعاً در آن زمان فعال بوده؟
- بعد از ورود به SRV-FS01 چه فعالیتی انجام شده؟
- آیا Process مشکوکی اجرا شده؟
- آیا Account Administrator قبل یا بعد از این رویداد روی سیستم دیگری استفاده شده؟
- آیا Source Host قبلاً نیز چنین رفتاری داشته است؟

این فرآیند را **Investigation Pivoting** می‌نامیم.

---

# 1.30 تمرین عملی فصل

## تمرین 1 — تشخیص Event، Alert و Incident

برای هر مورد زیر مشخص کنید که بیشتر به کدام دسته تعلق دارد:

### مورد A

```text
User ali successfully logged in to PC-12 at 09:00.
```

### مورد B

```text
50 failed logins from one IP within 2 minutes.
```

### مورد C

```text
Successful login followed by suspicious PowerShell
execution and connection to a known malicious domain.
```

پاسخ را همراه با دلیل فنی بنویسید.

---

## تمرین 2 — Context Analysis

فرض کنید Alert زیر را دارید:

```text
PowerShell Execution Detected
Host: SRV-02
User: admin
Time: 02:15
```

حداقل **10 سؤال** بنویسید که برای Triage اولیه باید پاسخ آن‌ها را پیدا کنید.

---

## تمرین 3 — طراحی Handover

سناریو:

```text
User: reza
Host: WS-22
Alert: Suspicious Login
Source IP: 10.10.30.44

5 failed logins
1 successful login

After successful login:
Remote connection to SRV-03

Investigation incomplete.
```

یک Handover حرفه‌ای برای تحلیلگر شیفت بعد بنویسید.

هدف تمرین این نیست که تشخیص دهید «حمله قطعی است».

هدف این است که **وضعیت فعلی Investigation را به شکلی منتقل کنید که تحلیلگر بعدی بتواند بدون شروع دوباره کار را ادامه دهد.**

---

# 1.31 تمرین عملی آزمایشگاه

در این فصل هنوز نیازی به ساخت SIEM نداریم.

آزمایشگاه اولیه فقط باید یک محیط کوچک داشته باشد:

```text
┌─────────────────────┐
│   Analyst Machine   │
│                     │
│ Windows / Linux     │
└──────────┬──────────┘
           │
       Lab Network
           │
    ┌──────┴──────┐
    │             │
┌───▼────┐   ┌────▼────┐
│Windows │   │ Linux   │
│System  │   │ System  │
└────────┘   └─────────┘
```

در این مرحله هدف فقط این است که مخاطب:

- Event تولید کند؛
- Event را مشاهده کند؛
- تفاوت فعالیت عادی و غیرعادی را درک کند؛
- و برای اولین بار با دیدگاه یک SOC Analyst به سیستم نگاه کند.

در فصل‌های بعدی همین محیط به‌تدریج توسعه پیدا خواهد کرد.

---

# 1.32 جمع‌بندی فصل

در این فصل آموختیم که SOC یک نرم‌افزار یا یک اتاق پر از مانیتور نیست.

SOC یک قابلیت عملیاتی متشکل از:

```text
People
Process
Technology
Data
```

است.

همچنین میان:

```text
Event
Alert
Incident
```

تفاوت وجود دارد.

یک تحلیلگر Tier 1 باید بتواند Alert را دریافت، Triage و در صورت نیاز Investigation اولیه انجام دهد.

مهم‌ترین اصل این فصل:

> **Never treat an alert as a fact. Treat it as a question.**

یعنی:

> هر Alert را به‌عنوان یک واقعیت قطعی در نظر نگیر؛ آن را یک سؤال برای Investigation بدان.

وقتی SIEM می‌گوید:

```text
Suspicious Login
```

کار تحلیلگر این نیست که فوراً بگوید:

> «کاربر هک شده است.»

بلکه باید بپرسد:

> «چه شواهدی داریم که این Login واقعاً مشکوک است؟»

این تغییر نگرش، یکی از بنیادی‌ترین مهارت‌هایی است که در ادامه دوره توسعه خواهد یافت.

---

# 1.33 مهارت‌های مورد انتظار پس از پایان فصل

پس از مطالعه و انجام تمرین‌های این فصل، مخاطب باید بتواند:

- SOC را تعریف کند.
- نقش Security Operations را توضیح دهد.
- وظایف Tier 1 را تشریح کند.
- Event، Alert و Incident را تفکیک کند.
- False Positive و Benign Positive را تشخیص مفهومی دهد.
- مفهوم Triage را توضیح دهد.
- اهمیت Context را در Investigation بیان کند.
- نقش SIEM را توضیح دهد.
- تفاوت SOC و NOC را بیان کند.
- مفهوم Escalation و Handover را درک کند.
- برای یک Alert مجموعه‌ای از سؤالات Investigation طراحی کند.
- یک Handover اولیه و حرفه‌ای تهیه کند.

در فصل بعد، وارد **«ذهنیت و روش تفکر تحلیلگر SOC»** می‌شویم و به‌صورت عمیق‌تر روی Evidence، Hypothesis، Context، Normal/Anomalous/Malicious و خطاهای شناختی تحلیلگر کار خواهیم کرد.