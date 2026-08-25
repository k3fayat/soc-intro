# فصل پنجم — Windows Security Monitoring

## 5.1 مقدمه

در فصل قبل یاد گرفتیم که SOC Analyst برای تصمیم‌گیری به **Visibility** و داده‌های امنیتی نیاز دارد. یکی از مهم‌ترین منابع این داده‌ها در بسیاری از سازمان‌ها، سیستم‌عامل Windows است.

برای یک تحلیلگر Tier 1، توانایی خواندن و تفسیر Windows Security Events یک مهارت پایه‌ای و ضروری است.

هدف این فصل این نیست که فهرستی از Event IDها را حفظ کنید. هدف این است که بتوانید از روی Eventها به سؤالات عملی پاسخ دهید:

* چه کسی وارد سیستم شده است؟
* ورود از کجا انجام شده است؟
* ورود Local بوده یا Remote؟
* Authentication موفق بوده یا ناموفق؟
* چه حسابی مورد استفاده قرار گرفته است؟
* آیا رفتار مشاهده‌شده عادی است؟
* آیا چند Event مختلف به یک Activity واحد مربوط‌اند؟
* برای تأیید یا رد یک فرضیه، چه Event دیگری باید بررسی شود؟

رویکرد ما در این فصل:

```text
Activity
   ↓
Windows generates Event
   ↓
Security Log
   ↓
Event Interpretation
   ↓
Correlation
   ↓
Timeline
   ↓
Analyst Decision
```

---

# 5.2 Windows Security Log

Windows چندین Event Channel دارد، اما برای Security Monitoring مهم‌ترین مورد:

```text
Security
```

است.

برای مشاهده آن می‌توان از **Event Viewer** استفاده کرد:

```text
Event Viewer
    ↓
Windows Logs
    ↓
Security
```

در محیط‌های سازمانی، این داده‌ها معمولاً توسط Agent یا Windows Event Forwarding به سیستم مرکزی منتقل می‌شوند.

بنابراین در آزمایشگاه ما:

```text
WIN-EP01
    ↓
Windows Security Log
    ↓
Collector
    ↓
Central Storage / SIEM
```

---

# 5.3 Audit چیست؟

**Auditing** یعنی ثبت فعالیت‌هایی که برای کنترل، بررسی و پاسخ‌گویی امنیتی اهمیت دارند.

Windows دارای مکانیزم‌های مختلف Audit Policy است.

مثلاً سازمان می‌تواند تعیین کند که فعالیت‌هایی مانند:

```text
Logon
Account Management
Process Creation
Object Access
Policy Change
Privilege Use
```

ثبت شوند.

نکته مهم:

> وجود یک قابلیت Audit در Windows به این معنی نیست که تمام Eventهای مربوط به آن الزاماً روی سیستم شما ثبت می‌شوند.

Configuration اهمیت دارد.

---

# 5.4 Audit Policy

Audit Policy مشخص می‌کند چه نوع فعالیت‌هایی ثبت شوند.

از دید مفهومی:

```text
User Activity
      ↓
Audit Policy
      ↓
Event Generation
```

اگر Audit مناسب فعال نباشد:

```text
User Activity
      ↓
No Event
      ↓
No Visibility
```

بنابراین یکی از اولین وظایف Security Administrator یا Detection Engineer، اطمینان از مناسب بودن Audit Configuration است.

برای Tier 1 Analyst نیز دانستن این موضوع ضروری است؛ زیرا هنگام مشاهده یک Gap نباید فوراً فرض کند که Activity رخ نداده است.

---

# 5.5 مهم‌ترین Event IDهای Authentication

در این فصل روی چند Event بسیار مهم تمرکز می‌کنیم.

| Event ID | مفهوم کلی                                |
| -------: | ---------------------------------------- |
|     4624 | Successful Logon                         |
|     4625 | Failed Logon                             |
|     4634 | Logoff                                   |
|     4647 | User Initiated Logoff                    |
|     4648 | Logon Using Explicit Credentials         |
|     4672 | Special Privileges Assigned to New Logon |

این‌ها را حفظ نکنید.

آن‌ها را به‌عنوان ابزار پاسخ به سؤال‌های Investigation ببینید.

---

# 5.6 Event ID 4624 — Successful Logon

Event ID:

```text
4624
```

معمولاً نشان‌دهنده یک Successful Logon است.

مثلاً:

```text
An account was successfully logged on.
```

اما سؤال مهم این است:

> «Successful Logon چگونه انجام شده است؟»

پاسخ را باید از فیلدهای Event استخراج کنیم.

---

# 5.7 فیلدهای مهم Event 4624

در یک 4624 معمولاً به دنبال اطلاعاتی مانند موارد زیر هستیم:

```text
Subject
Account
Logon Type
New Logon
Authentication Information
Network Information
Process Information
```

در Investigation معمولاً این موارد برای ما بسیار مهم‌اند:

```text
Target User
Domain
Logon Type
Source Network Address
Workstation Name
Authentication Package
Logon ID
```

---

# 5.8 Logon Type

یکی از مهم‌ترین مفاهیم Windows Authentication:

> **Logon Type**

است.

Logon Type مشخص می‌کند Authentication از چه نوعی بوده است.

مقادیر مهمی که برای Tier 1 باید بشناسید:

| Logon Type | مفهوم کلی         |
| ---------: | ----------------- |
|          2 | Interactive       |
|          3 | Network           |
|          4 | Batch             |
|          5 | Service           |
|          7 | Unlock            |
|          8 | NetworkCleartext  |
|          9 | NewCredentials    |
|         10 | RemoteInteractive |
|         11 | CachedInteractive |

این جدول را حفظ کردن کافی نیست.

باید بتوانید از آن در Investigation استفاده کنید.

---

# 5.9 Logon Type 2 — Interactive

Logon Type:

```text
2
```

معمولاً به **Interactive Logon** مربوط است.

مثلاً کاربر در مقابل سیستم نشسته و Credentials خود را وارد می‌کند.

سناریو:

```text
User
 ↓
Windows Login Screen
 ↓
Username + Password
 ↓
Successful Authentication
```

در چنین حالتی ممکن است Logon Type 2 مشاهده شود.

---

# 5.10 Logon Type 3 — Network

Logon Type:

```text
3
```

به **Network Logon** مربوط است.

مثلاً یک سیستم برای دسترسی به Resource روی سیستم دیگری Authentication انجام می‌دهد.

مثال:

```text
WIN-EP01
    |
    | SMB / Network Resource
    ↓
FILE-SRV01
```

ممکن است روی سیستم مقصد یک Network Logon مشاهده شود.

بنابراین:

> Logon Type 3 الزاماً به معنی آن نیست که یک انسان پشت سیستم مقصد Login کرده است.

این نکته برای جلوگیری از False Positive بسیار مهم است.

---

# 5.11 Logon Type 5 — Service

Logon Type:

```text
5
```

معمولاً به Service Logon مربوط است.

مثلاً یک Windows Service با یک Service Account اجرا می‌شود.

```text
Windows Service
      ↓
Service Account
      ↓
Logon Type 5
```

اگر هر Event Type 5 را به‌عنوان رفتار مشکوک در نظر بگیریم، تعداد زیادی False Positive خواهیم داشت.

---

# 5.12 Logon Type 7 — Unlock

Logon Type:

```text
7
```

معمولاً مربوط به Unlock شدن Workstation است.

سناریو:

```text
User
 ↓
Workstation Locked
 ↓
User unlocks workstation
```

این با Login اولیه تفاوت دارد.

---

# 5.13 Logon Type 10 — RemoteInteractive

یکی از مهم‌ترین موارد برای SOC:

```text
Logon Type 10
```

که معمولاً با **Remote Interactive Logon** مانند Remote Desktop Protocol یا RDP مرتبط است.

سناریو:

```text
Administrator
      ↓
RDP
      ↓
WIN-SERVER01
```

روی سیستم مقصد می‌توان Event مرتبط با Remote Interactive Logon مشاهده کرد.

---

# 5.14 چرا Logon Type مهم است؟

فرض کنید Alert زیر را داریم:

```text
Successful Logon
User: administrator
Time: 02:17
```

به‌تنهایی اطلاعات زیادی نداریم.

اما:

```text
User:
administrator

Time:
02:17

Logon Type:
10

Source IP:
10.10.20.55
```

اکنون می‌دانیم:

> یک Remote Interactive Logon ثبت شده است.

سؤال بعدی:

> آیا این Remote Login برای این User و این سیستم در این ساعت طبیعی است؟

این همان جایی است که تحلیل آغاز می‌شود.

---

# 5.15 Event ID 4625 — Failed Logon

Event ID:

```text
4625
```

معمولاً نشان‌دهنده Failed Logon است.

مثال:

```text
Account failed to log on.
```

یک Event 4625 به‌تنهایی Incident نیست.

ممکن است علت آن:

* Password اشتباه
* Credential قدیمی
* Service misconfiguration
* User error
* Script
* Brute Force
* Password Spraying
* Attack

باشد.

پس:

> **Failed Authentication ≠ Attack**

---

# 5.16 تحلیل Failed Logon

برای 4625 به موارد زیر توجه کنید:

```text
Target Account
Logon Type
Source Network Address
Workstation
Failure Reason
Status
SubStatus
Authentication Package
```

مثلاً:

```text
User:
admin

Source:
10.10.20.45

Logon Type:
10

Failure:
Bad Password
```

یک Event منفرد است.

حالا اگر داشته باشیم:

```text
02:10:01
admin
10.10.20.45

02:10:03
admin
10.10.20.45

02:10:05
admin
10.10.20.45

02:10:07
admin
10.10.20.45

02:10:10
admin
10.10.20.45
```

الگوی متفاوتی شکل گرفته است.

---

# 5.17 Brute Force

**Brute Force** یعنی تلاش مکرر برای حدس زدن Credentials.

الگوی ساده:

```text
Many Failed Logons
        ↓
Same Account
        ↓
Same Source
        ↓
Short Time Window
```

اما این تنها یک Hypothesis است.

برای تصمیم‌گیری باید Context بیشتری داشته باشیم.

---

# 5.18 Password Spraying

در **Password Spraying** مهاجم معمولاً یک یا چند Password رایج را روی تعداد زیادی Account امتحان می‌کند.

الگوی مفهومی:

```text
Source IP
   |
   +---- user01 → failure
   |
   +---- user02 → failure
   |
   +---- user03 → failure
   |
   +---- user04 → failure
```

این با Brute Force سنتی متفاوت است.

### Brute Force

```text
One Account
Many Password Attempts
```

### Password Spraying

```text
Many Accounts
Few Common Password Attempts
```

---

# 5.19 Event 4625 و Detection

فرض کنید SIEM Rule زیر را دارد:

```text
5 failed logons
within 5 minutes
for same account
from same source
```

این Rule ممکن است برای Brute Force طراحی شده باشد.

اما ممکن است:

```text
User forgot password
```

نیز همین الگو را تولید کند.

بنابراین Alert فقط:

> **Signal**

است، نه Verdict.

---

# 5.20 Event ID 4634 — Logoff

Event:

```text
4634
```

معمولاً به Logoff مربوط است.

در Investigation می‌تواند به ساختن Session Timeline کمک کند.

مثلاً:

```text
09:00
4624 Successful Logon

12:30
4634 Logoff
```

این اطلاعات برای درک Session مفید هستند.

---

# 5.21 Event ID 4647 — User-Initiated Logoff

Event:

```text
4647
```

معمولاً نشان‌دهنده Logoffی است که توسط User Initiated شده است.

تفاوت مفهومی آن با 4634 مهم است:

```text
4647
User initiated logoff

4634
Logon session was logged off
```

در Investigationهای دقیق ممکن است هر دو Event اهمیت داشته باشند.

---

# 5.22 Event ID 4648 — Explicit Credentials

یکی از Eventهای مهم:

```text
4648
```

این Event زمانی اهمیت پیدا می‌کند که Process یا User از Credentials مشخصی به‌صورت Explicit استفاده کرده باشد.

مفهوم:

```text
Existing Session
      +
Explicit Credentials
      ↓
Authentication Attempt
```

این Event در برخی سناریوهای:

* Administrative Activity
* Lateral Movement
* Credential Use

می‌تواند بسیار ارزشمند باشد.

اما باز هم:

> 4648 به‌تنهایی اثبات‌کننده حمله نیست.

---

# 5.23 Event ID 4672 — Special Privileges

Event:

```text
4672
```

معمولاً نشان‌دهنده اختصاص برخی Special Privileges به یک New Logon است.

این Event به‌خصوص زمانی اهمیت دارد که حساب دارای سطح دسترسی بالا باشد.

مثلاً:

```text
Administrator
Domain Admin
Service Account
```

اما باز هم باید Context را بررسی کنیم.

اگر Domain Administrator هر روز در ساعت کاری روی Domain Controller Login کند، وجود 4672 لزوماً غیرعادی نیست.

---

# 5.24 مفهوم Privileged Account

**Privileged Account** حسابی است که دسترسی بالاتری نسبت به User عادی دارد.

نمونه:

```text
Local Administrator
Domain Administrator
Service Account
Database Administrator
Security Administrator
```

چنین Accountهایی باید Monitoring دقیق‌تری داشته باشند.

چرا؟

زیرا:

```text
Compromised Normal Account
       ↓
Limited Impact

Compromised Privileged Account
       ↓
Potentially High Impact
```

---

# 5.25 Account Management

Authentication تنها بخشی از Windows Security Monitoring است.

Account Management نیز اهمیت بالایی دارد.

Eventهای مهم شامل مواردی مانند:

```text
4720
4722
4723
4724
4725
4726
4738
4740
```

هستند.

این Eventها می‌توانند به Activityهایی مانند:

```text
User Creation
Account Enable
Password Change
Password Reset
Account Disable
Account Delete
Account Change
Account Lockout
```

مرتبط باشند.

---

# 5.26 Event ID 4720 — User Account Created

Event:

```text
4720
```

معمولاً نشان‌دهنده ایجاد User Account است.

سناریو:

```text
Administrator
      ↓
Creates Account
      ↓
New User
```

در SOC سؤال مهم:

> چه کسی Account را ایجاد کرده و چرا؟

---

# 5.27 Event ID 4740 — Account Lockout

Event:

```text
4740
```

برای Account Lockout بسیار مهم است.

مثلاً:

```text
User:
ali

Account locked
```

اما باید بررسی کنیم:

> چه چیزی باعث Lockout شده؟

ممکن است:

* User چند بار Password اشتباه وارد کرده باشد.
* Mobile Device دارای Password قدیمی باشد.
* Service با Credential قدیمی اجرا شود.
* Script اشتباه داشته باشیم.
* مهاجم Passwordهای مختلف را امتحان کرده باشد.

---

# 5.28 Lockout Investigation

سناریو:

```text
08:00
User Account Locked
```

به‌عنوان Tier 1 Analyst نباید فقط Event 4740 را ببینیم.

باید بررسی کنیم:

```text
Previous Failed Logons
Source
Workstation
Time Pattern
Other Accounts
```

مثلاً:

```text
07:58
user01 failed

07:58
user01 failed

07:59
user01 failed

08:00
user01 locked
```

در اینجا یک الگوی منطقی شکل می‌گیرد.

---

# 5.29 Timeline

یکی از مهم‌ترین مهارت‌های SOC Analyst:

> ساختن Timeline

است.

به‌جای دیدن Eventها به‌صورت جداگانه:

```text
4625
4625
4625
4740
4624
```

آن‌ها را به‌صورت زنجیره زمانی ببینید:

```text
10:00:01 Failed Login
10:00:03 Failed Login
10:00:05 Failed Login
10:00:07 Account Locked
10:02:15 Admin Reset Password
10:05:20 Successful Login
```

اکنون یک Story داریم.

---

# 5.30 Timeline در Investigation

Timeline باید حداقل شامل موارد زیر باشد:

| Time     | Host     | User | Event | Source     | Interpretation |
| -------- | -------- | ---- | ----- | ---------- | -------------- |
| 10:00:01 | WIN-EP01 | ali  | 4625  | 10.10.10.5 | Failed         |
| 10:00:03 | WIN-EP01 | ali  | 4625  | 10.10.10.5 | Failed         |
| 10:00:05 | WIN-EP01 | ali  | 4625  | 10.10.10.5 | Failed         |
| 10:00:07 | WIN-EP01 | ali  | 4740  | DC01       | Locked         |

این جدول می‌تواند پایه تصمیم‌گیری Tier 1 باشد.

---

# 5.31 Correlation

**Correlation** یعنی مرتبط کردن Eventهای مختلف برای کشف یک الگو.

مثلاً:

```text
4625
   +
4625
   +
4625
   +
4740
```

ممکن است نشان‌دهنده:

```text
Repeated Authentication Failure
```

باشد.

اما اگر:

```text
4624
   +
4624
   +
4672
```

ببینیم، ممکن است یک Administrator Session مشروع باشد.

پس:

> Correlation بدون Context کافی نیست.

---

# 5.32 Context

Context یعنی اطلاعات پیرامونی که معنی Event را مشخص می‌کند.

مثلاً:

```text
Event:
4624
```

Context:

```text
User:
administrator

Time:
03:12

Source:
Unknown workstation

Logon Type:
10

Destination:
Domain Controller

Previous Activity:
Multiple 4625
```

اکنون Event بسیار مهم‌تر می‌شود.

---

# 5.33 Baseline

**Baseline** یعنی شناخت رفتار معمول محیط.

مثلاً:

```text
administrator
normally logs in
08:00–18:00
from jump-server
```

حالا:

```text
administrator
03:00
from workstation-unknown
```

بیشتر جلب توجه می‌کند.

اما بدون Baseline:

```text
03:00 Login
```

به‌تنهایی معنای قطعی ندارد.

---

# 5.34 Anomaly

**Anomaly** یعنی رفتار غیرعادی نسبت به الگوی مورد انتظار.

مثال:

```text
Normal:
Admin → Jump Server → DC

Observed:
Admin → User Workstation → DC
```

این تفاوت می‌تواند یک Signal امنیتی باشد.

ولی:

> Anomaly ≠ Maliciousness

رفتار غیرعادی باید Investigation شود.

---

# 5.35 Source IP

در Authentication Eventها یکی از مهم‌ترین فیلدها:

```text
Source Network Address
```

است.

مثلاً:

```text
10.10.10.20
```

اما توجه کنید:

> Source IP الزاماً هویت واقعی مهاجم نیست.

ممکن است:

* NAT
* Proxy
* VPN
* Jump Host
* Load Balancer

در مسیر وجود داشته باشد.

پس:

```text
Source IP
```

را به‌عنوان:

> نقطه مشاهده‌شده در Network Path

در نظر بگیرید، نه الزاماً شخص مهاجم.

---

# 5.36 Internal IP هم می‌تواند مهم باشد

بعضی تحلیلگران فقط IPهای Public را مشکوک می‌دانند.

این اشتباه است.

ممکن است حمله:

```text
Internal Host
     ↓
Internal Host
```

باشد.

این سناریو می‌تواند مربوط به:

* Lateral Movement
* Credential Abuse
* Insider Activity
* Compromised Endpoint

باشد.

بنابراین:

> Internal ≠ Trusted

---

# 5.37 Workstation Name

در بعضی Eventها اطلاعاتی درباره Workstation یا Computer نیز وجود دارد.

مثلاً:

```text
Workstation:
WS-023
```

این می‌تواند به ما کمک کند بفهمیم Authentication از کدام سیستم انجام شده است.

اما باید با سایر فیلدها Correlate شود.

---

# 5.38 Authentication Package

در Windows Authentication با مفاهیمی مانند:

```text
Kerberos
NTLM
Negotiate
```

مواجه می‌شویم.

برای Tier 1 لازم نیست در این فصل وارد جزئیات عمیق پروتکل‌های Authentication شویم.

اما باید بدانید:

> Authentication Package بخشی از Context مربوط به نحوه Authentication است.

در فصل‌های بعد، Kerberos و NTLM را عمیق‌تر بررسی خواهیم کرد.

---

# 5.39 یک اصل مهم

فرض کنید Alert:

```text
Multiple Failed Logons
```

داریم.

نباید بگوییم:

> «Brute Force Attack detected.»

بلکه بهتر است بگوییم:

> «Multiple authentication failures were observed and require investigation.»

چرا؟

زیرا تحلیلگر باید بین:

```text
Observed Evidence
```

و:

```text
Interpretation
```

تفاوت بگذارد.

---

# 5.40 Evidence vs Hypothesis

### Evidence

```text
12 failed logons
from 10.10.20.15
against 4 accounts
within 3 minutes
```

### Hypothesis

```text
Possible password spraying activity
```

Evidence را با Hypothesis اشتباه نگیرید.

---

# 5.41 تمرین عملی 1 — مشاهده Event 4624

روی `WIN-EP01`:

1. Logout کنید.
2. دوباره Login کنید.
3. Event Viewer را باز کنید.
4. به:

```text
Windows Logs
→ Security
```

بروید.
5. Event ID زیر را پیدا کنید:

```text
4624
```

6. این فیلدها را ثبت کنید:

```text
Time
Account Name
Logon Type
Source Network Address
Workstation Name
Authentication Package
Logon ID
```

جدول:

| Field        | Value |
| ------------ | ----- |
| Time         |       |
| Account      |       |
| Logon Type   |       |
| Source       |       |
| Workstation  |       |
| Auth Package |       |
| Logon ID     |       |

---

# 5.42 تمرین عملی 2 — ایجاد Failed Logon

یک Username معتبر ولی Password اشتباه وارد کنید.

سپس Event:

```text
4625
```

را پیدا کنید.

موارد زیر را استخراج کنید:

```text
Account
Source
Logon Type
Failure Reason
Status
SubStatus
```

سپس پاسخ دهید:

> آیا یک 4625 به‌تنهایی برای ایجاد Incident کافی است؟

---

# 5.43 تمرین عملی 3 — ساخت الگوی Brute Force

در محیط آزمایشگاهی، چند بار Password اشتباه وارد کنید.

مثلاً:

```text
5 Attempts
within a short time
```

سپس Timeline بسازید:

```text
Time
Event ID
Account
Source
Result
```

بعد بررسی کنید:

> آیا تمام Failureها از یک Source آمده‌اند؟

> آیا فاصله زمانی آن‌ها مشابه است؟

> آیا Account یکسان است؟

---

# 5.44 تمرین عملی 4 — Account Lockout

در صورت امکان، یک Account آزمایشگاهی را به‌گونه‌ای تنظیم کنید که پس از چند تلاش ناموفق Lock شود.

سپس Event:

```text
4740
```

را پیدا کنید.

بررسی کنید:

```text
Locked Account
Caller Computer
Time
```

سپس Eventهای قبلی را بررسی کنید.

هدف:

> ارتباط بین 4625 و 4740 را کشف کنید.

---

# 5.45 تمرین عملی 5 — Remote Logon

در آزمایشگاه، در صورت داشتن سیستم دوم و سرویس Remote Desktop، یک Remote Authentication ایجاد کنید.

روی سیستم مقصد Event:

```text
4624
```

را بررسی کنید.

به:

```text
Logon Type
Source Network Address
Target Account
```

توجه کنید.

هدف:

> تشخیص Remote Interactive Logon.

**نکته:** فعال‌سازی RDP را فقط در شبکه آزمایشگاهی انجام دهید و آن را مستقیماً روی اینترنت قرار ندهید.

---

# 5.46 تمرین عملی 6 — Explicit Credentials

در محیط آزمایشگاهی، یک Activity مدیریتی ایجاد کنید که از Explicit Credentials استفاده کند.

سپس Event:

```text
4648
```

را جست‌وجو کنید.

بررسی کنید:

```text
Account
Target Server
Process
Time
```

سپس توضیح دهید:

> چرا 4648 می‌تواند در Investigationهای Credential Usage مهم باشد؟

---

# 5.47 تمرین عملی 7 — Privileged Logon

با یک Account مدیریتی آزمایشگاهی Login کنید.

سپس Event:

```text
4672
```

را جست‌وجو کنید.

سؤال:

> آیا مشاهده 4672 به‌تنهایی نشان‌دهنده فعالیت مخرب است؟

پاسخ را با استفاده از مفهوم:

```text
Context
Baseline
Account Role
Time
Source
```

توضیح دهید.

---

# 5.48 تمرین عملی 8 — Account Creation

یک Account آزمایشگاهی ایجاد کنید.

سپس Event:

```text
4720
```

را پیدا کنید.

این موارد را ثبت کنید:

```text
Subject Account
New Account
Time
Domain
```

سپس بنویسید:

> اگر همین Event برای یک Domain Administrator در ساعت 03:00 و بدون Change Request مشاهده شود، چه سؤالاتی باید از تیم مربوطه پرسیده شود؟

---

# 5.49 تمرین عملی 9 — تحلیل یک سناریوی واقعی‌نما

فرض کنید SIEM این Eventها را نشان می‌دهد:

```text
02:14:01
4625
User: administrator
Source: 10.10.20.55

02:14:04
4625
User: administrator
Source: 10.10.20.55

02:14:07
4625
User: administrator
Source: 10.10.20.55

02:14:10
4625
User: administrator
Source: 10.10.20.55

02:14:15
4624
User: administrator
Source: 10.10.20.55
Logon Type: 10
```

### وظیفه

بدون استفاده از عبارت‌هایی مانند «قطعاً هک شده»:

1. Evidence را بنویسید.
2. Hypothesis را بنویسید.
3. ریسک را ارزیابی کنید.
4. چه داده‌های دیگری نیاز دارید؟
5. چه کسی باید Alert را بررسی کند؟
6. آیا در سطح Tier 1 می‌توانید Incident را Close کنید؟

---

# 5.50 پاسخ تحلیلی نمونه

### Evidence

```text
Four failed authentication attempts
followed by a successful Remote Interactive Logon
for administrator
from the same source IP.
```

### Hypothesis

```text
Possible credential attack or unauthorized remote access.
```

### Data مورد نیاز

```text
Source Host Identity
Historical Login Pattern
EDR Telemetry
Process Creation
Previous Authentication Events
Account Baseline
Other Accounts targeted from same source
```

### نتیجه

این داده‌ها برای ایجاد یک **Security Investigation** کافی هستند، اما به‌تنهایی برای اثبات Compromise کافی نیستند.

---

# 5.51 تمرین پیشرفته — چند Account

سناریو:

```text
02:00
user01 → Failed

02:01
user02 → Failed

02:02
user03 → Failed

02:03
user04 → Failed

02:04
administrator → Failed
```

همه از:

```text
10.10.20.55
```

هستند.

سؤال:

> آیا این الگو بیشتر به Brute Force شبیه است یا Password Spraying؟

پاسخ خود را با Evidence توضیح دهید.

---

# 5.52 تمرین پیشرفته — تشخیص False Positive

سناریو:

```text
Every morning at 08:00

Service Account
Logon Type 5
Source = expected server
```

روزانه چندین Event ایجاد می‌شود.

Alert:

```text
Repeated Successful Logons
```

ایجاد شده است.

### سؤال

آیا این رفتار الزاماً Incident است؟

چه اطلاعاتی برای Tuning Detection نیاز دارید؟

---

# 5.53 تمرین پیشرفته — تحلیل Timeline

Timeline زیر را تحلیل کنید:

```text
01:10
4625
admin
10.10.50.20

01:11
4625
admin
10.10.50.20

01:12
4624
admin
10.10.50.20
Logon Type 10

01:13
4688
powershell.exe

01:14
DNS Query
suspicious-domain.example

01:15
Outbound Connection
10.10.50.20 → External IP
```

در این سناریو، دیگر فقط یک Authentication Event نداریم.

یک زنجیره شکل گرفته است:

```text
Failed Authentication
        ↓
Successful Remote Logon
        ↓
PowerShell
        ↓
Suspicious DNS
        ↓
External Connection
```

این نوع Correlation ارزش Investigation را بسیار افزایش می‌دهد.

---

# 5.54 یک نکته مهم درباره Event ID 4688

در سناریوی قبلی از:

```text
4688
```

استفاده کردیم.

این Event معمولاً با:

> **Process Creation**

مرتبط است.

در فصل بعدی Process Monitoring را به‌صورت کامل بررسی خواهیم کرد.

هدف فعلی فقط این است که ببینید چگونه:

```text
Authentication Event
+
Process Event
```

می‌توانند در یک Timeline قرار گیرند.

---

# 5.55 توسعه آزمایشگاه

تا پایان فصل چهارم:

```text
WIN-EP01
LIN-SRV01
LOG01
```

داشتیم.

اکنون معماری منطقی آزمایشگاه:

```text
                    SOC-LAB
                       |
          ┌────────────┴────────────┐
          │       LAB Network       │
          │    192.168.100.0/24     │
          └────────────┬────────────┘
                       |
       ┌───────────────┼────────────────┐
       │               │                │
   WIN-EP01        LIN-SRV01          LOG01
    .10               .20               .30
       │               │                │
       │               │                │
       └─────── Windows/Linux Logs ─────┘
                       │
                       ↓
                 Central Logs
```

---

# 5.56 Snapshot جدید

پس از اطمینان از عملکرد آزمایشگاه:

```text
Snapshot:
CH05-WINDOWS-LOGGING
```

ایجاد کنید.

این Snapshot بسیار ارزشمند است.

اگر در مراحل بعدی Configuration را خراب کردید، می‌توانید به این وضعیت بازگردید.

---

# 5.57 ساخت Lab Dataset

از این فصل به بعد بهتر است یک Dataset واقعی از Eventهای آزمایشگاهی خود داشته باشید.

مثلاً:

```text
lab-events/
├── authentication/
├── account-management/
├── process/
├── network/
└── dns/
```

برای هر سناریو یک فایل یا Export از Eventهای مرتبط نگهداری کنید.

هدف این نیست که صرفاً فایل جمع کنید.

هدف این است که بتوانید بعداً:

```text
Event
→ Query
→ Detection
→ Investigation
```

را روی داده‌های واقعی آزمایشگاه تمرین کنید.

---

# 5.58 قالب ثبت Investigation

از این فصل یک فرم ثابت برای Investigation استفاده کنید:

```text
Alert ID:
Date/Time:

Affected Host:

User:

Source IP:

Destination:

Event IDs:

Observed Activity:

Initial Hypothesis:

Supporting Evidence:

Contradicting Evidence:

Additional Data Required:

Analyst Assessment:

Recommended Action:

Escalation Required:
Yes / No

Reason:
```

این قالب را در تمام فصل‌های آینده استفاده خواهیم کرد.

---

# 5.59 Tier 1 Perspective

Tier 1 Analyst معمولاً قرار نیست در همان لحظه تمام حقیقت را کشف کند.

وظیفه اصلی او:

```text
Detect
Validate
Enrich
Triage
Document
Escalate
```

است.

در Windows Authentication Monitoring یعنی:

```text
Alert
 ↓
Identify Event
 ↓
Identify User
 ↓
Identify Source
 ↓
Identify Logon Type
 ↓
Check Frequency
 ↓
Check Baseline
 ↓
Correlate Events
 ↓
Assess Severity
 ↓
Escalate / Close
```

---

# 5.60 اشتباهات رایج تحلیلگران تازه‌کار

### اشتباه اول

> هر 4625 یعنی Brute Force.

اشتباه.

### اشتباه دوم

> هر 4624 یعنی Login انسانی.

اشتباه.

### اشتباه سوم

> هر Logon Type 10 یعنی حمله.

اشتباه.

### اشتباه چهارم

> هر 4672 یعنی Privilege Escalation.

اشتباه.

### اشتباه پنجم

> Internal IP قابل اعتماد است.

اشتباه.

### اشتباه ششم

> Event ID به‌تنهایی کافی است.

اشتباه.

### اشتباه هفتم

> Alert = Incident

اشتباه.

---

# 5.61 مدل ذهنی صحیح

به‌جای:

```text
Event ID
   ↓
Conclusion
```

از این مدل استفاده کنید:

```text
Event
  ↓
Fields
  ↓
Context
  ↓
Correlation
  ↓
Baseline
  ↓
Hypothesis
  ↓
Additional Evidence
  ↓
Assessment
```

این تفاوت اصلی بین:

> Event Reader

و:

> SOC Analyst

است.

---

# 5.62 چک‌لیست مهارتی فصل

پیش از عبور از این فصل باید بتوانید:

* [ ] Windows Security Log را پیدا کنید.
* [ ] Audit Policy را به‌صورت مفهومی توضیح دهید.
* [ ] Event ID 4624 را تفسیر کنید.
* [ ] Event ID 4625 را تفسیر کنید.
* [ ] Logon Typeهای 2، 3، 5، 7 و 10 را توضیح دهید.
* [ ] Eventهای 4634 و 4647 را تفکیک کنید.
* [ ] Event 4648 را توضیح دهید.
* [ ] Event 4672 را تفسیر کنید.
* [ ] Event 4720 را بررسی کنید.
* [ ] Event 4740 را بررسی کنید.
* [ ] Brute Force را از Password Spraying تفکیک کنید.
* [ ] Source IP را در Context تحلیل کنید.
* [ ] Timeline ایجاد کنید.
* [ ] Correlation انجام دهید.
* [ ] Evidence و Hypothesis را از هم جدا کنید.
* [ ] False Positive احتمالی را شناسایی کنید.
* [ ] Investigation را مستندسازی کنید.

---

# 5.63 آزمون پایان فصل

### سؤال 1

Event ID 4624 چه مفهومی دارد؟

### سؤال 2

چرا مشاهده یک Event 4625 به‌تنهایی برای اعلام Brute Force کافی نیست؟

### سؤال 3

تفاوت Logon Type 2 و 10 چیست؟

### سؤال 4

Logon Type 3 چه کاربردی در Investigation دارد؟

### سؤال 5

چرا Logon Type 5 معمولاً نباید بدون Context به‌عنوان رفتار مشکوک در نظر گرفته شود؟

### سؤال 6

تفاوت Brute Force و Password Spraying چیست؟

### سؤال 7

Event 4740 چه اطلاعاتی می‌تواند در اختیار Analyst قرار دهد؟

### سؤال 8

Event 4648 چرا در Credential Investigation اهمیت دارد؟

### سؤال 9

چرا Event 4672 به‌تنهایی نشان‌دهنده Privilege Escalation نیست؟

### سؤال 10

تفاوت Evidence و Hypothesis چیست؟

### سؤال 11

چرا Internal IP را نباید ذاتاً Trusted فرض کرد؟

### سؤال 12

در یک Investigation، چرا Timeline اهمیت دارد؟

---

# 5.64 پروژه عملی پایان فصل

سناریوی نهایی:

در `WIN-EP01` یک Account آزمایشگاهی ایجاد کنید.

سپس عمداً:

1. چند Authentication Failure ایجاد کنید.
2. در صورت امکان Account را Lock کنید.
3. Lockout را بررسی کنید.
4. Authentication موفق ایجاد کنید.
5. یک Remote Authentication آزمایشگاهی انجام دهید.
6. Eventهای مربوطه را جمع‌آوری کنید.
7. آن‌ها را در `LOG01` مشاهده کنید.
8. Timeline بسازید.
9. Eventها را Correlate کنید.
10. یک گزارش Tier 1 بنویسید.

گزارش باید حداقل شامل این بخش‌ها باشد:

```text
Executive Summary

Affected Host

Affected Account

Source

Timeline

Observed Events

Evidence

Initial Assessment

Alternative Explanation

Additional Evidence Required

Severity

Escalation Decision

Analyst Notes
```

---

# جمع‌بندی فصل

در این فصل از حفظ Event IDها فراتر رفتیم و یاد گرفتیم چگونه Windows Security Events را به‌عنوان **Evidence** تحلیل کنیم.

مهم‌ترین زنجیره‌ای که باید از این فصل در ذهن شما باقی بماند:

```text
Authentication Activity
        ↓
Windows Security Event
        ↓
Event Fields
        ↓
Logon Type
        ↓
Source
        ↓
Timeline
        ↓
Correlation
        ↓
Context / Baseline
        ↓
Hypothesis
        ↓
Additional Evidence
        ↓
Tier 1 Assessment
```

اگر این زنجیره را واقعاً در آزمایشگاه اجرا کنید، دیگر Event IDهای Windows صرفاً اعداد حفظ‌شده نخواهند بود؛ بلکه به ابزارهایی برای پاسخ به سؤالات Investigation تبدیل می‌شوند.

**فصل بعدی — Process & Endpoint Monitoring** وارد یکی از مهم‌ترین مهارت‌های عملی SOC Tier 1 می‌شود: تشخیص Processهای غیرعادی، Parent/Child Process، Command Line، PowerShell، Process Creation Eventها و ساختن Process Tree. در آن فصل `4688` را از یک Event ID ساده به یک ابزار واقعی برای تحلیل رفتار Endpoint تبدیل خواهیم کرد.
