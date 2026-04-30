# 📌 Web Application Basics

## Security Headers
**Security Headers**
HTTP Security Headers help improve the overall security of the web application by providing mitigations against attacks like Cross-Site Scripting (XSS), clickjacking, and others. We will now dig deeper into the following security headers:

Content-Security-Policy (CSP)
Strict-Transport-Security (HSTS)
X-Content-Type-Options
Referrer-Policy
You can use a site like **https://securityheaders.io/(opens in new tab)** to analyse the security headers of any website. After the discussion in this task, you will hopefully have a better understanding of what it is reporting on.


#### **In simple terms:**
**CSP (Content Security Policy)**
Controls where the browser is allowed to load scripts and other resources from.
➡ Protects against XSS attacks

**HSTS (Strict Transport Security)**
Forces the browser to always use HTTPS.
➡ Protects against MITM attacks and sniffing

**X-Content-Type-Options**
Prevents the browser from guessing a file’s MIME type.
➡ Protects against malicious file execution

**Referrer-Policy**
Controls how much referrer information is shared with other websites.
➡ Protects against information leakage

**Simple real-world analogy:**
Think of the browser as an employee, and the security headers as instructions from the manager:

CSP:
“Only get files from trusted suppliers.”

HSTS:
“Only use secure communication lines.”

X-Content-Type-Options:
“Don’t guess what’s inside the package.”

Referrer-Policy:
“Don’t reveal where we came from.”
