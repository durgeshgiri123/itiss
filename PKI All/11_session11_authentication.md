# Session 11: Strong Authentication, Single/Multi-Factor Authentication, SSO, OpenID & OAuth, Graphical Passwords

## 1. Strong Authentication — The Three (or More) Factors

Authentication factors are categorized by **what kind of evidence** they rely on:

| Factor Type | Description | Examples |
|---|---|---|
| **Something you know** | Knowledge-based | Password, PIN, security question |
| **Something you have** | Possession-based | OTP token, smart card, USB security key, mobile phone (for SMS/app OTP) |
| **Something you are** | Inherence/biometric-based | Fingerprint, iris scan, face recognition, voice |
| **Somewhere you are** *(less common, sometimes added as a 4th)* | Location-based | GPS location, IP geolocation |
| **Something you do** *(behavioral, sometimes added as a 5th)* | Behavior-based | Typing rhythm, gait analysis, signature dynamics |

> **"Strong Authentication"** generally refers to authentication that combines **multiple independent factors**, making it significantly harder for an attacker to compromise all factors simultaneously, compared to relying on a single weak factor (like a password alone).

## 2. Single-Factor vs Multi-Factor Authentication

### Single-Factor Authentication (SFA)
Relies on **only one** category of evidence — most commonly just a password. Vulnerable to a wide range of attacks: phishing, keylogging, credential stuffing (reused passwords from other breaches), brute force, shoulder surfing.

### Multi-Factor Authentication (MFA)
Requires **two or more independent factors from different categories**. 

> **Critical exam trap**: Using a password + a security question is **NOT** true MFA — both are "something you know," so they're still effectively single-factor in terms of category diversity (an attacker who can guess/phish one can often guess/phish the other). True MFA requires factors from **different categories** (e.g., password + fingerprint, or password + OTP from a phone).

### Two-Factor Authentication (2FA) — a specific case of MFA
Exactly two factors from different categories, e.g., password (know) + OTP from an authenticator app (have).

### Common MFA Implementation Methods
- **TOTP (Time-based One-Time Password)** — algorithmically generates a new code every 30 seconds, based on a shared secret + current time (used by Google Authenticator, Microsoft Authenticator).
- **HOTP (HMAC-based One-Time Password)** — generates a code based on a shared secret + an incrementing counter (rather than time) — **note the direct callback to HMAC from Session 6!**
- **Push notifications** — app-based approval prompts.
- **Hardware security keys** — e.g., YubiKey, using FIDO/U2F standards (detailed in Session 12).
- **SMS-based OTP** — convenient but considered the **weakest** MFA method, vulnerable to **SIM-swapping attacks** and SS7 network interception.

> **Frequently asked**: "Why is SMS-OTP considered weak MFA?" — Because the security of "something you have" (the phone number) can be subverted through **SIM swapping** (social-engineering the telecom carrier into transferring a victim's number to an attacker's SIM) or interception via SS7 protocol vulnerabilities — meaning the factor isn't as exclusively tied to physical possession as it appears.

## 3. Single Sign-On (SSO)

**SSO** allows a user to authenticate **once** with a central **Identity Provider (IdP)**, and then access **multiple independent applications/services** without needing to log in separately to each one.

### How SSO Works (Conceptual Flow)
1. User attempts to access Service A (a **Service Provider/SP**).
2. Service A redirects the user to the trusted **Identity Provider** for authentication.
3. User authenticates with the IdP (once).
4. The IdP issues a **signed assertion/token** (proving successful authentication) back to Service A.
5. Service A trusts this token (because it trusts the IdP's signature) and grants access — no separate password needed for Service A.
6. If the user then visits Service B (which also trusts the same IdP), the existing session with the IdP can be reused — no re-login needed.

### Common SSO Protocols/Standards
- **SAML (Security Assertion Markup Language)** — XML-based, common in enterprise environments.
- **OAuth 2.0** and **OpenID Connect** — covered in detail below; common in consumer/web app contexts ("Login with Google").
- **Kerberos** — common in Windows Active Directory environments (ties into Session 14's LDAP/AD topic).

### SSO Benefits and Risks (commonly asked together)

| Benefit | Risk |
|---|---|
| Improved user experience (fewer passwords to remember) | **Single point of failure** — if the IdP account is compromised, ALL connected services become accessible to the attacker |
| Centralized access control (easy to revoke access everywhere at once) | If the IdP itself is compromised or goes down, it can lock users out of everything simultaneously |
| Reduced password fatigue → fewer weak/reused passwords | Requires strong protection (often MFA) specifically on the IdP account, since it's now the master key |

## 4. OpenID and OAuth — The Most Confused Pair in This Entire Course

This distinction is asked **constantly** in interviews, so internalize it carefully:

| | **OAuth 2.0** | **OpenID Connect (OIDC)** |
|---|---|---|
| **Purpose** | **Authorization** — granting limited access to a resource on your behalf | **Authentication** — proving who you are |
| **Core question answered** | "Can this app access my Google Drive files?" | "Who is this user, really?" |
| **What it returns** | An **access token** (used to call APIs on the user's behalf) | An **ID token** (a signed JWT containing identity claims — who the user is) |
| **Built on top of** | Standalone protocol | **Built ON TOP of OAuth 2.0** (adds an identity layer) |
| **Analogy** | A valet key that lets the valet drive your car but not open your trunk (limited delegated access) | A passport that proves who you are |

> **The single most important sentence to memorize**: **OAuth is about authorization (access), OpenID Connect is about authentication (identity), and OpenID Connect is built as an identity layer on top of OAuth 2.0.** Many real "Login with Google/Facebook" buttons actually use OpenID Connect (not raw OAuth) precisely because they need to know *who* you are, not just get permission to access some API.

### Classic OAuth 2.0 Flow (Authorization Code Grant — most common/secure flow)
1. User clicks "Login with Google" on a third-party app.
2. App redirects user to Google's authorization server.
3. User logs into Google (if not already) and **consents** to the requested permissions ("This app wants access to your email and profile").
4. Google redirects back to the app with an **authorization code**.
5. The app's backend exchanges this code (plus its own client secret) for an **access token** (and, if using OIDC, an **ID token** too).
6. The app uses the access token to call Google APIs on the user's behalf, within the granted scope.

> **Why the authorization code flow has an extra "code exchange" step instead of returning the token directly**: The authorization code is exchanged for the actual token **server-to-server**, using the app's confidential client secret — this prevents the token from ever being exposed in the browser's URL/history (where it could be intercepted), and ensures only the legitimate app (which knows the secret) can complete the exchange.

## 5. Graphical Passwords

An alternative authentication scheme using **images instead of (or alongside) text** for the "something you know" factor, designed to be more memorable and resistant to certain attacks.

### Categories of Graphical Passwords
1. **Recognition-based** — user must identify/select previously chosen images from a set of decoys (e.g., "select the 5 images you registered with from this grid of 25").
2. **Recall-based** — user must reproduce a drawing/pattern from memory (e.g., **Android's pattern unlock**, drawing a specific shape on a grid of dots).
3. **Cued-recall-based** — user clicks specific points/regions on a single provided image, in a specific order (e.g., click 5 specific points on a photo of a map, in the correct sequence).

### Why Graphical Passwords Are Proposed (Psychological Basis)
Based on the **picture superiority effect** in cognitive psychology — humans are generally better at recognizing/recalling images than arbitrary text strings, potentially allowing for stronger, more memorable "passwords" without users resorting to weak/reused text passwords.

### Vulnerabilities of Graphical Passwords (commonly tested)
- **Shoulder surfing** — can be EASIER to observe than typed passwords (watching someone click points on an image can be very revealing, sometimes more so than watching keystrokes).
- **Smudge attacks** — on touchscreens, the oily residue/smudge pattern left by a recall-based pattern (like Android's pattern lock) can sometimes be visually reconstructed, even after the screen is "clean," under the right lighting.
- **Limited password space** in some schemes compared to robust text passwords, depending on grid size/complexity.

---

## Tricky / Conceptual Questions

**Q1. Is "password + security question" multi-factor authentication?**
No — this is a recurring trap. Both belong to the **same category** ("something you know"). True MFA requires factors from **different** categories (e.g., knowledge + possession, or knowledge + inherence).

**Q2. If SSO creates a "single point of failure," why is it still considered a security best practice rather than a security risk?**
Because in practice, SSO **concentrates** security effort onto protecting ONE strong, well-monitored, MFA-protected account, rather than forcing users to manage dozens of separate (often weak, reused) passwords across many services — each of which is a potential weak link. The "single point of failure" risk is real, but it's a much **smaller, more defensible attack surface** than many scattered weak points, *provided* the IdP itself is protected with strong MFA and monitoring.

**Q3. Why is OAuth 2.0 alone (without OpenID Connect) NOT sufficient for "Login with X" buttons, even though many people loosely call this "OAuth login"?**
Because OAuth 2.0's access token only proves "this app has been granted permission to call certain APIs" — it does NOT, by itself, give the relying application a standardized, verifiable assertion of **who the user is**. Some early/poorly-designed systems misused OAuth access tokens for pseudo-authentication (sometimes called the "OAuth as authentication" anti-pattern), which has known security weaknesses. OpenID Connect was specifically created to provide a standardized, secure **ID token** for genuine authentication on top of OAuth's authorization machinery.

**Q4. Are graphical passwords automatically more secure than text passwords?**
No — security depends heavily on the specific scheme's design (password space size, resistance to shoulder-surfing/smudge attacks) rather than the mere fact that it's graphical. Some graphical schemes are actually **more** vulnerable to observation-based attacks (shoulder surfing) than typed passwords, because clicking visible points on a screen can be easier to observe/record than rapid keystrokes.

**Q5. In HOTP vs TOTP, which one is more vulnerable if the counter/clock gets out of sync, and why does this matter for usability?**
HOTP relies on a synchronized **counter** between client and server — if a user generates several codes without using them (e.g., repeatedly pressing a button), the counters can drift out of sync, requiring a resynchronization mechanism (often allowing a "look-ahead window" of several counter values). TOTP relies on **time** synchronization — clock drift on either side can also cause failures, but is generally self-correcting since both sides naturally stay close to real-world time, whereas counter drift in HOTP can accumulate indefinitely without explicit resync logic. This is a key usability/design consideration when choosing between the two for an MFA implementation.

---

## Interview Questions & Model Answers

**Q: A client wants "two-factor authentication" by asking for a password and then their date of birth. Is this acceptable, and what would you recommend instead?**
> This isn't genuine two-factor authentication — both a password and date of birth fall under "something you know," and date of birth is often publicly discoverable or guessable, making it weaker than even a typical security question. I'd recommend a real second factor from a different category, such as a TOTP code from an authenticator app, a push notification to a registered device, or a hardware security key — anything that requires the attacker to also compromise something the user *physically has* or *biologically is*, not just additional knowledge.

**Q: Explain to a product manager why "Login with Google" uses OpenID Connect and not just plain OAuth.**
> OAuth alone is designed to grant an app permission to access specific resources (like reading your calendar), not to prove your identity. If we used raw OAuth tokens to decide "who is logged in," we'd be relying on a mechanism that wasn't designed for that purpose, opening up subtle security gaps. OpenID Connect adds a properly-designed, signed identity token specifically meant to answer "who is this user," giving our app a secure, standardized way to know exactly who just logged in, while OAuth (running underneath) still handles any actual permission/API access needs separately.

**Q: What's your recommendation for an organization currently using SMS-based OTP for MFA, and why?**
> I'd recommend migrating away from SMS-OTP toward app-based TOTP or, ideally, hardware security keys (FIDO2/WebAuthn, covered further in Session 12), because SMS is vulnerable to SIM-swapping and SS7 interception attacks that don't require compromising the user's device at all — just convincing/bribing/social-engineering a telecom employee, or exploiting carrier infrastructure weaknesses. App-based or hardware-based MFA ties the second factor much more tightly and verifiably to something the user actually, physically possesses.

**Q: Why might an enterprise still choose SAML over OpenID Connect for internal SSO, despite OIDC being newer?**
> SAML remains deeply embedded in many enterprise/legacy systems (HR platforms, older enterprise SaaS tools) and has mature, well-understood tooling for complex enterprise scenarios like attribute-based access control passed via rich XML assertions. OIDC is generally lighter-weight, more mobile/API-friendly (JSON/JWT-based), and preferred for newer, consumer-facing, or API-driven applications — so the choice often comes down to existing infrastructure investment and the specific ecosystem being integrated with, rather than one being unconditionally "better."

---

## Quick Revision Table

| Concept | One-line Recall |
|---|---|
| 3 Authentication factors | Know, Have, Are (knowledge/possession/inherence) |
| MFA trap | Password + security question = NOT true MFA (same category) |
| TOTP | Time-based OTP (e.g., Google Authenticator) |
| HOTP | Counter-based OTP — direct use of HMAC |
| SMS-OTP weakness | Vulnerable to SIM swapping, SS7 interception |
| SSO | Authenticate once with IdP, access many services |
| SSO risk | Single point of failure — protect the IdP strongly (MFA!) |
| OAuth 2.0 | Authorization — "can this app access X?" → returns Access Token |
| OpenID Connect | Authentication — "who is this user?" → returns ID Token; built ON TOP of OAuth |
| Authorization Code Flow | Code exchanged server-side for token — avoids exposing tokens in browser |
| Graphical passwords | Recognition-based, Recall-based, Cued-recall-based |
| Graphical password risk | Shoulder surfing (sometimes easier!), smudge attacks |
