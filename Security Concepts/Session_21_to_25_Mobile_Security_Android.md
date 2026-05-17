# Sessions 21–25: Mobile Security — Android Architecture, Attacks & Analysis

> **Exam Weight:** High | **Type:** Theory + Lab

---

# SESSION 21: Android Architecture & Security Model

## 1. Android Architecture

### 1.1 Android Architecture Layers (Bottom to Top)

```
┌─────────────────────────────────────────┐
│         APPLICATIONS                    │  ← Your apps (Gmail, Chrome, etc.)
├─────────────────────────────────────────┤
│    APPLICATION FRAMEWORK                │  ← Activity Manager, Package Manager, etc.
├─────────────────────────────────────────┤
│  LIBRARIES          │  ANDROID RUNTIME  │  ← SQLite, WebKit / Dalvik/ART
├─────────────────────────────────────────┤
│         LINUX KERNEL                    │  ← Drivers, memory, power management
└─────────────────────────────────────────┘
```

### 1.2 Detailed Layer Breakdown

#### Linux Kernel (Bottom Layer)
- Core of Android OS
- Provides: hardware abstraction, memory management, process scheduling, networking
- Security: File permissions, SELinux policies, UID/GID isolation
- Each Android app runs with a **unique Linux User ID (UID)**

#### Hardware Abstraction Layer (HAL)
- Interface between hardware drivers and Android framework
- Standardizes how hardware is accessed

#### Android Runtime (ART)
- **ART** (Android Runtime) — replaced Dalvik in Android 5.0+
- **AOT (Ahead-of-Time) compilation** — compiles bytecode to native at install time
- Faster execution than Dalvik's JIT
- Apps compiled to `.dex` (Dalvik Executable) format

> **Tricky Q:** What replaced Dalvik in Android 5.0?
> **A:** ART (Android Runtime). Uses AOT compilation vs Dalvik's JIT compilation.

#### Android Libraries
- SQLite (database)
- WebKit (browser engine)
- SSL libraries
- OpenGL ES (graphics)
- Media framework

#### Application Framework
- **Activity Manager** — manages app lifecycle (start, pause, stop, destroy)
- **Package Manager** — installs/manages apps, tracks permissions
- **Window Manager** — manages display windows
- **Content Providers** — share data between apps
- **Notification Manager** — handles notifications

#### Applications Layer (Top)
- System apps (Phone, SMS, Camera)
- User-installed apps

---

## 2. Android File Structure

```
/
├── /data/         → App data (protected)
│   ├── /data/data/<package>/     → App private data
│   │   ├── databases/   → SQLite databases
│   │   ├── shared_prefs/ → SharedPreferences XML
│   │   ├── files/        → App files
│   │   └── cache/        → Cache files
│   └── /data/app/       → Installed APK files
├── /system/       → OS files (read-only normally)
├── /sdcard/       → External storage (world-readable!)
├── /proc/         → Process information
├── /dev/          → Device files
└── /cache/        → System cache
```

> **Tricky Q:** Where are Android SharedPreferences stored?
> **A:** `/data/data/<package_name>/shared_prefs/` — if not encrypted, can be read on rooted devices

> **Tricky Q:** What is the security risk of storing data in `/sdcard/`?
> **A:** External storage is world-readable — any app with `READ_EXTERNAL_STORAGE` permission can access it. Never store sensitive data there.

---

## 3. Android Build Process

```
Source Code (.java/.kt)
        ↓ (javac)
     Bytecode (.class)
        ↓ (dx/d8 tool)
   Dalvik Bytecode (.dex)
        ↓ (aapt - package with resources, manifest)
      APK file (.apk)
        ↓ (jarsigner / apksigner)
   Signed APK (.apk)
        ↓ (install on device)
```

**APK Structure (ZIP file):**
```
app.apk
├── AndroidManifest.xml  → App metadata, permissions, components
├── classes.dex          → Compiled code
├── res/                 → Resources (layouts, images, strings)
├── assets/              → Raw files
├── lib/                 → Native libraries (.so files)
└── META-INF/            → Signature files
```

---

## 4. Android App Fundamentals

### 4.1 Four App Components

| Component | What it is | Security Risk |
|-----------|-----------|--------------|
| **Activity** | Single screen UI | Improper exported activities |
| **Service** | Background processing | Insecure service communication |
| **Broadcast Receiver** | Receives system broadcasts | Accepting broadcasts from any app |
| **Content Provider** | Shares data between apps | SQL injection in content providers |

### 4.2 AndroidManifest.xml — Security Critical

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.app">
    
    <!-- Permissions declared (dangerous ones require runtime approval) -->
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.READ_CONTACTS"/>
    
    <application android:allowBackup="true"  <!-- RISK: allows adb backup of app data -->
                 android:debuggable="true">   <!-- RISK: allows debugging in production! -->
        
        <activity android:name=".MainActivity"
                  android:exported="true">   <!-- Can be called by other apps -->
        </activity>
        
        <activity android:name=".AdminActivity"
                  android:exported="false">  <!-- SAFE: internal only -->
        </activity>
    </application>
</manifest>
```

**Security Issues in Manifest:**
- `android:debuggable="true"` in release → allows ADB debugging, code injection
- `android:allowBackup="true"` → ADB can backup all app data
- `android:exported="true"` on sensitive activities → any app can start them

> **Tricky Q:** Why is `android:debuggable="true"` dangerous in a production app?
> **A:** Allows ADB debugging → attacker with USB access can attach debugger, dump memory, inject code. Apps on Google Play are auto-checked for this.

### 4.3 Android Permission Groups

**Normal permissions** — auto-granted (no popup):
- `INTERNET`, `ACCESS_WIFI_STATE`, `BLUETOOTH`

**Dangerous permissions** — require runtime user approval:
- `READ_CONTACTS`, `CAMERA`, `LOCATION`, `READ_SMS`, `RECORD_AUDIO`

**Signature permissions** — only granted to apps signed with same certificate:
- System-level permissions

---

## 5. Android Security Model

### 5.1 Core Security Mechanisms

| Mechanism | How it works |
|-----------|-------------|
| **App Sandbox** | Each app has unique UID, runs isolated |
| **Permission system** | Explicit permission declarations + user approval |
| **App signing** | APKs must be signed; updates need same certificate |
| **SELinux (SE Android)** | Mandatory Access Control policies |
| **Verified Boot** | Ensures OS hasn't been tampered with |
| **Google Play Protect** | Scans apps for malware |
| **Encryption** | FBE (File-Based Encryption) by default since Android 7 |
| **Secure Enclave** | Hardware-protected key storage (StrongBox) |

### 5.2 Android Sandbox
```
App A (UID 10001) | App B (UID 10002) | App C (UID 10003)
     [sandbox]    |     [sandbox]     |     [sandbox]
         ↓               ↓                   ↓
    Linux Kernel (enforces UID-based isolation)
    
Apps cannot read each other's private data
Communication via IPC (Intents, Content Providers, AIDL)
```

---

## 6. Device Rooting

### 6.1 What is Rooting?
Gaining **root (superuser) access** to Android, bypassing security restrictions.

**Equivalent to:** Jailbreaking on iOS

### 6.2 Why Users Root

- Install custom ROMs
- Remove bloatware
- Access root-only apps
- Full backup access
- Overclock CPU

### 6.3 How Rooting Works

1. **Bootloader unlock** — OEM unlock in developer options (if allowed)
2. **Flash custom recovery** (TWRP — TeamWin Recovery Project)
3. **Install Magisk** (rootless root framework) or older SuperSU

### 6.4 Security Risks of Rooting

| Risk | Description |
|------|-------------|
| **Sandbox bypass** | Root apps can access all other apps' data |
| **No verified boot** | Malware can persist in boot partition |
| **Malicious root apps** | Apps with root can do anything |
| **Disabled SafetyNet/Play Integrity** | Can't use banking apps |
| **Custom ROM risks** | Unverified OS code |
| **No OEM updates** | Security patches stop |

> **Tricky Q:** How does malware exploit rooted devices?
> **A:** On rooted devices, malware can request root permissions, access other apps' data, modify system files, install persistent backdoors, intercept all network traffic, access SMS/call logs, and disable security software.

### 6.5 Root Detection Methods (in Apps)
```java
// Checks for root indicators:
- Presence of su binary (/system/xbin/su)
- Superuser.apk or Magisk Manager installed
- Build tags (test-keys instead of release-keys)
- RW system partition
- Custom ROM signatures

Tools to bypass root detection:
- Magisk Hide
- RootCloak
- Frida hooks
```

---

# SESSION 22: Android Debug Bridge (ADB)

## 1. What is ADB?

**Android Debug Bridge (ADB)** is a command-line tool for communicating with Android devices for development and debugging.

```
Computer → USB/WiFi → ADB Daemon (adbd on device) → Device
```

**Components:**
- **ADB client** — runs on your PC
- **ADB daemon (adbd)** — runs on Android device
- **ADB server** — manages communication on PC (port 5037)

## 2. Key ADB Commands

```bash
# Device management
adb devices               # List connected devices
adb -s <serial> shell     # Connect to specific device

# Shell access
adb shell                 # Open shell on device
adb shell su              # Root shell (if rooted)

# File transfer
adb push local_file /sdcard/  # Upload file to device
adb pull /sdcard/file .        # Download file from device

# App management
adb install app.apk       # Install APK
adb uninstall com.package.name  # Uninstall app
adb shell pm list packages  # List all installed packages
adb shell monkey -p com.package 1000  # Stress test app

# Data extraction
adb backup -all -f backup.ab   # Full device backup
adb shell content query --uri content://contacts/phones  # Query contacts

# Log capture
adb logcat                # Stream device logs
adb logcat -d > log.txt   # Dump logs to file

# Port forwarding
adb forward tcp:8080 tcp:8080  # Forward phone port to PC

# Network debugging
adb tcpip 5555            # Enable WiFi ADB on port 5555
adb connect 192.168.1.x:5555  # Connect over WiFi
```

## 3. ADB Security Risks

| Risk | Description |
|------|-------------|
| **USB debugging on** | Allows full ADB access to unlocked device |
| **ADB backup** | Backup bypasses app's allowBackup=false check (partially) |
| **Logcat leakage** | Apps logging sensitive data (passwords, tokens) |
| **TCP ADB** | Port 5555 open on network → any device can connect |
| **APK extraction** | `adb shell pm path <package>` + `adb pull` |

> **Tricky Q:** What port does ADB use for WiFi debugging?
> **A:** Port 5555 TCP (device side). ADB server on PC uses port 5037.

---

# SESSION 23: OWASP Mobile Top 10 & Android Attacks

## 1. OWASP Mobile Top 10 (2024)

| Rank | Vulnerability | Description |
|------|--------------|-------------|
| M1 | **Improper Credential Usage** | Hardcoded credentials, insecure storage |
| M2 | **Inadequate Supply Chain Security** | Third-party SDK vulnerabilities |
| M3 | **Insecure Authentication/Authorization** | Missing auth, improper session |
| M4 | **Insufficient I/O Validation** | SQL injection, XSS in WebViews |
| M5 | **Insecure Communication** | Cleartext traffic, improper TLS |
| M6 | **Inadequate Privacy Controls** | Excessive data collection |
| M7 | **Insufficient Binary Protections** | No obfuscation, debugging enabled |
| M8 | **Security Misconfiguration** | Default config, open backup |
| M9 | **Insecure Data Storage** | Sensitive data in logs, SharedPrefs |
| M10 | **Insufficient Cryptography** | Hardcoded keys, weak algorithms |

> **Tricky Q:** What is the most common mobile security vulnerability?
> **A:** M9 — Insecure Data Storage. Developers frequently store sensitive data (API keys, tokens, PII) in insecure locations: SharedPreferences, SQLite without encryption, external storage, log files.

### 1.1 Insecure Data Storage — Where Devs Store Badly
```
SQLite databases (unencrypted)
SharedPreferences XML
Log files (adb logcat)
External storage (/sdcard/)
Clipboard (copy/paste buffer)
Temp files in /cache/
Hardcoded in source code / strings.xml
```

### 1.2 Insecure Communication
```
Risks:
- HTTP instead of HTTPS
- Self-signed certificates accepted
- SSL/TLS pinning not implemented
- Hostname verification disabled

Vulnerable code:
TrustManager that accepts ALL certificates (common dev shortcut!)
HostnameVerifier that returns true for all hosts

Attack: MITM with Burp Suite proxy
Defense: 
- Use HTTPS only
- Implement SSL/TLS pinning
- Network Security Config (Android 7+)
```

### 1.3 WebView Vulnerabilities
```java
// Dangerous: enables JavaScript + file access
webView.getSettings().setJavaScriptEnabled(true);
webView.getSettings().setAllowFileAccessFromFileURLs(true);
webView.getSettings().setAllowUniversalAccessFromFileURLs(true);

// If the WebView loads attacker-controlled URL:
// → XSS in WebView
// → Can access local files (file:// URI scheme)
// → JavaScript can call Java methods via addJavascriptInterface

// Dangerous JavaScript bridge:
webView.addJavascriptInterface(new MyClass(), "Android");
// JS can call Android.sensitiveMethod() → Remote Code Execution!
```

## 2. Reverse Engineering Android Apps

### 2.1 APK Structure Reminder
APK = ZIP file containing:
- `classes.dex` — compiled Java/Kotlin code
- `AndroidManifest.xml` — binary XML
- `resources.arsc` — compiled resources

### 2.2 Reverse Engineering Tools

| Tool | Purpose | Command |
|------|---------|---------|
| **APKTool** | Decode APK → smali code, decode manifest | `apktool d app.apk` |
| **JADX** | Decompile .dex to Java source | `jadx-gui app.apk` |
| **dex2jar** | Convert .dex to .jar | `d2j-dex2jar.sh app.apk` |
| **JD-GUI** | View .jar/class files | Open .jar in JD-GUI |
| **Frida** | Dynamic instrumentation (hook functions) | `frida -U -f com.app` |
| **objection** | Runtime mobile exploration (built on Frida) | `objection -g com.app explore` |

### 2.3 What to Look for in Reversed Code

```java
// Hardcoded credentials
String password = "hardcoded_secret_123";
String apiKey = "sk-abc123def456";

// Insecure endpoints
String baseUrl = "http://api.example.com";  // HTTP not HTTPS

// Weak cryptography
MessageDigest md = MessageDigest.getInstance("MD5");  // MD5 broken!

// Debug functionality left in production
if (BuildConfig.DEBUG) { ... }  // If minification off, DEBUG=true possible

// SQL injection vulnerable code
db.rawQuery("SELECT * FROM users WHERE id=" + userId, null);
```

## 3. Smishing Attack

**SMS Phishing** — fraudulent SMS messages:
```
"ALERT: Your bank account has been suspended. Verify now: http://fake-bank.com"
"You've won ₹1,00,000! Click: bit.ly/claim-now"
"Your package delivery failed. Update address: http://fedex-fake.com"
```

**Attack chain:**
1. Attacker sends smishing SMS (spoofed number possible)
2. Victim clicks malicious link
3. Downloads malicious APK or enters credentials on phishing site
4. Device compromised or credentials stolen

---

# SESSION 24: Web, Network & Social Engineering Attacks on Android

## 1. Web-Based Attacks on Android

### 1.1 Drive-By Download
- Visiting malicious/compromised website
- Browser or plugin vulnerability exploited
- Malware auto-downloads without user interaction
- Often via malicious ads (malvertising)

### 1.2 Man-in-the-Browser (MitB)
- Malware sits between browser and user
- Intercepts form submissions BEFORE encryption
- Modifies displayed content (fake balance)
- Injects malicious transactions

## 2. Network-Based Attacks on Android

### 2.1 MITM (Man-in-the-Middle) Attack

```
Normal:   Android Device ←→ Access Point ←→ Internet
MITM:     Android Device ←→ [Attacker] ←→ Access Point ←→ Internet

Attacker intercepts all traffic
If unencrypted HTTP → reads everything
If HTTPS without pinning → can use Burp cert to decrypt
```

**MITM Setup with Burp Suite (Mobile):**
```
1. Android: Set proxy to Burp IP:8080
2. PC: Burp Suite running, intercept on
3. Android: Download Burp CA cert from http://burpsuite/
4. Android: Install cert → Settings → Security → Install certificate
5. Now Burp intercepts all HTTPS traffic!
```

### 2.2 SSL Pinning

```
Without pinning: App accepts ANY valid SSL certificate
→ Burp can present its own cert → decrypt traffic

With SSL pinning: App only trusts SPECIFIC certificate/public key
→ If different cert presented → connection rejected
→ Attacker can't intercept

Types:
Certificate pinning → Pin exact certificate
Public key pinning  → Pin the public key (more flexible, survives cert renewal)

Bypass techniques:
- Frida hook to disable pinning check
- Objection: android sslpinning disable
- Patch APK to remove pinning code (apktool → edit → recompile)
```

### 2.3 Packet Sniffing (Mobile)
```bash
# Using Wireshark on same network (MITM needed for HTTPS)
# tcpdump on rooted device:
adb shell
tcpdump -i any -w /sdcard/capture.pcap
adb pull /sdcard/capture.pcap
# Open in Wireshark on PC
```

## 3. Social Engineering on Android

| Attack | Method |
|--------|--------|
| **Phishing** | Fake Google login, bank website |
| **Smishing** | SMS with malicious link |
| **Vishing** | Voice call impersonating bank/tech support |
| **Fake apps** | Malicious apps on unofficial stores |
| **QR code attacks** | Malicious QR codes → malicious URLs |
| **Overlay attacks** | Transparent overlay on legitimate app → credential theft |

---

# SESSION 25: Mobile Malware & MobSF Analysis

## 1. Overview of Mobile Malware

### 1.1 Android Malware Types

| Malware | Description | Example |
|---------|-------------|---------|
| **SMS Trojans** | Send premium SMS, spy on messages | Android.FakePlayer |
| **Banking Trojans** | Overlay attacks on banking apps | Anubis, Cerberus |
| **Spyware** | Collect location, contacts, messages | Pegasus |
| **Adware** | Display aggressive ads | HummingBad |
| **RAT** | Remote control of device | AhMyth |
| **Ransomware** | Lock screen or encrypt files | Android.Locker |
| **Cryptominer** | Mine cryptocurrency | Loapi |
| **Clipper** | Replace clipboard contents (crypto addresses) | — |

### 1.2 Real-World Android Malware Case Studies

#### Joker Malware (Bread)
- Hid in seemingly legitimate apps on Play Store
- **Functionality:** SMS fraud (signed up victims for premium subscriptions)
- **Evasion:** Code obfuscation, delayed payload loading, used legitimate C2 infrastructure
- **Impact:** Millions of devices affected across multiple waves

#### EventBot
- Android banking Trojan (2020)
- Targeted 200+ financial apps (PayPal, HSBC, CapitalOne)
- Abused Android Accessibility Service for keylogging
- Intercepted 2FA SMS codes

> **Tricky Q:** How do banking Trojans intercept 2FA SMS codes?
> **A:** They register as SMS receivers and request READ_SMS permission. They can also abuse the Accessibility Service to read SMS content from the notification. The captured OTP is sent to attacker's C2 server.

---

## 2. MobSF (Mobile Security Framework)

### 2.1 What is MobSF?
An **automated mobile app security testing framework** for:
- Android APKs
- iOS IPAs
- Windows Apps

Supports both **static and dynamic analysis**.

### 2.2 MobSF Static Analysis Output

```
For an Android APK, MobSF provides:

Security Score (0-100)
├── Manifest Analysis
│   ├── Permissions (dangerous ones highlighted)
│   ├── Exported components (activities, services, receivers)
│   ├── Backup settings
│   └── Debuggable flag check
│
├── Code Analysis
│   ├── Hardcoded secrets (API keys, credentials)
│   ├── Insecure random number generation
│   ├── SQL injection patterns
│   ├── Cleartext communication
│   ├── Insecure cryptography
│   └── Dynamic code loading
│
├── Binary Analysis
│   ├── Anti-debugger checks
│   ├── Root detection
│   ├── SSL pinning
│   └── Obfuscation
│
└── File Analysis
    ├── Embedded files
    ├── Certificates
    └── Strings of interest
```

### 2.3 MobSF Dynamic Analysis Output
- Network traffic (HTTP/HTTPS with proxy)
- File system changes
- Shared preferences created
- SQLite database queries
- Screenshot of running app
- API calls made

---

## 3. Exam Questions Bank (Mobile Sessions)

**Q1:** What is the Android sandbox and how does it work?
**A:** Each Android app runs with a unique Linux UID in an isolated process. Apps cannot access each other's private data. Communication must go through Android's IPC mechanisms (Intents, ContentProviders) with explicit permission.

**Q2:** What does APKTool do?
**A:** Decodes (decompiles) APK to smali code and decoded resources/manifest. Also used to repackage/rebuild modified APKs.

**Q3:** What is SSL pinning and how is it bypassed?
**A:** SSL pinning makes the app trust only a specific certificate/key, preventing MITM. Bypassed using Frida/Objection to hook the pinning check, or patching the APK to remove pinning code.

**Q4:** Where is sensitive app data most commonly found insecurely?
**A:** SharedPreferences (XML), SQLite databases (unencrypted), external storage (/sdcard/), log files, hardcoded in source code

**Q5:** What Android Manifest attribute allows full ADB backup of app data?
**A:** `android:allowBackup="true"`

**Q6:** What is the Accessibility Service and how is it abused by malware?
**A:** Android's Accessibility Service provides hooks into UI for accessibility tools. Malware abuses it to read screen content, intercept input, and perform actions on behalf of user — essentially a keylogger.

**Q7:** How does Smishing differ from Phishing?
**A:** Smishing uses SMS as the vector; phishing uses email. Both aim to steal credentials or deliver malware.

**Q8:** What does MobSF stand for and what does it analyze?
**A:** Mobile Security Framework — analyzes Android APKs, iOS IPAs for security vulnerabilities using static and dynamic analysis.

**Q9:** What port does ADB use on the device side for WiFi debugging?
**A:** Port 5555

**Q10:** What is the OWASP Mobile category for cleartext HTTP traffic?
**A:** M5 — Insecure Communication

---

# MASTER QUICK REFERENCE: KEY PORTS

| Port | Protocol/Service | Notes |
|------|-----------------|-------|
| 21 | FTP | Plaintext credentials |
| 22 | SSH | Secure shell |
| 23 | Telnet | Plaintext — very insecure |
| 25 | SMTP | Email sending |
| 53 | DNS | TCP/UDP |
| 80 | HTTP | Plaintext web |
| 110 | POP3 | Email |
| 137-139 | NetBIOS | Windows naming |
| 143 | IMAP | Email |
| 161/162 | SNMP | UDP — network management |
| 389 | LDAP | Directory services |
| 443 | HTTPS | Encrypted web |
| 445 | SMB | Windows file sharing (direct) |
| 3306 | MySQL | Database |
| 3389 | RDP | Remote Desktop Protocol |
| 5555 | ADB | Android Debug Bridge WiFi |
| 8080 | HTTP Alt / Burp Proxy | Dev/proxy port |

---

*End of Complete PGCP-ITISS Security Concepts Notes*
