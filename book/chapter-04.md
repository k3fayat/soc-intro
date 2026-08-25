# ماژول اول — مبانی عملیات امنیت و نقش SOC

# فصل چهارم — Logging، Telemetry و معماری داده در SOC

## اهداف یادگیری

در پایان این فصل، مخاطب باید بتواند:

* مفهوم **Logging** را در معماری SOC توضیح دهد.
* تفاوت Event، Log، Telemetry و Alert را درک کند.
* ساختار کلی Windows Event Log را بشناسد.
* مفهوم Event ID، Channel، Provider و Event Record را توضیح دهد.
* با ساختار کلی Syslog در Linux آشنا شود.
* انواع مهم Log Sourceها را طبقه‌بندی کند.
* تشخیص دهد برای هر سؤال امنیتی به چه Logهایی نیاز است.
* تفاوت **Raw Log** و **Normalized Data** را درک کند.
* مفهوم Log Collection را توضیح دهد.
* با مشکلات رایج Logging مانند Missing Logs، Timestamp اختلاف‌دار و Log Noise آشنا شود.
* یک معماری ساده Centralized Logging طراحی کند.
* آزمایشگاه فصل قبل را به یک محیط دارای Logging مرکزی تبدیل کند.
* برای یک Investigation مشخص، Data Source مناسب انتخاب کند.

---

# 4.1 مقدمه

در سه فصل گذشته سه مفهوم را به‌صورت تدریجی ساختیم:

```text
فصل 1:
SOC چیست؟

فصل 2:
چگونه باید به‌عنوان Analyst فکر کنیم؟

فصل 3:
چگونه محیط عملی برای تمرین بسازیم؟
```

اکنون باید وارد یکی از بنیادی‌ترین مباحث SOC شویم:

> **داده‌ای که تحلیلگر SOC بر اساس آن تصمیم می‌گیرد، از کجا می‌آید؟**

اگر تحلیلگر Alert دریافت کند:

```text
Suspicious Login
```

سؤال بعدی این است:

> این Alert بر اساس چه داده‌ای ایجاد شده است؟

ممکن است پاسخ این باشد:

```text
Windows Security Event
```

اما این Event از کجا آمده؟

```text
User Action
      ↓
Windows
      ↓
Event
      ↓
Event Log
      ↓
Log Collector
      ↓
SIEM
      ↓
Detection Rule
      ↓
Alert
```

اگر این زنجیره را درک نکنیم، کار با SIEM تبدیل به حفظ کردن Query و کلیک کردن روی Dashboardها خواهد شد.

هدف این فصل این است که این زنجیره را از پایین‌ترین سطح بشناسیم.

---

# 4.2 Data در SOC

SOC بدون Data نمی‌تواند فعالیت کند.

اما منظور از Data فقط Log نیست.

منابع داده یک SOC می‌توانند شامل موارد زیر باشند:

```text
Logs
Events
Telemetry
Network Traffic
DNS Data
Authentication Data
Endpoint Data
Cloud Audit Data
Threat Intelligence
Asset Information
User Information
```

برای تحلیلگر، سؤال اصلی این نیست که:

> «چه Logهایی داریم؟»

بلکه:

> «برای پاسخ به سؤال امنیتی من، چه داده‌ای لازم است؟»

---

# 4.3 Event چیست؟

در ساده‌ترین تعریف:

> Event ثبت وقوع یک Activity یا State Change است.

مثلاً:

```text
User Login
Process Started
File Created
Service Stopped
Firewall Connection Allowed
Firewall Connection Blocked
```

Event معمولاً دارای ویژگی‌هایی مانند:

```text
Timestamp
Source
Type
User
Host
Action
Result
```

است.

مثلاً:

```text
Timestamp: 10:15:22
Host: WIN-EP01
User: ali
Action: Logon
Result: Success
```

این یک Event است.

---

# 4.4 Log چیست؟

**Log** رکوردی است که یک سیستم برای ثبت فعالیت، وضعیت یا رویداد ایجاد می‌کند.

در عمل Event و Log گاهی به‌جای یکدیگر استفاده می‌شوند، اما از دید معماری داده بهتر است آن‌ها را کاملاً مترادف ندانیم.

برای مثال:

```text
Event
```

یک رخداد منطقی است.

در حالی که:

```text
Log
```

می‌تواند شکل ثبت‌شده و ذخیره‌شده آن رخداد باشد.

به‌صورت ساده:

```text
Activity
   ↓
Event
   ↓
Log Record
```

---

# 4.5 Telemetry چیست؟

Telemetry داده‌ای است که برای مشاهده وضعیت و رفتار یک سیستم جمع‌آوری می‌شود.

Telemetry می‌تواند بسیار گسترده‌تر از Log سنتی باشد.

مثلاً Endpoint Telemetry:

```text
Process Creation
Process Tree
Command Line
Network Connection
File Hash
Loaded Module
Registry Change
User Logon
DNS Query
```

بنابراین:

> هر Telemetry الزاماً یک Log سنتی نیست.

در ابزارهای مدرن EDR، حجم زیادی از Telemetry به‌صورت ساختاریافته جمع‌آوری می‌شود.

---

# 4.6 تفاوت Event، Log، Telemetry و Alert

این چهار مفهوم را کنار هم قرار دهیم:

| مفهوم     | معنی                                              |
| --------- | ------------------------------------------------- |
| Event     | یک رخداد یا فعالیت                                |
| Log       | ثبت یک رخداد/اطلاعات توسط سیستم                   |
| Telemetry | داده جمع‌آوری‌شده برای مشاهده و تحلیل رفتار سیستم |
| Alert     | نتیجه یک Detection یا Rule که نیازمند توجه است    |

مثال:

```text
User enters wrong password
          ↓
Windows generates Event
          ↓
Event is stored in Security Log
          ↓
Log is sent to SIEM
          ↓
Detection identifies repeated failures
          ↓
Alert is generated
```

این زنجیره را باید کاملاً درک کنید.

---

# 4.7 چرا Logging برای SOC حیاتی است؟

فرض کنید یک کاربر در ساعت 02:00 وارد سیستم شده و پس از آن یک فایل حساس را خوانده است.

اگر Logging نداشته باشیم:

```text
What happened?
Unknown
```

اگر Authentication Log داشته باشیم:

```text
Who logged in?
Known
```

اگر Process Telemetry داشته باشیم:

```text
What process ran?
Known
```

اگر File Access Logging داشته باشیم:

```text
What file was accessed?
Known
```

اگر Network Telemetry داشته باشیم:

```text
Where did the system connect?
Known
```

بنابراین:

> کیفیت Investigation به‌شدت به کیفیت و پوشش داده وابسته است.

---

# 4.8 Visibility

در SOC اصطلاح بسیار مهمی داریم:

> **Visibility**

Visibility یعنی توانایی مشاهده فعالیت‌ها و وضعیت سیستم‌ها.

فرض کنید مهاجم یک Process اجرا کرده است، اما EDR آن سیستم فعال نیست.

ممکن است:

```text
Actual Activity = Yes
Visibility = No
```

باشد.

در این حالت عدم مشاهده Event به معنای عدم وقوع Activity نیست.

این نکته بسیار مهم است:

> **Absence of Evidence is not always Evidence of Absence.**

یعنی نبودن یک Log لزوماً به معنای رخ ندادن Activity نیست.

ممکن است Logging:

* فعال نباشد.
* ناقص باشد.
* حذف شده باشد.
* به SIEM ارسال نشده باشد.
* Filtering شده باشد.
* Retention آن تمام شده باشد.

---

# 4.9 Log Source

هر سیستمی که داده امنیتی تولید کند، می‌تواند یک **Log Source** باشد.

نمونه‌ها:

### Endpoint

```text
Windows
Linux
macOS
EDR
```

### Network

```text
Firewall
Router
Switch
VPN
Proxy
NDR
IDS/IPS
```

### Identity

```text
Active Directory
LDAP
IAM
SSO
MFA
```

### Application

```text
Web Server
Database
ERP
Email
Application Server
```

### Cloud

```text
Cloud Audit Logs
Cloud Identity
Cloud Firewall
Cloud Storage
```

---

# 4.10 دسته‌بندی Logها

یک دسته‌بندی کاربردی:

```text
Authentication Logs
Process Logs
Network Logs
DNS Logs
Application Logs
System Logs
Security Logs
Audit Logs
Cloud Logs
```

هر دسته برای سؤالات خاصی مفید است.

مثلاً:

> چه کسی Login کرده؟

Authentication Log.

> چه Processی اجرا شده؟

Process Telemetry.

> سیستم به چه IPای متصل شده؟

Network Telemetry.

> چه Domainای Query شده؟

DNS Log.

---

# 4.11 Windows Event Logging

در Windows یکی از مهم‌ترین منابع داده برای SOC:

> **Windows Event Log**

است.

سیستم‌عامل Windows Eventهای زیادی تولید می‌کند.

Eventها در Channelهای مختلف ذخیره می‌شوند.

Channelهای مهم:

```text
Application
Security
System
Setup
Forwarded Events
```

---

# 4.12 Security Channel

برای SOC، Security Channel اهمیت بسیار زیادی دارد.

در این Channel می‌توان Activityهایی مانند موارد زیر را مشاهده کرد:

```text
Authentication
Account Management
Privilege Use
Policy Changes
Process-related Security Events
```

البته اینکه دقیقاً چه Eventهایی ثبت شوند، به Audit Policy و Configuration سیستم بستگی دارد.

این نکته مهم است:

> Windows به‌صورت جادویی تمام فعالیت‌های امنیتی را ثبت نمی‌کند.

Logging باید به‌درستی Configure شود.

---

# 4.13 Event ID

یکی از مهم‌ترین فیلدهای Windows Event:

> **Event ID**

است.

برای مثال:

```text
4624
```

معمولاً با Successful Logon مرتبط است.

و:

```text
4625
```

معمولاً با Failed Logon مرتبط است.

Event ID به تحلیلگر اجازه می‌دهد Eventها را سریع‌تر دسته‌بندی و Search کند.

اما:

> Event ID به‌تنهایی معنی کامل Event را مشخص نمی‌کند.

باید سایر فیلدها نیز بررسی شوند.

---

# 4.14 Event ID به‌تنهایی کافی نیست

فرض کنید:

```text
Event ID = 4624
```

این اطلاعات محدود است.

باید بدانیم:

```text
Who?
Where?
When?
Logon Type?
Source IP?
Authentication Package?
Target Account?
Workstation?
```

مثلاً:

```text
Event ID:
4624

User:
administrator

Source:
10.10.20.50

Target:
DC01

Time:
03:15
```

اکنون Event معنا پیدا می‌کند.

---

# 4.15 Event Provider

هر Event معمولاً دارای یک **Provider** نیز هست.

Provider مشخص می‌کند کدام Component یا Service Event را ایجاد کرده است.

برای تحلیل دقیق Eventها، این فیلد می‌تواند بسیار مفید باشد.

مثلاً:

```text
Provider
Event ID
Channel
Level
Timestamp
```

در کنار هم Context بیشتری ایجاد می‌کنند.

---

# 4.16 Event Level

Eventها ممکن است سطحی مانند موارد زیر داشته باشند:

```text
Information
Warning
Error
Critical
```

اما یک نکته مهم:

> Event Level با Security Severity یکسان نیست.

مثلاً یک Event با Level:

```text
Information
```

ممکن است از نظر امنیتی بسیار مهم باشد.

بنابراین نباید تصور کنیم:

```text
Information = Low Security Importance
```

این دو مفهوم متفاوت‌اند.

---

# 4.17 Windows Event Record

یک Windows Event معمولاً مجموعه‌ای از Metadata و Event Data دارد.

به‌صورت مفهومی:

```text
Event
├── Timestamp
├── Provider
├── Event ID
├── Level
├── Computer
├── Channel
└── Event Data
     ├── User
     ├── Source IP
     ├── Process
     └── Other Fields
```

تحلیلگر SOC باید یاد بگیرد به Event به‌عنوان یک **ساختار داده** نگاه کند، نه فقط یک متن.

---

# 4.18 Structured Data

داده ساختاریافته یعنی Fieldهای مشخص داشته باشد.

مثلاً:

```text
user = ali
source_ip = 10.10.20.15
event_id = 4625
action = logon_failure
```

این بسیار بهتر از یک متن آزاد مانند:

```text
User ali failed to login from 10.10.20.15
```

برای پردازش ماشینی است.

SIEMها معمولاً تلاش می‌کنند داده‌های مختلف را به شکل قابل Search و Correlate تبدیل کنند.

---

# 4.19 Linux Logging

در Linux معماری Logging بسته به Distribution و سرویس‌ها متفاوت است.

یکی از مفاهیم قدیمی و مهم:

> **Syslog**

است.

Syslog یک استاندارد/مکانیزم رایج برای انتقال و ثبت پیام‌های Logging است.

در محیط Linux ممکن است سرویس‌هایی مانند:

```text
rsyslog
syslog-ng
systemd-journald
```

را مشاهده کنید.

---

# 4.20 Journal در Linux

در سیستم‌های مدرن Linux، **systemd-journald** نقش مهمی دارد.

داده‌ها از طریق:

```bash
journalctl
```

قابل مشاهده هستند.

مثلاً:

```bash
journalctl
```

یا برای مشاهده پیام‌های Kernel:

```bash
journalctl -k
```

برای مشاهده Logهای مربوط به یک Boot:

```bash
journalctl -b
```

در فصل‌های بعد با Queryهای دقیق‌تر کار خواهیم کرد.

---

# 4.21 Authentication در Linux

یکی از منابع مهم برای SOC، Authentication Log است.

در برخی سیستم‌ها فایل‌هایی مانند:

```text
/var/log/auth.log
```

یا:

```text
/var/log/secure
```

مورد استفاده قرار می‌گیرند.

نام و محل دقیق Log به Distribution و Configuration بستگی دارد.

این Logها می‌توانند اطلاعاتی درباره:

```text
SSH Login
Failed Authentication
sudo
Privilege Escalation Attempts
```

ارائه کنند.

---

# 4.22 Syslog Severity

در Syslog معمولاً Severityهایی از:

```text
Emergency
Alert
Critical
Error
Warning
Notice
Informational
Debug
```

وجود دارند.

این Levelها با Severityهایی که در SIEM برای Incident استفاده می‌کنیم یکی نیستند.

مثلاً:

```text
Syslog Severity = Warning
```

نباید لزوماً به معنی:

```text
Security Incident Severity = High
```

باشد.

---

# 4.23 Facility

یکی دیگر از مفاهیم Syslog:

> **Facility**

است.

Facility مشخص می‌کند پیام معمولاً به کدام دسته یا منبع منطقی مربوط است.

در محیط‌های مختلف ممکن است با مواردی مانند:

```text
auth
kern
daemon
user
mail
local0-local7
```

مواجه شوید.

برای تحلیلگر SOC، مهم‌تر از حفظ تمام مقادیر این است که بداند:

> Syslog پیام‌ها دارای Metadata هستند که برای Filtering و Routing استفاده می‌شود.

---

# 4.24 Centralized Logging

در آزمایشگاه فصل قبل دو سیستم داشتیم:

```text
WIN-EP01
LIN-SRV01
```

اگر فقط همین دو سیستم باشند، می‌توانیم Logها را مستقیماً روی خودشان بررسی کنیم.

اما فرض کنید:

```text
100 Endpoints
20 Servers
5 Firewalls
2 VPN
1 Email Gateway
```

در این حالت بررسی تک‌تک سیستم‌ها بسیار دشوار می‌شود.

راه‌حل:

> **Centralized Logging**

یعنی Logها را از منابع مختلف جمع‌آوری و در یک نقطه مرکزی ذخیره/پردازش کنیم.

معماری:

```text
WIN-EP01 ─────┐
              │
LIN-SRV01 ────┤
              │
Firewall ─────┤
              │
VPN ──────────┤
              ↓
       Log Collector
              ↓
     Central Storage
```

---

# 4.25 چرا Centralized Logging مهم است؟

مزایای اصلی:

### Search متمرکز

یک Query روی چندین سیستم.

### Correlation

ارتباط دادن Eventهای مختلف.

### Retention

نگهداری داده برای مدت مشخص.

### Access Control

کنترل دسترسی به داده‌ها.

### Integrity

کاهش وابستگی به سیستم منبع.

### Investigation

ساخت Timeline از چند منبع.

---

# 4.26 معماری ساده Centralized Logging

در این دوره ابتدا معماری ساده زیر را ایجاد می‌کنیم:

```text
                  SOC-LAB
                     |
        ┌────────────┼────────────┐
        │            │            │
    WIN-EP01      LIN-SRV01     Collector
        │            │            │
        └────────────┴────────────┘
                     │
                     ↓
              Central Logs
```

در مرحله بعد:

```text
Central Logs
     ↓
SIEM
     ↓
Detection
     ↓
Alert
```

---

# 4.27 Log Collector چیست؟

**Log Collector** سیستمی است که Logها را از منابع مختلف دریافت و منتقل یا پردازش می‌کند.

وظایف Collector ممکن است شامل موارد زیر باشد:

* Receive
* Parse
* Buffer
* Filter
* Enrich
* Forward
* Store

Collector الزاماً SIEM نیست.

این تفاوت را حفظ کنید:

```text
Log Collector ≠ SIEM
```

SIEM قابلیت‌های بسیار بیشتری دارد.

---

# 4.28 Log Forwarding

برای انتقال Log ممکن است از روش‌های مختلف استفاده شود.

مثلاً:

```text
Windows
   ↓
Windows Event Forwarding / Agent
   ↓
Collector
```

یا:

```text
Linux
   ↓
Syslog
   ↓
Collector
```

یا:

```text
Endpoint
   ↓
Security Agent
   ↓
Backend
```

روش دقیق به محصول و معماری بستگی دارد.

---

# 4.29 Push و Pull

در Log Collection دو مدل مفهومی رایج داریم.

## Push

Source خودش داده را به Collector ارسال می‌کند.

```text
Source
   ↓
Collector
```

## Pull

Collector از Source داده را دریافت می‌کند.

```text
Collector
   ↓
Source
```

در دنیای واقعی مدل‌ها می‌توانند پیچیده‌تر باشند.

تحلیلگر SOC لازم نیست در این مرحله تمام جزئیات مهندسی Collectorها را بداند، اما باید معماری را درک کند.

---

# 4.30 Log Parsing

فرض کنید Log خام چنین باشد:

```text
Aug 25 10:15:20 server sshd[1234]:
Failed password for user ali from 10.10.20.15
```

برای انسان قابل فهم است.

اما برای SIEM بهتر است به Fieldهای ساختاریافته تبدیل شود:

```text
timestamp = 2026-08-25T10:15:20
service = sshd
event = authentication_failure
user = ali
source_ip = 10.10.20.15
```

این فرآیند:

> **Parsing**

نام دارد.

---

# 4.31 Normalization

فرض کنید Windows یک مفهوم را این‌گونه ثبت کند:

```text
Logon Failure
```

و Linux:

```text
Failed password
```

و VPN:

```text
Authentication Failed
```

هر سه از نظر معنایی می‌توانند به یک مفهوم مشترک مربوط باشند:

```text
Authentication Failure
```

**Normalization** تلاش می‌کند داده‌های متفاوت را به یک ساختار معنایی مشترک تبدیل کند.

مثلاً:

```text
event.category = authentication
event.action = logon
event.outcome = failure
```

این کار Correlation را بسیار آسان‌تر می‌کند.

---

# 4.32 چرا Normalization مهم است؟

فرض کنید می‌خواهیم Detection زیر را بنویسیم:

> اگر یک User در مدت کوتاه از چند Source مختلف Authentication Failure داشته باشد، Alert ایجاد کن.

اگر داده‌ها ساختارهای کاملاً متفاوت داشته باشند، Rule پیچیده می‌شود.

اما اگر Normalized باشند:

```text
user
source.ip
event.category
event.outcome
timestamp
```

Rule ساده‌تر می‌شود.

---

# 4.33 Raw Data را دور نریزید

در معماری SOC باید مراقب باشیم که فقط داده Normalize شده را نگه نداریم.

چرا؟

چون ممکن است:

* Parser اشتباه کند.
* Fieldی از بین برود.
* در آینده به Field جدیدی نیاز داشته باشیم.
* Investigation به داده اصلی نیاز داشته باشد.

بنابراین در بسیاری از معماری‌ها:

```text
Raw Data
+
Parsed / Normalized Data
```

اهمیت دارند.

---

# 4.34 Log Noise

یکی از مشکلات بزرگ SOC:

> **Log Noise**

است.

یعنی حجم زیادی از Eventهایی که ارزش امنیتی کمی دارند.

مثلاً یک Server ممکن است هزاران Event تولید کند، اما تنها درصد کمی برای Security Monitoring مهم باشند.

اگر همه چیز بدون مدیریت وارد SIEM شود:

```text
Huge Data Volume
       ↓
High Storage Cost
       ↓
High Query Cost
       ↓
Analyst Overload
       ↓
Alert Fatigue
```

---

# 4.35 Alert Fatigue

**Alert Fatigue** یعنی تحلیلگر به دلیل دریافت حجم زیاد Alert، توان و تمرکز خود را از دست بدهد.

مثلاً:

```text
1000 Alerts/day
```

اگر:

```text
950 = Benign
50 = Relevant
```

باشد، تحلیلگر ممکن است به مرور نسبت به Alertها بی‌تفاوت شود.

بنابراین SOC باید روی:

* Detection Quality
* Tuning
* Prioritization
* Context
* Automation

کار کند.

---

# 4.36 Log Retention

Log باید برای مدت مشخصی نگهداری شود.

مثلاً:

```text
Hot:
7 days

Warm:
30 days

Archive:
1 year
```

این اعداد صرفاً مثال هستند.

Retention واقعی به:

* قانون
* Policy
* نوع داده
* نیاز کسب‌وکار
* هزینه Storage
* Risk

وابسته است.

---

# 4.37 Log Integrity

یکی از سؤالات مهم:

> آیا می‌توان به Log اعتماد کرد؟

اگر مهاجم روی سیستم خود دسترسی Administrator داشته باشد، ممکن است تلاش کند:

* Log را حذف کند.
* Log را تغییر دهد.
* Logging را Disable کند.
* Timestamp را دستکاری کند.

به همین دلیل Centralized Logging اهمیت بیشتری پیدا می‌کند.

اگر Log در زمان نزدیک به وقوع Event به سیستم دیگری ارسال شده باشد، دستکاری روی Endpoint دشوارتر می‌شود.

---

# 4.38 Logging Coverage

یک SOC باید بداند:

> چه سیستم‌هایی Logging می‌شوند و چه سیستم‌هایی نمی‌شوند؟

یک جدول ساده:

| Asset     | Logging | Source           | Status  |
| --------- | ------- | ---------------- | ------- |
| WIN-EP01  | Yes     | Security Log     | Active  |
| LIN-SRV01 | Yes     | Syslog           | Active  |
| DC01      | Future  | Windows Security | Planned |
| Firewall  | Future  | Syslog           | Planned |

این مفهوم به **Visibility Management** کمک می‌کند.

---

# 4.39 Logging Gap

اگر یک Asset مهم هیچ Logی ارسال نکند:

```text
Critical Server
     ↓
No Logs
     ↓
No Visibility
```

این خودش یک مسئله امنیتی است.

مثلاً:

> Domain Controller بدون Security Logging مناسب

ریسک بسیار بیشتری نسبت به یک سیستم تستی دارد.

---

# 4.40 Data Source Selection

یکی از مهم‌ترین مهارت‌های SOC Analyst:

> انتخاب Data Source مناسب برای سؤال.

مثلاً:

### سؤال:

چه کسی وارد سیستم شده؟

Data Source:

```text
Authentication Logs
```

### سؤال:

چه Processی اجرا شده؟

Data Source:

```text
Endpoint Telemetry
```

### سؤال:

سیستم با چه IPای ارتباط داشته؟

Data Source:

```text
Network Telemetry / Firewall / EDR
```

### سؤال:

چه Domainای Resolve شده؟

Data Source:

```text
DNS Logs
```

### سؤال:

چه کسی فایل را تغییر داده؟

Data Source:

```text
File Auditing / EDR
```

---

# 4.41 Data Source Matrix

یک Data Source Matrix برای آزمایشگاه ایجاد کنید:

| Security Question   | Data Source           |
| ------------------- | --------------------- |
| Who logged in?      | Windows Security Log  |
| Failed login?       | Windows Security Log  |
| Process executed?   | Endpoint Telemetry    |
| Network connection? | EDR / Firewall        |
| DNS query?          | DNS Log               |
| SSH login?          | Linux Auth Log        |
| Privilege use?      | OS Security/Audit Log |
| File modification?  | File Audit / EDR      |

این جدول در ادامه دوره دائماً گسترش پیدا خواهد کرد.

---

# 4.42 تمرین عملی 1 — Windows Logging

روی `WIN-EP01`:

1. با کاربر عادی Login کنید.
2. یک Login ناموفق ایجاد کنید.
3. یک Login موفق انجام دهید.
4. PowerShell اجرا کنید.
5. یک فایل ایجاد کنید.
6. فایل را حذف کنید.
7. Event Viewer را باز کنید.
8. Security Log را بررسی کنید.

برای هر Activity حداقل یک Event مرتبط پیدا کنید.

جدول:

| Activity         | Event ID | User | Time | Evidence |
| ---------------- | -------: | ---- | ---- | -------- |
| Failed Login     |          |      |      |          |
| Successful Login |          |      |      |          |
| PowerShell       |          |      |      |          |
| File Activity    |          |      |      |          |

**توجه:** همه این فعالیت‌ها الزاماً با یک Event ID مشخص یا با Audit پیش‌فرض قابل مشاهده نیستند. اگر Eventی پیدا نکردید، آن را نیز ثبت کنید و دلیل احتمالی را بررسی کنید.

---

# 4.43 تمرین عملی 2 — Linux Logging

روی `LIN-SRV01`:

یک Login محلی یا SSH آزمایشگاهی ایجاد کنید.

سپس:

```bash
journalctl
```

را بررسی کنید.

در صورت وجود فایل Authentication Log:

```bash
sudo tail -n 50 /var/log/auth.log
```

یا در Distributionهایی که از فایل دیگری استفاده می‌کنند، Log مناسب را بررسی کنید.

هدف:

> پیدا کردن Authentication Event و استخراج Timestamp، User و Source است.

---

# 4.44 تمرین عملی 3 — مقایسه Windows و Linux

یک فعالیت مشابه ایجاد کنید:

```text
Authentication Failure
```

سپس مقایسه کنید:

### Windows

```text
Event ID
Channel
Provider
Account
Source
```

### Linux

```text
Timestamp
Service
User
Source
Message
```

سپس پاسخ دهید:

> چرا SIEM باید داده‌های متفاوت را Normalize کند؟

---

# 4.45 تمرین عملی 4 — طراحی Data Source

برای موارد زیر Data Source مناسب تعیین کنید:

### A

> کاربر در ساعت غیرعادی Login کرده است.

### B

> بعد از Login، Process مشکوکی اجرا شده است.

### C

> سیستم با یک IP خارجی ارتباط برقرار کرده است.

### D

> کاربر به یک Domain مشکوک Query ارسال کرده است.

### E

> یک فایل حساس تغییر کرده است.

برای هر مورد:

```text
Primary Data Source
Secondary Data Source
```

را مشخص کنید.

---

# 4.46 تمرین عملی 5 — ساخت Timeline

فرض کنید از سه منبع داده موارد زیر را دارید:

```text
Windows:
10:01:02 Successful Logon

EDR:
10:01:05 powershell.exe

DNS:
10:01:07 suspicious-example.com

Firewall:
10:01:09 Connection Allowed
```

Timeline بسازید.

سپس بنویسید:

> کدام Event باعث شد Event بعدی برای شما مهم‌تر شود؟

این تمرین مفهوم **Correlation** را به‌صورت عملی معرفی می‌کند.

---

# 4.47 تمرین عملی 6 — تشخیص Logging Gap

فرض کنید:

```text
WIN-EP01
Authentication Logs = Available

LIN-SRV01
Authentication Logs = Available

Critical Server
Authentication Logs = Missing
```

سؤال:

کدام مورد باید اولویت بیشتری برای اصلاح داشته باشد؟

چرا؟

پاسخ باید براساس:

```text
Asset Criticality
+
Visibility
+
Risk
```

باشد.

---

# 4.48 توسعه آزمایشگاه — Central Logging

اکنون وقت آن رسیده است که آزمایشگاه را یک مرحله توسعه دهیم.

معماری جدید:

```text
                       SOC-LAB
                          |
              ┌───────────┴───────────┐
              │       LAB Network     │
              │   192.168.100.0/24    │
              └───────────┬───────────┘
                          |
          ┌───────────────┼────────────────┐
          │               │                │
     ┌────▼────┐     ┌────▼────┐     ┌────▼────┐
     │WIN-EP01 │     │LIN-SRV01│     │ LOG01   │
     │ .10     │     │ .20     │     │ .30     │
     └────┬────┘     └────┬────┘     └────▲────┘
          │               │                │
          │               │                │
          └───────────────┴────────────────┘
                         Logs
```

VM جدید:

```text
LOG01
192.168.100.30
```

نقش:

> Central Log Collector

است.

---

# 4.49 طراحی LOG01

برای شروع:

```text
Name:
LOG01

IP:
192.168.100.30

CPU:
2 vCPU

RAM:
4 GB

Disk:
40 GB+
```

می‌توان از Linux Server برای این نقش استفاده کرد.

در این مرحله هدف آموزشی، ساخت یک **Collector ساده** است؛ نه ایجاد SIEM کامل.

---

# 4.50 انتخاب ابزار Collector

در ادامه دوره می‌توان از ابزارهایی مانند:

* Syslog
* rsyslog
* Fluent Bit
* Wazuh
* Elastic Stack
* OpenSearch
* سایر Agent/Collectorها

استفاده کرد.

اما برای جلوگیری از تبدیل فصل به آموزش محصول‌محور، ابتدا معماری را می‌آموزیم.

در یک SOC Analyst باید بدانید:

```text
Source
 ↓
Transport
 ↓
Collector
 ↓
Parser
 ↓
Storage
```

حتی اگر محصول تغییر کند، این معماری باقی می‌ماند.

---

# 4.51 اولین Central Logging ساده

در مرحله اول می‌توانیم Linux Log را به `LOG01` ارسال کنیم.

معماری:

```text
LIN-SRV01
    |
    | Syslog
    ↓
  LOG01
    |
    ↓
Central Log File
```

بعد Windows را نیز اضافه خواهیم کرد.

---

# 4.52 چرا Linux را اول اضافه می‌کنیم؟

Linux و Syslog برای درک معماری Central Logging نسبتاً ساده هستند.

مخاطب ابتدا مفهوم:

```text
Source
 ↓
Transport
 ↓
Collector
```

را تجربه می‌کند.

سپس همین مفهوم را برای Windows و در ادامه SIEM به‌کار می‌بریم.

---

# 4.53 تمرین آزمایشگاهی — ساخت LOG01

در این مرحله:

1. یک Linux VM جدید ایجاد کنید.
2. نام آن را `LOG01` بگذارید.
3. IP آن را:

```text
192.168.100.30
```

قرار دهید.
4. ارتباط آن را با `LIN-SRV01` بررسی کنید.
5. Snapshot ایجاد کنید:

```text
BASELINE-LOG01
```

6. وضعیت زمان را بررسی کنید.
7. Hostname و IP را ثبت کنید.

---

# 4.54 تمرین آزمایشگاهی — اولین Syslog

هدف این تمرین این است که یک Log از:

```text
LIN-SRV01
```

به:

```text
LOG01
```

ارسال شود.

پس از پیاده‌سازی، باید بتوانید چیزی مشابه این مشاهده کنید:

```text
LIN-SRV01
      |
      | Syslog
      ↓
LOG01
      |
      ↓
/var/log/...
```

ساختار دقیق فایل و Configuration بسته به Distribution و Syslog daemon متفاوت است؛ بنابراین در این مرحله مهم‌تر از حفظ Configuration، درک مسیر داده است.

---

# 4.55 تست Central Logging

پس از برقراری Logging:

روی `LIN-SRV01` یک Activity ایجاد کنید.

مثلاً:

```text
Login
Logout
sudo command
SSH authentication
```

سپس روی `LOG01` بررسی کنید آیا Event دریافت شده است.

اگر دریافت شد:

```text
Source:
LIN-SRV01

Collector:
LOG01

Event:
Authentication Activity
```

اکنون اولین Centralized Logging واقعی آزمایشگاه ایجاد شده است.

---

# 4.56 Troubleshooting Central Logging

اگر Log دریافت نشد، Investigation را مانند یک SOC Analyst انجام دهید.

از خودتان بپرسید:

### 1. آیا Source Event تولید کرده؟

```text
LIN-SRV01
```

### 2. آیا Syslog Service فعال است؟

### 3. آیا Source به Collector دسترسی شبکه‌ای دارد؟

### 4. آیا Port موردنیاز در حال Listen است؟

### 5. آیا Firewall مانع است؟

### 6. آیا Collector پیام را دریافت کرده؟

### 7. آیا پیام در محل صحیح ذخیره شده؟

این دقیقاً همان روش تفکری است که در Troubleshooting یک Security Pipeline نیز استفاده می‌شود.

---

# 4.57 Data Pipeline

اکنون یک مفهوم مهم را معرفی کنیم:

> **Data Pipeline**

مسیر داده:

```text
Activity
   ↓
Event Generation
   ↓
Log Source
   ↓
Agent / Syslog
   ↓
Network Transport
   ↓
Collector
   ↓
Parser
   ↓
Storage
   ↓
Search
```

هر مرحله ممکن است Failure داشته باشد.

بنابراین اگر Alertی ایجاد نشد، نباید فوراً نتیجه بگیریم:

> «Detection خراب است.»

ممکن است:

```text
Event تولید نشده
```

یا:

```text
Log ارسال نشده
```

یا:

```text
Collector دریافت نکرده
```

یا:

```text
Parser خراب بوده
```

یا:

```text
Data در Search قابل مشاهده نیست
```

باشد.

---

# 4.58 Observability برای SOC

یک SOC خوب باید نه‌تنها سیستم‌های سازمان را مشاهده کند، بلکه **خود Pipeline امنیتی** را نیز مشاهده کند.

یعنی باید بدانیم:

```text
Are logs arriving?
Are agents healthy?
Are sources connected?
Are events delayed?
Are parsers working?
Is storage available?
```

این موضوع در آینده در مبحث **SOC Health Monitoring** اهمیت زیادی پیدا می‌کند.

---

# 4.59 Data Loss

اگر Source روزانه:

```text
1,000,000 Events
```

تولید کند اما Collector فقط:

```text
700,000
```

دریافت کند، بخشی از Visibility از بین رفته است.

این مسئله می‌تواند ناشی از:

* Network Failure
* Agent Failure
* Collector Overload
* Storage Failure
* Configuration Error
* Filtering

باشد.

در SOC، **Missing Data خودش یک Signal مهم است.**

---

# 4.60 Timestamp و Event Delay

گاهی Event در:

```text
10:00:00
```

ایجاد می‌شود ولی ساعت:

```text
10:00:30
```

به Collector می‌رسد.

این اختلاف:

> **Event Delay / Ingestion Delay**

می‌تواند در Investigation اهمیت داشته باشد.

بنابراین باید تفاوت بین:

```text
Event Time
```

و:

```text
Ingestion Time
```

را بشناسیم.

---

# 4.61 مثال

```text
Event Time:
10:00:00

Ingestion Time:
10:00:12

Search Time:
10:00:15
```

اگر تحلیلگر فقط به زمان Search نگاه کند، ممکن است Timeline اشتباه ایجاد کند.

---

# 4.62 سؤال طلایی تحلیلگر

از این فصل به بعد، هنگام مشاهده هر Log این سؤال را مطرح کنید:

> **این داده دقیقاً چه چیزی را ثابت می‌کند؟**

و سپس:

> **چه چیزی را ثابت نمی‌کند؟**

مثلاً:

```text
Successful Login
```

ثابت می‌کند:

> Authentication موفق ثبت شده است.

اما لزوماً ثابت نمی‌کند:

> حساب هک شده است.

یا:

```text
PowerShell Execution
```

ثابت می‌کند:

> PowerShell اجرا شده است.

اما لزوماً ثابت نمی‌کند:

> PowerShell مخرب بوده است.

این تفکیک، پایه Investigation دقیق است.

---

# 4.63 تمرین نهایی فصل — از Activity تا SOC

سناریو:

کاربر روی `WIN-EP01` یک Login ناموفق ایجاد می‌کند.

شما باید مسیر داده را از لحظه انجام Activity تا رسیدن آن به SOC ترسیم کنید.

در حالت نهایی باید چیزی شبیه این طراحی کنید:

```text
User
 ↓
WIN-EP01
 ↓
Windows Event
 ↓
Security Log
 ↓
Collection Agent
 ↓
Network
 ↓
LOG01
 ↓
Parsing
 ↓
Central Storage
 ↓
SIEM
 ↓
Detection
 ↓
Alert
 ↓
SOC Tier 1
```

برای هر مرحله توضیح دهید:

1. چه اتفاقی می‌افتد؟
2. چه داده‌ای ایجاد می‌شود؟
3. Failure احتمالی چیست؟
4. چگونه می‌توان آن Failure را تشخیص داد؟

---

# 4.64 تمرین سناریویی — Log Missing

فرض کنید یک کاربر می‌گوید:

> «من ساعت 10:00 وارد سیستم شدم.»

اما در SIEM هیچ Authentication Eventی وجود ندارد.

به‌عنوان Tier 1 Analyst نباید فوراً نتیجه بگیرید:

> «کاربر دروغ می‌گوید.»

حداقل این موارد را بررسی کنید:

```text
1. Was the event generated?
2. Is local logging enabled?
3. Is the endpoint agent running?
4. Was the event forwarded?
5. Did the collector receive it?
6. Did parsing work?
7. Is the SIEM search correct?
8. Is there ingestion delay?
9. Did retention/filtering affect the data?
```

این سناریو یکی از مهم‌ترین درس‌های عملی فصل است:

> **عدم وجود Log، خودش نیازمند Investigation است.**

---

# 4.65 Checklist فصل

پیش از عبور از این فصل، باید بتوانید:

* [ ] Event را تعریف کنید.
* [ ] Log را تعریف کنید.
* [ ] Telemetry را تعریف کنید.
* [ ] Alert را از Event تفکیک کنید.
* [ ] Log Source را توضیح دهید.
* [ ] Windows Security Log را بشناسید.
* [ ] Event ID را توضیح دهید.
* [ ] Provider و Channel را بشناسید.
* [ ] Syslog را توضیح دهید.
* [ ] مفهوم Centralized Logging را توضیح دهید.
* [ ] Collector را از SIEM تفکیک کنید.
* [ ] Parsing را توضیح دهید.
* [ ] Normalization را توضیح دهید.
* [ ] Log Noise را توضیح دهید.
* [ ] Alert Fatigue را توضیح دهید.
* [ ] Event Time و Ingestion Time را تفکیک کنید.
* [ ] Logging Gap را شناسایی کنید.
* [ ] Data Source مناسب برای یک سؤال امنیتی انتخاب کنید.
* [ ] مسیر یک Event از Endpoint تا SOC را رسم کنید.

---

# 4.66 آزمون پایان فصل

### سؤال 1

تفاوت Event و Log چیست؟

### سؤال 2

Telemetry چه تفاوتی با Log سنتی دارد؟

### سؤال 3

چرا Event ID به‌تنهایی برای Investigation کافی نیست؟

### سؤال 4

Syslog چیست؟

### سؤال 5

Collector چه وظیفه‌ای دارد؟

### سؤال 6

تفاوت Parsing و Normalization چیست؟

### سؤال 7

چرا Raw Data می‌تواند در Investigation مهم باشد؟

### سؤال 8

Log Noise چگونه می‌تواند عملکرد SOC را کاهش دهد؟

### سؤال 9

اگر Logی در SIEM وجود نداشته باشد، چه احتمالاتی را باید بررسی کرد؟

### سؤال 10

تفاوت Event Time و Ingestion Time چیست؟

---

# 4.67 جمع‌بندی فصل

در این فصل یک مفهوم بنیادی را یاد گرفتیم:

> **SOC بدون Visibility نمی‌تواند به‌صورت قابل اعتماد تصمیم‌گیری کند.**

Visibility از طریق Data ایجاد می‌شود و Data از منابع مختلف می‌آید:

```text
Endpoint
Server
Network
Identity
Application
Cloud
```

این داده‌ها باید:

```text
Generate
   ↓
Collect
   ↓
Transport
   ↓
Parse
   ↓
Normalize
   ↓
Store
   ↓
Search
   ↓
Analyze
```

شوند.

همچنین دیدیم که یک SOC Analyst نباید صرفاً Alertها را ببیند.

او باید بفهمد:

> Alert بر چه داده‌ای ساخته شده است؟

و اگر داده وجود ندارد:

> چرا وجود ندارد؟

---

# نکته منتور

از اینجا به بعد، هرگاه در یک SIEM یا ابزار امنیتی چیزی مشاهده کردید، سه سؤال را به عادت ذهنی خود تبدیل کنید:

> **Source چیست؟**

> **این داده دقیقاً چه چیزی را نشان می‌دهد؟**

> **چه چیزی در این داده وجود ندارد که برای تصمیم‌گیری به آن نیاز دارم؟**

این سه سؤال ساده، در بسیاری از Investigationهای واقعی شما را از نتیجه‌گیری عجولانه نجات خواهند داد.

---

## فصل بعد

در **فصل پنجم — Windows Security Monitoring** وارد عمیق‌ترین بخش مانیتورینگ Windows در سطح Tier 1 می‌شویم. از Event IDهای مهم Authentication و Account Management شروع می‌کنیم، Logon Typeها را بررسی می‌کنیم، تفاوت Local و Remote Authentication را می‌آموزیم و با ساخت Timeline از Windows Events کار خواهیم کرد. سپس همین موارد را مستقیماً در `WIN-EP01` آزمایش می‌کنیم و آزمایشگاه را یک مرحله دیگر توسعه می‌دهیم.
