# 📌 Web Security Essentials

## Web Infrastructure

Components of a Web Service

- Application: The code, images, styles, and icons that dictate how the website looks and functions.
- Web Server: This component hosts the application. It listens for requests and returns a response to the user.
- Host Machine: The underlying operating system, Linux or Windows, that runs the web server and the application.

## Protecting the Web

Protecting the Application

- Secure Coding: Avoid insecure functions, ensure proper handling of errors, and remove sensitive information.
- Input Validation & Sanitization: Validate and sanitize user input to prevent injection attacks.
- Access Control: Restrict access based on user roles.

Protecting the Web Server

- Logging: Keep a detailed record of all web requests with access logs.
- Web Application Firewall (WAF): Filter and block harmful traffic based on defined rules.
- Content Delivery Network (CDN): Reduce direct exposure to your server and use integrated WAFs.

Protecting the Host Machine

- Least Privilege: Use low-privilege users for services.
- System Hardening: Disable unnecessary services and close unused ports.
- Antivirus: Add endpoint-level protection that blocks known malware.

Security Tips for All Three Components

- Strong Authentication: Don't just let anyone access your code, admin panels, or host machine.
- Patch Management: Ensure your app dependencies, web server, and host machine are up to date.


## Defense Systems

Functionality

As stated above, WAFs inspect HTTP requests to detect anomalies, attacks, or known suspicious patterns. Below are some of the methods used, along with examples of requests that may be blocked.

| WAF Feature |	Detection Method |	Example |
|--|--|--|
| Signature-Based Detection |	Matches known attack patterns or payloads | A request with a User-Agent that matches a known tool, sqlmap/1.8.1 |
| Heuristic-Based Detection |	Analyzes the context and behavior of requests |	A long query string with special characters search?q=%3Cscript%20(1) |
| Anomaly & Behavioral Analysis |	Flags deviations from normal traffic behavior |	A single IP address makes repeated login attempts in a short period of time |
| Location & IP Reputation Filtering |	Uses location and threat intel to block IPs |	A request from an IP address that is outside of your normal business area |


