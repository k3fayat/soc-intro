# ماژول اول — مبانی عملیات امنیت و نقش SOC

# فصل سوم — طراحی و ساخت آزمایشگاه مجازی SOC

## اهداف یادگیری

در پایان این فصل، مخاطب باید بتواند:

* مفهوم **Security Lab** را درک کند.
* معماری آزمایشگاه دوره را طراحی کند.
* اجزای آزمایشگاه را متناسب با نیاز آموزشی مرحله‌بندی کند.
* یک محیط مجازی ایزوله و قابل بازگردانی ایجاد کند.
* سیستم‌های Windows و Linux موردنیاز آزمایشگاه را آماده کند.
* شبکه آزمایشگاهی را از شبکه واقعی جدا کند.
* مفهوم Snapshot و Baseline را در محیط آزمایشگاهی به‌کار ببرد.
* یک Windows Endpoint و یک Linux Server را برای تولید Telemetry آماده کند.
* اولین Eventهای امنیتی را تولید و مشاهده کند.
* اهمیت Time Synchronization، Naming و IP Addressing را در آزمایشگاه امنیتی درک کند.
* آزمایشگاه را به‌گونه‌ای طراحی کند که در فصل‌های بعدی بتوان SIEM، EDR، Syslog، Active Directory و سایر اجزا را به آن اضافه کرد.

---

# 3.1 مقدمه

از این فصل به بعد، کتاب از یک آموزش صرفاً نظری فاصله می‌گیرد.

هدف این دوره تربیت فردی نیست که بتواند تعاریف زیر را حفظ کند:

> SIEM چیست؟
> EDR چیست؟
> Brute Force چیست؟

هدف این است که مخاطب بتواند در مقابل یک رخداد واقعی قرار بگیرد و بگوید:

> «این Event کجا تولید شده؟ چه داده‌ای در اختیار دارم؟ چگونه آن را جمع‌آوری کنم؟ چگونه تحلیل کنم؟ و بر اساس آن چه تصمیمی بگیرم؟»

برای رسیدن به این هدف، به یک **آزمایشگاه عملیاتی SOC** نیاز داریم.

اما قرار نیست از همان ابتدا یک محیط پیچیده با ده‌ها سیستم ایجاد کنیم.

اگر در اولین مرحله چنین محیطی بسازیم، بخش زیادی از زمان صرف مدیریت Infrastructure خواهد شد و هدف آموزشی گم می‌شود.

بنابراین آزمایشگاه را به‌صورت **Progressive Lab** می‌سازیم.

یعنی:

```text
مرحله 1
Windows + Linux
       ↓
مرحله 2
Logging
       ↓
مرحله 3
Centralized Logging
       ↓
مرحله 4
SIEM
       ↓
مرحله 5
Active Directory
       ↓
مرحله 6
Endpoint Telemetry / EDR
       ↓
مرحله 7
Network Monitoring
       ↓
مرحله 8
Attack Simulation
       ↓
مرحله 9
Full SOC Lab
```

هر مرحله دقیقاً زمانی توسعه داده می‌شود که محتوای آموزشی به آن نیاز پیدا کند.

---

# 3.2 فلسفه طراحی آزمایشگاه

آزمایشگاه این دوره پنج ویژگی اصلی خواهد داشت:

## 1. Isolated

محیط آزمایشگاهی باید تا حد امکان از محیط واقعی جدا باشد.

## 2. Reproducible

مخاطب باید بتواند محیط را خراب کند و دوباره آن را ایجاد یا Restore کند.

## 3. Observable

فعالیت‌های انجام‌شده باید قابل مشاهده و Logging باشند.

## 4. Incremental

آزمایشگاه به‌صورت مرحله‌ای رشد کند.

## 5. Safe

تمرین‌های امنیتی باید در محیط کنترل‌شده انجام شوند.

---

# 3.3 چرا آزمایشگاه باید Isolated باشد؟

در آموزش SOC، ممکن است عمداً فعالیت‌هایی ایجاد کنیم که در محیط واقعی خطرناک باشند.

برای مثال:

* ایجاد تعداد زیادی Login Failure
* اجرای Processهای غیرمعمول
* ایجاد Connectionهای آزمایشی
* تولید DNS Queryهای خاص
* شبیه‌سازی رفتارهای مهاجم
* اجرای ابزارهای تست امنیت

اگر این فعالیت‌ها روی سیستم واقعی سازمان انجام شوند، ممکن است:

* Alert واقعی ایجاد کنند.
* باعث اختلال شوند.
* حساب کاربری Lock شود.
* اطلاعات واقعی تحت تأثیر قرار گیرد.
* ابزارهای امنیتی سازمان فعال شوند.

بنابراین:

> تمام تمرین‌های این دوره باید در محیط آزمایشگاهی کنترل‌شده انجام شوند.

---

# 3.4 معماری کلی آزمایشگاه

در ابتدای دوره فقط دو سیستم خواهیم داشت:

```text
                 SOC LAB
        ┌─────────────────────┐
        │    Isolated LAN     │
        │   192.168.100.0/24  │
        └──────────┬──────────┘
                   │
          ┌────────┴────────┐
          │                 │
     ┌────▼─────┐      ┌────▼─────┐
     │ Windows  │      │  Linux   │
     │ Endpoint │      │  Server  │
     │          │      │          │
     │192.168.  │      │192.168.  │
     │100.10    │      │100.20    │
     └──────────┘      └──────────┘
```

در مراحل بعدی:

```text
                    SOC LAB
                       |
                 Lab Network
                       |
      ┌────────┬───────┼────────┬─────────┐
      │        │       │        │         │
   Windows   Linux    AD/DC    SIEM      SOC
   Endpoint  Server            Server    Analyst
```

و در مراحل پیشرفته‌تر:

```text
                         SOC LAB
                            |
        ┌───────────────────┼──────────────────┐
        │                   │                  │
   User Network        Server Network     Security Network
        │                   │                  │
   Workstations        Servers/DC         SIEM/EDR
        │                   │                  │
        └───────────────────┼──────────────────┘
                            │
                       Monitoring
```

---

# 3.5 انتخاب Virtualization Platform

برای ساخت آزمایشگاه به یک **Hypervisor** نیاز داریم.

Hypervisor نرم‌افزاری است که امکان اجرای Virtual Machineها را فراهم می‌کند.

دو نوع کلی دارد:

### Type 1

مستقیماً روی Hardware اجرا می‌شود.

مثلاً:

* VMware ESXi
* Microsoft Hyper-V Server
* Proxmox VE

### Type 2

روی یک سیستم‌عامل موجود اجرا می‌شود.

مثلاً:

* VMware Workstation
* Oracle VirtualBox

برای یک آزمایشگاه آموزشی شخصی، Type 2 معمولاً شروع ساده‌تری دارد.

اما اگر سیستم قدرتمندی در اختیار دارید، استفاده از یک Hypervisor Type 1 نیز کاملاً منطقی است.

در این کتاب تمرکز ما روی **مفاهیم مستقل از Hypervisor** است.

بنابراین ساختار آزمایشگاه نباید وابسته به یک محصول خاص باشد.

---

# 3.6 منابع سخت‌افزاری

نیازمندی دقیق به مرحله آزمایشگاه بستگی دارد.

برای مرحله اول، محیط بسیار ساده است.

یک سیستم Host با منابع مناسب برای اجرای همزمان چند VM نیاز داریم.

حداقل منطقی برای شروع:

```text
CPU:
4 Physical Cores یا بیشتر

RAM:
16 GB یا بیشتر

Storage:
حداقل 150 GB فضای آزاد

Virtualization:
Enabled
```

اما برای ادامه دوره، به‌خصوص زمانی که SIEM و چندین سرویس اضافه می‌شوند، بهتر است:

```text
RAM:
32 GB یا بیشتر

CPU:
8 Core یا بیشتر

Storage:
250 GB+
```

در نظر گرفته شود.

این اعداد **پیشنهاد آزمایشگاهی** هستند و بسته به محصول، نسخه سیستم‌عامل و تعداد VMها تغییر می‌کنند.

---

# 3.7 چرا RAM اهمیت زیادی دارد؟

در آزمایشگاه SOC ممکن است همزمان سیستم‌های زیر فعال باشند:

```text
Windows Endpoint
Windows Server
Domain Controller
Linux Server
SIEM
Security Tools
Attack Simulation Host
```

مثلاً اگر:

```text
Windows = 4 GB
DC = 4 GB
Linux = 2 GB
SIEM = 8 GB
```

تنها VMها:

```text
18 GB
```

RAM نیاز دارند.

Host نیز RAM می‌خواهد.

بنابراین کمبود RAM یکی از رایج‌ترین مشکلات آزمایشگاه‌های SOC است.

---

# 3.8 طراحی شبکه آزمایشگاه

یکی از مهم‌ترین تصمیم‌ها، نوع Network است.

ما یک شبکه مجازی اختصاصی ایجاد می‌کنیم:

```text
Network Name:
SOC-LAB

Network:
192.168.100.0/24
```

نمونه Address Plan:

| سیستم                    | IP             |
| ------------------------ | -------------- |
| Windows Endpoint         | 192.168.100.10 |
| Linux Server             | 192.168.100.20 |
| آینده: Domain Controller | 192.168.100.30 |
| آینده: SIEM              | 192.168.100.40 |
| آینده: Security VM       | 192.168.100.50 |

در این مرحله Gateway الزامی نیست.

---

# 3.9 چرا فعلاً Internet را حذف می‌کنیم؟

برای بسیاری از تمرین‌های اولیه، Internet لازم نیست.

داشتن Internet در آزمایشگاه دو مشکل ایجاد می‌کند:

### مشکل اول — ایمنی

ممکن است فعالیت آزمایشی ناخواسته از محیط خارج شود.

### مشکل دوم — کنترل

اگر یک سیستم به Internet دسترسی داشته باشد، تحلیل رفتار Network آن پیچیده‌تر می‌شود.

بنابراین در مراحل اولیه:

```text
SOC-LAB
   |
Isolated Network
   |
No Internet
```

را ترجیح می‌دهیم.

هر زمان نیاز به Internet باشد، آن را به‌صورت کنترل‌شده و با هدف مشخص اضافه می‌کنیم.

---

# 3.10 Network Modes

در Hypervisorها معمولاً با مفاهیمی مانند موارد زیر مواجه می‌شویم:

### NAT

VM از طریق Host به شبکه خارجی دسترسی پیدا می‌کند.

### Bridged

VM مانند یک سیستم واقعی وارد شبکه فیزیکی می‌شود.

### Host-Only

VMها و Host می‌توانند با یکدیگر ارتباط داشته باشند، اما معمولاً ارتباط مستقیمی با شبکه خارجی ندارند.

### Internal Network

VMها در یک شبکه داخلی مجازی با یکدیگر ارتباط برقرار می‌کنند.

برای آزمایشگاه اولیه، یک شبکه:

> **Host-Only یا Internal**

مناسب است.

در بخش‌های بعدی بر اساس نیاز، شبکه‌های بیشتری اضافه می‌کنیم.

---

# 3.11 اصل مهم: هیچ Bridged Network در آزمایش‌های حساس

در زمان اجرای تمرین‌های امنیتی، به‌صورت پیش‌فرض VMها را به شبکه سازمان یا شبکه خانگی واقعی Bridge نمی‌کنیم.

معماری مطلوب:

```text
Physical Network
        X
        |
     Isolated
        |
    SOC-LAB
```

است.

---

# 3.12 Naming Convention

از همین ابتدا باید نام‌گذاری استاندارد داشته باشیم.

مثلاً:

```text
WIN-EP01
LIN-SRV01
DC01
SIEM01
SOC-TOOLS01
```

معنی:

```text
WIN = Windows
LIN = Linux
EP = Endpoint
SRV = Server
DC = Domain Controller
SIEM = SIEM Server
```

این موضوع شاید در ابتدا ساده به نظر برسد، اما در Investigation بسیار مهم است.

فرض کنید در SIEM صدها Host وجود دارد.

این:

```text
WIN-EP01
```

بسیار بهتر از این است:

```text
DESKTOP-7F83K2
```

زیرا نقش سیستم را نیز مشخص می‌کند.

---

# 3.13 مرحله اول — ساخت Windows Endpoint

اولین VM ما:

```text
WIN-EP01
```

خواهد بود.

این سیستم نقش یک Workstation سازمانی را بازی می‌کند.

هدف آن:

* تولید Windows Event
* اجرای Process
* ایجاد Login
* ایجاد File Activity
* تولید Network Activity
* تمرین Endpoint Analysis

است.

---

# 3.14 نصب Windows VM

نسخه دقیق Windows بسته به منابع و مجوزهای در دسترس شما قابل انتخاب است.

برای آموزش امنیت سازمانی بهتر است از یک نسخه Windows Client مدرن و پشتیبانی‌شده استفاده شود.

در زمان ایجاد VM:

```text
Name:
WIN-EP01

CPU:
2 vCPU

RAM:
4 GB یا بیشتر

Disk:
60 GB

Network:
SOC-LAB
```

مقادیر بالا برای شروع آموزشی هستند.

---

# 3.15 ایجاد Local Administrator

در محیط آزمایشگاه یک حساب Administrative محلی داشته باشید:

```text
Username:
labadmin
```

و یک حساب کاربر عادی:

```text
Username:
analyst-user
```

هدف این است که بتوانیم تفاوت بین:

```text
Administrator Activity
```

و:

```text
Standard User Activity
```

را مشاهده کنیم.

---

# 3.16 چرا نباید همه فعالیت‌ها با Administrator انجام شود؟

در محیط‌های آموزشی یک اشتباه رایج این است که همه چیز با Administrator انجام می‌شود.

اما در SOC باید بتوانیم رفتار حساب‌های مختلف را تحلیل کنیم.

برای مثال:

```text
User:
analyst-user

Privilege:
Standard User
```

و:

```text
labadmin

Privilege:
Administrator
```

در فصل‌های بعدی می‌بینیم که Privilege Context در تحلیل امنیتی بسیار مهم است.

---

# 3.17 بررسی Hostname

پس از نصب سیستم:

```powershell
hostname
```

باید نام سیستم را بررسی کنید.

انتظار داریم:

```text
WIN-EP01
```

نمایش داده شود.

---

# 3.18 بررسی IP

در Windows:

```powershell
ipconfig
```

باید IP آزمایشگاه را مشاهده کنید.

مثلاً:

```text
IPv4 Address:
192.168.100.10
```

---

# 3.19 تست ارتباط

اکنون Linux VM را نیز ایجاد می‌کنیم.

برای تست ارتباط:

```powershell
ping 192.168.100.20
```

اگر پاسخ دریافت شد:

```text
Reply from 192.168.100.20
```

ارتباط لایه شبکه بین دو VM برقرار است.

در صورت عدم پاسخ، الزاماً به معنی مشکل شبکه نیست؛ ممکن است ICMP در سیستم مقصد Block شده باشد.

بنابراین:

> Ping یک ابزار تست است، نه اثبات کامل Connectivity.

---

# 3.20 مرحله دوم — ساخت Linux Server

VM دوم:

```text
LIN-SRV01
```

است.

این سیستم نقش Server سازمانی را خواهد داشت.

پیشنهاد اولیه:

```text
CPU:
2 vCPU

RAM:
2-4 GB

Disk:
30-40 GB

IP:
192.168.100.20
```

یک Distribution رایج و مناسب آموزشی انتخاب کنید؛ مثلاً یکی از نسخه‌های پایدار Ubuntu Server یا Debian.

---

# 3.21 چرا Linux هم لازم است؟

یک SOC واقعی فقط Windows را مانیتور نمی‌کند.

محیط‌های سازمانی معمولاً ترکیبی از:

```text
Windows
Linux
Network Devices
Cloud
Applications
Security Appliances
```

هستند.

در ادامه دوره با:

* Linux Authentication
* SSH
* Syslog
* Process Activity
* Network Connections
* File Activity

کار خواهیم کرد.

---

# 3.22 بررسی Linux

روی Linux:

```bash
hostname
```

و:

```bash
ip addr
```

را اجرا کنید.

همچنین:

```bash
whoami
```

برای مشاهده User فعلی استفاده می‌شود.

---

# 3.23 ساخت Activity آزمایشی

اکنون اولین فعالیت امنیتی را ایجاد می‌کنیم.

در Windows با User عادی Login کنید.

سپس چند فعالیت ساده انجام دهید:

```text
Open PowerShell
Create a file
Rename the file
Delete the file
Run a program
Log out
```

در Linux نیز:

```bash
touch test.txt
mkdir lab-test
echo "SOC Lab" > lab-test/test.txt
rm lab-test/test.txt
```

این فعالیت‌ها در آینده برای بررسی Logging استفاده خواهند شد.

---

# 3.24 Snapshot چیست؟

یکی از مهم‌ترین قابلیت‌های Virtual Lab:

> **Snapshot**

است.

Snapshot یک نقطه بازگشت از وضعیت VM است.

مثلاً:

```text
Windows Installed
        ↓
Configured
        ↓
Snapshot
        ↓
Security Tools Installed
        ↓
Experiment
        ↓
System Broken
        ↓
Revert Snapshot
```

به وضعیت قبلی بازمی‌گردیم.

---

# 3.25 Baseline Snapshot

پس از نصب و تنظیم اولیه:

```text
WIN-EP01
```

یک Snapshot ایجاد کنید.

نام پیشنهادی:

```text
BASELINE-WIN-EP01
```

برای Linux:

```text
BASELINE-LIN-SRV01
```

این Snapshotها نباید به‌صورت تصادفی حذف شوند.

---

# 3.26 Snapshot و Backup یکی نیستند

این تفاوت مهم است.

Snapshot:

* برای بازگشت سریع در محیط آزمایشگاهی مناسب است.
* وابسته به Hypervisor است.
* جایگزین Backup واقعی نیست.

Backup:

* برای محافظت از داده‌ها و بازیابی سیستم طراحی شده است.
* معمولاً مستقل‌تر است.

در آزمایشگاه:

> Snapshot ابزار آموزشی و عملیاتی ماست، نه سیستم Backup سازمانی.

---

# 3.27 زمان؛ یکی از مهم‌ترین داده‌های SOC

در تمام سیستم‌ها باید Time تا حد امکان هماهنگ باشد.

فرض کنید:

```text
Windows:
10:05:01

Linux:
10:05:08

SIEM:
10:04:57
```

اگر این اختلاف زیاد باشد، ساخت Timeline دشوار می‌شود.

در Investigation باید بتوانیم بگوییم:

```text
10:05:01
Login

10:05:03
Process Creation

10:05:05
Network Connection
```

نه اینکه Timestampها چند دقیقه اختلاف داشته باشند.

---

# 3.28 Time Synchronization

در محیط واقعی معمولاً از:

> NTP — Network Time Protocol

برای هماهنگ‌سازی زمان استفاده می‌شود.

در آزمایشگاه اولیه نیز باید مطمئن شوید زمان سیستم‌ها صحیح است.

برای بررسی زمان Windows:

```powershell
Get-Date
```

برای Linux:

```bash
date
```

در مراحل بعدی، Time Synchronization را در معماری Lab دقیق‌تر بررسی خواهیم کرد.

---

# 3.29 اولین نگاه به Windows Event Log

Windows دارای زیرسیستم Logging قدرتمندی است.

برای مشاهده Eventها می‌توانید از:

```text
Event Viewer
```

استفاده کنید.

مسیرهای مهم:

```text
Windows Logs
 ├── Application
 ├── Security
 ├── Setup
 ├── System
 └── Forwarded Events
```

در این دوره تمرکز ویژه‌ای روی:

> Security Log

خواهیم داشت.

---

# 3.30 Event ID چیست؟

هر Event در Windows معمولاً دارای یک شناسه است:

> **Event ID**

برای مثال، یکی از Eventهای شناخته‌شده در Windows Security Log:

```text
4624
```

است که معمولاً به Successful Logon مربوط است.

و:

```text
4625
```

معمولاً نشان‌دهنده Failed Logon است.

این شناسه‌ها در فصل‌های مربوط به Windows Security Monitoring به‌صورت عمیق بررسی خواهند شد.

در این مرحله فقط باید بدانید:

> Event ID یک شناسه مهم برای طبقه‌بندی Eventهاست.

---

# 3.31 اولین Investigation

اکنون یک تمرین ساده انجام می‌دهیم.

روی:

```text
WIN-EP01
```

یک Login ناموفق ایجاد کنید.

مثلاً عمداً Password اشتباه وارد کنید.

سپس وارد Windows شوید.

در Event Viewer:

```text
Windows Logs
    ↓
Security
```

را باز کنید.

Eventهای جدید را بررسی کنید.

هدف این تمرین پیدا کردن Event ID نیست.

هدف این است که برای اولین بار مسیر زیر را ببینید:

```text
Human Action
      ↓
Operating System
      ↓
Event
      ↓
Log
```

این همان زنجیره‌ای است که در آینده به:

```text
Human Action
 ↓
Endpoint
 ↓
Log
 ↓
Collector
 ↓
SIEM
 ↓
Detection
 ↓
Alert
 ↓
SOC Analyst
```

تبدیل خواهد شد.

---

# 3.32 یک سؤال بسیار مهم

فرض کنید:

```text
User:
analyst-user

Action:
Failed Login
```

Event ایجاد شده است.

آیا می‌توانیم از این Event نتیجه بگیریم که:

> کاربر مورد حمله قرار گرفته است؟

خیر.

ممکن است کاربر Password خود را اشتباه وارد کرده باشد.

این مثال، ارتباط مستقیم این فصل با فصل دوم را نشان می‌دهد.

Event:

```text
4625
```

یک **Observation** است.

تفسیر آن نیاز به Context دارد.

---

# 3.33 مشاهده Event Details

در Event Viewer، یک Event را باز کنید.

به فیلدهایی مانند موارد زیر توجه کنید:

```text
TimeCreated
Computer
Account Name
Logon Type
Source Network Address
Authentication Package
Process Information
```

تمام این فیلدها در Investigation ارزش یکسانی ندارند.

تحلیلگر باید یاد بگیرد:

> کدام Field برای سؤال فعلی من مهم است؟

---

# 3.34 Log Source

یکی از اصطلاحات بسیار مهم SOC:

> **Log Source**

است.

Log Source یعنی سیستمی که Log تولید می‌کند.

مثلاً:

```text
Windows Endpoint
Firewall
VPN
DNS Server
Linux Server
EDR
Email Gateway
```

در SIEM معمولاً می‌خواهیم بدانیم:

```text
این Event از کجا آمده است؟
```

این سؤال را همیشه در ذهن داشته باشید.

---

# 3.35 Telemetry چیست؟

**Telemetry** داده‌ای است که از سیستم‌ها برای مشاهده و تحلیل وضعیت آن‌ها جمع‌آوری می‌شود.

مثلاً Endpoint Telemetry می‌تواند شامل:

```text
Process Creation
Network Connection
File Activity
Registry Activity
Authentication
Command Line
```

باشد.

در آینده تفاوت بین:

```text
Log
Event
Telemetry
Alert
```

را با جزئیات بیشتری بررسی خواهیم کرد.

---

# 3.36 معماری Lab در پایان این فصل

در پایان فصل سوم، آزمایشگاه ما فعلاً چنین ساختاری دارد:

```text
                         HOST
                          |
              ┌───────────┴───────────┐
              │       Hypervisor      │
              │                       │
              │       SOC-LAB         │
              │   192.168.100.0/24    │
              │                       │
              │  ┌───────────────┐    │
              │  │   WIN-EP01    │    │
              │  │ 192.168.100.10│    │
              │  └───────────────┘    │
              │           │            │
              │           │            │
              │  ┌────────▼────────┐   │
              │  │   LIN-SRV01     │   │
              │  │ 192.168.100.20  │   │
              │  └─────────────────┘   │
              │                        │
              └────────────────────────┘
```

فعلاً خبری از:

```text
SIEM
AD
EDR
Firewall
NDR
SOAR
```

نیست.

و این کاملاً عمدی است.

---

# 3.37 چرا SIEM را همین حالا نصب نمی‌کنیم؟

زیرا هنوز سؤال مهمی را به‌صورت عملی تجربه نکرده‌ایم:

> چرا اصلاً به SIEM نیاز داریم؟

وقتی فقط دو سیستم داریم، می‌توانیم Eventها را مستقیماً روی خود سیستم‌ها ببینیم.

اما وقتی تعداد سیستم‌ها افزایش یابد:

```text
2 Systems
 ↓
10 Systems
 ↓
100 Systems
 ↓
1000 Systems
```

دیگر بررسی جداگانه هر سیستم عملی نیست.

در آن زمان نیاز به:

```text
Centralized Logging
```

و سپس:

```text
SIEM
```

کاملاً محسوس خواهد شد.

بنابراین معماری را بر اساس **نیاز واقعی** رشد می‌دهیم.

---

# 3.38 تمرین عملی 1 — ساخت Lab

محیط آزمایشگاهی اولیه را ایجاد کنید.

### VM شماره 1

```text
Name:
WIN-EP01

Role:
Windows Endpoint

IP:
192.168.100.10
```

### VM شماره 2

```text
Name:
LIN-SRV01

Role:
Linux Server

IP:
192.168.100.20
```

هر دو را به:

```text
SOC-LAB
```

متصل کنید.

---

# 3.39 تمرین عملی 2 — Network Verification

از Windows:

```powershell
ipconfig
```

و:

```powershell
ping 192.168.100.20
```

از Linux:

```bash
ip addr
```

و:

```bash
ping 192.168.100.10
```

نتایج را ثبت کنید.

جدول پیشنهادی:

| Parameter    | WIN-EP01 | LIN-SRV01 |
| ------------ | -------- | --------- |
| IP           |          |           |
| Hostname     |          |           |
| OS           |          |           |
| Connectivity |          |           |
| Time         |          |           |

---

# 3.40 تمرین عملی 3 — ایجاد Baseline

پس از اطمینان از سلامت VMها:

```text
Snapshot:
BASELINE-WIN-EP01

Snapshot:
BASELINE-LIN-SRV01
```

ایجاد کنید.

سپس یک تغییر آزمایشی ایجاد کنید و VM را به Snapshot برگردانید.

هدف:

> مطمئن شوید که فرآیند Restore را واقعاً بلد هستید.

این کار را **قبل از ورود به تمرین‌های پیچیده‌تر** انجام دهید.

---

# 3.41 تمرین عملی 4 — Windows Event Investigation

روی Windows:

1. یک Login ناموفق ایجاد کنید.
2. Event Viewer را باز کنید.
3. Security Log را بررسی کنید.
4. Event مربوطه را پیدا کنید.
5. Timestamp را ثبت کنید.
6. Account Name را ثبت کنید.
7. Source Network Address را بررسی کنید.
8. Event ID را ثبت کنید.

سپس پاسخ دهید:

> آیا فقط با مشاهده این Event می‌توان گفت حمله‌ای رخ داده است؟

پاسخ باید همراه با استدلال باشد.

---

# 3.42 تمرین عملی 5 — ساخت Timeline

سه فعالیت ایجاد کنید:

```text
Activity 1:
Failed Login

Activity 2:
Successful Login

Activity 3:
PowerShell Execution
```

سپس زمان هرکدام را ثبت کنید.

خروجی را به شکل زیر بسازید:

```text
10:01:12
Failed Login

10:01:45
Successful Login

10:02:03
PowerShell Started
```

سپس بنویسید:

> چه اطلاعات دیگری برای تحلیل این Timeline نیاز دارید؟

---

# 3.43 تمرین عملی 6 — تحلیل یک Event بدون Context

فرض کنید فقط این داده را داریم:

```text
Event ID: 4625
Account: administrator
Time: 03:22
```

سه احتمال مختلف برای این Event بنویسید.

مثلاً:

```text
1. User accidentally entered wrong password.
2. Automated service is misconfigured.
3. Unauthorized authentication attempt.
```

سپس برای هر Hypothesis مشخص کنید چه Evidenceهایی لازم دارید.

این تمرین مستقیماً مهارت فصل دوم را تقویت می‌کند.

---

# 3.44 تمرین عملی 7 — تحلیل از دید SOC

به جای اینکه بپرسید:

> «این Event چیست؟»

این سؤالات را پاسخ دهید:

```text
Who?
What?
When?
Where?
Source?
Target?
Normal?
Anomalous?
Malicious?
What evidence is missing?
What should I do next?
```

برای Event مربوط به Failed Login پاسخ مکتوب تهیه کنید.

---

# 3.45 مشکلات رایج آزمایشگاه

## مشکل 1 — VM اینترنت ندارد

در این مرحله ممکن است کاملاً طبیعی باشد.

اگر تمرین به Internet نیاز ندارد، آن را فعال نکنید.

---

## مشکل 2 — Ping جواب نمی‌دهد

بررسی کنید:

* IP صحیح است؟
* VMها در یک Virtual Network هستند؟
* Firewall سیستم مقصد ICMP را Block نکرده؟
* Subnet صحیح است؟

---

## مشکل 3 — Event پیدا نمی‌شود

بررسی کنید:

* آیا Activity واقعاً انجام شده؟
* زمان سیستم درست است؟
* Log موردنظر را بررسی کرده‌اید؟
* Filter فعال نیست؟

---

## مشکل 4 — VM خراب شده

به Snapshot Baseline برگردید.

به همین دلیل Snapshot را **قبل از آزمایش‌های مهم** ایجاد می‌کنیم.

---

# 3.46 مستندسازی آزمایشگاه

از همین فصل یک اصل مهم را اجرا کنید:

> هر چیزی که در Lab انجام می‌دهید، ثبت کنید.

یک فایل ساده ایجاد کنید:

```text
SOC-LAB-NOTES.md
```

ساختار پیشنهادی:

```text
# SOC Lab

## Network
Subnet:
192.168.100.0/24

## Hosts

WIN-EP01
192.168.100.10

LIN-SRV01
192.168.100.20

## Snapshots

BASELINE-WIN-EP01
BASELINE-LIN-SRV01

## Notes

...
```

در آینده این فایل تبدیل به مستندات واقعی آزمایشگاه شما خواهد شد.

---

# 3.47 Lab Notebook

علاوه بر مستندات فنی، یک **Lab Notebook** داشته باشید.

برای هر تمرین:

```text
Date:
Exercise:
Objective:
Environment:
Actions:
Observed Events:
Findings:
Problems:
Lessons Learned:
```

این روش فقط برای دوره نیست.

در کار واقعی SOC نیز مستندسازی Investigation اهمیت زیادی دارد.

---

# 3.48 تمرین مهم: یک Incident فرضی را ثبت کنید

فرض کنید:

```text
03:10
Failed Login

03:11
Failed Login

03:12
Successful Login

03:13
PowerShell Execution
```

در Lab Notebook بنویسید:

### Observation

چه چیزی مشاهده شد؟

### Hypothesis

چه احتمالاتی وجود دارد؟

### Evidence

چه داده‌هایی دارید؟

### Missing Evidence

چه چیزهایی ندارید؟

### Assessment

ارزیابی فعلی چیست؟

### Next Step

قدم بعدی چیست؟

این قالب را در فصل‌های بعد نیز استفاده خواهیم کرد.

---

# 3.49 معیار قبولی این فصل

تا زمانی که موارد زیر را انجام نداده‌اید، بهتر است وارد فصل بعد نشوید:

* [ ] ساخت Network آزمایشگاهی
* [ ] ساخت WIN-EP01
* [ ] ساخت LIN-SRV01
* [ ] برقراری Connectivity
* [ ] تنظیم Hostname
* [ ] بررسی IP
* [ ] ایجاد Snapshot
* [ ] تولید حداقل یک Windows Event
* [ ] پیدا کردن Event در Event Viewer
* [ ] ثبت Event ID
* [ ] ایجاد Timeline ساده
* [ ] ثبت نتایج در Lab Notebook

---

# 3.50 آزمون پایان فصل

### سؤال 1

چرا محیط SOC Lab باید از شبکه واقعی جدا باشد؟

### سؤال 2

تفاوت NAT، Bridged و Host-Only چیست؟

### سؤال 3

چرا Snapshot برای آزمایشگاه امنیتی مهم است؟

### سؤال 4

چرا Time Synchronization در Investigation اهمیت دارد؟

### سؤال 5

Log Source چیست؟

### سؤال 6

Telemetry چه مفهومی دارد؟

### سؤال 7

آیا یک Event به‌تنهایی می‌تواند Incident را اثبات کند؟

### سؤال 8

چرا باید Naming Convention داشته باشیم؟

### سؤال 9

چرا SIEM را در اولین مرحله Lab نصب نکردیم؟

### سؤال 10

مسیر زیر را توضیح دهید:

```text
User Action
     ↓
Endpoint
     ↓
Event
     ↓
Log
     ↓
SIEM
     ↓
Detection
     ↓
Alert
     ↓
SOC Analyst
```

---

# 3.51 جمع‌بندی فصل

در این فصل اولین نسخه از محیط عملیاتی خود را ساختیم.

اکنون یک آزمایشگاه کوچک داریم:

```text
WIN-EP01
    +
LIN-SRV01
```

اما مهم‌تر از VMها، یک مدل عملیاتی ایجاد کرده‌ایم:

```text
Activity
   ↓
System
   ↓
Event
   ↓
Log
   ↓
Observation
```

در فصل‌های بعد، این زنجیره را گسترش خواهیم داد:

```text
Activity
   ↓
Endpoint
   ↓
Log
   ↓
Log Collection
   ↓
Centralized Storage
   ↓
SIEM
   ↓
Detection
   ↓
Alert
   ↓
Triage
   ↓
Investigation
   ↓
Escalation
```

این دقیقاً همان مسیری است که یک تحلیلگر SOC در محیط واقعی با آن مواجه می‌شود.

---

# نکته منتور

در این مرحله یک نکته را بسیار جدی بگیرید:

**آزمایشگاه را برای نمایش دادن ابزارها نسازید؛ آزمایشگاه را برای پاسخ دادن به سؤال‌های امنیتی بسازید.**

اگر بدانید:

> «می‌خواهم بفهمم بعد از یک Login چه اتفاقی روی Endpoint افتاده است»،

آن‌وقت می‌فهمید به چه Telemetry، چه Log و در نهایت چه ابزاری نیاز دارید.

اما اگر از ابتدا بگویید:

> «من باید SIEM نصب کنم»،

ممکن است ابزار داشته باشید ولی مسئله‌ای برای حل کردن نداشته باشید.

---

## فصل بعد

در **فصل چهارم — معماری داده و Logging در SOC** وارد اولین بخش جدی فنی می‌شویم: اینکه یک فعالیت روی سیستم دقیقاً چگونه به Event و Log تبدیل می‌شود، تفاوت Windows Event Log و Syslog چیست، چه Logهایی برای SOC اهمیت دارند، Log Collection چگونه انجام می‌شود و چرا «داشتن Log» با «داشتن Telemetry قابل استفاده» یکسان نیست.

در فصل بعد، همین آزمایشگاه را یک مرحله توسعه می‌دهیم و وارد **Centralized Logging** می‌شویم.
