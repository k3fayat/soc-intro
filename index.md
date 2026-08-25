# فهرست مطالب

## پیشگفتار
- هدف و رویکرد کتاب
- جایگاه SOC در معماری امنیت سازمان
- نقش و مسئولیت‌های تحلیلگر SOC Tier 1
- تفاوت یادگیری تئوری و مهارت عملی
- روش استفاده از آزمایشگاه عملی
- استانداردها و چارچوب‌های مورد استفاده
- خروجی مورد انتظار از دوره

---

# ماژول اول — مبانی عملیات امنیت و نقش SOC

## فصل 1 — آشنایی با Security Operations
### 1.1 امنیت سایبری از دیدگاه عملیاتی
### 1.2 Security Operations چیست؟
### 1.3 SOC چیست و چه مسئله‌ای را حل می‌کند؟
### 1.4 معماری و اجزای یک SOC
### 1.5 ساختار تیم SOC
### 1.6 نقش‌های Tier 1، Tier 2 و Tier 3
### 1.7 تفاوت SOC با NOC، CSIRT و Incident Response Team
### 1.8 جریان کاری یک رخداد در SOC
### 1.9 شیفت کاری و فرآیند Handover
### 1.10 KPI و معیارهای عملکرد SOC
### تمرین عملی فصل

## فصل 2 — ذهنیت و روش تفکر تحلیلگر SOC
### 2.1 تحلیلگر SOC دقیقاً چه کاری انجام می‌دهد؟
### 2.2 تفکر مبتنی بر شواهد
### 2.3 تفاوت Event، Alert و Incident
### 2.4 فرضیه‌سازی در تحلیل امنیتی
### 2.5 پرسش‌های کلیدی در تحلیل یک Alert
### 2.6 Context چیست و چرا اهمیت دارد؟
### 2.7 Normal، Anomalous و Malicious
### 2.8 خطاهای رایج تحلیلگران تازه‌کار
### 2.9 Cognitive Bias در تحلیل امنیتی
### 2.10 ساختن ذهنیت Investigation
### تمرین عملی فصل

---

# ماژول دوم — ساخت آزمایشگاه عملی SOC

## فصل 3 — طراحی Cybersecurity Lab
### 3.1 هدف آزمایشگاه
### 3.2 الزامات سخت‌افزاری
### 3.3 انتخاب Hypervisor
### 3.4 طراحی شبکه آزمایشگاه
### 3.5 Network Segmentation
### 3.6 IP Addressing و DNS
### 3.7 طراحی نام‌گذاری سیستم‌ها
### 3.8 طراحی Snapshot و Backup
### 3.9 ملاحظات امنیتی آزمایشگاه
### 3.10 معماری مرحله اول آزمایشگاه
### تمرین عملی فصل

## فصل 4 — ساخت اولین محیط Windows و Linux
### 4.1 ایجاد ماشین مجازی Windows
### 4.2 ایجاد ماشین مجازی Linux
### 4.3 تنظیم شبکه
### 4.4 ایجاد کاربران آزمایشگاهی
### 4.5 ایجاد سرویس‌های پایه
### 4.6 تولید فعالیت‌های عادی
### 4.7 مشاهده و تحلیل Eventهای سیستم
### 4.8 ایجاد Snapshot
### 4.9 تست ارتباط میان سیستم‌ها
### تمرین عملی فصل

## فصل 5 — توسعه آزمایشگاه به محیط سازمانی
### 5.1 طراحی Domain
### 5.2 نصب Domain Controller
### 5.3 ایجاد Domain Client
### 5.4 ایجاد کاربران و گروه‌ها
### 5.5 Group Policy
### 5.6 File Server
### 5.7 DNS و سرویس‌های وابسته
### 5.8 طراحی VLAN و Subnetهای آزمایشگاهی
### 5.9 ایجاد شبکه مدیریت و شبکه کاربران
### 5.10 معماری نهایی این مرحله
### تمرین عملی فصل

---

# ماژول سوم — شناخت داده‌های امنیتی و Logging

## فصل 6 — مفهوم Log و Event
### 6.1 Log چیست؟
### 6.2 Event چیست؟
### 6.3 چرا Logging برای SOC حیاتی است؟
### 6.4 انواع Log
### 6.5 System Log
### 6.6 Application Log
### 6.7 Security Log
### 6.8 Network Log
### 6.9 Authentication Log
### 6.10 Audit Log
### 6.11 ساختار یک Log
### 6.12 Timestamp و Time Synchronization
### 6.13 اهمیت Time Zone
### 6.14 Log Integrity
### تمرین عملی فصل

## فصل 7 — Windows Security Events
### 7.1 Windows Event Log Architecture
### 7.2 Event Viewer
### 7.3 Security Events
### 7.4 Logon و Logoff Events
### 7.5 Failed Authentication
### 7.6 Account Management
### 7.7 Process Creation
### 7.8 Service Creation
### 7.9 PowerShell Logging
### 7.10 Windows Defender Events
### 7.11 Event ID چیست؟
### 7.12 تحلیل Eventهای مرتبط
### تمرین عملی فصل

## فصل 8 — Linux و Syslog
### 8.1 Linux Logging Architecture
### 8.2 Syslog چیست؟
### 8.3 rsyslog
### 8.4 journalctl و systemd-journald
### 8.5 Authentication Logs
### 8.6 SSH Logs
### 8.7 sudo و privilege events
### 8.8 Process و Service Logs
### 8.9 Centralized Logging
### 8.10 تحلیل Logهای Linux
### تمرین عملی فصل

---

# ماژول چهارم — معماری و عملکرد SIEM

## فصل 9 — SIEM چیست؟
### 9.1 تعریف SIEM
### 9.2 مسئله‌ای که SIEM حل می‌کند
### 9.3 اجزای SIEM
### 9.4 Log Collection
### 9.5 Parsing
### 9.6 Normalization
### 9.7 Enrichment
### 9.8 Indexing
### 9.9 Correlation
### 9.10 Detection
### 9.11 Alerting
### 9.12 Dashboard
### 9.13 معماری عملی SIEM

## فصل 10 — ساخت SIEM آزمایشگاهی
### 10.1 انتخاب پلتفرم SIEM آزمایشگاه
### 10.2 نصب و راه‌اندازی
### 10.3 اتصال Windows
### 10.4 اتصال Linux
### 10.5 دریافت Network Logs
### 10.6 بررسی Data Ingestion
### 10.7 Parsing و Field Extraction
### 10.8 Search و Query
### 10.9 ساخت Dashboard
### 10.10 ساخت Detection ساده
### 10.11 ایجاد Alert
### تمرین عملی فصل

## فصل 11 — Query و جست‌وجوی داده‌های امنیتی
### 11.1 فلسفه جست‌وجوی امنیتی
### 11.2 Filtering
### 11.3 Field-based Search
### 11.4 Time Range
### 11.5 Aggregation
### 11.6 Sorting
### 11.7 Counting
### 11.8 Search بر اساس IP
### 11.9 Search بر اساس User
### 11.10 Search بر اساس Host
### 11.11 Search بر اساس Process
### 11.12 ساخت Queryهای Investigation
### 11.13 تبدیل سؤال امنیتی به Query
### تمرین عملی فصل

---

# ماژول پنجم — فرآیند Triage و تحلیل Alert

## فصل 12 — Alert Triage
### 12.1 Alert چیست؟
### 12.2 Anatomy of an Alert
### 12.3 Severity
### 12.4 Priority
### 12.5 Confidence
### 12.6 Risk
### 12.7 False Positive
### 12.8 True Positive
### 12.9 Benign Positive
### 12.10 Triage Workflow
### 12.11 تصمیم‌گیری اولیه
### تمرین عملی فصل

## فصل 13 — تحلیل هویت و Authentication
### 13.1 Authentication و Authorization
### 13.2 مفهوم Identity در SOC
### 13.3 Successful Logon
### 13.4 Failed Logon
### 13.5 Brute Force
### 13.6 Password Spraying
### 13.7 Credential Stuffing
### 13.8 Impossible Travel
### 13.9 Anomalous Login
### 13.10 Service Account
### 13.11 Privileged Account
### 13.12 تحلیل زنجیره Authentication Events
### تمرین عملی: شناسایی حمله Brute Force

## فصل 14 — تحلیل Endpoint
### 14.1 Endpoint چیست؟
### 14.2 Process و Process Tree
### 14.3 Parent-Child Relationship
### 14.4 Command Line
### 14.5 File Creation
### 14.6 Registry Activity
### 14.7 Service Creation
### 14.8 Scheduled Task
### 14.9 PowerShell
### 14.10 Windows Script Host
### 14.11 LOLBins و Living-off-the-Land
### 14.12 تشخیص رفتار غیرعادی Endpoint
### تمرین عملی فصل

## فصل 15 — تحلیل Network Events
### 15.1 Network Telemetry
### 15.2 Source و Destination
### 15.3 Port و Protocol
### 15.4 Connection Events
### 15.5 DNS Query
### 15.6 HTTP و HTTPS
### 15.7 TLS Metadata
### 15.8 Firewall Logs
### 15.9 Proxy Logs
### 15.10 Beaconing
### 15.11 Port Scanning
### 15.12 ارتباطات غیرعادی
### تمرین عملی فصل

---

# ماژول ششم — شناخت حملات از دید SOC

## فصل 16 — Kill Chain و Attack Lifecycle
### 16.1 مفهوم Attack Lifecycle
### 16.2 Cyber Kill Chain
### 16.3 محدودیت‌های مدل Kill Chain
### 16.4 ارتباط Kill Chain با SOC
### 16.5 تشخیص مراحل حمله از روی Telemetry
### تمرین عملی فصل

## فصل 17 — MITRE ATT&CK
### 17.1 MITRE ATT&CK چیست؟
### 17.2 Tactic
### 17.3 Technique
### 17.4 Sub-technique
### 17.5 Procedure
### 17.6 Mapping یک Alert به ATT&CK
### 17.7 استفاده عملی از ATT&CK در Investigation
### 17.8 ATT&CK Navigator
### 17.9 محدودیت‌های Mapping
### تمرین عملی فصل

## فصل 18 — Initial Access
### 18.1 Phishing
### 18.2 Malicious Attachment
### 18.3 Malicious Link
### 18.4 Valid Accounts
### 18.5 Exploitation
### 18.6 External Remote Services
### 18.7 تحلیل شواهد Initial Access
### تمرین عملی فصل

## فصل 19 — Execution
### 19.1 Command Shell
### 19.2 PowerShell
### 19.3 Windows Management Instrumentation
### 19.4 Scripting
### 19.5 Scheduled Task
### 19.6 User Execution
### 19.7 تشخیص Execution از روی Log
### تمرین عملی فصل

## فصل 20 — Persistence
### 20.1 مفهوم Persistence
### 20.2 Registry Run Keys
### 20.3 Services
### 20.4 Scheduled Tasks
### 20.5 Startup Locations
### 20.6 Account Manipulation
### 20.7 تحلیل Persistence
### تمرین عملی فصل

## فصل 21 — Privilege Escalation و Credential Access
### 21.1 مفهوم Privilege Escalation
### 21.2 Account Discovery
### 21.3 Credential Access
### 21.4 Credential Dumping
### 21.5 Password Attacks
### 21.6 سوءاستفاده از حساب‌های Privileged
### 21.7 تشخیص رفتارهای مشکوک
### تمرین عملی فصل

## فصل 22 — Discovery و Lateral Movement
### 22.1 Discovery
### 22.2 Host Discovery
### 22.3 Account Discovery
### 22.4 Network Discovery
### 22.5 Remote Services
### 22.6 SMB
### 22.7 RDP
### 22.8 WinRM
### 22.9 Lateral Movement
### 22.10 ساخت Timeline حرکت مهاجم
### تمرین عملی فصل

## فصل 23 — Command and Control
### 23.1 مفهوم C2
### 23.2 Beaconing
### 23.3 DNS-based C2
### 23.4 HTTP/HTTPS C2
### 23.5 Periodic Connections
### 23.6 Domain Reputation
### 23.7 IP Reputation
### 23.8 تشخیص الگوهای C2
### تمرین عملی فصل

## فصل 24 — Collection و Exfiltration
### 24.1 Data Collection
### 24.2 File Discovery
### 24.3 Archive Collected Data
### 24.4 Data Staging
### 24.5 Exfiltration
### 24.6 تشخیص رفتارهای انتقال داده
### تمرین عملی فصل

---

# ماژول هفتم — Threat Intelligence و تحلیل IOC

## فصل 25 — Threat Intelligence
### 25.1 Threat Intelligence چیست؟
### 25.2 Strategic Intelligence
### 25.3 Tactical Intelligence
### 25.4 Operational Intelligence
### 25.5 Technical Intelligence
### 25.6 Intelligence در SOC Tier 1
### 25.7 Threat Feed
### 25.8 محدودیت‌های Threat Intelligence

## فصل 26 — IOC و تحلیل Indicator
### 26.1 IOC چیست؟
### 26.2 IP Address
### 26.3 Domain
### 26.4 URL
### 26.5 File Hash
### 26.6 Email Address
### 26.7 Filename
### 26.8 User Agent
### 26.9 بررسی Reputation
### 26.10 Contextual Analysis
### 26.11 IOC بدون Context چرا خطرناک است؟
### تمرین عملی فصل

---

# ماژول هشتم — Email Security و Phishing Analysis

## فصل 27 — Email در SOC
### 27.1 Email Attack Surface
### 27.2 ساختار Email
### 27.3 Header
### 27.4 Sender و Recipient
### 27.5 SMTP
### 27.6 Authentication Mechanisms
### 27.7 SPF
### 27.8 DKIM
### 27.9 DMARC
### 27.10 تحلیل Message Header
### تمرین عملی فصل

## فصل 28 — Phishing Investigation
### 28.1 تشخیص Phishing
### 28.2 Social Engineering
### 28.3 Malicious URL
### 28.4 URL Redirection
### 28.5 Attachment Analysis
### 28.6 Domain Analysis
### 28.7 Sender Reputation
### 28.8 Credential Phishing
### 28.9 Business Email Compromise
### 28.10 ساخت Phishing Investigation Workflow
### تمرین عملی فصل

---

# ماژول نهم — EDR و Endpoint Investigation

## فصل 29 — مفهوم EDR
### 29.1 EDR چیست؟
### 29.2 تفاوت EDR و Antivirus
### 29.3 Telemetry
### 29.4 Detection
### 29.5 Prevention
### 29.6 Response
### 29.7 Process Tree
### 29.8 Endpoint Timeline
### 29.9 ارتباط EDR و SIEM

## فصل 30 — Endpoint Investigation
### 30.1 بررسی Process
### 30.2 بررسی Command Line
### 30.3 بررسی Network Connection
### 30.4 بررسی File
### 30.5 بررسی Persistence
### 30.6 بررسی User Activity
### 30.7 بررسی Parent Process
### 30.8 تحلیل زنجیره Processها
### 30.9 ساخت Timeline Endpoint
### تمرین عملی فصل

---

# ماژول دهم — Incident Analysis و Case Management

## فصل 31 — Event تا Incident
### 31.1 Event
### 31.2 Alert
### 31.3 Case
### 31.4 Incident
### 31.5 ارتباط این مفاهیم
### 31.6 Incident Classification
### 31.7 Incident Severity
### 31.8 Incident Priority

## فصل 32 — Incident Investigation
### 32.1 تعریف Scope
### 32.2 جمع‌آوری شواهد
### 32.3 Pivot کردن بین داده‌ها
### 32.4 Timeline
### 32.5 Entity Relationship
### 32.6 Hypothesis Testing
### 32.7 Determining Impact
### 32.8 Determining Confidence
### 32.9 تعیین وضعیت Incident
### تمرین عملی فصل

## فصل 33 — Case Management و Documentation
### 33.1 چرا مستندسازی مهم است؟
### 33.2 ساختار Case
### 33.3 Incident Summary
### 33.4 Evidence
### 33.5 Analyst Notes
### 33.6 Timeline
### 33.7 Indicators
### 33.8 Findings
### 33.9 Recommendation
### 33.10 Closure Criteria
### 33.11 Handover Note
### تمرین عملی فصل

---

# ماژول یازدهم — Escalation و فرآیند عملیاتی SOC

## فصل 34 — Escalation
### 34.1 مفهوم Escalation
### 34.2 چه زمانی باید Escalate کرد؟
### 34.3 Escalation به Tier 2
### 34.4 Escalation به تیم شبکه
### 34.5 Escalation به تیم سیستم
### 34.6 Escalation به Incident Response
### 34.7 Escalation به مدیریت
### 34.8 اطلاعات ضروری هنگام Escalation
### 34.9 خطاهای رایج در Escalation

## فصل 35 — Playbook و Runbook
### 35.1 Playbook چیست؟
### 35.2 Runbook چیست؟
### 35.3 تفاوت Playbook و Runbook
### 35.4 Alert Triage Playbook
### 35.5 Brute Force Playbook
### 35.6 Phishing Playbook
### 35.7 Malware Playbook
### 35.8 Suspicious Login Playbook
### 35.9 Endpoint Compromise Playbook
### تمرین عملی فصل

## فصل 36 — Shift Handover
### 36.1 اهمیت Handover
### 36.2 اطلاعاتی که باید منتقل شوند
### 36.3 وضعیت Incidentهای باز
### 36.4 فعالیت‌های انجام‌شده
### 36.5 اقدامات Pending
### 36.6 Risk و Priority
### 36.7 نوشتن Handover حرفه‌ای
### تمرین عملی فصل

---

# ماژول دوازدهم — Detection Engineering برای تحلیلگر SOC

## فصل 37 — منطق Detection
### 37.1 Detection چیست؟
### 37.2 Signature-based Detection
### 37.3 Behavior-based Detection
### 37.4 Rule-based Detection
### 37.5 Threshold
### 37.6 Correlation Rule
### 37.7 Detection Logic
### 37.8 False Positive Management

## فصل 38 — ساخت Detectionهای عملی
### 38.1 تشخیص Brute Force
### 38.2 تشخیص Password Spraying
### 38.3 تشخیص اجرای PowerShell مشکوک
### 38.4 تشخیص Process غیرعادی
### 38.5 تشخیص ارتباط غیرعادی
### 38.6 تشخیص Account Anomaly
### 38.7 تست Detection
### 38.8 Tuning
### 38.9 ارزیابی کیفیت Detection
### تمرین عملی فصل

---

# ماژول سیزدهم — تحلیل پیشرفته برای Tier 1

## فصل 39 — Correlation و تحلیل چندمنبعی
### 39.1 چرا یک Log کافی نیست؟
### 39.2 Correlation بین Endpoint و Network
### 39.3 Correlation بین Identity و Endpoint
### 39.4 Correlation بین DNS و Proxy
### 39.5 Correlation بین Firewall و Endpoint
### 39.6 Multi-Stage Detection
### 39.7 ساخت Attack Narrative
### تمرین عملی فصل

## فصل 40 — Timeline Analysis
### 40.1 مفهوم Timeline
### 40.2 ساخت Timeline از چند منبع
### 40.3 ترتیب زمانی Eventها
### 40.4 تشخیص نقطه شروع حمله
### 40.5 تشخیص Persistence
### 40.6 تشخیص Lateral Movement
### 40.7 تشخیص C2
### 40.8 بازسازی Incident
### تمرین عملی فصل

## فصل 41 — Threat Hunting مقدماتی
### 41.1 Threat Hunting چیست؟
### 41.2 تفاوت Hunting و Alert Triage
### 41.3 Hypothesis-driven Hunting
### 41.4 Hunting بر اساس IOC
### 41.5 Hunting بر اساس TTP
### 41.6 Hunting در SIEM
### 41.7 Hunting در Endpoint
### 41.8 مستندسازی یافته‌های Hunting
### تمرین عملی فصل

---

# ماژول چهاردهم — پروژه‌های عملی SOC

## فصل 42 — پروژه اول: Brute Force Investigation
### سناریو
### داده‌های اولیه
### Alert
### Triage
### Investigation
### تحلیل Authentication Logs
### تعیین Scope
### تشخیص Attack Pattern
### مستندسازی
### Escalation

## فصل 43 — پروژه دوم: Phishing Investigation
### دریافت Email
### بررسی Header
### بررسی URL
### بررسی Domain
### بررسی Endpoint
### تعیین Impact
### IOC Extraction
### Incident Documentation

## فصل 44 — پروژه سوم: Suspicious PowerShell
### Alert اولیه
### بررسی Process Tree
### بررسی Command Line
### بررسی User
### بررسی Parent Process
### بررسی Network Connection
### Mapping به MITRE ATT&CK
### تعیین Verdict
### Escalation

## فصل 45 — پروژه چهارم: Endpoint Compromise
### Initial Alert
### جمع‌آوری شواهد
### Timeline
### Process Analysis
### Persistence
### Network Activity
### C2
### Scope Determination
### Incident Classification
### Escalation

## فصل 46 — پروژه پنجم: Lateral Movement
### تشخیص Login غیرعادی
### تحلیل Source و Destination
### بررسی SMB/RDP/Remote Services
### بررسی Account
### بررسی Endpoint
### ساخت Attack Timeline
### تعیین مسیر احتمالی مهاجم
### تعیین Impact
### گزارش Incident

---

# ماژول پانزدهم — پروژه نهایی SOC Tier 1

## فصل 47 — طراحی سناریوی نهایی
### 47.1 معماری نهایی آزمایشگاه
### 47.2 نقش سیستم‌ها
### 47.3 نقش کاربران
### 47.4 منابع Log
### 47.5 Detectionها
### 47.6 سناریوی حمله
### 47.7 اهداف آموزشی

## فصل 48 — اجرای Incident از ابتدا تا انتها
### 48.1 دریافت Alert
### 48.2 Triage
### 48.3 جمع‌آوری شواهد
### 48.4 Pivot
### 48.5 Correlation
### 48.6 Timeline
### 48.7 IOC Analysis
### 48.8 MITRE ATT&CK Mapping
### 48.9 تعیین Severity
### 48.10 تعیین Scope
### 48.11 تصمیم‌گیری
### 48.12 Escalation
### 48.13 مستندسازی
### 48.14 Handover

## فصل 49 — سناریوی بدون راهنما
### 49.1 دریافت داده خام
### 49.2 تحلیل مستقل
### 49.3 تشکیل Hypothesis
### 49.4 Investigation
### 49.5 تعیین Verdict
### 49.6 تهیه گزارش
### 49.7 دفاع از تصمیم تحلیلی

---

# پیوست‌ها

## پیوست A — واژه‌نامه تخصصی SOC و Cybersecurity
- اصطلاحات انگلیسی
- معادل فارسی
- تعریف فنی
- مثال عملی

## پیوست B — Windows Event IDهای مهم برای SOC Tier 1

## پیوست C — Linux و Syslog Reference

## پیوست D — MITRE ATT&CK Quick Reference

## پیوست E — IOC Investigation Reference

## پیوست F — Queryهای پرکاربرد SIEM

## پیوست G — SOC Analyst Cheat Sheet

## پیوست H — Alert Triage Checklist

## پیوست I — Incident Documentation Template

## پیوست J — Escalation Template

## پیوست K — Shift Handover Template

## پیوست L — SOC Metrics و KPIها

## پیوست M — معماری کامل آزمایشگاه نهایی

## پیوست N — سناریوهای تمرینی تکمیلی

## پیوست O — آزمون نهایی مهارت عملی SOC Tier 1

---

# ساختار آموزشی پیشنهادی هر فصل

برای حفظ ماهیت عملی کتاب، هر فصل با الگوی نسبتاً ثابتی ارائه خواهد شد:

1. **اهداف یادگیری**
2. **مفاهیم نظری ضروری**
3. **اصطلاحات تخصصی انگلیسی**
4. **دیدگاه SOC Analyst**
5. **مثال واقعی یا شبه‌واقعی**
6. **تحلیل Log / Alert / Telemetry**
7. **روش Investigation**
8. **اشتباهات رایج**
9. **نکات عملیاتی**
10. **آزمایش عملی در Lab**
11. **سناریوی مستقل**
12. **سؤالات ارزیابی**
13. **جمع‌بندی**
14. **مهارت‌هایی که باید کسب شده باشند**

به این ترتیب، مسیر کتاب از **ساخت آزمایشگاه → شناخت داده → تحلیل Alert → Investigation → شناخت رفتار مهاجم → Incident Handling → Escalation → پروژه واقعی SOC** حرکت می‌کند و در پایان، مخاطب با یک پروژه جامع بدون راهنما ارزیابی خواهد شد.