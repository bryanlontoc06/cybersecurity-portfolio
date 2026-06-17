# 📌 Web Server Attacks - I

## Identifying Web Servers

The Server Response Header

Run curl with the -I flag to request only the headers, not the response body:

```bash
# -s suppresses the progress bar
# -I sends a HEAD request, returning only response headers
curl -sI http://MACHINE_IP:80
```

Here is what each server in this lab returns by default:

| Port |	Server |	Default Server Header |
| 80 |	Apache2 |	Apache/2.4.x (Ubuntu) |
| 8000 |	Python HTTP Server |	SimpleHTTP/0.6 Python/3.xx.x |
| 3000 |	Node.js Express |	None (set by application) |
| 8080 |	Nginx	| nginx/1.xx.x |


### The X-Powered-By Header

Some frameworks add an X-Powered-By header that reveals the application layer behind the server. Express sets this by default:

```bash
X-Powered-By: Express
```

### Default Error Pages

The -sI flag sends a HEAD request, which returns headers only and no body. To see default error pages, you need a GET request switch to -s without the -I:

```bash
# HEAD request: headers only, no body
curl -sI http://10.49.170.164:PORT/

# GET request: full response including body
curl -s http://10.49.170.164:PORT/nonexistent-page-xyz
```

## Python HTTP Server

Python ships with a built-in HTTP server that any developer can start with a single command. That convenience is exactly why it shows up on pentests.

```bash
# This command serves the current working directory over HTTP on port 8000
python3 -m http.server 8000
```

### Accessing Dotfiles

```bash
root@attackbox:~# curl -s http://10.49.170.164:8000/.env
SECRET_KEY=dev-secret-key-do-not-use
DATABASE_URL=postgresql://webapp:S3cur3DBPass!@localhost/production
DEBUG=True
```

### Downloading and Inspecting Archives

```bash
root@attackbox:~# curl -s http://10.49.170.164:8000/backup.zip -o backup.zip
root@attackbox:~# unzip backup.zip -d backup-contents/
root@attackbox:~# cat backup-contents/db_dump.sql
Archive:  backup.zip
  inflating: backup-contents/db_dump.sql
-- Database dump for staging environment

CREATE TABLE users (id INTEGER PRIMARY KEY, username VARCHAR(50));
INSERT INTO users VALUES (1, 'admin', 'admin@company.com');
INSERT INTO users VALUES (2, 'jsmith', 'jsmith@company.com');

-- End of dump
```

## Apache2

### Version Disclosure

Start with the basics: check the Server header.

```bash
root@ip-10-81-64-63:~# curl -SI http://MACHINE_IP:80 | grep -i server
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0 10671    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
Server: Apache/2.4.58 (Ubuntu)
```

## Directory Listing

```http://10.48.186.114/files/```

## The mod_status Page

Apache includes a built-in status page powered by the mod_status module. When correctly configured, it is accessible only from localhost. *When misconfigured with Require all granted, it is accessible from any IP. You can access it using **http://10.48.186.114:80/server-status***


### Finding Unlinked Files with Gobuster

```bash
# -u is the target URL
# -w is the wordlist path
# -x tells gobuster to also append these extensions to each word
gobuster dir -u http://TARGET_IP:80 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -x bak,txt,html -t 20
```

## Node.js (Express)

### Framework Fingerprinting

The headers on port 3000 confirm what you are dealing with:

```bash
root@ip-10-81-64-63:~# curl -sI http://10.48.186.114:3000
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 56
ETag: W/"38-K8iCfm09rMr0MV0NsgqdAb94DAk"
Date: Sat, 11 Apr 2026 07:27:28 GMT
Connection: keep-alive
Keep-Alive: timeout=5
```

You will see X-Powered-By: Express in the response. Express sets this header automatically unless the developer explicitly disables it. This confirms you are dealing with an Express application, which tells you what to look for next.

### Reading the Application Version

```bash
root@ip-10-81-64-63:~# curl -s http://10.48.186.114:3000
{"status":"ok","app":"company-portal","version":"1.2.0"}
```

### Triggering Verbose Errors

```bash
root@ip-10-81-64-63:~# curl -s http://10.48.186.114:3000/api/users | python3 -m json.tool
{
    "error": "connect ECONNREFUSED 127.0.0.1:5432",
    "stack": "Error: connect ECONNREFUSED 127.0.0.1:5432\n    at /opt/nodeapp/app.js:16:15\n    at Layer.handle [as handle_request] (/opt/nodeapp/node_modules/express/lib/router/layer.js:95:5)\n    at next (/opt/nodeapp/node_modules/express/lib/router/route.js:149:13)\n    at Route.dispatch (/opt/nodeapp/node_modules/express/lib/router/route.js:119:3)\n    at Layer.handle [as handle_request] (/opt/nodeapp/node_modules/express/lib/router/layer.js:95:5)\n    at /opt/nodeapp/node_modules/express/lib/router/index.js:284:15\n    at Function.process_params (/opt/nodeapp/node_modules/express/lib/router/index.js:346:12)\n    at next (/opt/nodeapp/node_modules/express/lib/router/index.js:280:10)\n    at expressInit (/opt/nodeapp/node_modules/express/lib/middleware/init.js:40:5)\n    at Layer.handle [as handle_request] (/opt/nodeapp/node_modules/express/lib/router/layer.js:95:5)",
    "query": "SELECT * FROM users"
}
```

### Enumerating Routes via Debug Endpoints

```bash
root@ip-10-81-64-63:~# curl -s http://10.48.186.114:3000/api/routes
[{"method":"GET","path":"/"},{"method":"GET","path":"/api/users"},{"method":"GET","path":"/api/routes"},{"method":"GET","path":"/api/debug/env"}]
```

### Exposed Environment Variables
Environment variables in Node.js applications often contain database credentials, API keys, and configuration flags. A debug endpoint that returns process.env is a significant finding:
```bash
root@ip-10-81-64-63:~# curl -s http://10.48.186.114:3000/api/debug/env
{"NODE_ENV":"development","DB_PASSWORD":"NodeDBPass2024!","PORT":"3000","DB_HOST":"localhost:5432","APP_NAME":"company-portal"}r
```

### Static File Serving

Express applications commonly use the express.static() middleware to serve front-end assets like JavaScript files, stylesheets, and configuration. The static files route, if it exists, serves everything in a directory. Client-side JavaScript files sometimes contain API endpoint URLs, internal hostnames, or debug flags embedded as constants.

Once you know the routes from the /api/routes endpoint, check what is being served statically:

```bash
root@ip-10-81-64-63:~# curl -s http://10.48.186.114:3000/static/config.js
// Client-side configuration
const API_BASE = 'http://internal-api.company.local:8080';
const DEBUG = true;
const VERSION = '1.2.0';
```


## NGINX

### Version Disclosure

The pattern is the same as before: start with the Server header.

```bash
root@ip-10-81-64-63:~# curl -sI http://MACHINE_IP:8080 | grep -i server
Server: nginx/1.24.0 (Ubuntu)
```

The server_tokens directive controls both the Server header and the version string in default error pages simultaneously; setting server_tokens off suppresses the version from both places at once. If the Server header is suppressed, requesting a non-existent path will confirm whether server_tokens is truly off or just partially configured?

```bash
root@ip-10-81-64-63:~# curl -s http://10.49.130.99:8080/nonexistent-path
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.24.0 (Ubuntu)</center>
</body>
</html>
```

### Directory Listing with Autoindex

Nginx does not enable directory listing by default. When a developer wants to expose a directory listing, they add autoindex to a location block in the configuration:

```bash
location /files/ {
    autoindex on;
    root /var/www/nginx/;
}
```

```bash
http://10.49.130.99/files/
```

### The nginx_status Endpoint

Nginx's stub_status module exposes real-time connection metrics at a configurable URL. The secure configuration restricts access to localhost only. The misconfigured version allows access from any IP:

```bash
location /nginx_status {
    stub_status;
    allow all;  # Should be: allow 127.0.0.1; deny all;
}
```

```bash
root@ip-10-81-64-63:~# curl -s http://10.49.130.99:8080/nginx_status
Active connections: 1
server accepts handled requests
 15 15 15
Reading: 0 Writing: 1 Waiting: 0
```

The three numbers on the second line are in order: total accepted connections, total handled connections, and total requests since the server started. The third line breaks down currently active connections by state. While this data is not directly exploitable, it leaks operational information about server load and usage patterns. On a real engagement, an exposed /nginx_status endpoint is a finding because it confirms the internal monitoring setup and may indicate other monitoring endpoints are similarly exposed.


## Common Misconfigurations Across Servers

### Security Headers

Security headers are HTTP response headers that instruct the browser on how to handle the page content. They protect against a range of client-side attacks, including clickjacking, MIME sniffing, and cross-site scripting. None of the servers in this lab has been configured to send these headers, which is the default state for all four server types.

Here are the most common ones and what each one does:

| Header |	What It Protects Against |	Example Value |
|--|--|--|
| X-Frame-Options |	Clickjacking (prevents the page from being embedded in an iframe on another domain) |	DENY or SAMEORIGIN |
| X-Content-Type-Options |	MIME sniffing (prevents the browser from guessing content types) |	nosniff |
| Content-Security-Policy |	Restricts where scripts, stylesheets, and other resources can load from |	default-src 'self' |
| Referrer-Policy |	Controls what is sent in the Referer header when navigating to another page |	no-referrer or strict-origin |
| Strict-Transport-Security |	Forces HTTPS for subsequent requests (only meaningful on HTTPS servers) |	max-age=31536000 |



Audit each server with curl:

```bash
root@ip-10-81-64-63:~# for port in 80 8000 3000 8080; do echo "=== Port $port ==="; curl -sI http://MACHINE_IP:$port/ | grep -iE "x-frame-options|x-content-type|content-security-policy|strict-transport|referrer-policy" || echo "(no security headers found)"; done
=== Port 80 ===
(no security headers found)
=== Port 8000 ===
(no security headers found)
=== Port 3000 ===
(no security headers found)
=== Port 8080 ===
(no security headers found)
```

### Automated Scanning with Nikto

Nikto is a web server scanner that checks for known misconfigurations, outdated software, exposed admin interfaces, and missing security headers. It is not subtle; it generates a lot of traffic and is easy to detect. That makes it appropriate for authorised testing, not stealth. Run it against the Apache server:

```bash
root@ip-10-81-64-63:~# nikto -h http://10.49.145.97:80 -nointeractive
- Nikto v2.1.5
---------------------------------------------------------------------------
+ Target IP:          10.49.145.97
+ Target Hostname:    10.49.145.97
+ Target Port:        80
+ Start Time:         2026-04-11 09:16:09 (GMT1)
---------------------------------------------------------------------------
+ Server: Apache/2.4.58 (Ubuntu)
+ Server leaks inodes via ETags, header found with file /, fields: 0x29af 0x64e9243796aa2
+ The anti-clickjacking X-Frame-Options header is not present.
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Allowed HTTP Methods: HEAD, GET, POST, OPTIONS
+ OSVDB-561: /server-status: This reveals Apache information. Comment out appropriate line in httpd.conf or restrict access to allowed hosts.
+ OSVDB-3268: /files/: Directory indexing found.
+ OSVDB-3092: /files/: This might be interesting...
+ 6544 items checked: 0 error(s) and 6 item(s) reported on remote host
+ End Time:           2026-04-11 09:16:18 (GMT1) (9 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```


